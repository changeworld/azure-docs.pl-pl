---
title: Usługa Azure SQL Database — rdzeń wirtualny | Dokumentacja firmy Microsoft
description: Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie skalować zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 10/12/2018
ms.openlocfilehash: a0d8e225718361c096b914245d73064edb1715c4
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166359"
---
# <a name="vcore-service-tiers-azure-hybrid-use-benefit-and-migration"></a>Rdzeń wirtualny warstwy usługi, korzyści z używania hybrydowej platformy Azure i migracji

Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie skalować zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny. Umożliwia również można wybrać generacji sprzętu:

- 4. generacji — maksymalnie 24 logiczne procesory CPU oparte na Intel E5-2673 v3 (Haswell) 2,4 GHz procesorów, pamięci rdzeń wirtualny = 1 PP (rdzeni fizycznych), 7 GB na rdzeń procesora, dołączonych dysków SSD
- 5. generacji — maksymalnie 80 logiczne procesory CPU oparte na Intel E5-2673 v4 (broadwell z zegarem) 2,3 GHz, — rdzeń wirtualny = LP 1 (funkcja hyper wątek), 5.5. GB na rdzeń procesora, szybkie eNVM dysków SSD

model rdzenia wirtualnego pozwala również na używanie [hybrydowej platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) uzyskanie oszczędności kosztów.

> [!NOTE]
> Aby uzyskać informacji na temat warstwy usług oparte na jednostkach DTU, zobacz [warstwy usług oparte na jednostkach DTU](sql-database-service-tiers-dtu.md). Aby dowiedzieć się, jak rozróżnianie warstwy usług oparte na jednostkach DTU i warstwy usług oparte na rdzeniach wirtualnych, zobacz [usługi Azure SQL Database, zakup modeli](sql-database-service-tiers.md).

## <a name="service-tier-characteristics"></a>Właściwości warstwy usług

Model rdzenia wirtualnego zapewnia dwie warstwy usług ogólnego przeznaczenia i krytyczne dla działania firmy. Warstwy usługi są zróżnicowane według szeroką gamę rozmiarów wystąpień obliczeniowych, projekt wysokiej dostępności, odporności izolacji, typów pamięci masowej i zakres operacji We/Wy. Klienta należy skonfigurować osobno wymagane okres przechowywania i przechowywania kopii zapasowych. Musisz oddzielnie skonfigurować wymagane okres przechowywania i przechowywania kopii zapasowych. W witrynie Azure portal przejdź do serwera (bez bazy danych) > zarządzanych kopii zapasowych > Konfiguruj zasady > w czasie przywracania konfiguracji punktu > 7 – 35 dni.

Poniższa tabela pomoże Ci zrozumieć różnice między tymi dwoma warstwami:

||**Ogólnego przeznaczenia**|**Krytyczne dla działania**|**Na dużą skalę (wersja zapoznawcza)**|
|---|---|---|---|
|Najlepsze dla|Większości obciążeń biznesowych. Oferty budżetu, aby poznać podstawy zrównoważonych oraz skalowalnych opcji obliczeniowych i magazynu.|Aplikacje biznesowe z wysokimi wymaganiami w zakresie operacji wejścia/wyjścia. Oferuje najwyższą odporność na awarie, korzystając z kilku izolowanych replik.|Większości obciążeń biznesowych za pomocą wysoce skalowalny magazyn i wymagań skali odczytu|
|Wystąpienia obliczeniowe|4. generacji: — rdzeń wirtualny 1-24<br/>5. generacji: — rdzeń wirtualny 1 do 80|4. generacji: — rdzeń wirtualny 1-24<br/>5. generacji: — rdzeń wirtualny 1 do 80|4. generacji: — rdzeń wirtualny 1-24<br/>5. generacji: — rdzeń wirtualny 1 do 80|
|Memory (Pamięć)|4. generacji: 7 GB na rdzeń<br>5. generacji: 5.5 GB na rdzeń | 4. generacji: 7 GB na rdzeń<br>5. generacji: 5.5 GB na rdzeń |4. generacji: 7 GB na rdzeń<br>5. generacji: 5.5 GB na rdzeń|
|Magazyn|[Usługa Premium storage zdalnego](../virtual-machines/windows/premium-storage.md),<br/>Pojedyncza baza danych: 5 GB – 4 TB<br/>Wystąpienie zarządzane: 32 GB – 8 TB |Lokalny magazyn SSD<br/>Pojedyncza baza danych: 5 GB – 4 TB<br/>Wystąpienie zarządzane: 32 GB – 4 TB |Elastyczne, automatyczne zwiększanie magazynu zgodnie z potrzebami. Obsługuje maksymalnie 100 TB pamięci masowej i nie tylko. Lokalny magazyn SSD w pamięci podręcznej puli bufora lokalnych i lokalne przechowywanie danych. Usługa Azure storage zdalnego końcowego długoterminowego przechowywania danych. |
|Przepustowość operacji We/Wy (w przybliżeniu)|Pojedyncza baza danych: 500 operacji We/Wy na rdzeniach wirtualnych za pomocą 7000 maksymalna liczba IOPS</br>Wystąpienia zarządzanego: Zależy [rozmiar pliku](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 operacji We/Wy na rdzeń z 200 000 maksymalna liczba IOPS|TBD|
|Dostępność|1 repliki, bez skalowania odczytu|3 repliki, 1 [skali odczytu replik](sql-database-read-scale-out.md),<br/>Strefa nadmiarowe wysokiej dostępności|?|
|Tworzenie kopii zapasowych|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dni (domyślnie co 7 dni)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dni (domyślnie co 7 dni)|na podstawie migawki kopii zapasowej w magazynie zdalnym platformy Azure i przywracanie na użytek migawek Szybkie odzyskiwanie. Kopie zapasowe są natychmiastowe i nie wpływać na wydajność operacji We/Wy, mocy obliczeniowej. Operacje przywracania są bardzo szybkie i nie rozmiar operacji danych (trwa minuty, a nie godziny lub dni).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||

- Aby uzyskać więcej informacji, zobacz [limity zasobów — rdzeń wirtualny w pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów — rdzeń wirtualny w wystąpieniu zarządzanym](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Aby uzyskać więcej informacji na temat warstw usługi ogólnego przeznaczenia i krytyczne dla działania firmy, zobacz [warstwy usług ogólnego przeznaczenia i krytyczne dla działania firmy](sql-database-service-tiers-general-purpose-business-critical.md).
- Szczegółowe informacje na temat warstwy usług na dużą skalę w modelu zakupu opartego na rdzeniach wirtualnych, [warstwy usługi w Hiperskali](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Jeśli potrzebujesz mniejszej niż jeden rdzeń wirtualny mocy obliczeniowej, przy użyciu modelu zakupu opartego na jednostkach DTU.

Zobacz [— często zadawane pytania dla bazy danych SQL](sql-database-faq.md) odpowiedzi na często zadawane pytania.

## <a name="azure-hybrid-use-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Oparty na rdzeniach wirtualnych model zakupu mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych na temat korzystania z bazy danych SQL [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Ta korzyść platformy Azure pozwala na używanie licencji programu SQL Server w środowisku lokalnym można zapisać do 30% w usłudze Azure SQL Database przy użyciu lokalnych licencji programu SQL Server z pakietem Software Assurance.

![cennik](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migracja z modelu jednostek DTU do modelu rdzenia wirtualnego

### <a name="migration-of-a-database"></a>Migracja bazy danych

Migrowanie bazy danych z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeniach wirtualnych jest podobny do uaktualnienia lub zmiany na starszą wersję między Standard i Premium baz danych w modelu zakupu opartego na jednostkach DTU.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migracja baz danych za pomocą łącza replikacji geograficznej

Migrowanie do modelu opartego na jednostkach DTU do modelu opartego na rdzeniach wirtualnych jest podobny do uaktualnienia lub zmiany na starszą wersję relacje replikacji geograficznej między bazami danych w warstwach standardowa i Premium. Nie jest wymagane, czy zakończenie replikacji geograficznej, ale użytkownik musi być zgodna z zasadami sekwencjonowania. Podczas uaktualniania, należy najpierw uaktualnić pomocnicze bazy danych, a następnie Uaktualnij podstawowy. Przed obniżeniem, odwrócić kolejność: należy najpierw obniżyć podstawowej bazy danych, a następnie obniżenia poziomu pomocniczej.

Korzystając z replikacją geograficzną między dwie pule elastyczne, zaleca się wyznaczyć jedną pulę jako podstawowy, a druga — jako pomocnicza. W takim przypadku Migrowanie pul elastycznych, należy użyć te same wskazówki.  Jednak jest technicznie jest to możliwe, że pula elastyczna zawiera podstawowych i pomocniczych baz danych. W tym przypadku prawidłowo migracji możesz należy traktować puli z lepszego wykorzystania jako "primary" i postępuj zgodnie z zasadami sekwencjonowania.  

W poniższej tabeli przedstawiono wskazówki dotyczące scenariuszy migracji:

|Bieżąca warstwa usługi|Docelowej warstwy usług|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa (Standard)|Zastosowania ogólne|Boczna|Można migrować w dowolnej kolejności, ale musisz upewnić się, ustalanie rozmiaru odpowiedniego — rdzeń wirtualny *|
|Premium|Krytyczne dla działania firmy|Boczna|Można migrować w dowolnej kolejności, ale musisz upewnić się, ustalanie rozmiaru odpowiedniego — rdzeń wirtualny *|
|Standardowa (Standard)|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
|Krytyczne dla działania firmy|Standardowa (Standard)|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Premium|Zastosowania ogólne|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Premium|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
|Krytyczne dla działania firmy|Zastosowania ogólne|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
||||

\* Każdy 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 rdzeń wirtualny, a każdy 125 jednostek DTU w warstwie Premium co najmniej 1 rdzeń wirtualny

### <a name="migration-of-failover-groups"></a>Migracja grupy trybu failover

Migracja grup trybu failover z wieloma bazami danych wymaga migracji poszczególnych baz danych podstawowych i pomocniczych. Podczas tego procesu rozważenia tych samych czynników i sekwencjonowania zasady są stosowane. Po przekonwertowaniu baz danych do modelu opartego na rdzeniach wirtualnych, grupy trybu failover będzie obowiązywać z tymi samymi ustawieniami zasad.

### <a name="creation-of-a-geo-replication-secondary"></a>Tworzenie pomocniczej replikacji geograficznej

Można utworzyć tylko przy użyciu tej samej warstwy usługi jako podstawowy pomocniczej geograficznej. Baza danych o szybkości generowania rekordów dziennika wysoka zalecane jest, czy pomocniczy został utworzony przy użyciu tego samego rozmiaru obliczeń jako podstawowy. Jeśli tworzysz pomocniczej geograficznej w puli elastycznej dla pojedynczej podstawowej bazy danych, zaleca się że pula ma `maxVCore` ustawienie odpowiada rozmiarowi obliczeń podstawowej bazy danych. Jeśli tworzysz pomocniczej geograficznej w puli elastycznej dla podstawowego w innej puli elastycznej, zalecane pule mają taką samą `maxVCore` ustawienia

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Przy użyciu kopii bazy danych do przekonwertowania bazy danych oparty na jednostkach DTU na bazę danych oparty na rdzeniach wirtualnych

Można skopiować dowolną bazę danych o rozmiarze mocą obliczeniową opartą na jednostkach DTU do bazy danych o rozmiarze oparty na rdzeniach wirtualnych obliczeń bez ograniczeń lub specjalne sekwencjonowania, tak długo, jak rozmiar obliczeń docelowej obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopiowanie bazy danych tworzy migawkę danych od godziny rozpoczęcia operacji kopiowania i nie wykonuje synchronizację danych między źródłowym a docelowym.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępny dla pojedynczej bazy danych, zobacz [limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL Database dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępne dla pul elastycznych, zobacz [bazy danych SQL Database oparty na rdzeniach wirtualnych zasobów limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
