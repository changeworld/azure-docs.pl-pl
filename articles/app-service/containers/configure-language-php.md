---
title: Konfigurowanie aplikacji języka PHP — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować aplikacje PHP do pracy w usłudze Azure App Service
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
ms.openlocfilehash: 11d0648ee5090f02cb96c2d42a8d90cc3ea0ed28
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551340"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurowanie aplikacji w języku PHP w systemie Linux dla usługi Azure App Service

Ten przewodnik pokazuje, jak skonfigurować wbudowanych środowiska uruchomieniowego języka PHP dla aplikacji sieci web, mobilnych zaplecza i aplikacji API apps w usłudze Azure App Service.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów języka PHP, którzy korzystają z wbudowanych kontenera systemu Linux w usłudze App Service. Jeśli nie znasz usługi Azure App Service, postępuj zgodnie z [Szybki Start PHP](quickstart-php.md) i [PHP z MySQL samouczek](tutorial-php-mysql-app.md) pierwszy.

## <a name="show-php-version"></a>Pokaż wersji języka PHP

Aby wyświetlić bieżącą wersję PHP, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka PHP, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Ustaw wersję języka PHP

Uruchom następujące polecenie [Cloud Shell](https://shell.azure.com) umożliwiające ustawienie wersji środowiska PHP 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Uruchom Composer (kompozytor)

Domyślnie nie zostanie uruchomiona Kudu [Composer](https://getcomposer.org/). Aby włączyć automatyzacji narzędzia Composer podczas wdrażania programu Kudu, musisz podać [skrypt wdrożenia niestandardowego](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

W oknie lokalnego terminala przejdź do katalogu głównego repozytorium. Postępuj zgodnie z [kroki instalacji z wiersza polecenia](https://getcomposer.org/download/) można pobrać *composer.phar*.

Uruchom następujące polecenia:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Dodano dwa nowe pliki w uzupełnieniu do katalogu głównym repozytorium *composer.phar*: *.deployment* i *deploy.sh*. Pliki te działają zarówno dla aplikacji Windows i Linux odmian usługi App Service.

Otwórz *deploy.sh* i Znajdź `Deployment` sekcji. Zastąp całą sekcję poniższym kodem:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Zatwierdź wszystkie zmiany i ponownie wdróż swój kod. Composer powinna teraz być uruchomiona jako część automatyzację wdrażania.

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie w kontenerze Wbudowane PHP uruchom serwer Apache. Podczas rozruchu, uruchamia `apache2ctl -D FOREGROUND"`. Jeśli chcesz uruchomić inne polecenie podczas rozruchu, uruchamiając następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [określić ustawienia aplikacji](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) poza kodu aplikacji. Następnie uzyskiwaniu dostępu do nich przy użyciu standardu [getenv()](https://secure.php.net/manual/function.getenv.php) wzorca. Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `DB_HOST`, użyj następującego kodu:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Zmiana lokacji głównej

Struktura sieci web w wybranym mogą używać podkatalogu jako katalogu głównego witryny. Na przykład [platformy Laravel](https://laravel.com/), używa `public/` podkatalogu jako katalogu głównego witryny.

Domyślnego obrazu PHP dla usługi App Service używa platformy Apache i nie pozwala na dostosowywanie katalogu głównego witryny dla aplikacji. Aby obejść to ograniczenie, Dodaj *.htaccess* plik do katalogu głównego repozytorium o następującej zawartości:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Jeśli nie chcesz używać ponownego zapisywania pliku *.htaccess*, zamiast tego możesz wdrożyć aplikację platformy Laravel przy użyciu [niestandardowego obrazu platformy Docker](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. Na platformie [CodeIgniter](https://codeigniter.com/) element [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) domyślnie sprawdza wartość `X_FORWARDED_PROTO`.

## <a name="customize-phpini-settings"></a>Dostosowywanie ustawień w pliku php.ini

Jeśli potrzebujesz wprowadzić zmiany w instalacji PHP, można zmienić dowolne z [dyrektywy php.ini](http://www.php.net/manual/ini.list.php) wykonaj następujące czynności.

> [!NOTE]
> Najlepszym sposobem na wersji środowiska PHP i bieżący *php.ini* konfiguracji jest wywołanie [phpinfo()](https://php.net/manual/function.phpinfo.php) w swojej aplikacji.
>

### <a name="customize-non-phpinisystem-directives"></a>Dostosowywanie innych PHP_INI_SYSTEM dyrektywy

Dostosowywanie PHP_INI_USER PHP_INI_PERDIR i PHP_INI_ALL dyrektywy (zobacz [dyrektywy php.ini](http://www.php.net/manual/ini.list.php)), Dodaj *.htaccess* plik do katalogu głównego aplikacji.

W *.htaccess* plików, dodawanie dyrektyw przy użyciu `php_value <directive-name> <value>` składni. Na przykład:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ponowne wdrażanie aplikacji ze zmianami, a następnie uruchom go ponownie. W przypadku wdrożenia za pomocą aparatu Kudu (na przykład za pomocą [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), zostanie automatycznie uruchomiony ponownie po wdrożeniu.

Jako alternatywa dla użycia *.htaccess*, możesz użyć [ini_set()](http://www.php.net/manual/function.ini-set.php) w swojej aplikacji, aby dostosować te dyrektywy bez PHP_INI_SYSTEM.

### <a name="customize-phpinisystem-directives"></a>Customize PHP_INI_SYSTEM directives

Aby dostosować PHP_INI_SYSTEM dyrektywy (zobacz [dyrektywy php.ini](http://www.php.net/manual/ini.list.php)), nie można użyć *.htaccess* podejście. Usługa App Service udostępnia oddzielny mechanizm przy użyciu `PHP_INI_SCAN_DIR` ustawienia aplikacji.

Po pierwsze, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com) dodać aplikację nosi nazwę `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` jest domyślnego katalogu, w którym *php.ini* istnieje. `/home/site/ini` jest niestandardowym katalogu, w którym należy dodać niestandardowy *.ini* pliku. Oddziel wartości z `:`.

Przejdź do sieci web sesję SSH z kontenera systemu Linux (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Utwórz katalog w `/home/site` o nazwie `ini`, następnie utwórz *.ini* w pliku `/home/site/ini` katalog (na przykład *plik settings.ini)* przy użyciu dyrektyw, który chcesz dostosować. Użyj tej samej składni, należy użyć w *php.ini* pliku. 

> [!TIP]
> W przypadku wbudowanych kontenerów systemu Linux w usłudze App Service */home* służy jako trwałego magazynu udostępnionego. 
>

Na przykład, aby zmienić wartość [expose_php](http://php.net/manual/ini.core.php#ini.expose-php) uruchom następujące polecenia:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby zmiany zaczęły obowiązywać należy ponownie uruchomić aplikację.

## <a name="enable-php-extensions"></a>Włącz rozszerzenia języka PHP

Wbudowane instalacji PHP zawiera najczęściej używanymi rozszerzeniami. Możesz włączyć dodatkowe rozszerzenia w taki sam sposób, jak [dostosowywanie dyrektywy php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Najlepszym sposobem na wersji środowiska PHP i bieżący *php.ini* konfiguracji jest wywołanie [phpinfo()](https://php.net/manual/function.phpinfo.php) w swojej aplikacji.
>

Aby włączyć dodatkowe rozszerzenia, wykonując następujące czynności:

Dodaj `bin` katalog do katalogu głównego aplikacji i put `.so` rozszerzenia plików (na przykład *mongodb.so*). Upewnij się, że rozszerzenia są zgodne z wersji środowiska PHP w platformie Azure oraz czy VC9 i zgodnego z innego niż wątkowo (nts).

Wdróż swoje zmiany.

Postępuj zgodnie z instrukcjami w [dyrektywy dostosować PHP_INI_SYSTEM](#customize-php_ini_system-directives), dodawanie rozszerzeń do niestandardowej *.ini* plik z [rozszerzenia](https://www.php.net/manual/ini.core.php#ini.extension) lub [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) dyrektywy.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Aby zmiany zaczęły obowiązywać należy ponownie uruchomić aplikację.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Gdy działającą aplikację PHP zachowuje się inaczej w usłudze App Service lub zawiera błędy, spróbuj wykonać następujące czynności:

- [Dostęp do strumienia dziennika](#access-diagnostic-logs).
- Testowanie aplikacji lokalnie w trybie produkcyjnym. Usługi App Service uruchamia aplikacji Node.js w trybie produkcyjnym, dlatego należy się upewnić, że projekt działa zgodnie z oczekiwaniami w trybie produkcyjnym lokalnie. Na przykład:
    - W zależności od Twojego *composer.json*, różnych pakietach mogą być zainstalowane dla trybu produkcyjnego (`require` a `require-dev`).
    - Niektóre platformy internetowe mogą wdrożyć pliki statyczne inaczej w trybie produkcyjnym.
    - Niektóre platformy internetowe może używać własne skrypty uruchamiania, podczas uruchamiania w trybie produkcyjnym.
- Uruchom aplikację w usłudze App Service w trybie debugowania. Na przykład w [platformy Laravel](http://meanjs.org/), można skonfigurować aplikację, aby dane wyjściowe komunikaty debugowania w środowisku produkcyjnym przez [ustawienie `APP_DEBUG` ustawienia aplikacji `true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="robots933456"></a>robots933456

Może zostać wyświetlony następujący komunikat w dziennikach kontenera:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Można bezpiecznie zignorować ten komunikat. `/robots933456.txt` jest fikcyjnego ścieżka URL, która korzysta z usługi App Service do sprawdzenia, czy kontener jest w stanie obsłużyć żądania. Odpowiedzi 404 po prostu wskazuje, że ścieżka nie istnieje, ale dzięki temu wiadomo, czy kontener jest w dobrej kondycji i gotowy do odpowiadania na żądania w usłudze App Service.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja PHP z bazą danych MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](app-service-linux-faq.md)