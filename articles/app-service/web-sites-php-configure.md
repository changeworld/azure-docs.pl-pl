---
title: Konfigurowanie obsługi języka PHP w usłudze Azure App Service Web Apps
description: Dowiedz się, jak skonfigurować domyślnej instalacji PHP lub dodać do instalacji niestandardowej PHP dla usługi Web Apps w usłudze Azure App Service.
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
ms.openlocfilehash: 1e5f7ed2fb4c77e0a738cbe6ee6c84b46bc59bb8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230839"
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Konfigurowanie obsługi języka PHP w usłudze Azure App Service Web Apps

## <a name="introduction"></a>Wprowadzenie

Ten przewodnik pokazuje, jak skonfigurować wbudowanych środowiska uruchomieniowego języka PHP dla usługi Web Apps w [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), podaj niestandardowego środowiska uruchomieniowego języka PHP oraz włączania rozszerzeń. Aby korzystać z usługi App Service, należy zasubskrybować [bezpłatna wersja próbna]. Aby uzyskać najbardziej z tego przewodnika, należy najpierw utworzyć aplikacji sieci web w języku PHP w usłudze App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Porady: Zmienianie wbudowanych wersji języka PHP

PHP 5.6 jest domyślnie, jest zainstalowana i natychmiast dostępny do użycia podczas tworzenia aplikacji sieci web usługi App Service. Najlepszym sposobem, aby wyświetlić poprawkę dostępną wersją konfiguracji domyślnej i włączono rozszerzenia jest wdrożenie skryptu, który wywołuje [phpinfo()] funkcji.

Środowisko PHP 7.0 i języka PHP 7.2 wersje są również dostępne, ale nie jest włączony domyślnie. Aby zaktualizować wersję języka PHP, wykonaj jedną z następujących metod:

### <a name="azure-portal"></a>Azure Portal

1. Przejdź do aplikacji sieci web w [witryny Azure portal](https://portal.azure.com) i kliknij pozycję **ustawienia** przycisku.

    ![Ustawienia aplikacji sieci Web][settings-button]
1. Z **ustawienia** bloku wybierz **ustawienia aplikacji** i wybierz nową wersję PHP.

    ![Ustawienia aplikacji][application-settings]
1. Kliknij przycisk **Zapisz** znajdujący się u góry **ustawienia aplikacji sieci Web** bloku.

    ![Zapisanie ustawień konfiguracji][save-button]

### <a name="azure-powershell-windows"></a>Program Azure PowerShell (Windows)

1. Otwieranie programu Azure PowerShell i zaloguj się do swojego konta:

        PS C:\> Connect-AzureRmAccount
1. Ustawianie wersji języka PHP, dla aplikacji sieci web.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. Wersja języka PHP są teraz skonfigurowane. Możesz sprawdzić te ustawienia:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Aby użyć interfejsu wiersza polecenia platformy Azure, musisz mieć [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na tym komputerze.

1. Otwórz okno terminalu i zaloguj się do swojego konta.

        az login

1. Sprawdź listę obsługiwanych środowisk uruchomieniowych.

        az webapp list-runtimes | grep php

1. Ustawianie wersji języka PHP, dla aplikacji sieci web.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. Wersja języka PHP są teraz skonfigurowane. Możesz sprawdzić te ustawienia:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Porady: Zmienianie wbudowanych konfiguracji PHP

Do wszelkich wbudowanych środowiska uruchomieniowego języka PHP możesz zmienić opcje konfiguracji wykonaj następujące czynności. (Aby uzyskać informacji dotyczących dyrektyw w pliku php.ini, zobacz [Lista dyrektywy w pliku php.ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Zmiana języka PHP\_INI\_użytkownika, PHP\_INI\_PERDIR, PHP\_INI\_wszystkie ustawienia konfiguracji

1. Dodaj [. user.ini] plik do katalogu głównego.
1. Dodaj ustawienia konfiguracji w celu `.user.ini` plików przy użyciu tej samej składni, należy użyć w `php.ini` pliku. Na przykład, jeśli chcesz włączyć `display_errors` ustawienie i ustaw `upload_max_filesize` ustawienie 10 mln swoje `.user.ini` plik będzie zawierać ten tekst:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. Wdrażanie aplikacji sieci web.
1. Uruchom ponownie aplikację internetową. (Ponowne uruchomienie jest konieczne, ponieważ odczytuje częstotliwość, z których PHP `.user.ini` plików jest regulowane przez `user_ini.cache_ttl` ustawienie, to ustawienie poziomie systemu, która domyślnie jest 300 sekund (5 minut). Trwa ponowne uruchamianie aplikacji sieci web wymusza PHP można odczytać nowe ustawienia w `.user.ini` pliku.)

Jako alternatywa dla użycia `.user.ini` pliku, można użyć [ini_set()] funkcji w skryptach, aby ustawić opcje konfiguracji, które nie są dyrektywami poziom systemu.

### <a name="changing-phpinisystem-configuration-settings"></a>Zmiana języka PHP\_INI\_ustawienia konfiguracji systemu

1. Dodaj ustawienie aplikacji do aplikacji sieci Web przy użyciu klucza `PHP_INI_SCAN_DIR` i wartości `d:\home\site\ini`
1. Tworzenie `settings.ini` plików przy użyciu konsoli Kudu (http://&lt;Nazwa lokacji&gt;. scm.azurewebsite.net) w `d:\home\site\ini` katalogu.
1. Dodaj ustawienia konfiguracji w celu `settings.ini` plików przy użyciu tej samej składni, należy użyć w `php.ini` pliku. Na przykład, jeśli chce się punktu `curl.cainfo` ustawienie `*.crt` plik, a dla ustawienia "wincache.maxfilesize" 512 KB, Twoje `settings.ini` plik będzie zawierać ten tekst:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Aby załadować ponownie zmiany, uruchom ponownie swoją aplikację sieci Web.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Porady: Włączanie rozszerzeń w domyślnego środowiska uruchomieniowego PHP

Jak wspomniano w poprzedniej sekcji, najlepszym sposobem, aby wyświetlić domyślną wersję języka PHP, domyślnej konfiguracji i włączono rozszerzenia jest wdrożenie skryptu, który wywołuje [phpinfo()]. Aby włączyć dodatkowe rozszerzenia, wykonując następujące czynności:

### <a name="configure-via-ini-settings"></a>Skonfiguruj za pomocą ustawienia ini

1. Dodaj `ext` do katalogu `d:\home\site` katalogu.
1. Umieść `.dll` rozszerzenia plików `ext` katalog (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją języka PHP i czy VC9 i zgodnego z innego niż wątkowo (nts).
1. Dodaj ustawienie aplikacji do aplikacji sieci Web przy użyciu klucza `PHP_INI_SCAN_DIR` i wartości `d:\home\site\ini`
1. Tworzenie `ini` w pliku `d:\home\site\ini` o nazwie `extensions.ini`.
1. Dodaj ustawienia konfiguracji w celu `extensions.ini` plików przy użyciu tej samej składni, należy użyć w `php.ini` pliku. Na przykład, jeśli chcesz włączyć rozszerzenia bazy danych MongoDB i narzędzia XDebug swoje `extensions.ini` plik będzie zawierać ten tekst:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Uruchom ponownie swoją aplikację sieci Web do zmiany obciążenia.

### <a name="configure-via-app-setting"></a>Konfigurowanie poprzez ustawienie aplikacji

1. Dodaj `bin` katalog do katalogu głównego.
1. Umieść `.dll` rozszerzenia plików `bin` katalog (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją języka PHP i czy VC9 i zgodnego z innego niż wątkowo (nts).
1. Wdrażanie aplikacji sieci web.
1. Przejdź do aplikacji sieci web w witrynie Azure portal, a następnie kliknij pozycję **ustawienia** przycisku.

    ![Ustawienia aplikacji sieci Web][settings-button]
1. Z **ustawienia** bloku wybierz **ustawienia aplikacji** i przewiń do **ustawienia aplikacji** sekcji.
1. W **ustawienia aplikacji** sekcji, Utwórz **PHP_EXTENSIONS** klucza. Wartość tego klucza będzie ścieżka względem katalogu głównego witryny sieci Web: **bin\your ext, rozszerzenie pliku**.

    ![Włączanie rozszerzenia w ustawieniach aplikacji][php-extensions]
1. Kliknij przycisk **Zapisz** znajdujący się u góry **ustawienia aplikacji sieci Web** bloku.

    ![Zapisanie ustawień konfiguracji][save-button]

Rozwiązanie Zend rozszerzenia również są obsługiwane przy użyciu **PHP_ZENDEXTENSIONS** klucza. Aby włączyć wiele rozszerzeń, należy dołączyć rozdzielaną przecinkami listę `.dll` pliki wartość ustawienia aplikacji.

## <a name="how-to-use-a-custom-php-runtime"></a>Porady: Użyj niestandardowego środowiska uruchomieniowego języka PHP

Zamiast domyślnego środowiska uruchomieniowego PHP App Service Web Apps można użyć środowiska uruchomieniowego języka PHP, który udostępnić wykonywanie skryptów w języku PHP. Środowisko uruchomieniowe, które należy podać mogą być konfigurowane przez `php.ini` plików, które zapewniają również. Aby użyć niestandardowego środowiska uruchomieniowego języka PHP z usługą Web Apps, wykonaj następujące kroki.

1. Uzyskaj bez wątkowo, VC9 lub VC11 zgodnej wersji programu PHP dla Windows. Najnowsze wersje systemu Windows dla PHP można znaleźć tutaj: [ http://windows.php.net/download/ ]. Starsze wersje można znaleźć w archiwum, w tym miejscu: [ http://windows.php.net/downloads/releases/archives/ ].
1. Modyfikowanie `php.ini` pliku dla Twojego środowiska uruchomieniowego. Ustawienia konfiguracji, które są dyrektywami system poziom — tylko są ignorowane przez aplikacje sieci Web. (Uzyskać informacji dotyczących systemu poziom — tylko do dyrektyw, zobacz [Lista dyrektywy w pliku php.ini]).
1. Opcjonalnie dodaj rozszerzenia do Twojego środowiska uruchomieniowego języka PHP i włączyć je w `php.ini` pliku.
1. Dodaj `bin` katalog do katalogu głównego i put katalogu, który zawiera Twoje środowiska uruchomieniowego języka PHP w nim (na przykład `bin\php`).
1. Wdrażanie aplikacji sieci web.
1. Przejdź do aplikacji sieci web w witrynie Azure portal, a następnie kliknij pozycję **ustawienia** przycisku.

    ![Ustawienia aplikacji sieci Web][settings-button]
1. Z **ustawienia** bloku wybierz **ustawienia aplikacji** i przewiń do **mapowania obsługi** sekcji. Dodaj `*.php` z rozszerzeniem pola, a następnie dodaj ścieżkę do `php-cgi.exe` pliku wykonywalnego. Jeśli przeniesiesz swoje środowiska uruchomieniowego języka PHP `bin` katalogu w folderze głównym aplikacji, ścieżka jest `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Określ program obsługi mapowania obsługi][handler-mappings]
1. Kliknij przycisk **Zapisz** znajdujący się u góry **ustawienia aplikacji sieci Web** bloku.

    ![Zapisanie ustawień konfiguracji][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Porady: Włączanie automatyzacji narzędzia Composer na platformie Azure

Domyślnie usługa App Service nie wprowadza żadnych zmian przy użyciu pliku composer.json, jeśli nie masz w swoim projekcie w języku PHP. Jeśli używasz [wdrożenie Git](app-service-deploy-local-git.md), można włączyć composer.json przetwarzania podczas `git push` , należy włączyć rozszerzenie Composer (kompozytor).

> [!NOTE]
> Możesz [głos obsługi najwyższej jakości Composer (kompozytor) w usłudze App Service w tym miejscu](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. W swojej PHP sieci web bloku aplikacji w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **narzędzia** > **rozszerzenia**.

    ![Blok ustawienia portalu Azure, aby Włączanie automatyzacji narzędzia Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kliknij przycisk **Dodaj**, następnie kliknij przycisk **Composer**.

    ![Dodaj rozszerzenie Composer Włączanie automatyzacji narzędzia Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Kliknij przycisk **OK** aby zaakceptować warunki prawne. Kliknij przycisk **OK** ponownie, aby dodać rozszerzenie.

    **Zainstalowanych rozszerzeń** blok rozszerzenie Composer (kompozytor).
    ![Zaakceptuj postanowienia prawne na włączanie automatyzacji narzędzia Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Teraz, w oknie terminala na komputerze lokalnym, należy wykonać `git add`, `git commit`, i `git push` do aplikacji sieci Web. Należy zauważyć, że Composer (kompozytor) jest instalowany zdefiniowanych w pliku composer.json zależności.

    ![Wdrażanie usługi Git za pomocą automatyzacji narzędzia Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://azure.microsoft.com/try/app-service/) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
>

[bezpłatna wersja próbna]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista dyrektywy w pliku php.ini]: http://www.php.net/manual/en/ini.list.php
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
