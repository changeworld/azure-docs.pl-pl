---
title: Technologie open source — często zadawane pytania — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące technologii open source w funkcji Web Apps w usłudze Azure App Service.
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
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 7831e5e989835b2c9432dbd61a242584a7b6244d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270217"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Technologie open source często zadawane pytania dotyczące aplikacji sieci Web na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania (FAQ) dotyczących problemów z technologii open source dla [funkcji Web Apps w usłudze Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Jak włączyć PHP rejestrowania rozwiązywać problemy z języka PHP

Aby włączyć rejestrowanie PHP:

1. Zaloguj się do Twojej [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. W górnym menu wybierz **konsoli debugowania** > **CMD**.
3. Wybierz **witryny** folderu.
4. Wybierz **wwwroot** folderu.
5. Wybierz **+** ikonę, a następnie wybierz **nowy plik**.
6. Ustaw nazwę pliku na **. user.ini**.
7. Wybierz ikonę ołówka obok **. user.ini**.
8. W pliku Dodaj następujący kod: `log_errors=on`
9. Wybierz pozycję **Zapisz**.
10. Wybierz ikonę ołówka obok **wp config.php**.
11. Zmień tekst z następującym kodem:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. W witrynie Azure portal w menu aplikacji sieci web Uruchom ponownie swoją aplikację sieci web.

Aby uzyskać więcej informacji, zobacz [dzienniki błędów WordPress Włącz](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Jak rejestrować błędów aplikacji w języku Python w aplikacjach, które są hostowane w usłudze App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Jak zmienić wersję aplikacji Node.js, która jest hostowana w usłudze App Service?

Aby zmienić wersję aplikacji Node.js, można użyć jednej z następujących opcji:

* W witrynie Azure portal, należy użyć **ustawienia aplikacji**.
  1. W witrynie Azure portal przejdź do aplikacji sieci web.
  2. Na **ustawienia** bloku wybierz **ustawienia aplikacji**.
  3. W **ustawienia aplikacji**, można dołączyć WEBSITE_NODE_DEFAULT_VERSION jako klucza i wersję środowiska Node.js, ma jako wartość.
  4. Przejdź do swojej [konsoli Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
  5. Aby sprawdzić wersję środowiska Node.js, wprowadź następujące polecenie:  
     ```
     node -v
     ```
* Zmodyfikuj plik iisnode.yml udostępniany. Zmiana wersji środowiska Node.js w plik iisnode.yml udostępniany tylko zestawy środowiska wykonawczego tego programu iisnode używa. Usługi Kudu cmd, a inne nadal używać wersji środowiska Node.js, który jest ustawiony w **ustawienia aplikacji** w witrynie Azure portal.

  Aby ręcznie ustawić plik iisnode.yml, należy utworzyć plik iisnode.yml w folderze głównym aplikacji. W pliku należy umieścić następujący wiersz:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Ustaw plik iisnode.yml udostępniany przy użyciu pliku package.json podczas wdrażania kontroli źródła.
  Proces wdrażania kontroli źródła platformy Azure obejmuje następujące czynności:
  1. Powoduje przeniesienie zawartości do aplikacji sieci web platformy Azure.
  2. Tworzy domyślny skrypt wdrożenia, jeśli nie istnieje (pliku deploy.cmd, pliki .deployment) w folderze głównym aplikacji sieci web.
  3. Uruchamia skrypt wdrażania, w którym tworzy plik iisnode.yml Jeśli wspomnisz o wersji środowiska Node.js w pliku package.json > aparat `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Plik iisnode.yml udostępniany ma następujący wiersz kodu:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Widzę komunikat "Błąd podczas nawiązywania połączenia z bazą danych" w swojej aplikacji WordPress, która jest hostowana w usłudze App Service. Jak rozwiązać ten problem?

Jeśli zostanie wyświetlony ten błąd w swojej aplikacji WordPress na platformie Azure, umożliwiające php_errors.log i czy, pełną kroki szczegółowo opisane w [dzienniki błędów WordPress Włącz](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Jeśli dzienniki są włączone, Odtwórz błąd, a następnie sprawdź dzienniki, aby zobaczyć, jeśli kończy się połączenia:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Jeśli zostanie wyświetlony ten błąd w plikach czy lub php_errors.log aplikacji przekracza liczbę połączeń. Jeśli przechowujesz na ClearDB, sprawdź liczbę połączeń, które są dostępne w Twojej [planu usług](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Jak debugować aplikację Node.js, która jest hostowana w usłudze App Service?

1.  Przejdź do swojej [konsoli Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Przejdź do folderu dzienników aplikacji (D:\home\LogFiles\Application).
3.  W pliku logging_errors.txt Sprawdź, czy zawartość.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Jak zainstalować natywnych modułów języka Python w aplikacji sieci web usługi App Service lub aplikacji interfejsu API?

Niektóre pakiety nie może zainstalować przy użyciu narzędzia pip na platformie Azure. Pakiet może nie być dostępne na indeksu pakietów języka Python i kompilatora mogą być wymagane (kompilator nie jest dostępne na komputerze, na którym działa aplikacja sieci web w usłudze App Service). Aby uzyskać informacji dotyczących instalowania modułów natywnych w aplikacji usługi App Service web apps i API apps, zobacz [zainstalować moduły języka Python w usłudze App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Jak wdrożyć aplikację Django w usłudze App Service przy użyciu narzędzia Git i nową wersję języka Python?

Aby uzyskać informacje o instalowaniu Django, zobacz [wdrażanie aplikacji Django w usłudze App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Gdzie znajdują się pliki dziennika Tomcat?

Portal Azure Marketplace i wdrożeń niestandardowych:

* Lokalizacja folderu: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Pliki zainteresowania:
    * catalina. *rrrr mm-dd*.log
    * Menedżer hosta. *rrrr mm-dd*.log
    * localhost. *rrrr mm-dd*.log
    * Menedżer. *rrrr mm-dd*.log
    * site_access_log. *rrrr mm-dd*.log


Dla portalu **ustawienia aplikacji** wdrożenia:

* Lokalizacja folderu: D:\home\LogFiles
* Pliki zainteresowania:
    * catalina. *rrrr mm-dd*.log
    * Menedżer hosta. *rrrr mm-dd*.log
    * localhost. *rrrr mm-dd*.log
    * Menedżer. *rrrr mm-dd*.log
    * site_access_log. *rrrr mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Jak rozwiązywanie problemów z błędami połączenia sterownika JDBC

W dziennikach Tomcat, może zostać wyświetlony następujący komunikat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Aby naprawić błąd:

1. Usuń plik sqljdbc*.jar z folderu aplikacji/lib.
2. Jeśli używasz niestandardowego serwera sieci web Tomcat lub Azure Marketplace Tomcat, skopiuj ten plik JAR do folderu biblioteki Tomcat.
3. Jeśli włączasz Java w witrynie Azure portal (wybierz **Java 1.8** > **serwera Tomcat**), skopiuj plik jar sqljdbc.* w folderze, który jest zbliżony do swojej aplikacji. Następnie należy dodać następujące ustawienie ścieżkę do pliku web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Dlaczego są wyświetlane błędy, gdy próbuję skopiować plików dziennika na żywo?

Próbujesz skopiować na żywo plików dziennika dla aplikacji Java (np. Tomcat), może zostać wyświetlony ten błąd FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Komunikat o błędzie może się różnić w zależności od klienta FTP.

Wszystkie aplikacje w języku Java mają ten problem blokowania. Tylko Kudu obsługuje pobieranie tego pliku, gdy aplikacja jest uruchomiona.

Zatrzymywanie aplikacji umożliwia FTP dostęp do tych plików.

Inne obejście jest można zapisać zadania WebJob, które jest uruchamiane zgodnie z harmonogramem, a następnie kopiuje te pliki do innego katalogu. Aby uzyskać przykładowy projekt, zobacz [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projektu.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Gdzie znaleźć pliki dziennika dla Jetty?

Dla portalu Marketplace i wdrożeń niestandardowych plik dziennika znajduje się w folderze D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Należy zauważyć, że lokalizacja folderu zależy od wersji Jetty, którego używasz. Na przykład ścieżka podana w tym miejscu jest Jetty 9.1.2. Wyszukaj jetty_*YYYY_MM_DD*. stderrout.log.

W przypadku wdrożeń aplikacji ustawienia portalu plik dziennika znajduje się w D:\home\LogFiles. Wyszukaj jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Czy mogę wysyłać wiadomości e-mail z aplikacji sieci web platformy Azure?

Usługa App Service nie ma funkcji wbudowanych wiadomości e-mail. Dla niektórych dobre rozwiązania alternatywne dla wysyłania wiadomości e-mail z aplikacji, zobacz ten [dyskusji w witrynie Stack Overflow](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Dlaczego Moja witryna WordPress przekierowania do innego adresu URL?

W przypadku niedawno migracji na platformę Azure, WordPress może przekierowywać do starego adresu URL domeny. Jest to spowodowane przez odpowiednie ustawienie w bazie danych MySQL.

WordPress Buddy + to rozszerzenie witryny platformy Azure, który umożliwia zaktualizowanie adresu URL przekierowania bezpośrednio w bazie danych. Aby uzyskać więcej informacji o korzystaniu z systemu WordPress Buddy + zobacz [WordPress narzędzi i migracja MySQL Buddy + z WordPress](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Alternatywnie Jeśli wolisz ręcznie zaktualizować Przekierowywanie adresu URL przy użyciu zapytania SQL lub narzędzia PHPMyAdmin, zobacz [WordPress: Przekierowanie do adresu URL niewłaściwego](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Jak zmienić hasło logowania WordPress?

Jeśli pamiętasz hasła logowania WordPress umożliwia WordPress Buddy + go zaktualizować. Do zresetowania hasła, zainstalować WordPress Buddy + Azure rozszerzenie witryny usługi, a następnie wykonaj kroki opisane w [WordPress narzędzi i migracja MySQL Buddy + z WordPress](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nie można zalogować się do systemu WordPress. Jak rozwiązać ten problem?

Jeśli okaże się, blokada WordPress po zainstalowaniu ostatnio wtyczkę, może być uszkodzony wtyczki. WordPress Buddy + jest rozszerzenie witryny platformy Azure, które mogą ułatwić Wyłącz wtyczek w usłudze WordPress. Aby uzyskać więcej informacji, zobacz [WordPress narzędzi i migracja MySQL Buddy + z WordPress](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Jak migrować bazę danych WordPress?

Istnieje wiele opcji migracji bazy danych MySQL, który jest podłączony do swojej witryny sieci Web WordPress:

* Deweloperzy: Użyj [wiersza polecenia lub narzędzia PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Non deweloperów: Użyj [WordPress Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Jak pomóc zabezpieczyć WordPress?

Informacje na temat najlepszych rozwiązań dotyczących zabezpieczeń dla środowiska WordPress, zobacz [najlepsze rozwiązania dotyczące zabezpieczeń WordPress na platformie Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Próbuję użyć narzędzia PHPMyAdmin i pojawił się komunikat "Odmowa dostępu." Jak rozwiązać ten problem?

Ten problem może wystąpić, jeśli funkcja w aplikacji MySQL nie jest jeszcze uruchomiona w tym wystąpieniu usługi App Service. Aby rozwiązać ten problem, spróbuj wysłać uzyskiwać dostęp do witryny. Spowoduje to uruchomienie wymaganych procesów, w tym procesie MySQL w aplikacji. Aby sprawdzić, czy MySQL w aplikacji jest uruchomiona w Eksplorator procesów, upewnij się, że ten mysqld.exe znajduje się w procesach.

Po upewnieniu się, że tego MySQL w aplikacji jest uruchomiona, spróbuj użyć narzędzia PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Błąd HTTP 403 podczas importowania lub eksportowania bazy danych MySQL w aplikacji przy użyciu narzędzia PHPMyadmin. Jak rozwiązać ten problem?

Jeśli używasz starszej wersji przeglądarki Chrome, być może wystąpiły to znana usterka. Aby rozwiązać ten problem, Uaktualnij do nowszej wersji dla programu Chrome. Spróbuj również przy użyciu innej przeglądarki, takich jak program Internet Explorer lub Microsoft Edge, których ten problem nie występuje.
