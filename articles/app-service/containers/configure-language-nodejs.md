---
title: Konfigurowanie aplikacji Node.js
description: Dowiedz się, jak skonfigurować wstępnie utworzony kontener Node.js dla aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252729"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurowanie aplikacji Linux Node.js dla usługi Azure App Service

Aplikacje Node.js muszą być wdrażane ze wszystkimi wymaganymi zależnościami NPM. Aparat wdrażania usługi App Service (Kudu) jest automatycznie uruchamiany `npm install --production` po wdrożeniu [repozytorium Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)lub pakietu [Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) z włączonymi procesami kompilacji. Jednak w przypadku wdrażania plików przy użyciu [protokołu FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)należy ręcznie przesłać wymagane pakiety.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla deweloperów Node.js, którzy używają wbudowanego kontenera systemu Linux w usłudze App Service. Jeśli nigdy nie używasz usługi Azure App Service, wykonaj najpierw przewodnik [Szybki start node.js](quickstart-nodejs.md) i [node.js z mongodb samouczka.](tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Pokaż wersję node.js

Aby wyświetlić bieżącą wersję node.js, uruchom następujące polecenie w [aplikacji Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje node.js, uruchom następujące polecenie w [aplikacji Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Ustawianie wersji node.js

Aby ustawić aplikację na [obsługiwaną wersję node.js,](#show-nodejs-version)uruchom następujące polecenie w [aplikacji Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

To ustawienie określa wersję node.js, której należy używać, zarówno w czasie wykonywania, jak i podczas automatycznego przywracania pakietu w Kudu.

> [!NOTE]
> Należy ustawić wersję Node.js w projekcie `package.json`. Aparat wdrażania działa w oddzielnym kontenerze, który zawiera wszystkie obsługiwane wersje Node.js.

## <a name="customize-build-automation"></a>Dostosowywanie automatyzacji kompilacji

Jeśli wdrożysz aplikację przy użyciu pakietów Git lub zip z włączoną automatyzacją kompilacji, kroki automatyzacji kompilacji usługi App Service w następującej kolejności:

1. Uruchom skrypt niestandardowy, jeśli jest określony przez `PRE_BUILD_SCRIPT_PATH`plik .
1. Uruchom `npm install` bez żadnych flag, `preinstall` który `postinstall` zawiera npm `devDependencies`i skrypty, a także instaluje .
1. Uruchom, `npm run build` jeśli skrypt kompilacji jest określony w *pliku package.json*.
1. Uruchom, `npm run build:azure` jeśli skrypt build:azure jest określony w *pliku package.json*.
1. Uruchom skrypt niestandardowy, jeśli jest określony przez `POST_BUILD_SCRIPT_PATH`plik .

> [!NOTE]
> Zgodnie z [opisem](https://docs.npmjs.com/misc/scripts)w npm `prebuild` docs , skrypty nazwane i `postbuild` uruchamiane przed i po `build`, odpowiednio, jeśli są określone. `preinstall`i `postinstall` uruchomić odpowiednio `install`przed i po.

`PRE_BUILD_COMMAND`i `POST_BUILD_COMMAND` są zmienne środowiskowe, które są domyślnie puste. Aby uruchomić polecenia przed kompilacją, zdefiniuj `PRE_BUILD_COMMAND`plik . Aby uruchomić polecenia po kompilacji, zdefiniuj `POST_BUILD_COMMAND`plik .

Poniższy przykład określa dwie zmienne do serii poleceń, oddzielone przecinkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Aby uzyskać dodatkowe zmienne środowiskowe w celu dostosowania automatyzacji kompilacji, zobacz [Konfiguracja Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Aby uzyskać więcej informacji na temat sposobu działania i tworzenia aplikacji Node.js w systemie Windows w systemie Linux, zobacz [dokumentację oryxa: Jak aplikacje Node.js są wykrywane i budowane.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)

## <a name="configure-nodejs-server"></a>Konfigurowanie serwera Node.js

Kontenery Node.js są wyposażone w [pm2](https://pm2.keymetrics.io/), menedżera procesów produkcyjnych. Aplikację można skonfigurować tak, aby zaczynała się od pm2, npm lub za pomocą polecenia niestandardowego.

- [Uruchom polecenie niestandardowe](#run-custom-command)
- [Uruchom npm start](#run-npm-start)
- [Biegnij z PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Uruchom polecenie niestandardowe

Usługa App Service może uruchamiać aplikację za pomocą polecenia niestandardowego, takiego jak plik wykonywalny, taki jak *run.sh*. Na przykład, `npm run start:prod`aby uruchomić, uruchom następujące polecenie w [przysłonie chmury:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Uruchom npm start

Aby uruchomić aplikację `npm start`przy użyciu `start` programu , upewnij się, że skrypt znajduje się w pliku *package.json.* Przykład:

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

Aby użyć niestandardowego *pliku package.json* w projekcie, uruchom następujące polecenie w [aplikacji Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Biegnij z PM2

Kontener automatycznie uruchamia aplikację z pm2, gdy jeden z typowych plików Node.js zostanie znaleziony w projekcie:

- *pojemnik/www*
- *server.js*
- *plik app.js*
- *indeks.js*
- *hostingstart.js*
- Jeden z następujących [plików PM2:](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) *process.json* i *ecosystem.config.js*

Można również skonfigurować niestandardowy plik startowy z następującymi rozszerzeniami:

- Plik *.js*
- [Plik PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) z rozszerzeniem *.json*, *.config.js*, *.yaml*, lub *.yml*

Aby dodać niestandardowy plik startowy, uruchom następujące polecenie w [połocie Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Debugowanie zdalne

> [!NOTE]
> Zdalne debugowanie jest obecnie w wersji zapoznawczej.

Aplikację Node.js można debugować zdalnie w [programie Visual Studio Code,](https://code.visualstudio.com/) jeśli skonfigurujesz ją tak, aby [działała z pm2](#run-with-pm2), z wyjątkiem sytuacji, gdy jest uruchamiana przy użyciu pliku *.config.js, *.yml lub *.yaml*.

W większości przypadków nie jest wymagana żadna dodatkowa konfiguracja dla aplikacji. Jeśli aplikacja jest uruchamiana z plikiem *process.json* (domyślnym lub niestandardowym), musi mieć `script` właściwość w katalogu głównym JSON. Przykład:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Aby skonfigurować program Visual Studio Code do zdalnego debugowania, zainstaluj [rozszerzenie usługi App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Postępuj zgodnie z instrukcjami na stronie rozszerzenia i zaloguj się do platformy Azure w programie Visual Studio Code.

W Eksploratorze platformy Azure znajdź aplikację, którą chcesz debugować, kliknij ją prawym przyciskiem myszy i wybierz polecenie **Rozpocznij zdalne debugowanie**. Kliknij **przycisk Tak,** aby włączyć ją dla aplikacji. Usługa App Service uruchamia serwer proxy tunelu dla Ciebie i dołącza debugera. Następnie można wywrzeć żądania do aplikacji i wyświetlić debuger wstrzymywania w punktach przerwania.

Po zakończeniu debugowania zatrzymaj debuger, wybierając **pozycję Rozłącz**. Po wyświetleniu monitu należy kliknąć przycisk **Tak,** aby wyłączyć debugowanie zdalne. Aby wyłączyć ją później, kliknij ponownie aplikację w Eksploratorze platformy Azure i wybierz pozycję **Wyłącz debugowanie zdalne**.

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [ustawić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp przy użyciu standardowego wzorca Node.js. Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `NODE_ENV`, użyj następującego kodu:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Uruchom Grunt/Bower/Gulp

Domyślnie Kudu `npm install --production` działa po rozpoznaniu aplikacji Node.js jest wdrażany. Jeśli aplikacja wymaga dowolnego z popularnych narzędzi automatyzacji, takich jak Grunt, Bower lub Gulp, należy podać [niestandardowy skrypt wdrażania,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) aby go uruchomić.

Aby włączyć repozytorium do uruchamiania tych narzędzi, należy dodać je do zależności w *package.json.* Przykład:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

W oknie terminala lokalnego zmień katalog na katalog główny repozytorium i uruchom następujące polecenia:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Katalog główny repozytorium ma teraz dwa dodatkowe pliki: *.deployment* i *deploy.sh*.

Otwórz *deploy.sh* i znajdź `Deployment` sekcję, która wygląda następująco:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ta sekcja kończy `npm install --production`się uruchomieniem . Dodaj sekcję kodu, której potrzebujesz, aby uruchomić `Deployment` wymagane narzędzie na *końcu* sekcji:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Zobacz [przykład w przykładzie MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), gdzie skrypt `npm install` wdrażania uruchamia również polecenie niestandardowe.

### <a name="bower"></a>Bower

Ten fragment kodu `bower install`jest uruchamiany .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ten fragment kodu `gulp imagemin`jest uruchamiany .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ten fragment kodu `grunt`jest uruchamiany .

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

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. W [ekspresowym](https://expressjs.com/)programie można użyć [serwerów proxy zaufania](https://expressjs.com/guide/behind-proxies.html). Przykład:

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

Gdy działająca aplikacja Node.js zachowuje się inaczej w usłudze App Service lub ma błędy, spróbuj wykonać następujące czynności:

- [Dostęp do strumienia dziennika](#access-diagnostic-logs).
- Przetestuj aplikację lokalnie w trybie produkcyjnym. Usługa App Service uruchamia aplikacje Node.js w trybie produkcyjnym, dlatego należy upewnić się, że projekt działa zgodnie z oczekiwaniami w trybie produkcji lokalnie. Przykład:
    - W zależności od *pliku package.json,* różne pakiety mogą być instalowane w trybie produkcji (w`dependencies` porównaniu z. `devDependencies`).
    - Niektóre struktury sieci web mogą inaczej wdrażać pliki statyczne w trybie produkcyjnym.
    - Niektóre struktury sieci web mogą używać niestandardowych skryptów startowych podczas pracy w trybie produkcyjnym.
- Uruchom aplikację w usłudze App Service w trybie programowania. Na przykład w [pliku MEAN.js](https://meanjs.org/)można ustawić aplikację na tryb rozwoju w czasie wykonywania, [ustawiając ustawienie `NODE_ENV` aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja Node.js z MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Często zadawane pytania dotyczące usługi aplikacji Linux](app-service-linux-faq.md)
