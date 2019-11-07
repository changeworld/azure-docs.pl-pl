---
title: Ciągłość działania & odzyskiwanie po awarii — regiony sparowane na platformie Azure
description: Dowiedz się więcej na temat kojarzenia regionalnego platformy Azure w celu zapewnienia odporności aplikacji podczas awarii centrum danych.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: b71048412f5715fd1b8ef3edf742716916672bd5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718746"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Ciągłość działania i odzyskiwanie po awarii (BCDR): wielosparowane regiony platformy Azure

## <a name="what-are-paired-regions"></a>Jakie są sparowane regiony?

Platforma Azure działa w wielu lokalizacje geograficzne na całym świecie. Lokalizacja geograficzna platformy Azure to zdefiniowany obszar świata, który zawiera co najmniej jeden region świadczenia usługi Azure. Region świadczenia usługi Azure jest obszarem geograficznym zawierającym co najmniej jedno centrum danych.

Każdy region platformy Azure jest sparowany z innym regionem w tej samej lokalizacji geograficznej, tworząc parę regionalną. Wyjątkiem jest Brazylia Południowa, która jest sparowana z regionem spoza położenia geograficznego. W obszarze pary regionów są używane aktualizacje serializacji platformy Azure (planowana konserwacja), dzięki czemu w danym momencie będzie aktualizowana tylko jedna sparowany region. W przypadku awarii mającej wpływ na wiele regionów, co najmniej jeden region w każdej parze będzie miał priorytet na potrzeby odzyskiwania.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Rysunek 1 — pary regionalne platformy Azure

| Geograficzne | Sparowane regiony |  |
|:--- |:--- |:--- |
| Azja |Azja Wschodnia |Azja Południowo-Wschodnia |
| Australia |Australia Wschodnia |Australia Południowo-Wschodnia |
| Australia |Australia Środkowa |Australia Środkowa 2 |
| Brazylia |Brazylia Południowa |Środkowo-południowe stany USA |
| Kanada |Kanada Środkowa |Kanada Wschodnia |
| Chiny |Chiny Północne |Chiny Wschodnie|
| Chiny |Chiny Północne 2 |Chiny Wschodnie 2|
| Europa |Europa Północna (Irlandia) |Europa Zachodnia (Holandia) |
| Francja |Francja Środkowa|Francja Południowa|
| Niemcy |Niemcy Środkowe |Niemcy Północno-Wschodnie |
| Indie |Indie Środkowe |Indie Południowe |
| Indie |Indie Zachodnie |Indie Południowe |
| Japonia |Japonia Wschodnia |Japonia Zachodnia |
| Korea |Korea Środkowa |Korea Południowa |
| Ameryka Północna |Wschodnie stany USA |Zachodnie stany USA |
| Ameryka Północna |Wschodnie stany USA 2 |Środkowe stany USA |
| Ameryka Północna |Środkowo-północne stany USA |Środkowo-południowe stany USA |
| Ameryka Północna |Zachodnie stany USA 2 |Środkowo-zachodnie stany USA 
| Republika Południowej Afryki | Północna Republika Południowej Afryki | Północna Republika Południowej Afryki
| Zjednoczone Królestwo |Zachodnie Zjednoczone Królestwo |Południowe Zjednoczone Królestwo |
| Zjednoczone Emiraty Arabskie | Północne Zjednoczone Emiraty Arabskie | Środkowe Zjednoczone Emiraty Arabskie
| Departament Obrony Stanów Zjednoczonych |US DoD — wschodnie stany |US DoD — środkowe stany |
| Rząd USA |Administracja USA — Arizona |Administracja USA — Teksas |
| Rząd USA |US Gov Iowa |Administracja USA — Wirginia |
| Rząd USA |Administracja USA — Wirginia |Administracja USA — Teksas |

Tabela 1 — mapowanie par regionalnych platformy Azure

- Indie Zachodnie są sparowane tylko w jednym kierunku. Region pomocniczy Indie Zachodnie to Indie Południowe, ale region pomocniczy w Republice Południowej Indie to środkowe Indie.
- Brazylia Południowa jest unikatowa, ponieważ jest sparowany z regionem spoza własnego położenia geograficznego. Region w regionie Brazylia Południowa jest Południowo-środkowe stany USA. Region w regionie Południowo-środkowe stany USA nie jest Brazylia Południowa.
- Region pomocniczy US Gov Iowa jest US Gov Wirginia.
- Region pomocniczy US Gov Wirginia jest US Gov Teksas.
- US Gov Teksas "region pomocniczy jest US Gov Arizona.


Zalecamy skonfigurowanie odzyskiwania po awarii (BCDR) w różnych regionach regionalnych, aby korzystać z zasad izolacji i dostępności platformy Azure. W przypadku aplikacji obsługujących wiele aktywnych regionów zalecamy używanie obu regionów w parze regionów, gdy jest to możliwe. Zapewni to optymalną dostępność aplikacji i zminimalizowany czas odzyskiwania w przypadku awarii. 

## <a name="an-example-of-paired-regions"></a>Przykład par regionów
Na rysunku 2 poniżej przedstawiono hipotetyczną aplikację, która używa pary regionalnej do odzyskiwania po awarii. Zielony numer wyróżnia działania obejmujące wiele regionów z trzech usług platformy Azure (Azure COMPUTE, Storage i Database) oraz sposób ich konfiguracji w różnych regionach. Unikatowe korzyści wynikające z wdrożenia w różnych regionach są wyróżniane przez pomarańczowe liczby.

![Omówienie zalet par regionów](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Rysunek 2 — hipotetyczna para regionalna platformy Azure

## <a name="cross-region-activities"></a>Działania obejmujące wiele regionów
Jak określono na rysunku 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure COMPUTE (IaaS)** — w celu zapewnienia dostępności zasobów w innym regionie w trakcie awarii należy zainicjować obsługę administracyjną dodatkowych zasobów obliczeniowych. Aby uzyskać więcej informacji, zobacz [Wskazówki techniczne dotyczące odporności na platformie Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md).

![Storage](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** — Jeśli używasz dysków zarządzanych, Dowiedz się więcej o [kopiach zapasowych między regionami](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) i Azure Backup oraz [replikowanie maszyn wirtualnych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) z jednego regionu do innego za pomocą Azure Site Recovery. Jeśli używasz kont magazynu, to magazyn Geograficznie nadmiarowy (GRS) jest konfigurowany domyślnie podczas tworzenia konta usługi Azure Storage. W przypadku GRS dane są automatycznie replikowane trzy razy w regionie podstawowym, a trzy razy w sparowanym regionie. Aby uzyskać więcej informacji, zobacz [Opcje nadmiarowości usługi Azure Storage](storage/common/storage-redundancy.md).

![usługi Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** — z Azure SQL Database replikacją geograficzną można skonfigurować asynchroniczne replikację transakcji do dowolnego regionu na świecie; Zalecamy jednak wdrożenie tych zasobów w sparowanym regionie w przypadku większości scenariuszy odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [replikację geograficzną w Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Menedżer zasobów](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** -Menedżer zasobów z założenia logicznej izolacji składników w różnych regionach. Oznacza to, że logiczne błędy w jednym regionie są mniej podobne do innych.

## <a name="benefits-of-paired-regions"></a>Zalety par regionów
Jak określono na rysunku 2.  

![izolacja](./media/best-practices-availability-paired-regions/5Orange.png)
**izolacji fizycznej** — Jeśli to możliwe, platforma Azure preferuje co najmniej 300 kilometrów rozdzielenia między centrami danych w parze regionalnym, chociaż nie jest to praktyczne ani możliwe w przypadku wszystkich lokalizacje geograficzne. Fizyczne rozdzielenie centrów danych zmniejsza prawdopodobieństwo wystąpienia klęsk żywiołowych, awarii cywilnej, przerwy w zasilaniu lub sieci fizycznej jednocześnie mających wpływ na oba regiony jednocześnie. Izolacja podlega ograniczeniom w lokalizacji geograficznej (rozmiar geografii, dostępność infrastruktury mocy/sieci, regulacje itp.).  

![replikacja](./media/best-practices-availability-paired-regions/6Orange.png)
**replikacji** — niektóre usługi, takie jak magazyn Geograficznie nadmiarowy, zapewniają automatyczną replikację do sparowanego regionu.

![](./media/best-practices-availability-paired-regions/7Orange.png)
odzyskiwania **regionu odzyskiwania** — w przypadku rozległej awarii odzyskiwanie jednego regionu jest ustalane z uwzględnieniem każdej pary. Aplikacje wdrożone w ramach sparowanych regionów mają gwarancję, że jeden z regionów zostanie odzyskany z priorytetem. Jeśli aplikacja jest wdrożona w różnych regionach, które nie są sparowane, odzyskiwanie może zostać opóźnione — w najgorszym przypadku wybrane regiony mogą być ostatnimi do odzyskania.

aktualizacje ![](./media/best-practices-availability-paired-regions/8Orange.png)
**aktualizacje sekwencyjne** — planowane aktualizacje systemu platformy Azure są wdrożone w sparowanych regionach sekwencyjnie (nie w tym samym czasie), aby zminimalizować przestoje, wpływ błędów i błędy logiczne w rzadkich przypadkach nieprawidłowej aktualizacji.

![dane](./media/best-practices-availability-paired-regions/9Orange.png)
miejsce **zamieszkania** — region znajduje się w tej samej lokalizacji geograficznej, w której znajduje się para (z wyjątkiem Brazylii Południowej) w celu spełnienia wymagań dotyczących miejsca zamieszkania w celach podatkowych i prawnych.
