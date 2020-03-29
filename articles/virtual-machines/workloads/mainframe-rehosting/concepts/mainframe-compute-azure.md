---
title: Przenoszenie obliczeń komputera mainframe na maszyny wirtualne platformy Azure
description: Zasoby obliczeniowe platformy Azure porównują się korzystnie do pojemności komputerów mainframe, dzięki czemu można migrować i modernizować aplikacje IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288935"
---
# <a name="move-mainframe-compute-to-azure"></a>Przenoszenie obliczeń komputera mainframe na platformę Azure

Komputery mainframe cieszą się renomą wysokiej niezawodności i dostępności oraz nadal są zaufanym kręgosłupem wielu przedsiębiorstw. Często uważa się, że mają prawie nieograniczoną skalowalność i moc obliczeniową. Jednak niektóre przedsiębiorstwa przerosły możliwości największych dostępnych komputerów mainframe. Jeśli brzmi to jak Ty, platforma Azure oferuje elastyczność, zasięg i oszczędność infrastruktury.

Aby uruchamiać obciążenia komputerów mainframe na platformie Microsoft Azure, musisz wiedzieć, jak możliwości obliczeniowe komputera mainframe są porównywane z platformą Azure. Na podstawie ibm z14 mainframe (najbardziej aktualny model w tym piśmie), w tym artykule opisano, jak uzyskać porównywalne wyniki na platformie Azure.

Aby rozpocząć, należy wziąć pod uwagę środowiska obok siebie. Na poniższej ilustracji porównano środowisko komputera mainframe do uruchamiania aplikacji ze środowiskiem hostingu platformy Azure.

![Usługi platformy Azure i środowiska emulacji oferują porównywalną pomoc techniczną i usprawniają migrację](media/mainframe-compute-azure.png)

Moc komputerów mainframe jest często używana do przetwarzania transakcji online (OLTP), które obsługują miliony aktualizacji dla tysięcy użytkowników. Aplikacje te często używają oprogramowania do przetwarzania transakcji, obsługi ekranu i wprowadzania formularzy. Mogą oni korzystać z systemu kontroli informacji o klientach (CICS), systemu zarządzania informacjami (IMS) lub pakietu interfejsu transakcji (TIP).

Jak pokazano na rysunku, emulator modułu TPM na platformie Azure może obsługiwać obciążenia CICS i IMS. Emulator systemu wsadowego na platformie Azure pełni rolę języka kontroli zadań (JCL). Dane mainframe są migrowane do baz danych platformy Azure, takich jak usługa Azure SQL Database. Usługi platformy Azure lub inne oprogramowanie hostowane w maszynach wirtualnych platformy Azure może służyć do zarządzania systemem.

## <a name="mainframe-compute-at-a-glance"></a>Obliczenia mainframe w skrócie

W modelu głównym z14 procesory są rozmieszczone w maksymalnie czterech *szufladach.* *Szuflada* jest po prostu klastrem procesorów i chipsetów. Każda szuflada może mieć sześć aktywnych układów procesora centralnego (CP), a każda CP ma 10 układów kontrolera systemu (SC). W terminologii Intel x86 na szufladę przypada sześć gniazd, 10 rdzeni na gniazdo i cztery szuflady. Ta architektura zapewnia odpowiednik 24 gniazd i 240 rdzeni, maksymalnie dla z14.

Szybki Z14 CP ma taktowanie 5,2 GHz. Zazwyczaj z14 jest dostarczany ze wszystkimi CPs w polu. Są one aktywowane w razie potrzeby. Klient jest często naliczany przez co najmniej cztery godziny czasu obliczeniowego miesięcznie pomimo rzeczywistego użycia.

Procesor typu mainframe można skonfigurować jako jeden z następujących typów:

- Procesor ogólnego przeznaczenia (GP)
- System ze Zintegrowanym Procesorem Informacyjnym (zIIP)
- Zintegrowany procesor Facility for Linux (IFL)
- Procesor wspomagania systemu (SAP)
- Zintegrowany procesor sprzęgający (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Skalowanie obliczeń mainframe w górę i na zewnątrz

Komputery mainframe IBM oferują możliwość skalowania do 240 rdzeni (obecny rozmiar z14 dla jednego systemu). Ponadto komputery mainframe IBM mogą być skalowane w poziomie za pomocą funkcji zwanej urządzeniem sprzęgało (CF). Cf umożliwia wielu systemom mainframe dostęp do tych samych danych jednocześnie. Za pomocą cf, mainframe Parallel Sysplex technologii grupowania procesorów mainframe w klastrach. Podczas pisania tego przewodnika funkcja Parallel Sysplex obsługiwała 32 grupy po 64 procesory. W ten sposób można zgrupować do 2048 procesorów w celu skalowania w poziomie pojemności obliczeniowej.

Cf umożliwia klastrom obliczeniowym udostępnianie danych z bezpośrednim dostępem. Służy do blokowania informacji, informacji o pamięci podręcznej i listy udostępnionych zasobów danych. Równoległy Sysplex przy użyciu jednego lub więcej plików CF można traktować jako "wspólne wszystko" skalowania w poziomie klastra obliczeniowego. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [Parallel Sysplex w systemie IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) w witrynie internetowej IBM.

Aplikacje mogą korzystać z tych funkcji, aby zapewnić zarówno wydajność skalowa w poziomie, jak i wysoką dostępność. Aby uzyskać informacje o tym, jak cics może korzystać z równoległego sysplexu z systemem CF, pobierz ibm [CICS i](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook Ds.

## <a name="azure-compute-at-a-glance"></a>Informacje obliczeniowe platformy Azure w skrócie

Niektórzy ludzie błędnie myślą, że serwery oparte na Intelu nie są tak potężne jak komputery mainframe. Jednak nowe systemy oparte na rdzeniu, oparte na intelu, mają tyle samo mocy obliczeniowej, co komputery mainframe. W tej sekcji opisano opcje infrastruktury platformy Azure jako usługi (IaaS) do przetwarzania i przechowywania. Platforma Azure udostępnia również opcje platformy jako usługi (PaaS), ale w tym artykule skupiono się na opcjach IaaS, które zapewniają porównywalną pojemność komputera mainframe.

Maszyny wirtualne platformy Azure zapewniają moc obliczeniową w zakresie rozmiarów i typów. Na platformie Azure wirtualny procesor (vCPU) w przybliżeniu odpowiada rdzeniu na mainframe.

Obecnie zakres rozmiarów maszyny wirtualnej platformy Azure zapewnia od 1 do 128 procesorów wirtualnych. Typy maszyn wirtualnych są zoptymalizowane pod kątem określonych obciążeń. Na przykład na poniższej liście przedstawiono typy maszyn wirtualnych (bieżący na czas pisania) i ich zalecane zastosowania:

| Rozmiar     | Typ i opis                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Seria D | Ogólnego przeznaczenia z 64 vCPU i prędkością zegara do 3,5 GHz                           |
| Seria E | Pamięć zoptymalizowana z maksymalnie 64 procesorami wirtualnymi                                                 |
| Seria F | Możliwość obliczenia zoptymalizowana pod kątem do 64 procesorów wirtualnych i taktowania 3..7 GHz                       |
| Seria H | Zoptymalizowany pod kątem zastosowań obliczeniowych o wysokiej wydajności (HPC)                          |
| Seria L | Pamięć masowa zoptymalizowana pod kątem aplikacji o wysokiej przepływności wspieranych przez bazy danych, takie jak NoSQL |
| Seria M | Maszyny wirtualne zoptymalizowane pod kątem mocy obliczeniowej i pamięci z maksymalnie 128 procesorami wirtualnymi                        |

Aby uzyskać szczegółowe informacje na temat dostępnych maszyn wirtualnych, zobacz [Seria maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Mainframe z14 może mieć do 240 rdzeni. Jednak komputery główne z14 prawie nigdy nie używają wszystkich rdzeni dla pojedynczej aplikacji lub obciążenia. Zamiast tego mainframe segreguje obciążenia na partycje logiczne (LPARs), a LPARs mają oceny — MIPS (miliony instrukcji na sekundę) lub MSU (Million Service Unit). Podczas określania porównywalnego rozmiaru maszyny Wirtualnej potrzebne do uruchomienia obciążenia mainframe na platformie Azure, współczynnik w klasyfikacji MIPS (lub MSU).

Oto ogólne szacunki:

-   150 MIPS na procesor wirtualny

-   1000 MIPS na procesor

Aby określić poprawny rozmiar maszyny Wirtualnej dla danego obciążenia w LPAR, najpierw zoptymalizuj maszynę wirtualną pod kątem obciążenia. Następnie należy określić liczbę potrzebnych procesorów wirtualnych. Konserwatywne oszacowanie wynosi 150 MIPS na procesor wirtualny. Na podstawie tego szacunku, na przykład, maszyna wirtualna z serii F z 16 procesorami wirtualnymi może z łatwością obsługiwać obciążenie IBM Db2 pochodzące z lparu z 2400 MIPS.

## <a name="azure-compute-scale-up"></a>Skalowanie obliczeń platformy Azure w górę

Maszyny wirtualne z serii M można skalować do 128 procesorów wirtualnych (w momencie pisania tego artykułu). Przy użyciu konserwatywnego oszacowania 150 MIPS na procesor wirtualny, maszyna wirtualna serii M odpowiada około 19 000 MIPS. Ogólna zasada szacowania MIPS dla modelu mainframe wynosi 1000 MIPS na procesor. Model mainframe z14 może mieć do 24 procesorów i zapewnia około 24 000 MIPS dla jednego systemu mainframe.

Największa pojedyncza klatka główna z14 ma około 5000 MIPS więcej niż największa maszyna wirtualna dostępna na platformie Azure. Jednak należy porównać sposób wdrażania obciążeń. Jeśli system mainframe ma zarówno aplikację, jak i relacyjną bazę danych, są one zazwyczaj wdrażane na tej samej fizycznej ramie mainframe — każda we własnym LPAR. To samo rozwiązanie na platformie Azure jest często wdrażane przy użyciu jednej maszyny Wirtualnej dla aplikacji i oddzielnej maszyny Wirtualnej o odpowiednim rozmiarze dla bazy danych.

Jeśli na przykład system vCPU M64 obsługuje aplikację, a dla bazy danych jest używany procesor wirtualny M96, potrzeba około 150 procesorów wirtualnych , czyli około 24 000 mipsów, jak pokazano na poniższej ilustracji.

![Porównanie wdrożeń obciążeń 24 000 MIPS](media/mainframe-compute-mips.png)

Podejście polega na migracji LPARs do poszczególnych maszyn wirtualnych. Następnie platforma Azure łatwo skaluje się do rozmiaru wymaganego dla większości aplikacji, które są wdrażane w jednym systemie mainframe.

## <a name="azure-compute-scale-out"></a>Skalowanie w poziomie obliczeń platformy Azure

Jedną z zalet rozwiązania opartego na platformie Azure jest możliwość skalowania w poziomie. Skalowanie sprawia, że prawie nieograniczona pojemność obliczeniowa jest dostępna dla aplikacji. Platforma Azure obsługuje wiele metod skalowania w poziomie mocy obliczeniowej:

- **Równoważenie obciążenia w klastrze.** W tym scenariuszu aplikacja może użyć [modułu równoważenia obciążenia](/azure/load-balancer/load-balancer-overview) lub menedżera zasobów, aby rozłożyć obciążenie między wiele maszyn wirtualnych w klastrze. Jeśli wymagana jest większa pojemność obliczeniowa, do klastra dodawane są dodatkowe maszyny wirtualne.

- **Zestawy skalowania maszyny wirtualnej.** W tym scenariuszu serii aplikacji można skalować do dodatkowych [zasobów obliczeniowych](/azure/virtual-machine-scale-sets/overview) na podstawie użycia maszyny Wirtualnej. Gdy popyt spada, liczba maszyn wirtualnych w zestawie skalowania może również spaść, zapewniając efektywne wykorzystanie mocy obliczeniowej.

- **Skalowanie PaaS.** Usługa Azure PaaS oferuje skalowanie zasobów obliczeniowych. Na przykład [usługa Azure Service Fabric](/azure/service-fabric/service-fabric-overview) przydziela zasoby obliczeniowe, aby sprostać wzrostowi liczby żądań.

- **Klastry kubernetes.** Aplikacje na platformie Azure mogą używać [klastrów Kubernetes](/azure/aks/concepts-clusters-workloads) dla usług obliczeniowych dla określonych zasobów. Usługa Azure Kubernetes Service (AKS) to usługa zarządzana, która organizuje węzły, pule i klastry kubernetes na platformie Azure.

Aby wybrać odpowiednią metodę skalowania zasobów obliczeniowych w poziomie, należy zrozumieć, jak różnią się platformy Azure i mainframes. Kluczem jest sposób i czy dane są współużytkowane przez zasoby obliczeniowe. Na platformie Azure dane (domyślnie) nie są zazwyczaj współużytkowane przez wiele maszyn wirtualnych. Jeśli udostępnianie danych jest wymagane przez wiele maszyn wirtualnych w klastrze obliczeniowym skalowawczym w poziomie, udostępnione dane muszą znajdować się w zasobie obsługującym tę funkcję. Na platformie Azure udostępnianie danych obejmuje magazyn, jak w poniższej sekcji omówiono.

## <a name="azure-compute-optimization"></a>Optymalizacja obliczeń platformy Azure

Można zoptymalizować każdą warstwę przetwarzania w architekturze platformy Azure. Użyj najbardziej odpowiedniego typu maszyn wirtualnych i funkcji dla każdego środowiska. Na poniższej ilustracji przedstawiono jeden potencjalny wzorzec wdrażania maszyn wirtualnych na platformie Azure do obsługi aplikacji CICS, która używa db2. W lokacji głównej maszyny wirtualne produkcyjne, przedprodukcyjne i testujące są wdrażane z wysoką dostępnością. Lokacja dodatkowa służy do tworzenia kopii zapasowych i odzyskiwania po awarii.

Każda warstwa może również zapewnić odpowiednie usługi odzyskiwania po awarii. Na przykład maszyny wirtualne produkcji i bazy danych może wymagać odzyskiwania na gorąco lub ciepło, podczas gdy rozwój i testowanie maszyn wirtualnych obsługuje odzyskiwanie przeziębienie.

![Wdrożenie o wysokiej dostępności, które obsługuje odzyskiwanie po awarii](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Następne kroki

- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Ponowne hostowanie pulpitu głównego na maszynach wirtualnych platformy Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Przenoszenie magazynu mainframe na platformę Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Zasoby IBM

- [Równoległy sysplex w ibm z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS i urządzenie sprzęgało: poza podstawami](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Tworzenie wymaganych użytkowników dla instalacji funkcji pureScale Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt — polecenie Utwórz wystąpienie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Rozwiązanie do klastrowej bazy danych Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Chmura microsoft azure dla instytucji rządowych dla aplikacji mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft i FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Więcej zasobów migracji

- [Przewodnik po wirtualnym centrum danych azure i przeniesieniu](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
