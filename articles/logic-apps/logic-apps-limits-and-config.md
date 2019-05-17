---
title: Limity i konfiguracja — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wartości konfiguracji dla usługi Azure Logic Apps i limity usługi
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: a320f584ff82f2b8a2b3d784e1995aa043004587
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597494"
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

| Name (Nazwa) | Limit wielodostępnych | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Czas trwania przebiegu | 90 dni | 365 dni | Aby zmienić domyślny limit, zobacz [Zmień czas uruchomienia](#change-duration). |
| Przechowywanie w magazynie | Godzina rozpoczęcia w ciągu 90 dni od uruchomienia | 365 dni | Aby zmienić domyślny limit, zobacz [zmiana okresu przechowywania magazynu](#change-retention). |
| Interwał cyklu minimalne | 1 s | 1 s ||
| Maksymalna wartość cyklu interwału | 500 dni | 500 dni ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Zmiana okresu przechowywania wykonywania, czas trwania i przechowywanie

Aby zmienić domyślny limit czasu trwania wykonywania i przechowywanie w magazynie, wykonaj następujące kroki. Jeśli musisz przejść przekracza maksymalny limit [się z zespołem usługi Logic Apps](mailto://logicappsemail@microsoft.com) Aby uzyskać pomoc dotyczącą wymagań.

1. W witrynie Azure portal w menu aplikacji logiki wybierz pozycję **ustawienia przepływu pracy**.

2. W obszarze **opcje środowiska uruchomieniowego**, z **czas przechowywania historii uruchamiania w dniach** wybierz **niestandardowe**.

3. Wprowadź lub przeciągnij suwak, liczbę dni, które chcesz.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Współbieżność, pętli i usuwanie partii limity

Poniżej przedstawiono limity dotyczące przebiegu aplikacji logiki pojedynczego:

| Name (Nazwa) | Limit | Uwagi |
| ---- | ----- | ----- |
| Współbieżności wyzwalacza | * Nieograniczone po wyłączeniu kontrola współbieżności <p><p>* 25 jest domyślny limit, gdy kontrola współbieżności jest włączona, którego nie można cofnąć po włączeniu formantu. Domyślne można zmienić na wartość z zakresu od 1 do 50 włącznie. | Limit ten opisuje największa liczba wystąpień aplikacji logiki, które można uruchomić w tym samym czasie lub w sposób równoległy. <p><p>Aby zmienić domyślny limit wartość z zakresu od 1 do 50 włącznie, zobacz [limit współbieżności wyzwalacza zmiany](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) lub [sekwencyjnie wyzwolić wystąpień](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maksymalna przebiegów oczekiwania | Po włączeniu kontroli współbieżności minimalna liczba oczekujących przebiegów jest 10, a także liczbę równoczesnych uruchomień (współbieżności wyzwalacza). Możesz zmienić maksymalną liczbę maksymalnie 100 (włącznie). | Limit ten opisuje największa liczba wystąpień aplikacji logiki, które mogą poczekać do uruchamiania, gdy aplikacja logiki jest już uruchomiona maksymalna liczba współbieżnych wystąpień. <p><p>Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Elementy tablicy foreach | 100,000 | Limit ten opisuje największa liczba elementów tablicy, które mogą przetwarzać pętlę "for each". <p><p>Do filtrowania większych tablic, można użyć [Akcja zapytania](../connectors/connectors-native-query.md). |
| Współbieżność foreach | 20 jest domyślny limit, gdy kontrola współbieżności jest wyłączona. Domyślne można zmienić na wartość z zakresu od 1 do 50 włącznie. | To ograniczenie jest największa liczba "for each" iteracji, które można uruchomić w tym samym czasie lub równolegle w pętli. <p><p>Aby zmienić domyślny limit wartość z zakresu od 1 do 50 włącznie, zobacz [zmienić współbieżności "for each" limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) lub [Uruchom "for each" w pętli sekwencyjnie](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Elementy SplitOn | 100,000 | W przypadku wyzwalaczy, które zwracają tablicę, można określić wyrażenie, które korzysta z właściwością "SplitOn" który [dzieli lub debatches elementów tablicy do wielu wystąpień przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) przetwarzania zamiast użycia dla pętli "Foreach". To wyrażenie odwołuje się do tablicy, na potrzeby tworzenia i uruchamiania wystąpienia przepływu pracy dla każdego elementu tablicy. |
| Iteracje UNTIL | 5,000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limity przepustowości

Poniżej przedstawiono limity dotyczące przebiegu aplikacji logiki pojedynczego:

### <a name="multi-tenant-logic-apps-service"></a>Usługa Logic Apps z wielu dzierżawców

| Name (Nazwa) | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcja: Liczba wykonań na 5 minut | 100 000 jest domyślny limit 300 000 jest jednak maksymalny limit. | Aby zmienić domyślny limit, zobacz [uruchomić swoją aplikację logiki w trybie "wysokiej przepływności"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), która jest dostępna w wersji zapoznawczej. Alternatywnie można rozkłada obciążenie na więcej niż jednej aplikacji logiki zgodnie z potrzebami. |
| Akcja: Równoczesne połączenia wychodzące | ~2,500 | Można zmniejszyć liczbę jednoczesnych żądań lub Skróć czas trwania zgodnie z potrzebami. |
| Punkt końcowy środowiska uruchomieniowego: Współbieżnych wywołań przychodzących | ~1,000 | Można zmniejszyć liczbę jednoczesnych żądań lub Skróć czas trwania zgodnie z potrzebami. |
| Punkt końcowy środowiska uruchomieniowego: Odczyt wywołań na 5 minut  | 60,000 | Więcej niż jedną aplikację zgodnie z potrzebami można rozdystrybuować obciążenie. |
| Punkt końcowy środowiska uruchomieniowego: Wywołania na 5 minut | 45,000 | Więcej niż jedną aplikację zgodnie z potrzebami można rozdystrybuować obciążenie. |
| Przepływność zawartości na 5 minut | 600 MB | Więcej niż jedną aplikację zgodnie z potrzebami można rozdystrybuować obciążenie. |
||||

### <a name="integration-service-environment-ise"></a>Środowisko usługi integracji (ISE)

| Name (Nazwa) | Limit | Uwagi |
|------|-------|-------|
| Limit wykonywania jednostkę podstawową | System ograniczenia gdy pojemność infrastruktury osiągnie 80% | Udostępnia ~ 4000 wykonania akcji na minutę, czyli ~ 160 mln wykonań akcji na miesiąc | |
| Limit wykonywania jednostki skalowania | System ograniczenia gdy pojemność infrastruktury osiągnie 80% | Każda jednostka skalowania może zapewnić wykonania dodatkowych akcji ~ 2000 na minutę, czyli ~ ponad 80 mln więcej wykonania akcji na miesiąc | |
| Jednostki skalowania maksymalne, które można dodać | 10 | |
||||

Przejdź powyżej tych limitów w normalnego przetwarzania lub uruchamiania testów obciążenia, które mogły zostać zapisane powyżej te limity [się z zespołem usługi Logic Apps](mailto://logicappsemail@microsoft.com) Aby uzyskać pomoc dotyczącą wymagań.

<a name="request-limits"></a>

## <a name="http-limits"></a>Limity HTTP

Poniżej przedstawiono limity dotyczące pojedynczego żądania HTTP lub łącznika synchroniczne wywołanie:

#### <a name="timeout"></a>Limit czasu

Niektóre operacje łącznika wykonywać wywołania asynchronicznego lub wysyłane żądania elementu webhook, więc limitu czasu dla tych operacji może być dłuższa niż te limity. Aby uzyskać więcej informacji, zobacz szczegółowe informacje techniczne dla określonego łącznika, a także [wyzwalaczy przepływu pracy i działań](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name (Nazwa) | Limit wielodostępnych | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Żądania wychodzące | 120 sekund | 240 sekund | W przypadku dłużej uruchomionej operacji, użyj [wzorca asynchronicznego sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern) lub [pętlą until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Synchronicznej odpowiedzi | 120 sekund | 240 sekund | Oryginalnego żądania sposobem uzyskania odpowiedzi wszystkich kroków opisanych w odpowiedzi musi zakończyć w ramach limitu, chyba że wywołanie innego zagnieżdżonego przepływu pracy aplikacji logiki. Aby uzyskać więcej informacji, zobacz [wywołania wyzwalacza lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Rozmiar komunikatu

| Name (Nazwa) | Limit wielodostępnych | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Rozmiar komunikatu | 100 MB | 200 MB | Aby obejść to ograniczenie, zobacz [Obsługa dużych komunikatów z segmentu](../logic-apps/logic-apps-handle-large-messages.md). Jednak niektóre łączniki i interfejsy API nie obsługuje segmentu lub nawet domyślny limit. |
| Rozmiar komunikatu z segmentu | 1 GB | 5 GB | Ten limit dotyczy akcje, które natywnie obsługują segmentu i pozwalają włączyć segmentu w swojej konfiguracji środowiska uruchomieniowego. <p>W środowisku usługi integracji, aparat usługi Logic Apps obsługuje ten limit, ale łączniki mają własne segmentu limity do limitu aparatu, na przykład, zobacz [łącznika usługi Azure Blob Storage](/connectors/azureblob/). Aby uzyskać więcej informacji o dzielący na fragmenty, zobacz [Obsługa dużych komunikatów z segmentu](../logic-apps/logic-apps-handle-large-messages.md). |
| Limit oceniania wyrażeń | 131 072 znaków | 131 072 znaków | `@concat()`, `@base64()`, `@string()` Wyrażenia nie może być dłuższa niż to ograniczenie. |
|||||

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

| Name (Nazwa) | Limit wielodostępnych | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Liczba łączników niestandardowych | 1000 na subskrypcję platformy Azure | 1000 na subskrypcję platformy Azure ||
| Liczba żądań na minutę dla łącznika niestandardowego | 500 żądań na minutę na połączenie | 2000 żądań na minutę na *łącznika niestandardowego* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Tożsamości zarządzane

| Name (Nazwa) | Limit |
| ---- | ----- |
| Liczba aplikacji logiki za pomocą przypisany systemowo zarządzana tożsamości na subskrypcję platformy Azure | 10 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity konta integracji

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limity artefaktu dla konta integracji

W tym miejscu są ograniczenia na liczbę artefaktów dla każdego konta integracji. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!NOTE] 
> Korzystać z bezpłatnej warstwy tylko na potrzeby scenariuszy eksploracyjnego, a nie scenariuszy produkcyjnych. Ta warstwa ogranicza przepustowość i użycia i ma nie umową dotyczącą poziomu usług (SLA).

| Artefakt | Wolny | Podstawowy | Standardowa (Standard) |
|----------|------|-------|----------|
| Handlowym umów integracji EDI | 10 | 1 | 500 |
| Partnerzy handlowi EDI | 25 | 2 | 500 |
| Mapy | 25 | 500 | 1000 |
| Schematy | 25 | 500 | 1000 |
| Zestawy | 10 | 25 | 50 |
| Certyfikaty | 25 | 2 | 500 |
| Konfiguracje partii | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity pojemności artefaktu

| Artefakt | Limit | Uwagi |
| -------- | ----- | ----- |
| Zestaw | 8 MB | Aby przekazać pliki, które są większe niż 2 MB, należy użyć [kontener konta i obiektów blob usługi Azure storage](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mapy (plik XSLT) | 8 MB | Aby przekazać pliki, które są większe niż 2 MB, należy użyć [mapuje API REST usługi Azure Logic Apps —](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Schemat | 8 MB | Aby przekazać pliki, które są większe niż 2 MB, należy użyć [kontener konta i obiektów blob usługi Azure storage](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Punkt końcowy środowiska uruchomieniowego | Limit | Uwagi |
|------------------|-------|-------|
| Odczyt wywołań na 5 minut | 60,000 | Za rozkłada obciążenie na więcej niż jedno konto zgodnie z potrzebami. |
| Wywołania na 5 minut | 45,000 | Za rozkłada obciążenie na więcej niż jedno konto zgodnie z potrzebami. |
| Wywołania śledzenia na 5 minut | 45,000 | Za rozkłada obciążenie na więcej niż jedno konto zgodnie z potrzebami. |
| Zablokowanie współbieżnych wywołań | ~1,000 | Można zmniejszyć liczbę jednoczesnych żądań lub Skróć czas trwania zgodnie z potrzebami. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Rozmiar komunikatu protokołu B2B (X12, EDIFACT AS2)

Limity rozmiaru wiadomości, które są stosowane do protokoły B2B są następujące:

| Name (Nazwa) | Limit wielodostępnych | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 — 100 MB<br>V1 — 50 MB | v2 — 200 MB <br>V1 — 50 MB | Dotyczy dekodowanie i kodowanie |
| X12 | 50 MB | 50 MB | Dotyczy dekodowanie i kodowanie |
| EDIFACT | 50 MB | 50 MB | Dotyczy dekodowanie i kodowanie |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Wyłączanie lub usuwanie aplikacji logiki

Po wyłączeniu aplikacji logiki są tworzone nie nowe przebiegi. Wszystkie w toku, oczekujących uruchomień aż do ich zakończy się, co może zająć czas trwania.

Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Konfiguracja zapory: Adresy IP

Wszystkie aplikacje logiki, w tym samym regionie, użyj tych samych zakresów adresów IP. Do obsługi połączeń, wchodzące w aplikacjach logiki bezpośrednio za pomocą [HTTP](../connectors/connectors-native-http.md), [protokołu HTTP + Swagger](../connectors/connectors-native-http-swagger.md)i inne żądania HTTP, skonfiguruj zapory przy użyciu *wszystkich* [ dla ruchu przychodzącego](#inbound) *i* [wychodzącego](#outbound) adresy IP używane przez usługę Logic Apps, na podstawie regionów, gdy istnieją aplikacje Logic Apps. Te adresy są wyświetlane w obszarze **przychodzący** i **ruchu wychodzącego** nagłówków w tej sekcji i są sortowane według regionów.

Do obsługi wywołania, [łączników zarządzanych przez firmę Microsoft](../connectors/apis-list.md) upewnij, skonfigurowania zapory za pomocą *wszystkich* [wychodzącego](#outbound) adresy IP używane przez te łączniki, na podstawie regionów Jeśli istnieją aplikacje Logic Apps. Te adresy są wyświetlane w obszarze **ruchu wychodzącego** nagłówka w tej sekcji i są sortowane według regionów.

Aby uzyskać [Azure dla instytucji rządowych](../azure-government/documentation-government-overview.md) i [Azure China 21Vianet](/azure/china/china-welcome), zastrzeżone adresy IP dla łączników nie są obecnie dostępne.

> [!IMPORTANT]
>
> Jeśli masz istniejące konfiguracje, zaktualizuj je **możliwie jak przed 1 września 2018** tak aby dołączyć i pasują do adresów IP w tych list dla regionów, gdy istnieją aplikacje Logic Apps.

Usługa Logic Apps nie obsługuje bezpośredniego połączenia z konta usługi Azure storage za pośrednictwem zapór. Aby uzyskać dostęp do tych kont magazynu, użyj tutaj jedną z opcji:

* Tworzenie [środowisko usługi integracji](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), które mogą łączyć się z zasobami w sieci wirtualnej platformy Azure.

* Jeśli używasz już usługi API Management, można użyć tej usługi, w tym scenariuszu. Aby uzyskać więcej informacji, zobacz [Architektura integracji przedsiębiorstw proste](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Adresy IP dla ruchu przychodzącego — tylko usługa Logic Apps

| Obszar | IP |
|--------|----|
| Australia Wschodnia | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Australia Południowo-Wschodnia | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazylia Południowa | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Kanada Środkowa | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Kanada Wschodnia | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Indie Środkowe | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Środkowe stany USA | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Azja Wschodnia | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| Wschodnie stany USA | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| Wschodnie stany USA 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Japonia Wschodnia | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Japonia Zachodnia | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Północno-środkowe stany USA | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Europa Północna | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Południowo-środkowe stany USA | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie Południowe | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Azja Południowo-Wschodnia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Zachodnio-środkowe stany USA | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa Zachodnia | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| Indie Zachodnie | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Zachodnie stany USA | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Zachodnie stany USA 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Zjednoczone Królestwo (południe) | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Zjednoczone Królestwo (zachód) | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Wychodzące adresy IP — & zarządzanych łączników usługi Logic Apps

| Obszar | IP aplikacji logiki | Adresów IP zarządzanych łączników |
|--------|---------------|-----------------------|
| Australia Wschodnia | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 |
| Australia Południowo-Wschodnia | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 |
| Brazylia Południowa | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Kanada Środkowa | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 |
| Kanada Wschodnia | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 |
| Indie Środkowe | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 |
| Środkowe stany USA | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 |
| Azja Wschodnia | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 |
| Wschodnie stany USA | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| Wschodnie stany USA 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 |
| Japonia Wschodnia | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 |
| Japonia Zachodnia | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 |
| Północno-środkowe stany USA | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 |
| Europa Północna | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 |
| Południowo-środkowe stany USA | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 |
| Indie Południowe | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 |
| Azja Południowo-Wschodnia | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 |
| Zachodnio-środkowe stany USA | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 |
| Europa Zachodnia | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 |
| Indie Zachodnie | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 |
| Zachodnie stany USA | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 |
| Zachodnie stany USA 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 |
| Zjednoczone Królestwo (południe) | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15 |
| Zjednoczone Królestwo (zachód) | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Kolejne kroki  

* Dowiedz się, jak [utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Dowiedz się więcej o [typowe przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)
