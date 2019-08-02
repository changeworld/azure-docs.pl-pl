---
title: 'Azure Backup: Tworzenie magazynów Recovery Services'
description: Tworzenie magazynów Recovery Services, w których są przechowywane kopie zapasowe i punkty odzyskiwania
ms.reviewer: sogup
author: dcurwin
manager: carmonm
keywords: Magazyn Recovery Services; Kopia zapasowa maszyny wirtualnej platformy Azure; Przywracanie maszyny wirtualnej platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: dacurwin
ms.openlocfilehash: d13aff40d735e98e7745358f8caddcd8cdc20727
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688560"
---
# <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn Recovery Services zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z chronionymi maszynami wirtualnymi.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/backup-create-rs-vault/click-all-services.png)

3. W oknie dialogowym **Wszystkie usługi** wprowadź frazę **Recovery Services**. Lista filtrów zasobów zgodnie z danymi wejściowymi. Na liście zasobów wybierz pozycję magazyny **Recovery Services**.

    ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Na pulpicie nawigacyjnym **Recovery Services magazynów** wybierz pozycję **Dodaj**.

    ![Dodawanie magazynu Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Zostanie otwarte okno dialogowe **magazyn Recovery Services** . Podaj wartości dla **nazwy**, **subskrypcji**, **grupy zasobów**i **lokalizacji**.

    ![Konfigurowanie magazynu Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: Wprowadź przyjazną nazwę, aby zidentyfikować magazyn. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa znaki, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i zawierać tylko litery, cyfry i łączniki.
   - **Subskrypcja**: Wybierz subskrypcję, która ma zostać użyta. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, Użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji, które są dostępne tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure.
   - **Grupa zasobów**: Użyj istniejącej grupy zasobów lub Utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącej**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** i wprowadź nazwę. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Azure Resource Manager przegląd](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Lokalizacja**: Wybierz region geograficzny magazynu. Aby utworzyć magazyn do ochrony maszyn wirtualnych, magazyn **musi** znajdować się w tym samym regionie co maszyny wirtualne.

      > [!IMPORTANT]
      > Jeśli nie masz pewności co do lokalizacji maszyny wirtualnej, Zamknij okno dialogowe. Przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w kilku regionach, Utwórz magazyn Recovery Services w każdym regionie. Utwórz magazyn w pierwszej lokalizacji, zanim utworzysz magazyn dla innej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn Recovery Services i usługa Azure Backup obsługują automatycznie.
      >
      >

5. Gdy wszystko będzie gotowe do utworzenia magazynu Recovery Services, wybierz pozycję **Utwórz**.

    ![Tworzenie magazynu Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu Recovery Services może chwilę potrwać. Monitoruj powiadomienia o stanie w obszarze **powiadomień** w prawym górnym rogu portalu. Po utworzeniu magazynu będzie on widoczny na liście magazynów Recovery Services. Jeśli Twój magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

     ![Odświeżanie listy magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Ustawianie nadmiarowości magazynu

Azure Backup automatycznie obsługuje magazyn dla magazynu. Należy określić sposób replikowania magazynu.

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn. W sekcji **Ustawienia** kliknij pozycję **Właściwości**.
2. W obszarze **Właściwości**, w obszarze **Konfiguracja kopii zapasowej**, kliknij przycisk **Aktualizuj**.

3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Zalecamy, aby Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, Kontynuuj, aby użyć domyślnego ustawienia geograficznie nadmiarowego.
   - Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
   - Dowiedz się [](../storage/common/storage-redundancy-grs.md) więcej o nadmiarowości geograficznym i [lokalnym](../storage/common/storage-redundancy-lrs.md) .

> [!NOTE]
> Przed skonfigurowaniem kopii zapasowych w magazynie należy zmienić **Typ replikacji magazynu** (lokalnie nadmiarowy lub geograficznie nadmiarowy) dla magazynu usługi Recovery Services. Po skonfigurowaniu kopii zapasowej opcja Modyfikuj jest wyłączona i nie można zmienić **typu replikacji magazynu**. 

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny Recovery Services.
[Dowiedz się więcej o](backup-azure-delete-vault.md) Usuń magazyny Recovery Services.
