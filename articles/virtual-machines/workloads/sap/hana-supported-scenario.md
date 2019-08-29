---
title: Obsługiwane scenariusze SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Obsługiwane scenariusze i szczegółowe informacje o architekturze dla SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f17e447f26ae4f7573941fc0c578a918ff45a145
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101230"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Obsługiwane scenariusze dla dużych wystąpień HANA
W tym dokumencie opisano obsługiwane scenariusze wraz ze szczegółowymi informacjami o architekturze dla dużych wystąpień HANA (HLI).

>[!NOTE]
>Jeśli w tym miejscu nie określono wymaganego scenariusza, skontaktuj się z zespołem usługi Microsoft Service Management, aby ocenić wymagania.
Przed przystąpieniem do inicjowania obsługi administracyjnej jednostki, zweryfikuj projekt przy użyciu oprogramowania SAP lub partnera implementacji usługi.

## <a name="terms-and-definitions"></a>Warunki i definicje
Zapoznaj się z postanowieniami i definicjami używanymi w dokumencie.

- SID: Identyfikator systemu dla systemu HANA.
- HLI Duże wystąpienia Hana.
- ROUTINGU Lokacja odzyskiwania po awarii.
- Normalne DR: Konfiguracja systemu z dedykowanym zasobem przeznaczonym do użycia w celu odzyskiwania po awarii.
- Multipurpose DR: System w lokacji DR skonfigurowany do używania środowiska nieprodukcyjnego wraz z wystąpieniem produkcyjnym skonfigurowanym do używania do zdarzenia DR. 
- Pojedynczy identyfikator SID:  System z zainstalowanym jednym wystąpieniem.
- Wiele identyfikatorów SID: Skonfigurowano system z wieloma wystąpieniami. Nazywana również środowiskiem MCOS.


## <a name="overview"></a>Omówienie
Duże wystąpienia HANA obsługują różne architektury w celu spełnienia wymagań firmy. Poniższa lista obejmuje scenariusze i ich szczegóły konfiguracji. 

Projekt architektury pochodnej jest całkowicie z perspektywy infrastruktury i należy skontaktować się z producentem SAP lub partnerami implementacji w celu wdrożenia platformy HANA. Jeśli Twoje scenariusze nie są wyświetlane na liście, skontaktuj się z zespołem konto Microsoft, aby zapoznać się z architekturą i uzyskać dla Ciebie rozwiązanie.

**Te architektury są w pełni zgodne z projektem TDI (dostosowana integracja danych) i jest obsługiwany przez SAP.**

W tym dokumencie opisano szczegółowe informacje o dwóch składnikach w poszczególnych obsługiwanych architekturach:

- Obciążony
- Magazyn

### <a name="ethernet"></a>Obciążony

Każdy inicjowany serwer jest wstępnie skonfigurowany przy użyciu zestawów interfejsów sieci Ethernet. Poniżej znajdują się szczegółowe informacje o interfejsach Ethernet skonfigurowanych w poszczególnych jednostkach.

- **Odp.:** Ten interfejs jest używany przez/przez dostęp klienta.
- **B**: Ten interfejs jest używany do komunikacji węzła z węzłem. Ten interfejs jest skonfigurowany na wszystkich serwerach (niezależnie od żądanej topologii), ale jest używany tylko przez 
- scenariusze skalowania w poziomie.
- **C**: Ten interfejs jest używany przez węzeł do połączenia z magazynem.
- **D**: Ten interfejs jest używany przez węzeł do połączenia z urządzeniem ISCSI dla Instalatora STONITH. Ten interfejs jest konfigurowany tylko po zażądaniu konfiguracji HSR.  

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Węzeł do węzła |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | STONITH |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Węzeł do węzła |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | STONITH |

Interfejsy są używane na podstawie topologii skonfigurowanej w jednostce. Na przykład interfejs "B" jest skonfigurowany do komunikacji między węzłami, co jest przydatne w przypadku skonfigurowania topologii skalowalnej w poziomie. W przypadku konfiguracji skalowania pojedynczego węzła ten interfejs nie jest używany. Przejrzyj wymagane scenariusze (w dalszej części tego dokumentu), aby uzyskać więcej informacji o użyciu interfejsu. 

W razie konieczności możesz samodzielnie zdefiniować dodatkowe karty sieciowe. NIE można jednak zmienić konfiguracji istniejących kart sieciowych.

>[!NOTE]
>Nadal mogą znajdować się dodatkowe interfejsy, które są interfejsami fizycznymi lub wiązania. Należy wziąć pod uwagę powyższe interfejsy w używanym przypadku, reszta może być ignorowana/lub nie do odporności.

Rozkład dla jednostek z dwoma przypisanymi adresami IP powinien wyglądać następująco:

- Sieć Ethernet "A" powinna mieć przypisany adres IP, który znajduje się poza zakresem adresów puli adresów IP serwera przesłanym do firmy Microsoft. Ten adres IP będzie używany do utrzymywania w/etc/hosts systemu operacyjnego.

- Sieć Ethernet "C" powinna mieć przypisany adres IP używany do komunikacji z systemem plików NFS. W związku z tym te adresy **nie** muszą być utrzymywane w etc/hostach, aby umożliwić wystąpieniu wystąpienia ruchu w ramach dzierżawy.

W przypadku wdrożeń w przypadku wystąpienia replikacji systemu HANA lub skalowania platformy HANA konfiguracja bloku z dwoma przypisanymi adresami IP nie jest odpowiednia. Jeśli mają być przypisane dwa adresy IP i chcesz wdrożyć taką konfigurację, skontaktuj się z SAP HANA w usłudze Azure Service Management, aby uzyskać trzeci adres IP w trzeciej przypisaniu sieci VLAN. W przypadku dużych jednostek wystąpienia platformy HANA, które mają trzy adresy IP przypisane do trzech portów kart sieciowych, mają zastosowanie następujące reguły użycia:

- Sieć Ethernet "A" powinna mieć przypisany adres IP, który znajduje się poza zakresem adresów puli adresów IP serwera przesłanym do firmy Microsoft. W związku z tym ten adres IP nie jest używany do utrzymania w/etc/hosts systemu operacyjnego.

- Sieć Ethernet "B" powinna być używana wyłącznie do przechowywania w trybie etc/hosty w celu komunikacji między różnymi wystąpieniami. Te adresy będą również adresami IP, które muszą być utrzymywane w konfiguracjach platformy HANA w skalowalnej w poziomie jako adresy IP używane przez platformę konfiguracji między węzłami.

- Sieć Ethernet "C" powinna mieć przypisany adres IP używany do komunikacji z magazynem systemu plików NFS. W związku z tym tego typu adresy nie należy utrzymywać w etc/hostach.

- Sieć Ethernet "D" powinna być używana wyłącznie na potrzeby dostępu do urządzenia STONITH na potrzeby usługi Pacemaker. Ten interfejs jest wymagany podczas konfigurowania replikacji systemu HANA (HSR) i chcą uzyskać tryb failover w systemie operacyjnym przy użyciu urządzenia z systemem SBD.


### <a name="storage"></a>Magazyn
Magazyn jest wstępnie skonfigurowany na podstawie żądanej topologii. Rozmiary i mountpoint woluminów są różne w zależności od liczby skonfigurowanych serwerów, jednostek SKU i topologii. Przejrzyj wymagane scenariusze (w dalszej części tego dokumentu), aby uzyskać więcej informacji. Jeśli jest wymagany dodatkowy magazyn, można go zakupić w jednym TB.

>[!NOTE]
>> Identyfikatora SID\<mountpoint/usr/SAP/jest link symboliczny do mountpoint/Hana/Shared.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Na diagramach architektury następujące notacji są używane do grafiki:

![Legendy. PNG](media/hana-supported-scenario/Legends.PNG)

Na poniższej liście przedstawiono obsługiwane scenariusze:

1. Pojedynczy węzeł z jednym identyfikatorem SID
2. MCOS jednego węzła
3. Pojedynczy węzeł z odzyskiwaniem po awarii (normalne)
4. Pojedynczy węzeł z odzyskiwaniem po awarii (Multipurpose)
5. HSR z STONITH
6. HSR z DR (Normal/Multipurpose) 
7. Przełączenie w tryb failover hosta (1 + 1) 
8. Skalowanie w poziomie przy użyciu rezerwy
9. Skalowanie w poziomie bez rezerwy
10. Skalowanie w poziomie za pomocą programu DR



## <a name="1-single-node-with-one-sid"></a>1. Pojedynczy węzeł z jednym identyfikatorem SID

Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z jednym identyfikatorem SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID | Instalacja platformy HANA | 
|/hana/data/SID/mnt00001 | Instalowanie plików danych | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. MCOS jednego węzła

Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z wieloma identyfikatorami SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID1 | Instalacja platformy HANA dla SID1 | 
|/hana/data/SID1/mnt00001 | Instalowanie plików danych dla SID1 | 
|/hana/log/SID1/mnt00001 | Pliki dziennika instalacji dla SID1 | 
|/hana/logbackups/SID1 | Wykonaj ponownie dzienniki dla SID1 |
|/hana/shared/SID2 | Instalacja platformy HANA dla SID2 | 
|/hana/data/SID2/mnt00001 | Instalowanie plików danych dla SID2 | 
|/hana/log/SID2/mnt00001 | Pliki dziennika instalacji dla SID2 | 
|/hana/logbackups/SID2 | Wykonaj ponownie dzienniki dla SID2 |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- Rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Zapoznaj się z sekcją [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) , aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku multisid.

## <a name="3-single-node-with-dr-normal"></a>3. Pojedynczy węzeł z odzyskiwaniem po awarii (normalne)
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z co najmniej jednym identyfikatorem SID z replikacją opartą na magazynie do lokacji DR dla podstawowego identyfikatora SID. Na diagramie tylko jeden identyfikator SID jest przedstawiony w lokacji głównej, ale jest również obsługiwany multisid (MCOS).

### <a name="architecture-diagram"></a>Diagram architektury  

![Single-Node-with-Dr. png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID | Instalacja platformy HANA dla identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- Dla MCOS: Rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Zapoznaj się z sekcją [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) , aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku multisid.
- Na stronie DR: Woluminy i mountpoints są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- Na stronie DR: Dane, logbackups i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko w czasie pracy awaryjnej. Aby uzyskać więcej informacji, Przeczytaj [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) , aby uzyskać więcej szczegółów.
- Wolumin rozruchowy dla **typu jednostki SKU I klasy** jest replikowany do węzła Dr.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Pojedynczy węzeł z odzyskiwaniem po awarii (Multipurpose)
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z co najmniej jednym identyfikatorem SID z replikacją opartą na magazynie do lokacji DR dla podstawowego identyfikatora SID. Na diagramie tylko jeden identyfikator SID jest przedstawiony w lokacji głównej, ale jest również obsługiwany multisid (MCOS). W odniesieniu do wystąpienia funkcji pytań i odpowiedzi w lokacji DR jest używana jednostka "HLI", podczas gdy operacje produkcyjne są uruchamiane z lokacji głównej. W momencie przełączania do trybu failover odzyskiwania po awarii (test pracy w trybie failover) wystąpienie funkcji pytań i odpowiedzi w witrynie DR jest wyłączone.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single-Node-with-Dr-Multipurpose. png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/shared/SID | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W witrynie DR**|
|/hana/shared/SID | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/shared/QA-SID | Instalacja platformy HANA dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID usługi pytania i odpowiedzi |
|/hana/logbackups/QA-SID | Dzienniki ponownego wykonywania dla identyfikatora SID usługi pytania i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- Dla MCOS: Rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Zapoznaj się z sekcją [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) , aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku multisid.
- Na stronie DR: Woluminy i mountpoints są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- Na stronie DR: Dane, logbackups i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko w czasie pracy awaryjnej. Aby uzyskać więcej informacji, Przeczytaj [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) , aby uzyskać więcej szczegółów. 
- Na stronie DR: Dane, logbackups, log, udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia pytania i odpowiedzi") są konfigurowane na potrzeby instalacji wystąpienia usługi pytania i odpowiedzi.
- Wolumin rozruchowy dla **typu jednostki SKU I klasy** jest replikowany do węzła Dr.

## <a name="5-hsr-with-stonith"></a>5. HSR z STONITH
 
Ta topologia obsługuje dwa węzły dla konfiguracji replikacji systemu HANA (HSR). Ta konfiguracja jest obsługiwana tylko w przypadku pojedynczych wystąpień platformy HANA w węźle. MCOS scenariusze nie są obsługiwane.

**Obecnie ta architektura jest obsługiwana tylko dla systemu operacyjnego SUSE.**


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Używane na potrzeby STONITH |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Używane na potrzeby STONITH |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/shared/SID | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W węźle pomocniczym**|
|/hana/shared/SID | Instalacja platformy HANA dla pomocniczego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla pomocniczego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla pomocniczego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla pomocniczego identyfikatora SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- Dla MCOS: Rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Zapoznaj się z sekcją [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) , aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku multisid.
- STONITH: SBD jest skonfigurowany do konfiguracji STONITH. Jednak użycie STONITH jest opcjonalne.


## <a name="6-hsr-with-dr"></a>6. HSR z odzyskiwaniem po awarii
 
Ta topologia obsługuje dwa węzły dla konfiguracji replikacji systemu HANA (HSR). Obsługiwane są zarówno normalne, jak i uniwersalne DR. Te konfiguracje są obsługiwane tylko w przypadku pojedynczych wystąpień platformy HANA w węźle. Oznacza to, że scenariusze MCOS nie są obsługiwane w przypadku tych konfiguracji.

Na diagramie jest przedstawiony scenariusz wielomiarowy, w którym jest używana lokacja usługi DR w przypadku wystąpienia funkcji pytań i odpowiedzi podczas wykonywania operacji produkcyjnych z lokacji głównej. W momencie przełączania do trybu failover odzyskiwania po awarii (test pracy w trybie failover) wystąpienie funkcji pytań i odpowiedzi w witrynie DR jest wyłączone. 



### <a name="architecture-diagram"></a>Diagram architektury  

![HSR-with-DR. png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Używane na potrzeby STONITH |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Używane na potrzeby STONITH |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym w lokacji głównej**|
|/hana/shared/SID | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W węźle pomocniczym w lokacji głównej**|
|/hana/shared/SID | Instalacja platformy HANA dla pomocniczego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla pomocniczego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla pomocniczego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla pomocniczego identyfikatora SID |
|**W witrynie DR**|
|/hana/shared/SID | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/shared/QA-SID | Instalacja platformy HANA dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID usługi pytania i odpowiedzi |
|/hana/logbackups/QA-SID | Dzienniki ponownego wykonywania dla identyfikatora SID usługi pytania i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- Dla MCOS: Rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Zapoznaj się z sekcją [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) , aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku multisid.
- STONITH: SBD jest skonfigurowany do konfiguracji STONITH. Jednak użycie STONITH jest opcjonalne.
- Na stronie DR: **Dwa zestawy woluminów magazynu są wymagane** do replikacji węzłów podstawowych i pomocniczych.
- Na stronie DR: Woluminy i mountpoints są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- Na stronie DR: Dane, logbackups i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko w czasie pracy awaryjnej. Aby uzyskać więcej informacji, Przeczytaj [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) , aby uzyskać więcej szczegółów. 
- Na stronie DR: Dane, logbackups, log, udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia pytania i odpowiedzi") są konfigurowane na potrzeby instalacji wystąpienia usługi pytania i odpowiedzi.
- Wolumin rozruchowy dla **typu jednostki SKU I klasy** jest replikowany do węzła Dr.


## <a name="7-host-auto-failover-11"></a>7. Przełączenie w tryb failover hosta (1 + 1)
 
Ta topologia obsługuje dwa węzły w konfiguracji automatycznej pracy awaryjnej hosta. Istnieje jeden węzeł z rolą Master/Worker i innymi jako stanem gotowości. **System SAP obsługuje ten scenariusz tylko dla S/4 HANA.** Aby uzyskać więcej informacji, odwołaj się do usługi OSS Uwaga "[2408419-SAP S/4HANA — obsługa wielu węzłów](https://launchpad.support.sap.com/#/notes/2408419)".



### <a name="architecture-diagram"></a>Diagram architektury  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|**Na węzłach głównych i rezerwowych**|
|/hana/shared | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |



### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W stanie wstrzymania: Woluminy i mountpoints są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") na potrzeby instalacji wystąpienia HANA w jednostce w stanie wstrzymania.
 

## <a name="8-scale-out-with-standby"></a>8. Skalowanie w poziomie przy użyciu rezerwy
 
Ta topologia obsługuje wiele węzłów w konfiguracji skalowania w poziomie. Istnieje jeden węzeł z rolą nadrzędną, co najmniej jednego węzła z rolą proces roboczy, a co najmniej jeden węzłów jest w stanie wstrzymania. Mimo że w danym momencie może istnieć tylko jeden węzeł główny.


### <a name="architecture-diagram"></a>Diagram architektury  

![ScaleOut-nm-Standby. png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|**W węzłach Master, Worker i Standby**|
|/hana/shared | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |


## <a name="9-scale-out-without-standby"></a>9. Skalowanie w poziomie bez rezerwy
 
Ta topologia obsługuje wiele węzłów w konfiguracji skalowania w poziomie. Istnieje jeden węzeł z rolą wzorca i jeden lub tryb z rolą proces roboczy. Mimo że w danym momencie może istnieć tylko jeden węzeł główny.


### <a name="architecture-diagram"></a>Diagram architektury  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|**Na węzłach głównych i procesów roboczych**|
|/hana/shared | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Skalowanie w poziomie za pomocą programu DR
 
Ta topologia obsługuje wiele węzłów w skalowaniu w poziomie przy użyciu funkcji odzyskiwania po awarii. Obsługiwane są zarówno normalne, jak i Multipurpose DR. Na diagramie przedstawiony jest tylko pojedynczy cel DR. Możesz zażądać tej topologii z węzłem w stanie wstrzymania lub bez niego.


### <a name="architecture-diagram"></a>Diagram architektury  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Obciążony
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| INTERFEJSY LOGICZNE KART INTERFEJSU SIECIOWEGO | TYP JEDNOSTKI SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient do HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł do magazynu |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | >\<sieci VLAN tenantNo | team0. Dzierżawca | Klient do HLI |
| B | TYP II | tenantNo\<sieci VLAN + 2 > | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | tenantNo\<sieci VLAN + 1 > | team0. Dzierżawca + 1 | Węzeł do magazynu |
| D | TYP II | tenantNo\<sieci VLAN + 3 > | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące mountpoints są wstępnie skonfigurowane:

| Mountpoint | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/shared | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W węźle DR**|
|/hana/shared | Instalator platformy HANA dla produkcyjnych identyfikatorów SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Pliki dziennika instalacji dla produkcyjnego identyfikatora SID | 


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
-  Na stronie DR: Woluminy i mountpoints są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- Na stronie DR: Dane, logbackups i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko w czasie pracy awaryjnej. Aby uzyskać więcej informacji, Przeczytaj [procedurę trybu failover odzyskiwania po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) , aby uzyskać więcej szczegółów. 
- Wolumin rozruchowy dla **typu jednostki SKU I klasy** jest replikowany do węzła Dr.


## <a name="next-steps"></a>Następne kroki
- Odwołuje się do [infrastruktury i łączności](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) dla elementu HLI
- Zapoznaj się [z wysoką dostępnością i odzyskiwaniem po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) dla elementu HLI
