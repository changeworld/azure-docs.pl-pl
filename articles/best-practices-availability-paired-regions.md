---
title: Zapewnienie ciągłości działania & odzyskiwania po awarii przy użyciu sparowanych regionów platformy Azure
description: Zapewnianie odporności aplikacji przy użyciu parowania regionalnego platformy Azure
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248257"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR): regiony sparowane platformy Azure

## <a name="what-are-paired-regions"></a>Co to są sparowane regiony?

Region platformy Azure składa się z zestawu centrów danych wdrożonych w obwodzie zdefiniowanym przez opóźnienie i połączonych za pośrednictwem dedykowanej sieci o małych opóźnieniach.  Dzięki temu usługi platformy Azure w regionie platformy Azure oferują najlepszą możliwą wydajność i zabezpieczenia.  

Geografia platformy Azure definiuje obszar świata zawierający co najmniej jeden region platformy Azure. Obszary geograficzne definiują dyskretny rynek, zazwyczaj zawierający dwa lub więcej regionów, które zachowują granice rezydencji danych i zgodności.  Więcej informacji na temat globalnej infrastruktury platformy Azure znajdziesz [tutaj](https://azure.microsoft.com/global-infrastructure/regions/)

Para regionalna składa się z dwóch regionów w tej samej lokalizacji geograficznej. Platforma Azure serializuje aktualizacje platformy (planowaną konserwację) między parami regionalnymi, zapewniając, że tylko jeden region w każdej parze aktualizuje się naraz. Jeśli awaria dotyczy wielu regionów, co najmniej jeden region w każdej parze będzie priorytetem dla odzyskiwania.

![AzureGeography (AzureGeography)](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Niektóre usługi platformy Azure dodatkowo wykorzystują sparowane regiony, aby zapewnić ciągłość działania i chronić przed utratą danych.  Platforma Azure udostępnia kilka [rozwiązań magazynu,](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) które wykorzystują sparowane regiony, aby zapewnić dostępność danych. Na przykład [usługa Azure Geo-redundant Storage](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) automatycznie replikuje dane do regionu pomocniczego, zapewniając, że dane są trwałe nawet w przypadku, gdy region podstawowy nie jest możliwe do odzyskania. 

Należy zauważyć, że nie wszystkie usługi platformy Azure automatycznie replikują dane, ani wszystkie usługi platformy Azure automatycznie awaryjnie z regionu nie powiodło się do jego pary.  W takich przypadkach odzyskiwanie i replikacja musi być skonfigurowana przez klienta.

## <a name="can-i-select-my-regional-pairs"></a>Czy mogę wybrać moje pary regionalne?

Nie. Niektóre usługi platformy Azure opierają się na parach regionalnych, takich jak [nadmiarowy magazyn](./storage/common/storage-redundancy.md)platformy Azure. Te usługi nie umożliwiają tworzenia nowych par regionalnych.  Podobnie, ponieważ platforma Azure kontroluje planowane priorytety konserwacji i odzyskiwania dla par regionalnych, nie można zdefiniować własnych par regionalnych, aby skorzystać z tych usług. Można jednak utworzyć własne rozwiązanie do odzyskiwania po awarii, tworząc usługi w dowolnej liczbie regionów i wykorzystując usługi platformy Azure do ich parowania. 

Na przykład można użyć usług platformy Azure, takich jak [AzCopy](./storage/common/storage-use-azcopy-v10.md) zaplanować kopie zapasowe danych do konta magazynu w innym regionie.  Korzystając z [usługi Azure DNS i usługi Azure Traffic Manager,](./networking/disaster-recovery-dns-traffic-manager.md)klienci mogą zaprojektować odporną architekturę dla swoich aplikacji, która przetrwa utratę regionu podstawowego.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Czy ograniczam się do korzystania z usług w moich regionalnych parach?

Nie. Podczas gdy dana usługa platformy Azure może polegać na parze regionalnej, możesz hostować inne usługi w dowolnym regionie, który spełnia Twoje potrzeby biznesowe.  Rozwiązanie magazynu usługi Azure GRS może sparować dane w Kanadzie Środkowej z elementem równorzędnym w Kanadzie na Wschodzie podczas korzystania z zasobów obliczeniowych znajdujących się we wschodnich stanach USA.  

## <a name="must-i-use-azure-regional-pairs"></a>Czy muszę używać par regionalnych platformy Azure?

Nie. Klienci mogą korzystać z usług platformy Azure, aby zaprojektować odporną usługę bez polegania na regionalnych parach platformy Azure.  Zaleca się jednak skonfigurowanie odzyskiwania po awarii (BCDR) ciągłości działania w różnych parach regionalnych, aby korzystać z [izolacji](./security/fundamentals/isolation-choices.md) i poprawić [dostępność.](./availability-zones/az-overview.md) W przypadku aplikacji obsługujących wiele aktywnych regionów zalecamy używanie obu regionów z pary regionalnej, gdy jest to możliwe. Zapewnia to optymalną dostępność aplikacji i zminimalizowany czas odzyskiwania w przypadku awarii. Jeśli to możliwe, zaprojektuj aplikację, aby [zapewnić maksymalną odporność](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) i łatwość [odzyskiwania po awarii.](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Pary regionalne platformy Azure

| Lokalizacja geograficzna | Para regionalna A | Para regionalna B  |
|:--- |:--- |:--- |
| Azja-Pacyfik |Azja Wschodnia (Hongkong) | Azja Południowo-Wschodnia (Singapur) |
| Australia |Australia Wschodnia |Australia Południowo-Wschodnia |
| Australia |Australia Środkowa |Australia Środkowa 2 |
| Brazylia |Brazylia Południowa |Południowo-środkowe stany USA |
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
| Ameryka Północna |Północno-środkowe stany USA |Południowo-środkowe stany USA |
| Ameryka Północna |Zachodnie stany USA 2 |Zachodnio-środkowe stany USA |
| Norwegia | Norwegia Wschodnia | Norwegia Zachodnia |
| Republika Południowej Afryki | Republika Południowej Afryki Północ |Republika Południowej Afryki Zachód |
| Szwajcaria | Szwajcaria Północna |Szwajcaria Zachód |
| Zjednoczone Królestwo |Zachodnie Zjednoczone Królestwo |Południowe Zjednoczone Królestwo |
| Zjednoczone Emiraty Arabskie | Zjednoczone Emiraty Północne | Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emirat
| Departament Obrony USA |US DoD (region wschodni) |US DoD (region środkowy) |
| US Government |US Gov Arizona |US Gov Teksas |
| US Government |US Gov Iowa |US Gov Wirginia |
| US Government |US Gov Wirginia |US Gov Teksas |

> [!Important]
> - Indie Zachodnie są sparowane tylko w jednym kierunku. Drugorzędnym regionem Indii Zachodnich są Indie Południowe, ale drugorzędnym regionem Południowych Indii są Indie Środkowe.
> - Brazylia Południowa jest wyjątkowa, ponieważ jest połączona z regionem poza jego geografią. Drugim regionem Brazylii Południowej jest południowo-środkowe stany USA. Południowo-środkowym regionem USA nie jest Brazylia Południowa.


## <a name="an-example-of-paired-regions"></a>Przykład sparowanych regionów
Poniższy obraz ilustruje hipotetyczną aplikację, która używa pary regionalnej do odzyskiwania po awarii. Liczby zielone podkreślają działania między regionami trzech usług platformy Azure (obliczenia platformy Azure, magazynu i bazy danych) oraz sposób konfigurowania ich do replikacji w różnych regionach. Unikatowe korzyści wynikające z wdrażania w sparowanych regionach są wyróżnione pomarańczowymi liczbami.

![Przegląd korzyści z sparowanego regionu](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Rysunek 2 — hipotetyczna para regionalna platformy Azure

## <a name="cross-region-activities"></a>Działania międzyregionacje
Jak określono na rysunku 2.

1. **Azure Compute (IaaS)** — należy aprowizować dodatkowe zasoby obliczeniowe z wyprzedzeniem, aby upewnić się, że zasoby są dostępne w innym regionie podczas awarii. Aby uzyskać więcej informacji, zobacz [wskazówki techniczne dotyczące odporności platformy Azure.](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md) 

2. **Usługa Azure Storage** — jeśli używasz dysków zarządzanych, dowiedz się więcej o [kopiach zapasowych między regionami](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) za pomocą usługi Azure Backup i [replikowaniu maszyn wirtualnych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) z jednego regionu do drugiego za pomocą usługi Azure Site Recovery. Jeśli używasz kont magazynu, magazyn geograficznie nadmiarowy (GRS) jest domyślnie skonfigurowany podczas tworzenia konta usługi Azure Storage. W programie GRS dane są automatycznie replikowane trzy razy w regionie podstawowym i trzy razy w regionie sparowanym. Aby uzyskać więcej informacji, zobacz [Opcje nadmiarowości usługi Azure Storage](storage/common/storage-redundancy.md).

3. **Usługa Azure SQL Database** — dzięki replikacji geograficznej bazy danych SQL azure można skonfigurować replikację asynchroniczna transakcji do dowolnego regionu na świecie; Jednak zaleca się wdrożenie tych zasobów w regionie sparowanym dla większości scenariuszy odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [Replikacja geograficzna w bazie danych SQL Azure](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** — usługa Resource Manager z założenia zapewnia logiczną izolację składników w różnych regionach. Oznacza to, że błędy logiczne w jednym regionie są mniej prawdopodobne, aby wpłynąć na inny.

## <a name="benefits-of-paired-regions"></a>Korzyści z regionów sparowanych

5. **Izolacja fizyczna** — jeśli to możliwe, platforma Azure preferuje co najmniej 300 mil separacji między centrami danych w parze regionalnej, chociaż nie jest to praktyczne ani możliwe we wszystkich regionach geograficznych. Fizyczne oddzielenie centrum danych zmniejsza prawdopodobieństwo wystąpienia klęsk żywiołowych, niepokojów społecznych, przerw w zasilaniu lub fizycznych awarii sieci, które wpływają na oba regiony jednocześnie. Izolacja podlega ograniczeniom w obrębie geografii (rozmiar geografii, dostępność infrastruktury zasilania/sieci, przepisy itp.).  

6. **Replikacja dostarczona przez platformę** — niektóre usługi, takie jak Magazyn geograficznie nadmiarowy, zapewniają automatyczną replikację do sparowanego regionu.

7. **Kolejność odzyskiwania regionu** — w przypadku wystąpienia dużej awarii odzyskiwanie jednego regionu jest traktowane priorytetowo z każdej pary. Aplikacje wdrożone w ramach sparowanych regionów mają gwarancję, że jeden z regionów zostanie odzyskany priorytetowo. Jeśli aplikacja jest wdrażana w regionach, które nie są sparowane, odzyskiwanie może być opóźnione — w najgorszym przypadku wybrane regiony mogą być dwa ostatnie do odzyskania.

8. **Aktualizacje sekwencyjne** — planowane aktualizacje systemu platformy Azure są wprowadzane do sparowanych regionów sekwencyjnie (nie w tym samym czasie), aby zminimalizować przestoje, efekt błędów i błędy logiczne w rzadkim przypadku złej aktualizacji.

9. **Miejsce zamieszkania danych** — region znajduje się w tej samej lokalizacji geograficznej co jego para (z wyjątkiem Brazylii Południowej), aby spełnić wymagania dotyczące rezydencji danych dla celów podatkowych i jurysdykcji organów ścigania.
