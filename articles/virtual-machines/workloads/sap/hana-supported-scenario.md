---
title: Obsługiwane scenariusze platformy SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Scenariusze obsługiwane i ich szczegóły architektury dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia)
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
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0e9d57c224150454677a03462368038ed8c63edf
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576497"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Obsługiwane scenariusze dla dużych wystąpień HANA
W tym dokumencie opisano obsługiwane scenariusze wraz z ich szczegółami dotyczącymi architektury dla platformy HANA wystąpienia duże (HLI).

>[!NOTE]
>Jeśli wymaganego scenariusza nie jest tutaj wymieniony, skontaktuj się z zespołem zarządzania usługami firmy Microsoft do oceny wymagań.
Przed kontynuowaniem aprowizacja jednostek HLI, sprawdzanie poprawności projektu SAP lub partnerem implementacji usługi.

## <a name="terms-and-definitions"></a>Terminy i definicje
Przyjrzyjmy się terminy i definicje w dokumencie.

- Identyfikator SID: Identyfikator systemowy dla systemu HANA.
- HLI: Dużymi wystąpieniami platformy Hana.
- Odzyskiwanie po awarii: Lokacji odzyskiwania po awarii.
- Normalny odzyskiwania po awarii: konfiguracji systemu z dedykowanymi zasobami w celu odzyskiwania po awarii, używane tylko.
- Multipurpose odzyskiwania po awarii: System lokacji odzyskiwania po awarii, skonfigurowany do używania w środowisku nieprodukcyjnym, wraz z wystąpienia produkcyjnego skonfigurowany do używania zdarzenia odzyskiwania po awarii. 
- Pojedynczy identyfikator SID: System z jednym wystąpieniem zainstalowane.
- Wiele identyfikatorów SID: System z wieloma wystąpieniami skonfigurowane. Skrót środowiska MCOS.


## <a name="overview"></a>Przegląd
Dużych wystąpień HANA obsługują różne architektury, aby osiągnąć Twoje wymagania biznesowe. Poniższa lista obejmuje scenariusze oraz szczegóły dotyczące ich konfiguracji. 

Projektowania architektury pochodnej jest wyłącznie z punktu widzenia infrastruktury i należy zapoznać się z SAP lub partnerów wdrożenia dla wdrożenia platformy HANA. W przypadku scenariuszy nie są wyświetlane, skontaktuj się z zespołu kont Microsoft, aby zapoznać się z architekturą i pobieraj rozwiązaniem dla Ciebie.

**Te architektury są w pełni zgodne z konstrukcją TDI (dostosowane integracji danych) i obsługiwanych przez oprogramowanie SAP.**

W tym dokumencie opisano szczegóły dwa składniki w każdej obsługiwanej architektury:

- Ethernet
- Magazyn

### <a name="ethernet"></a>Ethernet

Każdy serwer aprowizacji ma wstępnie skonfigurowany zestawy interfejsów ethernet. Poniżej przedstawiono szczegółowe informacje o interfejsy sieci ethernet, skonfigurowane w każdej jednostce HLI.

- **A**: ten interfejs jest wykorzystywany do/przez dostępu klienta.
- **B**: ten interfejs jest używany do komunikacji między węzłami. Ten interfejs jest skonfigurowana na wszystkich serwerach (niezależnie od topologii, na żądanie), ale tylko używane dla 
- scenariusze skalowalnego w poziomie.
- **C**: ten interfejs jest używany dla węzła do łączności z magazynu.
- **D**: ten interfejs jest używany na połączenie z urządzeniem ISCSI pomocą metody STONITH instalacji węzła. Ten interfejs jest skonfigurowane tylko zleconą ustawienia replikacji systemu HANA.  

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzła do węzła |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | POMOCĄ METODY STONITH |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzła do węzła |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | POMOCĄ METODY STONITH |

Możesz użyć interfejsów oparte na topologii skonfigurowane w jednostce HLI. Na przykład "B" interfejs skonfigurowano tak, komunikacji między węzłami, co jest przydatne w przypadku topologii skalowalnego w poziomie, skonfigurowana. Ten interfejs nie jest używana w przypadku konfiguracji skalowania w górę jednego węzła. Aby uzyskać więcej informacji na temat użycia interfejsu, należy przejrzeć wymagane scenariuszy (w dalszej części tego dokumentu). 

Jeśli to konieczne, można zdefiniować dodatkowe karty interfejsu Sieciowego na własną rękę. Jednak nie można zmienić konfigurację na istniejących kart sieciowych.

>[!NOTE]
>Nadal może się okazać dodatkowe interfejsy, które są interfejsy fizyczne lub łączenia. Należy rozważyć interfejsów wymienionych powyżej przypadku używany, można zignorować rest / lub nie być zgodne z.

Powinien wyglądać rozkładu dla jednostek o dwa adresy IP:

- Ethernet "A" powinien mieć przypisanego adresu IP spoza zakresu adresów puli adresów IP serwera, który przesłanych do firmy Microsoft. Ten adres IP jest używany do przechowywania w/etc/hosts systemu operacyjnego.

- Ethernet "C" powinien mieć przypisanego adresu IP, używanej do komunikacji z systemu plików NFS. W związku z tym, czy te adresy **nie** muszą być przechowywane w etc/hosts w celu zezwolenia na ruch wystąpienie lub wystąpienia w ramach dzierżawy.

W przypadku wdrażania replikacji systemu HANA lub HANA skalowalnego w poziomie konfiguracji bloku, z dwoma adresami IP, przypisać nie jest odpowiednia. Jeśli masz dwa adresy IP tylko i chce wdrożyć takiej konfiguracji, skontaktuj się z oprogramowania SAP HANA na zarządzanie usługami platformy Azure, aby uzyskać trzeci adres IP w trzecim przypisane sieci VLAN. Duże wystąpienie oprogramowania HANA w przypadku urządzeń mających trzy adresy IP przypisane do trzech portów karty Sieciowej mają zastosowanie następujące reguły użycia:

- Ethernet "A" powinien mieć przypisanego adresu IP spoza zakresu adresów puli adresów IP serwera, który przesłanych do firmy Microsoft. Dlatego tego adresu IP nie stosuje się do przechowywania w/etc/hosts systemu operacyjnego.

- Ethernet "B" powinna służyć wyłącznie do przechowywania w etc/hosty do komunikacji między różnymi wystąpieniami. Te adresy będą również adresy IP, które muszą być przechowywane w konfiguracjach HANA skalowalnego w poziomie jako adresy IP, której HANA używa konfiguracji komunikacja między węzłami.

- Ethernet "C" powinien mieć przypisanego adresu IP, używanej do komunikacji z magazynu NFS. Dlatego adresy tego typu nie powinny być utrzymywane w etc/hosts.

- Ethernet "D" należy używać wyłącznie w przypadku dostępu do urządzenia pomocą metody STONITH program pacemaker. Ten interfejs jest wymagane podczas konfigurowania replikacji systemu HANA (HSR) i chcesz osiągnąć automatyczny tryb failover w systemie operacyjnym, przy użyciu urządzenia na podstawie interwencja.


### <a name="storage"></a>Magazyn
Magazyn został wstępnie skonfigurowany na podstawie topologii żądane. Woluminy o maksymalnym rozmiarze i punktu instalacji różnią się zależnie od liczby serwerów, jednostki SKU i topologii skonfigurowana. Aby uzyskać więcej informacji, należy przejrzeć wymagane scenariuszy (w dalszej części tego dokumentu). Jeśli wymagana jest więcej pamięci masowej, możesz kupić go w jedną jednostkę TB.

>[!NOTE]
>Punktinstalacji/usr/sap/<SID> łącze symboliczne do punktu instalacji/hana/udostępnione.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

W diagramów architektury następujące notacji służą do grafiki:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

Na poniższej liście przedstawiono obsługiwane scenariusze:

1. Jeden węzeł z jednego identyfikatora SID
2. Jeden węzeł MCOS
3. Jeden węzeł za pomocą odzyskiwania po awarii (normalny)
4. Jeden węzeł za pomocą odzyskiwania po awarii (Multipurpose)
5. Replikacji systemu HANA za pomocą metody STONITH
6. Replikacji systemu HANA za pomocą odzyskiwania po awarii (Multipurpose / normalny) 
7. Host automatyczny tryb failover (1 + 1) 
8. Skalowalny w poziomie ze stanem gotowości
9. Skalowalny w poziomie bez stan wstrzymania
10. Skalowalny w poziomie za pomocą odzyskiwania po awarii



## <a name="1-single-node-with-one-sid"></a>1. Jeden węzeł z jednego identyfikatora SID

Ta topologia obsługuje jeden węzeł w skalowania w górę konfiguracji z jednego identyfikatora SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczego węzła jeden SID.png z](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie jest używany |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie jest używany |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/Hana/Shared/SID | Instalacja oprogramowania HANA | 
|/Hana/Data/SID/mnt00001 | Instalowanie plików danych | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienników |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Jeden węzeł MCOS

Ta topologia obsługuje jeden węzeł w skalowania w górę konfiguracji z wieloma identyfikatorami SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![jednym node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie jest używany |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie jest używany |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/Hana/Shared/SID1 | Instalacja oprogramowania HANA SID1 | 
|/Hana/Data/SID1/mnt00001 | Pliki danych zainstaluj dla SID1 | 
|/Hana/log/SID1/mnt00001 | Pliki dziennika instalacji dla SID1 | 
|/Hana/logbackups/SID1 | Wykonaj ponownie dzienników dla SID1 |
|/Hana/Shared/SID2 | Instalacja oprogramowania HANA SID2 | 
|/Hana/Data/SID2/mnt00001 | Pliki danych zainstaluj dla SID2 | 
|/Hana/log/SID2/mnt00001 | Pliki dziennika instalacji dla SID2 | 
|/Hana/logbackups/SID2 | Wykonaj ponownie dzienników dla SID2 |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.
- Dystrybucja rozmiar woluminu na podstawie rozmiaru bazy danych w pamięci. Zapoznaj się [omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się więcej o rozmiarach z jakiej bazy danych w pamięci obsługujące multisid środowiska.

## <a name="3-single-node-with-dr-normal"></a>3. Jeden węzeł za pomocą odzyskiwania po awarii (normalny)
 
Ta topologia obsługuje jeden węzeł w skalowania w górę konfiguracji z jednego lub wielu identyfikatorów SID za pomocą replikacji opartej na magazyn do lokacji odzyskiwania po awarii na podstawowy identyfikator SID. Na diagramie tylko pojedynczy identyfikator SID jest przedstawiony w lokacji głównej, ale multisid (MCOS) jest również obsługiwany.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jednego węzła przy użyciu dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie jest używany |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie jest używany |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/Hana/Shared/SID | Instalacja oprogramowania HANA dla identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Instalowanie plików danych, dla identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji dla identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienników dla identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu jest na podstawie rozmiaru bazy danych w pamięci. Zapoznaj się [omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się więcej o rozmiarach z jakiej bazy danych w pamięci obsługujące multisid środowiska.
- W przypadku odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczonych jako "Wymagane do instalacji oprogramowania HANA") w środowisku produkcyjnym instalację wystąpienie oprogramowania HANA w jednostce HLI odzyskiwania po awarii. 
- W przypadku odzyskiwania po awarii: dane, logbackups i udostępnione woluminy (oznaczonych jako "Replikacji magazynu"), które są replikowane za pomocą migawki z witryny produkcyjnej. Te woluminy są instalowane tylko na czasie pracy awaryjnej. Aby uzyskać więcej informacji, przeczytaj dokument [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) Aby uzyskać więcej informacji.
- Rozruch wolumin **typ jednostki SKU I klasy** są replikowane do odzyskiwania po awarii węzła.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Jeden węzeł za pomocą odzyskiwania po awarii (Multipurpose)
 
Ta topologia obsługuje jeden węzeł w skalowania w górę konfiguracji z jednego lub wielu identyfikatorów SID za pomocą replikacji opartej na magazyn do lokacji odzyskiwania po awarii na podstawowy identyfikator SID. Na diagramie tylko pojedynczy identyfikator SID jest przedstawiony w lokacji głównej, ale multisid (MCOS) jest również obsługiwany. W lokacji odzyskiwania po awarii HLI jest używany dla wystąpienia pytań i odpowiedzi podczas wykonywania operacji w środowisku produkcyjnym z lokacji głównej. W czasie trybu failover odzyskiwania po awarii (lub test trybu failover) wystąpienie odpowiedzi na pytania w lokacji odzyskiwania po awarii jest pobierana w dół.

### <a name="architecture-diagram"></a>Diagram architektury  

![pojedynczego węzła odzyskiwania po awarii multipurpose.png z](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie jest używany |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie jest używany |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W lokacji odzyskiwania po awarii**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Shared/QA-SID | Instalacja oprogramowania HANA dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/Data/QA-SID/mnt00001 | Pliki danych zainstaluj dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/log/QA-SID/mnt00001 | Pliki dziennika instalacji dla identyfikatora SID pytań i odpowiedzi |
|/Hana/logbackups/QA-SID | Wykonaj ponownie dzienników dla identyfikatora SID pytań i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu jest na podstawie rozmiaru bazy danych w pamięci. Zapoznaj się [omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się więcej o rozmiarach z jakiej bazy danych w pamięci obsługujące multisid środowiska.
- W przypadku odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczonych jako "Wymagane do instalacji oprogramowania HANA") w środowisku produkcyjnym instalację wystąpienie oprogramowania HANA w jednostce HLI odzyskiwania po awarii. 
- W przypadku odzyskiwania po awarii: dane, logbackups i udostępnione woluminy (oznaczonych jako "Replikacji magazynu"), które są replikowane za pomocą migawki z witryny produkcyjnej. Te woluminy są instalowane tylko na czasie pracy awaryjnej. Aby uzyskać więcej informacji, przeczytaj dokument [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) Aby uzyskać więcej informacji. 
- W przypadku odzyskiwania po awarii: logbackups, danych, dzienników, udostępnione woluminy, aby uzyskać odpowiedzi na pytania (oznaczone jako "Instalacja wystąpienia odpowiedzi na pytania") są skonfigurowane podczas instalacji wystąpienia pytań i odpowiedzi.
- Rozruch wolumin **typ jednostki SKU I klasy** są replikowane do odzyskiwania po awarii węzła.

## <a name="5-hsr-with-stonith"></a>5. Replikacji systemu HANA za pomocą metody STONITH
 
Ta topologia obsługę konfiguracji replikacji systemu HANA (HSR) z uwzględnieniem dwóch węzłów. Ta konfiguracja jest obsługiwana tylko dla jednego wystąpienia oprogramowania HANA w węźle. Oznacza, że, MCOS scenariusze nie są obsługiwane.

**Aktualnie Ta architektura jest obsługiwana tylko w przypadku system operacyjny SUSE.**


### <a name="architecture-diagram"></a>Diagram architektury  

![Replikacji systemu HANA przy użyciu STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie jest używany |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Używany do pomocą metody STONITH |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie jest używany |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Używany do pomocą metody STONITH |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W węźle pomocniczym**|
|/Hana/Shared/SID | HANA instalacji dla pomocniczego identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych zainstaluj dla pomocniczego identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji dla pomocniczego identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienników dla pomocniczego identyfikatora SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu jest na podstawie rozmiaru bazy danych w pamięci. Zapoznaj się [omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się więcej o rozmiarach z jakiej bazy danych w pamięci obsługujące multisid środowiska.
- Pomocą metody STONITH: Interwencja została skonfigurowana do instalacji pomocą metody STONITH. Jednak użycie pomocą metody STONITH jest opcjonalne.


## <a name="6-hsr-with-dr"></a>6. Replikacji systemu HANA za pomocą odzyskiwania po awarii
 
Ta topologia obsługę konfiguracji replikacji systemu HANA (HSR) z uwzględnieniem dwóch węzłów. Zarówno normalne i multipurpose odzyskiwania po awarii jest obsługiwane. Te konfiguracje są obsługiwane tylko dla jednego wystąpienia oprogramowania HANA w węźle. Oznacza to, że MCOS scenariusze nie są obsługiwane za pomocą tych konfiguracji.

Na diagramie uniwersalny scenariusz jest opisany w przypadku gdy w lokacji odzyskiwania po awarii, HLI jest używany dla wystąpienia pytań i odpowiedzi podczas wykonywania operacji w środowisku produkcyjnym z lokacji głównej. W czasie trybu failover odzyskiwania po awarii (lub test trybu failover) wystąpienie odpowiedzi na pytania w lokacji odzyskiwania po awarii jest pobierana w dół. 



### <a name="architecture-diagram"></a>Diagram architektury  

![Replikacji systemu HANA przy użyciu DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Skonfigurowane, ale nie jest używany |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Używany do pomocą metody STONITH |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Skonfigurowane, ale nie jest używany |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Używany do pomocą metody STONITH |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym w lokacji głównej**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W węźle pomocniczym w lokacji głównej**|
|/Hana/Shared/SID | HANA instalacji dla pomocniczego identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych zainstaluj dla pomocniczego identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji dla pomocniczego identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienników dla pomocniczego identyfikatora SID |
|**W lokacji odzyskiwania po awarii**|
|/Hana/Shared/SID | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Shared/QA-SID | Instalacja oprogramowania HANA dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/Data/QA-SID/mnt00001 | Pliki danych zainstaluj dla identyfikatora SID pytań i odpowiedzi | 
|/Hana/log/QA-SID/mnt00001 | Pliki dziennika instalacji dla identyfikatora SID pytań i odpowiedzi |
|/Hana/logbackups/QA-SID | Wykonaj ponownie dzienników dla identyfikatora SID pytań i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.
- Dla MCOS: Dystrybucji rozmiar woluminu jest na podstawie rozmiaru bazy danych w pamięci. Zapoznaj się [omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sekcji, aby dowiedzieć się więcej o rozmiarach z jakiej bazy danych w pamięci obsługujące multisid środowiska.
- Pomocą metody STONITH: Interwencja została skonfigurowana do instalacji pomocą metody STONITH. Jednak użycie pomocą metody STONITH jest opcjonalne.
- W przypadku odzyskiwania po awarii: **wymagane są dwa zestawy woluminów magazynu** podstawowego i pomocniczego węzła replikacji.
- W przypadku odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczonych jako "Wymagane do instalacji oprogramowania HANA") w środowisku produkcyjnym instalację wystąpienie oprogramowania HANA w jednostce HLI odzyskiwania po awarii. 
- W przypadku odzyskiwania po awarii: dane, logbackups i udostępnione woluminy (oznaczonych jako "Replikacji magazynu"), które są replikowane za pomocą migawki z witryny produkcyjnej. Te woluminy są instalowane tylko na czasie pracy awaryjnej. Aby uzyskać więcej informacji, przeczytaj dokument [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) Aby uzyskać więcej informacji. 
- W przypadku odzyskiwania po awarii: logbackups, danych, dzienników, udostępnione woluminy, aby uzyskać odpowiedzi na pytania (oznaczone jako "Instalacja wystąpienia odpowiedzi na pytania") są skonfigurowane podczas instalacji wystąpienia pytań i odpowiedzi.
- Rozruch wolumin **typ jednostki SKU I klasy** są replikowane do odzyskiwania po awarii węzła.


## <a name="7-host-auto-failover-11"></a>7. Host automatyczny tryb failover (1 + 1)
 
Ta topologia obsługuje dwa węzły w konfiguracji trybu failover hostów auto. Istnieje jeden węzeł z rolą wzorca/procesu roboczego i innych jako nieaktywne. **Ten scenariusz jest obsługiwany tylko w przypadku S/4 HANA SAP.** Zapoznaj się uwagę OSS "[2408419 - SAP S/4HANA — Obsługa wielu węzłach](https://launchpad.support.sap.com/#/notes/2408419)" Aby uzyskać więcej szczegółów.



### <a name="architecture-diagram"></a>Diagram architektury  

![Funkcja o wartościach Ska](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł, aby węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł, aby węzłami |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węzłach master i w gotowości**|
|/ hana/udostępnione | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |



### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.
- Stan gotowości: woluminy i punkty instalacji są skonfigurowane (oznaczonych jako "Wymagane do instalacji oprogramowania HANA") dla instalacji wystąpienie oprogramowania HANA w jednostce wstrzymania.
 

## <a name="8-scale-out-with-standby"></a>8. Skalowalny w poziomie ze stanem gotowości
 
Ta topologia obsługuje wiele węzłów w ramach konfiguracji skalowania w poziomie. Istnieje jeden węzeł z rolą wzorca, jeden lub więcej węzłów za pomocą roli procesu roboczego i co najmniej jeden węzeł jako nieaktywne. Jednak może istnieć tylko jeden węzeł główny w dowolnym czasie.


### <a name="architecture-diagram"></a>Diagram architektury  

![skalowalne nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł, aby węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł, aby węzłami |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węzłach wzorzec, proces roboczy i w gotowości**|
|/ hana/udostępnione | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |


## <a name="9-scale-out-without-standby"></a>9. Skalowalny w poziomie bez stan wstrzymania
 
Ta topologia obsługuje wiele węzłów w ramach konfiguracji skalowania w poziomie. Istnieje jeden węzeł z rolą wzorca i jeden lub trybie węzłów za pomocą roli procesu roboczego. Jednak może istnieć tylko jeden węzeł główny w dowolnym czasie.


### <a name="architecture-diagram"></a>Diagram architektury  

![nm.png skalowania w poziomie](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł, aby węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł, aby węzłami |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węzłach głównego i proces roboczy**|
|/ hana/udostępnione | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Skalowalny w poziomie za pomocą odzyskiwania po awarii
 
Ta topologia obsługuje wiele węzłów w skalowalnym w poziomie za pomocą odzyskiwania po awarii. Normalne i multipurpose odzyskiwania po awarii jest obsługiwane. Na diagramie przedstawiono tylko jednemu celowi odzyskiwania po awarii. Możesz zażądać tej topologii z lub bez wstrzymania węzła.


### <a name="architecture-diagram"></a>Diagram architektury  

![skalowania w poziomie za pomocą dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KARTY SIECIOWEJ | TYP JEDNOSTKI SKU | Nazwa z systemem operacyjnym SUSE | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Węzeł, aby węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł magazynu |
| D | TYP I | eth4.tenant | eno4.tenant | Skonfigurowane, ale nie jest używany |
| A | TYP II | sieci VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sieć VLAN < tenantNo + 2 > | team0.tenant + 2 | Węzeł, aby węzłami |
| C | TYP II | sieć VLAN < tenantNo + 1 > | team0.tenant + 1 | Węzeł magazynu |
| D | TYP II | sieć VLAN < tenantNo + 3 > | team0.tenant + 3 | Skonfigurowane, ale nie jest używany |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/ hana/udostępnione | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 
|/Hana/logbackups/SID | Wykonaj ponownie dzienniki w środowisku produkcyjnym identyfikatora SID |
|**W węźle odzyskiwania po awarii**|
|/ hana/udostępnione | HANA zainstalować w środowisku produkcyjnym identyfikatora SID | 
|/Hana/Data/SID/mnt00001 | Pliki danych, zainstaluj w środowisku produkcyjnym identyfikatora SID | 
|/Hana/log/SID/mnt00001 | Pliki dziennika instalacji w środowisku produkcyjnym identyfikatora SID | 


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/SAP/SID jest link symboliczny do /hana/shared/SID.
-  W przypadku odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczonych jako "Wymagane do instalacji oprogramowania HANA") w środowisku produkcyjnym instalację wystąpienie oprogramowania HANA w jednostce HLI odzyskiwania po awarii. 
- W przypadku odzyskiwania po awarii: dane, logbackups i udostępnione woluminy (oznaczonych jako "Replikacji magazynu"), które są replikowane za pomocą migawki z witryny produkcyjnej. Te woluminy są instalowane tylko na czasie pracy awaryjnej. Aby uzyskać więcej informacji, przeczytaj dokument [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) Aby uzyskać więcej informacji. 
- Rozruch wolumin **typ jednostki SKU I klasy** są replikowane do odzyskiwania po awarii węzła.


## <a name="next-steps"></a>Kolejne kroki
- Zapoznaj się [infrastruktura i łączność](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) dla HLI
- Zapoznaj się [wysoką dostępność i odzyskiwanie awaryjne](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) dla HLI