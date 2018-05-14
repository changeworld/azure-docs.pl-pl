---
title: Technologie open source często zadawane pytania dotyczące usługi Azure web apps | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące open source technologii w funkcji aplikacji sieci Web w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 747ee61d2620e7f79353207c0e44bcea36df30ee
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Technologie open source często zadawane pytania dotyczące aplikacji sieci Web na platformie Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczące problemów z technologii open source [funkcja Web Apps usługi Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-cleardb-database-is-down-how-do-i-resolve-this"></a>Baza danych ClearDB jest wyłączony. Jak rozwiązać ten problem?

W przypadku problemów związanych z bazy danych, skontaktuj się z [Obsługa ClearDB](https://www.cleardb.com/developers/help/support). 

Aby uzyskać odpowiedzi na często zadawane pytania dotyczące ClearDB, zobacz [ClearDB — często zadawane pytania](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="why-wasnt-my-cleardb-database-migrated-during-my-subscription-migration"></a>Dlaczego nie migracji bazy danych ClearDB podczas migracji mojej subskrypcji?

Podczas przeprowadzania migracji zasobów w subskrypcjach, mają zastosowanie następujące ograniczenia. Baza danych ClearDB MySQL jest usługi innej firmy i nie są migrowane podczas migracji subskrypcji platformy Azure.

Jeśli nie zarządzasz migracji bazy danych MySQL, przed przeprowadzeniem migracji zasobów platformy Azure, bazy danych ClearDB MySQL mogą być niedostępne. Aby tego uniknąć, najpierw ręcznie należy zmigrować bazę danych ClearDB, a następnie ponów próbę migracji subskrypcji platformy Azure dla aplikacji sieci web.

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące bazy danych ClearDB MySQL z usługi aplikacji Azure](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Jak włączyć rejestrowanie w celu rozwiązywania problemów PHP PHP

Aby włączyć rejestrowanie PHP:

1. Zaloguj się do Twojego [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz z górnego menu **konsoli debugowania** > **CMD**.
3. Wybierz **lokacji** folderu.
4. Wybierz **wwwroot** folderu.
5. Wybierz **+** ikonę, a następnie wybierz **nowy plik**.
6. Ustaw nazwę pliku **. user.ini**.
7. Wybierz ikonę ołówka obok **. user.ini**.
8. W pliku Dodaj ten kod: `log_errors=on`
9. Wybierz pozycję **Zapisz**.
10. Wybierz ikonę ołówka obok **wp config.php**.
11. Zmień go na następujący kod:
   ```
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Supress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. W portalu Azure, w menu aplikacji sieci web należy ponownie uruchomić aplikacji sieci web.

Aby uzyskać więcej informacji, zobacz [dzienniki błędów włączyć WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Jak rejestrować błędów aplikacji Python w aplikacjach, które znajdują się w usłudze App Service?

Aby przechwycić błędów aplikacji Python:

1. W portalu Azure w aplikacji sieci web wybierz **ustawienia**.
2. Na **ustawienia** wybierz opcję **ustawienia aplikacji**.
3. W obszarze **ustawień aplikacji**, wprowadź następujące parę klucza i wartości:
    * Klucz: WSGI_LOG
    * Wartość: D:\home\site\wwwroot\logs.txt (wprowadź wybrana nazwa pliku)

Błędy w pliku logs.txt w folderze wwwroot powinna zostać wyświetlona.

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Jak zmienić wersję aplikacji Node.js, która jest obsługiwana w usłudze App Service?

Aby zmienić wersję aplikacji Node.js, użyj jednej z następujących opcji:

*   W portalu Azure, użyj **ustawień aplikacji**.
    1. W portalu Azure przejdź do aplikacji sieci web.
    2. Na **ustawienia** bloku, wybierz opcję **ustawienia aplikacji**.
    3. W **ustawień aplikacji**, można dołączać WEBSITE_NODE_DEFAULT_VERSION jako klucz i wersji środowiska Node.js ma jako wartość.
    4. Przejdź do Twojej [konsoli Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    5. Aby sprawdzić wersję środowiska Node.js, wprowadź następujące polecenie:  
   ```
   node -v
   ```
*   Zmodyfikuj plik iisnode.yml udostępniany. Zmiana wersji środowiska Node.js w plik iisnode.yml udostępniany tylko ustawia środowisko uruchomieniowe tego programu iisnode używa. Twoje cmd Kudu i inne nadal używać wersji Node.js, który jest ustawiony w **ustawień aplikacji** w portalu Azure.

    Aby ręcznie ustawić plik iisnode.yml, należy utworzyć plik iisnode.yml udostępniany w folderze głównym aplikacji. W pliku Dołącz następujący wiersz:
   ```
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Ustaw plik iisnode.yml udostępniany przy użyciu pliku package.json podczas wdrażania kontroli źródła.
    Proces wdrażania kontroli źródła Azure obejmuje następujące kroki:
    1. Przenosi zawartości do aplikacji sieci web platformy Azure.
    2. Tworzy domyślny skrypt wdrożenia, jeśli nie ma takiego (pliku deploy.cmd, pliki .deployment) w katalogu głównym aplikacji sieci web.
    3. Uruchamia skrypt wdrożenia, w którym tworzy plik iisnode.yml udostępniany Jeśli wspomina wersji środowiska Node.js w pliku package.json > aparatu `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. Plik iisnode.yml udostępniany ma następujący wiersz kodu:
        ```
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Pojawił się komunikat "Błąd podczas nawiązywania połączenia z bazą danych" w mojej aplikacji WordPress, który znajduje się w usłudze App Service. Jak rozwiązać ten problem?

Jeśli zostanie wyświetlony ten błąd w aplikacji Azure WordPress, aby włączyć php_errors.log i debug.log, pełną kroki szczegółowo w [dzienniki błędów włączyć WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Jeśli dzienniki są włączone, występuje błąd, a następnie sprawdź dzienniki, aby sprawdzić, czy są uruchomione poza połączeń:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Jeśli zostanie wyświetlony ten błąd w plikach debug.log lub php_errors.log aplikacji przekracza liczbę połączeń. Jeśli przechowujesz na ClearDB Sprawdź liczbę połączeń, które są dostępne w Twojej [plan usługi](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Jak debugować aplikację Node.js, która jest hostowana w usłudze App Service?

1.  Przejdź do Twojej [konsoli Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Przejdź do folderu dzienników aplikacji (D:\home\LogFiles\Application).
3.  W pliku logging_errors.txt Sprawdź, czy zawartość.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Jak zainstalować natywnych modułów środowiska Python w aplikacji sieci web usługi aplikacji lub aplikacji interfejsu API?

Niektóre pakiety mogą nie zainstalować przy użyciu narzędzia pip na platformie Azure. Pakiet może nie być dostępne na indeksu pakietów języka Python lub kompilatora mogą być wymagane (kompilator nie jest dostępna na komputerze, na którym działa aplikacja sieci web w usłudze App Service). Informacje dotyczące instalowania modułów macierzystych z usługi aplikacji — aplikacje sieci web i aplikacji API apps, zobacz [zainstalować modułów środowiska Python w usłudze App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Sposób wdrażania aplikacji Django do usługi App Service przy użyciu usługi Git i nowej wersji języka Python?

Aby uzyskać informacje o instalowaniu Django, zobacz [wdrażanie aplikacji Django do usługi App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Gdzie znajdują się pliki dziennika Tomcat

Dla portalu Azure Marketplace i wdrożeń niestandardowych:

* Lokalizacja folderu: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Pliki odsetek:
    * catalina. *rrrr mm-dd*.log
    * Menedżer hosta. *rrrr mm-dd*.log
    * localhost. *rrrr mm-dd*.log
    * Menedżer. *rrrr mm-dd*.log
    * site_access_log. *rrrr mm-dd*.log


Dla portalu **ustawień aplikacji** wdrożenia:

* Lokalizacja folderu: D:\home\LogFiles
* Pliki odsetek:
    * catalina. *rrrr mm-dd*.log
    * Menedżer hosta. *rrrr mm-dd*.log
    * localhost. *rrrr mm-dd*.log
    * Menedżer. *rrrr mm-dd*.log
    * site_access_log. *rrrr mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Jak rozwiązywać problemy z błędami połączeń sterownik JDBC

W dziennikach Tomcat, może być wyświetlony następujący komunikat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Aby rozwiązać ten błąd:

1. Usuń plik sqljdbc*.jar z folderu aplikacji/lib.
2. Jeśli używasz niestandardowego serwera sieci web Tomcat lub Azure Marketplace Tomcat, skopiuj ten plik JAR w folderze lib Tomcat.
3. Jeśli włączasz Java z portalu Azure (wybierz **Java 1.8** > **serwera Tomcat**), skopiuj plik jar sqljdbc.* w folderze, który jest zbliżony do aplikacji. Następnie dodaj następujące ustawienie ścieżkę do pliku web.config:

    ```
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Dlaczego Zobacz błędy, gdy próbuję skopiować pliki dziennika na żywo?

Jeśli spróbujesz skopiować na żywo plików dziennika dla aplikacji Java (na przykład Tomcat), można napotkać tego błędu FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Komunikat o błędzie może się różnić w zależności od klienta FTP.

Wszystkie aplikacje Java mają to blokującego problem. Tylko Kudu obsługuje pobieranie tego pliku, gdy aplikacja jest uruchomiona.

Zatrzymywanie aplikacji umożliwia dostęp do tych plików usługi FTP.

Inne obejście jest można zapisać zadania WebJob, która działa zgodnie z harmonogramem i kopiuje pliki do innego katalogu. Aby uzyskać przykładowy projekt, zobacz [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projektu.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Gdzie znaleźć plik dziennika dla Jetty?

Marketplace i wdrożeń niestandardowych plik dziennika znajduje się w folderze D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Należy pamiętać, że lokalizacja folderu zależy od wersji Jetty używasz. Na przykład ścieżka podane tu dla Jetty 9.1.2. Wyszukaj jetty_*YYYY_MM_DD*. stderrout.log.

W przypadku wdrożeń ustawienie aplikacji portalu plik dziennika jest D:\home\LogFiles. Wyszukaj jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Z mojej aplikacji sieci web platformy Azure można wysłać wiadomości e-mail?

Usługa aplikacji nie ma funkcji wbudowanych poczty e-mail. Dla niektórych dobrej alternatyw do wysyłania wiadomości e-mail z aplikacji, zobacz ten [przepełnienie stosu dyskusji](http://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Dlaczego Moja witryna WordPress przekierowania do innego adresu URL?

Jeśli został ostatnio zmigrowany do platformy Azure, WordPress może przekierować do starego adresu URL domeny. Jest to spowodowane ustawienia w bazie danych MySQL.

WordPress Buddy + jest rozszerzeniem lokacji Azure, które można zaktualizować adres URL przekierowania bezpośrednio w bazie danych. Aby uzyskać więcej informacji o używaniu WordPress Buddy +, zobacz [WordPress narzędzia i MySQL migracji Buddy + z WordPress](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Alternatywnie, jeśli wolisz ręcznie zaktualizować Przekierowywanie adresu URL za pomocą zapytania SQL lub PHPMyAdmin, zobacz [WordPress: Przekierowanie do adresu URL niewłaściwy](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Jak zmienić hasło logowania WordPress?

Jeśli pamiętasz hasła logowania WordPress służy WordPress Buddy + go zaktualizować. Aby zresetować hasło, zainstaluj WordPress Buddy + Azure lokacji rozszerzenie, a następnie wykonaj kroki opisane w [WordPress narzędzia i MySQL migracji Buddy + z WordPress](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nie można zarejestrować celu WordPress. Jak rozwiązać ten problem?

Jeśli okaże się zablokowane WordPress po zainstalowaniu ostatnio wtyczkę, może być uszkodzony wtyczki. WordPress Buddy + jest rozszerzeniem lokacji Azure, które mogą pomóc Wyłącz wtyczki w WordPress. Aby uzyskać więcej informacji, zobacz [WordPress narzędzia i MySQL migracji Buddy + z WordPress](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Jak przeprowadzić migrację bazy danych WordPress?

Istnieje wiele opcji dotyczących migracji bazy danych MySQL, który jest połączony z witryny sieci Web WordPress:

* Deweloperzy: Za pomocą [wiersza polecenia lub PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Innych niż deweloperów: Użyj [WordPress Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Sposób pomóc zabezpieczyć WordPress

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń dla środowiska WordPress, zobacz [najlepszych rozwiązaniach dotyczących zabezpieczeń WordPress na platformie Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Próbuję użyć PHPMyAdmin i pojawił się komunikat "Odmowa dostępu." Jak rozwiązać ten problem?

Ten problem może wystąpić, jeśli funkcja w aplikacji MySQL nie jest jeszcze uruchomiona w tym wystąpieniu usługi aplikacji. Aby rozwiązać ten problem, spróbuj uzyskać dostępu do witryny sieci Web. Spowoduje to uruchomienie wymagane procesów, w tym proces MySQL w aplikacji. Aby sprawdzić, czy MySQL w aplikacji jest uruchomiona, w Eksploratorze procesu, upewnij się, że ten mysqld.exe ma na liście procesów.

Po upewnieniu się, że jest uruchomiona w tym MySQL w aplikacji, spróbuj użyć PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Błąd HTTP 403 podczas importowania lub eksportowania bazy danych MySQL w aplikacji przy użyciu PHPMyadmin. Jak rozwiązać ten problem?

Jeśli używasz starszej wersji przeglądarki Chrome, mogą występować znaną usterką. Aby rozwiązać ten problem, Uaktualnij do nowszej wersji programu Chrome. Spróbuj również przy użyciu innej przeglądarki, takich jak program Internet Explorer lub Microsoft Edge, gdzie występuje problem.
