---
title: Automatyzacja migracji maszyny w obszarze Migracji w usłudze Azure Migrate
description: W tym artykule opisano, jak używać skryptów do migracji dużej liczby komputerów w ramach migracji platformy Azure
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196362"
---
# <a name="scale-migration-of-vms"></a>Migracja skalowania maszyn wirtualnych 

Ten artykuł pomaga zrozumieć, jak używać skryptów do migracji dużej liczby maszyn wirtualnych (maszyn wirtualnych). Aby skalować migrację, należy użyć [usługi Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Skrypty odzyskiwania witryny są dostępne do pobrania w usłudze [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repozytorium w usłudze GitHub. Skrypty mogą służyć do migracji serwerów VMware, AWS, GCP VM i serwerów fizycznych na dyski zarządzane na platformie Azure. Za pomocą tych skryptów można również migrować maszyny wirtualne funkcji Hyper-V w przypadku migracji maszyn wirtualnych jako serwerów fizycznych. Skrypty, które wykorzystują usługę Azure Site Recovery PowerShell są udokumentowane [w tym miejscu.](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)

## <a name="current-limitations"></a>Bieżące ograniczenia
- Obsługa określania statycznego adresu IP tylko dla podstawowej karty sieciowej docelowej maszyny Wirtualnej
- Skrypty nie przyjmują danych wejściowych związanych z korzyścią hybrydową platformy Azure, należy ręcznie zaktualizować właściwości replikowanej maszyny Wirtualnej w portalu

## <a name="how-does-it-work"></a>Jak to działa?

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy wykonać następujące czynności:
- Upewnij się, że magazyn odzyskiwania witryny jest tworzony w ramach subskrypcji platformy Azure
- Upewnij się, że serwer konfiguracji i serwer przetwarzania są zainstalowane w środowisku źródłowym, a przechowalnia jest w stanie odnajdować środowisko
- Upewnij się, że zasady replikacji są tworzone i skojarzone z serwerem konfiguracji
- Upewnij się, że konto administratora maszyny Wirtualnej zostało dodane do serwera konfiguracji (które będzie używane do replikowania lokalnych maszyn wirtualnych)
- Upewnij się, że artefakty docelowe na platformie Azure są tworzone
    - Docelowa grupa zasobów
    - Konto magazynu docelowego (i jego grupa zasobów) — tworzenie konta magazynu w wersji premium, jeśli planujesz migrację na dyski zarządzane w ramach premium
    - Konto magazynu pamięci podręcznej (i jego grupa zasobów) — tworzenie standardowego konta magazynu w tym samym regionie co magazyn
    - Docelowa sieć wirtualna do pracy awaryjnej (i jej grupy zasobów)
    - Podsieć docelowa
    - Docelowa sieć wirtualna do testowania pracy awaryjnej (i jej grupy zasobów)
    - Zestaw dostępności (w razie potrzeby)
    - Docelowa grupa zabezpieczeń sieci i jej grupa zasobów
- Upewnij się, że zdecydowałeś się na właściwości docelowej maszyny Wirtualnej
    - Docelowa nazwa maszyny Wirtualnej
    - Docelowy rozmiar maszyny Wirtualnej na platformie Azure (można zdecydować za pomocą oceny migracji platformy Azure)
    - Prywatny adres IP podstawowej karty sieciowej na maszynie wirtualnej
- Pobieranie skryptów z repozytorium [przykładów programu Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) w usłudze GitHub

### <a name="csv-input-file"></a>Plik wejściowy CSV
Po zakończeniu wszystkich wymagań wstępnych należy utworzyć plik CSV, który zawiera dane dla każdego komputera źródłowego, który ma zostać zmigrowane. Wejściowy plik CSV musi mieć wiersz nagłówka ze szczegółami wejściowymi i wiersz ze szczegółami dla każdego komputera, który musi zostać zmigrowany. Wszystkie skrypty są przeznaczone do pracy na tym samym pliku CSV. Przykładowy szablon CSV jest dostępny w folderze skryptów dla odwołania.

### <a name="script-execution"></a>Wykonanie skryptu
Gdy csv jest gotowy, można wykonać następujące kroki, aby wykonać migrację lokalnych maszyn wirtualnych:

**Krok #** | **Nazwa skryptu** | **Opis**
--- | --- | ---
1 | asr_startmigration.ps1 | Włącz replikację dla wszystkich maszyn wirtualnych wymienionych w csv, skrypt tworzy wyjście CSV ze szczegółami zadania dla każdej maszyny Wirtualnej
2 | asr_replicationstatus.ps1 | Sprawdź stan replikacji, skrypt tworzy csv ze stanem dla każdej maszyny Wirtualnej
3 | asr_updateproperties.ps1 | Gdy maszyny wirtualne są replikowane/chronione, użyj tego skryptu, aby zaktualizować właściwości docelowe maszyny Wirtualnej (właściwości obliczeniowe i sieciowe)
4 | asr_propertiescheck.ps1 | Sprawdź, czy właściwości są odpowiednio zaktualizowane
5 | asr_testmigration.ps1 |  Rozpocznij testową pracę awaryjną maszyn wirtualnych wymienionych w csv, skrypt tworzy wyjście CSV ze szczegółami zadania dla każdej maszyny wirtualnej
6 | asr_cleanuptestmigration.ps1 | Po ręcznej weryfikacji maszyn wirtualnych, które zostały przetestowane nie powiodło się, można użyć tego skryptu, aby wyczyścić test maszyn wirtualnych trybu failover
7 | asr_migration.ps1 | Wykonaj nieplanowane trybu failover dla maszyn wirtualnych wymienionych w csv, skrypt tworzy wyjście CSV ze szczegółami zadania dla każdej maszyny wirtualnej. Skrypt nie zamyka na lokalnych maszyn wirtualnych przed wyzwoleniem pracy awaryjnej, dla spójności aplikacji, zaleca się ręczne zamknięcie maszyn wirtualnych przed wykonaniem skryptu.
8 | asr_completemigration.ps1 | Wykonaj operację zatwierdzania na maszynach wirtualnych i usuń jednostki usługi Azure Site Recovery
9 | asr_postmigration.ps1 | Jeśli planujesz przypisać sieciowe grupy zabezpieczeń do kart sieciowych po przełączeniu w błąd, możesz użyć tego skryptu, aby to zrobić. Przypisuje sieciowej grupy sieciowej do dowolnej karty sieciowej w docelowej maszynie wirtualnej.

## <a name="how-to-migrate-to-managed-disks"></a>Jak przeprowadzić migrację na dyski zarządzane?
Skrypt domyślnie migruje maszyny wirtualne do dysków zarządzanych na platformie Azure. Jeśli konto magazynu docelowego jest kontem magazynu w wersji premium, dyski zarządzane w wersji premium są tworzone po migracji. Konto magazynu pamięci podręcznej nadal może być kontem standardowym. Jeśli docelowe konto magazynu jest standardowym kontem magazynu, dyski standardowe są tworzone po migracji. 

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) o migrowaniu serwerów na platformę Azure przy użyciu usługi Azure Site Recovery
