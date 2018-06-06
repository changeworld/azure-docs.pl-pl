---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664959"
---
## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
Magazyn usług odzyskiwania jest jednostka, która przechowuje kopie zapasowe i punkty odzyskiwania, które zostały utworzone w czasie. Magazyn usług odzyskiwania zawiera również zasady tworzenia kopii zapasowej, które są skojarzone z chronionych maszyn wirtualnych.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do subskrypcji w [portalu Azure](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **wszystkie usługi**.

    ![Wybierz opcję wszystkie usługi, w menu głównym](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. W oknie dialogowym z wszystkich usług, wpisz *usług odzyskiwania*. Po rozpoczęciu wpisywania, dane wejściowe filtruje listę zasobów. Gdy zostanie wyświetlony, wybierz **Magazyny usług odzyskiwania**.

    ![Typ usługi odzyskiwania w oknie dialogowym wszystkie usługi](./media/backup-create-rs-vault/all-services.png) <br/>

    Zostanie wyświetlona lista magazynów usług odzyskiwania w ramach subskrypcji.
4. Na **Magazyny usług odzyskiwania** menu, wybierz opcję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Magazyny usług odzyskiwania** zostanie otwarte menu. Monituje o podanie informacji o **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji**.

    ![Okienko "Magazyny usług odzyskiwania"](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Wpisz nazwę, która zawiera co najmniej dwóch, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.
6. Aby uzyskać **subskrypcji**, wybierz subskrypcję, którego chcesz użyć. Jeśli jesteś członkiem tylko jednej subskrypcji, pojawi się tej nazwy. Jeśli nie masz pewności, które subskrypcji do użycia, go przy użyciu domyślnego (lub sugerowane) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy pracy lub konto służbowe jest skojarzony z wieloma subskrypcjami platformy Azure.
7. Aby uzyskać **grupy zasobów** można użyć istniejącej grupy zasobów lub Utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz **Użyj istniejącego**i kliknij przycisk menu rozwijanego. Aby utworzyć nową grupę zasobów, wybierz **Utwórz nowy** i wpisz nazwę. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
8. Aby uzyskać **lokalizacji** wybierz region geograficzny magazynu. Podczas tworzenia magazynu do ochrony maszyn wirtualnych, magazyn *musi* znajdować się w tym samym regionie co maszyn wirtualnych.

   > [!IMPORTANT]
   > Jeśli masz pewności co do lokalizacji, w której istnieje maszyna wirtualna, zamknij okno dialogowe Tworzenie magazynu i przejdź do listy maszyn wirtualnych w portalu. Jeśli Twoje maszyny wirtualne znajdują się w wielu regionach, utwórz magazyn usługi Recovery Services w każdym regionie. Przed przejściem do następnej lokalizacji utwórz magazyn w pierwszej lokalizacji. Jest niepotrzebna do określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn usług odzyskiwania usługi Azure Backup obsługi i który automatycznie.
   >
   >

9. Gdy wszystko będzie gotowe utworzyć magazyn usług odzyskiwania, kliknij przycisk **Utwórz**.

    ![Lista magazynów kopii zapasowych](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu Usług odzyskiwania może zająć trochę czasu. Monitoruje powiadomienia o stanie w sekcji powiadomienia (obszar prawym górnym portalu). Po utworzeniu magazynu zostanie wyświetlona lista magazynów usług odzyskiwania. Jeśli nadal nie widać magazynu, kliknij przycisk **Odśwież**.

     ![Lista magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)
