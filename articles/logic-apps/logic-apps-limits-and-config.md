---
title: Limity i konfiguracja — Azure Logic Apps | Microsoft Docs
description: Limity i wartości konfiguracji usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: 95df72875338b6964f42075404cf9c30ba132f9d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900208"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informacje o limitach i konfiguracji Azure Logic Apps

W tym artykule opisano limity i szczegóły konfiguracji dotyczące tworzenia i uruchamiania zautomatyzowanych przepływów pracy przy użyciu Azure Logic Apps. Aby uzyskać Microsoft Flow, zobacz [limity i konfiguracja w Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limity definicji

Poniżej przedstawiono limity dla jednej definicji aplikacji logiki:

| Name | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcje na przepływ pracy | 500 | Aby zwiększyć ten limit, można w razie potrzeby dodać zagnieżdżone przepływy pracy. |
| Dozwolona głębokość zagnieżdżenia dla akcji | 8 | Aby zwiększyć ten limit, można w razie potrzeby dodać zagnieżdżone przepływy pracy. |
| Przepływy pracy na region na subskrypcję | 1000 | |
| Wyzwalacze na przepływ pracy | 10 | Praca w widoku kodu, a nie Projektant |
| Limit przypadków przełączania zakresu | 25 | |
| Zmienne na przepływ pracy | 250 | |
| Liczba znaków na wyrażenie | 8192 | |
| Maksymalny rozmiar dla`trackedProperties` | 16 000 znaków |
| `action` Nazwa lub`trigger` | 80 znaków | |
| Długość`description` | 256 znaków | |
| Długość`parameters` | 50 | |
| Długość`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Czas trwania i limity przechowywania

Poniżej przedstawiono limity dla pojedynczego uruchomienia aplikacji logiki:

| Name | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Czas trwania przebiegu | 90 dni | 365 dni | Aby zmienić domyślny limit, zobacz [zmiana czasu trwania](#change-duration). |
| Przechowywanie magazynu | 90 dni od czasu rozpoczęcia przebiegu | 365 dni | Aby zmienić domyślny limit, zobacz [zmiana przechowywania magazynu](#change-retention). |
| Minimalny interwał cyklu | 1 s | 1 s ||
| Maksymalny interwał cyklu | 500 dni | 500 dni ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Czas trwania przebiegu zmian i przechowywanie magazynu

Aby zmienić domyślny limit czasu wykonywania i przechowywania magazynu, wykonaj następujące czynności. Jeśli musisz przejść powyżej maksymalnego limitu, [skontaktuj się z zespołem Logic Apps](mailto://logicappsemail@microsoft.com) , aby uzyskać pomoc dotyczącą Twoich wymagań.

1. W Azure Portal w menu aplikacji logiki wybierz pozycję **Ustawienia przepływu pracy**.

2. W obszarze **Opcje środowiska uruchomieniowego**na liście **przechowywanie historii uruchamiania w dniach** wybierz pozycję niestandardowa.

3. Wprowadź lub przeciągnij suwak, aby określić żądaną liczbę dni.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limity współbieżności, zapętlania i departii

Poniżej przedstawiono limity dla pojedynczego uruchomienia aplikacji logiki:

| Name | Limit | Uwagi |
| ---- | ----- | ----- |
| Współbieżność wyzwalacza | * Nieograniczone, gdy kontrola współbieżności jest wyłączona <p><p>* 25 jest domyślnym limitem, gdy włączony jest formant współbieżności, którego nie można cofnąć po włączeniu formantu. Można zmienić wartość domyślną z przedziału od 1 do 50 włącznie. | Ten limit opisuje największą liczbę wystąpień aplikacji logiki, które mogą być uruchamiane w tym samym czasie lub równolegle. <p><p>Aby zmienić domyślny limit na wartość z przedziału od 1 do 50 włącznie, zobacz [Zmienianie wyzwalacza współbieżności](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) lub [wystąpień wyzwalaczy sekwencyjnie](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maksymalna liczba oczekujących przebiegów | Po włączeniu kontroli współbieżności minimalna liczba oczekujących uruchomień wynosi 10 i liczbę współbieżnych uruchomień (współbieżność wyzwalacza). Można zmienić maksymalną liczbę do 100 włącznie. | Ten limit opisuje największą liczbę wystąpień aplikacji logiki, które mogą czekać na uruchomienie, gdy w aplikacji logiki jest już uruchomiona Maksymalna liczba wystąpień współbieżnych. <p><p>Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Elementy tablicy foreach | 100,000 | Ten limit opisuje największą liczbę elementów tablicy, które może przetworzyć pętla for each. <p><p>Aby filtrować większe tablice, można użyć [akcji zapytania](../connectors/connectors-native-query.md). |
| Współbieżność foreach | 20 jest domyślnym limitem, gdy kontrola współbieżności jest wyłączona. Można zmienić wartość domyślną z przedziału od 1 do 50 włącznie. | Ten limit to największą liczbę iteracji pętli "for each", które mogą być uruchamiane w tym samym czasie lub równolegle. <p><p>Aby zmienić domyślny limit na wartość z przedziału od 1 do 50 włącznie, zobacz [zmiana "dla każdego" ograniczenia współbieżności](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) lub [uruchomienie "dla każdej" pętli sekwencyjnie](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Elementy SplitOn | 100,000 | Dla wyzwalaczy, które zwracają tablicę, można określić wyrażenie używające właściwości "SplitOn", które [dzieli lub departia elementy tablicy w wielu wystąpieniach przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) do przetworzenia, zamiast używać pętli "foreach". To wyrażenie odwołuje się do tablicy, która ma zostać użyta do utworzenia i uruchomienia wystąpienia przepływu pracy dla każdego elementu tablicy. |
| Do czasu iteracji | 5,000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limity przepływności

Poniżej przedstawiono limity dla jednej definicji aplikacji logiki:

### <a name="multi-tenant-logic-apps-service"></a>Usługa Logic Apps z wieloma dzierżawcami

| Name | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcja: Wykonania na 5 minut | 100 000 jest limitem domyślnym, ale 300 000 jest maksymalnym limitem. | Aby zmienić domyślny limit, zobacz [Uruchamianie aplikacji logiki w trybie "Wysoka przepływność"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), która jest w wersji zapoznawczej. Można też rozłożyć obciążenie na więcej niż jedną aplikację logiki w razie potrzeby. |
| Akcja: Współbieżne wywołania wychodzące | ~2,500 | Możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
| Punkt końcowy środowiska uruchomieniowego: Współbieżne wywołania przychodzące | ~1,000 | Możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
| Punkt końcowy środowiska uruchomieniowego: Wywołania odczytu na 5 minut  | 60,000 | W razie potrzeby można dystrybuować obciążenia w więcej niż jednej aplikacji. |
| Punkt końcowy środowiska uruchomieniowego: Wywołania wywołań na 5 minut | 45,000 | W razie potrzeby można dystrybuować obciążenia w więcej niż jednej aplikacji. |
| Przepływność zawartości na 5 minut | 600 MB | W razie potrzeby można dystrybuować obciążenia w więcej niż jednej aplikacji. |
||||

### <a name="integration-service-environment-ise"></a>Środowisko usługi integracji (ISE)

| Name | Limit | Uwagi |
|------|-------|-------|
| Limit wykonywania jednostki podstawowej | Ograniczanie systemu w przypadku, gdy pojemność infrastruktury osiągnie 80% | Zapewnia 4 000 wykonania akcji na minutę, czyli ~ 160 000 000 wykonań akcji miesięcznie | |
| Limit wykonywania jednostek skalowania | Ograniczanie systemu w przypadku, gdy pojemność infrastruktury osiągnie 80% | Każda jednostka skalowania może dostarczyć ~ 2 000 dodatkowych wykonań akcji na minutę, czyli ~ 80 000 000 więcej wykonań akcji miesięcznie | |
| Maksymalna liczba jednostek skalowania, które można dodać | 10 | |
||||

Aby przekroczyć te limity podczas normalnego przetwarzania lub uruchomić testy obciążenia, które mogą wykraczać ponad te limity, [skontaktuj się z zespołem Logic Apps](mailto://logicappsemail@microsoft.com) , aby uzyskać pomoc dotyczącą Twoich wymagań.

<a name="request-limits"></a>

## <a name="http-limits"></a>Limity protokołu HTTP

Poniżej znajdują się limity dotyczące pojedynczego żądania HTTP lub wywołania łącznika synchronicznego:

#### <a name="timeout"></a>limit czasu

Niektóre operacje łączników powodują wywołania asynchroniczne lub Nasłuchuj żądań elementu webhook, dlatego limit czasu dla tych operacji może być dłuższy niż te limity. Aby uzyskać więcej informacji, zobacz szczegóły techniczne dla określonego łącznika oraz [wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Żądanie wychodzące | 120 sekund | 240 sekund | Aby działały dłużej, użyj [wzorca sondowania asynchronicznego](../logic-apps/logic-apps-create-api-app.md#async-pattern) lub [pętli do until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Odpowiedź synchroniczna | 120 sekund | 240 sekund | W przypadku oryginalnego żądania pobrania odpowiedzi wszystkie kroki odpowiedzi muszą zakończyć się w limicie, chyba że zostanie wywołana inna aplikacja logiki jako zagnieżdżony przepływ pracy. Aby uzyskać więcej informacji, zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Rozmiar komunikatu

| Name | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Rozmiar komunikatu | 100 MB | 200 MB | Aby obejść ten limit, zobacz [Obsługa dużych komunikatów przy użyciu fragmentów](../logic-apps/logic-apps-handle-large-messages.md). Jednak niektóre łączniki i interfejsy API mogą nie obsługiwać fragmentacji lub nawet domyślnego limitu. |
| Rozmiar komunikatu z fragmentem | 1 GB | 5 GB | Ten limit ma zastosowanie do akcji, które natywnie obsługują fragmenty i umożliwiają włączenie fragmentu w konfiguracji środowiska uruchomieniowego. <p>W przypadku środowiska usługi integracji aparat Logic Apps obsługuje ten limit, ale łączniki mają własne ograniczenia dotyczące limitu aparatu, na przykład, zobacz [Łącznik usługi Azure Blob Storage](/connectors/azureblob/). Aby uzyskać więcej informacji, zobacz [Obsługa dużych komunikatów przy użyciu fragmentów](../logic-apps/logic-apps-handle-large-messages.md). |
| Limit szacowania wyrażeń | 131 072 znaków | 131 072 znaków | Wyrażenia `@concat()`, `@base64()` ,`@string()` nie mogą być dłuższe niż ten limit. |
|||||

#### <a name="retry-policy"></a>Zasady ponawiania

| Name | Limit | Uwagi |
| ---- | ----- | ----- |
| Liczba ponownych prób | 90 | Wartość domyślna to 4. Aby zmienić wartość domyślną, użyj [parametru zasady ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maksymalne opóźnienie ponawiania | 1 dzień | Aby zmienić wartość domyślną, użyj [parametru zasady ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Opóźnienie minimalnej próby | 5 sekund | Aby zmienić wartość domyślną, użyj [parametru zasady ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limity łączników niestandardowych

Poniżej przedstawiono limity łączników niestandardowych, które można tworzyć z interfejsów API sieci Web.

| Name | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Liczba łączników niestandardowych | 1000 na subskrypcję platformy Azure | 1000 na subskrypcję platformy Azure ||
| Liczba żądań na minutę dla łącznika niestandardowego | 500 żądań na minutę za połączenie | 2 000 żądań na minutę na *Łącznik niestandardowy* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Tożsamości zarządzane

| Name | Limit |
| ---- | ----- |
| Liczba aplikacji logiki z tożsamościami zarządzanymi przypisanymi do systemu na subskrypcję platformy Azure | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity kont integracji

Dla każdej subskrypcji platformy Azure obowiązują następujące limity kont integracji:

* Jedno konto integracji w [warstwie Bezpłatna](../logic-apps/logic-apps-pricing.md#integration-accounts) na region platformy Azure

* 1 000 łączne konta integracji, w tym konta integracji w dowolnych [środowiskach usług integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zarówno dla [deweloperów, jak i Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Każdy ISE, niezależnie od tego, czy [deweloper lub Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), jest ograniczony do 5 łącznych kont integracji:

  | JEDNOSTKA SKU ISE | Limit |
  |---------|-------|
  | **Premium** | 5 — tylko w [warstwie Standardowa](../logic-apps/logic-apps-pricing.md#integration-accounts) , bez wolnego lub Basic |
  | **Developer** | 5 — bezpłatnie (ograniczone do 1), Standard lub oba, ale bez podstawowego |
  |||

Dodatkowe koszty dotyczą kont integracji, które są dodawane poza kontami integracji, które są dołączone do ISE. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limity artefaktów na konto integracji

Poniżej przedstawiono limity liczby artefaktów dla każdej warstwy konta integracji. Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/). Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla kont integracji, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Korzystaj z warstwy Bezpłatna tylko w scenariuszach poznawczych, a nie w scenariuszach produkcyjnych. Ta warstwa ogranicza przepływność i użycie oraz nie ma umowy dotyczącej poziomu usług (SLA).

| Artefakt | Wolne | Podstawowa | Standardowa (Standard) |
|----------|------|-------|----------|
| Umowy handlowe EDI | 10 | 1 | 1000 |
| Partnerzy handlowi EDI | 25 | 2 | 1000 |
| Maps | 25 | 500 | 1000 |
| Schematy | 25 | 500 | 1000 |
| Zestawy | 10 | 25 | 1000 |
| Certyfikaty | 25 | 2 | 1000 |
| Konfiguracje partii | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity pojemności artefaktów

| Artefakt | Limit | Uwagi |
| -------- | ----- | ----- |
| Zestaw | 8 MB | Aby przekazać pliki o rozmiarze większym niż 2 MB, użyj [konta usługi Azure Storage i kontenera obiektów BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Map (plik XSLT) | 8 MB | Aby przekazać pliki o rozmiarze większym niż 2 MB, użyj [Azure Logic Apps interfejsu API REST — Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Schemat | 8 MB | Aby przekazać pliki o rozmiarze większym niż 2 MB, użyj [konta usługi Azure Storage i kontenera obiektów BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Punkt końcowy środowiska uruchomieniowego | Limit | Uwagi |
|------------------|-------|-------|
| Wywołania odczytu na 5 minut | 60,000 | W razie potrzeby można rozesłać obciążenie do więcej niż jednego konta. |
| Wywołania wywołań na 5 minut | 45,000 | W razie potrzeby można rozesłać obciążenie do więcej niż jednego konta. |
| Śledzenie wywołań na 5 minut | 45,000 | W razie potrzeby można rozesłać obciążenie do więcej niż jednego konta. |
| Blokowanie współbieżnych wywołań | ~1,000 | Możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Rozmiar komunikatu protokołu B2B (AS2, X12, EDIFACT)

Poniżej przedstawiono limity rozmiaru komunikatów, które dotyczą protokołów B2B:

| Name | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 — 100 MB<br>V1 – 50 MB | v2 — 200 MB <br>V1 – 50 MB | Dotyczy kodowania i kodowania |
| X12 | 50 MB | 50 MB | Dotyczy kodowania i kodowania |
| EDIFACT | 50 MB | 50 MB | Dotyczy kodowania i kodowania |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Wyłączanie lub usuwanie aplikacji logiki

Po wyłączeniu aplikacji logiki nie są tworzone żadne nowe uruchomienia. Wszystkie w toku i oczekujące przebiegi są kontynuowane do czasu zakończenia, co może zająć trochę czasu.

Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Konfiguracja zapory: Adresy IP

Wszystkie aplikacje logiki w tym samym regionie używają tych samych zakresów adresów IP. Aby zapewnić obsługę wywołań, które bezpośrednio tworzą aplikacje logiki przy użyciu [protokołu HTTP](../connectors/connectors-native-http.md), [http + Swagger](../connectors/connectors-native-http-swagger.md)i innych żądań HTTP, skonfiguruj zapory za pomocą *wszystkich* [przychodzących](#inbound) *i* [wychodzących](#outbound) adresów IP używanych przez usługę Logic Apps , w oparciu o regiony, w których istnieją aplikacje logiki. Te adresy są wyświetlane w obszarze nagłówki **przychodzące** i wychodzące w tej sekcji i są sortowane według regionów. 

Aby zapewnić obsługę wywołań wywoływanych przez [Łączniki zarządzane przez firmę Microsoft](../connectors/apis-list.md) , należy skonfigurować zaporę ze *wszystkimi* wychodzącymi adresami IP używanymi przez te łączniki w oparciu o regiony, w których istnieją aplikacje logiki. [](#outbound) Te adresy są wyświetlane pod nagłówkiem wychodzącym w tej sekcji i są sortowane według regionów.

W przypadku aplikacji logiki, które działają w środowisku usługi integracji (ISE), upewnij się, że [te porty są otwarte](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#ports).

W przypadku [Azure Government](../azure-government/documentation-government-overview.md) i [platformy Azure z Chin](https://docs.microsoft.com/azure/china/), zastrzeżone adresy IP dla łączników nie są obecnie dostępne.

> [!IMPORTANT]
>
> Jeśli masz istniejące konfiguracje, zaktualizuj je **tak szybko, jak to możliwe, przed 1 września 2018,** aby uwzględnić i dopasować adresy IP z tych list dla regionów, w których istnieją aplikacje logiki.

Logic Apps nie obsługuje bezpośredniego łączenia się z kontami usługi Azure Storage za pomocą zapór. Aby uzyskać dostęp do tych kont magazynu, użyj jednej z następujących opcji:

* Utwórz [środowisko usługi integracji](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), które może łączyć się z zasobami w sieci wirtualnej platformy Azure.

* Jeśli używasz już API Management, możesz użyć tej usługi w tym scenariuszu. Aby uzyskać więcej informacji, zobacz [prosta architektura integracji przedsiębiorstwa](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Przychodzące adresy IP — tylko usługa Logic Apps

| Region | IP |
|--------|----|
| Australia Wschodnia | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Australia Południowo-Wschodnia | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazylia Południowa | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Kanada Środkowa | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Kanada Wschodnia | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Indie Środkowe | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Środkowe stany USA | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Azja Wschodnia | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| East US | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| Wschodnie stany USA 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Japonia Wschodnia | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Japonia Zachodnia | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Środkowo-północne stany USA | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Europa Północna | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Środkowo-południowe stany USA | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie Południowe | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Azja Południowo-Wschodnia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Środkowo-zachodnie stany USA | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa Zachodnia | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| Indie Zachodnie | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Zachodnie stany USA | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Zachodnie stany USA 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Południowe Zjednoczone Królestwo | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Zachodnie Zjednoczone Królestwo | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Wychodzące adresy IP — łączniki zarządzane & usługi Logic Apps Service

| Region | Adres IP Logic Apps | Adres IP łączników zarządzanych |
|--------|---------------|-----------------------|
| Australia Wschodnia | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10 |
| Australia Południowo-Wschodnia | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.77.50.240 - 13.77.50.255, 13.70.136.174 |
| Brazylia Południowa | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 191.233.203.192 - 191.233.203.207, 104.41.59.51 | 
| Kanada Środkowa | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126 |
| Kanada Wschodnia | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.242.35.152 |
| Indie Środkowe | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 104.211.81.192 - 104.211.81.207, 52.172.211.12 |
| Środkowe stany USA | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 52.173.245.164 |
| Azja Wschodnia | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 52.175.23.169 |
| East US | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| Wschodnie stany USA 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154 |
| Japonia Wschodnia | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.78.108.0 - 13.78.108.15, 13.71.153.19 |
| Japonia Zachodnia | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 104.215.61.248 |
| Środkowo-północne stany USA | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161 |
| Europa Północna | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68 |
| Środkowo-południowe stany USA | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 104.214.19.48 - 104.214.19.63, 13.65.86.57 |
| Indie Południowe | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 40.78.194.240 - 40.78.194.255, 13.71.125.22 |
| Azja Południowo-Wschodnia | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 52.187.68.19 |
| Środkowo-zachodnie stany USA | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.102.22 |
| Europa Zachodnia | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 52.174.88.118 |
| Indie Zachodnie | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.189.218 |
| Zachodnie stany USA | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 | 40.112.243.160 - 40.112.243.175, 104.42.122.49 |
| Zachodnie stany USA 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 52.183.78.157 |
| Południowe Zjednoczone Królestwo | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.148.0 - 51.140.148.15, 51.140.80.51 |
| Zachodnie Zjednoczone Królestwo | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Następne kroki  

* Dowiedz się, jak [utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Poznaj [typowe przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)
