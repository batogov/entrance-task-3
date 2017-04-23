# Задание 3

Мобилизация.Гифки – сервис для поиска гифок в перерывах между занятиями.

Сервис написан с использованием [bem-components](https://ru.bem.info/platform/libs/bem-components/5.0.0/).

Работа избранного в оффлайне реализована с помощью технологии [Service Worker](https://developer.mozilla.org/ru/docs/Web/API/Service_Worker_API/Using_Service_Workers).

Для поиска изображений используется [API сервиса Giphy](https://github.com/Giphy/GiphyAPI).

В браузерах, не поддерживающих сервис-воркеры, приложение так же должно корректно работать, 
за исключением возможности работы в оффлайне.

## Структура проекта

  * `gifs.html` – точка входа
  * `assets` – статические файлы проекта
  * `vendor` –  статические файлы внешних библиотек
  * `service-worker.js` – скрипт сервис-воркера

Открывать `gifs.html` нужно с помощью локального веб-сервера – не как файл. 
Это можно сделать с помощью встроенного в WebStorm/Idea веб-сервера, с помощью простого сервера
из состава PHP или Python. Можно воспользоваться и любым другим способом.

## Исправление багов

1. Для корректной работы серсис воркера, файл `service-worker.js` был перенесен в корень проекта (также был изменён путь до этого файла в `blocks.js`). Теперь скоуп сервис воркера охватывает всё приложение.
2. Следующее исправление касается функции `needStoreForOffline` в файле сервис воркера. Для того, чтобы сервис воркер в оффлайн режиме отслеживал файл `gifs.html`, в возвращаемое условие была добавлена ещё одна строчка:

```javascript
function needStoreForOffline(cacheKey) {
    return cacheKey.includes('vendor/') ||
        cacheKey.includes('assets/') ||
        cacheKey.includes('gifs.html') ||
        cacheKey.endsWith('jquery.min.js');
}
```

3. Для того, чтобы приложение сначала пыталось скачивать файлы, а только потом, в случае неудачи, брало их из кеша, было изменено пару строчек в обработчике события fetch:

```javascript
let response;
if (needStoreForOffline(cacheKey)) {
    response = fetchAndPutToCache(cacheKey, event.request);
} else {
    response = fetchWithFallbackToCache(event.request);
}
```

4. Последнее маленькое исправление. Для того, чтобы приложение корректно обновляло статику, была изменена константа `CACHE_VERSION`:

```javascript
const CACHE_VERSION = '1.1.0';
```
