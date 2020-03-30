---
title: Omówienie modelu rdzeni wirtualnych
description: Model zakupu w ykonie umożliwia niezależne skalowanie zasobów obliczeniowych i magazynowych, dopasowanie wydajności lokalnej i optymalizację ceny.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481086"
---
# <a name="vcore-model-overview"></a>Omówienie modelu rdzeni wirtualnych

Model rdzenia wirtualnego (vCore) zapewnia kilka korzyści:

- Wyższe limity obliczeń, pamięci, we/wy i magazynu.
- Kontrola nad generowaniem sprzętu w celu lepszego dopasowania wymagań obliczeniowych i pamięci obciążenia.
- Rabaty cenowe dla [korzyści hybrydowych platformy Azure (AHB)](sql-database-azure-hybrid-benefit.md) i [wystąpienia zarezerwowanego (RI)](sql-database-reserved-capacity.md).
- Większa przejrzystość szczegółów sprzętu, które zasilają obliczenia; ułatwia planowanie migracji z wdrożeń lokalnych.

## <a name="service-tiers"></a>Warstwy usług

Opcje warstwy usług w modelu vCore obejmują ogólnego przeznaczenia, krytyczne dla firmy i hiperskali. Warstwa usług zazwyczaj definiuje architekturę magazynu, limity miejsca i we/wy oraz opcje ciągłości biznesowej związane z dostępnością i odzyskiwaniem po awarii.

||**Zastosowania ogólne**|**Krytyczne dla biznesu**|**Hiperskala**|
|---|---|---|---|
|Najlepsze dla|Większość obciążeń biznesowych. Oferuje opcje obliczeniowe, zrównoważone i skalowalne opcje obliczeniowe i pamięci masowej. |Oferuje aplikacjom biznesowym najwyższą odporność na awarie przy użyciu kilku replik izolowanych i zapewnia najwyższą wydajność we/wy na replikę bazy danych.|Większość obciążeń biznesowych z wysoce skalowalnymi wymaganiami dotyczącymi pamięci masowej i skali odczytu.  Oferuje większą odporność na awarie, umożliwiając konfigurację więcej niż jednej repliki izolowanej bazy danych. |
|Magazyn|Używa magazynu zdalnego.<br/>**Pojedyncze bazy danych i pule elastyczne aprowizacji obliczeń:**<br/>5 GB – 4 TB<br/>**Obliczenia bezserwerowe:**<br/>5 GB - 3 TB<br/>**Wystąpienie zarządzane:** 32 GB - 8 TB |Używa lokalnego magazynu SSD.<br/>**Pojedyncze bazy danych i pule elastyczne aprowizacji obliczeń:**<br/>5 GB – 4 TB<br/>**Wystąpienie zarządzane:**<br/>32 GB - 4 TB |Elastyczny autogrow pamięci masowej w razie potrzeby. Obsługuje do 100 TB pamięci masowej. Używa lokalnego magazynu SSD do lokalnej pamięci podręcznej puli buforów i lokalnego magazynu danych. Używa magazynu zdalnego platformy Azure jako ostatecznego długoterminowego magazynu danych. |
|IOPS i przepustowość (przybliżona)|**Pojedyncze bazy danych i pule elastyczne**: Zobacz limity zasobów dla [pojedynczych baz danych](../sql-database/sql-database-vcore-resource-limits-single-databases.md) i [pul elastycznych](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Wystąpienie zarządzane:** Zobacz [Omówienie limitów zasobów zarządzanych wystąpienia usługi Azure SQL Database](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics).|Zobacz limity zasobów dla [pojedynczych baz danych](../sql-database/sql-database-vcore-resource-limits-single-databases.md) i [pul elastycznych](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Hiperskala to architektura wielopoziomowa z buforowania na wielu poziomach. Skuteczne we/wy i przepływność będzie zależeć od obciążenia.|
|Dostępność|1 replika, brak replik w skali odczytu|3 repliki, 1 [replika w skali odczytu](sql-database-read-scale-out.md),<br/>wysoka dostępność (strefa nadmiarowa)|1 replika odczytu i zapisu oraz repliki w [skali odczytu](sql-database-read-scale-out.md) 0-4|
|Tworzenie kopii zapasowych|[Magazyn geograficzny dostępu do odczytu (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dni (domyślnie 7 dni)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dni (domyślnie 7 dni)|Kopie zapasowe oparte na migawkach w magazynie zdalnym platformy Azure. Przywraca używa tych migawek do szybkiego odzyskiwania. Kopie zapasowe są natychmiastowe i nie mają wpływu na wydajność we/wy obliczeniowej. Przywraca są szybkie i nie są operacją rozmiaru danych (biorąc minut, a nie godzin lub dni).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||


### <a name="choosing-a-service-tier"></a>Wybieranie warstwy usługi

Aby uzyskać informacje na temat wybierania warstwy usług dla określonego obciążenia, zobacz następujące artykuły:

- [Kiedy wybrać warstwę usług ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Kiedy wybrać warstwę usług o krytycznym znaczeniu dla firmy](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Kiedy wybrać warstwę usługi Hiperskala](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Warstwy obliczeniowe

Opcje warstwy obliczeniowej w modelu vCore obejmują aprowizowanych i bezserwerowych warstw obliczeniowych.


### <a name="provisioned-compute"></a>Obliczenia aprowizacji

Warstwa obliczeń aprowizacji zapewnia określoną ilość zasobów obliczeniowych, które są stale aprowizacji niezależnie od działania obciążenia i rachunki za ilość obliczeń aprowizowanych po stałej cenie za godzinę.


### <a name="serverless-compute"></a>Bezserwerowe usługi obliczeniowe

Warstwa [obliczeniowa bezserwerowa](sql-database-serverless.md) automatycznie skaluje zasoby obliczeniowe na podstawie aktywności obciążenia i rozlicza ilość obliczeń używanych na sekundę.



## <a name="hardware-generations"></a>Generacje sprzętu

Opcje generowania sprzętu w modelu vCore obejmują Gen 4/5, M-series (wersja zapoznawcza) i Fsv2-series (wersja zapoznawcza). Generowanie sprzętu zazwyczaj definiuje limity obliczeniowe i pamięci oraz inne cechy, które mają wpływ na wydajność obciążenia.

### <a name="gen4gen5"></a>Gen4/Gen5

- Sprzęt Gen4/Gen5 zapewnia zrównoważone zasoby obliczeniowe i pamięci i jest odpowiedni dla większości obciążeń bazy danych, które nie mają wyższej pamięci, wyższej liczby komputerów wirtualnych lub szybszych wymagań pojedynczych rów wirtualnych, zgodnie z wymogami serii Fsv2 lub M.

W przypadku regionów, w których dostępna jest gen4/gen5, zobacz [Dostępność gen4/gen5](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Seria Fsv2 (wersja zapoznawcza)

- Seria Fsv2 to zoptymalizowana pod względem obliczeniowym opcja sprzętowa zapewniająca niskie opóźnienia procesora i wysoką szybkość zegara dla najbardziej wymagających obciążeń procesora.
- W zależności od obciążenia seria Fsv2 może zapewnić większą wydajność procesora na rdzenie wirtualne niż Gen5, a rozmiar rdzeni wirtualnych 72 może zapewnić większą wydajność procesora CPU przy niższych kosztach niż 80 rdzeni wirtualnych w gen5. 
- Fsv2 zapewnia mniej pamięci i tempdb na vCore niż inny sprzęt, więc obciążeń wrażliwych na te limity może chcieć rozważyć Gen5 lub M serii zamiast.  

Seria FSv2 jest obsługiwana tylko w warstwie ogólnego przeznaczenia.  W przypadku regionów, w których dostępna jest seria Fsv2, zobacz [Dostępność serii Fsv2.](#fsv2-series)


### <a name="m-seriespreview"></a>Seria M (wersja zapoznawcza)

- Seria M to zoptymalizowana pod kątem pamięci opcja sprzętowa dla obciążeń wymagających większej ilości pamięci i wyższych limitów obliczeniowych niż zapewniana przez Gen5.
- Seria M zapewnia 29 GB na r/r i 128 corów wirtualnych, co zwiększa limit pamięci w stosunku do Gen5 o 8x do prawie 4 TB.

Seria M jest obsługiwana tylko w warstwie Krytyczne dla firm i nie obsługuje nadmiarowości strefy.

Aby włączyć sprzęt serii M dla subskrypcji i regionu, należy otworzyć żądanie pomocy technicznej. Subskrypcja musi być płatnym typem oferty, w tym płatności zgodnie z rzeczywistym lub umową Enterprise Agreement (EA).  Jeśli żądanie pomocy technicznej zostanie zatwierdzone, środowisko wyboru i inicjowania obsługi administracyjnej serii M jest zgodne z tym samym wzorcem, co w przypadku innych generacji sprzętu. W przypadku regionów, w których dostępna jest seria M, zobacz [Dostępność serii M](#m-series).


### <a name="compute-and-memory-specifications"></a>Specyfikacje obliczeniowe i pamięci


|Generowanie sprzętu  |Wystąpienia obliczeniowe  |Memory (Pamięć)  |
|:---------|:---------|:---------|
|Gen4     |- Procesory Intel E5-2673 v3 (Haswell) 2,4 GHz<br>- Rezerwa do 24 rdzeni wirtualnych (1 vCore = 1 rdzeń fizyczny)  |- 7 GB na r/r<br>- Rezerwa do 168 GB|
|Gen5     |**Obliczenia aprowizacji**<br>- Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz i Intel SP-8160 (Skylake)*<br>- Możliwość udostępnienia do 80 vCores (1 vCore = 1 hyper-thread)<br><br>**Bezserwerowe usługi obliczeniowe**<br>- Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz i Intel SP-8160 (Skylake)*<br>- Automatyczne skalowanie do 16 vCores (1 vCore = 1 hyper-thread)|**Obliczenia aprowizacji**<br>- 5,1 GB na r/r<br>- Rezerwa do 408 GB<br><br>**Bezserwerowe usługi obliczeniowe**<br>- Automatyczne skalowanie do 24 GB na vCore<br>- Automatyczne skalowanie do 48 GB maks.|
|Seria Fsv2     |- Procesory Intel Xeon Platinum 8168 (SkyLake)<br>- Wyposażony w trwałą prędkość zegara turbo rdzenia 3,4 GHz i maksymalną jednordzeniową prędkość zegara turbo 3,7 GHz.<br>- Rezerwa 72 vCores (1 vCore = 1 hyper-thread)|- 1,9 GB na r/r<br>- Przepis 136 GB|
|Seria M     |- Procesory Intel Xeon E7-8890 v3 2,5 GHz<br>- Rezerwa 128 vCores (1 vCore = 1 hyper-thread)|- 29 GB na r/r<br>- Rezerwa 3,7 TB|

\*W dynamicznym widoku zarządzania [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) generowanie sprzętu dla baz danych Gen5 przy użyciu procesorów Intel SP-8160 (Skylake) pojawia się jako Gen6. Limity zasobów dla wszystkich baz danych Gen5 są takie same niezależnie od typu procesora (Broadwell lub Skylake).

Aby uzyskać więcej informacji na temat limitów zasobów, zobacz [Limity zasobów dla pojedynczych baz danych (vCore)](sql-database-vcore-resource-limits-single-databases.md)lub [Limity zasobów dla pul elastycznych (vCore)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Wybieranie generacji sprzętu

W witrynie Azure portal można wybrać generowanie sprzętu dla bazy danych LUB puli SQL w momencie tworzenia lub można zmienić generowanie sprzętu istniejącej bazy danych lub puli SQL.

**Aby wybrać generowanie sprzętu podczas tworzenia bazy danych lub puli SQL**

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie bazy danych SQL](sql-database-single-database-get-started.md).

Na karcie **Podstawy** wybierz **łącze Konfigurowanie bazy danych** w sekcji **Obliczaj + magazyn,** a następnie wybierz łącze **Zmień konfigurację:**

  ![Konfigurowanie bazy danych](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Wybierz żądaną generację sprzętu:

  ![wybierz sprzęt](media/sql-database-service-tiers-vcore/select-hardware.png)


**Aby zmienić generowanie sprzętu istniejącej bazy danych lub puli SQL**

W przypadku bazy danych na stronie Przegląd wybierz **łącze warstwy cenowej:**

  ![zmienianie sprzętu](media/sql-database-service-tiers-vcore/change-hardware.png)

W przypadku puli na stronie Przegląd wybierz pozycję **Konfiguruj**.

Wykonaj kroki, aby zmienić konfigurację i wybierz generowanie sprzętu zgodnie z opisem w poprzednich krokach.

**Aby wybrać generowanie sprzętu podczas tworzenia wystąpienia zarządzanego**

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).

Na karcie **Podstawy** wybierz **łącze Konfigurowanie bazy danych** w sekcji **Obliczaj + magazyn,** a następnie wybierz żądaną generację sprzętu:

  ![konfigurowanie wystąpienia zarządzanego](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Aby zmienić generowanie sprzętu istniejącego wystąpienia zarządzanego**

# <a name="portal"></a>[Portal](#tab/azure-portal)

Na stronie wystąpienia zarządzanego wybierz **łącze warstwy cenowej** umieszczone w sekcji Ustawienia

![zmienianie sprzętu wystąpienia zarządzanego](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Na stronie **warstwy cenowej** będzie można zmienić generowanie sprzętu zgodnie z opisem w poprzednich krokach.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Użyj poniższego skryptu programu PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Aby uzyskać więcej informacji, sprawdź [polecenie Set-AzSqlInstance.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia INTERFEJSU WIERSZA POLECENIA:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Aby uzyskać więcej informacji, sprawdź [polecenie aktualizacji az sql mi.](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

---

### <a name="hardware-availability"></a>Dostępność sprzętu

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

Sprzęt Gen4 jest [wycofywany](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) i nie jest już dostępny dla nowych wdrożeń. Wszystkie nowe bazy danych muszą być wdrożone na sprzęcie Gen5.

Gen5 jest dostępny w większości regionów na całym świecie.

#### <a name="fsv2-series"></a>Seria Fsv2

Seria Fsv2 jest dostępna w następujących regionach: Australia Central, Australia Central 2, Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Azja Wschodnia, Wschodnie Stany Zjednoczone, Francja Środkowa, Indie Środkowe, Indie Zachodnie, Korea Środkowa, Korea Południowa, Północna Europa, Republika Południowej Afryki Północnej, Azja Południowo-Wschodnia, Wielka Brytania Południowa, Wielka Brytania Zachodnia, Europa Zachodnia, Zachodnie Stany 2.


#### <a name="m-series"></a>Seria M

Seria M jest dostępna w następujących regionach: Wschodnie stany USA, Europa Północna, Europa Zachodnia, Zachodnie stany USA 2.
Seria M może mieć również ograniczoną dostępność w dodatkowych regionach. Możesz zażądać innego regionu niż wymienione w tym miejscu, ale spełnienie w innym regionie może nie być możliwe.

Aby włączyć dostępność serii M w ramach subskrypcji, należy zażądać dostępu, [składając nowe żądanie pomocy technicznej.](#create-a-support-request-to-enable-m-series)


##### <a name="create-a-support-request-to-enable-m-series"></a>Utwórz żądanie pomocy technicznej, aby włączyć serię M: 

1. Wybierz **Pomoc + wsparcie** w portalu.
2. Wybierz **pozycję Nowe żądanie pomocy technicznej**.

Na stronie **Podstawy** podaj następujące informacje:

1. W obszarze **Typ problemu**wybierz pozycję **Limity usługi i subskrypcji (przydziały).**
2. Dla **subskrypcji** = wybierz subskrypcję, aby włączyć serii M.
3. W przypadku **typu przydziału**wybierz pozycję **Baza danych SQL**.
4. Wybierz **pozycję Dalej,** aby przejść do strony **Szczegóły.**

Na stronie **Szczegóły** podaj następujące informacje:

1. W sekcji **SZCZEGÓŁY PROBLEMU** wybierz łącze **Podaj szczegóły.** 
2. W przypadku **typu przydziału bazy danych SQL** wybierz opcję Seria **M**.
3. W przypadku **regionu**wybierz region, aby włączyć serię M.
    W przypadku regionów, w których dostępna jest seria M, zobacz [Dostępność serii M](#m-series).

Zatwierdzone żądania pomocy technicznej są zazwyczaj wypełniane w ciągu 5 dni roboczych.


## <a name="next-steps"></a>Następne kroki

- Aby utworzyć bazę danych SQL, zobacz [Tworzenie bazy danych SQL przy użyciu portalu Azure](sql-database-single-database-get-started.md).
- Aby uzyskać określone rozmiary obliczeń i opcje rozmiaru magazynu dostępne dla pojedynczych baz danych, zobacz Limity zasobów opartych na polach [wirtualnych bazy danych SQL dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- Aby uzyskać określone rozmiary obliczeń i wybór rozmiaru magazynu dostępne dla pul elastycznych, zobacz [Limity zasobów opartych na](sql-database-vcore-resource-limits-elastic-pools.md)polach elastycznych bazy danych SQL .
- Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę cennika usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).
