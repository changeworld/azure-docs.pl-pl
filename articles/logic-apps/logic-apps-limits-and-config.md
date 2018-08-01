---
title: Limity i konfiguracja — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wartości konfiguracji dla usługi Azure Logic Apps i limity usługi
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 644d382b87b0cc7c60cc8917edbaeff34b222718
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390742"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limity i informacje o konfiguracji dla usługi Azure Logic Apps

W tym artykule opisano limitach oraz szczegółach konfiguracji, tworzenia i uruchamiając automatyczne przepływy pracy z usługą Azure Logic Apps. Aby uzyskać Microsoft Flow, zobacz [limitów i konfiguracji w programie Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limity definicji

Poniżej przedstawiono limity dla definicji aplikacji logiki pojedynczego:

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Akcje na przepływ pracy | 500 | Aby rozszerzyć ten limit, możesz dodać zagnieżdżone przepływy pracy zgodnie z potrzebami. |
| Dozwolona głębokość zagnieżdżenia akcji | 8 | Aby rozszerzyć ten limit, możesz dodać zagnieżdżone przepływy pracy zgodnie z potrzebami. | 
| Przepływy pracy na region na subskrypcję | 1000 | | 
| Wyzwalacze w ramach przepływu pracy | 10 | Podczas pracy w widoku kodu, nie projektanta | 
| Limit przypadków zakresu przełącznika | 25 | | 
| Zmienne w ramach przepływu pracy | 250 | | 
| Liczba znaków w wyrażeniu | 8192 | | 
| Maksymalny rozmiar `trackedProperties` | 16 000 znaków | 
| Nazwy `action` lub `trigger` | 80 znaków | | 
| Długość `description` | 256 znaków | | 
| Maksymalna `parameters` | 50 | | 
| Maksymalna `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Uruchom limity czasu trwania i przechowywanie

Poniżej przedstawiono limity dotyczące przebiegu aplikacji logiki pojedynczego:

| Name (Nazwa) | Limit | Uwagi | 
|------|-------|-------| 
| Czas trwania przebiegu | 90 dni | Aby zmienić ten limit, zobacz [Zmień czas uruchomienia](#change-duration). | 
| Przechowywanie w magazynie | Godzina rozpoczęcia w ciągu 90 dni od uruchomienia | Aby zmienić ten limit na wartość z zakresu od 7 do 90 dni, zobacz [zmiana okresu przechowywania magazynu](#change-retention). | 
| Interwał cyklu minimalne | 1 sekunda | | 
| Maksymalna wartość cyklu interwału | 500 dni | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Zmiana okresu przechowywania wykonywania, czas trwania i przechowywanie

Aby zmienić domyślny limit od 7 do 90 dni, wykonaj następujące kroki. Jeśli musisz przejść przekracza maksymalny limit [się z zespołem usługi Logic Apps](mailto://logicappsemail@microsoft.com) Aby uzyskać pomoc dotyczącą wymagań.

1. W witrynie Azure portal w menu aplikacji logiki wybierz pozycję **ustawienia przepływu pracy**. 

2. W obszarze **opcje środowiska uruchomieniowego**, z **czas przechowywania historii uruchamiania w dniach** wybierz **niestandardowe**. 

3. Wprowadź lub przeciągnij suwak, liczbę dni, które chcesz.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Współbieżność, pętli i usuwanie partii limity

Poniżej przedstawiono limity dotyczące przebiegu aplikacji logiki pojedynczego:

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Współbieżności wyzwalacza | 50 | Domyślny limit wynosi 20. Limit ten opisuje maksymalną liczbę wystąpień aplikacji logiki, które można uruchomić w tym samym czasie lub w sposób równoległy. <p><p>Aby zmienić domyślny limit wartość z zakresu od 1 do 50 włącznie, zobacz [współbieżności wyzwalacza zmiany](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) lub [sekwencyjnie wyzwolić wystąpień](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). | 
| Maksymalna przebiegów oczekiwania | 100 | Domyślny limit wynosi 10. Limit ten opisuje maksymalną liczbę wystąpień aplikacji logiki, które mogą poczekać do uruchamiania, gdy aplikacja logiki jest już uruchomiona maksymalna liczba współbieżnych wystąpień. <p><p>Aby zmienić domyślny limit wartość z zakresu od 0 do 100 włącznie, zobacz [ograniczać przebiegi oczekujących zmian](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). | 
| Elementy foreach | 100 000 | Limit ten opisuje maksymalną liczbę elementów tablicy, które mogą przetwarzać pętlę "for each". <p><p>Do filtrowania większych tablic, można użyć [Akcja zapytania](../connectors/connectors-native-query.md). | 
| Liczba iteracji foreach | 50 | Domyślny limit wynosi 20. Ten limit w tym artykule opisano "for each" Maksymalna liczba iteracji, które można uruchomić w tym samym czasie lub równolegle w pętli. <p><p>Aby zmienić domyślny limit wartość z zakresu od 1 do 50 włącznie, zobacz [zmienić "for each" współbieżności](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) lub [Uruchom "for each" w pętli sekwencyjnie](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). | 
| Elementy SplitOn | 100 000 | | 
| Iteracje UNTIL | 5000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limity przepustowości

Poniżej przedstawiono limity dotyczące przebiegu aplikacji logiki pojedynczego:

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Akcji: Wykonania na 5 minut | 300,000 | Domyślny limit wynosi 100 000. Aby zmienić domyślny limit, zobacz [uruchomić swoją aplikację logiki w trybie "wysokiej przepływności"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), która jest dostępna w wersji zapoznawczej. Alternatywnie można rozkłada obciążenie na więcej niż jednej aplikacji logiki zgodnie z potrzebami. | 
| Akcja: Równoczesne połączenia wychodzące | ~2,500 | Można zmniejszyć liczbę jednoczesnych żądań lub Skróć czas trwania zgodnie z potrzebami. | 
| Punkt końcowy środowiska uruchomieniowego: współbieżnych wywołań przychodzących | ~1,000 | Można zmniejszyć liczbę jednoczesnych żądań lub Skróć czas trwania zgodnie z potrzebami. | 
| Punkt końcowy środowiska uruchomieniowego: Odczyt wywołań na 5 minut  | 60,000 | Więcej niż jedną aplikację zgodnie z potrzebami można rozdystrybuować obciążenie. | 
| Środowisko uruchomieniowe punkt końcowy: wywołania na 5 minut | 45,000 | Więcej niż jedną aplikację zgodnie z potrzebami można rozdystrybuować obciążenie. | 
| Przepływność zawartości na 5 minut | 600 MB | Więcej niż jedną aplikację zgodnie z potrzebami można rozdystrybuować obciążenie. | 
|||| 

Przejdź powyżej tych limitów w normalnego przetwarzania lub uruchamiania testów obciążenia, które mogły zostać zapisane powyżej te limity [się z zespołem usługi Logic Apps](mailto://logicappsemail@microsoft.com) Aby uzyskać pomoc dotyczącą wymagań.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>Limity żądań HTTP

Poniżej przedstawiono limity dotyczące pojedynczego żądania HTTP lub łącznika synchroniczne wywołanie:

#### <a name="timeout"></a>Limit czasu

Niektóre operacje łącznika wykonywać wywołania asynchronicznego lub wysyłane żądania elementu webhook, więc limitu czasu dla tych operacji może być dłuższa niż te limity. Aby uzyskać więcej informacji, zobacz szczegółowe informacje techniczne dla określonego łącznika, a także [wyzwalaczy przepływu pracy i działań](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Żądania wychodzące | 120 sekund | W przypadku dłużej uruchomionej operacji, użyj [wzorca asynchronicznego sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern) lub [pętlą until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Synchronicznej odpowiedzi | 120 sekund | Oryginalnego żądania sposobem uzyskania odpowiedzi wszystkich kroków opisanych w odpowiedzi musi zakończyć w ramach limitu, chyba że wywołanie innego zagnieżdżonego przepływu pracy aplikacji logiki. Aby uzyskać więcej informacji, zobacz [wywołania wyzwalacza lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Rozmiar komunikatu

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Rozmiar komunikatu | 100 MB | Aby obejść to ograniczenie, zobacz [Obsługa dużych komunikatów z segmentu](../logic-apps/logic-apps-handle-large-messages.md). Jednak niektóre łączniki i interfejsy API nie obsługuje segmentu lub nawet domyślny limit. | 
| Rozmiar komunikatu z segmentu | 1 GB | Ten limit dotyczy akcje, które natywnie obsługują segmentu i pozwalają włączyć segmentu w swojej konfiguracji środowiska uruchomieniowego. Aby uzyskać więcej informacji, zobacz [Obsługa dużych komunikatów z segmentu](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limit oceniania wyrażeń | 131 072 znaków | `@concat()`, `@base64()`, `@string()` Wyrażenia nie może być dłuższa niż to ograniczenie. | 
|||| 

#### <a name="retry-policy"></a>Zasady ponawiania

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Liczba ponownych prób | 90 | Wartość domyślna to 4. Aby zmienić domyślny, należy użyć [ponów parametru zasad](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Maksymalna opóźnienie ponowienia próby | 1 dzień | Aby zmienić domyślny, należy użyć [ponów parametru zasad](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Min. opóźnienie ponowienia próby | 5 sekund | Aby zmienić domyślny, należy użyć [ponów parametru zasad](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limity łączników niestandardowych

Poniżej przedstawiono limity łączników niestandardowych, które można utworzyć na podstawie interfejsów API sieci web.

| Name (Nazwa) | Limit | 
| ---- | ----- | 
| Liczba łączników niestandardowych | 1000 na subskrypcję platformy Azure | 
| Liczba żądań na minutę dla każdego połączenia utworzonego przez łącznik niestandardowy | 500 żądań na połączenie |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity konta integracji

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limity artefaktu dla konta integracji

W tym miejscu są ograniczenia na liczbę artefaktów dla każdego konta integracji. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Warstwa bezpłatna*

Korzystać z bezpłatnej warstwy tylko na potrzeby scenariuszy eksploracyjnego, a nie scenariuszy produkcyjnych. Ta warstwa ogranicza przepustowość i użycia i ma nie umową dotyczącą poziomu usług (SLA).

| Artefakt | Limit | Uwagi | 
|----------|-------|-------| 
| Partnerzy handlowi EDI | 25 | | 
| Handlowym umów integracji EDI | 10 | | 
| Maps | 25 | | 
| Schematy | 25 | 
| Zestawy | 10 | | 
| Konfiguracje partii | 5 | 
| Certyfikaty | 25 | | 
|||| 

*Warstwa podstawowa*

| Artefakt | Limit | Uwagi | 
|----------|-------|-------| 
| Partnerzy handlowi EDI | 2 | | 
| Handlowym umów integracji EDI | 1 | | 
| Maps | 500 | | 
| Schematy | 500 | 
| Zestawy | 25 | | 
| Konfiguracje partii | 1 | | 
| Certyfikaty | 2 | | 
|||| 

*Warstwa standardowa*

| Artefakt | Limit | Uwagi | 
|----------|-------|-------| 
| Partnerzy handlowi EDI | 500 | | 
| Handlowym umów integracji EDI | 500 | | 
| Maps | 500 | | 
| Schematy | 500 | 
| Zestawy | 50 | | 
| Konfiguracje partii | 5 |  
| Certyfikaty | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity pojemności artefaktu

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| Schemat | 8 MB | Aby przekazać pliki, które są większe niż 2 MB, należy użyć [identyfikator URI obiektu blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mapy (plik XSLT) | 2 MB | | 
| Punkt końcowy środowiska uruchomieniowego: Odczyt wywołań na 5 minut | 60,000 | Za rozkłada obciążenie na więcej niż jedno konto zgodnie z potrzebami. | 
| Środowisko uruchomieniowe punkt końcowy: wywołania na 5 minut | 45,000 | Za rozkłada obciążenie na więcej niż jedno konto zgodnie z potrzebami. | 
| Punkt końcowy środowiska uruchomieniowego: śledzenie wywołań na 5 minut | 45,000 | Za rozkłada obciążenie na więcej niż jedno konto zgodnie z potrzebami. | 
| Punkt końcowy środowiska uruchomieniowego: blokowanie współbieżnych wywołań | ~1,000 | Można zmniejszyć liczbę jednoczesnych żądań lub Skróć czas trwania zgodnie z potrzebami. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Rozmiar komunikatu protokołu B2B (X12, EDIFACT AS2)

Limity, które są stosowane do protokoły B2B są następujące:

| Name (Nazwa) | Limit | Uwagi | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Dotyczy dekodowanie i kodowanie | 
| X12 | 50 MB | Dotyczy dekodowanie i kodowanie | 
| EDIFACT | 50 MB | Dotyczy dekodowanie i kodowanie | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Konfiguracji: Adresy IP

### <a name="azure-logic-apps-service"></a>Usługa Azure Logic Apps

Wszystkie aplikacje logiki w regionie, użyj tych samych zakresów adresów IP. Do obsługi wywołań, które aplikacje logiki bezpośrednio wprowadzać za pomocą [HTTP](../connectors/connectors-native-http.md), [protokołu HTTP + Swagger](../connectors/connectors-native-http-swagger.md), a inne żądania HTTP, skonfiguruj konfiguracje zapory, aby zawierały te wychodzące i przychodzące adresy IP, na podstawie w którym istnieje aplikacji logiki:

| Region aplikacji logiki | IP ruchu wychodzącego |
|-------------------|-------------|
| Australia Wschodnia | 13.75.149.4, 104.210.90.241, 104.210.91.55 |
| Australia Południowo-Wschodnia | 13.70.159.205, 13.73.114.207, 13.77.3.139 |
| Brazylia Południowa | 191.234.182.26, 191.235.82.221, 191.235.91.7 |
| Kanada Środkowa | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Kanada Wschodnia | 52.229.120.45, 52.229.126.25, 52.232.128.155 |
| Indie Środkowe | 52.172.154.168, 52.172.185.79, 52.172.186.159 |
| Środkowe stany USA | 13.67.236.125, 40.122.170.198, 104.208.25.27 |
| Azja Wschodnia | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Wschodnie stany USA | 13.92.98.111, 40.114.82.191, 40.121.91.41 |
| Wschodnie stany USA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Japonia Wschodnia | 13.71.158.3, 13.71.158.120, 13.73.4.207 |
| Japonia Zachodnia | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Środkowo-północne stany USA | 157.55.210.61, 157.55.212.238, 168.62.248.37 |
| Europa Północna | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| Środkowo-południowe stany USA | 13.65.82.17, 13.66.52.232, 104.210.144.48 |
| Indie Południowe | 52.172.50.24, 52.172.52.0, 52.172.55.231 |
| Azja Południowo-Wschodnia | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Środkowo-zachodnie stany USA | 52.161.18.218, 52.161.9.108, 52.161.27.190 |
| Europa Zachodnia | 13.95.147.65, 40.68.209.23, 40.68.222.65 |
| Indie Zachodnie | 104.211.162.205, 104.211.164.80, 104.211.164.136 |
| Zachodnie stany USA | 40.83.164.80 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |
| Zachodnie stany USA 2 | 13.66.210.167, 52.183.29.132, 52.183.30.169 |
| Południowe Zjednoczone Królestwo | 51.140.73.85, 51.140.74.14, 51.140.78.44 |
| Zachodnie Zjednoczone Królestwo | 51.141.45.238, 51.141.47.136, 51.141.54.185 |
| | |

| Region aplikacji logiki | Liczba przychodzących adresów IP |
|-------------------|------------|
| Australia Wschodnia | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Australia Południowo-Wschodnia | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Brazylia Południowa | 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Kanada Środkowa | 13.88.249.209, 52.233.29.79, 52.233.30.218 |
| Kanada Wschodnia | 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Indie Środkowe | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Środkowe stany USA | 13.67.236.76, 40.77.31.87, 40.77.111.254 |
| Azja Wschodnia | 13.75.89.159, 23.97.68.172, 168.63.200.173 |
| Wschodnie stany USA | 40.117.99.79, 40.117.100.228, 137.135.106.54 |
| Wschodnie stany USA 2 | 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Japonia Wschodnia | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Japonia Zachodnia | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Środkowo-północne stany USA | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Europa Północna | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| Środkowo-południowe stany USA | 13.65.98.39, 13.84.41.46, 13.84.43.45 |
| Indie Południowe | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Azja Południowo-Wschodnia | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Środkowo-zachodnie stany USA | 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa Zachodnia | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Indie Zachodnie | 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Zachodnie stany USA | 13.91.252.184 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Zachodnie stany USA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Południowe Zjednoczone Królestwo | 51.140.78.71, 51.140.79.109, 51.140.84.39 |
| Zachodnie Zjednoczone Królestwo | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Łączniki

Do obsługi wywołania, [łączników](../connectors/apis-list.md) upewnij, tak skonfigurowany, konfiguracje zapory obejmują one te wychodzące adresy IP na podstawie regionów wówczas, gdy istnieją aplikacje Logic Apps.

> [!IMPORTANT]
>
> Jeśli masz istniejące konfiguracje, zaktualizuj je **możliwie jak przed 1 września 2018** tak aby dołączyć i pasują do adresów IP na tej liście dla regionów, gdy istnieją aplikacje Logic Apps. 

| Region aplikacji logiki | IP ruchu wychodzącego | 
|-------------------|-------------|  
| Australia Wschodnia | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Australia Południowo-Wschodnia | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 | 
| Brazylia Południowa | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Kanada Środkowa | 13.71.170.208 — 13.71.170.223, 13.71.170.224 — 13.71.170.239 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 | 
| Kanada Wschodnia | 40.69.106.240 - 40.69.106.255 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 | 
| Indie Środkowe | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 | 
| Środkowe stany USA | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 | 
| Azja Wschodnia | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 | 
| Wschodnie stany USA | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| Wschodnie stany USA 2 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Japonia Wschodnia | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 | 
| Japonia Zachodnia | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 | 
| Środkowo-północne stany USA | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Europa Północna | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| Środkowo-południowe stany USA | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 | 
| Indie Południowe | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 | 
| Azja Południowo-Wschodnia | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 | 
| Środkowo-zachodnie stany USA | 13.71.195.32 - 13.71.195.47 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 | 
| Europa Zachodnia | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 | 
| Indie Zachodnie | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 | 
| Zachodnie stany USA | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 | 
| Zachodnie stany USA 2 | 13.66.140.128 - 13.66.140.143 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 | 
| Południowe Zjednoczone Królestwo | 51.140.80.51, 51.140.148.0 - 51.140.148.15 | 
| Zachodnie Zjednoczone Królestwo | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
| | | 

## <a name="next-steps"></a>Kolejne kroki  

* Dowiedz się, jak [utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Dowiedz się więcej o [typowe przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)
