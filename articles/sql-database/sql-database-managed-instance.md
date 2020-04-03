---
title: Omówienie wystąpienia zarządzanego SQL
description: W tym artykule opisano wystąpienie zarządzane usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 06242af6cb00e3adebbc80da722898fb8e348e36
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585370"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Co to jest wystąpienie zarządzane usługi Azure SQL Database?

Wystąpienie zarządzane to nowa opcja wdrażania usługi Azure SQL Database, zapewniająca niemal 100% zgodność z najnowszym lokalnym aparatem baz danych programu SQL Server (Enterprise Edition), zapewniająca implementację natywnej [sieci wirtualnej (VNet),](../virtual-network/virtual-networks-overview.md) która rozwiązuje typowe problemy związane z zabezpieczeniami, oraz [model biznesowy](https://azure.microsoft.com/pricing/details/sql-database/) korzystny dla lokalnych klientów programu SQL Server. Model wdrażania wystąpienia zarządzanego umożliwia istniejącym klientom programu SQL Server podnoszenie i przenoszenie aplikacji lokalnych do chmury przy minimalnych zmianach aplikacji i bazy danych. Jednocześnie opcja wdrażania wystąpienia zarządzanego zachowuje wszystkie funkcje PaaS (automatyczne instalowanie poprawek i aktualizacji wersji, [automatyczne tworzenie kopii zapasowych,](sql-database-automated-backups.md) [wysoka dostępność),](sql-database-high-availability.md) co drastycznie zmniejsza obciążenie związane z zarządzaniem i koszt posiadania.

> [!IMPORTANT]
> Aby uzyskać listę regionów, w których opcja wdrażania wystąpienia zarządzanego jest obecnie dostępna, zobacz [obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions).

Na poniższym diagramie przedstawiono najważniejsze funkcje wystąpień zarządzanych:

![najważniejsze funkcje](./media/sql-database-managed-instance/key-features.png)

Model wdrażania wystąpienia zarządzanego jest przeznaczony dla klientów, którzy chcą przeprowadzić migrację dużej liczby aplikacji z lokalnego lub IaaS, środowiska wbudowanego samodzielnie lub isv do w pełni zarządzanego środowiska chmury PaaS, przy jak najmniejszym wysiłku związanym z migracją. Korzystając z w pełni zautomatyzowanej [usługi migracji danych (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) na platformie Azure, klienci mogą podnosić i przenosić swój lokalny program SQL Server do wystąpienia zarządzanego, które oferuje zgodność z programem SQL Server lokalnie i pełną izolację wystąpień klientów z natywną obsługą sieci wirtualnej.  Dzięki pakietowi Software Assurance można wymieniać istniejące licencje na zniżki na wystąpienie zarządzane przy użyciu [korzyści hybrydowej platformy Azure dla programu SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/)  Wystąpienie zarządzane jest najlepszym miejscem docelowym migracji w chmurze dla wystąpień programu SQL Server, które wymagają wysokiego poziomu zabezpieczeń i bogatej powierzchni programowalności.

Opcja wdrażania wystąpienia zarządzanego ma na celu zapewnia blisko 100% zgodność powierzchni z najnowszą lokalną wersją programu SQL Server za pośrednictwem planu wersji etapowej.

Aby zdecydować między opcjami wdrażania usługi Azure SQL Database: pojedynczą bazą danych, pulową bazą danych i wystąpieniem zarządzanym oraz programem SQL Server hostowanym na maszynie wirtualnej, zobacz, [jak wybrać odpowiednią wersję programu SQL Server na platformie Azure.](sql-database-paas-vs-sql-server-iaas.md)

## <a name="key-features-and-capabilities"></a>Najważniejsze funkcje i możliwości

Wystąpienie zarządzane łączy najlepsze funkcje, które są dostępne zarówno w usłudze Azure SQL Database, jak i w programie SQL Server Database Engine.

> [!IMPORTANT]
> Wystąpienie zarządzane jest uruchamiane ze wszystkimi funkcjami najnowszej wersji programu SQL Server, w tym operacjami online, automatycznymi korektami planu i innymi ulepszeniami wydajności przedsiębiorstwa. Porównanie dostępnych funkcji jest wyjaśnione w [porównanie funkcji: Usługa Azure SQL Database vs SQL Server](sql-database-features.md).

| **Korzyści PaaS** | **Ciągłość działalności biznesowej** |
| --- | --- |
|Brak zakupu sprzętu i zarządzania nim <br>Brak kosztów zarządzania zarządzaniem podstawową infrastrukturą <br>Szybkie udostępnianie obsługi administracyjnej i skalowanie usług <br>Automatyczne łatanie i uaktualnianie wersji <br>Integracja z innymi usługami danych PaaS |99.99% umowy SLA o czasie pracy bez przestojów  <br>Zbudowany w [wysokiej dostępności](sql-database-high-availability.md) <br>Dane chronione za pomocą [automatycznych kopii zapasowych](sql-database-automated-backups.md) <br>Okres przechowywania kopii zapasowych z konfigurowaniem przez klienta <br>[Kopie zapasowe](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) inicjowane przez użytkownika <br>[Funkcja przywracania bazy danych w czasie](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Bezpieczeństwo i zgodność** | **Zarządzanie**|
|Środowisko izolowane[(integracja sieci wirtualnej,](sql-database-managed-instance-connectivity-architecture.md)usługa pojedynczej dzierżawy, dedykowane obliczenia i magazynowanie) <br>[Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Uwierzytelnianie usługi Azure AD](sql-database-aad-authentication.md), obsługa logowania jednokrotnego <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Podmioty serwera usługi Azure AD (loginy)</a>  <br>Przestrzega standardów zgodności tak samo jak baza danych SQL usługi Azure <br>[Inspekcja SQL](sql-database-managed-instance-auditing.md) <br>[Zaawansowana ochrona przed zagrożeniami](sql-database-managed-instance-threat-detection.md) |Interfejs API usługi Azure Resource Manager do automatyzacji inicjowania obsługi administracyjnej i skalowania usług <br>Funkcja portalu Azure do ręcznego inicjowania obsługi administracyjnej i skalowania <br>Usługa Data Migration Service

> [!IMPORTANT]
> Usługa Azure SQL Database (wszystkie opcje wdrażania) została certyfikowana zgodnie z wieloma standardami zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania platformy Microsoft Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najnowszą listę certyfikatów zgodności bazy danych SQL.

Najważniejsze funkcje wystąpień zarządzanych są pokazane w poniższej tabeli:

|Funkcja | Opis|
|---|---|
| Wersja / kompilacja programu SQL Server | Aparat baz danych programu SQL Server (najnowsza stabilna) |
| Zarządzane automatyczne kopie zapasowe | Tak |
| Wbudowane monitorowanie instancji i bazy danych oraz metryki | Tak |
| Automatyczne łatanie oprogramowania | Tak |
| Najnowsze funkcje aparatu baz danych | Tak |
| Liczba plików danych (WIERSZY) w bazie danych | Wiele |
| Liczba plików dziennika (LOG) w bazie danych | 1 |
| VNet — wdrożenie usługi Azure Resource Manager | Tak |
| VNet — klasyczny model wdrażania | Nie |
| Obsługa portalu | Tak|
| Wbudowana usługa integracji (SSIS) | Nie — SSIS jest częścią [usługi Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Wbudowana usługa analizy (SSAS) | Nie - SSAS jest [oddzielnym PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Wbudowana usługa raportowania (SSRS) | Nie — zamiast tego użyj [raportów na strony podziałów usługi Power BI](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) lub hosta SSRS na maszynie wirtualnej platformy Azure. Chociaż wystąpienie zarządzane nie może uruchomić SSRS jako usługi, może obsługiwać bazy danych wykazu SSRS 2019 dla zewnętrznego serwera raportowania przy użyciu uwierzytelniania programu SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

[Model zakupów oparty na wynikach wirtualnych](sql-database-service-tiers-vcore.md) dla wystąpień zarządzanych zapewnia elastyczność, kontrolę, przejrzystość i prosty sposób tłumaczenia lokalnych wymagań dotyczących obciążenia w chmurze. Ten model umożliwia zmianę obliczeń, pamięci i magazynu w zależności od potrzeb związanych z obciążeniem. Model vCore kwalifikuje się również do 55 procent oszczędności dzięki [korzyści hybrydowej platformy Azure dla programu SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/)

W modelu vCore można wybierać między generacjami sprzętu.

- **Gen4** Procesory logiczne są oparte na procesorach Intel E5-2673 v3 (Haswell) 2,4 GHz, dołączonym dysku SSD, rdzeniach fizycznych, 7 GB pamięci RAM na rdzeń i rozmiarach obliczeniowych od 8 do 24 rdzeni wirtualnych.
- **Gen5** Procesory logiczne są oparte na procesorach Intel E5-2673 v4 (Broadwell) 2,3 GHz i Intel SP-8160 (Skylake), szybkich dyskach SSD NVMe, hiperwątkowym rdzeniu logicznym i rozmiarach obliczeniowych od 4 do 80 rdzeni.

Znajdź więcej informacji na temat różnicy między generacjami sprzętu w [limitach zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Warstwy usług wystąpienia zarządzanego

Wystąpienie zarządzane jest dostępne w dwóch warstwach usług:

- **Przeznaczenie ogólne:** przeznaczone do zastosowań o typowej wydajności i wymaganiach dotyczących opóźnień we/wy.
- **Krytyczne dla firmy:** Przeznaczone do aplikacji o niskim poziomie opóźnień we/wy i minimalnym wpływie podstawowych operacji konserwacji na obciążenie pracą.

Obie warstwy usług gwarantują dostępność na poziomie 99,99% i umożliwiają samodzielne wybieranie rozmiaru magazynu i pojemności obliczeniowej. Aby uzyskać więcej informacji na temat architektury wysokiej dostępności usługi Azure SQL Database, zobacz [Wysoka dostępność i baza danych SQL Azure](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Warstwa usług ogólnego przeznaczenia

Na poniższej liście opisano kluczową charakterystykę warstwy usług ogólnego przeznaczenia:

- Projektowanie dla większości zastosowań biznesowych o typowych wymaganiach dotyczących wydajności
- Wydajny magazyn obiektów blob platformy Azure (8 TB)
- Wbudowana [wysoka dostępność oparta](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) na niezawodnym magazynie obiektów Blob platformy Azure i [sieci szkieletowej usług Azure](../service-fabric/service-fabric-overview.md)

Aby uzyskać więcej informacji, zobacz [warstwę magazynu w warstwie ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) i [wydajności magazynu najlepsze rozwiązania i zagadnienia dotyczące wystąpień zarządzanych (ogólnego przeznaczenia)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Znajdź więcej informacji na temat różnicy między warstwami usług w [limitach zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Warstwa usług o krytycznym znaczeniu dla firmy

Warstwa usług o krytycznym znaczeniu dla firmy jest przeznaczona dla aplikacji o wysokich wymaganiach dotyczących we/wy. Oferuje najwyższą odporność na awarie przy użyciu kilku replik izolowanych.

Na poniższej liście przedstawiono kluczowe cechy warstwy usług o krytycznym znaczeniu dla firmy:

- Zaprojektowany do zastosowań biznesowych o najwyższej wydajności i wymaganiach wysokiej jakości
- Jest wyposażony w superszybką lokalną pamięć masową SSD (do 1 TB w Gen4 i do 4 TB w Gen5)
- Wbudowana [wysoka dostępność oparta](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) na [grupach dostępności always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) i [sieci szkieletowej usług Azure.](../service-fabric/service-fabric-overview.md)
- Wbudowana dodatkowa [replika bazy danych tylko do odczytu,](sql-database-read-scale-out.md) która może być używana do raportowania i innych obciążeń tylko do odczytu
- [Oltp w pamięci,](sql-database-in-memory.md) który może być używany do obciążenia z wymaganiami o wysokiej wydajności  

Znajdź więcej informacji na temat różnicy między warstwami usług w [limitach zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operacje zarządzania wystąpieniami zarządzanymi

Usługa Azure SQL Database udostępnia operacje zarządzania, których można używać do automatycznego wdrażania nowych wystąpień zarządzanych, aktualizowania właściwości wystąpień i usuwania wystąpień, gdy nie są już potrzebne. Ta sekcja zawiera informacje o operacjach zarządzania i ich typowych okresach trwania.

Aby obsługiwać [wdrożenia w sieciach wirtualnych platformy Azure (sieci wirtualne)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) i zapewnić izolację i bezpieczeństwo dla klientów, wystąpienie zarządzane opiera się na [klastrach wirtualnych,](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)które reprezentują dedykowany zestaw izolowanych maszyn wirtualnych wdrożonych wewnątrz podsieci sieci wirtualnej klienta. Zasadniczo każde wdrożenie wystąpienia zarządzanego w pustej podsieci powoduje nową kompilację klastra wirtualnego.

Kolejne operacje na wdrożonych wystąpieniach zarządzanych mogą również mieć wpływ na jego podstawowy klaster wirtualny. Wpływa to na czas trwania operacji zarządzania, ponieważ wdrażanie dodatkowych maszyn wirtualnych wiąże się z obciążeniem, które należy wziąć pod uwagę podczas planowania nowych wdrożeń lub aktualizacji istniejących wystąpień zarządzanych.

Wszystkie operacje zarządzania można klasyfikować w następujący sposób:

- Wdrażanie wystąpienia (tworzenie nowego wystąpienia). 
- Aktualizacja wystąpienia (zmiana właściwości wystąpienia, takich jak vCore lub zarezerwowanego magazynu.
- Usunięcie wystąpienia.

Zazwyczaj operacje na klastrach wirtualnych trwać najdłużej. Czas trwania operacji w klastrach wirtualnych różnią się — poniżej znajdują się wartości, których zazwyczaj można oczekiwać, na podstawie istniejących danych telemetrycznych usługi:

- Tworzenie klastra wirtualnego. Jest to krok synchroniczne w operacjach zarządzania wystąpieniami. **90% operacji kończy się w ciągu 4 godzin.**
- Zmiana rozmiaru klastra wirtualnego (rozszerzanie lub zmniejszanie). Rozwinięcie jest krokiem synchronicznego, podczas gdy zmniejszanie jest wykonywane asynchronicznie (bez wpływu na czas trwania operacji zarządzania wystąpieniami). **90% rozszerzeń klastra kończy się w mniej niż 2,5 godziny.**
- Usunięcie klastra wirtualnego. Usuwanie jest krokiem asynchronicznie, ale można je również [zainicjować ręcznie](sql-database-managed-instance-delete-virtual-cluster.md) w pustym klastrze wirtualnym, w którym to przypadku wykonuje synchronicznie. **90% usuwania wirtualnego klastra kończy się w ciągu 1,5 godziny.**

Ponadto zarządzanie wystąpieniami może również obejmować jedną z operacji w hostowanych bazach danych, co skutkuje dłuższym czasem trwania:

- Dołączanie plików bazy danych z usługi Azure Storage. Jest to krok synchroniczne, takie jak obliczenia (vCore) lub skalowanie magazynu w górę lub w dół w warstwie usług ogólnego przeznaczenia. **90% tych operacji kończy się w 5 minut.**
- Zawsze włączone rozstawienie grupy dostępności. Jest to krok synchroniczne, takie jak obliczenia (vCore) lub skalowanie magazynu w warstwie usług Krytyczne dla firmy, a także w zmianie warstwy usług z ogólnego przeznaczenia na krytyczne dla firmy (lub odwrotnie). Czas trwania tej operacji jest proporcjonalny do całkowitego rozmiaru bazy danych, a także bieżącej aktywności bazy danych (liczba aktywnych transakcji). Działanie bazy danych podczas aktualizowania wystąpienia może wprowadzić znaczne odchylenie całkowitego czasu trwania. **90% tych operacji wykonuje się z prędkością 220 GB / godzinę lub wyższą**.

W poniższej tabeli podsumowano operacje i typowe całkowite czasy trwania:

|Kategoria  |Operacja  |Segment długotrwały  |Szacowany czas trwania  |
|---------|---------|---------|---------|
|**Wdrożenie** |Pierwsze wystąpienie w pustej podsieci|Tworzenie klastra wirtualnego|90% operacji kończy się w ciągu 4 godzin|
|Wdrożenie |Pierwsze wystąpienie innego generowania sprzętu w niepustej podsieci (na przykład pierwsze wystąpienie gen 5 w podsieci z wystąpieniami gen 4)|Tworzenie klastra wirtualnego*|90% operacji kończy się w ciągu 4 godzin|
|Wdrożenie |Tworzenie 4 ładowań wirtualnych w pustej lub niepustej podsieci|Tworzenie klastra wirtualnego**|90% operacji kończy się w ciągu 4 godzin|
|Wdrożenie |Kolejne tworzenie wystąpień w niepustej podsieci (drugie, trzecie wystąpienie itp.)|Zmiana rozmiaru klastra wirtualnego|90% operacji kończy się w ciągu 2,5 godziny|
|**Aktualizacja** |Zmiana właściwości wystąpienia (hasło administratora, logowanie AAD, flaga usługi Azure Hybrid Benefit)|Nie dotyczy|Do 1 minuty|
|Aktualizacja |Skalowanie magazynu wystąpień w górę/w dół (warstwa usług ogólnego przeznaczenia)|Dołączanie plików bazy danych|90% operacji kończy się w 5 minut|
|Aktualizacja |Skalowanie magazynu wystąpień w górę/w dół (warstwa usługi o znaczeniu krytycznym dla firmy)|- Zmiana rozmiaru wirtualnego klastra<br>- Zawsze na rozstawienie grupy dostępności|90% operacji kończy się w 2,5 godziny + czas na wysiew wszystkich baz danych (220 GB / godzinę)|
|Aktualizacja |Skalowanie obliczeń instancji (vCore) w górę i w dół (ogólnego przeznaczenia)|- Zmiana rozmiaru wirtualnego klastra<br>- Dołączanie plików bazy danych|90% operacji kończy się w ciągu 2,5 godziny|
|Aktualizacja |Skalowanie obliczeń instancji (vCore) w górę i w dół (krytyczne dla firmy)|- Zmiana rozmiaru wirtualnego klastra<br>- Zawsze na rozstawienie grupy dostępności|90% operacji kończy się w 2,5 godziny + czas na wysiew wszystkich baz danych (220 GB / godzinę)|
|Aktualizacja |Skalowanie wystąpienia w dół do 4 generów wirtualnych (ogólnego przeznaczenia)|- Zmiana rozmiaru klastra wirtualnego (jeśli odbywa się po raz pierwszy, może to wymagać tworzenia klastra wirtualnego**)<br>- Dołączanie plików bazy danych|90% operacji kończy się w 4 h 5 min**|
|Aktualizacja |Skalowanie wystąpienia w dół do 4 owo wirtualnych (krytyczne dla firmy)|- Zmiana rozmiaru klastra wirtualnego (jeśli odbywa się po raz pierwszy, może to wymagać tworzenia klastra wirtualnego**)<br>- Zawsze na rozstawienie grupy dostępności|90% operacji kończy się w ciągu 4 godzin + czas na wysiew wszystkich baz danych (220 GB / godzinę)|
|Aktualizacja |Zmiana warstwy usługi wystąpienia (ogólnego przeznaczenia na krytyczne dla firmy i odwrotnie)|- Zmiana rozmiaru wirtualnego klastra<br>- Zawsze na rozstawienie grupy dostępności|90% operacji kończy się w 2,5 godziny + czas na wysiew wszystkich baz danych (220 GB / godzinę)|
|**Usunięcie**|Usunięcie wystąpienia|Rejestrowanie kopii zapasowej ogona dla wszystkich baz danych|90% operacji kończy się w ciągu 1 minuty.<br>Uwaga: jeśli ostatnie wystąpienie w podsieci zostanie usunięte, ta operacja zaplanuje usunięcie klastra wirtualnego po 12 godzinach***|
|Usunięcie|Usunięcie klastra wirtualnego (jako operacja inicjowana przez użytkownika)|Usuwanie klastra wirtualnego|90% operacji kończy się w ciągu 1,5 godziny|

\*Klaster wirtualny jest zbudowany na generację sprzętu.

\*\*Opcja wdrażania 4 linii wirtualnych została wydana w czerwcu 2019 r. i wymaga nowej wersji klastra wirtualnego. Jeśli w podsieci docelowej były wystąpienia utworzone przed 12 czerwca, nowy klaster wirtualny zostanie automatycznie wdrożony do obsługi 4 wystąpień 4-owo wirtualnych.

\*\*\*12 godzin to bieżąca konfiguracja, ale może się to zmienić w przyszłości, więc nie należy od niej uzależniać. Jeśli chcesz usunąć klaster wirtualny wcześniej (na przykład zwolnić podsieć), zobacz [Usuwanie podsieci po usunięciu wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Dostępność wystąpienia podczas zarządzania

Wystąpienia zarządzane nie są dostępne dla aplikacji klienckich podczas wdrażania i usuwania operacji.

Wystąpienia zarządzane są dostępne podczas operacji aktualizacji, ale istnieje krótki przestój spowodowany przez pracę awaryjną, która odbywa się na końcu aktualizacji, która zazwyczaj trwa do 10 sekund. Wyjątkiem jest aktualizacja zarezerwowanego miejsca do magazynowania w warstwie usług ogólnego przeznaczenia, która nie powoduje przeczesywać pracy awaryjnej ani wpływać na dostępność wystąpień.

> [!IMPORTANT]
> Czas pracy awaryjnej może się znacznie różnić w przypadku długotrwałych transakcji, które mają miejsce w bazach danych z powodu [wydłużenia czasu odzyskiwania.](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process) W związku z tym nie jest zalecane skalowanie obliczeń lub magazynu wystąpienia zarządzanego usługi Azure SQL Database lub zmiany warstwy usługi w tym samym czasie z długotrwałych transakcji (import danych, zadania przetwarzania danych, odbudować indeks, itp.). Praca awaryjna bazy danych, która zostanie wykonana po zakończeniu operacji, anuluje bieżące transakcje i spowoduje wydłużenie czasu odzyskiwania.

> [!TIP]
> Aktualizacja zarezerwowanego miejsca do magazynowania w warstwie usług ogólnego przeznaczenia nie powoduje poniesienia trybu failover ani wpływu na dostępność wystąpień.

[Przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md) nie jest obecnie dostępne dla wystąpień zarządzanych usługi Azure SQL Database. Po włączeniu ta funkcja znacznie zmniejszy zmienność czasu pracy awaryjnej, nawet w przypadku długotrwałych transakcji.

### <a name="canceling-management-operations"></a>Anulowanie operacji zarządzania

W poniższej tabeli podsumowano możliwość anulowania określonych operacji zarządzania i typowych całkowitych czasów trwania:

Kategoria  |Operacja  |Cancelable  |Szacowany czas trwania anuluj  |
|---------|---------|---------|---------|
|Wdrożenie |Tworzenie wystąpienia |Nie |  |
|Aktualizacja |Skalowanie magazynu wystąpień w górę/w dół (ogólnego przeznaczenia) |Nie |  |
|Aktualizacja |Skalowanie magazynu wystąpień w górę/w dół (krytyczne dla firmy) |Tak |90% operacji kończy się w 5 minut |
|Aktualizacja |Skalowanie obliczeń instancji (vCore) w górę i w dół (ogólnego przeznaczenia) |Tak |90% operacji kończy się w 5 minut |
|Aktualizacja |Skalowanie obliczeń instancji (vCore) w górę i w dół (krytyczne dla firmy) |Tak |90% operacji kończy się w 5 minut |
|Aktualizacja |Zmiana warstwy usługi wystąpienia (ogólnego przeznaczenia na krytyczne dla firmy i odwrotnie) |Tak |90% operacji kończy się w 5 minut |
|Usuń |Usunięcie wystąpienia |Nie |  |
|Usuń |Usunięcie klastra wirtualnego (jako operacja inicjowana przez użytkownika) |Nie |  |

Aby anulować operację zarządzania, przejdź do bloku przeglądu i kliknij pole powiadomienia o trwającej operacji. Z prawej strony pojawi się ekran z bieżącą operacją i pojawi się przycisk anulowania operacji. Po pierwszym kliknięciu zostaniesz poproszony o ponowne kliknięcie i potwierdzenie, że chcesz anulować operację.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Po przesłaniu i przetworzyniu żądania anulowania otrzymasz powiadomienie, jeśli anulowanie zostało rozpatrzone pozytywnie, czy nie. 

W przypadku anulowania sukcesu, operacja zarządzania zostanie anulowana w ciągu kilku minut, co spowoduje niepowodzenie.

![anulowanie wyniku operacji](./media/sql-database-managed-instance/canceling-operation-result.png)

Jeśli żądanie anuluj nie powiedzie się lub przycisk anuluj nie jest aktywny, oznacza to, że operacja zarządzania została wprowadzona nie anulowane stan i że zakończy się w ciągu kilku minut. Operacja zarządzania będzie kontynuować jego wykonanie, dopóki nie zostanie zakończona.

> [!IMPORTANT]
> Anulowanie operacji jest obecnie obsługiwane tylko w portalu.

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Opcja wdrażania wystąpienia zarządzanego łączy zaawansowane funkcje zabezpieczeń dostarczane przez chmurę platformy Azure i aparat baz danych programu SQL Server.

### <a name="managed-instance-security-isolation"></a>Izolacja zabezpieczeń wystąpienia zarządzanego

Wystąpienie zarządzane zapewnia dodatkową izolację zabezpieczeń od innych dzierżaw w chmurze platformy Azure. Izolacja zabezpieczeń obejmuje:

- [Natywna implementacja sieci wirtualnej](sql-database-managed-instance-connectivity-architecture.md) i łączność ze środowiskiem lokalnym przy użyciu usługi Azure Express Route lub bramy sieci VPN.
- W przypadku wdrożenia domyślnego punkt końcowy SQL jest udostępniane tylko za pośrednictwem prywatnego adresu IP, co umożliwia bezpieczną łączność z prywatnej platformy Azure lub sieci hybrydowych.
- Pojedyncza dzierżawa z dedykowaną infrastrukturą podstawową (obliczeń, magazyn).

Na poniższym diagramie przedstawiono różne opcje łączności dla aplikacji:

![wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Aby dowiedzieć się więcej szczegółów na temat integracji sieci wirtualnej i wymuszania zasad sieci sieci na poziomie podsieci, zobacz [architektura sieci wirtualnej dla wystąpień zarządzanych](sql-database-managed-instance-connectivity-architecture.md) i [Łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Umieść wiele wystąpień zarządzanych w tej samej podsieci, wszędzie tam, gdzie jest to dozwolone przez wymagania dotyczące zabezpieczeń, ponieważ przyniesie Ci to dodatkowe korzyści. Kolokowanie wystąpień w tej samej podsieci znacznie uprości konserwację infrastruktury sieciowej i skróci czas inicjowania obsługi administracyjnej wystąpień, ponieważ długi czas inicjowania obsługi administracyjnej jest skojarzony z kosztem wdrożenia pierwszego wystąpienia zarządzanego w podsieci.

### <a name="azure-sql-database-security-features"></a>Funkcje zabezpieczeń bazy danych SQL platformy Azure

Usługa Azure SQL Database udostępnia zestaw zaawansowanych funkcji zabezpieczeń, które mogą służyć do ochrony danych.

- [Inspekcja wystąpienia zarządzanego](sql-database-managed-instance-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji umieszczonym na koncie magazynu platformy Azure. Inspekcja może pomóc w utrzymaniu zgodności z przepisami, zrozumieniu aktywności bazy danych i uzyskaniu wglądu w rozbieżności i anomalie, które mogą wskazywać na obawy biznesowe lub podejrzewane naruszenia zabezpieczeń.
- Szyfrowanie danych w ruchu — wystąpienie zarządzane zabezpiecza dane, zapewniając szyfrowanie danych w ruchu przy użyciu zabezpieczeń warstwy transportowej. Oprócz zabezpieczeń warstwy transportu opcja wdrażania wystąpienia zarządzanego oferuje ochronę poufnych danych w locie, w spoczynku i podczas przetwarzania zapytań za pomocą [opcji Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkcja Always Encrypted to pierwsze w branży rozwiązanie, które oferuje bezkonkurencyjne zabezpieczenie danych przed naruszeniami, w tym przed kradzieżą danych o kluczowym znaczeniu. Na przykład w przypadku zawsze zaszyfrowanych numery kart kredytowych są zawsze przechowywane w bazie danych, nawet podczas przetwarzania zapytań, co pozwala na odszyfrowanie w miejscu użycia przez autoryzowanych pracowników lub aplikacje, które muszą przetwarzać te dane.
- [Zaawansowana ochrona przed zagrożeniami](sql-database-managed-instance-threat-detection.md) [uzupełnia inspekcję,](sql-database-managed-instance-auditing.md) udostępniając dodatkową warstwę analizy zabezpieczeń wbudowanej w usługę, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Użytkownik jest powiadamiany o podejrzanych działaniach, potencjalnych lukach w zabezpieczeniach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych. Alerty zaawansowanej ochrony przed zagrożeniami można wyświetlać w [usłudze Azure Security Center](https://azure.microsoft.com/services/security-center/) i dostarczać szczegółowe informacje o podejrzanej aktywności i zalecać działania dotyczące sposobu badania i łagodzenia zagrożenia.  
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ogranicza ekspozycję poufnych danych, maskując ją użytkownikom nieuprzywilejowanym. Dynamiczne maskowanie danych pomaga zapobiegać nieautoryzowanemu dostępowi do poufnych danych, umożliwiając określenie, ile poufnych danych ma zostać ujawnionych przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) umożliwiają kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie cech użytkownika wykonującego kwerendę (na przykład według członkostwa w grupie lub kontekstu wykonywania). Zabezpieczenia na poziomie wiersza (RLS, Row-Level Security) upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Na przykład zapewnienie, że pracownicy mogą uzyskać dostęp tylko do wierszy danych, które są istotne dla ich działu lub ograniczenie dostępu do danych tylko do odpowiednich danych.
- [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) szyfruje pliki danych zarządzanych wystąpień, znane jako szyfrowanie danych w spoczynku. TDE wykonuje szyfrowanie we/wy w czasie rzeczywistym i odszyfrowywanie danych i plików dziennika. Szyfrowanie używa klucza szyfrowania bazy danych (DEK), który jest przechowywany w rekordzie rozruchu bazy danych w celu zapewnienia dostępności podczas odzyskiwania. Można chronić wszystkie bazy danych w wystąpieniu zarządzanym za pomocą przezroczystego szyfrowania danych. TDE to sprawdzona technologia szyfrowania w spoczynku programu SQL Server, która jest wymagana przez wiele standardów zgodności w celu ochrony przed kradzieżą nośników pamięci masowej.

Migracja zaszyfrowanej bazy danych do wystąpienia zarządzanego jest obsługiwana za pośrednictwem usługi migracji bazy danych Azure (DMS) lub przywracania macierzystego. Jeśli planujesz przeprowadzić migrację zaszyfrowanej bazy danych przy użyciu przywracania macierzystego, migracja istniejącego certyfikatu TDE z lokalnego programu SQL Server lub programu SQL Server na maszynie wirtualnej do wystąpienia zarządzanego jest wymaganym krokiem. Aby uzyskać więcej informacji na temat opcji migracji, zobacz [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Opcja wdrażania wystąpienia zarządzanego obsługuje tradycyjne logowania i logowania aparatu bazy danych serwera SQL zintegrowane z usługą Azure Active Directory (AAD). Podmioty serwera usługi Azure AD (logowania)**(publiczna wersja zapoznawcza)** to lokalna wersja danych logowania do lokalnej bazy danych w chmurze platformy Azure, której używasz w środowisku lokalnym. Podmioty serwera usługi Azure AD (logowania) umożliwiają określenie użytkowników i grup z dzierżawy usługi Azure Active Directory jako podmiotów o określonym zakresie wystąpienia, zdolnych do wykonywania operacji na poziomie wystąpienia, w tym zapytań między bazami danych w tym samym wystąpieniu zarządzanym.

Wprowadzono nową składnię do tworzenia podmiotów serwera usługi Azure AD (loginów) **z zewnętrznego dostawcy**. Aby uzyskać więcej informacji na temat składni, zobacz <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">TWORZENIE LOGOWANIA</a>i przejrzyj [artykuł Aprowizuj administratora usługi Azure Active Directory dla wystąpienia zarządzanego.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Opcja wdrażania wystąpienia zarządzanego umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft za pomocą [integracji usługi Azure Active Directory.](sql-database-aad-authentication.md) Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication-configure.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania.

### <a name="authentication"></a>Authentication

Uwierzytelnianie wystąpienia zarządzanego odnosi się do sposobu, w jaki użytkownicy potwierdzają swoją tożsamość podczas łączenia się z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:  

- **Uwierzytelnianie SQL:**

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła.
- **Uwierzytelnianie usługi Azure Active Directory:**

  Ta metoda uwierzytelniania używa tożsamości zarządzanych przez usługę Azure Active Directory i jest obsługiwana dla domen zarządzanych i zintegrowanych. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co użytkownik może zrobić w bazie danych SQL Azure i jest kontrolowana przez członkostwo roli bazy danych konta użytkownika i uprawnienia na poziomie obiektu. Wystąpienie zarządzane ma takie same możliwości autoryzacji jak SQL Server 2017.

## <a name="database-migration"></a>Migracja bazy danych

Opcja wdrażania wystąpienia zarządzanego jest przeznaczona dla scenariuszy użytkowników z masową migracją bazy danych z lokalnych lub implementacji bazy danych IaaS. Wystąpienie zarządzane obsługuje kilka opcji migracji bazy danych:

### <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie  

Podejście migracji wykorzystuje kopie zapasowe SQL do magazynu obiektów Blob platformy Azure. Kopie zapasowe przechowywane w obiekcie blob magazynu platformy Azure można bezpośrednio przywrócić do wystąpienia zarządzanego za pomocą [polecenia T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Aby uzyskać przewodnik Szybki start pokazujący, jak przywrócić plik kopii zapasowej bazy danych Wide World Importers — Standard, zobacz [Przywracanie pliku kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md). Ten przewodnik Szybki start pokazuje, że musisz przekazać plik kopii zapasowej do magazynu obiektów blob platformy Azure i zabezpieczyć go przy użyciu klucza sygnatury dostępu współdzielonego (SAS).
- Aby uzyskać informacje o przywracaniu z adresu URL, zobacz [Przywracanie natywne z adresu URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Kopie zapasowe z wystąpienia zarządzanego można przywrócić tylko do innego wystąpienia zarządzanego. Nie można ich przywrócić do lokalnego programu SQL Server ani do pojedynczej bazy danych/puli elastycznej.

### <a name="data-migration-service"></a>Usługa Data Migration Service

Usługa migracji bazy danych azure to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowej migracji z wielu źródeł bazy danych do platform Azure Data przy minimalnym przestoju. Ta usługa usprawnia zadania wymagane do przeniesienia istniejących baz danych innych firm i programu SQL Server do bazy danych SQL Database (pojedyncze bazy danych, buforowane bazy danych w pulach elastycznych i bazy danych wystąpień w wystąpieniu zarządzanym) i sql server w usłudze Azure VM. Zobacz [Jak przeprowadzić migrację lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Obsługiwane funkcje SQL

Opcja wdrażania wystąpienia zarządzanego ma na celu zapewnienie zgodności powierzchni w pobliżu 100% z lokalnym programem SQL Server przychodzącymi etapami do ogólnej dostępności usługi. Aby uzyskać funkcje i listę porównawczą, zobacz [porównanie funkcji bazy danych SQL](sql-database-features.md)oraz listę różnic T-SQL w wystąpieniach zarządzanych w porównaniu z programem SQL Server, zobacz [różnice T-SQL wystąpienia zarządzanego z programu SQL Server](sql-database-managed-instance-transact-sql-information.md).

Opcja wdrażania wystąpienia zarządzanego obsługuje zgodność wsteczną z bazami danych SQL 2008. Bezpośrednia migracja z serwerów bazy danych SQL 2005 jest obsługiwana, poziom zgodności z migrowanymi bazami danych SQL 2005 jest aktualizowany do programu SQL 2008.
  
Na poniższym diagramie przedstawiono zgodność powierzchni w wystąpieniu zarządzanym:  

![Migracji](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Kluczowe różnice między programem SQL Server w środowisku lokalnym a wystąpieniem zarządzanym

Opcja wdrażania wystąpienia zarządzanego korzysta z bycia zawsze aktualną w chmurze, co oznacza, że niektóre funkcje w lokalnym programie SQL Server mogą być przestarzałe, wycofane lub mają alternatywy. Istnieją szczególne przypadki, gdy narzędzia muszą rozpoznać, że dana funkcja działa w nieco inny sposób lub że usługa jest uruchomiona w środowisku, które nie jest w pełni kontrolować:

- Wysoka dostępność jest wbudowana i wstępnie skonfigurowana przy użyciu technologii podobnej do [grup dostępności zawsze włączonych.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
- Automatyczne kopie zapasowe i przywracanie punktu w czasie. Klient może `copy-only` inicjować kopie zapasowe, które nie zakłócają automatycznego łańcucha kopii zapasowych.
- Wystąpienie zarządzane nie zezwala na określanie pełnych ścieżek fizycznych, więc wszystkie odpowiednie scenariusze muszą być obsługiwane inaczej: przywracanie bazy danych nie obsługuje z move, tworzenie bazy danych nie zezwala na ścieżki fizyczne, BULK INSERT działa tylko z obiektami blob platformy Azure itp.
- Wystąpienie zarządzane obsługuje [uwierzytelnianie usługi Azure AD](sql-database-aad-authentication.md) jako alternatywę dla uwierzytelniania systemu Windows w chmurze.
- Wystąpienie zarządzane automatycznie zarządza grupą plików XTP i plikami baz danych zawierających obiekty OLTP w pamięci
- Wystąpienie zarządzane obsługuje usługi SQL Server Integration Services (SSIS) i może obsługiwać katalog SSIS (SSISDB), który przechowuje pakiety SSIS, ale są one wykonywane w zarządzanym czasie wykonywania integracji platformy Azure-SSIS (IR) w usłudze Azure Data Factory (ADF), zobacz [Tworzenie usługi Azure-SSIS IR w usłudze ADF.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) Aby porównać funkcje SSIS w bazie danych SQL, zobacz [Porównanie pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego.](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)

### <a name="managed-instance-administration-features"></a>Funkcje administrowania wystąpieniami zarządzanymi

Opcja wdrażania wystąpienia zarządzanego umożliwia administratorowi systemu spędzanie mniej czasu na zadaniach administracyjnych, ponieważ usługa bazy danych SQL wykonuje je za ciebie lub znacznie upraszcza te zadania. Na przykład [instalacja i łatanie systemu operacyjnego / RDBMS,](sql-database-high-availability.md) [dynamiczna zmiana rozmiaru i konfiguracja wystąpienia,](sql-database-single-database-scale.md) [kopie zapasowe,](sql-database-automated-backups.md) [replikacja bazy danych](replication-with-sql-database-managed-instance.md) (w tym bazy danych systemu), [konfiguracja wysokiej dostępności](sql-database-high-availability.md)oraz konfiguracja strumieni danych monitorowania kondycji i [wydajności.](../azure-monitor/insights/azure-sql.md)

> [!IMPORTANT]
> Aby uzyskać listę obsługiwanych, częściowo obsługiwanych i nieobsługiconych funkcji, zobacz [funkcje bazy danych SQL](sql-database-features.md). Aby uzyskać listę różnic T-SQL w wystąpieniach zarządzanych w porównaniu z programem SQL Server, zobacz [różnice T-SQL wystąpienia zarządzanego z programu SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programowo zidentyfikować wystąpienie zarządzane

W poniższej tabeli przedstawiono kilka właściwości, dostępne za pośrednictwem programu Transact SQL, których można użyć do wykrycia, że aplikacja pracuje z wystąpieniem zarządzanym i pobrać ważne właściwości.

|Właściwość|Wartość|Komentarz|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) — 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ta wartość jest taka sama jak w bazie danych SQL. **Nie** oznacza to, że aparat SQL w wersji 12 (SQL Server 2014). Wystąpienie zarządzane zawsze uruchamia najnowszą stabilną wersję aparatu SQL, która jest równa lub wyższa niż najnowsza dostępna wersja programu SQL Server.  |
|`SERVERPROPERTY ('Edition')`|Usługi SQL Azure|Ta wartość jest taka sama jak w bazie danych SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Ta wartość jednoznacznie identyfikuje wystąpienie zarządzane.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Pełna nazwa DNS wystąpienia w`<instanceName>`następującym formacie: . `<dnsPrefix>`.database.windows.net, gdzie `<instanceName>` jest nazwa podana przez `<dnsPrefix>` klienta, podczas gdy jest automatyczniegenerowana część nazwy gwarantującej unikatowość globalnej nazwy DNS ("wcus17662feb9ce98", na przykład)|Przykład: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik szybki start](sql-database-managed-instance-get-started.md).
- Aby uzyskać funkcje i listę porównawczą, zobacz [wspólne funkcje SQL](sql-database-features.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [konfigurację sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- Aby uzyskać przewodnik Szybki start, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z samouczkiem przy użyciu usługi migracji bazy danych Azure (DMS) do migracji, zobacz [migracja wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpienia zarządzanego za pomocą wbudowanej analizy rozwiązywania problemów, zobacz [Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [cennik zarządzanych wystąpień bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
