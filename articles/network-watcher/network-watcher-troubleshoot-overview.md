---
title: Wprowadzenie do zasobów, rozwiązywanie problemów w usłudze Azure Network Watcher | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie funkcji rozwiązywania problemów zasób usługi Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 65ce9e7d298131486ae4e5f3584c7975ca81e1ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684244"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Wprowadzenie do zasobów, rozwiązywanie problemów w usłudze Azure Network Watcher

Bramy sieci wirtualnej zapewnia łączność między zasobami lokalnymi i innymi sieciami wirtualnymi w obrębie platformy Azure. Monitorowania bram i ich połączeń jest niezbędne do zapewnienia nieprzerwanej komunikacji. Usługa Network Watcher umożliwia rozwiązywanie problemów z bram i połączeń. Ta funkcja może być wywoływany za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Po wywołaniu usługi Network Watcher diagnozuje kondycję bramy lub połączenia, a następnie zwraca odpowiednie wyniki. Żądanie jest długotrwałą transakcją. Wyniki są zwracane po zakończeniu diagnostyki.

![portal][2]

## <a name="results"></a>Wyniki

Zwrócone wyniki wstępne stworzyć ogólny obraz kondycji zasobu. Bardziej szczegółowych informacji może być dostarczona dla zasobów, jak pokazano w poniższej sekcji:

Poniższa lista jest wartości zwracanych z Rozwiązywanie problemów z interfejsu API:

* **Godzina rozpoczęcia** — ta wartość jest czasu uruchomienia wywołań interfejsu API rozwiązywania problemów.
* **wartość endTime** — ta wartość jest czas, kiedy zakończył się rozwiązywania problemów.
* **Kod** — ta wartość jest zła, jeśli wystąpi awaria jednego diagnostyki.
* **wyniki** -wyników jest zbiór wyników zwróconych na połączenia lub bramy sieci wirtualnej.
    * **Identyfikator** — ta wartość jest typu błędów.
    * **Podsumowanie** — ta wartość jest podsumowanie błędu.
    * **szczegółowe** — ta wartość zawiera szczegółowy opis błędu.
    * **recommendedActions** -tej właściwości to zbiór zalecane akcje do wykonania.
      * **actionText** — ta wartość zawiera tekst opisujący jaką akcję należy podjąć.
      * **actionUri** — ta wartość Określa identyfikator URI do dokumentacji na temat sposobu działania.
      * **actionUriText** — ta wartość jest krótki opis tekstowy akcji.

W poniższej tabeli przedstawiono typy różnych domenach błędów (identyfikator w obszarze wyniki z powyższej liście), które są dostępne, a w przypadku błędu tworzy dzienniki.

### <a name="gateway"></a>Brama

| Typ błędu | Reason | Log|
|---|---|---|
| NoFault | Po wykryciu braku błędów |Tak|
| GatewayNotFound | Nie można odnaleźć bramy lub bramy nie zostało aprowizowane. |Nie|
| PlannedMaintenance |  Wystąpienie bramy jest w trakcie konserwacji  |Nie|
| UserDrivenUpdate | Ten błąd występuje, gdy aktualizacja użytkownika jest w toku. Aktualizacja może być operacji zmiany rozmiaru. | Nie |
| VipUnResponsive | Ten błąd występuje, gdy podstawowe wystąpienie bramy nie można połączyć się z powodu błędu sondy kondycji. | Nie |
| PlatformInActive | Występuje problem z platformą. | Nie|
| ServiceNotRunning | Usługa podstawowy nie jest uruchomiona. | Nie|
| NoConnectionsFoundForGateway | Nawiązywanie połączeń nie istnieje w bramie. Ten błąd jest tylko to ostrzeżenie.| Nie|
| ConnectionsNotConnected | Połączenia są połączone. Ten błąd jest tylko to ostrzeżenie.| Tak|
| GatewayCPUUsageExceeded | Brama bieżące użycie procesora CPU jest > 95%. | Tak |

### <a name="connection"></a>Połączenie

| Typ błędu | Reason | Log|
|---|---|---|
| NoFault | Po wykryciu braku błędów |Tak|
| GatewayNotFound | Nie można odnaleźć bramy lub bramy nie zostało aprowizowane. |Nie|
| PlannedMaintenance | Wystąpienie bramy jest w trakcie konserwacji  |Nie|
| UserDrivenUpdate | Ten błąd występuje, gdy aktualizacja użytkownika jest w toku. Aktualizacja może być operacji zmiany rozmiaru.  | Nie |
| VipUnResponsive | Ten błąd występuje, gdy podstawowe wystąpienie bramy nie można połączyć się z powodu błędu sondy kondycji. | Nie |
| ConnectionEntityNotFound | Brak konfiguracji połączenia | Nie |
| ConnectionIsMarkedDisconnected | Połączenie jest oznaczone jako "odłączonego" |Nie|
| ConnectionNotConfiguredOnGateway | Usłudze podstawowej nie ma skonfigurowanego połączenia. | Tak |
| ConnectionMarkedStandby | Usłudze podstawowej jest oznaczana w stanie wstrzymania.| Yes|
| Authentication | Niezgodność klucza wstępnego | Yes|
| PeerReachability | Bramy równorzędnej jest nieosiągalny. | Tak|
| IkePolicyMismatch | Brama elementów równorzędnych ma zasady IKE, które nie są obsługiwane przez platformę Azure. | Tak|
| WfpParse Error | Wystąpił błąd podczas analizowania dziennika wywołanie. |Yes|

## <a name="supported-gateway-types"></a>Obsługiwane typy bram

W poniższej tabeli przedstawiono, które bram i połączeń są obsługiwane w rozwiązywaniu problemów usługi Network Watcher:

|  |  |
|---------|---------|
|**Typy bram**   |         |
|Sieć VPN      | Obsługiwane        |
|ExpressRoute | Nieobsługiwane |
|**Typy sieci VPN** | |
|Na podstawie trasy | Obsługiwane|
|Oparte na zasadach | Nieobsługiwane|
|**Typy połączeń**||
|IPSec| Obsługiwane|
|VNet2Vnet| Obsługiwane|
|ExpressRoute| Nieobsługiwane|
|VPNClient| Nieobsługiwane|

## <a name="log-files"></a>Pliki dziennika

Pliki zasobów do rozwiązywania problemów z dziennika są przechowywane na koncie magazynu po zakończeniu rozwiązywania problemów z zasobów. Na poniższej ilustracji przedstawiono przykład zawartość wywołania, które spowodowały błąd.

![plik zip][1]

> [!NOTE]
> W niektórych przypadkach tylko podzbiór Pliki dziennika są zapisywane do magazynu.

Aby uzyskać instrukcje dotyczące pobierania plików z konta usługi azure storage, zapoznaj się [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Inne narzędzie, które mogą być używane jest Eksploratora usługi Storage. Więcej informacji na temat Eksploratora usługi Storage można znaleźć tutaj z łącza: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** plik zawiera ogólne statystyki połączenia, w tym Bajty przychodzące i wychodzące, stan połączenia i godzinę, o której nawiązano połączenie.

> [!NOTE]
> Jeśli wywołanie do rozwiązywania problemów z interfejsu API zwraca dobrej kondycji, jedyną czynnością, które są zwracane w pliku zip jest **ConnectionStats.txt** pliku.

Zawartość tego pliku są podobne do poniższego przykładu:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** plik zawiera użycie procesora CPU i pamięci w czasie testowania.  Zawartość tego pliku jest podobny do poniższego przykładu:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** plik zawiera wszystkie błędy protokołu IKE, które zostały znalezione w trakcie monitorowania.

Poniższy przykład pokazuje zawartość pliku IKEErrors.txt. Błędy mogą się różnić w zależności od danego problemu.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

**Scrubbed wfpdiag.txt** plik dziennika zawiera dziennik wywołanie. Ten dziennik zawiera rejestrowanie porzucania pakietów i IKE/AuthIP błędów.

Poniższy przykład pokazuje zawartość pliku Scrubbed wfpdiag.txt. W tym przykładzie klucz współużytkowany połączenia nie jest poprawna, co wynika z trzeciego wiersza od dołu. Poniższy przykład to tylko fragment cały dziennik, ponieważ dziennik może być długi, w zależności od problemu.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

**Wfpdiag.txt.sum** plik jest wyświetlanie buforów i przetworzone zdarzenia dziennika.

Poniższy przykład jest zawartość pliku wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak i diagnozowanie problemów z połączeniem bramy lub bramy, zobacz [diagnozowanie problemów z komunikacją między sieciami](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
