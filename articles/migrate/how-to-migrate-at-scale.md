---
title: Automatyzowanie migracji dużej liczby maszyn wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób migracji dużej liczby maszyn wirtualnych przy użyciu usługi Azure Site Recovery za pomocą skryptów
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: f90140e9464ee72e9ceae8ca140bd060c51aade8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597113"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Skala migracji maszyn wirtualnych przy użyciu usługi Azure Site Recovery

Ten artykuł pomoże Ci zrozumieć proces migracji dużej liczby maszyn wirtualnych przy użyciu usługi Azure Site Recovery za pomocą skryptów. Te skrypty są dostępne dla pobierania [przykłady programu Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repozytorium w witrynie GitHub. Skrypty może służyć do migracji programu VMware, AWS, GCP maszyn wirtualnych i serwerów fizycznych do platformy Azure i Obsługa migracji do usługi managed disks. Te skrypty umożliwia również Migrowanie maszyn wirtualnych funkcji Hyper-V, jeśli migrujesz maszyny wirtualne jako serwery fizyczne. Skrypty korzystać z platformy Azure: witryny programu PowerShell odzyskiwania udokumentowane [tutaj](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Bieżące ograniczenia:
- Obsługuje określania statycznego adresu IP tylko dla podstawowego interfejsu Sieciowego docelowej maszyny Wirtualnej
- Skrypty nie przyjmują korzyść użycia hybrydowego platformy Azure związane z danych wejściowych, musisz ręcznie zaktualizować właściwości zreplikowanej maszyny Wirtualnej w portalu

## <a name="how-does-it-work"></a>Jak to działa?

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy należy wykonać następujące czynności:
- Upewnij się, czy magazyn usługi Site Recovery został utworzony w ramach subskrypcji platformy Azure
- Upewnij się, że serwer konfiguracji i serwer przetwarzania, które są zainstalowane w środowisku źródłowym, jak i magazynu jest w stanie wykryć środowiska
- Upewnij się, że zasady replikacji jest utworzona i skojarzona z serwerem konfiguracji
- Upewnij się, że dodano konto administratora maszyny Wirtualnej serwera konfiguracji (który będzie używany do replikowania lokalnych maszyn wirtualnych)
- Upewnij się, że artefakty docelowy, na platformie Azure są tworzone
    - Docelowa grupa zasobów
    - Konto magazynu docelowego (i jej grupy zasobów) — Tworzenie konta usługi premium storage, jeśli planujesz migrację do dysków zarządzanych w warstwie premium
    - Konto magazynu pamięci podręcznej (i jej grupy zasobów) — Utwórz standardowe konto magazynu w tym samym regionie co magazyn
    - Docelowa sieć wirtualna dla trybu failover (i jej grupy zasobów)
    - Podsieć docelowa
    - Docelowa sieć wirtualna do testowania trybu failover (i jej grupy zasobów)
    - Zestaw dostępności (jeśli jest to konieczne)
    - Docelowa grupa zabezpieczeń sieci i jej grupy zasobów
- Upewnij się, że zdecydujesz się na właściwościach docelowej maszyny Wirtualnej
    - Nazwa docelowej maszyny wirtualnej
    - Rozmiar docelowej maszyny Wirtualnej na platformie Azure (może zostać podjęta przy użyciu usługi Azure Migrate oceny)
    - Prywatny adres IP podstawowej karty Sieciowej na maszynie wirtualnej
- Pobierz skrypty z [przykłady programu Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repozytorium w witrynie GitHub

### <a name="csv-input-file"></a>Plik CSV w danych wejściowych
Po utworzeniu warunków wstępnych zakończone, należy utworzyć w pliku CSV, który zawiera dane dla każdej maszyny źródłowej, która mają zostać zmigrowane. Dane wejściowe CSV musi mieć wiersz nagłówka z dane wejściowe i wiersz ze szczegółami dla każdej maszyny, które muszą zostać zmigrowane. Wszystkie skrypty zostały zaprojektowane do pracy na tym samym pliku CSV. Przykładowy szablon CSV jest dostępna w folderze skryptów, dla której można się odwołać.

### <a name="script-execution"></a>Wykonywanie skryptu
Gdy wolumin CSV jest gotowy, można wykonać poniższe kroki, aby przeprowadzić migrację lokalnych maszyn wirtualnych:

**Krok #** | **Nazwa skryptu** | **Opis**
--- | --- | ---
1 | asr_startmigration.ps1 | Włączanie replikacji dla wszystkich maszyn wirtualnych wymienionych w woluminie csv, skrypt tworzy wyjściowego pliku CSV ze szczegółami zadanie dla każdej maszyny Wirtualnej
2 | asr_replicationstatus.ps1 | Sprawdź stan replikacji, skrypt tworzy plik csv ze stanem dla każdej maszyny Wirtualnej
3 | asr_updateproperties.ps1 | Gdy maszyny wirtualne są replikowane chronione, użyj tego skryptu można zaktualizować właściwości docelowej maszyny wirtualnej (obliczenia i sieć właściwości)
4 | asr_propertiescheck.ps1 | Sprawdź, jeśli właściwości są odpowiednio aktualizowane
5 | asr_testmigration.ps1 |  Uruchom testowanie trybu failover maszyn wirtualnych wymienionych w woluminie csv, skrypt tworzy wyjściowego pliku CSV ze szczegółami zadanie dla każdej maszyny Wirtualnej
6 | asr_cleanuptestmigration.ps1 | Po ręcznie zweryfikować maszyn wirtualnych, które zostały testu w trybie failed-over, użyć tego skryptu, aby wyczyścić testowy tryb failover maszyn wirtualnych
7 | asr_migration.ps1 | Wykonaj nieplanowany tryb failover dla maszyn wirtualnych wymienionych w woluminie csv, skrypt tworzy wyjściowego pliku CSV ze szczegółami zadanie dla każdej maszyny Wirtualnej. Skrypt nie jest zamykany lokalnych maszyn wirtualnych przed wyzwoleniem trybu failover w celu zachowania spójności aplikacji, zaleca się ręczne zamykanie maszyny wirtualne przed wykonaniem skryptu.
8 | asr_completemigration.ps1 | Operacja zatwierdzenia na maszynach wirtualnych i usuwania jednostek usługi Azure Site Recovery
9 | asr_postmigration.ps1 | Jeśli planujesz przypisać sieciowych grup zabezpieczeń do kart sieciowych po przełączeniu w tryb failover służy ten skrypt można to zrobić. Sieciowa grupa zabezpieczeń przypisuje do dowolnego z jedną kartą Sieciową na docelowej maszynie Wirtualnej.

## <a name="how-to-migrate-to-managed-disks"></a>Jak przeprowadzić migrację do dysków zarządzanych?
Skrypt, domyślnie umożliwia migrowanie maszyn wirtualnych do usługi managed disks na platformie Azure. Jeśli podane konto magazynu docelowego konta magazynu premium storage, dyski zarządzane w warstwie premium są tworzone po migracji. Konto magazynu pamięci podręcznej może nadal być standardowe konto. Jeśli docelowe konto magazynu jest kontem magazynu w warstwie standardowa, dyski w warstwie standardowa są tworzone po migracji. 

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) o migracji serwerów do platformy Azure przy użyciu usługi Azure Site Recovery
