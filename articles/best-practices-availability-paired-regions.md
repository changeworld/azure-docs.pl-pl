---
title: 'Biznesowe ciągłości i odzyskiwanie po awarii (BCDR): łączyć regiony platformy Azure | Dokumentacja firmy Microsoft'
description: Dowiedz się więcej o Azure regionalnych parowania, aby upewnić się, że aplikacje są odporne podczas awarii centrum danych.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: multiple
ms.topic: article
ms.date: 05/09/2018
ms.author: raynew
ms.openlocfilehash: e2c288af881fa925c1680efdb0f86deec60b7510
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34302682"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Biznesowe ciągłości i odzyskiwanie po awarii (BCDR): łączyć regiony platformy Azure

## <a name="what-are-paired-regions"></a>Co to są skojarzone regionów?

Azure działa w wielu lokalizacji geograficznych na całym świecie. Geograficzne Azure jest zdefiniowany obszar świata, który zawiera co najmniej jeden Region platformy Azure. Region platformy Azure jest obszar wewnątrz obiektu geograficznego, zawierający co najmniej jeden centrów danych.

Każdy region platformy Azure jest skojarzone z innego regionu w tej samej lokalizacji geograficznej, razem wprowadzeniem regionalnych pary. Wyjątek stanowi Brazylia Południowa, który jest łączyć się z obszarem poza jego lokalizacji geograficznej.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Rysunek 1 — Azure pary regionalne

| Lokalizacja geograficzna | Sparowane regiony |  |
|:--- |:--- |:--- |
| Azja |Azja Wschodnia |Azja Południowo-Wschodnia |
| Australia |Australia Wschodnia |Australia Południowo-Wschodnia |
| Australia |Australia Środkowa |Australia Środkowa (2) |
| Brazylia |Brazylia Południowa (2) |Środkowo-południowe stany USA |
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
| Rząd USA |Iowa wersji dla instytucji rządowych Stanów Zjednoczonych (3) |Administracja USA — Wirginia |
| Rząd USA |Virginia wersji dla instytucji rządowych Stanów Zjednoczonych (4) |Administracja USA — Teksas |

Tabela 1 - mapowania par regionalnych Azure

- (1) Indie Zachodnie jest inny, ponieważ jest on łączyć się z innego regionu tylko w jednym kierunku. Indie Południowe jest region pomocniczy Indie Zachodnie, lecz region pomocniczy Indie Południowe Indie środkowe.
- (2) Brazylia Południowa jest unikatowa, ponieważ jest łączyć się z obszarem poza jego własnej lokalizacji geograficznej. Brazylia Południowa region pomocniczy jest południowo-środkowe stany, ale południowo-środkowe stany w regionie pomocniczym nie jest Brazylia Południowa.
- (3) region pomocniczy Iowa wersji dla instytucji rządowych Stanów Zjednoczonych jest Virginia nam wersji dla instytucji rządowych, ale region pomocniczy Virginia nam wersji dla instytucji rządowych nie Iowa nam wersji dla instytucji rządowych.
- (4) region pomocniczy Virginia wersji dla instytucji rządowych Stanów Zjednoczonych jest Texas nam wersji dla instytucji rządowych, ale region pomocniczy Texas nam wersji dla instytucji rządowych nie Virginia nam wersji dla instytucji rządowych.


Firma Microsoft zaleca replikować obciążenia między regionalnych pary do korzystania z platformy Azure zasad izolacji i dostępności. Na przykład aktualizacje planowane systemu Azure są wdrażane kolejno (nie w tym samym czasie) w parach regionach. Oznacza to, że nawet w rzadkich błędny aktualizacji obu regionów nie zostaną zmienione jednocześnie. Ponadto na wypadek awarii szerokie priorytetu jest Odzyskiwanie co najmniej jeden region poza każdej pary.

## <a name="an-example-of-paired-regions"></a>Przykład sparowanego regionów
Na rysunku 2 poniżej przedstawiono hipotetyczny aplikacji, która używa regionalnych pary odzyskiwania po awarii. Zielony numery zaznacz działania region między trzy usług platformy Azure (Azure obliczeniowe magazynu i bazy danych) i jak są skonfigurowane do replikacji w regionach. Unikatowy zalet wdrażania w różnych regionach sparowanego są wyróżnione pomarańczowy liczbami.

![Omówienie zalet sparowanego regionu](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Rysunek 2 — hipotetyczny Azure pary regionalne

## <a name="cross-region-activities"></a>Region między działaniami
Określone na rysunku 2.

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **(PaaS) rozwiązań usługi obliczenia Azure** — należy udostępnić zasoby obliczeniowe z wyprzedzeniem, aby upewnić się, zasoby są dostępne w innym regionie podczas awarii. Aby uzyskać więcej informacji, zobacz [wskazówki techniczne Azure odporności](resiliency/resiliency-technical-guidance.md).

![Magazyn](./media/best-practices-availability-paired-regions/2Green.png) **usługi Azure Storage** — magazyn geograficznie nadmiarowy (GRS) jest domyślnie skonfigurowany podczas tworzenia konta usługi Azure Storage. W wypadku magazynu GRS dane są automatycznie replikowane trzy razy w regionie podstawowym i trzy razy w regionie pary. Aby uzyskać więcej informacji, zobacz [opcje nadmiarowość magazynu Azure](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **baz danych SQL Azure** — z usługi Azure SQL standardowa replikacja geograficzna, możesz konfigurować replikację asynchroniczną transakcji sparowanego regionu. Premium replikacja geograficzna można skonfigurować replikację na dowolny region w świecie; jednak zaleca się wdrażanie tych zasobów w regionie sparowanego większości scenariuszy odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [— replikacja geograficzna w bazie danych SQL Azure](sql-database/sql-database-geo-replication-overview.md).

![Menedżer zasobów](./media/best-practices-availability-paired-regions/4Green.png) **usługi Azure Resource Manager** -Resource Manager zapewnia z założenia izolacji logicznej składników usługi zarządzania w regionach. Oznacza to, że błędów logicznych w jeden region jest mniej prawdopodobne wpłynąć na inny.

## <a name="benefits-of-paired-regions"></a>Korzyści sparowanego regionów
Określone na rysunku 2.  

![Izolacja](./media/best-practices-availability-paired-regions/5Orange.png)
**fizycznych izolacji** — Jeśli to możliwe, Azure preferuje co najmniej 300 mil rozdzielenie centrów danych w parze regionalnych, chociaż nie jest praktyczne lub możliwe w wszystkich regionów geograficznych. Rozdzielenie fizycznego centrum danych zmniejsza prawdopodobieństwo klęski żywiołowej, unrest cywilnego, awarie zasilania lub awarie sieci fizycznej wpływających na obu regionów jednocześnie. Izolacja podlega ograniczeń w ramach geograficznych (rozmiar obiektu geograficznego, dostępność infrastruktury zasilania i sieci, wykonawcze itp.).  

![Replikacja](./media/best-practices-availability-paired-regions/6Orange.png)
**replikacji dostarczane przez platformę** — automatyczne replikacji do regionu sparowanego Podaj niektórych usług, takich jak magazyn geograficznie nadmiarowy.

![Odzyskiwanie](./media/best-practices-availability-paired-regions/7Orange.png)
**kolejności odzyskiwania Region** — w przypadku awarii szerokie, odzyskiwania jeden region jest priorytety poza każdej pary. Aplikacje, które są wdrażane w regionach sparowanego dotrą do ma regionów odzyskane o priorytecie. Jeśli aplikacja jest wdrażana w regionach, które nie są skojarzone, odzyskiwania mogą być opóźnione — w najgorszym przypadku wybranych regionów może być ostatnie dwa mają zostać odzyskane.

![Aktualizacje](./media/best-practices-availability-paired-regions/8Orange.png)
**kolejnych aktualizacji** — aktualizacje systemu Azure planowane są wprowadzanie do regionów sparowanego sekwencyjnie (nie w tym samym czasie) aby zminimalizować czas przestoju, wpływ usterek i błędów logicznych w rzadkich zły Aktualizacja.

![Dane](./media/best-practices-availability-paired-regions/9Orange.png)
**siedziby danych** — region znajduje się w tej samej lokalizacji geograficznej, jako jego para (z wyjątkiem Brazylia Południowa) w celu spełnienia wymagań siedziby danych do celów właściwość wymuszania podatku i prawa.
