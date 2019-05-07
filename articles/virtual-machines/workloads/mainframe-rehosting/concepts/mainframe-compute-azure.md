---
title: Przenieś obliczeń mainframe na maszynach wirtualnych platformy Azure
description: Porównaj zasobów favorably do pojemności mainframe obliczeń Azure, aby można było migrować i modernizuj aplikacje z14 firmy IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 335a056a34412a7ed148613bfff59ecb30053e09
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190315"
---
# <a name="move-mainframe-compute-to-azure"></a>Przenoszenie komputera mainframe obliczeniowych na platformę Azure

Komputery mainframe mają reputację wysokiej niezawodności i dostępności i w dalszym ciągu być zaufanych sieci szkieletowej wiele przedsiębiorstw. Są one często traktować prawie nieograniczoną skalowalność i mocy obliczeniowej, jak również. Jednak niektóre jednostki mają przestarzałego możliwości największych mainframe dostępne. Jeśli ta funkcja sprawa takich jak Ty, platforma Azure oferuje elastyczność, zasięgu i infrastruktury oszczędności.

Uruchamianie obciążeń mainframe na Microsoft Azure, musisz wiedzieć, jak Twoja mainframe obliczenia porównanie funkcji na platformie Azure. Oparte na mainframe z14 IBM (najbardziej aktualną model na chwilę obecną), w tym artykule wyjaśniono sposób uzyskiwania porównywalnych wyników na platformie Azure.

Aby rozpocząć pracę, należy wziąć pod uwagę środowiska obok siebie. Poniższa ilustracja porównuje środowisku komputera mainframe do uruchamiania aplikacji do środowiska hostingu platformy Azure.

![Usługi i emulacji środowisk oferty porównywalne pomocy technicznej platformy Azure i upraszczają migrację](media/mainframe-compute-azure.png)

Możliwości mainframe jest często używany dla transakcji online (OLTP) systemów, które obsługiwać miliony aktualizacje dla tysięcy użytkowników przetwarzania. Aplikacje te często używają oprogramowania dla przetwarzania transakcji, obsługi ekranu i pozycja formularza. Mogą używać klienta informacji kontroli systemu (CICS), informacje o systemie zarządzania (IMS) lub pakiet interfejsu transakcji (TIP).

Jak pokazano na rysunku, emulator modułu TPM w systemie Azure może obsłużyć obciążenia CICS i IMS. Emulator systemu przetwarzania wsadowego na platformie Azure pełni rolę zlecenia kontrolki języka (JCL). Migracji komputera mainframe danych do baz danych Azure, takich jak Azure SQL Database. Usługi platformy Azure lub innym oprogramowaniem, hostowana w usłudze Azure Virtual Machines może służyć do zarządzania systemem.

## <a name="mainframe-compute-at-a-glance"></a>Obliczeń mainframe na pierwszy rzut oka

W generowanym z14, procesory są uporządkowane w maksymalnie czterech *szuflady*. A *szuflady* jest po prostu klastra procesorów i mikroukłady. Szuflada każdy może mieć sześć mikroukładami active Procesor centralny (CP), a każdy CP ma 10 mikroukładami kontrolera (SC) systemu. W terminologii Intel x86 istnieje sześć gniazda na szuflady, 10 rdzeni na gniazdo i cztery szuflady. Ta architektura zapewnia przybliżoną wielokrotność 24 sockets i 240 rdzeni, maksymalna, aby uzyskać z14.

Szybkie z14 CP ma prędkość zegara 5.2 GHz. Zazwyczaj z14 są dostarczane przez wszystkie CPs w polu. Zostały aktywowane, zgodnie z potrzebami. Klient często jest rozliczane za co najmniej cztery godziny czasu obliczeń na miesiąc, niezależnie od rzeczywistego użycia.

Procesor komputera mainframe można skonfigurować jako jeden z następujących typów:

- Ogólnego przeznaczenia (GP) procesora
- Informacje o zintegrowanego systemu z procesora (zIIP)
- Zintegrowane możliwości procesora, systemu Linux (IFL)
- Procesor Assist systemu (SAP)
- Zintegrowane procesora sprzężenia funkcji (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Skalowanie mainframe w usłudze compute a

Komputery mainframe firmy IBM oferują możliwość skalowania do 240 rdzeni (bieżący z14 rozmiar pojedynczego systemu). Ponadto komputery mainframe firmy IBM można skalować w poziomie przez funkcję o nazwie funkcji sprzężenia (CF). CF umożliwia wielu dużych systemach jednocześnie dostęp do tych samych danych. Za pomocą usługi CF, mainframe równoległe Sysplex technologii grup mainframe procesorów w klastrach. Ten przewodnik został napisany, funkcja równoległe Sysplex obsługiwanych 32 grupowania 64 procesory. Maksymalnie 2048 procesory mogą być grupowane w ten sposób, aby skalować wydajność obliczeniową.

CF umożliwia klastrów obliczeniowych, udostępnianie danych bezpośredni dostęp. Służy do blokowania informacje, informacje o pamięci podręcznej i wykaz danych udostępnionych zasobów. Sysplex równoległych, przy użyciu co najmniej jeden CFs można traktować jako "udostępnionego wszystko" klaster skalowalnego w poziomie zasobów obliczeniowych. Aby uzyskać więcej informacji o tych funkcjach, zobacz [Sysplex równolegle na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) w witrynie internetowej firmy IBM.

Aplikacje mogą używać tych funkcji w celu zapewnienia wysokiej dostępności i wydajności skalowalnego w poziomie. W przypadku informacji o jak CICS za pomocą równoległych Sysplex CF, Pobierz [IBM CICS i sprzężenia funkcji: Poza podstawy](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Obliczeniowe platformy Azure w skrócie

Niektóre osoby przez pomyłkę myśl, że serwerów opartych na Intel nie są wydajne jako mainframe. Jednak nowych systemów podstawowe w stojaku, opartych na architekturze Intel mieć znacznie obliczenia pojemności jako mainframe. W tej sekcji opisano Azure infrastruktura jako usługa (IaaS) opcje przetwarzania i przechowywania danych. System Azure oferuje również opcji platforma jako usługa (PaaS), ale ten artykuł koncentruje się na opcji IaaS, które zapewniają pojemność mainframe porównywalny.

Maszyny wirtualne platformy Azure zapewniają moc obliczeniową w zakresie rozmiarów i typów. Na platformie Azure Procesora wirtualnego (vCPU) około jest równa core na mainframe.

Obecnie rozmiary zakresu platformy Azure maszyna wirtualna zapewnia z zakresu od 1 do 128 procesorów wirtualnych. Typów maszyn wirtualnych (VM) są zoptymalizowane pod kątem konkretnych obciążeń. Na przykład na poniższej liście przedstawiono typy maszyn wirtualnych (bieżąca na chwilę obecną) i ich zastosowań zalecane:

| Rozmiar     | Typ i opis                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Seria D | Ogólnego przeznaczenia z 64 procesory wirtualne i maksymalnie prędkość zegara 3,5 GHz                           |
| Seria E | Zoptymalizowane pod kątem z maksymalnie 64 procesorów wirtualnych Vcpu pamięci                                                 |
| Seria F | Obliczenia zoptymalizowane przy użyciu 64 procesorów Vcpu i prędkość zegara 3..7 GHz                       |
| Seria H | Zoptymalizowane pod kątem aplikacji wysokowydajnych obliczeń (HPC)                          |
| Seria L | Magazyn zoptymalizowany pod kątem aplikacji o wysokiej przepływności, wspierane przez bazy danych, takich jak NoSQL |
| Seria M | Największy zasobów obliczeniowych i pamięci zoptymalizowane pod kątem maszyn wirtualnych z maksymalnie 128 procesorów wirtualnych                        |

Aby uzyskać szczegółowe informacje o dostępnych maszyn wirtualnych, zobacz [serię maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Z14 mainframe może mieć maksymalnie 240 rdzeni. Jednak mainframe z14 prawie nigdy nie używać wszystkich rdzeni dla jednej aplikacji lub obciążeń. Zamiast tego komputera mainframe oddziela, chroniąc je obciążeń na partycje logiczne (LPARs) i LPARs mają klasyfikacje — MIPS (miliony instrukcji na sekundę) lub MSU (milion jednostki usługi). Podczas określania porównywalny rozmiar maszyny Wirtualnej, wymagany do uruchomienia obciążenia komputera mainframe na platformie Azure, ocena czynnikiem MIPS (lub MSU).

Poniżej przedstawiono ogólne oszacowania:

-   150 MIPS na każdy procesor wirtualny

-   1000 MIPS na procesor

Aby określić odpowiedni rozmiar maszyny Wirtualnej dla określonego obciążenia w LPAR, najpierw należy zoptymalizować maszyn wirtualnych dla obciążenia. Następnie określ liczbę procesorów wirtualnych Vcpu potrzebne. Zachowawcze szacuje się 150 MIPS, na każdy procesor wirtualny. Oparte na te dane szacunkowe, na przykład, seria F Obsługa maszyny Wirtualnej z 16 procesorów wirtualnych Vcpu można łatwo obciążenia programu IBM Db2 pochodzące z LPAR z 2400 MIPS.

## <a name="azure-compute-scale-up"></a>Usługa Azure compute skalowanie w górę

Maszyny wirtualne serii M można skalować do 128 procesorów wirtualnych Vcpu (w tym czasie, który został zapisany w tym artykule). Za pomocą zachowawcze oszacowanie 150 MIPS na procesor wirtualny vCPU, maszyn wirtualnych serii M jest równa około 19,000 MIPS. Ogólne reguła dla szacowania MIPS mainframe jest MIPS 1000 dla każdego procesora. Z14 mainframe mogą mieć maksymalnie 24 procesorów i zapewnić około 24 000 MIPS system pojedynczego komputera mainframe.

Największy mainframe pojedynczego z14 ma około 5000 MIPS więcej niż największa maszyny Wirtualnej dostępne na platformie Azure. Jeszcze jest ważne, aby porównać, jak odbywa się wdrażanie obciążeń. Jeśli mainframe system relacyjnej bazy danych i aplikacji, są zazwyczaj wdrożone na tym samym fizycznego komputera mainframe — każde we własnej LPAR. Tego samego rozwiązania na platformie Azure jest często wdrażana przy użyciu jednej maszyny Wirtualnej dla aplikacji i oddzielnych, odpowiednio o rozmiarze maszyny Wirtualnej dla bazy danych.

Na przykład, jeśli system procesora wirtualnego vCPU M64 obsługuje aplikację i M96 vCPU jest używana dla bazy danych, około 150 procesorów wirtualnych Vcpu są potrzebne — lub około 24 000 MIPS, jak przedstawiono na poniższym rysunku.

![Porównywanie obciążenia wdrożeń 24 000 MIPS](media/mainframe-compute-mips.png)

Podejście to migrację LPARs dla poszczególnych maszyn wirtualnych. Następnie Azure łatwo jest skalowany w górę do rozmiaru wymagane dla większości aplikacji, które zostały wdrożone w systemie pojedynczej mainframe.

## <a name="azure-compute-scale-out"></a>Usługa Azure compute skalowalnego w poziomie

Jedną z zalet rozwiązanie oparte na platformie Azure jest możliwość skalowania w poziomie. Łatwe skalowanie prawie nieograniczoną obliczania pojemności dostępnej dla aplikacji. Platforma Azure obsługuje wiele metod skalowania mocy obliczeniowej:

- **Równoważenie obciążenia w klastrze.** W tym scenariuszu aplikacja może użyć [moduł równoważenia obciążenia](/azure/load-balancer/load-balancer-overview) lub Menedżera zasobów w celu rozłożenia obciążenia między wiele maszyn wirtualnych w klastrze. Jeśli coraz większej mocy obliczeniowej, potrzebne są możliwości obliczeniowe, dodatkowe maszyny wirtualne zostaną dodane do klastra.

- **Zestawy skalowania maszyn wirtualnych.** W tym scenariuszu serii skalowania aplikacji do dodatkowych [zasoby obliczeniowe](/azure/virtual-machine-scale-sets/overview) na podstawie użycia maszyny Wirtualnej. Kiedy zapotrzebowanie spada, liczba maszyn wirtualnych w zestawie skalowania, można także przejść w dół, zapewniając efektywne wykorzystanie mocy obliczeniowej.

- **PaaS skalowania.** Zasoby obliczeniowe skalowania ofert PaaS na platformie Azure. Na przykład [usługi Azure Service Fabric](/azure/service-fabric/service-fabric-overview) przydziela zasoby obliczeniowe, aby spełnić wzrost liczby żądań.

- **Klastry Kubernetes.** Aplikacje na platformie Azure mogą używać [klastrów Kubernetes](/azure/aks/concepts-clusters-workloads) usługi compute dla określonych zasobów. Usługa Azure Kubernetes Service (AKS) to usługa zarządzana, która organizuje węzłów rozwiązania Kubernetes, pul i klastry na platformie Azure.

Wybranie odpowiedniej metody skalowania zasobów obliczeniowych, ważne jest zrozumienie, jak platforma Azure i mainframe różnią się. Klucz jest jak — lub, jeśli — dane zostaną udostępnione, zasobów obliczeniowych. Na platformie Azure danych (domyślnie) nie jest zazwyczaj współużytkowana przez wiele maszyn wirtualnych. Jeśli udostępnianie danych jest wymagane przez klaster obliczeniowy wiele maszyn wirtualnych w skalowalnym w poziomie, udostępnione dane muszą znajdować się w zasobie, która obsługuje tę funkcję. Na platformie Azure udostępnianie danych obejmuje magazynu jak opisano w poniższej sekcji.

## <a name="azure-compute-optimization"></a>Optymalizacja obliczeniowych platformy Azure

Można zoptymalizować każdej warstwy przetwarzania w architekturze platformy Azure. Użyj najbardziej odpowiedni typ maszyn wirtualnych i funkcji dla każdego środowiska. Na poniższej ilustracji przedstawiono jeden wzorzec potencjalnych do wdrażania maszyn wirtualnych na platformie Azure w celu obsługi aplikacji CICS, która używa bazy danych Db2. W lokacji głównej, produkcji, przedprodukcyjne i testowania maszyny wirtualne są wdrażane o wysokiej dostępności. Lokacja dodatkowa jest kopia zapasowa i odzyskiwanie po awarii.

Każda warstwa oferuje również odpowiedni awarii usługi recovery services. Na przykład środowiska produkcyjnego i maszyn wirtualnych z bazy danych może wymagać gorąca czy ciepło odzyskiwania podczas tworzenia i testowania maszyn wirtualnych obsługują zimnych odzyskiwania.

![Wdrożenia o wysokiej dostępności, która obsługuje odzyskiwanie po awarii](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Kolejne kroki

- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting na maszynach wirtualnych platformy Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Przenieś magazyn komputera mainframe na platformie Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Zasoby firmy IBM

- [Równoległe Sysplex na Z firmy IBM](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS i funkcji sprzężenia: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Tworzenie użytkowników wymagane dla bazy danych Db2 pureScale instalacji funkcji](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt — Utwórz wystąpienie, polecenie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Bazy danych Db2 pureScale Clustered rozwiązanie bazy danych](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Chmura platformy Microsoft Azure Government dla aplikacji mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Firma Microsoft i FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Więcej zasobów migracji

- [Alliance modernizacji platformy: IBM Db2 na platformie Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Wirtualne centrum danych usługi Azure Lift- and -Shift przewodnik](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
