---
title: Obsługiwane scenariusze SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Scenariusze obsługiwane i ich szczegóły architektura SAP HANA na platformie Azure (wystąpienia duże)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/25/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b703f6d141005cf3cf29531a0586eebb61693a2
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754509"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Obsługiwane scenariusze dla wystąpień dużych HANA
W tym dokumencie opisano obsługiwane scenariusze wraz z ich szczegóły architektury dla HANA wystąpień duże (HLI).

>[!NOTE]
>Jeśli wymaganego scenariusza nie jest wymieniony w tym miejscu, skontaktuj się z zespołem zarządzania usługami firmy Microsoft do oceny wymagań.
Przed kontynuowaniem jednostki HLI inicjowania obsługi administracyjnej, należy zweryfikować projektu SAP lub partnerem implementacji usługi.

## <a name="terms-and-definitions"></a>— Warunki
Przyjrzyjmy się warunki i definicje używane w dokumencie.

- Identyfikator SID: Identyfikatora systemowego dla systemu HANA.
- HLI: Hana dużych wystąpienia.
- DR: Awaryjnego odzyskiwania lokacji.
- DR normalny: Instalator systemu z dedykowanym zasobów w celu odzyskiwania po awarii używane tylko.
- Multipurpose DR: System lokacji DR skonfigurowana do używania poza środowiskiem produkcyjnym wraz z wystąpienia produkcji skonfigurowany na potrzeby odzyskiwania po awarii zdarzeń. 
- Pojedynczy identyfikator SID: System z jedno jej wystąpienie.
- Wiele identyfikatorów SID: System z wielu wystąpień skonfigurowane. Skrót środowiska MCOS.


## <a name="overview"></a>Przegląd
Wystąpienia dużych HANA obsługują różne architektury celu wymagań biznesowych. Poniżej opisano scenariusze oraz ich konfiguracji. 

Projektowania architektury pochodnej jest wyłącznie z punktu widzenia infrastruktury i należy zapoznać się z programu SAP lub partnerów implementacji dla wdrożenia HANA. Jeśli scenariuszy nie są wyświetlane, skontaktuj się z zespół kont Microsoft, aby przejrzeć architektury i uzyskania rozwiązania dla Ciebie.

**Te architektury, są w pełni zgodne z projektem TDI (dostosowane Integracja danych) i obsługiwany przez SAP.**

W tym dokumencie opisano dwa składniki w każdej obsługiwanej architektury szczegóły:

- Ethernet
- Magazyn

### <a name="ethernet"></a>Ethernet

Każdy serwer elastycznie ma wstępnie skonfigurowane z zestawami ethernet. Poniżej przedstawiono szczegóły skonfigurowane w każdej jednostki HLI sieci ethernet.

- **A**: ten element jest używany dla/przez dostępu klienta.
- **B**: używany do komunikacji między węzłami. To jest skonfigurowana na wszystkich serwerach (niezależnie od topologii, żądanie), ale używana tylko w scenariuszach skalowalnego w poziomie.
- **C**: ten interfejs jest używany na komunikację magazynu węzła.
- **D**: ten interfejs jest używany na połączenie z urządzeniem ISCSI instalacji STONITH węzła. Ten interfejs jest skonfigurowane tylko podczas instalacji HSR jest wymagane.  

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzła do węzła |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | STONITH |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzła do węzła |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH |

Możesz używać interfejsów, na podstawie topologii skonfigurowane w jednostce HLI. Na przykład interfejsu "B" skonfigurowano tak, dla komunikacji między węzłami, co jest przydatne w przypadku topologii skalowalnego w poziomie skonfigurowane. W przypadku konfiguracji skalowanie w pionie jednego węzła ten interfejs nie jest używany. Przejrzyj wymagane scenariuszy (w dalszej części tego dokumentu), aby uzyskać więcej informacji na temat użycia interfejsu. 

W razie potrzeby można zdefiniować dodatkowe karty NIC samodzielnie. Jednak nie można zmienić konfigurację na istniejących kart interfejsu sieciowego.

>[!NOTE]
>Nadal może się okazać dodatkowych interfejsów, które są interfejsy fizyczne lub łączenia. Należy rozważyć wyżej interfejsów dla tej sprawy używana, można zignorować rest / lub być ograniczony z.


### <a name="storage"></a>Magazyn
Magazyn jest wstępnie skonfigurowana na podstawie topologii żądanie. Rozmiary woluminów i punktu instalacji różnią się w zależności od liczby serwerów, jednostki SKU i topologii skonfigurowane. Aby uzyskać więcej informacji, przejrzyj wymagane scenariuszy (w dalszej części tego dokumentu). Jeśli wymagana jest więcej pamięci masowej, możesz kupić ją w jedną jednostkę TB.

>[!NOTE]
>Punkt instalacjiusr/sap/<SID> jest łącze symboliczne do punktu instalacji/hana/udostępnionych.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

W diagramów architektury grafiki używane są następujące notacji:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

Poniżej wymieniono obsługiwane scenariusze:

1. Jeden węzeł z jednego identyfikatora SID
2. Pojedynczy węzeł MCOS
3. Jeden węzeł z DR (normalny)
4. Jeden węzeł z DR (Multipurpose)
5. HSR z STONITH
6. HSR z DR (normalne / Multipurpose) 
7. Tryb failover automatycznego hosta (1 + 1) 
8. Skalowanie w poziomie ze stanem gotowości
9. Skalowanie bez wstrzymania
10. Skalowanie w poziomie z DR



## <a name="1-single-node-with-one-sid"></a>1. Jeden węzeł z jednego identyfikatora SID

Ta topologia obsługuje jeden węzeł w skali konfigurację z jednego identyfikatora SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden węzeł jeden SID.png z](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie do użytku |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie do użytku |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/Hana/Shared/SID | HANA instalacji | 
|/Hana/Data/SID/mnt00001 | Instalowanie plików danych | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienników |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Pojedynczy węzeł MCOS

Ta topologia obsługuje jeden węzeł w skali konfigurację o wiele identyfikatorów SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![jednym node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie do użytku |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie do użytku |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/Hana/Shared/SID1 | Instalacja HANA SID1 | 
|/Hana/Data/SID1/mnt00001 | Instalowanie plików danych dla SID1 | 
|/Hana/log/SID1/mnt00001 | Pliki dziennika instalacji dla SID1 | 
|/Hana/logbackups/SID1 | Wykonaj ponownie dzienniki dla SID1 |
|/Hana/Shared/SID2 | Instalacja HANA SID2 | 
|/Hana/Data/SID2/mnt00001 | Instalowanie plików danych dla SID2 | 
|/Hana/log/SID2/mnt00001 | Pliki dziennika instalacji dla SID2 | 
|/Hana/logbackups/SID2 | Wykonaj ponownie dzienniki dla SID2 |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.
- Na rozmiar bazy danych w pamięci podstawie dystrybucji rozmiar woluminu. Zobacz [omówienie i architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się, jakie bazy danych rozmiary w pamięci są obsługiwane w środowisku multisid.

## <a name="3-single-node-with-dr-normal"></a>3. Jeden węzeł z DR (normalny)
 
Ta topologia obsługuje jeden węzeł w skali konfigurację z jednego lub wielu identyfikatorów SID z replikacji na podstawie magazynu do odzyskiwania po awarii lokacji w celu podstawowy identyfikator SID. Na diagramie tylko jednego identyfikatora SID jest opisany w lokacji głównej, ale multisid (MCOS) jest również obsługiwany.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden węzeł z dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie do użytku |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie do użytku |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/Hana/Shared/SID | Zainstaluj HANA dla identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Instalowanie plików danych dla identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji dla identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki dla identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu opiera się poza rozmiar bazy danych w pamięci. Zobacz [omówienie i architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się, jakie bazy danych rozmiary w pamięci są obsługiwane w środowisku multisid.
- W DR: woluminy i punkty instalacji są skonfigurowane (oznaczony jako "Wymagana przy instalowaniu HANA") w środowisku produkcyjnym instalację wystąpienia HANA w jednostce HLI odzyskiwania po awarii. 
- W DR: dane, logbackups i udostępnione woluminy (oznaczone jako "Replikacji magazynu"), które są replikowane za pomocą migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy awaryjnej. Zobacz [procedury awaryjnego odzyskiwania trybu failover](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) więcej szczegółów.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Jeden węzeł z DR (Multipurpose)
 
Ta topologia obsługuje jeden węzeł w skali konfigurację z jednego lub wielu identyfikatorów SID z replikacji na podstawie magazynu do odzyskiwania po awarii lokacji w celu podstawowy identyfikator SID. Na diagramie tylko jednego identyfikatora SID jest opisany w lokacji głównej, ale multisid (MCOS) jest również obsługiwany. W lokacji odzyskiwania po awarii HLI jest używany dla wystąpienia pytań i odpowiedzi podczas wykonywania operacji produkcyjnych z lokacji głównej. W czasie odzyskiwania po awarii trybu failover (lub test trybu failover) wystąpienie odpowiedzi na pytania w witrynie odzyskiwania po awarii jest wyłączona.

### <a name="architecture-diagram"></a>Diagram architektury  

![jeden węzeł dr multipurpose.png z](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie do użytku |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie do użytku |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W lokacji odzyskiwania po awarii**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Shared/QA-SID | Zainstaluj HANA dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/Data/QA-SID/mnt00001 | Instalowanie plików danych dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/log/QA-SID/mnt00001 | Pliki dziennika instalacji dla identyfikatorów SID pytań i odpowiedzi |
|/Hana/logbackups/QA-SID | Wykonaj ponownie dzienniki dla identyfikatora SID pytań i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu opiera się poza rozmiar bazy danych w pamięci. Zobacz [omówienie i architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się, jakie bazy danych rozmiary w pamięci są obsługiwane w środowisku multisid.
- W DR: woluminy i punkty instalacji są skonfigurowane (oznaczony jako "Wymagana przy instalowaniu HANA") w środowisku produkcyjnym instalację wystąpienia HANA w jednostce HLI odzyskiwania po awarii. 
- W DR: dane, logbackups i udostępnione woluminy (oznaczone jako "Replikacji magazynu"), które są replikowane za pomocą migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy awaryjnej. Zobacz [procedury awaryjnego odzyskiwania trybu failover](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) więcej szczegółów. 
- W DR: danych, logbackups dziennika, udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia QA") są skonfigurowane do instalacji wystąpienia pytań i odpowiedzi.

## <a name="5-hsr-with-stonith"></a>5. HSR z STONITH
 
Ta topologia obsługuje dwa węzły w konfiguracji replikacji systemu HANA (HSR). 

**Od tej chwili tej architektury jest obsługiwana tylko w przypadku systemu operacyjnego SUSE.**


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR z STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie do użytku |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Używany do STONITH |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie do użytku |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Używany do STONITH |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W węźle pomocniczym**|
|/Hana/Shared/SID | Zainstaluj HANA dla dodatkowej identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Instalowanie plików danych dla dodatkowej identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji dla dodatkowej identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki dla dodatkowej identyfikatora SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu opiera się poza rozmiar bazy danych w pamięci. Zobacz [omówienie i architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się, jakie bazy danych rozmiary w pamięci są obsługiwane w środowisku multisid.
- STONITH: Interwencja jest skonfigurowany do instalacji STONITH. Jednak użycie STONITH jest opcjonalne.


## <a name="6-hsr-with-dr"></a>6. HSR z DR
 
Ta topologia obsługuje dwa węzły w konfiguracji replikacji systemu HANA (HSR). Zarówno DR normalne i uniwersalne jest obsługiwana. 

Na diagramie multipurpose scenariusz jest opisany w przypadku gdy w lokacji odzyskiwania po awarii, jednostka HLI jest używana dla wystąpienia pytań i odpowiedzi, podczas wykonywania operacji produkcyjnych z lokacji głównej. W czasie odzyskiwania po awarii trybu failover (lub test trybu failover) wystąpienie odpowiedzi na pytania w witrynie odzyskiwania po awarii jest wyłączona. 



### <a name="architecture-diagram"></a>Diagram architektury  

![HSR z DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie do użytku |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Używany do STONITH |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie do użytku |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Używany do STONITH |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym w lokacji głównej**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W węźle pomocniczym w lokacji głównej**|
|/Hana/Shared/SID | Zainstaluj HANA dla dodatkowej identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Instalowanie plików danych dla dodatkowej identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji dla dodatkowej identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki dla dodatkowej identyfikatora SID |
|**W lokacji odzyskiwania po awarii**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Shared/QA-SID | Zainstaluj HANA dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/Data/QA-SID/mnt00001 | Instalowanie plików danych dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/log/QA-SID/mnt00001 | Pliki dziennika instalacji dla identyfikatorów SID pytań i odpowiedzi |
|/Hana/logbackups/QA-SID | Wykonaj ponownie dzienniki dla identyfikatora SID pytań i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu opiera się poza rozmiar bazy danych w pamięci. Zobacz [omówienie i architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się, jakie bazy danych rozmiary w pamięci są obsługiwane w środowisku multisid.
- STONITH: Interwencja jest skonfigurowany do instalacji STONITH. Jednak użycie STONITH jest opcjonalne.
- W DR: **wymagane są dwa zestawy woluminów magazynu** replikacji węzła podstawowego i pomocniczego.
- W DR: woluminy i punkty instalacji są skonfigurowane (oznaczony jako "Wymagana przy instalowaniu HANA") w środowisku produkcyjnym instalację wystąpienia HANA w jednostce HLI odzyskiwania po awarii. 
- W DR: dane, logbackups i udostępnione woluminy (oznaczone jako "Replikacji magazynu"), które są replikowane za pomocą migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy awaryjnej. Zobacz [procedury awaryjnego odzyskiwania trybu failover](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) więcej szczegółów. 
- W DR: danych, logbackups dziennika, udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia QA") są skonfigurowane do instalacji wystąpienia pytań i odpowiedzi.


## <a name="7-host-auto-failover-11"></a>7. Tryb failover automatycznego hosta (1 + 1)
 
Ta topologia obsługuje dwa węzły w konfiguracji hosta automatycznej pracy awaryjnej. Brak jednego węzła z rolą wzorca/proces roboczy i innych jako nieaktywne. **Ten scenariusz jest obsługiwany tylko w przypadku S/4 HANA SAP.** Zobacz Uwaga OSS "[2408419 - S SAP/4HANA — Obsługa wielowęzłowego](https://launchpad.support.sap.com/#/notes/2408419)" Aby uzyskać więcej szczegółów.



### <a name="architecture-diagram"></a>Diagram architektury  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł do komunikacji węzeł |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł do komunikacji węzeł |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węzłach głównego i będące w gotowości**|
|/ hana/udostępnionych | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |



### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.
- W stan wstrzymania: woluminy i punkty instalacji są skonfigurowane (oznaczony jako "Wymagana przy instalowaniu HANA") wystąpienia HANA instalacji na rezerwy jednostki.
 

## <a name="8-scale-out-with-standby"></a>8. Skalowanie w poziomie ze stanem gotowości
 
Ta topologia obsługuje wiele węzłów w skalowania konfiguracji. Brak jednego węzła z roli wzorca, co najmniej jeden węzeł z rolą proces roboczy i co najmniej jeden węzeł jako nieaktywne. Jednak może istnieć tylko jeden węzeł główny na dowolnym etapie czasu.


### <a name="architecture-diagram"></a>Diagram architektury  

![skalowania nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł do komunikacji węzeł |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł do komunikacji węzeł |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węzłach głównym, proces roboczy i gotowości**|
|/ hana/udostępnionych | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |


## <a name="9-scale-out-without-standby"></a>9. Skalowanie bez wstrzymania
 
Ta topologia obsługuje wiele węzłów w skalowania konfiguracji. Brak jednego węzła z rolą wzorca i jeden lub tryb węzłów z rolą proces roboczy. Jednak może istnieć tylko jeden węzeł główny na dowolnym etapie czasu.


### <a name="architecture-diagram"></a>Diagram architektury  

![nm.png skalowania w poziomie](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł do komunikacji węzeł |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł do komunikacji węzeł |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węzłach wzorca i proces roboczy**|
|/ hana/udostępnionych | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Skalowanie w poziomie z DR
 
Ta topologia obsługuje wiele węzłów w skalowania w poziomie z odzyskiwania po awarii. Zarówno normalnych, jak i uniwersalne odzyskiwania po awarii jest obsługiwana. Na diagramie przedstawiono tylko jednemu celowi odzyskiwania po awarii. Ta topologia z lub bez rezerwy węzeł może wysłać żądanie.


### <a name="architecture-diagram"></a>Diagram architektury  

![skalowania w poziomie z dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNEJ KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klientowi HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł do komunikacji węzeł |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie do użytku |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klientowi HLI |
| B | TYP II | sieci VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł do komunikacji węzeł |
| C | TYP II | sieci VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł do magazynu |
| D | TYP II | sieci VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie do użytku |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/ hana/udostępnionych | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W węźle DR**|
|/ hana/udostępnionych | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Zainstaluj pliki danych w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest łącze symboliczne do /hana/shared/SID.
-  W DR: woluminy i punkty instalacji są skonfigurowane (oznaczony jako "Wymagana przy instalowaniu HANA") w środowisku produkcyjnym instalację wystąpienia HANA w jednostce HLI odzyskiwania po awarii. 
- W DR: dane, logbackups i udostępnione woluminy (oznaczone jako "Replikacji magazynu"), które są replikowane za pomocą migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy awaryjnej. Zobacz [procedury awaryjnego odzyskiwania trybu failover](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) więcej szczegółów. 


## <a name="next-steps"></a>Kolejne kroki
- Zobacz [infrastruktury i łączności](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) dla HLI
- Zobacz [wysokiej dostępności i odzyskiwania po awarii](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) dla HLI