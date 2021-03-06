### <a name="create-a-nodejs-application"></a>Создание приложения Node.js
* Создайте новый файл JavaScript с именем `sender.js`.

### <a name="add-the-relay-npm-package"></a>Добавление пакета ретранслятора NPM
* Запустите `npm install hyco-ws` из командной строки NPM в папке проекта.

### <a name="write-some-code-to-send-messages"></a>Написание кода для отправки сообщений
1. Добавьте `constants` в начало файла `sender.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Добавьте ретранслятор `constants` в файл `sender.js`, чтобы получить сведения о гибридном подключении. Замените заполнители в скобках соответствующими значениями, которые получены при создании гибридного подключения.
   
   1. `const ns` — пространство имен ретранслятора (используйте полное доменное имя, например `{namespace}.servicebus.windows.net`)
   2. `const path` — имя гибридного подключения
   3. `const keyrule` — имя ключа SAS
   4. `const key` — значение ключа SAS
3. Добавьте в файл `sender.js` следующий код:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Вот как будет выглядеть файл listener.js:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

