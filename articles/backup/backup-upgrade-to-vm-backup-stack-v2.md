---
title: Uaktualnij do modelu wdrażania usługi Azure Resource Manager stosu kopii zapasowej maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Uaktualnij proces i często zadawane pytania dla stos kopii zapasowej maszyny Wirtualnej, modelu wdrażania usługi Resource Manager
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 1e5515486afac5a6d84a35bca33f55ae98e287d3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Uaktualnij do modelu wdrażania usługi Azure Resource Manager stosu kopii zapasowej maszyny Wirtualnej Azure
Model wdrażania Menedżera zasobów dla uaktualnienia do stosu kopii zapasowej maszyny wirtualnej (VM) zapewnia następujące udoskonalenia:
* Zdolność do wyświetlania migawek wykonanych w ramach zadania kopii zapasowej, który jest dostępny dla tej operacji bez oczekiwania na przesyłanie danych do zakończenia. Zmniejsza czas oczekiwania dla migawek można skopiować do magazynu przed wyzwalanie przywracania. Ponadto dzięki temu nie wymaganie dodatkowy magazyn do wykonywania kopii zapasowych maszyn wirtualnych, premium, z wyjątkiem pierwszej kopii zapasowej.  

* Skrócenie czasu kopii zapasowej i przywracania zachowując migawki lokalnie na siedem dni.

* Obsługa dysku rozmiarów do 4 TB.

* Możliwość użycia niezarządzane wirtualna oryginalnego konta magazynu podczas przywracania. Ta możliwość istnieje nawet wtedy, gdy maszyna wirtualna ma dysków, które są rozproszone na kontach magazynu. Przywraca pozwala szybciej w wielu różnych konfiguracji maszyny Wirtualnej.
    > [!NOTE] 
    > Ta możliwość nie jest taka sama jak zastępowanie oryginalna maszyna wirtualna. 
    >

## <a name="whats-changing-in-the-new-stack"></a>Co to jest zmiana w nowego stosu?
Obecnie zadanie tworzenia kopii zapasowej składa się z dwóch etapów:
1.  Tworzenie migawki maszyny Wirtualnej. 
2.  Przenoszenie migawki maszyny Wirtualnej w magazynie usługi Kopia zapasowa Azure. 

Punkt odzyskiwania jest uważany za tworzone dopiero po zakończeniu fazy 1 i 2. W ramach nowego stosu punkt odzyskiwania jest tworzony zaraz po zakończeniu migawki. Można także przywrócić z tego punktu przywracania przy użyciu tego samego przepływu przywracania. Można zidentyfikować tego punktu odzyskiwania w portalu Azure za pomocą "snapshot" jako typu punktu odzyskiwania. Po przekazaniu migawki do magazynu, typ punktu odzyskiwania zmienia się na "migawki i magazynu." 

![Zadanie tworzenia kopii zapasowej w maszyny Wirtualnej kopii zapasowej stosu usługi Resource Manager modelu wdrażania — magazynu i magazynu](./media/backup-azure-vms/instant-rp-flow.jpg) 

Domyślnie migawki są przechowywane przez 7 dni. Ta funkcja umożliwia przywracanie szybciej zakończenie z migawek. Zmniejsza czas, które są wymagane, aby skopiować dane z magazynu do konta magazynu klienta. 

## <a name="considerations-before-upgrade"></a>Zagadnienia dotyczące przed uaktualnieniem
* Uaktualnienie stos kopii zapasowej maszyny Wirtualnej jest jednym dwukierunkowego. Dlatego wszystkie kopie zapasowe, przejdź do tego przepływu. Ponieważ jest ona włączona na poziomie subskrypcji, wszystkie maszyny wirtualne przechodzą w tego przepływu. Wszystkie nowe informacje będą funkcji są oparte na tym samym stosie. Możliwość kontrolowania, które to na poziomie zasad pochodzi w przyszłych wersjach.

* Migawki są przechowywane lokalnie do zwiększania Tworzenie punktu odzyskiwania, a także aby przyspieszyć przywracania. W związku z tym zostanie wyświetlony koszty magazynowania, które odpowiadają migawek w ciągu siedmiu dni.

* Przyrostowe migawki są przechowywane jako stronicowych obiektów blob. Wszystkich klientów korzystających z niezarządzanego dyski są naliczane opłaty za siedem dni, które migawki są przechowywane na koncie magazynu lokalnego klienta. Zgodnie z bieżącym modelu cenowego należy nie ma żadnych kosztów dla klientów na dyskach zarządzanych.

* Jeśli wykonaj Przywracanie z migawki punktu odzyskiwania maszyny Wirtualnej — wersja Premium, zobaczysz tymczasowej lokalizacji, która jest używana, gdy maszyna wirtualna zostanie utworzona jako część przywracania.

* Dla kont premium magazynu migawek, które są pobierane dla natychmiastowe przywrócenie zajmują 10 TB przydzielonego miejsca.

## <a name="upgrade"></a>Uaktualnienie
### <a name="the-azure-portal"></a>Portalu Azure
Jeśli używasz portalu Azure, zostanie wyświetlone powiadomienie na pulpicie nawigacyjnym magazynu. To powiadomienie odnosi się do obsługi dużych dysków i poprawa prędkości kopii zapasowej i przywracania.

![Zadanie tworzenia kopii zapasowej w modelu wdrażania Menedżera zasobów stosu kopii zapasowej maszyny Wirtualnej — Obsługa powiadomień](./media/backup-azure-vms/instant-rp-banner.png) 

Aby otworzyć ekran uaktualniania do nowego stosu, wybierz banerze. 

![Zadanie tworzenia kopii zapasowej w stos kopii zapasowej maszyny Wirtualnej modelu wdrażania Menedżera zasobów--uaktualnienia](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Uruchom następujące polecenia cmdlet w terminalu PowerShell z podwyższonym poziomem uprawnień:
1.  Zaloguj się do konta platformy Azure: 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Wybierz subskrypcję, który chcesz zarejestrować podglądu:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Zarejestruj tę subskrypcję w prywatnej wersji zapoznawczej:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Sprawdzić, czy uaktualnienie jest zakończona
W terminalu programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenie cmdlet:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Informacja "Zarejestrowanej", subskrypcja jest uaktualniany do modelu wdrażania usługi Resource Manager stosu kopii zapasowej maszyny Wirtualnej.
