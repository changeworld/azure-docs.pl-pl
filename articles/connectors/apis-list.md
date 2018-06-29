---
title: Łączniki dla usługi Azure Logic Apps | Microsoft Docs
description: Automatyczne przepływy pracy dzięki wbudowanej, zarządzanego interfejsu API, lokalnych, konta integracji i łączniki przedsiębiorstwa dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 06/29/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bb3e2ce29037372395aa0b30e9f76f3e712667d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096615"
---
# <a name="connectors-for-azure-logic-apps"></a>Łączniki dla usługi Azure Logic Apps

Łączniki odtworzyć integralną częścią tworzenia automatycznych przepływów pracy z usługą Azure Logic Apps. Za pomocą łączników w aplikacjach logiki, możesz rozszerzyć możliwości dla lokalnej i aplikacji, aby wykonać zadania z danymi, które możesz utworzyć i już w chmurze. Podczas Logic Apps oferuje ~ 200 + łączników, w tym artykule opisano popularnych i najczęściej używanych łączniki, które są pomyślnie używane tysiące aplikacji i miliony wykonaniami do przetwarzania danych i informacji.
Łączniki są dostępne jako built-ins lub zarządzanych łączników. 

* [**Built-ins**](#built-ins): te akcje wbudowane i tworzenie aplikacji logiki, które można uruchamiać na niestandardowe harmonogramy pomocy wyzwalaczy komunikować się z innych punktów końcowych, odbierania i odpowiadać na żądania i Wywołaj Azure funkcje i aplikacje interfejsu API Azure (aplikacje sieci Web), własne Interfejsy API zarządzane i opublikowane z usługi Azure API Management i zagnieżdżone logikę aplikacji, które mogą odbierać żądań. Można również użyć wbudowanych akcje, które ułatwiają organizowanie i kontroli przepływu pracy aplikacji logiki, a także pracować z danymi.

* **Zarządzane łączniki**: tych łączników udostępnia wyzwalacze i akcje do uzyskiwania dostępu do innych usług i systemów. Niektóre łączniki wymagają utworzenia połączenia, które są zarządzane przez usługi Azure Logic Apps. Zarządzanych łączników są zorganizowane w tych grupach:

  |   |   |
  |---|---|
  | [**Zarządzanych łączników interfejsu API**](#managed-api-connectors) | Tworzenie aplikacji logiki, które korzystają z usług takich jak magazyn obiektów Blob Azure, Office 365, Dynamics, usługi Power BI, OneDrive, Salesforce, usługi SharePoint Online i wiele innych. | 
  | [**Łączniki lokalnej**](#on-premises-connectors) | Po zainstalowaniu i skonfigurować [bramy danych lokalnych][gateway-doc], te łączniki pomocy masz dostęp do aplikacji logiki lokalnych systemów, takich jak SQL Server, SharePoint Server, bazy danych Oracle, udziałów plików i inne. | 
  | [**Łączniki integracji dla konta**](#integration-account-connectors) | Dostępne podczas tworzenia i opłacać konta integracji transformacji tych łączników i zweryfikować XML, kodowania i dekodowania plików prostych i przetworzyć business-to-business (B2B) komunikatów z AS2, EDIFACT i X12 protokołów. | 
  | [**Łączniki dla przedsiębiorstw**](#enterprise-connectors) | Zapewnia dostęp do systemów przedsiębiorstwa, takich jak SAP i IBM MQ dla dodatkowych kosztów. |
  ||| 

  Na przykład, jeśli używasz Microsoft BizTalk Server aplikacje logiki można nawiązać połączenie i komunikują się z serwerem BizTalk przy użyciu [łącznika BizTalk Server](#on-premises-connectors). 
  Możesz rozszerzyć lub wykonywać operacje podobne BizTalk w aplikacjach logiki za pomocą [łączniki integracji dla konta](#integration-account-connectors). 

Aby uzyskać informacje techniczne dotyczące poszczególnych łączników wyzwalaczy i akcji, które są definiowane przez opis programu Swagger, a także żadnych limitów, zobacz [szczegóły łącznika](/connectors/). Aby uzyskać informacje o kosztach, zobacz [Logic Apps szczegóły cennika](https://azure.microsoft.com/pricing/details/logic-apps/) i [Logic Apps model cenowy](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Wbudowane

Logic Apps oferuje wbudowane wyzwalacze i akcje dzięki czemu można tworzyć oparte na harmonogramie przepływy pracy pomocy aplikacje logiki komunikować się z innych aplikacji i usług, kontroli przepływu pracy za pośrednictwem aplikacji logiki, zarządzanie i manipulować danymi. 

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona interfejsu API][schedule-icon]<br/>**harmonogramu**][recurrence-doc] | -Uruchom aplikację logiki zgodnie z zaplanowanym harmonogramem, od podstawowego do powtórzenia złożonych z **cyklu** wyzwalacza. <p>-Wstrzymania aplikacji logiki dla określonego czasu trwania z **opóźnienie** akcji. <p>-Wstrzymania aplikacji logiki do określonej daty i godziny z **opóźnienie do** akcji. | [![Ikona interfejsu API][http-icon]<br/>**HTTP**][http-doc] | Komunikować się z dowolnego punktu końcowego za pośrednictwem protokołu HTTP z wyzwalacze i akcje dla protokołu HTTP, HTTP + Swagger i HTTP + elementu Webhook. | 
| [![Ikona interfejsu API][http-request-icon]<br/>**żądania**][http-request-doc] | -Sprawdź aplikację logiki można wywołać z innych aplikacji lub usług, wyzwalacz zdarzenia siatki zasobów zdarzeń lub wyzwalacza w odpowiedzi na alerty Centrum zabezpieczeń Azure **żądania** wyzwalacza. <p>-Wysyłania odpowiedzi do aplikacji lub usługi z **odpowiedzi** akcji. | [![Ikona interfejsu API][batch-icon]<br/>**partii**][batch-doc] | -Przetwarzanie wiadomości w partiach o **partii komunikatów** wyzwalacza. <p>-Wywołanie logikę aplikacji, które mają partii wyzwalaczy z **wysyłanie komunikatów do przetwarzania wsadowego** akcji. | 
| [![Ikona interfejsu API][azure-functions-icon]<br/>**usługi Azure Functions**][azure-functions-doc] | Wywołanie funkcji platformy Azure, które uruchamiania wstawki kodu niestandardowego (C# lub Node.js) z aplikacji logiki. | [![Ikona interfejsu API][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Wywołanie wyzwalacze i akcje, które określają własne interfejsów API, które można publikować ani zarządzać nimi z usługą Azure API Management. | 
| [![Ikona interfejsu API][azure-app-services-icon]<br/>**usługi aplikacji Azure**][azure-app-services-doc] | Wywołanie aplikacji interfejsu API platformy Azure lub aplikacji sieci Web hostowanych w usłudze Azure App Service. Wyzwalacze i akcje zdefiniowane przez te aplikacje są wyświetlane jak inne najwyższej jakości wyzwalacze i akcje po struktury Swagger jest dołączony. | [![Ikona interfejsu API][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Wywołanie innych aplikacji logiki, rozpoczynających się od wyzwalacza żądania. | 
||||| 

### <a name="control-workflow"></a>Przepływu sterowania

Poniżej przedstawiono akcje wbudowane do definiowania i kontrolowania działań w przepływie pracy aplikacji logiki:

|   |   |   |   | 
|---|---|---|---| 
| [![Wbudowaną ikonę][condition-icon]<br/>**warunku**][condition-doc] | Ocena warunku i wykonywania różnych działań na podstawie warunku jest PRAWDA lub FAŁSZ. | [![Wbudowaną ikonę][for-each-icon]</br>**dla każdego**][for-each-doc] | Wykonaj te same akcje na każdy element w tablicy. | 
| [![Wbudowaną ikonę][scope-icon]<br/>**zakresu**][scope-doc] | Grupa działań w *zakresy*, który pobrania ich stanu po akcje w zakresie zakończenie działania. | [![Wbudowaną ikonę][switch-icon]</br>**przełącznika**][switch-doc] | Grupa działań w *przypadków*, przypisane unikatowe wartości z wyjątkiem od przypadku domyślnego. Uruchom tylko tego przypadku którego przypisaną wartość odpowiada wynik wyrażenia, obiektów lub token. Jeśli nie istnieją żadne dopasowań, uruchom na przypadek domyślny. | 
| [![Wbudowaną ikonę][terminate-icon]<br/>**przerwanie**][terminate-doc] | Zatrzymaj aktywnie uruchomiony przepływ pracy aplikacji logiki. | [![Wbudowaną ikonę][until-icon]<br/>**do czasu**][until-doc] | Powtórz akcje określony warunek jest spełniony lub niektóre stan został zmieniony. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Zarządzanie lub manipulować danymi

Poniżej przedstawiono akcje wbudowane do pracy z danych wyjściowych i ich formaty:  

|   |   | 
|---|---| 
| ![Ikona wbudowane][data-operations-icon]<br/>**Operacje na danych** | Wykonaj operacje przy użyciu danych: <p>- **Redagowanie**: tworzenie pojedynczego wyjścia z wielu danych wejściowych z różnych typów. <br>- **Utwórz tabelę CSV**: Tworzenie tabeli przecinkami wartości rozdzielanych przecinkami (CSV) z tablicy z obiektami JSON. <br>- **Tworzenie tabeli HTML**: Tworzenie tabeli HTML z tablicy z obiektami JSON. <br>- **Macierz filtru**: tworzenie tablicy z elementów w innej tablicy, spełniających podane kryteria. <br>- **Dołącz**: Tworzenie ciągu z wszystkich elementów w tablicy i tych elementów należy oddzielić określonego ogranicznika. <br>- **Przeanalizować składni JSON**: tworzenie tokenów przyjazną dla użytkownika z właściwości i ich wartości w formacie JSON zawartości, aby te właściwości można używać w przepływie pracy. <br>- **Wybierz**: utworzyć tablicy z obiektami JSON Przekształcanie elementów lub wartości w innej tablicy i mapowanie te elementy do określonej właściwości. | 
| ![Ikona wbudowane][date-time-icon]<br/>**Data i godzina** | Wykonaj operacje z sygnaturami czasowymi: <p>- **Dodaj do czasu**: Dodaj określoną liczbę jednostek do sygnatury czasowej. <br>- **Konwertuj strefy czasowej**: przekonwertować sygnatury czasowej z źródłową strefą czasową zgodnie ze strefą czasową docelowej. <br>- **Bieżący czas**: zwraca bieżącą sygnaturę czasową jako ciąg. <br>- **Pobierz przyszłości**: zwraca bieżącą sygnaturę czasową plus jednostki określonego czasu. <br>- **Pobierz minął czas**: zwraca bieżącą sygnaturę czasową minus jednostki określonego czasu. <br>- **Odjąć od czasu**: odejmowanie liczby jednostek czasu z sygnatury czasowej. |
| [![Wbudowaną ikonę][variables-icon]<br/>**zmiennych**][variables-doc] | Wykonaj operacje przy użyciu zmiennych: <p>- **Dołącz do tablicy zmiennej**: Wstaw wartość jako ostatni element w tablicy przechowywane za pomocą zmiennej. <br>- **Dołącz do zmiennej ciągu**: Wstaw wartość jako ostatni znak w ciągu przechowywane za pomocą zmiennej. <br>- **Zmienna dekrementacja**: zmniejszanie zmiennej o stałej wartości. <br>- **Zmienna przyrostu**: Zwiększ zmiennej o stałej wartości. <br>- **Inicjowanie zmiennej**: Utwórz zmienną i zadeklarować jego typu danych i wartości początkowej. <br>- **Ustaw zmienną**: przypisać inną wartość istniejącej zmiennej. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Zarządzanych łączników interfejsu API

Poniżej przedstawiono bardziej popularne łączniki do automatyzacji zadań, procesów i przepływy pracy z tych usług lub systemami:

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona interfejsu API][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Zarządzanie asynchroniczne wiadomości, sesje i subskrypcje tematu z łącznikiem najczęściej używanych w aplikacjach logiki. | [![Ikona interfejsu API][sql-server-icon]<br/>**programu SQL Server**][sql-server-doc] | Z programu SQL Server na lokalnych lub bazy danych SQL Azure w chmurze, więc zarządzania rekordami, uruchamiania procedur składowanych lub wykonywania zapytań. | 
| [![Ikona interfejsu API][office-365-outlook-icon]<br/>**usługi Office 365<br/>programu Outlook**][office-365-outlook-doc] | Połącz się na Twoje konto e-mail usługi Office 365, aby można utworzyć i zarządzać nimi, wiadomości e-mail, zadań, zdarzenia kalendarza i spotkania, kontakty, żądania i inne. | [![Ikona interfejsu API][azure-blob-storage-icon]<br/>**obiektów Blob platformy Azure<br/>magazynu**][azure-blob-storage-doc] | Łączenia się z kontem magazynu, aby można było utworzyć i zarządzać zawartością obiektów blob. | 
| [![Ikona interfejsu API][sftp-icon]<br/>**SFTP**][sftp-doc] | Łączenie z serwerami SFTP, do których masz dostęp z Internetu, dzięki czemu można pracować z plikami i folderami. | [![Ikona interfejsu API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Połączenie do usługi SharePoint Online, a więc można zarządzać pliki, załączniki, folderów i. | 
| [![Ikona interfejsu API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Łączenia się z kontem Dynamics 365, aby można było utworzyć i zarządzać rekordów, elementy i inne. | [![Ikona interfejsu API][ftp-icon]<br/>**FTP**][ftp-doc] | Łączenie z serwerami FTP, których masz dostęp z Internetu, dzięki czemu można pracować z plikami i folderami. | 
| [![Ikona interfejsu API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Połącz z kontem usług Salesforce, aby można utworzyć i zarządzać nimi elementy takie jak rekordy, zadań, obiekty i. | [![Ikona interfejsu API][twitter-icon]<br/>**usługi Twitter**][twitter-doc] | Łączenia się z kontem usługi Twitter, a więc można zarządzać tweetów, adherentami, harmonogram i. Zapisz tweetów użytkownika SQL, Excel lub programu SharePoint. | 
| [![Ikona interfejsu API][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Zużywają i publikowanie zdarzeń do Centrum zdarzeń. Na przykład uzyskać dane wyjściowe z aplikacji logiki z usługą Event Hubs, a następnie wyślij, że dane wyjściowe do dostawcy analiz w czasie rzeczywistym. | [![Ikona interfejsu API][azure-event-grid-icon]<br/>**zdarzeń Azure**</br>**siatki**][azure-event-grid-doc] | Monitorowanie zdarzeń opublikowanych przez siatki zdarzeń, na przykład po zmianie zasobów platformy Azure lub innych zasobów. | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Łączniki lokalne 

Poniżej przedstawiono niektóre często używane łączniki, które zapewniają dostęp do danych i zasobów w systemie lokalnym. Przed utworzeniem połączenia lokalnego systemu, należy najpierw [pobrać, zainstalować i skonfigurować bramę danych w lokalnych][gateway-doc]. Ta brama zapewnia bezpieczny kanał komunikacyjny bez konieczności konfigurowania koniecznej infrastruktury sieci. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ikona interfejsu API][biztalk-server-icon]<br/>**BizTalk**</br> **Serwer** | [![Ikona interfejsu API][file-system-icon]<br/>**pliku</br> systemu**][file-system-doc] | [![Ikona interfejsu API][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![Ikona interfejsu API][ibm-informix-icon]<br/>**IBM** </br> **programu Informix**][ibm-informix-doc] | ![Ikona interfejsu API][mysql-icon]<br/>**MySQL** | 
| [![Ikona interfejsu API][oracle-db-icon]<br/>**bazy danych programu Oracle**][oracle-db-doc] | ![Ikona interfejsu API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ikona interfejsu API][sharepoint-server-icon]<br/>**SharePoint</br> serwera**][sharepoint-server-doc] | [![Ikona interfejsu API][sql-server-icon]<br/>**SQL</br> serwera**][sql-server-doc] | ![Ikona interfejsu API][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Łączniki konta integracji 

Poniżej przedstawiono łączniki do tworzenia rozwiązań między firmami (B2B) w aplikacji logiki, podczas tworzenia i opłacać [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), który jest dostępny za pośrednictwem pakietu integracji przedsiębiorstwa (EIP) na platformie Azure. Z tego konta można utworzyć i przechowywać B2B artefaktów, takich jak handlowymi partnerów, umów, map, schematów, certyfikaty i tak dalej. Aby korzystać z tych artefaktów, należy skojarzyć aplikacje logiki z Twoim kontem integracji. Jeśli obecnie używasz BizTalk Server, te łączniki mogą wydawać się znanych już.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona interfejsu API][as2-icon]<br/>**AS2</br> dekodowania**][as2-decode-doc] | [![Ikona interfejsu API][as2-icon]<br/>**AS2</br> kodowania**][as2-encode-doc] | [![Ikona interfejsu API][edifact-icon]<br/>**EDIFACT</br> dekodowania**][edifact-decode-doc] | [![Ikona interfejsu API][edifact-icon]<br/>**EDIFACT</br> kodowania**][edifact-encode-doc] | 
| [![Ikona interfejsu API][flat-file-decode-icon]<br/>**pliku prostego</br> dekodowania**][flat-file-decode-doc] | [![Ikona interfejsu API][flat-file-encode-icon]<br/>**pliku prostego</br> kodowania**][flat-file-encode-doc] | [![Ikona interfejsu API][integration-account-icon]<br/>**integracji<br/>konta**][integration-account-doc] | [![Ikona interfejsu API][liquid-icon]<br/>**płynne**</br>**przekształca**][json-liquid-transform-doc] | 
| [![Ikona interfejsu API][x12-icon]<br/>**X12</br> dekodowania**][x12-decode-doc] | [![Ikona interfejsu API][x12-icon]<br/>**X12</br> kodowania**][x12-encode-doc] | [![Ikona interfejsu API][xml-transform-icon]<br/>**XML**</br>**przekształca**][xml-transform-doc] | [![Ikona interfejsu API][xml-validate-icon]<br/>**XML <br/>sprawdzania poprawności**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Łączniki dla przedsiębiorstw

Aplikacje logiki mogą uzyskiwać dostęp do systemów dla przedsiębiorstw, takie jak SAP i IBM MQ:

|   |   | 
|---|---| 
| [![Ikona interfejsu API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Ikona interfejsu API][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

## <a name="more-about-triggers-and-actions"></a>Więcej informacji o wyzwalacze i akcje

Kilka łączników udostępnia *wyzwalaczy* który powiadamiać aplikację logiki w przypadku wystąpienia określonych zdarzeń. Dlatego przypadku tych zdarzeń, wyzwalacz tworzy i uruchomione jest wystąpienie aplikacji logiki. Na przykład łącznik FTP dostarcza wyzwalacz "podczas dodawania lub modyfikowania pliku", który uruchamia aplikację logiki, kiedy plik zostanie zaktualizowany. 

Logic Apps oferuje rodzaju wyzwalaczy:  

* *Wyzwalacze sondowania*: te wyzwalacze sondują usługę na określoną częstotliwością i kontroli dla nowych danych. 

  Jeśli nowe dane są dostępne, nowe wystąpienie aplikacji logiki jest tworzony i uruchamiane z tymi danymi, który jest przekazywany jako danych wejściowych. 

* *Wyzwalacze wypychania*: te wyzwalacze nasłuchują dla nowych danych w punkcie końcowym lub zdarzenie, które tworzy i uruchamia nowe wystąpienie aplikacji logiki.

* *Wyzwalacz cyklu*: tego wyzwalacza tworzy i uruchomione jest wystąpienie aplikacji logiki na podstawie określonego harmonogramu.

Łączniki udostępniają *akcje* który wykonać zadania w przepływie pracy aplikacji logiki. Na przykład aplikację logiki można odczytywać dane i użyć tych danych w kolejnych krokach aplikacji logiki. W szczególności aplikację logiki można znaleźć dane klienta z bazy danych SQL i przetwarzać dane później w przepływie pracy aplikacji logiki. 

Aby uzyskać więcej informacji o wyzwalacze i akcje, zobacz [omówienie łączniki](connectors-overview.md). 

## <a name="custom-apis-and-connectors"></a>Niestandardowych interfejsów API i łączniki 

Do wywoływania interfejsów API, który uruchomienia niestandardowego kodu lub nie są dostępne jako łączniki, można rozszerzyć platformy Logic Apps przez [Tworzenie niestandardowych aplikacji interfejsu API](../logic-apps/logic-apps-create-api-app.md). Możesz również [Tworzenie niestandardowych łączniki](../logic-apps/custom-connector-overview.md) dla *żadnych* przerwę lub opartego na protokole SOAP API, które udostępniają te interfejsy API do wszystkich aplikacji logiki w Twojej subskrypcji platformy Azure.
Aby udostępnić funkcję niestandardowe aplikacje API Apps lub łączników każdemu używać na platformie Azure, możesz [przesłać łączniki do certyfikacji Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby przesłać lub zagłosować na pomysły dla usługi Azure Logic Apps i łącznikami, odwiedź stronę [witrynę opinii użytkowników Logic Apps](http://aka.ms/logicapps-wish).

* Czy na pewno docs brak artykułów lub szczegóły, które należy wziąć pod uwagę są ważne? Jeśli tak, możesz pomóc przez dodanie do artykułów istniejących lub pisanie własnych. Dokumentacja jest typu open source i w usłudze GitHub. Rozpoczynanie pracy w dokumentacji platformy Azure [repozytorium GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Tworzenie niestandardowych łączniki dla usługi logic apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Tworzenie niestandardowych interfejsów API dla aplikacji logiki](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Łączenie z lokalnymi źródłami danych z aplikacji logiki za pomocą bramy danych lokalnych"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integracja aplikacji logiki z usługą Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Utwórz wystąpienie usługi Zarządzanie interfejsami API Azure do zarządzania i publikowania swoje interfejsy API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integracja aplikacji logiki z usługą App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Wysyłanie komunikatów z tematów i kolejek usługi Service Bus oraz odbieranie komunikatów z subskrypcji i kolejek usługi Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Przetwarzanie komunikatów w grupach lub jako partii"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Ocena warunku i wykonywania różnych działań na podstawie warunku jest PRAWDA lub FAŁSZ"
[delay-doc]: ./connectors-native-delay.md "Wykonywanie akcji z opóźnieniem"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Wykonaj te same akcje na każdy element w tablicy"
[http-doc]: ./connectors-native-http.md "Nawiązywanie połączeń HTTP za pomocą łącznika HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Dodawanie akcji dla żądań i odpowiedzi HTTP w aplikacjach logiki"
[http-response-doc]: ./connectors-native-reqres.md "Dodawanie akcji dla żądań i odpowiedzi HTTP w aplikacjach logiki"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Nawiązywanie połączeń HTTP za pomocą łącznika protokołu HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Dodaj akcje elementu webhook HTTP i wyzwalaczy do aplikacji logiki"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integracja aplikacji logiki z zagnieżdżonymi przepływami pracy"
[query-doc]: ./connectors-native-query.md "Wybieranie i filtrowanie tablic za pomocą akcji zapytania"
[recurrence-doc]:  ./connectors-native-recurrence.md "Wyzwalanie akcji cyklicznych dla aplikacji logiki"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizuj akcje do grup, które uzyskać ich stanu po akcji w grupie zakończenie działania" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizuj akcje w przypadkach, które są przypisane unikatowe wartości. Uruchom tylko w przypadku którego wartość odpowiada wynik wyrażenia, obiektów lub token. Jeśli nie istnieją żadne dopasowań, uruchom na przypadek domyślny"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zatrzymać ani anulować aktywnie uruchomiony przepływ pracy aplikacji logiki"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Akcje Powtórz określony warunek jest spełniony lub niektóre stan został zmieniony"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Wykonywanie operacji zmiennych, takich jak inicjowanie, zestaw, inkrementacja, dekrementacja i Dołącz do zmiennej typu string lub array"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Zarządzanie plikami w kontenerze obiektów blob za pomocą łącznika usługi Azure Blob Storage"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Monitorowanie zdarzeń opublikowanych przez siatki zdarzeń, na przykład po zmianie zasobów platformy Azure lub innych zasobów"
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
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Połącz się arkusze Google, aby zmodyfikować arkuszy"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Łączenie z Zadaniami Google i zarządzanie zadaniami"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Łączenie z bazą danych IBM DB2 w chmurze lub lokalnie. Aktualizowanie wiersza, pobieranie tabeli oraz inne funkcje"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Łączenie z usługą Informix w chmurze lub lokalnie. Odczytywanie wiersza, wyświetlanie listy tabel i inne funkcje"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Połączenie z lokalnym programem IBM MQ lub na platformie Azure do wysyłania i odbierania wiadomości"
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
[smtp-doc]: ./connectors-create-api-smtp.md "Połącz się z serwerem SMTP i wysyłanie wiadomości e-mail z załącznikami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Łączenie z usługą SparkPost w celu komunikowania się"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Połącz z bazy danych Azure SQL lub programu SQL Server. Tworzenie, aktualizowanie, pobieranie i usuwanie wpisów w tabeli bazy danych SQL."
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