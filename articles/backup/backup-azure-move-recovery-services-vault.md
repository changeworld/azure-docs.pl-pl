---
title: Przenieś magazyn usługi Recovery Services między subskrypcjami platformy Azure lub do innej grupy zasobów
description: Instrukcje dotyczące przenoszenia usługi recovery services magazynu między subskrypcjami platformy azure i grup zasobów.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sogup
ms.openlocfilehash: 7745f986c6e9ba22258f51f9329444b8232762e1
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905770"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Przenoszenie magazynu usługi Recovery Services między subskrypcjami platformy Azure i grup zasobów (ograniczonej publicznej wersji zapoznawczej)

W tym artykule opisano sposób przenoszenia magazynu usługi Recovery Services, skonfigurowany dla usługi Azure Backup, między subskrypcjami platformy Azure lub do innej grupy zasobów w tej samej subskrypcji. Aby przenieść magazyn usługi Recovery Services, można użyć witryny Azure portal lub programu PowerShell.

> [!NOTE]
> Aby przenieść magazyn usługi Recovery Services i skojarzone z nią zasoby do innej grupy zasobów, należy najpierw [rejestrowanie subskrypcji źródłowej](#register-the-source-subscription-to-move-your-recovery-services-vault).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-moving-a-vault"></a>Wymagania wstępne dotyczące przenoszenia magazynu

- Podczas przenoszenia między grupami zasobów, zarówno źródłowa grupa zasobów, jak i docelowej grupy zasobów są zablokowane podczas operacji. Dopiero po zakończeniu przenoszenia, zapisu i usuwania działań są zablokowane na temat grup zasobów.
- Tylko administrator subskrypcji ma uprawnień do przeniesienia magazynu.
- Przenoszenie magazynu między subskrypcjami, subskrypcji docelowej musi istnieć w stanie włączonym i musi znajdować się w tej samej dzierżawie, co w przypadku subskrypcji źródłowej.
- Musi mieć uprawnienia do wykonywania operacji zapisu na docelową grupę zasobów.
- Nie można zmienić lokalizację magazynu usługi Recovery Services. Przenoszenie magazynu zmienia tylko grupę zasobów. Nowa grupa zasobów może być w innej lokalizacji, ale to nie ulega zmianie lokalizacji magazynu.
- Obecnie można przenieść jeden magazyn usługi Recovery Services, na region, w danym momencie.
- Maszyny Wirtualnej nie przenieść przy użyciu magazynu usługi Recovery Services, subskrypcji lub do nowej grupy zasobów, bieżących punktów odzyskiwania maszyn wirtualnych pozostają niezmienione w magazynie dopóki nie wygasną.
- Czy maszyna wirtualna zostanie przeniesiona z magazynem, czy nie, zawsze można przywrócić maszynę Wirtualną z zachowanej historii kopii zapasowych w magazynie.
-   Usługa Azure Disk Encryption wymaga, że maszyny wirtualne i usługi key vault, na których znajdują się w tym samym regionie platformy Azure i subskrypcji.
-   Aby przenieść maszynę wirtualną z dyskami zarządzanymi, zobacz ten [artykułu](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
-   Opcje przenoszenia zasobów wdrożonych za pośrednictwem klasycznego modelu różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji lub do nowej subskrypcji. Aby uzyskać więcej informacji, zobacz ten [artykułu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
-   Zasady tworzenia kopii zapasowych zdefiniowane dla magazynu zostaną zachowane, gdy magazyn zostanie przeniesiona w subskrypcjach lub do nowej grupy zasobów.
-   Obecnie nie można przenieść magazynów zawierających pliki Azure, usługi Azure File Sync lub SQL na maszynach wirtualnych IaaS w subskrypcji i grup zasobów. Wsparcie dla tych scenariuszy zostaną dodane w przyszłych wersjach.
-   Po przeniesieniu Magazyn zawierający dane kopii zapasowej maszyn wirtualnych, między subskrypcjami, możesz przenosić maszyny wirtualne z tą samą subskrypcją i Użyj tej samej grupie zasobów docelowych, aby kontynuować tworzenie kopii zapasowych.<br>

> [!NOTE]
>
> Magazyny usługi Recovery Services skonfigurowane do korzystania z **usługi Azure Site Recovery** nie można przenieść jeszcze. Jeśli skonfigurowano żadnych maszyn wirtualnych (IaaS platformy Azure, funkcji Hyper-V, VMware) lub komputerów fizycznych dla za pomocą odzyskiwania po awarii **usługi Azure Site Recovery**, operację przenoszenia, będą blokowane. Funkcji przenoszenia zasobów dla usługi Site Recovery nie jest jeszcze dostępna.

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>Zarejestruj subskrypcję źródła można przenieść magazynu usługi Recovery Services

Można zarejestrować subskrypcji źródłowej do **przenieść** magazynu usługi Recovery Services, uruchom następujące polecenia cmdlet z poziomu terminalu programu PowerShell:

1. Zaloguj się do swojego konta platformy Azure

   ```
   Connect-AzAccount
   ```

2. Wybierz subskrypcję, która ma zostać zarejestrowany

   ```
   Get-AzSubscription –SubscriptionName "Subscription Name" | Select-AzSubscription
   ```
3. Zarejestruj tę subskrypcję

   ```
   Register-AzProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
   ```

4. Uruchamianie polecenia

   ```
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

Poczekaj, aż subskrypcja złóż wniosek o umieszczenie przed rozpoczęciem operacji przenoszenia, przy użyciu witryny Azure portal lub programu PowerShell na 30 minut.

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Przenieś magazyn usługi Recovery Services do innej grupy zasobów za pomocą witryny Azure portal

Aby przenieść odzyskiwania usług magazynu i skojarzone z nią zasoby do innej grupy zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz listę **Magazyny usługi Recovery Services** i wybierz magazyn, którą chcesz przenieść. Po otwarciu pulpitu nawigacyjnego magazynu pojawia się, jak pokazano na poniższej ilustracji.

   ![Odzyskaj Otwórz magazyn usługi](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Jeśli nie widzisz **Essentials** informacje dla swojego magazynu, kliknij ikonę listy rozwijanej. Powinien zostać wyświetlony informacji podstawy dla magazynu.

   ![Informacje o karcie danych podstawowych](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przegląd magazynu, kliknij przycisk **zmienić** obok **grupy zasobów**, aby otworzyć **przenoszenia zasobów** bloku.

   ![Zmień grupę zasobów](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. W **przenoszenia zasobów** bloku wybranego magazynu zaleca przenieść opcjonalne powiązane zasoby, zaznaczając pole wyboru, jak pokazano na poniższej ilustracji.

   ![Przeniesienie subskrypcji](./media/backup-azure-move-recovery-services/move-resource.png)

5. Aby dodać docelową grupę zasobów w **grupy zasobów** listy rozwijanej wybierz istniejący zasób grupy, lub kliknij przycisk **Utwórz nową grupę** opcji.

   ![Utwórz zasób](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po dodaniu grupy zasobów, upewnij się, **zdaję sobie sprawę, że narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działać dopóki nie Zaktualizuję ich do używania nowych identyfikatorów zasobów** opcji, a następnie kliknij przycisk **OK** do ukończenia Przenoszenie magazynu.

   ![Komunikat z potwierdzeniem](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Przenieś magazyn usługi Recovery Services do innej subskrypcji za pomocą witryny Azure portal

Magazyn usługi Recovery Services i skojarzone z nią zasoby można przenieść do innej subskrypcji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwieranie listy magazynów usługi Recovery Services i wybierz magazyn, który chcesz przenieść. Po otwarciu pulpitu nawigacyjnego magazynu pojawia się, jak pokazano na poniższej ilustracji.

    ![Odzyskaj Otwórz magazyn usługi](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Jeśli nie widzisz **Essentials** informacje dla swojego magazynu, kliknij ikonę listy rozwijanej. Powinien zostać wyświetlony informacji podstawy dla magazynu.

    ![Informacje o karcie danych podstawowych](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przegląd magazynu, kliknij przycisk **zmienić** obok **subskrypcji**, aby otworzyć **przenoszenia zasobów** bloku.

   ![Zmień subskrypcję](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Wybierz zasoby do przeniesienia, w tym miejscu zalecamy używać **Zaznacz wszystko** możliwość dokonania wyboru z listy opcjonalnych zasobów.

   ![Przenoszenie zasobów](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Wybierz subskrypcję docelową z **subskrypcji** listy rozwijanej, którego magazynu do przeniesienia.
6. Aby dodać docelową grupę zasobów w **grupy zasobów** listy rozwijanej wybierz istniejący zasób grupy, lub kliknij przycisk **Utwórz nową grupę** opcji.

   ![Dodaj subskrypcję](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Kliknij przycisk **zdaję sobie sprawę, że narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działać dopóki nie Zaktualizuję ich do używania nowych identyfikatorów zasobów** opcję, aby potwierdzić, a następnie kliknij przycisk **OK**.

> [!NOTE]
> Krzyżowe subskrypcji kopii zapasowej (RS magazynu i chronionych maszyn wirtualnych znajdują się w różnych subskrypcjach) nie jest obsługiwanym scenariuszem. Ponadto nadmiarowości magazynu opcję lokalnie nadmiarowym (LRS) do globalnego magazyn nadmiarowy (GRS) i na odwrót nie może być modyfikowany podczas operacji przenoszenia magazynu.
>
>

## <a name="use-powershell-to-move-a-vault"></a>Przenoszenie magazynu przy użyciu programu PowerShell

Aby przenieść magazyn usługi Recovery Services do innej grupy zasobów, użyj `Move-AzResource` polecenia cmdlet. `Move-AzResource` wymaga nazwy zasobów i typu zasobu. Możesz uzyskać zarówno z poziomu `Get-AzRecoveryServicesVault` polecenia cmdlet.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Aby przenieść zasoby do innej subskrypcji, należy dołączyć `-DestinationSubscriptionId` parametru.

```
Move-AzResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po wykonaniu powyższych poleceń cmdlet, użytkownik jest proszony o upewnij się, że chcesz przenieść określonych zasobów. Typ **Y** o potwierdzenie. Po pomyślnej weryfikacji zasób zostanie przeniesiony.

## <a name="use-cli-to-move-a-vault"></a>Użyj interfejsu wiersza polecenia, aby przenieść Magazyn

Aby przenieść magazyn usługi Recovery Services do innej grupy zasobów, użyj następującego polecenia cmdlet:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Aby przejść do nowej subskrypcji, podać `--destination-subscription-id` parametru.

## <a name="post-migration"></a>Zadania po migracji

1. Musisz set/weryfikowania kontroli dostępu dla grup zasobów.  
2. Raportowanie kopii zapasowych i funkcja monitorowania należy skonfigurować ponownie dla wpisu magazynu, w których przeniesienie kończy. Poprzedniej konfiguracji zostaną utracone podczas operacji przenoszenia.



## <a name="next-steps"></a>Kolejne kroki

Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcji.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Przenoszenie zasobów do nowej grupy lub subskrypcji).
