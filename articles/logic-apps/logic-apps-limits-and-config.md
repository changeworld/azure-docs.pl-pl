---
title: Limity i konfiguracji — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Limity usług i wartości konfiguracji dla usługi Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/14/2018
ms.author: estfan
ms.openlocfilehash: 8c2ac4b8f55d25d5d3fcfdd6a9bcb6f6c8cfc201
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limity i informacje o konfiguracji dla usługi Azure Logic Apps

W tym artykule opisano limity i szczegóły konfiguracji tworzenie i uruchamianie automatycznych przepływów pracy z usługą Azure Logic Apps. Aby Flow firmy Microsoft, zobacz [limity i konfiguracji w programie Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limity definicji

Poniżej przedstawiono limity dla definicji aplikacji logiki pojedynczego:

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Akcje dla przepływu pracy | 500 | Aby rozszerzyć ten limit, można dodać zagnieżdżone przepływy pracy, zgodnie z potrzebami. |
| Dozwolona liczba poziomów zagnieżdżenia dla akcji | 8 | Aby rozszerzyć ten limit, można dodać zagnieżdżone przepływy pracy, zgodnie z potrzebami. | 
| Przepływy pracy na region na subskrypcję | 1000 | | 
| Wyzwalaczy na przepływu pracy | 10 | Podczas pracy w widoku kodu, nie projektanta | 
| Limit przypadków zakresu przełącznika | 25 | | 
| Zmienne dla przepływu pracy | 250 | | 
| Liczba znaków na wyrażenia | 8192 | | 
| Maksymalny rozmiar `trackedProperties` | 16 000 znaków. | 
| Nazwy `action` lub `trigger` | 80 znaków | | 
| długość `description` | 256 znaków | | 
| Maksymalna `parameters` | 50 | | 
| Maksymalna `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Uruchom limity czasu trwania i przechowywania

Poniżej przedstawiono limity aplikacji logiki pojedynczego uruchomienia:

| Name (Nazwa) | Limit | Uwagi | 
|------|-------|-------| 
| Czas trwania testu | 90 dni | Aby zmienić ten limit, zobacz [Zmień czas trwania](#change-duration). | 
| Magazyn przechowywania | Godzina rozpoczęcia 90 dni od uruchomienia | Aby zmienić ten limit, zobacz [zmienić przechowywania magazynu](#change-retention). | 
| Interwał cyklu minimalna | 1 sekunda | | 
| Maksymalna wartość cyklu interwału | 500 dni | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Zmień Uruchom przechowywania czas trwania i magazynu

To ograniczenie można zmienić na wartość z zakresu od 7 dni i 90 dni. Jednak, aby przekracza maksymalny limit [skontaktuj się z zespołem Logic Apps](mailto://logicappsemail@microsoft.com) Aby uzyskać pomoc dotyczącą wymagań.

1. W portalu Azure, w menu aplikacji logiki, wybierz **ustawienia przepływu pracy**. 

2. W obszarze **opcje środowiska uruchomieniowego**, z **Uruchom dni przechowywania historii** wybierz **niestandardowy**. 

3. Wprowadź lub przeciągnij suwak liczbę dni, które mają.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Powtarzanie i debatching limity

Poniżej przedstawiono limity aplikacji logiki pojedynczego uruchomienia:

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Do iteracji | 5000 | | 
| Elementy ForEach | 100,000 | Można użyć [zapytania akcji](../connectors/connectors-native-query.md) do filtrowania większych tablice zgodnie z potrzebami. | 
| Równoległość ForEach | 50 | Wartość domyślna to 20. <p>Określony poziom równoległości w pętli ForEach ustawia `runtimeConfiguration` właściwości w `foreach` akcji. <p>Aby uruchomić sekwencyjnie pętli ForEach, ustaw `operationOptions` właściwości na wartość "Sequential" w `foreach` akcji. | 
| Elementy SplitOn | 100,000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limity przepustowości

Poniżej przedstawiono limity aplikacji logiki pojedynczego uruchomienia:

| Name (Nazwa) | Limit | Uwagi | 
| ----- | ----- | ----- | 
| Wykonania akcji na 5 minut | 100,000 | Aby zwiększyć limit 300 000, możesz uruchomić aplikację logiki w `High Throughput` tryb. Aby skonfigurować tryb wysokiej przepływności, w obszarze `runtimeConfiguration` zasobu przepływu pracy, należy ustawić `operationOptions` właściwości `OptimizedForHighThroughput`. <p>**Uwaga**: Tryb wysokiej przepływności jest w wersji zapoznawczej. Ponadto można rozpowszechniać obciążenia pracą wielu aplikacjom w razie potrzeby. | 
| Akcje równoczesnych połączeń wychodzących | ~2,500 | Można zmniejszyć liczbę jednoczesnych żądań lub skrócić czas trwania, zgodnie z potrzebami. | 
| Punkt końcowy środowiska wykonawczego: równoczesnych połączeń przychodzących | ~1,000 | Można zmniejszyć liczbę jednoczesnych żądań lub skrócić czas trwania, zgodnie z potrzebami. | 
| Środowisko uruchomieniowe punkt końcowy: wywołania na 5 minut do odczytu  | 60,000 | Można rozpowszechniać obciążenia pracą wielu aplikacjom zgodnie z potrzebami. | 
| Środowisko uruchomieniowe punkt końcowy: wywołania wywołań na 5 minut| 45,000 |Można rozpowszechniać obciążenia pracą wielu aplikacjom zgodnie z potrzebami. | 
|||| 

Przekroczenie tych ograniczeń normalnego przetwarzania lub obciążenia wykonywania testów, które może przekroczyć te limity [skontaktuj się z zespołem Logic Apps](mailto://logicappsemail@microsoft.com) Aby uzyskać pomoc dotyczącą wymagań.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>Limity żądań HTTP

Poniżej przedstawiono limity dla pojedynczego żądania HTTP lub łącznika synchroniczne wywołanie:

#### <a name="timeout"></a>Limit czasu

Niektóre operacje łącznika wykonywać wywołania asynchronicznego lub nasłuchiwanie żądań elementu webhook, więc limitu czasu dla tych operacji może być dłuższa niż te limity. Aby uzyskać więcej informacji, zobacz szczegóły techniczne dla określonego łącznika, a także [przepływu pracy wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Żądania wychodzącego | 120 sekund | Dla operacji dłużej, użyj [wzorca asynchronicznego sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern) lub [do pętli](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Synchroniczne odpowiedzi | 120 sekund | Oryginalnego żądania pobrać odpowiedzi wszystkie kroki w odpowiedzi musi zakończyć się przed upływem limitu chyba, że wywołanie innego logiki aplikacji jako zagnieżdżony przepływ pracy. Aby uzyskać więcej informacji, zobacz [połączeń telefonicznych, wyzwalacza lub zagnieździć aplikacje logiki](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Rozmiar komunikatu

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Rozmiar komunikatu | 100 MB | Aby obejść to ograniczenie, zobacz [obsługi dużych wiadomości z podziału](../logic-apps/logic-apps-handle-large-messages.md). Jednak niektóre łączniki i interfejsów API nie obsługuje podziału lub nawet domyślny limit. | 
| Rozmiar komunikatu z podziału | 1 GB | Ten limit dotyczy akcje, które natywnie obsługują podziału albo może mieć podziału włączone w konfiguracji obsługi pomocy technicznej. Aby uzyskać więcej informacji, zobacz [obsługi dużych wiadomości z podziału](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limit obliczania wyrażeń | 131 072 znaków | `@concat()`, `@base64()`, `@string()` Wyrażenia nie może być dłuższa niż to ograniczenie. | 
|||| 

#### <a name="retry-policy"></a>Zasady ponawiania

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Liczba ponownych prób | 90 | Wartość domyślna to 4. Aby zmienić ustawienie domyślne, należy użyć [ponów parametr zasad](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Spróbuj ponownie Maksymalne opóźnienie | 1 dzień | Aby zmienić ustawienie domyślne, należy użyć [ponów parametr zasad](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Min. opóźnienie ponowienia próby | 5 sekund | Aby zmienić ustawienie domyślne, należy użyć [ponów parametr zasad](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limity łącznik niestandardowy

Poniżej przedstawiono limity dla łączników niestandardowych, które można utworzyć na podstawie interfejsów API sieci web.

| Name (Nazwa) | Limit | 
| ---- | ----- | 
| Wiele łączników niestandardowych | 1000 na subskrypcję platformy Azure | 
| Liczba żądań na minutę dla każdego połączenia utworzonego przez łącznik niestandardowy | 500 żądań połączenia dla |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity konta integracji

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limity artefaktu dla danego konta integracji

Poniżej przedstawiono limity liczby artefaktów dla każdego konta integracji. Aby uzyskać więcej informacji, zobacz [Logic Apps cennik](https://azure.microsoft.com/pricing/details/logic-apps/).

*Warstwa bezpłatna*

| Artefaktów | Limit | Uwagi | 
|----------|-------|-------| 
| Partnerzy handlowi EDI | 25 | | 
| Umowy handlowym EDI | 10 | | 
| Maps | 25 | | 
| Schematy | 25 | 
| Zestawy | 10 | | 
| Konfiguracje partii | 5 | 
| Certyfikaty | 25 | | 
|||| 

*Warstwa podstawowa*

| Artefaktów | Limit | Uwagi | 
|----------|-------|-------| 
| Partnerzy handlowi EDI | 2 | | 
| Umowy handlowym EDI | 1 | | 
| Maps | 500 | | 
| Schematy | 500 | 
| Zestawy | 25 | | 
| Konfiguracje partii | 1 | | 
| Certyfikaty | 2 | | 
|||| 

*Warstwa standardowa*

| Artefaktów | Limit | Uwagi | 
|----------|-------|-------| 
| Partnerzy handlowi EDI | 500 | | 
| Umowy handlowym EDI | 500 | | 
| Maps | 500 | | 
| Schematy | 500 | 
| Zestawy | 50 | | 
| Konfiguracje partii | 5 |  
| Certyfikaty | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity pojemności artefaktów

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Schemat | 8 MB | Aby przekazać pliki większe niż 2 MB, należy użyć [identyfikator URI obiektu blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mapy (plik XSLT) | 2 MB | | 
| Środowisko uruchomieniowe punkt końcowy: wywołania na 5 minut do odczytu | 60,000 | Wiele kont w razie potrzeby można rozpowszechniają obciążenia. | 
| Środowisko uruchomieniowe punkt końcowy: wywołania wywołań na 5 minut | 45,000 | Wiele kont w razie potrzeby można rozpowszechniają obciążenia. | 
| Punkt końcowy środowiska wykonawczego: śledzenie wywołań na 5 minut | 45,000 | Wiele kont w razie potrzeby można rozpowszechniają obciążenia. | 
| Punkt końcowy środowiska wykonawczego: blokowanie równoczesnych wywołań | ~1,000 | Możesz zmniejszyć liczbę jednoczesnych żądań lub skrócić czas trwania w razie potrzeby. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Rozmiar wiadomości B2B protokołu (AS2, X12, EDIFACT)

Poniżej przedstawiono ograniczeń, które dotyczą protokoły B2B:

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Ma zastosowanie do zdekodowania i kodowania | 
| X12 | 50 MB | Ma zastosowanie do zdekodowania i kodowania | 
| EDIFACT | 50 MB | Ma zastosowanie do zdekodowania i kodowania | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Konfiguracji: Adresy IP

### <a name="azure-logic-apps-service"></a>Usługa Azure Logic Apps

Wszystkie aplikacje logiki w regionie używać tego samego zakresu adresów IP.
Wywołania, które aplikacje logiki bezpośrednio za pomocą [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) lub inne żądania HTTP pochodzą z adresów IP na tej liście. 

| Region aplikacji logiki | Wychodzącego |
|-------------------|-------------|
| Australia | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
| Australia Wschodnia | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Brazylia Południowa | 191.235.82.221, 191.235.91.7, 191.234.182.26 |
| Kanada Środkowa | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Kanada Wschodnia | 52.232.128.155, 52.229.120.45, 52.229.126.25 |
| Indie Środkowe | 52.172.154.168, 52.172.186.159, 52.172.185.79 |
| Środkowe stany USA | 13.67.236.125, 104.208.25.27, 40.122.170.198 |
| Azja Wschodnia | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Wschodnie stany USA | 13.92.98.111, 40.121.91.41, 40.114.82.191 |
| Wschodnie stany USA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Japonia Wschodnia | 13.71.158.3, 13.73.4.207, 13.71.158.120 |
| Japonia Zachodnia | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Środkowo-północne stany USA | 168.62.248.37, 157.55.210.61, 157.55.212.238 |
| Europa Północna | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| Środkowo-południowe stany USA | 104.210.144.48, 13.65.82.17, 13.66.52.232 |
| Indie Południowe | 52.172.50.24, 52.172.55.231, 52.172.52.0 |
| Azja Południowo-Wschodnia | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Środkowo-zachodnie stany USA | 52.161.27.190, 52.161.18.218, 52.161.9.108 |
| Europa Zachodnia | 40.68.222.65, 40.68.209.23, 13.95.147.65 |
| Indie Zachodnie | 104.211.164.80, 104.211.162.205, 104.211.164.136 |
| Zachodnie stany USA | 52.160.92.112, 40.118.244.241, 40.118.241.243 |
| Zachodnie stany USA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132 |
| Południowe Zjednoczone Królestwo | 51.140.74.14, 51.140.73.85, 51.140.78.44 |
| Zachodnie Zjednoczone Królestwo | 51.141.54.185, 51.141.45.238, 51.141.47.136 |
| | |

| Region aplikacji logiki | IP dla ruchu przychodzącego |
|-------------------|-------------|
| Australia Wschodnia | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Australia Południowo-Wschodnia | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Brazylia Południowa | 191.235.86.199, 191.235.95.229, 191.235.94.220 |
| Kanada Środkowa | 13.88.249.209, 52.233.30.218, 52.233.29.79 |
| Kanada Wschodnia | 52.232.129.143, 52.229.125.57, 52.232.133.109 |
| Indie Środkowe | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Środkowe stany USA | 13.67.236.76, 40.77.111.254, 40.77.31.87 |
| Azja Wschodnia | 168.63.200.173, 13.75.89.159, 23.97.68.172 |
| Wschodnie stany USA | 137.135.106.54, 40.117.99.79, 40.117.100.228 |
| Wschodnie stany USA 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136 |
| Japonia Wschodnia | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Japonia Zachodnia | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Środkowo-północne stany USA | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Europa Północna | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| Środkowo-południowe stany USA | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Indie Południowe | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Azja Południowo-Wschodnia | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Środkowo-zachodnie stany USA | 52.161.26.172, 52.161.8.128, 52.161.19.82 |
| Europa Zachodnia | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Indie Zachodnie | 104.211.164.112, 104.211.165.81, 104.211.164.25 |
| Zachodnie stany USA | 52.160.90.237, 138.91.188.137, 13.91.252.184 |
| Zachodnie stany USA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Południowe Zjednoczone Królestwo | 51.140.79.109, 51.140.78.71, 51.140.84.39 |
| Zachodnie Zjednoczone Królestwo | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Łączniki

Wywołania który [łączniki](../connectors/apis-list.md) upewnij się, pochodzi z adresów IP na tej liście.

| Region aplikacji logiki | Wychodzącego |
|-------------------|-------------|
| Australia Wschodnia | 40.126.251.213 |
| Australia Południowo-Wschodnia | 40.127.80.34 |
| Brazylia Południowa | 191.232.38.129 |
| Kanada Środkowa | 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13 |
| Kanada Wschodnia | 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52 |
| Indie Środkowe | 104.211.98.164 |
| Środkowe stany USA | 40.122.49.51 |
| Azja Wschodnia | 23.99.116.181 |
| Wschodnie stany USA | 191.237.41.52 |
| Wschodnie stany USA 2 | 104.208.233.100 |
| Japonia Wschodnia | 40.115.186.96 |
| Japonia Zachodnia | 40.74.130.77 |
| Środkowo-północne stany USA | 65.52.218.230 |
| Europa Północna | 104.45.93.9 |
| Środkowo-południowe stany USA | 104.214.70.191 |
| Indie Południowe | 104.211.227.225 |
| Azja Południowo-Wschodnia | 13.76.231.68 |
| Europa Zachodnia | 40.115.50.13 |
| Indie Zachodnie | 104.211.161.203 |
| Zachodnie stany USA | 104.40.51.248 |
| Południowe Zjednoczone Królestwo | 51.140.80.51 |
| Zachodnie Zjednoczone Królestwo | 51.141.47.105 |
| | | 

## <a name="next-steps"></a>Kolejne kroki  

* [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Typowe przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Wideo: Automatyzować procesy biznesowe z usługą Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Wideo: Integrować systemy z usługą Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
