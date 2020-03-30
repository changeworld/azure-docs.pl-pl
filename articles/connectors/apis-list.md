---
title: Łączniki dla usługi Azure Logic Apps
description: Automatyzuj przepływy pracy za pomocą łączników dla usługi Azure Logic Apps, takich jak wbudowane, zarządzane, lokalne, kontem integracji, środowiskiem ISE i łącznikami przedsiębiorstwa
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247334"
---
# <a name="connectors-for-azure-logic-apps"></a>Łączniki dla usługi Azure Logic Apps

Łączniki zapewniają szybki dostęp do zdarzeń, danych i akcji w ramach innych aplikacji, usług, systemów, protokołów i platform z poziomu usługi Azure Logic Apps. Korzystając z łączników w aplikacjach logiki, rozszerzasz możliwości aplikacji w chmurze i aplikacji lokalnych w celu wykonywania zadań względem tworzonych danych, którymi już dysponujesz.

Chociaż logic apps oferuje [setki łączników,](https://docs.microsoft.com/connectors)w tym artykule opisano *popularne i częściej używane* łączniki, które są z powodzeniem używane przez tysiące aplikacji i miliony wykonań do przetwarzania danych i informacji. Aby znaleźć pełną listę łączników i informacje referencyjne każdego łącznika, takie jak wyzwalacze, akcje i limity, przejrzyj strony odwołania łącznika w [obszarze Przegląd łączników](https://docs.microsoft.com/connectors). Dowiedz się więcej o [wyzwalaczach i akcjach,](#triggers-actions) [modelu cen aplikacji logiki](../logic-apps/logic-apps-pricing.md)i [szczegółach cen aplikacji logiki.](https://azure.microsoft.com/pricing/details/logic-apps/)

> [!TIP]
> Aby zintegrować się z usługą lub interfejsem API, który nie ma łącznika, można bezpośrednio wywołać usługę za pośrednictwem protokołu, takiego jak HTTP, lub utworzyć [łącznik niestandardowy](#custom).

## <a name="connector-types"></a>Typy złączy

Łączniki są dostępne jako wbudowane wyzwalacze i akcje lub jako łączniki zarządzane.

<a name="built-in"></a>

* [**Wbudowane:**](#built-ins)Wbudowane wyzwalacze i akcje są "natywne" dla usługi Azure Logic Apps i ułatwiają wykonywanie tych zadań dla aplikacji logiki:

  * Uruchamianie zgodnie z harmonogramami niestandardowymi i zaawansowanymi.

  * Organizuj i kontroluj przepływ pracy aplikacji logiki, na przykład pętle i warunki, a także do pracy ze zmiennymi i operacjami danych.

  * Komunikuj się z innymi punktami końcowymi.

  * Odbieranie i odpowiadanie na żądania.

  * Wywołanie funkcji platformy Azure, usługi Azure API Apps (Web Apps), własne interfejsy API zarządzane i publikowane za pomocą usługi Azure API Management i zagnieżdżonych aplikacji logiki, które mogą odbierać żądania.

<a name="managed-connectors"></a>

* [**Łączniki zarządzane: wdrożone**](#managed-api-connectors)i zarządzane przez firmę Microsoft, te łączniki zapewniają wyzwalacze i akcje dostępu do usług w chmurze, systemów lokalnych lub obu tych, w tym usługi Office 365, Usługi Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint i innych. Niektóre łączniki w szczególności obsługują scenariusze komunikacji między firmami (B2B) i wymagają [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) połączonego z aplikacją logiki. Przed użyciem niektórych łączników może być konieczne najpierw utworzyć połączenia, które są zarządzane przez usługi Azure Logic Apps.

  Na przykład, jeśli używasz programu Microsoft BizTalk Server, aplikacje logiki mogą łączyć się z serwerem BizTalk i komunikować się z nią za pomocą [łącznika lokalnego serwera BizTalk Server](#on-premises-connectors). Następnie można rozszerzyć lub wykonać operacje podobne do systemu BizTalk w aplikacjach logiki przy użyciu [łączników kont integracji](#integration-account-connectors).

  Łączniki są klasyfikowane jako standardowe lub enterprise. [Złącza enterprise](#enterprise-connectors) zapewniają dostęp do systemów korporacyjnych, takich jak SAP, IBM MQ i IBM 3270, za dodatkową opłatą. Aby ustalić, czy łącznik jest standardem czy przedsiębiorstwem, zobacz szczegóły techniczne na stronie referencyjnej każdego łącznika w [obszarze Przegląd łączników](https://docs.microsoft.com/connectors).

  Łączniki można również zidentyfikować przy użyciu tych kategorii, chociaż niektóre łączniki mogą przecinać wiele kategorii. Na przykład SAP jest łącznikiem przedsiębiorstwa i łącznikiem lokalnym:

  |   |   |
  |---|---|
  | [**Łączniki zarządzane**](#managed-api-connectors) | Tworzenie aplikacji logiki korzystających z usług, takich jak Usługi Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online i wiele innych. |
  | [**Łączniki lokalne**](#on-premises-connectors) | Po zainstalowaniu i skonfigurowaniu [lokalnej bramy danych łączniki][gateway-doc]te ułatwiają aplikacjom logiki dostęp do systemów lokalnych, takich jak SQL Server, SharePoint Server, Oracle DB, udziały plików i inne. |
  | [**Łączniki konta integracji**](#integration-account-connectors) | Dostępne podczas tworzenia i płacenia za konto integracji, te łączniki przekształcają i weryfikują XML, kodują i dekodują pliki płaskie oraz przetwarzają komunikaty business-to-business (B2B) za pomocą protokołów AS2, EDIFACT i X12. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Łączenie się ze środowiska usługi integracji

W przypadku aplikacji logiki, które wymagają bezpośredniego dostępu do zasobów w sieci wirtualnej platformy Azure, można utworzyć środowisko usługi integracji izolowanej [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) w którym można tworzyć, wdrażać i uruchamiać aplikacje logiki na dedykowanych zasobach. W projektancie aplikacji logiki podczas przeglądania łączników, których chcesz używać w aplikacjach logiki w programie ISE, etykieta **CORE** jest wyświetlana na wbudowanych wyzwalaczach i akcjach, podczas gdy **etykieta ISE** jest wyświetlana na niektórych łącznikach:

* **CORE:** Wbudowane wyzwalacze i akcje z tą etykietą działają w tym samym ise co aplikacje logiki, na przykład:

  ![Przykładowe złącze ISE](./media/apis-list/example-core-connector.png)

* **ISE:** Łączniki zarządzane z tą etykietą są uruchamiane w tym samym ise co aplikacje logiki, na przykład:

  ![Przykładowe złącze ISE](./media/apis-list/example-ise-connector.png)

  Jeśli masz system lokalny, który jest połączony z siecią wirtualną platformy Azure, środowisko ISE umożliwia aplikacjom logiki bezpośredni dostęp do tego systemu bez [lokalnej bramy danych.](../logic-apps/logic-apps-gateway-connection.md) Zamiast tego można użyć łącznika **ISE** tego systemu, jeśli jest dostępny, akcji HTTP lub [łącznika niestandardowego](#custom). W przypadku systemów lokalnych, które nie mają łączników **ISE,** należy użyć lokalnej bramy danych. Aby przejrzeć dostępne złącza ISE, zobacz [złącza ISE](#ise-connectors).

* Wszystkie inne łączniki bez etykiety **CORE** lub **ISE,** które można nadal używać, uruchamiane w globalnej usłudze aplikacji logiki z wieloma dzierżawcami, na przykład:

  ![Przykładowy łącznik wielodostępny](./media/apis-list/example-multi-tenant-connector.png)

Aplikacje logiki, które działają w interfejsie ISE i ich łączniki, niezależnie od tego, gdzie te łączniki są uruchamiane, są zgodne ze stałym planem cen w porównaniu z planem cen opartym na zużyciu. Aby uzyskać więcej informacji, zobacz następujące strony:

* [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md)
* [Szczegóły cen aplikacji Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Łączenie się z sieciami wirtualnymi platformy Azure za pomocą aplikacji Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Wbudowane

Logic Apps udostępnia wbudowane wyzwalacze i akcje, dzięki czemu można tworzyć przepływy pracy oparte na harmonogramie, ułatwiać aplikacjom logiki komunikować się z innymi aplikacjami i usługami, kontrolować przepływ pracy za pośrednictwem aplikacji logiki oraz zarządzać danymi lub manipulować nimi.

|   |   |   |   |
|---|---|---|---|
| [![Ikona][schedule-icon]<br>interfejsu API**Harmonogram**][schedule-doc] | - Uruchom aplikację logiki na określonym cyklu, począwszy od podstawowych do zaawansowanych harmonogramów z [wyzwalacza **cyklu** ][schedule-recurrence-doc]. <p>- Uruchom aplikację logiki, która musi obsługiwać dane w ciągłych fragmentów z [ **wyzwalacza okna przesuwnego** ][schedule-sliding-window-doc]. <p>- Wstrzymaj aplikację logiki na określony czas z [ **Delay** delay akcji][schedule-delay-doc]. <p>- Wstrzymaj aplikację logiki do określonej daty i godziny z [ **opóźnieniem do** akcji][schedule-delay-until-doc]. | [![Partia][batch-icon]<br>**ikony** interfejsu API][batch-doc] | - Przetwarzanie wiadomości w partiach za pomocą wyzwalacza **komunikatów partii.** <p>- Wywołać aplikacje logiki, które mają istniejące wyzwalacze partii z wyślij wiadomości do akcji **wsadowej.** |
| [![Ikona][http-icon]<br>interfejsu API**HTTP**][http-doc] | Dzwoń za punkty końcowe HTTP lub HTTPS za pomocą wyzwalaczy i akcji http. Inne wbudowane wyzwalacze i akcje HTTP obejmują [HTTP + Swagger][http-swagger-doc] i [HTTP + Webhook][http-webhook-doc]. | [![][http-request-icon]<br>**Żądanie** ikony interfejsu API][http-request-doc] | - Upewnij się, że aplikacja logiki wywoływane z innych aplikacji lub usług, wyzwalania zdarzeń zasobów usługi Event Grid lub wyzwalania w odpowiedzi na alerty Usługi Azure Security Center za pomocą wyzwalacza **żądania.** <p>- Wysyłanie odpowiedzi do aplikacji lub usługi z **odpowiedzią** akcji. |
| [![Ikona][azure-api-management-icon]<br>interfejsu API**Usługi Azure API <br>Management**][azure-api-management-doc] | Wyzwalacze wywołania i akcje zdefiniowane przez własne interfejsy API, którymi zarządzasz i publikujesz za pomocą usługi Azure API Management. | [![Ikona][azure-app-services-icon]<br>interfejsu API**Usługi aplikacji <br>Platformy Azure**][azure-app-services-doc] | Wywołanie usługi Azure API Apps lub aplikacji sieci Web, hostowane w usłudze Azure App Service. Wyzwalacze i akcje zdefiniowane przez te aplikacje wyglądają jak inne wyzwalacze pierwszej klasy i akcje, gdy Swagger jest uwzględniony.|
| [![Ikona][azure-logic-apps-icon]<br>interfejsu API**Azure Logic <br>Apps**][nested-logic-app-doc] | Wywołaj inne aplikacje logiki, które rozpoczynają się od wyzwalacza **żądania.** |
|||||

### <a name="run-code-from-logic-apps"></a>Uruchamianie kodu z aplikacji logiki

Logic Apps udostępnia wbudowane akcje do uruchamiania własnego kodu w przepływie pracy aplikacji logiki:

|   |   |   |   |
|---|---|---|---|
| [![Ikona][azure-functions-icon]<br>interfejsu API**Usługi Platformy Azure**][azure-functions-doc] | Wywołanie funkcji platformy Azure, które uruchamiają niestandardowe fragmenty kodu (C# lub Node.js) z aplikacji logiki. | [![Ikona][inline-code-icon]<br>INTERFEJSU API**Wbudowany kod**][azure-functions-doc] | Dodawanie i uruchamianie fragmentów kodu JavaScript z aplikacji logiki. |
|||||

### <a name="control-workflow"></a>Sterowanie przepływem pracy

Logic Apps udostępnia wbudowane akcje do strukturyzowania i kontrolowania akcji w przepływie pracy aplikacji logiki:

|   |   |   |   |
|---|---|---|---|
| [![][condition-icon]<br>**Warunek** wbudowanej ikony][condition-doc] | Oceń warunek i uruchom różne akcje na podstawie tego, czy warunek jest prawdziwy czy fałszywy. | [![Wbudowana][for-each-icon]<br>**ikona Dla każdego**][for-each-doc] | Wykonaj te same akcje dla każdego elementu w tablicy. |
| [![Wbudowany][scope-icon]<br>**zakres** ikon][scope-doc] | Grupuj akcje w *zakresy*, które otrzymują swój własny stan po zakończeniu działania w zakresie. | [![Wbudowany][switch-icon]<br>**przełącznik** ikon][switch-doc] | Grupuj akcje w *sprawy,* którym przypisano unikatowe wartości z wyjątkiem przypadku domyślnego. Uruchom tylko tę sprawę, której przypisana wartość odpowiada wynikowi z wyrażenia, obiektu lub tokenu. Jeśli nie istnieje żadne dopasowania, uruchom przypadek domyślny. |
| [![Wbudowana][terminate-icon]<br>**ikona zakończenia**][terminate-doc] | Zatrzymaj aktywnie uruchomiony przepływ pracy aplikacji logiki. | [![Wbudowana][until-icon]<br>**ikona do**][until-doc] | Powtarzaj akcje, dopóki określony warunek nie zostanie spełniony lub stan zostanie zmieniony. |
|||||

### <a name="manage-or-manipulate-data"></a>Zarządzanie danymi lub manipulowanie nimi

Logic Apps udostępnia wbudowane akcje do pracy z danymi wyjściowymi i ich formatami:

|   |   |
|---|---|
| [![Wbudowane][data-operations-icon]<br>**operacje danych** ikon][data-operations-doc] | Wykonywanie operacji z danymi: <p>- **Redagowanie:** Utwórz pojedyncze dane wyjściowe z wielu wejść o różnych typach. <br>- **Tworzenie tabeli CSV**: Tworzenie tabeli csv (CSV) oddzielonej przecinkami z tablicy z obiektami JSON. <br>- **Tworzenie tabeli HTML:** Tworzenie tabeli HTML z tablicy z obiektami JSON. <br>- **Tablica filtru:** Utwórz tablicę z elementów w innej tablicy, które spełniają twoje kryteria. <br>- **Sprzężenie:** Utwórz ciąg ze wszystkich elementów w tablicy i oddziel te elementy określonym ogranicznikiem. <br>- **Analizuj JSON:** Tworzenie tokenów przyjaznych dla użytkownika z właściwości i ich wartości w zawartości JSON, dzięki czemu można używać tych właściwości w przepływie pracy. <br>- **Wybierz**: Utwórz tablicę z obiektami JSON, przekształcając elementy lub wartości w innej tablicy i mapując te elementy na określone właściwości. |
| ![Wbudowana ikona][date-time-icon]<br>**Data II** | Wykonywanie operacji z sygnaturami czasowymi: <p>- **Dodaj do czasu:** Dodaj określoną liczbę jednostek do sygnatury czasowej. <br>- **Konwertowanie strefy czasowej:** Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. <br>- **Bieżący czas:** Zwraca bieżący znacznik czasu jako ciąg. <br>- **Pobierz czas w przyszłości:** Zwraca bieżący sygnatura czasowa oraz określone jednostki czasu. <br>- **Uzyskaj czas przeszły:** Zwraca bieżący sygnatura czasowa minus określone jednostki czasu. <br>- **Odejmij od czasu:** Odejmij liczbę jednostek czasu od sygnatury czasowej. |
| [![Wbudowane][variables-icon]<br>**zmienne** ikon][variables-doc] | Wykonywanie operacji ze zmiennymi: <p>- **Dołącz do zmiennej tablicy:** Wstaw wartość jako ostatni element w tablicy przechowywanej przez zmienną. <br>- **Dołącz do zmiennej ciągu:** Wstaw wartość jako ostatni znak w ciągu przechowywanym przez zmienną. <br>- **Zmienna dekrementacji**: Zmniejsz zmienną o stałą wartość. <br>- **Zmienna przyrostowa**: Zwiększ zmienną o stałą wartość. <br>- **Inicjowanie zmiennej:** Utwórz zmienną i zadeklaruj jej typ danych i wartość początkową. <br>- **Ustaw zmienną**: Przypisz inną wartość do istniejącej zmiennej. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Łączniki zarządzane

Logic Apps udostępnia te popularne standardowe łączniki do automatyzacji zadań, procesów i przepływów pracy za pomocą tych usług lub systemów:

|   |   |   |   |
|---|---|---|---|
| [![Ikona][azure-service-bus-icon]<br>interfejsu API**usługi Azure Service Bus**][azure-service-bus-doc] | Zarządzaj komunikatami asynchronicznymi, sesjami i subskrypcjami tematów przy użyciu najczęściej używanego łącznika w usłudze Logic Apps. | [![Ikona][sql-server-icon]<br>interfejsu API**SQL Server**][sql-server-doc] | Połącz się z programem SQL Server lokalnie lub w bazie danych SQL Azure w chmurze, aby zarządzać rekordami, uruchamiać procedury przechowywane lub wykonywać kwerendy. |
| [![Ikona][azure-blob-storage-icon]<br>interfejsu API**usługi Azure Blob<br>Storage**][azure-blob-storage-doc] | Połącz się z kontem magazynu, aby utworzyć zawartość obiektu blob i zarządzać nią. | [![Ikona][office-365-outlook-icon]<br>interfejsu API**usługi Office<br>365 Outlook**][office-365-outlook-doc] | Połącz się z kontem e-mail usługi Office 365, aby tworzyć wiadomości e-mail, zadania, wydarzenia i spotkania kalendarza, kontakty, żądania i nie tylko nimi zarządzać. |
| [![Ikona][sftp-ssh-icon]<br>INTERFEJSU API**SFTP-SSH**][sftp-ssh-doc] | Połącz się z serwerami SFTP, do których można uzyskać dostęp z Internetu, korzystając z funkcji SSH, aby można było pracować z plikami i folderami. | [![Ikona][sharepoint-online-icon]<br>interfejsu API**usługi SharePoint<br>Online**][sharepoint-online-doc] | Połącz się z usługą SharePoint Online, aby zarządzać plikami, załącznikami, folderami i nie tylko. | 
| [![Ikona][dynamics-365-icon]<br>interfejsu API**Dynamics 365<br> **][dynamics-365-doc] | Połącz się z kontem Dynamics 365, aby tworzyć rekordy, elementy i nie tylko zarządzać nimi. | [![Ikona][azure-queues-icon]<br>interfejsu API**Kolejki platformy Azure <br>**][azure-queues-doc] | Łączenie się z kontem usługi Azure Storage, aby tworzyć kolejki i wiadomości oraz zarządzać nimi |
| [![Ikona][ftp-icon]<br>interfejsu API**FTP**][ftp-doc] | Połącz się z serwerami FTP, do których masz dostęp z Internetu, aby można było pracować z plikami i folderami. | [![][file-system-icon]<br>**Ikona <br>** interfejsu API System plików][file-system-doc] | Połącz się z lokalnym udziałem plików, aby tworzyć pliki i zarządzać nimi. |
| [![Ikona][azure-event-hubs-icon]<br>interfejsu API**Usługi Azure Event Hubs**][azure-event-hubs-doc] | Zużywają i publikuj zdarzenia za pośrednictwem Centrum zdarzeń. Na przykład pobierz dane wyjściowe z aplikacji logiki z Centrum zdarzeń, a następnie wyślij to dane wyjściowe do dostawcy analizy w czasie rzeczywistym. | [![Ikona][azure-event-grid-icon]<br>interfejsu API**Usługi Azure Event**<br>**Grid**][azure-event-grid-doc] | Monitorowanie zdarzeń opublikowanych przez siatkę zdarzeń, na przykład po zmianie zasobów platformy Azure lub zasobów innych firm. |
| [![Ikona][salesforce-icon]<br>interfejsu API**Salesforce**][salesforce-doc] | Połącz się z kontem Salesforce, aby tworzyć i zarządzać elementami, takimi jak rekordy, zadania, obiekty i inne. | [![Ikona][twitter-icon]<br>API**Twitter**][twitter-doc] | Połącz się ze swoim kontem na Twitterze, aby zarządzać tweetami, obserwatorami, osią czasu i nie tylko. Zapisz tweety w programach SQL, Excel lub SharePoint. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Łączniki lokalne

Oto kilka często używanych łączników standardowych, które aplikacja Logika udostępnia do uzyskiwania dostępu do danych i zasobów w systemach lokalnych. Przed utworzeniem połączenia z systemem lokalnym należy najpierw [pobrać, zainstalować i skonfigurować lokalną bramę danych][gateway-doc]. Ta brama zapewnia bezpieczny kanał komunikacji bez konieczności konfigurowania niezbędnej infrastruktury sieciowej.

|   |   |   |   |   |
|---|---|---|---|---|
| [![Ikona][biztalk-server-icon]<br>interfejsu API**BizTalk** <br> **Server**][biztalk-server-doc] | [![][file-system-icon]<br>**Ikona <br>** interfejsu API System plików][file-system-doc] | [![Ikona][ibm-db2-icon]<br>interfejsu API**IBM DB2**][ibm-db2-doc] | [![Ikona][ibm-informix-icon]<br>interfejsu API**IBM** <br> **Informix**][ibm-informix-doc] | [![Ikona][mysql-icon]<br>interfejsu API**MySQL**][mysql-doc] |
| [![Ikona][oracle-db-icon]<br>INTERFEJSU API**Oracle DB**][oracle-db-doc] | [![Ikona][postgre-sql-icon]<br>INTERFEJSU API**PostgreSQL**][postgre-sql-doc] | [![Ikona][sharepoint-server-icon]<br>interfejsu API**programu SharePoint <br>Server**][sharepoint-server-doc] | [![Ikona][sql-server-icon]<br>interfejsu API**SQL <br>Server**][sql-server-doc] | [![Ikona][teradata-icon]<br>API**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Łączniki konta integracji

Logic Apps zapewnia standardowe łączniki do tworzenia rozwiązań biznesowych (B2B) z aplikacjami logiki podczas tworzenia i płacenia za [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), które jest dostępne za pośrednictwem pakietu Enterprise Integration Pack (EIP) na platformie Azure. Za pomocą tego konta można tworzyć i przechowywać artefakty B2B, takie jak partnerzy handlowi, umowy, mapy, schematy, certyfikaty i tak dalej. Aby użyć tych artefaktów, skojarz aplikacje logiki z kontem integracji. Jeśli obecnie używasz serwera BizTalk, te łączniki mogą wydawać się już znane.

|   |   |   |   |
|---|---|---|---|
| [![Ikona][as2-icon]<br>API**AS2 <br>dekodowanie**][as2-doc] | [![Kodowanie][as2-icon]<br>ikony INTERFEJSU API**AS2 <br>**][as2-doc] | [![Dekodowanie ikony][edifact-icon]<br>API**EDIFACT <br>**][edifact-decode-doc] | [![Kodowanie][edifact-icon]<br>ikony INTERFEJSU API**EDIFACT <br>**][edifact-encode-doc] |
| [![Ikona][flat-file-decode-icon]<br>INTERFEJSU API**Dekodowanie <br>plików płaskich**][flat-file-decode-doc] | [![Ikona][flat-file-encode-icon]<br>interfejsu API**Kodowanie <br>plików płaskich**][flat-file-encode-doc] | [![Konto][integration-account-icon]<br>**integracji ikony <br>** interfejsu API][integration-account-doc] | [![Ikona][liquid-icon]<br>API**Liquid** <br> **przekształca**][json-liquid-transform-doc] |
| [![Ikona][x12-icon]<br>INTERFEJSU API**X12 <br>dekodowanie**][x12-decode-doc] | [![Kodowanie][x12-icon]<br>ikony interfejsu**API X12 <br>**][x12-encode-doc] | [![][xml-transform-icon]<br>**XML** <br> **Xml** ikona INTERFEJSU API przekształca][xml-transform-doc] | [![Sprawdzanie][xml-validate-icon]<br>poprawności ikony interfejsu API**XML <br>**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Łączniki dla przedsiębiorstw

Aplikacja Logic Apps udostępnia te łączniki enterprise do uzyskiwania dostępu do systemów korporacyjnych, takich jak SAP i IBM MQ:

|   |   |   |
|---|---|---|
| [![Ikona][ibm-3270-icon]<br>interfejsu API**IBM 3270**][ibm-3270-doc] | [![Ikona][ibm-mq-icon]<br>interfejsu API**IBM MQ**][ibm-mq-doc] | [![Ikona][sap-icon]<br>interfejsu API**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Złącza ISE

W przypadku aplikacji logiki, które tworzysz i uruchamiasz środowisko usługi integracji izolowanej [(ISE),](#integration-service-environment)projektant aplikacji logiki identyfikuje wbudowane wyzwalacze i akcje uruchamiane w środowisku ISE przy użyciu etykiety **CORE.** Łączniki zarządzane uruchamiane w ise wyświetlają etykietę **ISE,** podczas gdy łączniki uruchamiane w globalnej usłudze aplikacji logiki z wieloma dzierżawami nie wyświetlają żadnej z etykiet. Na tej liście przedstawiono łączniki, które obecnie mają wersje ISE:

|   |   |   |   |   |
|---|---|---|---|---|
[![Ikona][as2-icon]<br>interfejsu API**AS2**][as2-doc] | [![Ikona][azure-blob-storage-icon]<br>interfejsu API**usługi Azure Blob<br>Storage**][azure-blob-storage-doc] | [![Ikona][azure-cosmos-db-icon]<br>interfejsu API**Usługi Azure <br> Cosmos DB**][azure-cosmos-db-doc] | [![Ikona][azure-event-hubs-icon]<br>interfejsu API**Usługi Azure <br>Event Hubs**][azure-event-hubs-doc] | [![Ikona][azure-file-storage-icon]<br>interfejsu API**Usługi Azure File<br>Storage**][azure-file-storage-doc] |
| [![Ikona][azure-service-bus-icon]<br>interfejsu API**usługi Azure Service <br>Bus**][azure-service-bus-doc] | [![Ikona][azure-sql-data-warehouse-icon]<br>interfejsu API**Usługi Azure SQL Data <br>Warehouse**][azure-sql-data-warehouse-doc] | [![Ikona][azure-table-storage-icon]<br>interfejsu API**Usługi Azure Table <br>Storage**][azure-table-storage-doc] | [![Ikona][azure-queues-icon]<br>interfejsu API**Kolejki platformy Azure <br>**][azure-queues-doc] | [![Ikona][edifact-icon]<br>API**EDIFACT**][edifact-doc] |
| [![][file-system-icon]<br>**Ikona <br>** interfejsu API System plików][file-system-doc] | [![Ikona][ftp-icon]<br>interfejsu API**FTP**][ftp-doc] | [![Ikona][ibm-3270-icon]<br>interfejsu API**IBM 3270**][ibm-3270-doc] | [![Ikona][ibm-db2-icon]<br>interfejsu API**IBM DB2**][ibm-db2-doc] | [![Ikona][ibm-mq-icon]<br>interfejsu API**IBM MQ**][ibm-mq-doc] |
| [![Ikona][sap-icon]<br>interfejsu API**SAP**][sap-connector-doc] | [![Ikona][sftp-ssh-icon]<br>INTERFEJSU API**SFTP-SSH**][sftp-ssh-doc] | [![Ikona][smtp-icon]<br>interfejsu API**SMTP**][smtp-doc] | [![Ikona][sql-server-icon]<br>interfejsu API**SQL <br>Server**][sql-server-doc] | [![Ikona][x12-icon]<br>interfejsu API**X12**][x12-doc] |
||||||

Więcej informacji można znaleźć w następujących tematach:

* [Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md)
* [Łączenie się z sieciami wirtualnymi platformy Azure za pomocą aplikacji Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Wyzwalacze i typy akcji

Łączniki mogą dostarczać *wyzwalacze,* *akcje*lub oba te elementy. *Wyzwalacz* jest pierwszym krokiem w dowolnej aplikacji logiki, zwykle określając zdarzenie, które uruchamia wyzwalacz i rozpoczyna uruchamianie aplikacji logiki. Na przykład łącznik FTP ma wyzwalacz, który uruchamia aplikację logiki "po dodaniu lub zmodyfikowaniu pliku". Niektóre wyzwalacze regularnie sprawdzają określone zdarzenie lub dane, a następnie uruchamiają się po wykryciu określonego zdarzenia lub danych. Inne wyzwalacze czekają, ale uruchamiają się natychmiast, gdy nastąpi określone zdarzenie lub gdy dostępne są nowe dane. Wyzwalacze również przekazać wszystkie wymagane dane do aplikacji logiki. Aplikacja logiki może odczytywać i używać tych danych w całym przepływie pracy. Na przykład łącznik Twitter ma wyzwalacz "Po opublikowaniu nowego tweeta", który przekazuje zawartość tweeta do przepływu pracy aplikacji logiki.

Po uruchomieniu wyzwalacza usługa Azure Logic Apps tworzy wystąpienie aplikacji logiki i rozpoczyna uruchamianie *akcji* w przepływie pracy aplikacji logiki. Akcje to kroki, które należy wykonać wyzwalacz i wykonywać zadania w przepływie pracy aplikacji logiki. Na przykład można utworzyć aplikację logiki, która pobiera dane klientów z bazy danych SQL i przetwarzać te dane w późniejszych akcjach.

Oto ogólne rodzaje wyzwalaczy, które udostępnia usługa Azure Logic Apps:

* *Wyzwalacz cyklu:* Ten wyzwalacz działa zgodnie z określonym harmonogramem i nie jest ściśle skojarzony z określoną usługą lub systemem.

* *Wyzwalacz sondowania:* Ten wyzwalacz regularnie sonduje określoną usługę lub system na podstawie określonego harmonogramu, sprawdzając, czy nie ma nowych danych lub czy wystąpiło określone zdarzenie. Jeśli nowe dane są dostępne lub wystąpienie określonego zdarzenia wyzwalacz tworzy i uruchamia nowe wystąpienie aplikacji logiki, które może teraz używać danych przekazywanych jako dane wejściowe.

* *Wyzwalacz wypychania:* Ten wyzwalacz czeka i nasłuchuje nowych danych lub zdarzenia. Gdy nowe dane są dostępne lub gdy zdarzenie się dzieje, wyzwalacz tworzy i uruchamia nowe wystąpienie aplikacji logiki, która może teraz używać danych, które są przekazywane jako dane wejściowe.

<a name="connections"></a>

## <a name="connector-configuration"></a>Konfiguracja łącznika

Wyzwalacze i akcje każdego łącznika zapewniają własne właściwości do skonfigurowania. Wiele łączników wymaga również, aby najpierw utworzyć *połączenie z* usługą docelową lub systemem i podać poświadczenia uwierzytelniania lub inne szczegóły konfiguracji, zanim będzie można użyć wyzwalacza lub akcji w aplikacji logiki. Na przykład musisz autoryzować połączenie z kontem twitterowym w celu uzyskania dostępu do danych lub opublikowania w Twoim imieniu.

W przypadku łączników korzystających z usługi Azure Active Directory (Azure AD) OAuth utworzenie połączenia oznacza zalogowanie się do usługi, takiej jak Office 365, Salesforce lub GitHub, gdzie token dostępu jest [szyfrowany](../security/fundamentals/encryption-overview.md) i bezpiecznie przechowywany w tajnym magazynie platformy Azure. Inne łączniki, takie jak FTP i SQL, wymagają połączenia zawierającego szczegóły konfiguracji, takie jak adres serwera, nazwa użytkownika i hasło. Te szczegóły konfiguracji połączenia są również szyfrowane i bezpiecznie przechowywane. Dowiedz się więcej o [szyfrowaniu na platformie Azure](../security/fundamentals/encryption-overview.md).

Połączenia mogą uzyskać dostęp do usługi docelowej lub systemu tak długo, jak ta usługa lub system pozwala. W przypadku usług korzystających z połączeń usługi Azure AD OAuth, takich jak Office 365 i Dynamics, usługa Azure Logic Apps odświeża tokeny dostępu przez czas nieokreślony. Inne usługi mogą mieć limity dotyczące czasu korzystania z tokenu usługi Azure Logic Apps bez odświeżania. Ogólnie rzecz biorąc niektóre akcje unieważniają wszystkie tokeny dostępu, takie jak zmiana hasła.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Niestandardowe interfejsy API i łączniki

Aby wywołać interfejsy API, które uruchamiają kod niestandardowy lub nie są dostępne jako łączniki, można rozszerzyć platformę Aplikacje logiki, [tworząc niestandardowe aplikacje interfejsu API](../logic-apps/logic-apps-create-api-app.md). Można również [utworzyć łączniki niestandardowe](../logic-apps/custom-connector-overview.md) dla *dowolnych* interfejsów API opartych na rest lub soap, które udostępniają te interfejsy API do dowolnej aplikacji logiki w ramach subskrypcji platformy Azure. Aby udostępnić publicznie niestandardowe aplikacje interfejsu API lub łączniki dla wszystkich osób do użycia na platformie Azure, można [przesłać łączniki do certyfikacji firmy Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Aplikacje logiki, które można wdrożyć i uruchomić w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) można bezpośrednio uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. Jeśli masz łączniki niestandardowe, które wymagają lokalnej bramy danych i utworzono te łączniki poza środowiskiem ISE, aplikacje logiki w środowisku ISE mogą również używać tych łączników.
>
> Łączniki niestandardowe utworzone w środowisku ISE nie działają z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskiwać dostęp do lokalnych źródeł danych, które są połączone z siecią wirtualną platformy Azure hostującą środowisko ISE. Tak więc aplikacje logiki w ise najprawdopodobniej nie potrzebują bramy danych podczas komunikowania się z tymi zasobami.
>
> Aby uzyskać więcej informacji na temat tworzenia środowisk internetowych, zobacz [Łączenie się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Następne kroki

* Wyświetlanie [pełnej listy łączników](https://docs.microsoft.com/connectors)
* [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Tworzenie łączników niestandardowych dla aplikacji logiki](https://docs.microsoft.com/connectors/custom-connectors/)
* [Tworzenie niestandardowych interfejsów API dla aplikacji logiki](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Łączenie z lokalnymi źródłami danych z aplikacji logiki za pomocą bramy danych lokalnych"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Tworzenie wystąpienia usługi Azure API Management dla zarządzania interfejsami API i publikowania ich"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integracja aplikacji logiki z usługą App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integruj aplikacje logiki z usługą Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Przetwarzanie wiadomości w grupach lub w partiach"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Oceń warunek i uruchom różne akcje na podstawie tego, czy warunek jest prawdziwy czy fałszywy"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Wykonywanie tych samych akcji na każdym elemencie w tablicy"
[http-doc]: ./connectors-native-http.md "Wywoływanie punktów końcowych HTTP lub HTTPS z aplikacji logiki"
[http-request-doc]: ./connectors-native-reqres.md "Odbieranie żądań HTTP w aplikacjach logiki"
[http-response-doc]: ./connectors-native-reqres.md "Odpowiadanie na żądania HTTP z aplikacji logiki"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Wywoływanie punktów końcowych REST z aplikacji logiki"
[http-webhook-doc]: ./connectors-native-webhook.md "Poczekaj na określone zdarzenia z punktów końcowych HTTP lub HTTPS"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integracja aplikacji logiki z zagnieżdżonymi przepływami pracy"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Wybieranie i filtrowanie tablic za pomocą akcji zapytania"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Uruchamianie aplikacji logiki na podstawie harmonogramu"
[schedule-delay-doc]: ./connectors-native-delay.md "Opóźnienie uruchomienia następnej akcji"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Opóźnienie uruchomienia następnej akcji"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Uruchamianie aplikacji logiki zgodnie z harmonogramem cyklicznym"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Uruchamianie aplikacji logiki, które muszą obsługiwać dane w ciągłych fragmentach"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizowanie akcji w grupy, które otrzymują swój własny status po zakończeniu działania w grupie"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizowanie akcji w sprawy, którym przypisano unikatowe wartości. Uruchom tylko przypadek, którego wartość odpowiada wynikowi z wyrażenia, obiektu lub tokenu. Jeśli nie istnieją żadne dopasowania, uruchom domyślną sprawę"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zatrzymywać lub anulować aktywnie uruchomiony przepływ pracy dla aplikacji logiki"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Powtarzaj akcje, dopóki określony warunek nie zostanie spełniony lub stan się zmieni"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Wykonywanie operacji danych, takich jak filtrowanie tablic lub tworzenie tabel CSV i HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Wykonywanie operacji ze zmiennymi, takimi jak inicjowanie, ustawianie, zwiększanie, zmniejszanie i dołączanie do zmiennej ciągu lub tablicy"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Zarządzanie plikami w kontenerze obiektów blob za pomocą łącznika usługi Azure Blob Storage"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Łączenie się z usługą Azure Cosmos DB, aby uzyskać dostęp do dokumentów i procedur przechowywanych"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorowanie zdarzeń publikowanych przez siatkę zdarzeń, na przykład po zmianie zasobów platformy Azure lub zasobów innych firm"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Łączenie się z centrum zdarzeń platformy Azure, aby odbierać i wysyłać zdarzenia między aplikacjami logiki a centrum zdarzeń"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Połącz się ze swoim kontem usługi Azure Storage, aby tworzyć, aktualizować, utrzymywać i usuwać pliki"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Łączenie się z kontem usługi Azure Storage, aby tworzyć kolejki i wiadomości oraz zarządzać nimi"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Wysyłanie wiadomości z kolejek i tematów usługi Service Bus oraz odbieranie wiadomości z kolejek i subskrypcji magistrali usług"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Połącz się z usługą Azure SQL Data Warehouse, aby wyświetlać dane"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Łączenie się z kontem usługi Azure Storage, aby tworzyć, aktualizować i wysyłać zapytania i nie tylko"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Łączenie się z serwerem BizTalk, aby można było uruchamiać aplikacje oparte na systemie BizTalk obok siebie za pomocą aplikacji Azure Logic Apps"
[box-doc]: ./connectors-create-api-box.md "Połącz się z polem. Przesyłanie, usuwanie, wyświetlanie listy plików i nie tylko"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Połącz się z Dropbox. Przesyłanie, usuwanie, wyświetlanie listy plików i nie tylko"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Połącz się z dynamics CRM Online, aby pracować z danymi crm online"
[facebook-doc]: ./connectors-create-api-facebook.md "Połącz się z Facebookiem. Publikowanie na osi czasu, publikowanie kanału informacyjnego strony i nie tylko"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Łączenie z lokalnym systemem plików"
[ftp-doc]: ./connectors-create-api-ftp.md "Łączenie z serwerem FTP/FTPS i wykonywanie zadań związanych z protokołem FTP, takich jak np. przekazywanie, pobieranie i usuwanie plików"
[github-doc]: ./connectors-create-api-github.md "Łączenie z witryną GitHub i śledzenie problemów"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Łączy się z Kalendarzem Google i może zarządzać kalendarzem"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Połącz się z GoogleDrive, aby pracować z danymi"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Łączenie się z Arkuszami Google, aby modyfikować arkusze"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Łączy się z zadaniami Google, dzięki czemu możesz zarządzać zadaniami"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Łączenie się z aplikacjami 3270 na komputerach mainframe IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Połącz się z IBM DB2 w chmurze lub lokalnie. Aktualizowanie wiersza, otrzymywki i nie tylko"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Połącz się z informixem w chmurze lub lokalnie. Przeczytaj wiersz, wyświetl tabele i nie tylko"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Łączenie się z ibm MQ lokalnie lub na platformie Azure, aby wysyłać i odbierać wiadomości"
[instagram-doc]: ./connectors-create-api-instagram.md "Połącz się z Instagramem. Wyzwalanie zdarzeń lub działanie na nie"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Połącz się ze swoim kontem MailChimp. Zarządzanie pocztą i automatyz y"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Łączenie z usługą Mandrill w celu komunikowania się"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Połącz się z lokalną bazą danych MySQL, aby odczytywać i zapisywać dane"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Połącz się z kontem usługi Office 365, aby wysyłać i odbierać wiadomości e-mail, zarządzać kalendarzem i kontaktami i nie tylko"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Połącz się z osobistą usługą Microsoft OneDrive, aby przesyłać, usuwać, wystawiać pliki i nie tylko"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Połącz się z firmą Microsoft OneDrive, aby można było przesyłać, usuwać, wystawiać pliki i nie tylko"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Łączenie się z bazą danych Oracle, aby można było dodawać, wstawiać, usuwać wiersze i nie tylko"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Łączenie się ze skrzynką pocztową programu Outlook, aby zarządzać pocztą e-mail, kalendarzami, kontaktami i nie tylko"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Połącz się z bazą danych PostgreSQL, aby odczytywać dane z tabel"
[project-online-doc]: ./connectors-create-api-projectonline.md "Łączenie się z usługą Microsoft Project Online, aby zarządzać projektami, zadaniami, zasobami i nie tylko"
[rss-doc]: ./connectors-create-api-rss.md "Publikowanie i pobieranie elementów pliku danych, wyzwalanie operacji, gdy nowy element jest publikowany w kanale informacyjnym RSS"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Połącz się ze swoim kontem Salesforce. Zarządzanie kontami, potencjalnymi klientami, szansami sprzedaży i nie tylko"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Łączenie z lokalnym systemem SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Połącz się z SendGrid. Wysyłanie wiadomości e-mail i zarządzanie listami adresatów"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Połącz się ze swoim kontem SFTP za pomocą SSH. Przesyłanie, ugryczanie, usuwanie plików i nie tylko"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Łączenie się z serwerem lokalnym programu SharePoint. Zarządzanie dokumentami, elementami listy i nie tylko"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Połącz się z usługą SharePoint Online. Zarządzanie dokumentami, elementami listy i nie tylko"
[slack-doc]: ./connectors-create-api-slack.md "Łączenie się z slackiem i publikowanie wiadomości w kanałach Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Łączenie się z serwerem SMTP i wysyłanie wiadomości e-mail z załącznikami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Łączenie z usługą SparkPost w celu komunikowania się"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Połącz się z usługą Azure SQL Database lub SQL Server. Tworzenie, aktualizowanie, pobierania i usuwanie wpisów w tabeli bazy danych SQL"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Łączenie się z bazą danych Teradata w celu odczytu danych z tabel"
[trello-doc]: ./connectors-create-api-trello.md "Połącz się z Trello. Zarządzaj swoimi projektami i organizuj wszystko z kimkolwiek"
[twilio-doc]: ./connectors-create-api-twilio.md "Połącz się z urządzeniem Twilio. Wysyłaj i utrzymywuj wiadomości, otrzyj dostępne numery, zarządzaj przychodzącymi numerami telefonów i nie tylko"
[twitter-doc]: ./connectors-create-api-twitter.md "Połącz się z Twitterem. Otrzymuuj osi czasu, publikuj tweety i nie tylko"
[yammer-doc]: ./connectors-create-api-yammer.md "Połącz się z usługi Yammer. Publikowanie wiadomości, publikowanie nowych wiadomości i nie tylko"
[youtube-doc]: ./connectors-create-api-youtube.md "Połącz się z YouTube. Zarządzanie filmami i kanałami"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Kodowanie i dekodowanie wiadomości korzystających z protokołu AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Kodowanie i dekodowanie wiadomości korzystających z protokołu EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Dekodowanie wiadomości korzystających z protokołu EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Kodowanie wiadomości korzystających z protokołu EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Dowiedz się więcej o płaskim pliku integracji przedsiębiorstwa"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Dowiedz się więcej o płaskim pliku integracji przedsiębiorstwa"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Zarządzanie metadanymi artefaktów kont integracji"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Przekształcanie JSON za pomocą szablonów Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Kodowanie i dekodowanie wiadomości korzystających z protokołu X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Dekodowanie wiadomości korzystających z protokołu X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Kodowanie wiadomości korzystających z protokołu X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Przekształcanie komunikatów XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Sprawdzanie poprawności komunikatów XML"

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
