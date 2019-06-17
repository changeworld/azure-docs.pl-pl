---
title: Konfigurowanie aplikacji Node.js — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania aplikacji Node.js do pracy w usłudze Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 9422d543ad83f29d60fd7e1de51a79c3416e5b14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956162"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurowanie aplikacji Node.js w systemie Linux dla usługi Azure App Service

Aplikacje node.js musi zostać wdrożony przy użyciu wszystkich wymaganych zależności rozwiązania NPM. Aparat wdrażania usługi App Service (Kudu) automatycznie uruchamia `npm install --production` dla Ciebie podczas wdrażania [repozytorium Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), lub [spakowany pakiet](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) z procesami kompilacji, włączone. W przypadku wdrożenia z plików za pomocą [protokołu FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), jednak należy ręcznie przekazać wymagane pakiety.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów oprogramowania Node.js, którzy korzystają z wbudowanych kontenera systemu Linux w usłudze App Service. Jeśli nie znasz usługi Azure App Service, postępuj zgodnie z [szybkiego startu Node.js](quickstart-nodejs.md) i [Node.js, samouczek bazy danych MongoDB](tutorial-nodejs-mongodb-app.md) pierwszy.

## <a name="show-nodejs-version"></a>Pokaż wersję środowiska Node.js

Aby wyświetlić bieżącą wersję środowiska Node.js, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje środowiska Node.js, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Ustawienie wersji Node.js

Aby ustawić aplikacji [obsługiwanej wersji środowiska Node.js](#show-nodejs-version), uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

To ustawienie określa wersję środowiska Node.js do użycia zarówno w czasie wykonywania, jak i podczas przywracania pakietów automatycznych w Kudu.

> [!NOTE]
> Należy ustawić środowisko Node.js w wersji w twoim projekcie `package.json`. Aparat wdrażania działa w oddzielnym kontener, który zawiera wszystkie obsługiwane wersje środowiska Node.js.

## <a name="configure-nodejs-server"></a>Konfigurowanie serwera Node.js

Kontenery Node.js dołączone [PM2](https://pm2.keymetrics.io/), menedżera procesów produkcyjnych. Można skonfigurować aplikację do uruchamiania z PM2, lub za pomocą pakietu NPM lub za pomocą polecenia niestandardowe.

- [Uruchom polecenie niestandardowe](#run-custom-command)
- [Uruchom Menedżera npm start](#run-npm-start)
- [Uruchom z PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Uruchom polecenie niestandardowe

Usługa App Service można uruchomić aplikacji przy użyciu polecenia niestandardowego, takich jak plik wykonywalny, takich jak *run.sh*. Na przykład, aby uruchomić `npm run start:prod`, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Uruchom Menedżera npm start

Aby rozpocząć korzystanie z usługi aplikacji `npm start`, upewnij się, `start` skrypt znajduje się w *package.json* pliku. Na przykład:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Aby użyć niestandardowego *package.json* w projekcie, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Uruchom z PM2

Kontener automatycznie zaczyna się od aplikacji PM2 po znalezieniu jednego z typowych plików środowiska Node.js w projekcie:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Jedną z następujących [pliki PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* i *ecosystem.config.js*

Można również skonfigurować pliku startowego niestandardowe z następującymi rozszerzeniami:

- A *js* pliku
- A [pliku PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) z rozszerzeniem *.json*, *. config.js*, *.yaml*, lub *yml*

Aby dodać plik Rozpoczęcie niestandardowe, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Debugowanie zdalne

> [!NOTE]
> Zdalne debugowanie jest obecnie w wersji zapoznawczej.

Możesz przeprowadzić debugowanie zdalne w aplikacji Node.js [programu Visual Studio Code](https://code.visualstudio.com/) go do skonfigurowania [uruchamianie za pomocą PM2](#run-with-pm2), z wyjątkiem sytuacji, kiedy uruchomić go za pomocą *. config.js, *.yml, lub *.yaml*.

W większości przypadków żadna dodatkowa konfiguracja jest wymagana dla aplikacji. Jeśli Twoja aplikacja jest uruchamiana z *process.json* pliku (domyślny lub niestandardowy), musi on mieć `script` właściwości w folderze głównym JSON. Na przykład:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Aby skonfigurować dla zdalnego debugowania programu Visual Studio Code, należy zainstalować [rozszerzenia usługi App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Postępuj zgodnie z instrukcjami na stronie rozszerzenia i zaloguj się do platformy Azure w programie Visual Studio Code.

W Eksploratorze usługi Azure, możesz znaleźć aplikacji, aby debugować, kliknij go prawym przyciskiem myszy i wybierz **Rozpocznij debugowanie zdalne**. Kliknij przycisk **tak** włączające ją dla swojej aplikacji. Usługa App Service uruchamia serwer proxy tunelu dla Ciebie i dołącza debuger. Następnie można wysyłać żądania do aplikacji i Zobacz debuger wstrzymanie w punktach podziału.

Po zakończeniu debugowania, Zatrzymaj debuger, wybierając **rozłączenia**. Po wyświetleniu monitu kliknij **tak** Aby wyłączyć zdalne debugowanie. Aby wyłączyć później, kliknij prawym przyciskiem myszy aplikację ponownie w Eksploratorze usługi Azure, a następnie wybierz **wyłączyć zdalne debugowanie**.

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [określić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodu aplikacji. Następnie możesz można uzyskiwać do nich dostęp przy użyciu standardowego wzorca Node.js. Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `NODE_ENV`, użyj następującego kodu:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Uruchom Grunt/Bower/Gulp

Domyślnie uruchamia Kudu `npm install --production` po rozpoznaje aplikacja Node.js została wdrożona. Jeśli aplikacja wymaga, aby automatyzacji popularnych narzędzi, takich jak Grunt, Bower lub Gulp, musisz podać [skrypt wdrożenia niestandardowego](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) do jej uruchomienia.

Aby włączyć repozytorium do uruchamiania tych narzędzi, należy dodać je do zależności w *pliku package.json.* Na przykład:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

W oknie lokalnego terminala Zmień katalog do katalogu głównego repozytorium i uruchom następujące polecenia:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Katalogu głównym repozytorium ma teraz dwie dodatkowe pliki: *.deployment* i *deploy.sh*.

Otwórz *deploy.sh* i Znajdź `Deployment` sekcji, który wygląda następująco:

```bash
##################################################################################################################################
# Deployment
# ----------
```

W tej sekcji kończy się uruchamianie `npm install --production`. Dodaj sekcję kodu, należy uruchomić narzędzie wymagane *na końcu* z `Deployment` sekcji:

- [Bower](#bower)
- [Program gulp](#gulp)
- [Grunt](#grunt)

Zobacz [przykład w próbce MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), gdy skrypt wdrażania uruchamia niestandardowy `npm install` polecenia.

### <a name="bower"></a>Bower

Ten fragment kodu jest uruchamiany `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Program gulp

Ten fragment kodu jest uruchamiany `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ten fragment kodu jest uruchamiany `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. W [Express](https://expressjs.com/), możesz użyć [ufać serwery proxy](https://expressjs.com/guide/behind-proxies.html). Na przykład:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Gdy działającą aplikację Node.js zachowuje się inaczej w usłudze App Service lub zawiera błędy, spróbuj wykonać następujące czynności:

- [Dostęp do strumienia dziennika](#access-diagnostic-logs).
- Testowanie aplikacji lokalnie w trybie produkcyjnym. Usługi App Service uruchamia aplikacji Node.js w trybie produkcyjnym, dlatego należy się upewnić, że projekt działa zgodnie z oczekiwaniami w trybie produkcyjnym lokalnie. Na przykład:
    - W zależności od Twojego *package.json*, różnych pakietach mogą być zainstalowane dla trybu produkcyjnego (`dependencies` a `devDependencies`).
    - Niektóre platformy internetowe mogą wdrożyć pliki statyczne inaczej w trybie produkcyjnym.
    - Niektóre platformy internetowe może używać własne skrypty uruchamiania, podczas uruchamiania w trybie produkcyjnym.
- Uruchom aplikację w usłudze App Service w trybie projektowania. Na przykład w [MEAN.js](https://meanjs.org/), można ustawić aplikację do trybu projektowania w czasie wykonywania przez [ustawienie `NODE_ENV` ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja node.js z bazą danych MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](app-service-linux-faq.md)
