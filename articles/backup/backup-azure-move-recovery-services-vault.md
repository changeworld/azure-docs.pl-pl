---
title: Jak przenieść magazyny usług Azure Backup Recovery Services
description: Instrukcje dotyczące przenoszenia magazynu usług odzyskiwania w subskrypcjach platformy Azure i grupach zasobów.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: fed42c578da2e4f27f42e11d5ac67d698bbcd939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120715"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Przenoszenie magazynu usług odzyskiwania w subskrypcjach i grupach zasobów platformy Azure

W tym artykule wyjaśniono, jak przenieść magazyn usług odzyskiwania skonfigurowany dla usługi Azure Backup w ramach subskrypcji platformy Azure lub do innej grupy zasobów w tej samej subskrypcji. Za pomocą witryny Azure portal lub programu PowerShell można przenieść magazyn usług odzyskiwania.

## <a name="supported-regions"></a>Obsługiwane regiony

Ruch zasobów dla magazynu Usług odzyskiwania jest obsługiwany w Australii Wschodniej, Australii Południowo-Wschodniej, Kanadzie Środkowej, Kanadzie Wschodniej, Azji Południowo-Wschodniej, Azji Wschodniej, Środkowo-Wschodniej, Północno-Środkowych Stanach Zjednoczonych, Wschodnich stanach USA, Wschodnich stanach USA2, południowo-środkowych stanach USA, zachodnio-środkowych stanach USA, Indie Środkowe, Indie Południowe, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa, Europa Północna, Europa Zachodnia, Republika Południowej Afryki Północna, Republika Południowej Afryki Zachód, Wielka Brytania Południowa i Wielka Brytania Zachodnia.

## <a name="unsupported-regions"></a>Nieobsługiwane regiony

Francja Środkowa, Francja Południowa, Niemcy Północno-Wschodnie, Niemcy Środkowe, US Gov Iowa, Chiny Północne, Chiny Północne2, Chiny Wschodnie, Chiny Wschodnie2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Wymagania wstępne dotyczące przenoszenia magazynu usług odzyskiwania

- Podczas przenoszenia przechowalni między grupami zasobów zarówno źródłowe, jak i docelowe grupy zasobów są zablokowane, uniemożliwiając operacje zapisu i usuwania. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Tylko subskrypcja administracyjna ma uprawnienia do przenoszenia przechowalni.
- W przypadku przenoszenia magazynu między subskrypcjami subskrypcja docelowa musi znajdować się w tej samej dzierżawie co subskrypcja źródłowa i jej stan powinien być włączony.
- Musisz mieć uprawnienia do wykonywania operacji zapisu w docelowej grupie zasobów.
- Przenoszenie przechowalni zmienia tylko grupę zasobów. Magazyn usług odzyskiwania będzie znajdować się w tej samej lokalizacji i nie można go zmienić.
- W jednym czasie można przenosić tylko jeden magazyn usług odzyskiwania dla jednego regionu.
- Jeśli maszyna wirtualna nie zostanie przesunięta z magazynem usług odzyskiwania w ramach subskrypcji lub do nowej grupy zasobów, bieżące punkty odzyskiwania maszyny Wirtualnej pozostaną nienaruszone w przechowalni, dopóki nie wygasną.
- Niezależnie od tego, czy maszyna wirtualna jest przenoszona z magazynem, czy nie, zawsze można przywrócić maszynę wirtualną z historii zachowanych kopii zapasowych w magazynie.
- Szyfrowanie dysków platformy Azure wymaga, aby magazyn kluczy i maszyny wirtualne znajdują się w tym samym regionie i subskrypcji platformy Azure.
- Aby przenieść maszynę wirtualną z dyskami zarządzanymi, zobacz ten [artykuł](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Opcje przenoszenia zasobów wdrożonych za pośrednictwem modelu klasycznego różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji, czy do nowej subskrypcji. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Zasady tworzenia kopii zapasowych zdefiniowane dla magazynu są zachowywane po przechowalniprzejmuje się między subskrypcjami lub do nowej grupy zasobów.
- Przenoszenie magazynu za pomocą usługi Azure Files, Azure File Sync lub SQL w maszynach wirtualnych IaaS w różnych subskrypcjach i grupach zasobów nie jest obsługiwane.
- Jeśli przeniesiesz magazyn zawierający dane kopii zapasowej maszyny Wirtualnej w ramach subskrypcji, musisz przenieść maszyny wirtualne do tej samej subskrypcji i użyć tej samej docelowej nazwy grupy zasobów maszyny Wirtualnej (jak w starej subskrypcji), aby kontynuować tworzenie kopii zapasowych.

> [!NOTE]
>
> Magazyny usług odzyskiwania skonfigurowane do używania z **usługą Azure Site Recovery** nie mogą się jeszcze przenieść. Jeśli skonfigurowano wszystkie maszyny wirtualne (Azure IaaS, Hyper-V, VMware) lub fizyczne maszyny do odzyskiwania po awarii przy użyciu **usługi Azure Site Recovery,** operacja przenoszenia zostanie zablokowana. Funkcja przenoszenia zasobów dla usługi site recovery nie jest jeszcze dostępna.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Przenoszenie magazynu usług odzyskiwania do innej grupy zasobów za pomocą witryny Azure portal

Aby przenieść magazyn usług odzyskiwania i skojarzone z nim zasoby do innej grupy zasobów

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Otwórz listę **magazynów usług odzyskiwania** i wybierz przechowalnię, którą chcesz przenieść. Po otwarciu pulpitu nawigacyjnego przechowalni jest on wyświetlany w sposób pokazany na poniższej ilustracji.

   ![Otwórz magazyn usługi Recover](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Jeśli nie widzisz informacji **o podstawowe informacje** o przechowalni, kliknij ikonę rozwijaną. Informacje o podstawowych elementach przechowalni powinny być teraz widoczne.

   ![Karta Informacje podstawowe](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przegląd przechowalni kliknij polecenie **Zmień** obok **grupy Zasobów**, aby otworzyć blok **Przenieś zasoby.**

   ![Zmienianie grupy zasobów](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. W bloku **Przenieś zasoby** dla wybranego przechowalni zaleca się przeniesienie opcjonalnych powiązanych zasobów, zaznaczając pole wyboru, jak pokazano na poniższej ilustracji.

   ![Przenieś subskrypcję](./media/backup-azure-move-recovery-services/move-resource.png)

5. Aby dodać docelową grupę zasobów, na liście rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub kliknij pozycję Utwórz nową opcję **grupy.**

   ![Tworzenie zasobu](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po dodaniu grupy zasobów upewnij **się, że rozumiem, że narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualę ich, aby użyć nowej opcji identyfikatorów zasobów,** a następnie kliknij przycisk **OK,** aby zakończyć przenoszenie przechowalni.

   ![Komunikat potwierdzający](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Przenoszenie magazynu usług odzyskiwania do innej subskrypcji za pomocą witryny Azure portal

Magazyn usług odzyskiwania i skojarzone z nim zasoby można przenieść do innej subskrypcji

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Otwórz listę magazynów usług odzyskiwania i wybierz przechowalnię, którą chcesz przenieść. Po otwarciu pulpitu nawigacyjnego przechowalni jest wyświetlany w następujący sposób.

    ![Otwórz magazyn usługi Recover](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Jeśli nie widzisz informacji **o podstawowe informacje** o przechowalni, kliknij ikonę rozwijaną. Informacje o podstawowych elementach przechowalni powinny być teraz widoczne.

    ![Karta Informacje podstawowe](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przegląd przechowalni kliknij **polecenie Zmień** obok **pozycji Subskrypcja**, aby otworzyć blok **Przenieś zasoby.**

   ![Zmień subskrypcję](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Wybierz zasoby, które mają zostać przeniesione, w tym miejscu zalecamy użycie opcji **Wybierz wszystko,** aby wybrać wszystkie wymienione zasoby opcjonalne.

   ![przenoszenie zasobu](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Wybierz subskrypcję docelową z listy rozwijanej **Subskrypcja,** na której chcesz przenieść magazyn.
6. Aby dodać docelową grupę zasobów, na liście rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub kliknij pozycję Utwórz nową opcję **grupy.**

   ![Dodaj subskrypcję](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Kliknij **przycisk Rozumiem, że narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualizuję ich, aby użyć nowej opcji identyfikatorów zasobów,** aby potwierdzić, a następnie kliknij przycisk **OK**.

> [!NOTE]
> Kopia zapasowa między subskrypcjami (magazyn RS i chronione maszyny wirtualne są w różnych subskrypcjach) nie jest obsługiwanym scenariuszem. Ponadto opcja nadmiarowości magazynu z lokalnego magazynu nadmiarowego (LRS) do globalnego magazynu nadmiarowego (GRS) i odwrotnie nie może być modyfikowana podczas operacji przenoszenia magazynu.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Przenoszenie magazynu usług odzyskiwania za pomocą programu PowerShell

Aby przenieść magazyn usług odzyskiwania do `Move-AzureRMResource` innej grupy zasobów, użyj polecenia cmdlet. `Move-AzureRMResource`wymaga nazwy zasobu i typu zasobu. Możesz uzyskać oba `Get-AzureRmRecoveryServicesVault` z polecenia cmdlet.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Aby przenieść zasoby do innej `-DestinationSubscriptionId` subskrypcji, należy dołączyć parametr.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po wykonaniu powyższych powyższych poleceń cmdlet zostaniesz poproszony o potwierdzenie, że chcesz przenieść określone zasoby. Wpisz **Y,** aby potwierdzić. Po pomyślnym sprawd opodarek, zasób przenosi.

## <a name="use-cli-to-move-recovery-services-vault"></a>Przenoszenie magazynu usług odzyskiwania za pomocą interfejsu wiersza polecenia

Aby przenieść magazyn usług odzyskiwania do innej grupy zasobów, użyj następującego polecenia cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Aby przejść do nowej subskrypcji, podaj `--destination-subscription-id` parametr.

## <a name="post-migration"></a>Zadania po migracji

1. Ustaw/sprawdź formanty dostępu dla grup zasobów.  
2. Funkcja raportowania i monitorowania kopii zapasowej musi być ponownie skonfigurowana dla stanowiska przechowalni, który zostanie zakończony. Poprzednia konfiguracja zostanie utracona podczas operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki

Można przenieść wiele różnych typów zasobów między grupami zasobów i subskrypcji.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Przenoszenie zasobów do nowej grupy lub subskrypcji).
