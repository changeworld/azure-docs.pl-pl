---
title: 'Business ciągłości działania i odzyskiwania po awarii (BCDR): regiony sparowane platformy Azure | Dokumentacja firmy Microsoft'
description: Dowiedz się więcej o usłudze Azure parowanie regionalne, aby upewnić się, że aplikacje są odporne na błędy podczas awarii centrum danych.
author: rayne-wiselman
ms.service: multiple
ms.topic: article
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: 13a2b78b50b1b10975a90c1da38810f1a62a6bb5
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436913"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Business ciągłości działania i odzyskiwania po awarii (BCDR): regiony sparowane platformy Azure

## <a name="what-are-paired-regions"></a>Co to są sparowane regiony?

Platforma Azure działa w wielu lokalizacji geograficznych na całym świecie. Lokalizacja geograficzna platformy Azure jest zdefiniowany obszar świata, który zawiera co najmniej jednego regionu platformy Azure. Region platformy Azure jest obszar w lokalizacji geograficznej, zawierający co najmniej jedno centrum danych.

Każdy region platformy Azure jest powiązany z innym regionem w obrębie tego samego obszaru geograficznego razem wprowadzania parę regionalną. Wyjątek stanowi Brazylii Południowej, który jest powiązany z regionem poza jego lokalizacji geograficznej.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Rysunek 1 — pary regionalne platformy Azure

| Lokalizacja geograficzna | Sparowane regiony |  |
|:--- |:--- |:--- |
| Azja |Azja Wschodnia |Azja Południowo-Wschodnia |
| Australia |Australia Wschodnia |Australia Południowo-Wschodnia |
| Australia |Australia Środkowa |Australia Środkowa 2 |
| Brazylia |Brazylia Południowa 2 |Środkowo-południowe stany USA |
| Kanada |Kanada Środkowa |Kanada Wschodnia |
| Chiny |Chiny Północne |Chiny Wschodnie|
| Europa |Europa Północna |Europa Zachodnia |
| Niemcy |Niemcy Środkowe |Niemcy Północno-Wschodnie |
| Indie |Indie Środkowe |Indie Południowe |
| Indie |Indie Zachodnie (1) |Indie Południowe |
| Japonia |Japonia Wschodnia |Japonia Zachodnia |
| Korea |Korea Środkowa |Korea Południowa |
| Ameryka Północna |Wschodnie stany USA |Zachodnie stany USA |
| Ameryka Północna |Wschodnie stany USA 2 |Środkowe stany USA |
| Ameryka Północna |Środkowo-północne stany USA |Środkowo-południowe stany USA |
| Ameryka Północna |Zachodnie stany USA 2 |Środkowo-zachodnie stany USA 
| Zjednoczone Królestwo |Zachodnie Zjednoczone Królestwo |Południowe Zjednoczone Królestwo |
| Departamentu Obrony USA |US DoD — wschodnie stany |US DoD — środkowe stany |
| Rząd USA |Administracja USA — Arizona |Administracja USA — Teksas |
| Rząd USA |Administracja USA — Iowa (3) |Administracja USA — Wirginia |
| Rząd USA |Administracja USA — Wirginia (4) |Administracja USA — Teksas |

Tabela 1 — mapowanie pary regionalne platformy Azure

- (1) Indie Zachodnie jest inny, ponieważ jest powiązany z innym regionem tylko w jednym kierunku. Indie Zachodnie w regionie pomocniczym jest Indie Południowe, ale Indie Południowe w regionie pomocniczym jest Indie środkowe.
- (2) Brazylia Południowa jest unikatowa, ponieważ jest powiązany z regionem poza jego własnej lokalizacji geograficznej. Region pomocniczy w regionie Brazylia Południowa jest południowo-środkowe stany USA, ale południowo-środkowe stany USA w regionie pomocniczym nie jest Brazylia Południowa.
- (3) Administracja USA — Iowa w regionie pomocniczym jest Administracja USA — Wirginia, ale nie jest w regionie pomocniczym Administracja USA — Wirginia Administracja USA — Iowa.
- (4) Administracja USA — Wirginia w regionie pomocniczym jest Administracja USA — Teksas, ale nie jest w regionie pomocniczym Administracja USA — Teksas Administracja USA — Wirginia.


Firma Microsoft zaleca replikować obciążenia w pary regionalne do korzystania z zasad izolacji i dostępności platformy Azure. Na przykład aktualizacje systemu platformy Azure planowane są wdrażane po kolei (nie w tym samym czasie) w sparowanych regionach. Oznacza to, że nawet w przypadku rzadko wadliwe aktualizacji, oba regiony nie zostaną zmienione jednocześnie. Ponadto w mało prawdopodobnym przypadku odzyskiwanie co najmniej jednego regionu z każdej pary ma przydzielany wyższy priorytet.

## <a name="an-example-of-paired-regions"></a>Przykładem sparowane regiony
Na rysunku 2 poniżej przedstawiono hipotetyczny aplikację, która używa pary regionalnej dla odzyskiwania po awarii. Zielony numery zaznacz działań między regionami w trzech usług platformy Azure (Azure compute, storage i bazy danych) i jak są skonfigurowane do replikacji między regionami. Z wyjątkowych zalet wdrażania w sparowanych regionach zostały wyróżnione na pomarańczowy liczb.

![Omówienie zalet sparowanym regionie](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Rysunek 2 — hipotetyczny pary regionalne platformy Azure

## <a name="cross-region-activities"></a>Działania między regionami
Określone na rysunku 2.

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **usługi Azure Compute (PaaS)** — należy aprowizować dodatkowe zasoby obliczeniowe wcześniej, aby zapewnić zasoby są dostępne w innym regionie podczas awarii. Aby uzyskać więcej informacji, zobacz [wskazówek technicznych odporność platformy Azure](resiliency/resiliency-technical-guidance.md).

![Magazyn](./media/best-practices-availability-paired-regions/2Green.png) **usługi Azure Storage** -magazyn geograficznie nadmiarowy (GRS) jest domyślnie skonfigurowana podczas tworzenia konta usługi Azure Storage. W przypadku magazynu GRS dane są automatycznie replikowane trzy razy w regionie podstawowym i trzy razy w sparowanym regionie. Aby uzyskać więcej informacji, zobacz [Opcje nadmiarowości magazynu Azure](storage/common/storage-redundancy.md).

![Usługi Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **baz danych SQL Azure** — za pomocą usługi Azure SQL standardowej replikacji geograficznej, można skonfigurować Replikacja asynchroniczna transakcje w sparowanym regionie. Dzięki premium replikacji geograficznej można skonfigurować replikacji w dowolnym regionie na świecie; jednak zalecamy wdrażania tych zasobów w sparowanym regionie, w przypadku większości scenariuszy odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [replikacja geograficzna w usłudze Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Menedżer zasobów](./media/best-practices-availability-paired-regions/4Green.png) **usługi Azure Resource Manager** — usługa Resource Manager zapewnia natury izolacji logicznej składników zarządzania usługi między regionami. Oznacza to, że błędy logiczne w jednym regionie są mniej prawdopodobne wpłynąć na inny.

## <a name="benefits-of-paired-regions"></a>Zalety sparowane regiony
Określone na rysunku 2.  

![Izolacja](./media/best-practices-availability-paired-regions/5Orange.png)
**fizyczne odizolowanie** — gdy to możliwe, Azure preferuje co najmniej 300 mil separacji między centrami danych w parę regionalną, chociaż nie jest to praktyczne, czy możliwa wszystkich obszarach geograficznych. Rozdzielenie fizycznego centrum danych zmniejsza prawdopodobieństwo klęski żywiołowe, społeczne, przerwy w zasilaniu lub awarie sieci fizycznych będą wpływać na obydwa regiony na raz. Izolacja jest podlegających ograniczeniom w lokalizacji geograficznej (rozmiar lokalizacji geograficznej, dostępność infrastruktury zasilania i sieci, regulacjami itp.).  

![Replikacja](./media/best-practices-availability-paired-regions/6Orange.png)
**replikacji dostarczone przez platformę** -pewnych usług, takich jak magazyn geograficznie nadmiarowy oferowane automatyczną replikację do regionu sparowanego.

![Odzyskiwanie](./media/best-practices-availability-paired-regions/7Orange.png)
**Region ściągnięciu** — w przypadku awarii szerokie, odzyskiwania jeden region jest podzielony na priorytety z każdej pary. Aplikacje, które są wdrażane w sparowanych regionach są musi mieć jeden z regionów odzyskać z priorytetem. Jeśli aplikacja jest wdrażana w różnych regionach, które nie są skojarzone, odzyskiwania mogą być opóźnione — w najgorszym przypadku wybranych regionów może być ostatnie dwa do odzyskania.

![Aktualizacje](./media/best-practices-availability-paired-regions/8Orange.png)
**kolejnych aktualizacji** — Azure planowane aktualizacje systemu są realizowane w sparowanych regionach po kolei (nie w tym samym czasie) aby zminimalizować przestoje, efekt usterek i błędów logicznych w rzadkich zły Aktualizacja.

![Dane](./media/best-practices-availability-paired-regions/9Orange.png)
**rezydencji danych** — region znajduje się w tej samej lokalizacji geograficznej co para (z wyjątkiem Brazylii Południowej), aby spełnić wymagania dotyczące rezydencji danych na potrzeby podatku i prawa wymuszania jurysdykcji.
