---
title: Konfigurowanie aplikacji sieci Web w usłudze Azure App Service
description: Jak skonfigurować aplikację sieci web w usłudze Azure App Services
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: 0c1cea1646c71698318e94932248e08955359b9e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234529"
---
# <a name="configure-web-apps-in-azure-app-service"></a>Konfigurowanie aplikacji sieci Web w usłudze Azure App Service

W tym temacie opisano sposób konfigurowania aplikacji sieci web za pomocą [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Ustawienia aplikacji
1. W [Azure Portal], otwórz blok dla aplikacji sieci web.
3. Kliknij pozycję **Ustawienia aplikacji**.

![Ustawienia aplikacji][configure01]

**Ustawienia aplikacji** bloku ma ustawienia pogrupowane w różne kategorie.

### <a name="general-settings"></a>Ustawienia ogólne
**Framework w wersji**. Ustaw tych opcji, jeśli aplikacja używa tych platform: 

* **.NET framework**: Ustaw wersji programu .NET framework. 
* **PHP**: Ustaw wersję PHP lub **OFF** wyłączyć PHP. 
* **Java**: Wybierz wersję Java lub **OFF** wyłączyć Java. Użyj **kontener sieci Web** opcję, aby wybrać Tomcat i Jetty wersji.
* **Python**: Wybierz wersję języka Python lub **OFF** wyłączyć Python.

Ze względów technicznych włączenie Java aplikacji powoduje wyłączenie opcji .NET, PHP i Python.

<a name="platform"></a>
**Platforma**. Wybiera, czy aplikacja sieci web jest uruchamiana w środowisku 32-bitowy lub 64-bitowych. 64-bitowego środowiska wymaga trybu Basic lub Standard. Zwolnij i tryby udostępnione są zawsze uruchamiane w środowisku 32-bitowym.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Sieci Web Sockets**. Ustaw **ON** Aby włączyć protokół WebSocket; na przykład, jeśli aplikacja sieci web używa [Biblioteka SignalR platformy ASP.NET] lub [użyciu biblioteki socket.io](https://socket.io/).

<a name="alwayson"></a>
**Zawsze włączone**. Domyślnie aplikacje sieci web są usuwane, jeśli są one bezczynności przez niektóre czas. Pozwala to zaoszczędzić zasoby systemu. W trybie Basic lub Standard, aby umożliwić **zawsze na** do zachowania aplikacji załadowana przez cały czas. Jeśli aplikacja będzie działać ciągłe zadania Webjob lub uruchamia zadania Webjob wyzwolone przy użyciu wyrażenia CRON, należy włączyć **zawsze na**, lub zadania sieci web mogą nie działać prawidłowo.

**Zarządzane wersji potoku**. Ustawia IIS [tryb potokowy]. Pozostaw tego zestawu na zintegrowane (ustawienie domyślne), chyba że masz starszej wersji aplikacji, która wymaga starszej wersji programu IIS.

**Automatycznie wymiany**. Po włączeniu automatycznej wymiany dla miejsca wdrożenia usługi aplikacji — automatycznie zamianę aplikacji sieci web w środowisku produkcyjnym po naciśnięciu aktualizacji do tego miejsca. Aby uzyskać więcej informacji, zobacz [wdrażanie na tymczasowej miejsc aplikacji sieci web w usłudze Azure App Service](web-sites-staged-publishing.md).

### <a name="debugging"></a>Debugowanie
**Debugowanie zdalne**. Umożliwia zdalne debugowanie. Po włączeniu można użyć zdalnego debugera w programie Visual Studio, aby połączyć się bezpośrednio z aplikacji sieci web. Debugowanie zdalne pozostanie włączony 48 godzin. 

### <a name="app-settings"></a>Ustawienia aplikacji
Ta sekcja zawiera pary nazwa/wartość, które Twoja aplikacja sieci web zostanie załadowany po uruchomieniu się. 

* W przypadku aplikacji .NET, te ustawienia są wstrzykiwane do konfiguracji .NET `AppSettings` w czasie wykonywania, zastępowanie istniejących ustawień. 
* Aplikacje PHP, Python, Java i węzła mają dostęp do tych ustawień jako zmienne środowiskowe w czasie wykonywania. Dla każdego ustawienia aplikacji są tworzone dwie zmienne środowiskowe; jeden z nazwą określoną przez ustawienie wpis aplikacji, a druga z prefiksem APPSETTING_. Zawierają tę samą wartość.

Ustawienia aplikacji są zawsze szyfrowane, gdy przechowywane (szyfrowane podczas spoczynku).

### <a name="connection-strings"></a>Parametry połączeń
Parametry połączenia dla połączonych zasobów. 

W przypadku aplikacji .NET, te parametry połączenia są wstrzykiwane do konfiguracji .NET `connectionStrings` ustawienia w czasie wykonywania, zastępowanie istniejących wpisów, gdy klucz jest równe nazwy połączonej bazy danych. 

W przypadku aplikacji PHP, Python, Java i węzła te ustawienia będą dostępne jako zmienne środowiskowe w czasie wykonywania, prefiksem typu połączenia. Prefiksy zmiennej środowiskowej są następujące: 

* Program SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Baza danych SQL: `SQLAZURECONNSTR_`
* Niestandardowe: `CUSTOMCONNSTR_`

Na przykład, jeśli parametry połączenia MySql nazwany `connectionstring1`, czy dostęp do niej za pomocą zmiennej środowiskowej `MYSQLCONNSTR_connectionString1`.

Parametry połączenia są zawsze szyfrowane, gdy przechowywane (szyfrowane podczas spoczynku).

### <a name="default-documents"></a>Domyślne dokumenty
Dokument domyślny jest strony sieci web wyświetlaną w głównego adresu URL witryny sieci Web.  Pierwszy odpowiedniego pliku na liście jest używany. 

Aplikacje sieci Web może używać modułów, że trasy na podstawie adresu URL, a nie obsługujących zawartość statyczną, w którym to przypadku nie jest dokument domyślny, w związku.    

### <a name="handler-mappings"></a>Mapowania procedur obsługi
Aby dodać procesorów skryptu niestandardowego do obsługi żądań określonych rozszerzeń plików, użyj tego obszaru. 

* **Rozszerzenie**. Rozszerzenie pliku, które mają być obsługiwane, takie jak *.php lub handler.fcgi. 
* **Ścieżka do procesora skryptów**. Ścieżka bezwzględna procesora skryptów. Żądania dostępu do plików, zgodne z rozszerzeniem pliku zostanie przetworzony przez procesora skryptów. Użyj ścieżki `D:\home\site\wwwroot` do odwoływania się do katalogu głównego aplikacji.
* **Dodatkowe argumenty**. Opcjonalne argumenty wiersza polecenia do procesora skryptów 

### <a name="virtual-applications-and-directories"></a>Wirtualne aplikacje i katalogi
Aby skonfigurować wirtualne aplikacje i katalogi, określ każdy katalog wirtualny i jego odpowiedniego ścieżka fizyczna względem katalogu głównego witryny sieci Web. Opcjonalnie można wybrać **aplikacji** pole wyboru, aby oznaczyć katalogu wirtualnego jako aplikacji.

## <a name="enabling-diagnostic-logs"></a>Włączanie dzienników diagnostycznych
Aby włączyć dzienniki diagnostyczne:

1. W bloku dla aplikacji sieci web, kliknij **wszystkie ustawienia**.
2. Kliknij przycisk **dzienniki diagnostyczne**. 

Opcje zapisywania dzienników diagnostycznych z aplikacji sieci web, która obsługuje rejestrowanie: 

* **Rejestrowanie aplikacji**. Zapisuje Dzienniki aplikacji w systemie plików. Rejestrowanie okresu w okresie 12 godzin. 

**Poziom**. Po włączeniu rejestrowania aplikacji, ta opcja określa, że ilość informacji, które będą rejestrowane (błąd, ostrzeżenie, informacje lub pełne).

**Rejestrowanie pracy serwera sieci Web**. Dzienniki są zapisywane w rozszerzonym formacie W3C dziennika pliku. 

**Szczegółowe komunikaty o błędach**. Zapisuje szczegółowe informacje o błędzie komunikatów pliki htm. Pliki są zapisywane w obszarze /LogFiles/DetailedErrors. 

**Śledzenie nieudanych żądań**. Dzienniki nieudane żądania do plików XML. Pliki są zapisywane w obszarze/LogFiles/W3SVC*xxx*, gdzie xxx jest unikatowy identyfikator. Ten folder zawiera plik XSL i co najmniej jeden plik XML. Upewnij się, że Pobierz plik XSL, ponieważ zapewnia funkcje dotyczące formatowania i filtrowanie zawartości plików XML.

Aby wyświetlić pliki dziennika, należy utworzyć poświadczenia FTP w następujący sposób:

1. W bloku dla aplikacji sieci web, kliknij **wszystkie ustawienia**.
2. Kliknij przycisk **poświadczenia wdrażania**.
3. Wprowadź nazwę użytkownika i hasło.
4. Kliknij pozycję **Zapisz**.

![Konfigurowanie poświadczeń wdrożenia][configure03]

Pełna nazwa użytkownika FTP jest "app\username", gdzie *aplikacji* to nazwa aplikacji sieci web. Nazwa użytkownika jest wymieniony w bloku aplikacja sieci web, w obszarze **Essentials**.

![Poświadczenia wdrożenia FTP][configure02]

## <a name="other-configuration-tasks"></a>Inne zadania konfiguracji
### <a name="ssl"></a>Protokół SSL
W trybie Basic lub Standard możesz przekazać certyfikatów SSL dla domeny niestandardowej. Aby uzyskać więcej informacji, zobacz [Włącz protokół HTTPS dla aplikacji sieci web](app-service-web-tutorial-custom-ssl.md). 

Aby wyświetlić przekazane certyfikaty, kliknij przycisk **wszystkie ustawienia** > **domen niestandardowych i SSL**.

### <a name="domain-names"></a>Nazwy domen
Dodawanie niestandardowych nazw domen dla aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service](app-service-web-tutorial-custom-domain.md).

Aby wyświetlić nazwy domeny, kliknij przycisk **wszystkie ustawienia** > **domen niestandardowych i SSL**.

### <a name="deployments"></a>Wdrożenia
* Konfigurowanie ciągłego wdrażania. Zobacz [przy użyciu narzędzia Git do wdrożenia aplikacji Web Apps w usłudze Azure App Service](app-service-deploy-local-git.md).
* Miejsc wdrożenia. Zobacz [Wdrażanie na środowisk przejściowych dla aplikacji sieci Web w usłudze aplikacji Azure].

Aby wyświetlić Twojego miejsca wdrożenia, kliknij przycisk **wszystkie ustawienia** > **miejsc wdrożenia**.

### <a name="monitoring"></a>Monitorowanie
W trybie Basic lub Standard można sprawdzić dostępności punktów końcowych HTTP lub HTTPS, z maksymalnie trzech rozproszona geograficznie lokalizacji. Test monitorowania kończy się niepowodzeniem, jeśli kod odpowiedzi HTTP jest błąd (4xx lub 5xx) lub odpowiedzi trwa dłużej niż 30 sekund. Punkt końcowy jest traktowany jako dostępne w przypadku powodzenia testów monitorowania w określonych lokalizacjach. 

Aby uzyskać więcej informacji, zobacz [porady: monitorować stan punktu końcowego sieci web].

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Wypróbuj usługę App Service] (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]
* [Włącz protokół HTTPS dla aplikacji w usłudze aplikacji Azure]
* [Skalowanie aplikacji sieci web w usłudze Azure App Service]
* [Podstawy monitorowania aplikacji sieci Web w usłudze Azure App Service]

<!-- URL List -->

[Biblioteka SignalR platformy ASP.NET]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Wdrażanie na środowisk przejściowych dla aplikacji sieci Web w usłudze aplikacji Azure]: ./web-sites-staged-publishing.md
[Włącz protokół HTTPS dla aplikacji w usłudze aplikacji Azure]: ./app-service-web-tutorial-custom-ssl.md
[Porady: monitorować stan punktu końcowego sieci web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Podstawy monitorowania aplikacji sieci Web w usłudze Azure App Service]: ./web-sites-monitor.md
[Tryb potokowy]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalowanie aplikacji sieci web w usłudze Azure App Service]: ./web-sites-scale.md
[Wypróbuj usługę App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
