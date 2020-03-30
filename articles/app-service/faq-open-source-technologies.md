---
title: Najczęściej zadawane pytania dotyczące technologii open source
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące technologii typu open source w usłudze Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 33590f9fc7e6c4d46123cbc7088086a3197d52ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672441"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące technologii open source dla aplikacji sieci Web na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z technologiami typu open source dla [funkcji aplikacji sieci Web usługi Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Jak włączyć rejestrowanie PHP w celu rozwiązywania problemów z PHP?

Aby włączyć rejestrowanie PHP:

1. Zaloguj się na swojej [stronie internetowej Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. W górnym menu wybierz polecenie **Debugt Console** > **CMD**.
3. Wybierz folder **Lokacja.**
4. Wybierz folder **wwwroot.**
5. Zaznacz **+** ikonę, a następnie wybierz pozycję **Nowy plik**.
6. Ustaw nazwę pliku na **.user.ini**.
7. Wybierz ikonę ołówka obok **pliku .user.ini**.
8. W pliku dodaj ten kod:`log_errors=on`
9. Wybierz **pozycję Zapisz**.
10. Wybierz ikonę ołówka obok **wp-config.php**.
11. Zmień tekst na następujący kod:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. W witrynie Azure portal w menu aplikacji sieci web uruchom ponownie aplikację sieci web.

Aby uzyskać więcej informacji, zobacz [Włączanie dzienników błędów WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Jak rejestrować błędy aplikacji języka Python w aplikacjach hostowanych w usłudze App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Jak zmienić wersję aplikacji Node.js hostowanej w usłudze App Service?

Aby zmienić wersję aplikacji Node.js, można użyć jednej z następujących opcji:

* W witrynie Azure portal użyj **ustawień aplikacji**.
  1. W witrynie Azure portal przejdź do aplikacji sieci web.
  2. Na bloku **Ustawienia** wybierz pozycję **Ustawienia aplikacji**.
  3. W **ustawieniach aplikacji**można uwzględnić WEBSITE_NODE_DEFAULT_VERSION jako klucz, a wersję node.js, którą chcesz uzyskać jako wartość.
  4. Przejdź do [konsoli Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
  5. Aby sprawdzić wersję node.js, wprowadź następujące polecenie:  
     ```
     node -v
     ```
* Zmodyfikuj plik iisnode.yml. Zmiana wersji node.js w pliku iisnode.yml ustawia tylko środowisko wykonawcze używane przez iisnode. Twój cmd Kudu i inne nadal używają wersji Node.js, która jest ustawiona w **ustawieniach aplikacji** w witrynie Azure portal.

  Aby ręcznie ustawić plik iisnode.yml, utwórz plik iisnode.yml w folderze głównym aplikacji. W pliku dołącz następujący wiersz:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Ustaw plik iisnode.yml przy użyciu pliku package.json podczas wdrażania kontroli źródła.
  Proces wdrażania kontroli źródła platformy Azure obejmuje następujące kroki:
  1. Przenosi zawartość do aplikacji sieci Web platformy Azure.
  2. Tworzy domyślny skrypt wdrażania, jeśli w folderze głównym aplikacji sieci web nie ma go (deploy.cmd, .deployment files).
  3. Uruchamia skrypt wdrażania, w którym tworzy plik iisnode.yml, jeśli wskażesz o wersji Node.js w pliku package.json > engine`"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Plik iisnode.yml ma następujący wiersz kodu:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Widzę komunikat "Błąd ustanowienia połączenia z bazą danych" w mojej aplikacji WordPress, która jest hostowana w usłudze App Service. Jak rozwiązać ten problem?

Jeśli widzisz ten błąd w aplikacji Azure WordPress, aby włączyć php_errors.log i debug.log, wykonaj kroki opisane w [Włącz dzienniki błędów WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Gdy dzienniki są włączone, odtwórz błąd, a następnie sprawdź dzienniki, aby sprawdzić, czy kończą się połączenia:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Jeśli ten błąd jest widoczny w plikach debug.log lub php_errors.log, aplikacja przekracza liczbę połączeń. Jeśli hostujesz w cleardb, sprawdź liczbę połączeń, które są dostępne w [planie usług](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Jak debugować aplikację Node.js hostowane w usłudze App Service?

1.  Przejdź do [konsoli Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Przejdź do folderu dzienników aplikacji (D:\home\LogFiles\Application).
3.  W pliku logging_errors.txt sprawdź zawartość.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Jak zainstalować natywne moduły języka Python w aplikacji sieci Web lub aplikacji interfejsu API usługi App Service?

Niektóre pakiety mogą nie zostać zainstalowane przy użyciu pip na platformie Azure. Pakiet może nie być dostępny w indeksie pakietów języka Python lub może być wymagany kompilator (kompilator nie jest dostępny na komputerze, na który jest uruchomiona aplikacja sieci web w usłudze App Service). Aby uzyskać informacje dotyczące instalowania modułów natywnych w aplikacjach sieci Web usługi App Service i aplikacjach interfejsu API, zobacz [Instalowanie modułów języka Python w usłudze App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Jak wdrożyć aplikację Django w usłudze App Service za pomocą Gita i nowej wersji Pythona?

Aby uzyskać informacje na temat instalowania Django, zobacz [Wdrażanie aplikacji Django w usłudze App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Gdzie znajdują się pliki dziennika Tomcat?

W przypadku portalu Azure Marketplace i wdrożeń niestandardowych:

* Lokalizacja folderu: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Interesujące akta:
    * Catalina. *yyyy-mm-dd*.log
    * host-manager. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Menedżer. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log


W przypadku wdrożeń **ustawień aplikacji** portalu:

* Lokalizacja folderu: D:\home\LogFiles
* Interesujące akta:
    * Catalina. *yyyy-mm-dd*.log
    * host-manager. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Menedżer. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Jak rozwiązywać problemy z błędami połączenia sterownika JDBC?

W dziennikach tomcat może zostać wyświetlony następujący komunikat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Aby rozwiązać ten błąd:

1. Usuń plik sqljdbc*.jar z folderu app/lib.
2. Jeśli używasz niestandardowego serwera sieci Web Tomcat lub Azure Marketplace Tomcat, skopiuj ten plik jar do folderu Tomcat lib.
3. Jeśli włączasz język Java z witryny Azure portal (wybierz serwer **Java 1.8** > **Tomcat),** skopiuj plik jar sqljdbc.* w folderze równoległym do aplikacji. Następnie dodaj następujące ustawienie ścieżki klasy do pliku web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Dlaczego podczas próby skopiowania plików dziennika na żywo są widoczne błędy?

Jeśli spróbujesz skopiować pliki dziennika na żywo dla aplikacji Java (na przykład Tomcat), może pojawić się ten błąd FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Komunikat o błędzie może się różnić w zależności od klienta FTP.

Wszystkie aplikacje Java mają ten problem z blokowaniem. Tylko Kudu obsługuje pobieranie tego pliku, gdy aplikacja jest uruchomiona.

Zatrzymanie aplikacji umożliwia ftp dostęp do tych plików.

Innym rozwiązaniem jest napisanie programu WebJob, który jest uruchamiany zgodnie z harmonogramem i kopiuje te pliki do innego katalogu. Dla przykładowego projektu, zobacz [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projektu.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Gdzie mogę znaleźć pliki dziennika dla Jetty?

W przypadku portalu Marketplace i wdrożeń niestandardowych plik dziennika znajduje się w folderze D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs folder. Należy pamiętać, że lokalizacja folderu zależy od używanej wersji molo. Na przykład ścieżka podana w tym miejscu jest dla Molo 9.1.2. Poszukaj jetty_*YYYY_MM_DD*.stderrout.log.

W przypadku wdrożeń ustawień aplikacji portalu plik dziennika znajduje się w pliku D:\home\LogFiles. Poszukaj YYYY_MM_DD jetty_ .stderrout.log*YYYY_MM_DD*

## <a name="can-i-send-email-from-my-azure-web-app"></a>Czy mogę wysyłać wiadomości e-mail z mojej aplikacji sieci Web platformy Azure?

Usługa App Service nie ma wbudowanej funkcji poczty e-mail. Aby uzyskać kilka dobrych alternatyw dla wysyłania wiadomości e-mail z aplikacji, zobacz tę [dyskusję Przepełnienie stosu](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Dlaczego moja witryna WordPress przekierowuje do innego adresu URL?

Jeśli niedawno została zmigrowana na platformę Azure, wordpress może przekierować do starego adresu URL domeny. Jest to spowodowane przez ustawienie w bazie danych MySQL.

WordPress Buddy + to rozszerzenie witryny platformy Azure, którego można użyć do aktualizacji adresu URL przekierowania bezpośrednio w bazie danych. Aby uzyskać więcej informacji na temat korzystania z WordPress Buddy +, zobacz [narzędzia WordPress i migracji MySQL z WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

Alternatywnie, jeśli wolisz ręcznie zaktualizować adres URL przekierowania za pomocą zapytań SQL lub PHPMyAdmin, zobacz [WordPress: Przekierowanie do złego adresu URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Jak zmienić hasło logowania WordPress?

Jeśli nie pamiętasz hasła logowania WordPress, możesz użyć WordPress Buddy +, aby go zaktualizować. Aby zresetować hasło, zainstaluj rozszerzenie witryny WordPress Buddy+ Azure, a następnie wykonaj kroki opisane w [narzędziach WordPress i migracji MySQL za pomocą WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nie mogę zalogować się do WordPressa. Jak rozwiązać ten problem?

Jeśli znajdziesz się zablokowane z WordPress po niedawno instalacji wtyczki, może masz wadliwy plugin. WordPress Buddy + to rozszerzenie witryny platformy Azure, które może pomóc wyłączyć wtyczki w WordPress. Aby uzyskać więcej informacji, zobacz [Narzędzia WordPress i migracja MySQL z WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Jak przeprowadzić migrację bazy danych WordPress?

Masz wiele opcji migracji bazy danych MySQL, która jest połączona z witryną WordPress:

* Deweloperzy: Użyj [wiersza polecenia lub PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Non-developers: Użyj [WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Jak pomóc w zapewnieniu zwiększenia bezpieczeństwa aplikacji WordPress?

Aby dowiedzieć się więcej o najlepszych praktykach dotyczących zabezpieczeń wordpressa, zobacz [Najważniejsze wskazówki dotyczące zabezpieczeń WordPress na platformie Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Próbuję użyć PHPMyAdmin, i widzę komunikat "Odmowa dostępu." Jak rozwiązać ten problem?

Ten problem może wystąpić, jeśli funkcja MySQL w aplikacji nie jest jeszcze uruchomiona w tym wystąpieniu usługi app service. Aby rozwiązać ten problem, spróbuj uzyskać dostęp do witryny sieci Web. Spowoduje to uruchomienie wymaganych procesów, w tym procesu mysql w aplikacji. Aby sprawdzić, czy mysql w aplikacji jest uruchomiony, w Eksploratorze procesów, upewnij się, że mysqld.exe jest wymieniony w procesach.

Po upewnić się, że MySQL w aplikacji jest uruchomiony, spróbuj użyć PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Pojawia się błąd HTTP 403, gdy próbuję zaimportować lub wyeksportować bazę danych MySQL w aplikacji za pomocą PHPMyadmin. Jak rozwiązać ten problem?

Jeśli używasz starszej wersji Chrome, może wystąpić znany błąd. Aby rozwiązać ten problem, uaktualnij chrome do nowszej wersji. Spróbuj również użyć innej przeglądarki, takiej jak Internet Explorer lub Microsoft Edge, gdzie problem nie występuje.
