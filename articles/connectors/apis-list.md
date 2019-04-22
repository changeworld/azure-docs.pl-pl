---
title: Łączniki dla usługi Azure Logic Apps
description: Automatyzuj przepływy pracy za pomocą łączników dla usługi Azure Logic Apps, m.in. wbudowane, zarządzany w środowisku lokalnym, konto integracji i łączniki dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 59c8effb4c5feae99755b7937f4796e8f11fde46
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895887"
---
# <a name="connectors-for-azure-logic-apps"></a>Łączniki dla usługi Azure Logic Apps

Łączniki udostępniają szybki dostęp z usługi Azure Logic Apps do zdarzeń, danych i akcje w innych aplikacjach, usług, systemów, protokołów i platform. Za pomocą łączników w aplikacjach logiki, możesz rozszerzyć możliwości dla aplikacji w chmurze i lokalnych do wykonywania zadań z danymi, które tworzysz i masz już.

Podczas gdy Logic Apps oferuje [~ ponad 200 łączników](https://docs.microsoft.com/connectors), w tym artykule opisano popularnych i najczęściej używanych łączników tysiące aplikacji i milionach wykonań pomyślnie są używane do przetwarzania danych i informacji. Aby znaleźć pełną listę łączników i informacje referencyjne poszczególnych łączników, takich jak wyzwalacze, akcje i ograniczeń, przejrzyj łącznika odwołuje się do strony w obszarze [omówienie łączników](https://docs.microsoft.com/connectors). Ponadto Dowiedz się więcej o [wyzwalacze i akcje](#triggers-actions).

> [!NOTE]
> Aby zintegrować z usługą lub interfejsu API, który nie ma łącznika, można bezpośrednio wywołać usługę za pośrednictwem protokołu, takich jak HTTP lub utworzyć [łącznika niestandardowego](#custom).

Łączniki są dostępne jako wbudowane wyzwalaczy i akcji lub zarządzanych łączników:

* [**Elementy wbudowane**](#built-ins): Te wbudowane akcje i wyzwalacze są "natywnego" do usługi Azure Logic Apps oraz pomagają Ci tworzyć aplikacje logiki, które systemem niestandardowe harmonogramy, komunikować się z innych punktów końcowych, otrzymywanie i odpowiadanie na żądania i wywoływać funkcje platformy Azure, usługi Azure API Apps (aplikacje sieci Web), z własnymi interfejsami API zarządzane i opublikowanych przy użyciu usługi Azure API Management i zagnieżdżone logic apps, które mogą odbierać żądania. Możesz również użyć wbudowane akcje, które pomagają organizować kontrolowania przepływu pracy aplikacji logiki i także pracować z danymi.

* **Zarządzane łączniki**: Wdrożone i zarządzane przez firmę Microsoft, te łączniki udostępniają wyzwalacze i akcje do uzyskiwania dostępu do innych usług i systemów, takich jak usługi Office 365, Azure Blob Storage, SQL Server i Salesforce. Niektóre łączniki wymagają, należy najpierw utworzyć połączenia, które są zarządzane przez usługę Azure Logic Apps. Zarządzane łączniki są zorganizowane w tych grupach:

  |   |   |
  |---|---|
  | [**Zarządzane łączniki interfejsu API**](#managed-api-connectors) | Tworzenie aplikacji logiki, które korzystają z usług takich jak Azure Blob Storage, usługi Office 365, Dynamics, usługa Power BI, OneDrive, Salesforce, SharePoint Online i wiele innych. | 
  | [**Łączniki lokalne**](#on-premises-connectors) | Po zainstalowaniu i skonfigurować [lokalnej bramy danych][gateway-doc], pomagają one dostęp do usługi logic apps w środowisku lokalnym systemów, takich jak SQL Server, SharePoint Server, Oracle DB, udziały plików i innych łączników. | 
  | [**Łączniki konta integracji**](#integration-account-connectors) | Dostępne w przypadku tworzenia i opłaty za konto integracji, przekształcenie tych łączników i Walidacja danych XML, kodować i dekodować pliki proste i przetwarzania business-to-business (B2B) komunikatów AS2, EDIFACT i X12 protokołów. | 
  | [**Łączniki dla przedsiębiorstw**](#enterprise-connectors) | Zapewniają dostęp do systemów przedsiębiorstwa, takich jak SAP i IBM MQ za dodatkową opłatą. |
  ||| 

  Na przykład, jeśli używasz Microsoft BizTalk Server aplikacji usługi logic apps może nawiązać połączenie i komunikować się z programu BizTalk Server przy użyciu [łącznik programu BizTalk Server](#on-premises-connectors). 
  Można rozszerzyć lub wykonywać operacje przypominającej BizTalk w aplikacji logiki przy użyciu [łączniki konta integracji](#integration-account-connectors). 

> [!NOTE]
> Aby uzyskać pełną listę łączników i informacje referencyjne poszczególnych łączników, takich jak działania i wszelkie wyzwalacze, które są zdefiniowane w opisach platformy Swagger, oraz wszelkie limity można znaleźć pełną listę w obszarze [omówienie łączników](/connectors/). Aby uzyskać informacje o cenach, zobacz [Logic Apps, cennik](https://azure.microsoft.com/pricing/details/logic-apps/) i [model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Elementy wbudowane

Usługa Logic Apps oferuje wbudowane wyzwalaczy i akcji, aby można było utworzyć na podstawie harmonogramu przepływów pracy pomocy aplikacje logiki komunikować się z innych aplikacji i usług, kontroli przepływu pracy za pomocą aplikacji logiki oraz zarządzanie nimi i manipulowanie danymi. 

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona interfejsu API][schedule-icon]<br/>**harmonogramu**][recurrence-doc] | — Uruchamianie aplikacji logiki zgodnie z określonym harmonogramem, od podstawowych do złożonych cykli z **cyklu** wyzwalacza. <p>-Wstrzymać aplikacji logiki dla określonego czasu trwania z **opóźnienie** akcji. <p>-Wstrzymać aplikacji logiki do określonej daty i godziny przy użyciu **opóźnienie do** akcji. | [![Ikona interfejsu API][http-icon]<br/>**HTTP**][http-doc] | Komunikować się z dowolnego punktu końcowego za pośrednictwem protokołu HTTP przy użyciu wyzwalaczy i akcji w przypadku protokołu HTTP, HTTP + Swagger i protokołu HTTP + elementu Webhook. | 
| [![Ikona interfejsu API][http-request-icon]<br/>**żądania**][http-request-doc] | — Uzyskiwanie aplikacji logiki można wywołać za pomocą innych aplikacji lub usług, wyzwalacza usługi Event Grid zasobów zdarzeń lub wyzwalacza w odpowiedzi na alerty usługi Azure Security Center z **żądania** wyzwalacza. <p>— Wysłać odpowiedzi do aplikacji lub usługi za pomocą **odpowiedzi** akcji. | [![Ikona interfejsu API][batch-icon]<br/>**usługi Batch**][batch-doc] | -Przetwarzanie komunikatów w partie z **partii komunikatów** wyzwalacza. <p>— Wywołanie aplikacje logiki, które mają batch wyzwalaczy z **wysyłać komunikaty do przetwarzania zbiorczego** akcji. | 
| [![Ikona interfejsu API][azure-functions-icon]<br/>**usługi Azure Functions**][azure-functions-doc] | Wywołanie usługi Azure functions, uruchamiane niestandardowych fragmentach kodu (C# lub Node.js) z aplikacji logiki. | [![Ikona interfejsu API][azure-api-management-icon]</br>**usługi Azure API Management**][azure-api-management-doc] | Wywołaj wyzwalaczy i akcji zdefiniowanych przez własnych interfejsów API zarządzania i publikowania za pomocą usługi Azure API Management. | 
| [![Ikona interfejsu API][azure-app-services-icon]<br/>**usługi Azure App Services**][azure-app-services-doc] | Wywołanie usługi Azure API Apps lub aplikacji sieci Web hostowanych w usłudze Azure App Service. Wyzwalacze i akcje zdefiniowane przez te aplikacje są wyświetlane jak inne najwyższej jakości wyzwalaczy i akcji, gdy struktury Swagger jest dołączony. | [![Ikona interfejsu API][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Wywołuje inne aplikacje logic apps rozpoczynających się od wyzwalacza żądania. | 
||||| 

### <a name="control-workflow"></a>Kontroli przepływu pracy

Poniżej przedstawiono wbudowane akcje umożliwiające tworzenie struktury i kontrolowanie akcji w przepływie pracy aplikacji logiki:

|   |   |   |   | 
|---|---|---|---| 
| [![Wbudowaną ikonę][condition-icon]<br/>**warunku**][condition-doc] | Ocena warunku i wykonywania różnych działań na ich podstawie warunek ma wartość PRAWDA lub FAŁSZ. | [![Wbudowaną ikonę][for-each-icon]</br>**For each**][for-each-doc] | Wykonaj te same akcje dla każdego elementu w tablicy. | 
| [![Wbudowaną ikonę][scope-icon]<br/>**zakresu**][scope-doc] | Grupy akcji do *zakresy*, który uzyskać ich stanu po akcje w zakresie zakończenie działania. | [![Wbudowaną ikonę][switch-icon]</br>**przełącznika**][switch-doc] | Grupy akcji do *przypadków*, przypisane unikatowe wartości, z wyjątkiem przypadek domyślny. Uruchom tylko tego przypadku, w których przypisaną wartością odpowiada wynikiem wyrażenia, obiekt lub token. Jeśli istnieje żadnych dopasowań, Uruchom przypadek domyślny. | 
| [![Wbudowaną ikonę][terminate-icon]<br/>**zakończenia**][terminate-doc] | Zatrzymaj aktywnie uruchomionej przepływu pracy aplikacji logiki. | [![Wbudowaną ikonę][until-icon]<br/>**do momentu**][until-doc] | Powtórz czynności, aż określony warunek ma wartość true lub niektóre stan został zmieniony. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Zarządzanie i manipulowanie danymi

Poniżej przedstawiono wbudowane akcje umożliwiające pracę z danych wyjściowych danych i ich formatów:  

|   |   | 
|---|---| 
| [![Wbudowaną ikonę][data-operations-icon]<br/>**operacje na danych**][data-operations-doc] | Wykonaj operacje przy użyciu danych: <p>- **Redagowanie**: Utwórz pojedynczy dane wyjściowe na podstawie wielu danych wejściowych z różnymi typami. <br>- **Utwórz tabelę CSV**: Utwórz tabelę przecinkami wartości rozdzielanych przecinkami (CSV) z tablicy obiektów JSON. <br>- **Utwórz tabelę HTML**: Utwórz tabelę HTML z tablicy obiektów JSON. <br>- **Filtruj tablicę**: Utwórz tablicę z elementów w innej tablicy, spełniających podane kryteria. <br>- **Dołącz do**: Tworzenie ciągu z wszystkich elementów w tablicy, a następnie oddzielić te elementy przy użyciu określonego ogranicznika. <br>- **Przeanalizuj dane JSON**: Tworzenie tokenów przyjazny dla użytkownika z właściwości i ich wartości w zawartości JSON, aby można było używać tych właściwości w przepływie pracy. <br>- **Wybierz**: Utwórz tablicę z obiektami JSON poprzez przekształcenie elementów lub wartości w innej tablicy i mapowanie te elementy do określonej właściwości. | 
| ![Ikona wbudowane][date-time-icon]<br/>**Data i godzina** | Wykonaj czynności z sygnaturami czasowymi: <p>- **Dodaj do czasu**: Dodaj określoną liczbę jednostek do sygnatury czasowej. <br>- **Konwertuj strefę czasową**: Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. <br>- **Bieżący czas**: Zwraca bieżącą sygnaturę czasową jako ciąg. <br>- **Pobierz czas w przyszłości**: Zwraca bieżącą sygnaturę czasową, a także jednostek w określonym czasie. <br>- **Pobierz czas w przeszłości**: Zwraca bieżącą sygnaturę czasową minus jednostek w określonym czasie. <br>- **Odejmij od czasu**: Odjęcie liczby jednostek czasu z sygnatury czasowej. |
| [![Wbudowaną ikonę][variables-icon]<br/>**zmiennych**][variables-doc] | Wykonaj operacje za pomocą zmiennych: <p>- **Dołącz do zmiennej tablicowej**: Wstaw wartość jako ostatni element w tablicy przechowywane za pomocą zmiennej. <br>- **Dołącz do zmiennej ciągu**: Wstaw wartość jako ostatni znak w ciągu przechowywane za pomocą zmiennej. <br>- **Zmniejsz zmienną**: Zmniejsz zmienną o stałej wartości. <br>- **Zwiększ wartość zmiennej**: Zwiększ zmiennej przez wartość stałą. <br>- **Inicjowanie zmiennej**: Utwórz zmienną i zadeklarować jego typu danych i wartość początkową. <br>- **Ustaw zmienną**: Przypisz inną wartość do istniejącej zmiennej. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Zarządzane łączniki interfejsu API

Poniżej przedstawiono więcej popularnych łączników do automatyzowania zadań, procesy i przepływy pracy za pomocą tych usług lub systemy:

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona interfejsu API][azure-service-bus-icon]<br/>**usługi Azure Service Bus**][azure-service-bus-doc] | Zarządzaj wiadomości asynchronicznych, sesji i subskrypcje tematu przy użyciu łącznika najczęściej używanych w usłudze Logic Apps. | [![Ikona interfejsu API][sql-server-icon]<br/>**programu SQL Server**][sql-server-doc] | Połączenie z SQL Server w środowisku lokalnym lub usługi Azure SQL Database w chmurze Zarządzanie rekordami, uruchamianie procedur składowanych lub wykonywania zapytań. | 
| [![Ikona interfejsu API][office-365-outlook-icon]<br/>**usługi Office 365<br/>programu Outlook**][office-365-outlook-doc] | Nawiąż połączenie z kontem poczty e-mail usługi Office 365, dzięki czemu można tworzyć i zarządzać nimi wiadomości e-mail, zadania, zdarzenia kalendarza i spotkania, kontakty, żądania itd. | [![Ikona interfejsu API][azure-blob-storage-icon]<br/>**obiektów Blob platformy Azure<br/>magazynu**][azure-blob-storage-doc] | Nawiąż połączenie z kontem magazynu, dzięki czemu można tworzyć i zarządzać zawartość obiektu blob. | 
| [![Ikona interfejsu API][sftp-icon]<br/>**SFTP**][sftp-doc] | Łączenie z serwerami SFTP, do których masz dostęp z Internetu, aby można było pracować z plikami i folderami. | [![Ikona interfejsu API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Możesz określić pliki, załączniki, foldery i połączenie do usługi SharePoint Online. | 
| [![Ikona interfejsu API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Łączenie się ze swoim kontem Dynamics 365, dzięki czemu można tworzyć i zarządzać rekordami, elementy i inne. | [![Ikona interfejsu API][ftp-icon]<br/>**FTP**][ftp-doc] | Łączenie z serwerami FTP, w której będziesz mieć dostęp z Internetu, aby można było pracować z plikami i folderami. | 
| [![Ikona interfejsu API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Nawiąż połączenie z kontem usługi Salesforce, dzięki czemu można tworzyć i zarządzać elementy, takie jak rekordy, zadania, obiektów i. | [![Ikona interfejsu API][twitter-icon]<br/>**w usłudze Twitter**][twitter-doc] | Nawiąż połączenie z kontem usługi Twitter, więc można zarządzać tweetów, obserwatorów, osi czasu i nie tylko. Zapisz swoje tweety SQL, program Excel i programu SharePoint. | 
| [![Ikona interfejsu API][azure-event-hubs-icon]<br/>**usługi Azure Event Hubs**][azure-event-hubs-doc] | Używanie i publikowania zdarzeń za pośrednictwem Centrum zdarzeń. Na przykład pobierać dane wyjściowe z aplikacji logiki z usługą Event Hubs, a następnie wysyłać dane wyjściowe do dostawcy analiz w czasie rzeczywistym. | [![Ikona interfejsu API][azure-event-grid-icon]<br/>**usługi Azure Event**</br>**siatki**][azure-event-grid-doc] | Monitorowanie zdarzeń publikowanych przez usługę Event Grid, na przykład, gdy zmienią się zasoby platformy Azure lub innych firm. | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Łączniki lokalne 

Poniżej przedstawiono niektóre powszechnie używane łączniki, które zapewniają dostęp do danych i zasobów w systemach lokalnych. Przed utworzeniem połączenia do systemu lokalnego, musisz najpierw [pobrać, zainstalować i skonfigurować lokalną bramę danych][gateway-doc]. Ta brama zapewnia bezpieczny kanał komunikacyjny bez konieczności konfigurowania infrastruktury sieciowej to konieczne. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ikona interfejsu API][biztalk-server-icon]<br/>**BizTalk**</br> **Serwer** | [![Ikona interfejsu API][file-system-icon]<br/>**pliku</br> systemu**][file-system-doc] | [![Ikona interfejsu API][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![Ikona interfejsu API][ibm-informix-icon]<br/>**IBM** </br> **Informix**][ibm-informix-doc] | ![Ikona interfejsu API][mysql-icon]<br/>**MySQL** | 
| [![Ikona interfejsu API][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![Ikona interfejsu API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ikona interfejsu API][sharepoint-server-icon]<br/>**SharePoint</br> serwera**][sharepoint-server-doc] | [![Ikona interfejsu API][sql-server-icon]<br/>**SQL</br> serwera**][sql-server-doc] | ![Ikona interfejsu API][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Łączniki konta integracji 

Poniżej przedstawiono łączniki do tworzenia rozwiązań biznesowych (B2B) za pomocą aplikacji logiki, podczas tworzenia i płacić za [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), który jest dostępny za pośrednictwem pakietu integracji przedsiębiorstw (EIP) na platformie Azure. Przy użyciu tego konta można tworzyć i przechowywanie artefaktów B2B, takich jak handlowymi partnerów, umów, mapy, schematów, certyfikaty i tak dalej. Aby użyć tych artefaktów, należy skojarzyć aplikacji logiki przy użyciu konta integracji. Jeśli używasz obecnie programu BizTalk Server, te łączniki mogą wydawać się dobrze znanych już.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona interfejsu API][as2-icon]<br/>**AS2</br> dekodowanie**][as2-decode-doc] | [![Ikona interfejsu API][as2-icon]<br/>**AS2</br> kodowania**][as2-encode-doc] | [![Ikona interfejsu API][edifact-icon]<br/>**EDIFACT</br> dekodowanie**][edifact-decode-doc] | [![Ikona interfejsu API][edifact-icon]<br/>**EDIFACT</br> kodowania**][edifact-encode-doc] | 
| [![Ikona interfejsu API][flat-file-decode-icon]<br/>**pliku prostego</br> dekodowanie**][flat-file-decode-doc] | [![Ikona interfejsu API][flat-file-encode-icon]<br/>**pliku prostego</br> kodowania**][flat-file-encode-doc] | [![Ikona interfejsu API][integration-account-icon]<br/>**integracji<br/>konta**][integration-account-doc] | [![Ikona interfejsu API][liquid-icon]<br/>**Liquid**</br>**przekształcenia**][json-liquid-transform-doc] | 
| [![Ikona interfejsu API][x12-icon]<br/>**X12</br> dekodowanie**][x12-decode-doc] | [![Ikona interfejsu API][x12-icon]<br/>**X12</br> kodowania**][x12-encode-doc] | [![Ikona interfejsu API][xml-transform-icon]<br/>**XML**</br>**przekształcenia**][xml-transform-doc] | [![Ikona interfejsu API][xml-validate-icon]<br/>**XML <br/>sprawdzania poprawności**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Łączniki dla przedsiębiorstw

Aplikacje logiki mieli dostęp do systemów dla przedsiębiorstw, takimi jak SAP i IBM MQ:

|   |   | 
|---|---| 
| [![Ikona interfejsu API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Ikona interfejsu API][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Wyzwalacze i akcje — więcej informacji

Łączniki mogą zapewnić *wyzwalaczy*, *akcje*, lub obu. A *wyzwalacza* jest pierwszym krokiem we wszystkich aplikacjach logiki, zwykle określenie zdarzenia, które uruchamia wyzwalacz i uruchamiania aplikacji logiki. Na przykład łącznik FTP ma wyzwalacz, który uruchamia aplikację logiki "po dodaniu lub zmodyfikowaniu pliku". Niektórych wyzwalaczy regularne sprawdzanie określonego zdarzenia lub dane, a następnie uruchamiają się po wykryciu określonego zdarzenia lub dane. Inne wyzwalacze czekać, ale fire natychmiast po się dzieje określonego zdarzenia lub gdy nowe dane są dostępne. Wyzwalacze również przekazać wszystkie dane wymagane do aplikacji logiki. Twoja aplikacja logiki może odczytywać i używać tych danych w całym przepływie pracy.
Na przykład łącznik Twitter zawiera wyzwalacz, "Kiedy nowego tweetu", który przekazuje tweetu jego zawartość w przepływie pracy aplikacji logiki. 

Po aktywowaniu wyzwalacza usługi Azure Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania *akcje* w przepływie pracy aplikacji logiki. Akcje są kroki, które należy wykonać wyzwalacza i wykonywania zadań w przepływie pracy aplikacji logiki. Na przykład możesz utworzyć aplikację logiki, która pobiera dane klienta z bazy danych SQL i przetwarzania tych danych w kolejnych akcjach. 

Poniżej przedstawiono ogólne rodzaje wyzwalaczy udostępnianych przez usługi Azure Logic Apps:

* *Wyzwalacz cykliczny*: Ten wyzwalacz jest uruchamiany zgodnie z harmonogramem i nie jest ściśle związana z określonej usługi lub systemu.

* *Wyzwalacz sondowania*: Ten wyzwalacz jest regularnie sonduje określonej usługi lub systemu, na podstawie określonego harmonogramu, sprawdzania pod kątem nowych danych lub tego, czy wystąpienia określonego zdarzenia. Jeśli nowe dane są dostępne lub określone zdarzenie wystąpiło, wyzwalacz tworzy i uruchamia nowe wystąpienie aplikacji logiki, które mogą teraz używać danych, który jest przekazywany jako dane wejściowe.

* *Wypychanie wyzwalacza*: Ten wyzwalacz czeka i nasłuchuje nowych danych lub zdarzeń. Gdy nowe dane są dostępne, lub gdy zdarzenie, wyzwalacz tworzy i uruchamia nowe wystąpienie aplikacji logiki, które mogą teraz używać danych, który jest przekazywany jako dane wejściowe.

<a name="custom"></a>

## <a name="connector-configuration"></a>Konfiguracja łącznika

Wyzwalacze i akcje każdy łącznik Podaj własne właściwości do skonfigurowania. Również wymagać wiele łączników, należy najpierw utworzyć *połączenia* do docelowej usługi lub systemu i podaj poświadczenia uwierzytelniania lub innych szczegółów konfiguracji, zanim użyjesz wyzwalacza lub akcji w aplikacji logiki. Na przykład należy autoryzować połączenie z kontem usługi Twitter do uzyskiwania dostępu do danych lub w Twoim imieniu. 

Dla łączników, które używają protokołu OAuth tworzenia połączenia oznacza, że logowanie się do usługi, takie jak usługi Office 365, Salesforce lub GitHub, gdzie token dostępu jest zaszyfrowany i bezpiecznie przechowywane w sklepie systemu Azure dla wpisu tajnego. Innych łączników, takich jak FTP i SQL, wymaga połączenia, który ma szczegóły konfiguracji, takie jak adres serwera, nazwę użytkownika i hasło. Szczegóły konfiguracji tego połączenia są również są szyfrowane i bezpiecznie przechowywane. 

Połączenia można uzyskać dostęp do Usługa docelowa lub system tak długo, jak umożliwia tej usługi lub systemu. Dla usług korzystających z połączeń protokołu OAuth usługi Azure Active Directory (AD), takie jak usługi Office 365 i Dynamics Azure Logic Apps odświeża tokenów dostępu przez czas nieokreślony. Inne usługi może stanowić limity na jak długo Azure Logic Apps można użyć tokenu bez odświeżania. Ogólnie rzecz biorąc niektóre akcje unieważnienie wszystkich tokenów dostępu, takich jak zmienić hasło.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Niestandardowe interfejsy API i łączników

Aby wywoływać interfejsy API, który jest uruchomiony niestandardowy kod lub które nie są dostępne jako łączniki, można rozszerzyć platformę usługi Logic Apps przez [tworząc niestandardowe aplikacje interfejsu API](../logic-apps/logic-apps-create-api-app.md). Możesz również [Tworzenie łączników niestandardowych](../logic-apps/custom-connector-overview.md) dla *wszelkie* opartego na protokole SOAP API, który udostępnienia tych interfejsów API w dowolnej aplikacji logiki w ramach subskrypcji platformy Azure lub REST.
Aby udostępnić funkcję niestandardowe aplikacje interfejsu API i łączniki dla każdego, kto do użytku na platformie Azure, możesz [przesyłanie łączników do certyfikacji firmy Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby przesłać lub głosować na pomysły dotyczące usługi Azure Logic Apps i łączników, odwiedź stronę [witrynie opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Znajdź [pełną listę łączników](https://docs.microsoft.com/connectors)
* [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Tworzenie łączników niestandardowych dla usługi logic apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Tworzenie niestandardowych interfejsów API dla aplikacji logiki](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Łączenie z lokalnymi źródłami danych z aplikacji logiki za pomocą bramy danych lokalnych"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integracja aplikacji logiki z usługą Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Tworzenie wystąpienia usługi Azure API Management do zarządzania i publikowanie interfejsów API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integracja aplikacji logiki z usługą App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Wysyłanie komunikatów z tematów i kolejek usługi Service Bus oraz odbieranie komunikatów z subskrypcji i kolejek usługi Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Przetwarzanie komunikatów w grupach lub jako partie"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Ocena warunku i wykonywania różnych działań na ich podstawie warunek ma wartość PRAWDA lub FAŁSZ"
[delay-doc]: ./connectors-native-delay.md "Wykonywanie akcji z opóźnieniem"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Wykonaj te same akcje dla każdego elementu w tablicy"
[http-doc]: ./connectors-native-http.md "Nawiązywanie połączeń HTTP za pomocą łącznika HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Dodawanie akcji dla żądań i odpowiedzi HTTP w aplikacjach logiki"
[http-response-doc]: ./connectors-native-reqres.md "Dodawanie akcji dla żądań i odpowiedzi HTTP w aplikacjach logiki"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Nawiązywanie połączeń HTTP za pomocą łącznika protokołu HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Dodaj wyzwalacze i Akcje elementu webhook protokołu HTTP do aplikacji logiki"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integracja aplikacji logiki z zagnieżdżonymi przepływami pracy"
[query-doc]: ./connectors-native-query.md "Wybieranie i filtrowanie tablic za pomocą akcji zapytania"
[recurrence-doc]:  ./connectors-native-recurrence.md "Wyzwalanie akcji cyklicznych dla aplikacji logiki"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizuj akcje do grup, które otrzymanie własne stan akcji w grupie zakończenie działania" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizowanie akcji w przypadkach, które są przypisane unikatowe wartości. Uruchom przypadek, którego wartość odpowiada wynikiem wyrażenia, obiekt lub token. Jeśli istnieje żadnych dopasowań, Uruchom przypadek domyślny"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zatrzymać lub anulować aktywnie uruchomiony przepływ pracy aplikacji logiki"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Powtarzanie operacji, aż określony warunek ma wartość true lub niektóre stan został zmieniony"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Wykonywanie operacji danych, takich jak filtrowanie tablic lub tworzenia tabeli CSV i HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Wykonywanie operacji za pomocą zmiennych, takich jak inicjowanie, zestaw, inkrementacja, dekrementacja i Dołącz do zmiennej ciągu lub tablicy"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Zarządzanie plikami w kontenerze obiektów blob za pomocą łącznika usługi Azure Blob Storage"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Monitorowanie zdarzeń publikowanych przez usługę Event Grid, na przykład, gdy zmienią się zasoby platformy Azure lub innych firm"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Łączenie z usługą Azure Event Hubs. Odbieranie i wysyłanie zdarzeń między aplikacjami logiki i usługą Event Hubs"
[box-doc]: ./connectors-create-api-box.md "Łączenie z usługą Box. Przekazywanie, pobieranie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Łączenie z usługą Dropbox. Przekazywanie, pobieranie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Łączenie z usługą Dynamics CRM Online w celu korzystania z danych usługi CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Łączenie z serwisem Facebook. Publikowanie na osi czasu, uzyskiwanie kanału informacyjnego strony i inne funkcje"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Łączenie z lokalnym systemem plików"
[ftp-doc]: ./connectors-create-api-ftp.md "Łączenie z serwerem FTP/FTPS i wykonywanie zadań związanych z protokołem FTP, takich jak np. przekazywanie, pobieranie i usuwanie plików"
[github-doc]: ./connectors-create-api-github.md "Łączenie z witryną GitHub i śledzenie problemów"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Nawiązuje połączenie z usługą Kalendarz Google i może zarządzać kalendarzem."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Łączenie z Dyskiem Google i korzystanie z danych"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Łączenie z arkuszami Google, co pozwala modyfikować arkusze"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Łączenie z Zadaniami Google i zarządzanie zadaniami"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Łączenie z bazą danych IBM DB2 w chmurze lub lokalnie. Aktualizowanie wiersza, pobieranie tabeli oraz inne funkcje"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Łączenie z usługą Informix w chmurze lub lokalnie. Odczytywanie wiersza, wyświetlanie listy tabel i inne funkcje"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Połączenia z programem IBM MQ w środowisku lokalnym lub na platformie Azure w celu wysyłania i odbierania komunikatów"
[instagram-doc]: ./connectors-create-api-instagram.md "Łączenie z usługą Instagram. Wyzwalanie zdarzeń lub reagowanie na nie"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Łączenie z kontem MailChimp. Automatyzowanie obsługi wiadomości e-mail i zarządzanie nimi"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Łączenie z usługą Mandrill w celu komunikowania się"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Łączenie z usługą Microsoft Translator. Tłumaczenie tekstu, wykrywanie języków i inne funkcje" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Łączenie z kontem usługi Office 365. Wysyłanie i odbieranie wiadomości e-mail, zarządzanie kalendarzem i kontaktami oraz inne funkcje"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Pobieranie informacji o filmach wideo, list i kanałów wideo oraz adresów URL odtwarzania dla filmów wideo usługi Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Łączenie z osobistą usługą Microsoft OneDrive. Przekazywanie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Łączenie z firmową usługą Microsoft OneDrive. Przekazywanie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Łączenie z bazą danych Oracle w celu m.in. dodawania, wstawiania i usuwania wierszy"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Łączenie ze skrzynką pocztową programu Outlook. Zarządzanie pocztą e-mail, kalendarzami i kontaktami oraz inne funkcje"
[project-online-doc]: ./connectors-create-api-projectonline.md "Łączenie z usługą Microsoft Project Online. Zarządzanie projektami, zadaniami i zasobami oraz inne funkcje"
[rss-doc]: ./connectors-create-api-rss.md "Publikowanie i pobieranie elementów kanału informacyjnego, wyzwalanie operacji po opublikowaniu nowego elementu w kanale informacyjnym RSS."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Łączenie z kontem usługi Salesforce. Zarządzanie kontami, potencjalnymi klientami i możliwościami sprzedaży oraz inne funkcje"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Łączenie z lokalnym systemem SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Łączenie z usługą SendGrid. Wyślij wiadomość e-mail i zarządzanie listami adresatów"
[sftp-doc]: ./connectors-create-api-sftp.md "Łączenie z kontem SFTP. Przekazywanie, pobieranie i usuwanie plików oraz inne funkcje"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Łączenie z programem SharePoint na serwerze lokalnym. Zarządzanie dokumentami i elementami list oraz inne funkcje"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Łączenie z usługą SharePoint Online. Zarządzanie dokumentami i elementami list oraz inne funkcje"
[slack-doc]: ./connectors-create-api-slack.md "Łączenie z usługą Slack i publikowanie wiadomości w kanałach Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Połącz z serwerem SMTP i wysyłanie wiadomości e-mail z załącznikami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Łączenie z usługą SparkPost w celu komunikowania się"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Podłącz do usługi Azure SQL Database lub SQL Server. Tworzenie, aktualizowanie, pobieranie i usuwanie wpisów w tabeli bazy danych SQL."
[trello-doc]: ./connectors-create-api-trello.md "Łączenie z usługą Trello. Zarządzanie projektami i organizowanie dowolnych rzeczy z dowolnymi osobami"
[twilio-doc]: ./connectors-create-api-twilio.md "Łączenie z usługą Twilio. Wysyłanie i pobieranie wiadomości, pobieranie dostępnych numerów, zarządzanie przychodzącymi numerami telefonów i inne funkcje"
[twitter-doc]: ./connectors-create-api-twitter.md "Łączenie z serwisem Twitter. Pobieranie osi czasu, publikowanie tweetów i inne funkcje"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Łączenie z usługą Wunderlist. Zarządzanie zadaniami i listami zadań do wykonania, synchronizowanie danych oraz inne funkcje"
[yammer-doc]: ./connectors-create-api-yammer.md "Łączenie z usługą Yammer. Publikowanie wiadomości, pobieranie nowych wiadomości i inne funkcje"
[youtube-doc]: ./connectors-create-api-youtube.md "Łączenie z serwisem YouTube. Zarządzanie filmami wideo i kanałami"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Więcej informacji na temat integracji dla przedsiębiorstw — AS2."
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Więcej informacji na temat integracji dla przedsiębiorstw — dekodowanie komunikatów AS2."
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Więcej informacji na temat integracji dla przedsiębiorstw — kodowanie komunikatów AS2."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Więcej informacji na temat integracji dla przedsiębiorstw — dekodowanie komunikatów EDIFACT."
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Więcej informacji na temat integracji dla przedsiębiorstw — kodowanie komunikatów EDIFACT."
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Więcej informacji na temat integracji dla przedsiębiorstw — plik prosty."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Więcej informacji na temat integracji dla przedsiębiorstw — plik prosty."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Wyszukiwanie schematów, map partnerów i innych elementów na koncie integracji"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Informacje na temat przekształceń JSON i łącznika Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Więcej informacji na temat integracji dla przedsiębiorstw — X12."
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Więcej informacji na temat integracji dla przedsiębiorstw — dekodowanie komunikatów X12."
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Więcej informacji na temat integracji dla przedsiębiorstw — kodowanie komunikatów X12."
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Więcej informacji na temat integracji dla przedsiębiorstw — transformacje."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Więcej informacji na temat integracji dla przedsiębiorstw — walidacja XML."

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
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
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
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
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
[sftp-icon]: ./media/apis-list/sftp.png
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
[wunderlist-icon]: ./media/apis-list/wunderlist.png
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
