---
title: Konfigurowanie aplikacji sieci web — usłudze Azure App Service
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
ms.custom: seodec18
ms.openlocfilehash: 4286aa9cbaf07743c1d420fb1f5caace91bab7ee
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269434"
---
# <a name="configure-web-apps-in-azure-app-service"></a>Konfigurowanie aplikacji sieci Web w usłudze Azure App Service

W tym temacie wyjaśniono, jak skonfigurować aplikację internetową przy użyciu [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Ustawienia aplikacji
1. W [Azure Portal], otwórz blok dla aplikacji sieci web.
3. Kliknij pozycję **Ustawienia aplikacji**.

![Ustawienia aplikacji][configure01]

**Ustawienia aplikacji** blok zawiera ustawienia zgrupowane w kilku kategorii.

### <a name="general-settings"></a>Ustawienia ogólne
**Framework w wersji**. Jeśli aplikacja korzysta z żadnego następujące struktury, należy ustawić te opcje: 

* **.NET framework**: Ustaw wersję .NET framework. 
* **PHP**: Ustawianie wersji języka PHP lub **OFF** można wyłączyć języka PHP. 
* **Java**: Wybierz wersję języka Java lub **OFF** można wyłączyć języka Java. Użyj **kontener sieci Web** możliwość wyboru między wersjami Tomcat i Jetty.
* **Python**: Wybierz wersję języka Python lub **OFF** można wyłączyć języka Python.

Technical Preview ze względu na włączenie Java aplikacji powoduje wyłączenie opcji .NET, PHP i Python.

<a name="platform"></a>
**Platforma**. Zaznacza, czy działa aplikacja sieci web w środowisku 32-bitową lub 64-bitowych. 64-bitowego środowiska wymaga warstwy podstawowa lub standardowa. Bezpłatne, a warstwa współdzielona są zawsze uruchamiane w środowisku 32-bitowym.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Web Sockets**. Ustaw **ON** powoduje włączenie protokołu WebSocket; na przykład, jeśli aplikacja internetowa używa [Biblioteki SignalR platformy ASP.NET] lub [biblioteki socket.io](https://socket.io/).

<a name="alwayson"></a>
**Zawsze włączone**. Domyślnie aplikacje sieci web są usuwane, jeśli są one bezczynne przez pewien czas. Dzięki temu system zaoszczędzenia zasobów. W trybie Basic lub Standard, aby umożliwić **Always On** do zachowania aplikacji załadowana przez cały czas. Jeśli aplikacja działa ciągłych zadań Webjob lub uruchomienia zadania Webjob wyzwalane za pomocą wyrażenia CRON, należy włączyć **Always On**, lub zadania w sieci web może nie działać niezawodnie.

**Wersja potoku zarządzanego**. Ustawia IIS [tryb potokowy]. Pozostaw ten zestaw zintegrowany (ustawienie domyślne), chyba że masz starszych aplikacji, która wymaga starszej wersji usług IIS.

**Wersja HTTP**. Ustaw **2.0** włączyć obsługę [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokołu. 

> [!NOTE]
> Większość nowoczesnych przeglądarek obsługuje protokół HTTP/2 za pośrednictwem protokołu TLS tylko wtedy, gdy niezaszyfrowane ruch w dalszym ciągu używa protokołu HTTP/1.1. Aby upewnić się, że klient przeglądarki połączyć się z aplikacji przy użyciu protokołu HTTP/2 albo [zakup certyfikatu usługi App Service](web-sites-purchase-ssl-web-site.md) dla domeny niestandardowej aplikacji lub [powiązać certyfikat innej](app-service-web-tutorial-custom-ssl.md).

**Koligacja ARR**. W aplikacji, jest skalowana w poziomie do wielu wystąpień maszyn wirtualnych, koligacja ARR plików cookie gwarantuje, że klient jest kierowane do tego samego wystąpienia, przez cały okres istnienia sesji. Aby zwiększyć wydajność aplikacji bezstanowych, należy ustawić tę opcję na **poza**.   

**Auto-Swap**. Po włączeniu automatycznej wymiany dla miejsca wdrożenia usługi App Service automatycznie zamianę aplikacji sieci web w środowisku produkcyjnym po wypchnięciu aktualizacji do tego miejsca. Aby uzyskać więcej informacji, zobacz [wdrażanie do miejsc aplikacji sieci web w usłudze Azure App Service przejściowych](web-sites-staged-publishing.md).

### <a name="debugging"></a>Debugowanie
**Zdalne debugowanie**. Umożliwia zdalne debugowanie. Po włączeniu, aby połączyć się bezpośrednio z aplikacji sieci web za pomocą zdalnego debugera w programie Visual Studio. Zdalne debugowanie pozostanie włączony w ciągu 48 godzin. 

### <a name="app-settings"></a>Ustawienia aplikacji
Ta sekcja zawiera pary nazwa/wartość, które Twoja aplikacja sieci web załaduje się w menu start. 

* W przypadku aplikacji .NET, te ustawienia są wstrzykiwane do konfiguracji .NET `AppSettings` w czasie wykonywania, zastępowanie istniejących ustawień. 
* Dla usługi App Service w systemie Linux lub Web App for Containers, jeśli można zagnieżdżać json struktury klucza w imieniu użytkownika, takie jak `ApplicationInsights:InstrumentationKey` musisz mieć `ApplicationInsights__InstrumentationKey` jako nazwę klucza. Dlatego należy zauważyć, że wszelkie `:` powinna zostać zastąpiona przez `__` (np. podwójnym podkreśleniem).
* Aplikacje PHP, Python, Java i języka Node można uzyskać dostęp do tych ustawień, jako zmienne środowiskowe w czasie wykonywania. Dla każdego ustawienia aplikacji są tworzone dwie zmienne środowiskowe; jeden z nazwą określoną przez wpis ustawienie aplikacji, a druga z prefiksem APPSETTING_. Oba zawierają tę samą wartość.

Ustawienia aplikacji są zawsze szyfrowane, gdy przechowywany (zaszyfrowanych danych w spoczynku).

Ustawienia aplikacji mogą być rozwiązane z usługi Key Vault przy użyciu [odwołuje się do usługi Key Vault](app-service-key-vault-references.md).

### <a name="connection-strings"></a>Parametry połączeń
Parametry połączenia dla połączonych zasobów. 

W przypadku aplikacji .NET, te parametry połączenia są wstrzykiwane do konfiguracji .NET `connectionStrings` ustawienia w czasie wykonywania, zastępując istniejące wpisy, gdy klucz jest równe nazwy połączonej bazy danych. 

W przypadku aplikacji PHP, Python, Java i języka Node ustawienia te będą dostępne jako zmienne środowiskowe w czasie wykonywania, prefiks z typu połączenia. Prefiksy zmiennych środowiskowych są następujące: 

* Program SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Baza danych SQL: `SQLAZURECONNSTR_`
* Niestandardowy: `CUSTOMCONNSTR_`

Na przykład, jeśli nazwany ciąg połączenia MySql `connectionstring1`, będzie można uzyskać dostęp za pośrednictwem zmiennej środowiskowej `MYSQLCONNSTR_connectionString1`.

Parametry połączenia są zawsze szyfrowane, gdy przechowywany (zaszyfrowanych danych w spoczynku).

Parametry połączenia można rozwiązać z usługi Key Vault przy użyciu [odwołuje się do usługi Key Vault](app-service-key-vault-references.md).

### <a name="default-documents"></a>Domyślne dokumenty
Dokument domyślny jest strony sieci web, która jest wyświetlana na główny adres URL witryny sieci Web.  Pierwszy plik dopasowania na liście jest używany. 

Aplikacje sieci Web może używać modułów, że trasy na podstawie adresu URL, a nie obsługująca zawartość statyczną, w którym to przypadku nie jest dokument domyślny w związku z tym.    

### <a name="handler-mappings"></a>Mapowania procedur obsługi
Użyj tego obszaru można dodać procesorów skryptu niestandardowego do obsługi żądań dla określonych rozszerzeń plików. 

* **Rozszerzenie**. Rozszerzenie pliku, które mają być obsługiwane, takich jak *.php lub handler.fcgi. 
* **Ścieżka procesora skryptów**. Ścieżka bezwzględna procesora skryptów. Żądania dostępu do plików, zgodne z rozszerzeniem pliku zostanie przetworzone przez procesor skryptu. Użyj ścieżki `D:\home\site\wwwroot` do odwoływania się do katalogu głównego aplikacji.
* **Dodatkowe argumenty**. Opcjonalne argumenty wiersza polecenia dla procesora skryptów 

### <a name="virtual-applications-and-directories"></a>Wirtualne aplikacje i katalogi
Aby skonfigurować wirtualne aplikacje i katalogi, należy określić każdy katalog wirtualny i jego odpowiedniego ścieżka fizyczna względem katalogu głównego witryny sieci Web. Opcjonalnie można wybrać **aplikacji** pole wyboru, aby oznaczyć katalog wirtualny jako aplikacja.

## <a name="enabling-diagnostic-logs"></a>Włączanie dzienników diagnostycznych
Aby włączyć dzienniki diagnostyczne:

1. W bloku aplikacji sieci web kliknij **wszystkie ustawienia**.
2. Kliknij pozycję **Dzienniki diagnostyczne**. 

Opcje do zapisywania dzienników diagnostycznych z aplikacji sieci web, która obsługuje rejestrowanie: 

* **Rejestrowanie aplikacji**. Zapisuje Dzienniki aplikacji w systemie plików. Rejestrowanie jest dostępna na okres 12 godzin. 

**Poziom**. Gdy jest włączone rejestrowanie aplikacji, ta opcja określa, że ilość informacji, które będą rejestrowane (błąd, ostrzeżenie, informacje lub pełne).

**Rejestrowanie serwera sieci Web**. Dzienniki są zapisywane w rozszerzonym formacie W3C dziennika plików. 

**Szczegółowe komunikaty o błędach**. Zapisuje szczegółowy komunikat o błędzie komunikatów pliki .htm. Pliki są zapisywane w obszarze /LogFiles/DetailedErrors. 

**Śledzenie żądań zakończonych niepowodzeniem**. Dzienniki nieudanych żądań do plików XML. Pliki są zapisywane w obszarze/LogFiles/W3SVC*xxx*, gdzie xxx to unikatowy identyfikator. Ten folder zawiera pliku XSL i co najmniej jeden plik XML. Upewnij się, że pobieranie pliku XSL, ponieważ zapewnia funkcje dotyczące formatowania i filtrowania zawartości plików XML.

Aby wyświetlić pliki dziennika, należy utworzyć poświadczeń protokołu FTP w następujący sposób:

1. W bloku aplikacji sieci web kliknij **wszystkie ustawienia**.
2. Kliknij przycisk **poświadczenia wdrożenia**.
3. Wprowadź nazwę użytkownika i hasło.
4. Kliknij pozycję **Zapisz**.

![Konfigurowanie poświadczeń wdrożenia][configure03]

Gdzie jest pełna nazwa użytkownika protokołu FTP "app\username" *aplikacji* to nazwa aplikacji sieci web. Nazwa użytkownika znajduje się w bloku aplikacji sieci web, w obszarze **Essentials**.

![Poświadczeń wdrożenia FTP][configure02]

## <a name="other-configuration-tasks"></a>Inne zadania konfiguracji
### <a name="ssl"></a>Protokół SSL
W trybie Basic lub Standard możesz przekazać certyfikaty SSL dla domeny niestandardowej. Aby uzyskać więcej informacji, zobacz [Włącz protokół HTTPS dla aplikacji sieci web](app-service-web-tutorial-custom-ssl.md). 

Aby wyświetlić przekazany certyfikatów, kliknij **wszystkie ustawienia** > **domeny niestandardowe i protokół SSL**.

### <a name="domain-names"></a>Nazwy domen
Dodawanie niestandardowych nazw domen dla aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service](app-service-web-tutorial-custom-domain.md).

Aby wyświetlić nazwy domeny, kliknij **wszystkie ustawienia** > **domeny niestandardowe i protokół SSL**.

### <a name="deployments"></a>Wdrożenia
* Konfigurowanie ciągłego wdrażania. Zobacz [przy użyciu narzędzia Git do wdrożenia aplikacji sieci Web w usłudze Azure App Service](app-service-deploy-local-git.md).
* Miejsca wdrożenia. Zobacz [Wdróż do środowisk przejściowych dla aplikacji sieci Web w usłudze Azure App Service].

Aby wyświetlić swoje miejsc wdrożenia, kliknij **wszystkie ustawienia** > **miejsc wdrożenia**.

### <a name="monitoring"></a>Monitorowanie
W trybie Basic lub Standard można sprawdzić dostępności punktów końcowych HTTP lub HTTPS, z maksymalnie trzy rozproszone geograficznie lokalizacje. Test monitorowania kończy się niepowodzeniem, jeśli kod odpowiedzi HTTP jest błąd (4xx lub 5xx) lub odpowiedzi trwa dłużej niż 30 sekund. Punkt końcowy jest uważana za dostępną, jeśli testów monitorowania z określonych lokalizacji. 

Aby uzyskać więcej informacji, zobacz [jak: Monitorowanie stanu punktu końcowego sieci web].

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Wypróbuj usługę App Service] (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]
* [Włącz protokół HTTPS dla aplikacji w usłudze Azure App Service]
* [Skalowanie aplikacji sieci web w usłudze Azure App Service]
* [Podstawy monitorowania dla aplikacji sieci Web w usłudze Azure App Service]

<!-- URL List -->

[Biblioteki SignalR platformy ASP.NET]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Wdróż do środowisk przejściowych dla aplikacji sieci Web w usłudze Azure App Service]: ./web-sites-staged-publishing.md
[Włącz protokół HTTPS dla aplikacji w usłudze Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Jak: Monitorowanie stanu punktu końcowego sieci web]: https://go.microsoft.com/fwLink/?LinkID=279906
[Podstawy monitorowania dla aplikacji sieci Web w usłudze Azure App Service]: ./web-sites-monitor.md
[Tryb potokowy]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalowanie aplikacji sieci web w usłudze Azure App Service]: ./web-sites-scale.md
[Wypróbuj usługę App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
