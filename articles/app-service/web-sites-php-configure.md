---
title: Konfigurowanie środowiska uruchomieniowego języka PHP
description: Dowiedz się, jak skonfigurować domyślną instalację PHP lub dodać niestandardową instalację PHP dla usługi Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c73fb55e485d0c92d27eac2ac197a81337b9d5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016803"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurowanie php w usłudze Azure App Service

## <a name="introduction"></a>Wprowadzenie

W tym przewodniku pokazano, jak skonfigurować wbudowany czas pracy PHP dla aplikacji sieci web, tylnych linii mobilnych i aplikacji interfejsu API w [usłudze Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)udostępnić niestandardowe środowisko wykonawcze PHP i włączyć rozszerzenia. Aby korzystać z usługi App Service, zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej]. Aby w pełni wykorzystać to, co w tym przewodniku, należy najpierw utworzyć aplikację PHP w usłudze App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Jak: Zmiana wbudowanej wersji PHP

Domyślnie php 5.6 jest zainstalowany i natychmiast dostępny do użycia podczas tworzenia aplikacji usługi App Service. Najlepszym sposobem wyświetlenia dostępnej wersji, jej domyślnej konfiguracji i włączonych rozszerzeń jest wdrożenie skryptu, który wywołuje funkcję [phpinfo().]

Dostępne są również wersje PHP 7.0 i PHP 7.2, ale domyślnie nie są włączone. Aby zaktualizować wersję PHP, wykonaj jedną z następujących metod:

### <a name="azure-portal"></a>Portal Azure

1. Przejdź do aplikacji w [witrynie Azure portal](https://portal.azure.com) i przewiń do strony **konfiguracji.**

2. Z **opcji Konfiguracja**wybierz **pozycję Ustawienia ogólne** i wybierz nową wersję PHP.

3. Kliknij przycisk **Zapisz** u góry bloku **Ustawienia ogólne.**

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na komputerze.

1. Otwórz terminal i zaloguj się na swoje konto.

        az login

1. Sprawdź, czy lista obsługiwanych uruchomień.

        az webapp list-runtimes | grep php

2. Ustaw wersję PHP dla aplikacji.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. Wersja PHP jest teraz ustawiona. Możesz potwierdzić następujące ustawienia:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Jak: Zmiana wbudowanych konfiguracji PHP

W przypadku dowolnego wbudowanego środowiska wykonawczego PHP można zmienić dowolną z opcji konfiguracji, wykonując następujące kroki. (Aby uzyskać informacje na temat dyrektyw php.ini, zobacz [listę dyrektyw php.ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>\_Zmiana PHP\_INI USER, PHP\_INI\_PERDIR, PHP\_INI\_WSZYSTKIE ustawienia konfiguracyjne

1. Dodaj plik [.user.ini] do katalogu głównego.
1. Dodaj ustawienia konfiguracji `.user.ini` do pliku przy użyciu tej samej składni, która byłaby używana w `php.ini` pliku. Na przykład, jeśli chcesz włączyć `display_errors` ustawienie `upload_max_filesize` i ustawić ustawienie 10M, `.user.ini` plik będzie zawierał ten tekst:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Wdrażanie aplikacji.
3. Uruchom ponownie aplikację. (Ponowne uruchomienie jest konieczne, ponieważ częstotliwość, z jaką PHP odczytuje `.user.ini` pliki, jest regulowana przez `user_ini.cache_ttl` ustawienie, które jest ustawieniem poziomu systemu i wynosi domyślnie 300 sekund (5 minut). Ponowne uruchomienie aplikacji zmusza PHP do odczytu nowych ustawień w `.user.ini` pliku.)

Alternatywnie do używania `.user.ini` pliku można użyć funkcji [ini_set()] w skryptach, aby ustawić opcje konfiguracji, które nie są dyrektywami na poziomie systemowym.

### <a name="changing-php_ini_system-configuration-settings"></a>Zmiana ustawień\_konfiguracji\_systemu PHP INI

1. Dodawanie ustawienia aplikacji do aplikacji `PHP_INI_SCAN_DIR` za pomocą klucza i wartości`d:\home\site\ini`
1. Utwórz `settings.ini` plik przy użyciu konsoli Kudu Console (http:// nazwa&lt;&gt;witryny `d:\home\site\ini` .scm.azurewebsite.net) w katalogu.
1. Dodaj ustawienia konfiguracji `settings.ini` do pliku przy użyciu tej samej składni, która byłaby używana w `php.ini` pliku. Na przykład, jeśli chcesz `curl.cainfo` skierować `*.crt` ustawienie na plik i ustawić ustawienie "wincache.maxfilesize" `settings.ini` na 512K, plik będzie zawierał ten tekst:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Aby ponownie załadować zmiany, uruchom ponownie aplikację.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Jak: Włączanie rozszerzeń w domyślnym czasie pracy PHP

Jak wspomniano w poprzedniej sekcji, najlepszym sposobem, aby zobaczyć domyślną wersję PHP, jej domyślną konfigurację i włączone rozszerzenia jest wdrożenie skryptu, który wywołuje [phpinfo()]. Aby włączyć dodatkowe rozszerzenia, wykonaj następujące kroki:

### <a name="configure-via-ini-settings"></a>Konfigurowanie za pomocą ustawień ini

1. Dodaj `ext` katalog do `d:\home\site` katalogu.
1. Umieść `.dll` pliki rozszerzeń w `ext` `php_xdebug.dll`katalogu (na przykład ). Upewnij się, że rozszerzenia są zgodne z domyślną wersją PHP i są kompatybilne z VC9 i nienagłówkowym (nts).
1. Dodawanie ustawienia aplikacji do aplikacji `PHP_INI_SCAN_DIR` za pomocą klucza i wartości`d:\home\site\ini`
1. Tworzenie `ini` pliku `d:\home\site\ini` o `extensions.ini`nazwie .
1. Dodaj ustawienia konfiguracji `extensions.ini` do pliku przy użyciu tej samej składni, która byłaby używana w `php.ini` pliku. Na przykład, jeśli chcesz włączyć rozszerzenia MongoDB i XDebug, `extensions.ini` plik będzie zawierać ten tekst:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Uruchom ponownie aplikację, aby załadować zmiany.

### <a name="configure-via-app-setting"></a>Konfigurowanie za pomocą ustawienia aplikacji

1. Dodawanie `bin` katalogu do katalogu głównego.
2. Umieść `.dll` pliki rozszerzeń w `bin` `php_xdebug.dll`katalogu (na przykład ). Upewnij się, że rozszerzenia są zgodne z domyślną wersją PHP i są kompatybilne z VC9 i nienagłówkowym (nts).
3. Wdrażanie aplikacji.
4. Przejdź do aplikacji w witrynie Azure portal i kliknij **sekcję Konfiguracja znajdująca** się poniżej **sekcji Ustawienia.**
5. W bloku **Konfiguracja** wybierz pozycję **Ustawienia aplikacji**.
6. W sekcji **Ustawienia aplikacji** kliknij + Nowe **ustawienie aplikacji** i utwórz **PHP_EXTENSIONS** klucz. Wartość dla tego klucza będzie ścieżką względem katalogu głównego witryny: **bin\your-ext-file**.
7. Kliknij przycisk **Aktualizuj** u dołu, a następnie kliknij pozycję **Zapisz** nad kartą **Ustawienia aplikacji.**

Rozszerzenia Zend są również obsługiwane przy użyciu klucza **PHP_ZENDEXTENSIONS.** Aby włączyć wiele rozszerzeń, dołącz listę `.dll` plików oddzielonych przecinkami dla wartości ustawienia aplikacji.

## <a name="how-to-use-a-custom-php-runtime"></a>Jak: Korzystanie z niestandardowego środowiska wykonawczego PHP

Zamiast domyślnego środowiska wykonawczego PHP, Usługa App Service może użyć środowiska uruchomieniowego PHP, które udostępniasz do wykonywania skryptów PHP. Środowisko wykonawcze, które można podać `php.ini` można skonfigurować przez plik, który również można podać. Aby użyć niestandardowego środowiska wykonawczego PHP z usługą App Service, wykonaj następujące kroki.

1. Uzyskaj wersję PHP dla systemu Windows zgodną z innymi wątkami, VC9 lub VC11. Najnowsze wersje PHP dla Windows można [https://windows.php.net/download/]znaleźć tutaj: . Starsze wydania można znaleźć w [https://windows.php.net/downloads/releases/archives/]archiwum tutaj: .
2. Zmodyfikuj `php.ini` plik dla środowiska wykonawczego. Wszystkie ustawienia konfiguracji, które są tylko na poziomie systemu dyrektyw są ignorowane przez usługę App Service. (Aby uzyskać informacje na temat dyrektyw dotyczących tylko do systemu, zobacz [listę dyrektyw php.ini]).
3. Opcjonalnie dodaj rozszerzenia do środowiska wykonawczego PHP i `php.ini` włącz je w pliku.
4. Dodaj `bin` katalog do katalogu głównego i umieść w nim katalog zawierający środowisko wykonawcze `bin\php`PHP (na przykład ).
5. Wdrażanie aplikacji.
6. Przejdź do aplikacji w witrynie Azure portal i kliknij **blok Konfiguracji.**
8. W bloku **Konfiguracja** wybierz polecenie **Mapowania ścieżek**. 
9. Kliknij **+ Nowy program obsługi** i dodaj `*.php` do `php-cgi.exe` pola Rozszerzenie i dodaj ścieżkę do pliku wykonywalnego w **procesorze skryptów**. Jeśli umieścisz środowisko uruchomieniowe `bin` PHP w katalogu w katalogu `D:\home\site\wwwroot\bin\php\php-cgi.exe`głównym aplikacji, ścieżka jest .
10. U dołu kliknij przycisk **Aktualizuj,** aby zakończyć dodawanie mapowania programu obsługi.
11. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Jak: Włączanie automatyzacji kompozytora na platformie Azure

Domyślnie Usługa App Service nie robi nic z composer.json, jeśli masz go w projekcie PHP. Jeśli używasz [wdrożenia Git,](deploy-local-git.md)można włączyć composer.json przetwarzania podczas `git push` włączając rozszerzenie Kompozytor.

> [!NOTE]
> Możesz [głosować na pierwszorzędną obsługę kompozytora w usłudze App Service tutaj!](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)
>

1. W bloku aplikacji PHP w [witrynie Azure portal](https://portal.azure.com)kliknij pozycję**Rozszerzenia** **narzędzi** > .

    ![Blok ustawień portalu Azure, aby umożliwić automatyzację aplikacji Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kliknij **pozycję Dodaj**, a następnie kliknij pozycję **Kompozytor**.

    ![Dodawanie rozszerzenia Kompozytora w celu umożliwienia automatyzacji programu Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Kliknij **przycisk OK,** aby zaakceptować warunki prawne. Kliknij **przycisk OK** ponownie, aby dodać rozszerzenie.

    W bloku **Zainstalowane rozszerzenia** jest wyświetlane rozszerzenie Kompozytor.
    ![Akceptowanie warunków prawnych w celu umożliwienia automatyzacji kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Teraz, w oknie terminala na `git add`komputerze `git commit`lokalnym, wykonać , i `git push` do aplikacji. Należy zauważyć, że kompozytor instaluje zależności zdefiniowane w composer.json.

    ![Wdrażanie git z automatyzacją kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Centrum programistów PHP](https://azure.microsoft.com/develop/php/).

[bezpłatna wersja próbna]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista dyrektyw php.ini]: https://www.php.net/manual/en/ini.list.php
[plik .user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
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
