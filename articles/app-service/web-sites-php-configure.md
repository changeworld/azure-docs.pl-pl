---
title: Skonfigurować PHP w aplikacjach sieci Web usługi aplikacji Azure
description: Dowiedz się, jak skonfigurować domyślnej instalacji PHP lub Dodaj niestandardowy instalacji PHP dla aplikacji sieci Web w usłudze Azure App Service.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: d7b832659272f01b6e939150fa89626620d5eab7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Skonfigurować PHP w aplikacjach sieci Web usługi aplikacji Azure

## <a name="introduction"></a>Wprowadzenie

W tym przewodniku przedstawiono sposób konfigurowania wbudowanych środowiska uruchomieniowego języka PHP dla aplikacji sieci Web w [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), podaj niestandardowego środowiska uruchomieniowego języka PHP i Włącz rozszerzenia. Aby korzystać z usługi aplikacji, należy zarejestrować się w celu [bezpłatnej wersji próbnej]. Aby uzyskać najbardziej z tego przewodnika, należy najpierw utworzyć aplikację sieci web PHP w usłudze App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Porady: zmiana wbudowanych wersji PHP

Domyślnie PHP 5.6 jest zainstalowana i natychmiast dostępnej do użycia podczas tworzenia aplikacji sieci web usługi aplikacji. Najlepszym sposobem Zobacz poprawki dostępnych wersji domyślnej konfiguracji i włączone rozszerzenia jest wdrożenie skryptu, który wywołuje [phpinfo()] funkcji.

Wersji PHP w wersji 7.0 i PHP 7.2 są również dostępne, ale nie jest włączony domyślnie. Aby zaktualizować wersję PHP, wykonaj jedną z następujących metod:

### <a name="azure-portal"></a>Azure Portal

1. Przejdź do aplikacji sieci web w [portalu Azure](https://portal.azure.com) i wybierz polecenie **ustawienia** przycisku.

    ![Ustawienia aplikacji sieci Web][settings-button]
1. Z **ustawienia** bloku, wybierz opcję **ustawienia aplikacji** i wybierz nową wersję PHP.

    ![Ustawienia aplikacji][application-settings]
1. Kliknij przycisk **zapisać** przycisk w górnej części **ustawienia aplikacji w sieci Web** bloku.

    ![Zapisanie ustawień konfiguracji][save-button]

### <a name="azure-powershell-windows"></a>Program Azure PowerShell (system Windows)

1. Otwórz programu Azure PowerShell, a następnie zaloguj się do swojego konta:

        PS C:\> Connect-AzureRmAccount
1. Ustaw wersję PHP dla aplikacji sieci web.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. Wersja PHP ma teraz wartość. Można potwierdzić te ustawienia:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli-20-linux-mac-windows"></a>Azure CLI 2.0 (Linux, Mac, system Windows)

Aby użyć interfejsu wiersza polecenia platformy Azure, musisz [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na tym komputerze.

1. Otwórz okno terminalu i logowania do konta.

        az login

1. Sprawdź listę obsługiwanych środowisk uruchomieniowych.

        az webapp list-runtimes | grep php

1. Ustaw wersję PHP dla aplikacji sieci web.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. Wersja PHP ma teraz wartość. Można potwierdzić te ustawienia:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Porady: Zmienianie wbudowanych konfiguracji PHP

Dla dowolnego wbudowanych środowiska uruchomieniowego języka PHP można zmienić opcje konfiguracji, wykonując następujące kroki. (Informacje o dyrektywy php.ini znajdują się w temacie [listy dyrektywy php.ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Zmiana PHP\_INI\_użytkownika, PHP\_INI\_PERDIR, PHP\_INI\_wszystkie ustawienia konfiguracji

1. Dodaj [. user.ini] plik do katalogu głównego.
1. Ustawienia konfiguracji w celu dodania `.user.ini` plików przy użyciu takiej samej składni, należy użyć w `php.ini` pliku. Na przykład, jeśli chcesz włączyć `display_errors` ustawienie i ustaw `upload_max_filesize` ustawienie 10M, Twoje `.user.ini` plik będzie zawierać ten tekst:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. Wdrażanie aplikacji sieci web.
1. Uruchom ponownie aplikację sieci web. (Ponowne uruchomienie jest konieczne, ponieważ odczytuje częstotliwość, z którym PHP `.user.ini` podlega pliki `user_ini.cache_ttl` ustawienie, które jest to ustawienie systemowe i jest domyślnie 300 sekund (5 minut). Ponowne uruchamianie aplikacji sieci web wymusza PHP, aby odczytać nowe ustawienia w `.user.ini` pliku.)

Alternatywą wobec przy użyciu `.user.ini` plików, można użyć [ini_set()] funkcji w skryptach, aby ustawić opcje konfiguracji, które nie są dyrektywy na poziomie systemu.

### <a name="changing-phpinisystem-configuration-settings"></a>Zmiana PHP\_INI\_ustawień konfiguracji systemu

1. Dodaj ustawienie aplikacji do aplikacji sieci Web przy użyciu klucza `PHP_INI_SCAN_DIR` i wartości `d:\home\site\ini`
1. Utwórz `settings.ini` plików przy użyciu konsoli Kudu (http://&lt;-Nazwa lokacji&gt;. scm.azurewebsite.net) w `d:\home\site\ini` katalogu.
1. Ustawienia konfiguracji w celu dodania `settings.ini` plików przy użyciu takiej samej składni, które ma zostać użyty w pliku php.ini. Na przykład, jeśli chcesz punktu `curl.cainfo` ustawienie `*.crt` pliku i określ dla ustawienia "wincache.maxfilesize" 512 KB, Twoje `settings.ini` plik będzie zawierać ten tekst:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Aby ponownie załadować zmiany, należy ponownie uruchomić aplikacji sieci Web.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Porady: Włącz rozszerzenia w środowisku wykonawczym PHP domyślne

Jak wspomniano w poprzedniej sekcji, najlepiej domyślnej wersji PHP, domyślnej konfiguracji i włączone rozszerzenia jest wdrożenie skrypt, który wywołuje [phpinfo()]. Aby włączyć dodatkowe rozszerzenia, wykonaj następujące czynności:

### <a name="configure-via-ini-settings"></a>Konfigurowanie za pomocą ustawienia ini

1. Dodaj `ext` do katalogu `d:\home\site` katalogu.
1. Umieść `.dll` rozszerzenia plików `ext` katalog (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją PHP i są VC9 i zgodne z systemem innym niż wątkowo (nts).
1. Dodaj ustawienie aplikacji do aplikacji sieci Web przy użyciu klucza `PHP_INI_SCAN_DIR` i wartości `d:\home\site\ini`
1. Utwórz `ini` w pliku `d:\home\site\ini` o nazwie `extensions.ini`.
1. Ustawienia konfiguracji w celu dodania `extensions.ini` plików przy użyciu takiej samej składni, które ma zostać użyty w pliku php.ini. Na przykład, jeśli chcesz włączyć rozszerzenia bazy danych MongoDB i narzędzia XDebug Twoje `extensions.ini` plik będzie zawierać ten tekst:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Ponownie uruchom aplikację sieci Web, aby załadować zmiany.

### <a name="configure-via-app-setting"></a>Konfigurowanie za pomocą ustawienia aplikacji

1. Dodaj `bin` do katalogu głównego katalogu.
1. Umieść `.dll` rozszerzenia plików `bin` katalog (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją PHP i są VC9 i zgodne z systemem innym niż wątkowo (nts).
1. Wdrażanie aplikacji sieci web.
1. Przejdź do aplikacji sieci web w portalu Azure i wybierz polecenie **ustawienia** przycisku.

    ![Ustawienia aplikacji sieci Web][settings-button]
1. Z **ustawienia** bloku, wybierz opcję **ustawienia aplikacji** i przewiń do **ustawień aplikacji** sekcji.
1. W **ustawień aplikacji** sekcji, Utwórz **PHP_EXTENSIONS** klucza. Wartość tego klucza będzie ścieżka względem katalogu głównego witryny sieci Web: **bin\your ext, rozszerzenie pliku**.

    ![Włączanie rozszerzenia w ustawieniach aplikacji][php-extensions]
1. Kliknij przycisk **zapisać** przycisk w górnej części **ustawienia aplikacji w sieci Web** bloku.

    ![Zapisanie ustawień konfiguracji][save-button]

Zend rozszerzenia również są obsługiwane przy użyciu **PHP_ZENDEXTENSIONS** klucza. Aby włączyć wiele rozszerzeń, zawiera listę rozdzielaną przecinkami `.dll` pliki do wartości ustawienia aplikacji.

## <a name="how-to-use-a-custom-php-runtime"></a>Porady: Użyj niestandardowego środowiska uruchomieniowego języka PHP

Zamiast domyślnego środowiska uruchomieniowego języka PHP aplikacje sieci Web usługi aplikacji można użyć środowiska uruchomieniowego języka PHP, które umożliwiają wykonywanie skryptów PHP. Środowisko uruchomieniowe, które należy podać można skonfigurować przy `php.ini` pliku, który też podać. Aby użyć niestandardowego środowiska uruchomieniowego języka PHP z aplikacjami sieci Web, wykonaj następujące kroki.

1. Uzyskaj z systemem innym niż wątkowo, VC9 lub VC11 zgodnej wersji programu PHP dla systemu Windows. Najnowsze wersje PHP dla systemu Windows można znaleźć tutaj: [ http://windows.php.net/download/ ]. Starsze wersje można znaleźć w tym miejscu archiwum: [ http://windows.php.net/downloads/releases/archives/ ].
1. Modyfikowanie `php.ini` Twojego środowiska uruchomieniowego w pliku. Ignoruje wszystkie ustawienia konfiguracji, które są dyrektywy system poziom — tylko do aplikacji sieci Web. (Informacje dyrektywy system poziom wyłącznie-, zobacz [listy dyrektywy php.ini]).
1. Opcjonalnie można dodać rozszerzenia do Twojego środowiska uruchomieniowego języka PHP i włączyć je w `php.ini` pliku.
1. Dodaj `bin` do katalogu głównego i put katalog, który zawiera Twojego środowiska uruchomieniowego języka PHP w tym katalogu (na przykład `bin\php`).
1. Wdrażanie aplikacji sieci web.
1. Przejdź do aplikacji sieci web w portalu Azure i wybierz polecenie **ustawienia** przycisku.

    ![Ustawienia aplikacji sieci Web][settings-button]
1. Z **ustawienia** bloku, wybierz opcję **ustawienia aplikacji** i przewiń do **mapowania obsługi** sekcji. Dodaj `*.php` rozszerzenie pola i Dodaj ścieżkę do `php-cgi.exe` pliku wykonywalnego. Jeśli umieścisz Twojego środowiska uruchomieniowego języka PHP `bin` katalog w katalogu głównego aplikacji, ścieżka jest `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Określ w mapowania programu obsługi program obsługi][handler-mappings]
1. Kliknij przycisk **zapisać** przycisk w górnej części **ustawienia aplikacji w sieci Web** bloku.

    ![Zapisanie ustawień konfiguracji][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Porady: Automatyzowanie Composer na platformie Azure

Domyślnie usługi aplikacji nie są wykonywane z composer.json, jeśli istnieje w projekcie PHP. Jeśli używasz [wdrożenia Git](app-service-deploy-local-git.md), można włączyć composer.json przetwarzania podczas `git push` , należy włączyć rozszerzenie Composer.

> [!NOTE]
> Możesz [głos najwyższej jakości Composer pomocy technicznej w usłudze App Service w tym miejscu](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. W przypadku programu PHP sieci web bloku aplikacji w [portalu Azure](https://portal.azure.com), kliknij przycisk **narzędzia** > **rozszerzenia**.

    ![Blok ustawień portalu Azure w celu Automatyzowanie Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kliknij przycisk **Dodaj**, następnie kliknij przycisk **Composer**.

    ![Dodaj rozszerzenie Composer Automatyzowanie Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Kliknij przycisk **OK** zaakceptować postanowienia prawne. Kliknij przycisk **OK** ponownie, aby dodać rozszerzenie.

    **Zainstalowanych rozszerzeń** bloku pokazuje rozszerzenie Composer.
    ![Zaakceptuj postanowienia prawne Automatyzowanie Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Teraz, w okno terminalu na komputerze lokalnym, należy wykonać `git add`, `git commit`, i `git push` do aplikacji sieci Web. Zwróć uwagę, czy Composer jest instalowany zdefiniowane w composer.json zależności.

    ![Wdrażanie Git za pomocą automatyzacji Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](/develop/php/).

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://azure.microsoft.com/try/app-service/) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację sieci Web o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
>

[bezpłatnej wersji próbnej]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[listy dyrektywy php.ini]: http://www.php.net/manual/en/ini.list.php
[. user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
