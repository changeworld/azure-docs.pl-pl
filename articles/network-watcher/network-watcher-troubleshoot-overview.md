---
title: Wprowadzenie do rozwiązywania problemów z zasobami
titleSuffix: Azure Network Watcher
description: Ta strona zawiera omówienie możliwości rozwiązywania problemów z zasobami Obserwatora sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 199b4fc762919c2e3988f477c14d09fc23b0136b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840693"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Wprowadzenie do rozwiązywania problemów z zasobami w usłudze Azure Network Watcher

Bramy sieci wirtualnej zapewniają łączność między zasobami lokalnymi a innymi sieciami wirtualnymi na platformie Azure. Monitorowania bram i ich połączeń jest niezbędne do zapewnienia nieprzerwanej komunikacji. Kontrola sieci zapewnia możliwość rozwiązywania problemów z bramami i połączeniami. Możliwość można wywołać za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Po wywołaniu, Obserwator sieci diagnozuje kondycję bramy lub połączenia i zwraca odpowiednie wyniki. Żądanie jest długotrwałą transakcją. Wyniki są zwracane po zakończeniu diagnozy.

![portal][2]

## <a name="results"></a>Wyniki

Wstępne wyniki zwrócone dają ogólny obraz kondycji zasobu. Można uzyskać więcej informacji na temat zasobów, jak pokazano w poniższej sekcji:

Poniższa lista to wartości zwracane za pomocą interfejsu API rozwiązywania problemów:

* **startTime** — ta wartość jest czasem rozpoczęcia rozwiązywania problemów z wywołaniem interfejsu API.
* **endTime** — ta wartość jest czasem zakończenia rozwiązywania problemów.
* **kod** — ta wartość jest UnHealthy, jeśli występuje pojedynczy błąd diagnozy.
* **wyniki** — wyniki to zbiór wyników zwróconych na stronie Połączenie lub brama sieci wirtualnej.
    * **id** - Ta wartość jest typem usterki.
    * **podsumowanie** — ta wartość jest podsumowaniem usterki.
    * **szczegółowe** — ta wartość zawiera szczegółowy opis usterki.
    * **recommendedActions** - Ta właściwość jest zbiorem zalecanych działań do podjęcia.
      * **actionText** — ta wartość zawiera tekst opisujący, jakie działania należy podjąć.
      * **actionUri** — ta wartość zapewnia identyfikator URI do dokumentacji dotyczącej sposobu działania.
      * **actionUriText** — ta wartość jest krótkim opisem tekstu akcji.

W poniższych tabelach przedstawiono różne typy błędów (identyfikator w obszarze wyniki z poprzedniej listy), które są dostępne i jeśli błąd tworzy dzienniki.

### <a name="gateway"></a>Brama

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault (brak błędów) | Po wykryciu błędu |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można odnaleźć bramy lub bramy nie jest aprowizowana |Nie|
| PlannedMaintenance (planowana konserwacja) |  Wystąpienie bramy jest w trakcie konserwacji  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Ten błąd występuje, gdy trwa aktualizacja użytkownika. Aktualizacja może być operacją zmiany rozmiaru. | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Ten błąd występuje, gdy nie można skontaktować się z podstawowym wystąpieniem bramy z powodu błędu sondowania kondycji. | Nie |
| PlatformInActive (nieaktywna platforma) | Wystąpił problem z platformą. | Nie|
| UsługaNotRunning | Podstawowa usługa nie jest uruchomiona. | Nie|
| NoConnectionsFoundForGateway | W bramie nie ma żadnych połączeń. Ten błąd jest tylko ostrzeżeniem.| Nie|
| PołączeniaNiełączone | Połączenia nie są połączone. Ten błąd jest tylko ostrzeżeniem.| Tak|
| Portal GATEWAYCPUUsageExceeded | Bieżące użycie procesora CPU bramy wynosi > 95%. | Tak |

### <a name="connection"></a>Połączenie

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault (brak błędów) | Po wykryciu błędu |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można odnaleźć bramy lub bramy nie jest aprowizowana |Nie|
| PlannedMaintenance (planowana konserwacja) | Wystąpienie bramy jest w trakcie konserwacji  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Ten błąd występuje, gdy trwa aktualizacja użytkownika. Aktualizacja może być operacją zmiany rozmiaru.  | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Ten błąd występuje, gdy nie można skontaktować się z podstawowym wystąpieniem bramy z powodu błędu sondowania kondycji. | Nie |
| ConnectionEntityNotfound | Brak konfiguracji połączenia | Nie |
| ConnectionIsMarkedDisconnected | Połączenie jest oznaczone jako "odłączone" |Nie|
| ConnectionNotConfiguredOnGateway | Podstawowa usługa nie ma skonfigurowane połączenie. | Tak |
| ConnectionMarkedStandby (MarkedStandby) | Podstawowa usługa jest oznaczona jako wstrzymanie.| Tak|
| Uwierzytelnianie | Niezgodność kluczy wstępnych | Tak|
| Równość | Brama równorzędna jest niedoścignięci. | Tak|
| IkePolicyMismatch | Brama równorzędna ma zasady IKE, które nie są obsługiwane przez platformę Azure. | Tak|
| Błąd WfpParse | Wystąpił błąd podczas analizowania dziennika WFP. |Tak|

## <a name="supported-gateway-types"></a>Obsługiwane typy bram

W poniższej tabeli wymieniono, które bramy i połączenia są obsługiwane przez narzędzie do rozwiązywania problemów z obserwatorem sieciowym:

|  |  |
|---------|---------|
|**Typy bram**   |         |
|Sieć VPN      | Obsługiwane        |
|ExpressRoute | Nieobsługiwane |
|**Typy sieci VPN** | |
|Na podstawie trasy | Obsługiwane|
|Oparte na zasadach | Nieobsługiwane|
|**Typy połączeń**||
|IPsec| Obsługiwane|
|Sieci wirtualnej 2Vnet| Obsługiwane|
|ExpressRoute| Nieobsługiwane|
|VPNClient| Nieobsługiwane|

## <a name="log-files"></a>Pliki dziennika

Pliki dziennika rozwiązywania problemów z zasobami są przechowywane na koncie magazynu po zakończeniu rozwiązywania problemów z zasobami. Na poniższej ilustracji przedstawiono przykładową zawartość wywołania, które spowodowało błąd.

![plik zip][1]

> [!NOTE]
> W niektórych przypadkach tylko podzbiór plików dzienników jest zapisywany w magazynie.

Instrukcje dotyczące pobierania plików z kont usługi Azure Storage można znaleźć w [obszarze Wprowadzenie do usługi Azure Blob Storage przy użyciu programu .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Innym narzędziem, które może być używane, jest Eksplorator magazynu. Więcej informacji na temat Eksploratora magazynu można znaleźć tutaj pod następującym linkiem: [Eksplorator magazynu](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>Plik ConnectionStats.txt

**Plik ConnectionStats.txt** zawiera ogólne statystyki połączenia, w tym bajty przychodzące i wychodzące, stan połączenia i czas nawiązania połączenia.

> [!NOTE]
> Jeśli wywołanie interfejsu API rozwiązywania problemów zwraca w dobrej kondycji, jedyną rzeczą zwróconą w pliku zip jest plik **ConnectionStats.txt.**

Zawartość tego pliku jest podobna do poniższego przykładu:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CpuStats.txt

Plik **CPUStats.txt** zawiera użycie procesora i pamięć dostępną w czasie testowania.  Zawartość tego pliku jest podobna do następującego przykładu:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>Plik IKEErrors.txt

Plik **IKEErrors.txt** zawiera wszystkie błędy IKE, które zostały znalezione podczas monitorowania.

W poniższym przykładzie przedstawiono zawartość pliku IKEErrors.txt. Błędy mogą się różnić w zależności od problemu.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Szorowane-wfpdiag.txt

Plik dziennika **Scrubbed-wfpdiag.txt** zawiera dziennik wfp. Ten dziennik zawiera rejestrowanie upuszczania pakietów i błędów IKE/AuthIP.

W poniższym przykładzie przedstawiono zawartość pliku Scrubbed-wfpdiag.txt. W tym przykładzie klucz udostępniony połączenia nie został poprawny, jak widać z trzeciego wiersza od dołu. Poniższy przykład jest tylko fragment całego dziennika, jak dziennik może być długa w zależności od problemu.

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

Plik **wfpdiag.txt.sum** jest dziennikiem przedstawiającym bufory i zdarzenia przetworzone.

Poniższy przykład przedstawia zawartość pliku wfpdiag.txt.sum.
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

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zdiagnozować problem z połączeniem bramy lub bramy, zobacz [Diagnozowanie problemów z komunikacją między sieciami](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
