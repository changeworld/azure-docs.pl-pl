---
title: Konfigurowanie środowiska uruchomieniowego PHP
description: Dowiedz się, jak skonfigurować domyślną instalację PHP lub dodać niestandardową instalację PHP dla Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c73fb55e485d0c92d27eac2ac197a81337b9d5e1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016803"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurowanie języka PHP w Azure App Service

## <a name="introduction"></a>Wprowadzenie

W tym przewodniku pokazano, jak skonfigurować wbudowane środowisko uruchomieniowe PHP dla aplikacji sieci Web, zaplecza mobilnego i API Apps w [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), udostępnić środowisko uruchomieniowe języka PHP i włączyć rozszerzenia. Aby skorzystać z App Service, zarejestruj się w celu uzyskania [bezpłatnej wersji próbnej]. Aby najlepiej wykorzystać ten przewodnik, należy najpierw utworzyć aplikację PHP w App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Instrukcje: Zmienianie wbudowanej wersji języka PHP

Domyślnie aplikacja PHP 5,6 jest zainstalowana i natychmiast dostępna do użycia podczas tworzenia aplikacji App Service. Najlepszym sposobem wyświetlenia dostępnej poprawki wydania, jej konfiguracji domyślnej i włączonych rozszerzeń jest wdrożenie skryptu wywołującego funkcję [phpinfo ()] .

Wersje PHP 7,0 i PHP 7,2 są również dostępne, ale nie są domyślnie włączone. Aby zaktualizować wersję języka PHP, należy wykonać jedną z następujących metod:

### <a name="azure-portal"></a>Portal Azure

1. Przejdź do aplikacji w [Azure Portal](https://portal.azure.com) i przewiń do strony **Konfiguracja** .

2. W obszarze **Konfiguracja**wybierz pozycję **Ustawienia ogólne** i wybierz nową wersję php.

3. Kliknij przycisk **Zapisz** znajdujący się u góry bloku **Ustawienia ogólne** .

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy zainstalować na komputerze interfejs użytkownika [platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

1. Otwórz Terminal i zaloguj się na swoim koncie.

        az login

1. Zaznacz, aby wyświetlić listę obsługiwanych środowisk uruchomieniowych.

        az webapp list-runtimes | grep php

2. Ustaw wersję języka PHP dla aplikacji.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. Wersja języka PHP jest teraz ustawiona. Można potwierdzić następujące ustawienia:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Instrukcje: Zmienianie wbudowanych konfiguracji PHP

W przypadku każdego wbudowanego środowiska uruchomieniowego PHP można zmienić dowolną z opcji konfiguracji, wykonując następujące kroki. (Aby uzyskać informacje na temat dyrektyw php. ini, zobacz [Lista dyrektyw języka PHP. ini]).

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Zmiana\_pliku PHP\_USER, PHP\_INI\_PERDIR, PHP\_INI\_wszystkie ustawienia konfiguracji

1. Dodaj plik [. User. ini] do katalogu głównego.
1. Dodaj ustawienia konfiguracji do pliku `.user.ini` przy użyciu tej samej składni, która ma być używana w pliku `php.ini`. Na przykład jeśli chcesz włączyć ustawienie `display_errors` i ustawić ustawienie `upload_max_filesize` na 10 mln, plik `.user.ini` będzie zawierać następujący tekst:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Wdróż aplikację.
3. Uruchom ponownie aplikację. (Ponowne uruchomienie jest niezbędne, ponieważ częstotliwość odczytywania plików `.user.ini` przez PHP odbywa się według ustawienia `user_ini.cache_ttl`, który jest ustawieniem na poziomie systemu i jest 300 sekund (5 minut). Ponowne uruchomienie aplikacji wymusza, aby PHP odczytywać nowe ustawienia w pliku `.user.ini`.)

Alternatywą dla korzystania z pliku `.user.ini` może być użycie funkcji [ini_set ()] w skryptach w celu ustawienia opcji konfiguracji, które nie są dyrektywami na poziomie systemu.

### <a name="changing-php_ini_system-configuration-settings"></a>Zmienianie ustawień konfiguracji systemu PHP\_INI\_

1. Dodaj do aplikacji ustawienie aplikacji z kluczem `PHP_INI_SCAN_DIR` i wartością `d:\home\site\ini`
1. Utwórz plik `settings.ini` za pomocą konsoli programu kudu (http://&lt;Site-Name&gt;. scm.azurewebsite.net) w katalogu `d:\home\site\ini`.
1. Dodaj ustawienia konfiguracji do pliku `settings.ini` przy użyciu tej samej składni, która ma być używana w pliku `php.ini`. Jeśli na przykład chcesz wskazać plik `*.crt` `curl.cainfo` i ustawić ustawienie "WinCache. MaxFileSize" na 512K, plik `settings.ini` będzie zawierać następujący tekst:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Aby ponownie załadować zmiany, uruchom ponownie aplikację.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Instrukcje: Włączanie rozszerzeń w domyślnym środowisku uruchomieniowym PHP

Jak wskazano w poprzedniej sekcji, najlepszym sposobem wyświetlenia domyślnej wersji języka PHP, jej konfiguracji domyślnej i włączonych rozszerzeń jest wdrożenie skryptu wywołującego [phpinfo ()]. Aby włączyć dodatkowe rozszerzenia, wykonaj następujące czynności:

### <a name="configure-via-ini-settings"></a>Konfigurowanie za pomocą ustawień pliku ini

1. Dodaj katalog `ext` do katalogu `d:\home\site`.
1. Umieść pliki rozszerzeń `.dll` w katalogu `ext` (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją języka PHP i są zgodne z VC9 i niebezpiecznym wątkem (nkty przerwania).
1. Dodaj do aplikacji ustawienie aplikacji z kluczem `PHP_INI_SCAN_DIR` i wartością `d:\home\site\ini`
1. Utwórz plik `ini` w `d:\home\site\ini` o nazwie `extensions.ini`.
1. Dodaj ustawienia konfiguracji do pliku `extensions.ini` przy użyciu tej samej składni, która ma być używana w pliku `php.ini`. Na przykład jeśli chcesz włączyć rozszerzenia MongoDB i narzędzia Xdebug, plik `extensions.ini` będzie zawierać następujący tekst:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Uruchom ponownie aplikację, aby załadować zmiany.

### <a name="configure-via-app-setting"></a>Konfiguruj przy użyciu ustawienia aplikacji

1. Dodaj katalog `bin` do katalogu głównego.
2. Umieść pliki rozszerzeń `.dll` w katalogu `bin` (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją języka PHP i są zgodne z VC9 i niebezpiecznym wątkem (nkty przerwania).
3. Wdróż aplikację.
4. Przejdź do aplikacji w Azure Portal i kliknij **konfigurację** znajdującą się poniżej sekcji **Ustawienia** .
5. W bloku **Konfiguracja** wybierz pozycję **Ustawienia aplikacji**.
6. W sekcji **Ustawienia aplikacji** kliknij pozycję **+ nowe ustawienie aplikacji** i Utwórz klucz **PHP_EXTENSIONS** . Wartość tego klucza powinna być ścieżką względem katalogu głównego witryny sieci Web: **bin\your-EXT-File**.
7. Kliknij przycisk **Aktualizuj** u dołu ekranu, a następnie kliknij przycisk **Zapisz** powyżej karty **Ustawienia aplikacji** .

Rozszerzenia Zend są również obsługiwane przy użyciu klucza **PHP_ZENDEXTENSIONS** . Aby włączyć wiele rozszerzeń, należy dołączyć listę plików `.dll` rozdzieloną przecinkami dla wartości ustawienia aplikacji.

## <a name="how-to-use-a-custom-php-runtime"></a>Instrukcje: korzystanie z niestandardowego środowiska uruchomieniowego PHP

Zamiast domyślnego środowiska uruchomieniowego PHP, App Service może używać środowiska uruchomieniowego PHP, które jest zapewniane do wykonywania skryptów PHP. Środowisko uruchomieniowe, które można podać, można skonfigurować za pomocą pliku `php.ini`, który również udostępniasz. Aby użyć niestandardowego środowiska uruchomieniowego PHP z App Service, wykonaj następujące czynności.

1. Uzyskaj zgodną z VC9 lub VC11 wersję języka PHP dla systemu Windows. Najnowsze wersje środowiska PHP dla systemu Windows można znaleźć tutaj: [https://windows.php.net/download/]. Starsze wersje można znaleźć w archiwum tutaj: [https://windows.php.net/downloads/releases/archives/].
2. Zmodyfikuj plik `php.ini` dla środowiska uruchomieniowego. Wszystkie ustawienia konfiguracji, które są dyrektywami tylko na poziomie systemu, są ignorowane przez App Service. (Aby uzyskać informacje na temat dyrektyw opartych na poziomie systemu, zobacz [Lista dyrektyw języka PHP. ini]).
3. Opcjonalnie dodaj rozszerzenia do środowiska uruchomieniowego PHP i włącz je w pliku `php.ini`.
4. Dodaj katalog `bin` do katalogu głównego i umieść w nim katalog zawierający środowisko uruchomieniowe PHP (na przykład `bin\php`).
5. Wdróż aplikację.
6. Przejdź do aplikacji w Azure Portal i kliknij blok **Konfiguracja** .
8. W bloku **Konfiguracja** wybierz pozycję **mapowania ścieżek**. 
9. Kliknij pozycję **+ Nowy program obsługi** i Dodaj `*.php` do pola rozszerzenia i Dodaj ścieżkę do `php-cgi.exe` pliku wykonywalnego w **procesorze skryptów**. Jeśli umieścisz środowisko uruchomieniowe PHP w katalogu `bin` w katalogu głównym aplikacji, ścieżka jest `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Kliknij przycisk **Aktualizuj** w dolnej części, aby zakończyć dodawanie mapowania programu obsługi.
11. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Instrukcje: Włączanie automatyzacji kompozytora na platformie Azure

Domyślnie App Service nie wykonuje żadnych czynności z użyciem pliku Composer. JSON, jeśli istnieje on w projekcie PHP. Jeśli używasz [wdrożenia git](deploy-local-git.md), możesz włączyć przetwarzanie pliku Composer. json podczas `git push`, włączając rozszerzenie układacz.

> [!NOTE]
> Możesz [głosować na potrzeby obsługi kompozytorów pierwszej klasy w App Service tym miejscu](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. W bloku aplikacji PHP w [Azure Portal](https://portal.azure.com)kliknij pozycję **Narzędzia** > **rozszerzenia**.

    ![Blok ustawień Azure Portal, aby włączyć automatyzację kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kliknij przycisk **Dodaj**, a następnie kliknij pozycję **układacz**.

    ![Dodaj rozszerzenie Composer, aby włączyć automatyzację kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Kliknij przycisk **OK** , aby zaakceptować postanowienia prawne. Kliknij ponownie przycisk **OK** , aby dodać rozszerzenie.

    Blok **zainstalowane rozszerzenia** pokazuje rozszerzenie układacz.
    ![zaakceptuj postanowienia prawne, aby włączyć automatyzację kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Teraz w oknie terminalu na komputerze lokalnym wykonaj `git add`, `git commit`i `git push` do aplikacji. Zwróć uwagę, że układacz instaluje zależności zdefiniowane w pliku Composer. JSON.

    ![Wdrożenie narzędzia Git z automatyzacją Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[bezpłatnej wersji próbnej]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista dyrektyw języka PHP. ini]: https://www.php.net/manual/en/ini.list.php
[. User. ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set ()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
