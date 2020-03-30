---
title: Obsługiwane scenariusze dla sap HANA na platformie Azure (duże wystąpienia)| Dokumenty firmy Microsoft
description: Obsługiwane scenariusze i szczegóły ich architektury dla sap HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617179"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Obsługiwane scenariusze dla dużych wystąpień HANA
W tym artykule opisano obsługiwane scenariusze i szczegóły architektury dla dużych wystąpień HANA (HLI).

>[!NOTE]
>Jeśli wymagany scenariusz nie jest wymieniony w tym artykule, skontaktuj się z zespołem zarządzania usługami firmy Microsoft, aby ocenić wymagania.
Przed skonfigurowaniem jednostki HLI należy sprawdzić poprawność projektu za pomocą sap lub partnera implementacji usługi.

## <a name="terms-and-definitions"></a>Warunki i definicje
Przyjmijmy terminy i definicje, które są używane w tym artykule:

- **SID**: Identyfikator systemu dla systemu HANA
- **HLI**: Hana Duże instancje
- **DR**: Odzyskiwanie po awarii
- **Normalna dr:** Konfiguracja systemu z dedykowanym zasobem tylko do celów odzyskiwania po awarii
- **Wielofunkcyjna awaria odzyskiwania po**awarii: system lokacji odzyskiwania po awarii skonfigurowany do używania środowiska nieprodukcyjnego obok wystąpienia produkcyjnego skonfigurowanego dla zdarzenia odzyskiwania po awarii 
- **Pojedynczy identyfikator SID**: System z zainstalowanym jednym wystąpieniem
- **Multi-SID:** System z wieloma skonfigurowanymi wystąpieniami; zwane również środowiskiem MCOS
- **HSR**: Replikacja systemu SAP HANA

## <a name="overview"></a>Omówienie
Hana dużych wystąpień obsługuje różne architektury, aby pomóc w osiągnięciu wymagań biznesowych. Poniższe sekcje obejmują scenariusze architektury i ich szczegóły konfiguracji. 

Projekt architektury pochodnej jest wyłącznie z punktu widzenia infrastruktury i należy skonsultować się z SAP lub partnerów implementacji dla wdrożenia HANA. Jeśli scenariusze nie są wymienione w tym artykule, skontaktuj się z zespołem konta Microsoft, aby przejrzeć architekturę i uzyskać rozwiązanie dla Ciebie.

> [!NOTE]
> Architektury te są w pełni zgodne z projektem Tailored Data Integration (TDI) i obsługiwane przez sap.

W tym artykule opisano szczegóły dwóch składników w każdej obsługiwanej architekturze:

- Ethernet
- Magazyn

### <a name="ethernet"></a>Ethernet

Każdy aprowizowany serwer jest wstępnie skonfigurowany z zestawami interfejsów Ethernet. Interfejsy Ethernet skonfigurowane na każdej jednostce HLI są podzielone na cztery typy:

- **A:** Używany dla lub przez dostęp klienta.
- **B:** Służy do komunikacji między węzłami. Ten interfejs jest skonfigurowany na wszystkich serwerach (niezależnie od żądanej topologii), ale używany tylko w scenariuszach skalowania w poziomie.
- **C:** Używany do łączności między węzłami a magazynem.
- **D**: Służy do połączenia urządzenia między węzłami a iSCSI dla konfiguracji STONITH. Ten interfejs jest konfigurowany tylko wtedy, gdy żądana jest konfiguracja HSR.  

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Węzeł-węzeł|
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | OKRĘG WYBORCZY STONITH |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Węzeł-węzeł|
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | OKRĘG WYBORCZY STONITH |

Wybierz interfejs oparty na topologii skonfigurowany na jednostce HLI. Na przykład interfejs "B" jest skonfigurowany do komunikacji węzeł-węzeł, co jest przydatne, gdy masz skalowano w poziomie topologii skonfigurowane. Ten interfejs nie jest używany dla pojedynczego węzła, skalowania w górę konfiguracji. Aby uzyskać więcej informacji na temat użycia interfejsu, przejrzyj wymagane scenariusze (w dalszej części tego artykułu). 

W razie potrzeby można samodzielnie zdefiniować dodatkowe karty sieciowe. *Nie można* jednak zmienić konfiguracji istniejących kart sieciowych.

>[!NOTE]
>Można znaleźć dodatkowe interfejsy, które są interfejsami fizycznymi lub wiązania. Należy wziąć pod uwagę tylko wcześniej wymienione interfejsy dla przypadku użycia. Wszystkie inne mogą być ignorowane.

Dystrybucja dla jednostek z dwoma przypisanymi adresami IP powinna wyglądać następująco:

- Ethernet "A" powinien mieć przypisany adres IP, który znajduje się w zakresie adresów puli adresów IP serwera, który został przesłany do firmy Microsoft. Ten adres IP powinien być utrzymywany w katalogu */etc/hosts* systemu operacyjnego.

- Ethernet "C" powinien mieć przypisany adres IP, który jest używany do komunikacji z nfs. Ten adres *nie* musi być utrzymywany w katalogu *etc/hosts,* aby umożliwić ruch wystąpienia do wystąpienia w dzierżawie.

W przypadku wdrożenia hana system replikacji lub skalowania w poziomie HANA konfiguracja bloku z dwoma przypisanymi adresami IP nie jest odpowiednia. Jeśli masz tylko dwa przypisane adresy IP i chcesz wdrożyć taką konfigurację, skontaktuj się z SAP HANA w usłudze Azure Service Management. Mogą przypisać ci trzeci adres IP w trzeciej sieci VLAN. W przypadku jednostek dużych wystąpień HANA z trzema przypisanymi adresami IP na trzech portach kart sieciowych obowiązują następujące reguły użycia:

- Ethernet "A" powinien mieć przypisany adres IP, który znajduje się poza zakresem adresów puli adresów IP serwera przesłanym do firmy Microsoft. Ten adres IP nie powinien być utrzymywany w katalogu *etc/hosts* systemu operacyjnego.

- Ethernet "B" powinien być utrzymywany wyłącznie w katalogu *etc/hosts* do komunikacji między różnymi wystąpieniami. Są to adresy IP, które mają być utrzymywane w konfiguracji HANA skalowane w poziomie jako adresy IP używane przez hana do konfiguracji między węzłami.

- Ethernet "C" powinien mieć przypisany adres IP, który jest używany do komunikacji z pamięcią NFS. Ten typ adresu nie powinien być utrzymywany w katalogu *etc/hosts.*

- Ethernet "D" powinien być używany wyłącznie do dostępu do urządzeń STONITH dla rozrusznika serca. Ten interfejs jest wymagany podczas konfigurowania replikacji systemu HANA i chcesz automatycznie przechodzić w tryb failover systemu operacyjnego przy użyciu urządzenia opartego na SBD.


### <a name="storage"></a>Magazyn
Magazyn jest wstępnie skonfigurowany na podstawie żądanej topologii. Rozmiary woluminów i punktów instalacji różnią się w zależności od liczby serwerów, liczby jednostek SKU i skonfigurowanej topologii. Aby uzyskać więcej informacji, zapoznaj się z wymaganymi scenariuszami (w dalszej części tego artykułu). Jeśli potrzebujesz więcej miejsca, możesz go kupić w odstępach co 1 TB.

>[!NOTE]
>Punkt instalacji /usr/sap/\<SID> jest dowiązaniem symbolicznym do punktu instalacji /hana/shared.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Diagramy architektury w następnych sekcjach używają następujących notacji:

[![Tabela diagramów architektury](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Oto obsługiwane scenariusze:

* Pojedynczy węzeł z jednym identyfikatorem SID
* Pojedynczy węzeł MCOS
* Pojedynczy węzeł z dr (normalny)
* Pojedynczy węzeł z dr (uniwersalny)
* HSR z STONITH
* HSR z DR (normalny/wielofunkcyjny) 
* Automatyczne tryb failover hosta (1+1) 
* Skalowanie w poziomie ze stanem wstrzymania
* Skalowanie w poziomie bez gotowości
* Skalowanie w poziomie z odzyskiwania po awarii

## <a name="single-node-with-one-sid"></a>Pojedynczy węzeł z jednym identyfikatorem SID

Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z jednym identyfikatorem SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z jednym identyfikatorem SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|/hana/udostępniony/SID | Instalacja HANA | 
|/hana/data/SID/mnt00001 | Instalacja plików danych | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika | 
|/hana/logbackups/SID | Ponawianie dzienników |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.

## <a name="single-node-mcos"></a>Pojedynczy węzeł MCOS

Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z wieloma identyfikatorami SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID1 | Instalacja HANA dla SID1 | 
|/hana/data/SID1/mnt00001 | Instalacja plików danych dla identyfikatora SID1 | 
|/hana/log/SID1/mnt00001 | Instalacja plików dziennika dla identyfikatora SID1 | 
|/hana/logbackups/SID1 | Ponawianie dzienników dla identyfikatora SID1 |
|/hana/shared/SID2 | Instalacja HANA dla SID2 | 
|/hana/data/SID2/mnt00001 | Instalacja plików danych dla sid2 | 
|/hana/log/SID2/mnt00001 | Instalacja plików dziennika dla sid2 | 
|/hana/logbackups/SID2 | Ponawianie dzienników dla sid2 |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- Rozkład rozmiaru woluminu jest oparty na rozmiarze bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary baz danych w pamięci są obsługiwane w środowisku wielosydyskowym, zobacz [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

## <a name="single-node-with-dr-using-storage-replication"></a>Pojedynczy węzeł z odzyskiwania po awarii przy użyciu replikacji magazynu
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z jednym lub wieloma identyfikatorami SID, z replikacją opartą na magazynie do lokacji odzyskiwania po awarii dla podstawowego identyfikatora SID. Na diagramie tylko system z pojedynczym identyfikatorem SID jest przedstawiony w lokacji głównej, ale obsługiwane są również systemy MCOS.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z odzyskiwania po awarii przy użyciu replikacji magazynu](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|/hana/udostępniony/SID | Instalacja HANA dla identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID | 
|/hana/logbackups/SID | Ponawianie dzienników dla identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W przypadku mcos: rozkład rozmiaru woluminu jest oparty na rozmiarze bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary baz danych w pamięci są obsługiwane w środowisku wielosydyskowym, zobacz [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "Wymagane do instalacji HANA") dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika i udostępnione woluminy (oznaczone jako "Replikacja magazynu") są replikowane za pomocą migawki z lokacji produkcyjnej. Woluminy te są montowane tylko podczas pracy awaryjnej. Aby uzyskać więcej informacji, zobacz [Procedura odzyskiwania po awarii w pracy awaryjnej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery).
- Wolumin rozruchowy dla *klasy SKU typu I* jest replikowany do węzła odzyskiwania po awarii.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Pojedynczy węzeł z modułem ODZYSKIWANIA PO (wielofunkcyjnym) przy użyciu replikacji magazynu
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z jednym lub wieloma identyfikatorami SID, z replikacją opartą na magazynie do lokacji odzyskiwania po awarii dla podstawowego identyfikatora SID. Na diagramie tylko system z pojedynczym identyfikatorem SID jest przedstawiony w lokacji głównej, ale obsługiwane są również systemy wielosydyskowe (MCOS). W lokacji odzyskiwania po awarii jednostka HLI jest używana dla wystąpienia kontroli jakości, podczas gdy operacje produkcyjne są uruchomione z lokacji głównej. Podczas pracy awaryjnej odzyskiwania po awarii (lub testu trybu failover) wystąpienie kontroli jakości w lokacji odzyskiwania po awarii jest ścią głą amortyzowanie.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z modułem ODZYSKIWANIA PO (wielofunkcyjnym) przy użyciu replikacji magazynu](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W miejscu dr**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/shared/QA-SID | Instalacja HANA dla QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora QA SID |
|/hana/logbackups/QA-SID | Ponownie dzienniki dla identyfikatora QA SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W przypadku mcos: rozkład rozmiaru woluminu jest oparty na rozmiarze bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary baz danych w pamięci są obsługiwane w środowisku wielosydyskowym, zobacz [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "Wymagane do instalacji HANA") dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika i udostępnione woluminy (oznaczone jako "Replikacja magazynu") są replikowane za pomocą migawki z lokacji produkcyjnej. Woluminy te są montowane tylko podczas pracy awaryjnej. Aby uzyskać więcej informacji, zobacz [Procedura odzyskiwania po awarii w pracy awaryjnej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika, dziennik i udostępnione woluminy dla kontroli jakości (oznaczone jako "instalacja wystąpienia kontroli jakości") są skonfigurowane dla instalacji wystąpienia kontroli jakości.
- Wolumin rozruchowy dla *klasy SKU typu I* jest replikowany do węzła odzyskiwania po awarii.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR z STONITH dla wysokiej dostępności
 
Ta topologia obsługuje dwa węzły konfiguracji replikacji systemu HANA. Ta konfiguracja jest obsługiwana tylko dla pojedynczych wystąpień HANA w węźle. Oznacza to, że scenariusze MCOS *nie* są obsługiwane.

> [!NOTE]
> Od grudnia 2019 r. ta architektura jest obsługiwana tylko dla systemu operacyjnego SUSE.


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR z STONITH dla wysokiej dostępności](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Używany do STONITH |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Używany do STONITH |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W węźle pomocniczym**|
|/hana/udostępniony/SID | Instalacja HANA dla wtórnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla dodatkowego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla dodatkowego identyfikatora SID | 
|/hana/logbackups/SID | Ponawianie dzienników dla dodatkowego identyfikatora SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W przypadku mcos: rozkład rozmiaru woluminu jest oparty na rozmiarze bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary baz danych w pamięci są obsługiwane w środowisku wielosydyskowym, zobacz [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: Dla konfiguracji STONITH skonfigurowano dysk SBD. Jednak użycie STONITH jest opcjonalne.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Wysoka dostępność dzięki hsr i odzyskiwania po awarii z replikacją pamięci masowej
 
Ta topologia obsługuje dwa węzły dla konfiguracji replikacji systemu HANA. Obsługiwane są zarówno normalne, jak i wielozadaniowe rejestratory. Te konfiguracje są obsługiwane tylko dla pojedynczych wystąpień HANA w węźle. Oznacza to, że scenariusze MCOS *nie* są obsługiwane w tych konfiguracjach.

Na diagramie scenariusz wielofunkcyjny jest przedstawiony w lokacji odzyskiwania po awarii, gdzie jednostka HLI jest używana dla wystąpienia kontroli jakości, podczas gdy operacje produkcyjne są uruchomione z lokacji głównej. Podczas pracy awaryjnej odzyskiwania po awarii (lub testu trybu failover) wystąpienie kontroli jakości w lokacji odzyskiwania po awarii jest ścią głą amortyzowanie. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Wysoka dostępność dzięki hsr i odzyskiwania po awarii z replikacją pamięci masowej](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Używany do STONITH |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Używany do STONITH |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W węźle głównym w lokacji głównej**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W węźle pomocniczym w lokacji głównej**|
|/hana/udostępniony/SID | Instalacja HANA dla wtórnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla dodatkowego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla dodatkowego identyfikatora SID | 
|/hana/logbackups/SID | Ponawianie dzienników dla dodatkowego identyfikatora SID |
|**W miejscu dr**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/shared/QA-SID | Instalacja HANA dla QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora QA SID |
|/hana/logbackups/QA-SID | Ponownie dzienniki dla identyfikatora QA SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W przypadku mcos: rozkład rozmiaru woluminu jest oparty na rozmiarze bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary baz danych w pamięci są obsługiwane w środowisku wielosydyskowym, zobacz [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: Dla konfiguracji STONITH skonfigurowano dysk SBD. Jednak użycie STONITH jest opcjonalne.
- W lokacji odzyskiwania po awarii: Dwa zestawy woluminów magazynu są wymagane dla replikacji *węzła podstawowego* i pomocniczego.
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "Wymagane do instalacji HANA") dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika i udostępnione woluminy (oznaczone jako "Replikacja magazynu") są replikowane za pomocą migawki z lokacji produkcyjnej. Woluminy te są montowane tylko podczas pracy awaryjnej. Aby uzyskać więcej informacji, zobacz [Procedura odzyskiwania po awarii w pracy awaryjnej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika, dziennik i udostępnione woluminy dla kontroli jakości (oznaczone jako "instalacja wystąpienia kontroli jakości") są skonfigurowane dla instalacji wystąpienia kontroli jakości.
- Wolumin rozruchowy dla *klasy SKU typu I* jest replikowany do węzła odzyskiwania po awarii.


## <a name="host-auto-failover-11"></a>Automatyczne tryb failover hosta (1+1)
 
Ta topologia obsługuje dwa węzły w konfiguracji automatycznego trybu failover hosta. Istnieje jeden węzeł z rolą głównego/roboczego, a drugi jako stan gotowości. *SAP obsługuje ten scenariusz tylko dla S/4 HANA.* Aby uzyskać więcej informacji, zobacz [uwaga systemu operacyjnego 2408419 - SAP S/4HANA - Obsługa wielu węzłów](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagram architektury  

![Automatyczne tryb failover hosta (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Komunikacja między węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Komunikacja między węzłami |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W węzłach głównych i rezerwowych**|
|/hana/udostępniony | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |



### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W trybie gotowości: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "Wymagane do instalacji HANA") dla instalacji wystąpienia HANA w jednostce rezerwowej.
 

## <a name="scale-out-with-standby"></a>Skalowanie w poziomie ze stanem wstrzymania
 
Ta topologia obsługuje wiele węzłów w konfiguracji skalowawki w poziomie. Istnieje jeden węzeł z rolą wzorcową, jeden lub więcej węzłów z rolą procesu roboczego i jeden lub więcej węzłów jako rezerwy. Jednak może istnieć tylko jeden węzeł główny w dowolnym punkcie w czasie.


### <a name="architecture-diagram"></a>Diagram architektury  

![Skalowanie w poziomie ze stanem wstrzymania](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Komunikacja między węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Komunikacja między węzłami |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W węzłach wzorca, pracownika i gotowości**|
|/hana/udostępniony | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |


## <a name="scale-out-without-standby"></a>Skalowanie w poziomie bez gotowości
 
Ta topologia obsługuje wiele węzłów w konfiguracji skalowawki w poziomie. Istnieje jeden węzeł z rolą wzorcową i jeden lub więcej węzłów z rolą procesu roboczego. Jednak może istnieć tylko jeden węzeł główny w dowolnym punkcie w czasie.


### <a name="architecture-diagram"></a>Diagram architektury  

![Skalowanie w poziomie bez gotowości](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Komunikacja między węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Komunikacja między węzłami |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W węzłach główny i roboczy**|
|/hana/udostępniony | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Skalowanie w poziomie przy użyciu odzyskiwania po awarii przy użyciu replikacji magazynu
 
Ta topologia obsługuje wiele węzłów w skalowawce w poziomie z po awarii. Obsługiwane są zarówno normalne, jak i wielozadaniowe rejestratory. Na diagramie przedstawiono tylko pojedynczy cel ODZYSKIWANIA PO. Można zażądać tej topologii z lub bez węzła wstrzymania.


### <a name="architecture-diagram"></a>Diagram architektury  

![Skalowanie w poziomie przy użyciu odzyskiwania po awarii przy użyciu replikacji magazynu](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-HLI |
| B | TYP I | eth2.tenant | eno3.najemca | Komunikacja między węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Komunikacja między węzłami |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/udostępniony | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W węźle odzyskiwania po awarii**|
|/hana/udostępniony | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
-  W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "Wymagane do instalacji HANA") dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika i udostępnione woluminy (oznaczone jako "Replikacja magazynu") są replikowane za pomocą migawki z lokacji produkcyjnej. Woluminy te są montowane tylko podczas pracy awaryjnej. Aby uzyskać więcej informacji, zobacz [Procedura odzyskiwania po awarii w pracy awaryjnej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Wolumin rozruchowy dla *klasy SKU typu I* jest replikowany do węzła odzyskiwania po awarii.


## <a name="single-node-with-dr-using-hsr"></a>Pojedynczy węzeł z dr przy użyciu HSR
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z jednym identyfikatorem SID, z replikacją systemu HANA do lokacji odzyskiwania po awarii dla podstawowego identyfikatora SID. Na diagramie tylko system z pojedynczym identyfikatorem SID jest przedstawiony w lokacji głównej, ale obsługiwane są również systemy wielosydyskowe (MCOS).

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z dr przy użyciu HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-do-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-do-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane na obu jednostkach HLI (podstawowy i dr):

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|/hana/udostępniony/SID | Instalacja HANA dla identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID | 
|/hana/logbackups/SID | Ponawianie dzienników dla identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W przypadku mcos: rozkład rozmiaru woluminu jest oparty na rozmiarze bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary baz danych w pamięci są obsługiwane w środowisku wielosydyskowym, zobacz [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- Węzeł podstawowy synchronizuje się z węzłem odzyskiwania po awarii przy użyciu replikacji systemu HANA. 
- [Globalny zasięg](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) służy do łączenia obwodów usługi ExpressRoute w celu tworzenia sieci prywatnej między sieciami regionalnymi.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Pojedynczy węzeł HSR do odzyskiwania po awarii (zoptymalizowany pod kątem kosztów) 
 
 Ta topologia obsługuje jeden węzeł w konfiguracji skalowania w górę z jednym identyfikatorem SID, z replikacją systemu HANA do lokacji odzyskiwania po awarii dla podstawowego identyfikatora SID. Na diagramie tylko system z pojedynczym identyfikatorem SID jest przedstawiony w lokacji głównej, ale obsługiwane są również systemy wielosydyskowe (MCOS). W lokacji odzyskiwania po awarii jednostka HLI jest używana dla wystąpienia kontroli jakości, podczas gdy operacje produkcyjne są uruchomione z lokacji głównej. Podczas pracy awaryjnej odzyskiwania po awarii (lub testu trybu failover) wystąpienie kontroli jakości w lokacji odzyskiwania po awarii jest ścią głą amortyzowanie.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł HSR do odzyskiwania po awarii (zoptymalizowany pod kątem kosztów)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-do-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-do-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W miejscu dr**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|/hana/shared/QA-SID | Instalacja HANA dla QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora QA SID |
|/hana/logbackups/QA-SID | Ponownie dzienniki dla identyfikatora QA SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W przypadku mcos: rozkład rozmiaru woluminu jest oparty na rozmiarze bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary baz danych w pamięci są obsługiwane w środowisku wielosydyskowym, zobacz [Omówienie i architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wystąpienie PROD w lokacji odzyskiwania po awarii") dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika, dziennik i udostępnione woluminy dla kontroli jakości (oznaczone jako "instalacja wystąpienia kontroli jakości") są skonfigurowane dla instalacji wystąpienia kontroli jakości.
- Węzeł podstawowy synchronizuje się z węzłem odzyskiwania po awarii przy użyciu replikacji systemu HANA. 
- [Globalny zasięg](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) służy do łączenia obwodów usługi ExpressRoute w celu tworzenia sieci prywatnej między sieciami regionalnymi.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Wysoka dostępność i odzyskiwanie po awarii dzięki HSR 
 
 Ta topologia obsługuje dwa węzły dla konfiguracji replikacji systemu HANA dla wysokiej dostępności regionów lokalnych. W przypadku odzyskiwania po awarii trzeci węzeł w regionie odzyskiwania po awarii synchronizuje się z lokacją główną przy użyciu trybu HSR (trybu asynchronicznego). 

### <a name="architecture-diagram"></a>Diagram architektury  

![Wysoka dostępność i odzyskiwanie po awarii dzięki HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-do-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-do-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W miejscu dr**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wystąpienie PROD DR") dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- Węzeł lokacji głównej synchronizuje się z węzłem odzyskiwania po awarii przy użyciu replikacji systemu HANA. 
- [Globalny zasięg](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) służy do łączenia obwodów usługi ExpressRoute w celu tworzenia sieci prywatnej między sieciami regionalnymi.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Wysoka dostępność i odzyskiwanie po awarii dzięki HSR (zoptymalizowane pod względem kosztów)
 
 Ta topologia obsługuje dwa węzły dla konfiguracji replikacji systemu HANA dla wysokiej dostępności regionów lokalnych. W przypadku odzyskiwania po awarii trzeci węzeł w regionie odzyskiwania po awarii synchronizuje się z lokacją główną przy użyciu hsr (tryb asynchroniowy), podczas gdy inne wystąpienie (na przykład QA) jest już uruchomiony z węzła odzyskiwania po awarii. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Wysoka dostępność i odzyskiwanie po awarii dzięki HSR (zoptymalizowane pod względem kosztów)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-do-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.najemca | Skonfigurowane, ale nie używane |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-do-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Skonfigurowane, ale nie używane |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W miejscu dr**|
|/hana/udostępniony/SID | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|/hana/shared/QA-SID | Instalacja HANA dla QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora QA SID |
|/hana/logbackups/QA-SID | Ponownie dzienniki dla identyfikatora QA SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wystąpienie PROD DR") dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- W lokacji odzyskiwania po awarii: dane, kopie zapasowe dziennika, dziennik i udostępnione woluminy dla kontroli jakości (oznaczone jako "instalacja wystąpienia kontroli jakości") są skonfigurowane dla instalacji wystąpienia kontroli jakości.
- Węzeł lokacji głównej synchronizuje się z węzłem odzyskiwania po awarii przy użyciu replikacji systemu HANA. 
- [Globalny zasięg](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) służy do łączenia obwodów usługi ExpressRoute w celu tworzenia sieci prywatnej między sieciami regionalnymi.

## <a name="scale-out-with-dr-using-hsr"></a>Skalowanie w poziomie z dr przy użyciu HSR
 
Ta topologia obsługuje wiele węzłów w skalowawce w poziomie z po awarii. Można zażądać tej topologii z lub bez węzła wstrzymania. Węzeł lokacji głównej synchronizuje się z węzłem lokacji odzyskiwania po awarii przy użyciu usługi Replikacja systemu HANA (tryb asynchronowy).


### <a name="architecture-diagram"></a>Diagram architektury  

[![Skalowanie w poziomie z dr przy użyciu HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Wstępnie skonfigurowane są następujące interfejsy sieciowe:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa z SUSE OS | Nazwa z RHEL OS | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.najemca | Klient-do-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.najemca | Komunikacja między węzłami |
| C | TYP I | eth1.tenant | eno2.tenant | Węzeł do magazynu |
| D | TYP I | eth4.tenant | eno4.najemca | Skonfigurowane, ale nie używane |
| A | TYP II | vlan\<najemcaNie> | team0.tenant | Klient-do-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Komunikacja między węzłami |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Węzeł do magazynu |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Skonfigurowane, ale nie używane |

### <a name="storage"></a>Magazyn
Wstępnie skonfigurowane są następujące punkty instalacji:

| Punkt montażu | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/udostępniony | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |
|**W węźle odzyskiwania po awarii**|
|/hana/udostępniony | Instalacja HANA do produkcji SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla produkcyjnego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla produkcyjnego identyfikatora SID | 
|/hana/logbackups/SID | Ponowne rejestrowanie identyfikatora SID produkcji |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest dowiązaniem symbolicznym do /hana/shared/SID.
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane dla produkcyjnej instalacji wystąpienia HANA w jednostce DR HLI. 
- Węzeł lokacji głównej synchronizuje się z węzłem odzyskiwania po awarii przy użyciu replikacji systemu HANA. 
- [Globalny zasięg](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) służy do łączenia obwodów usługi ExpressRoute w celu tworzenia sieci prywatnej między sieciami regionalnymi.


## <a name="next-steps"></a>Następne kroki
- [Infrastruktura i łączność](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) dla dużych wystąpień HANA
- [Wysoka dostępność i odzyskiwanie po awarii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) dla dużych wystąpień HANA
