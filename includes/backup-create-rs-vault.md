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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38730502"
---
## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
Magazyn usługi Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania, które zostały utworzone wraz z upływem czasu. Magazyn usługi Recovery Services zawiera także zasady tworzenia kopii zapasowych, które są skojarzone z chronionych maszyn wirtualnych.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do Twojej subskrypcji w [witryny Azure portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **wszystkich usług**.

    ![Wybierz opcję wszystkie usługi, w menu głównym](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. W oknie dialogowym z wszystkich usług, wpisz *usługi Recovery Services*. Po rozpoczęciu wpisywania, dane wejściowe filtrowanie listy zasobów. Gdy zostanie wyświetlony, wybierz **Magazyny usługi Recovery Services**.

    ![Wpisz wszystkie okna dialogowego usługi Recovery Services](./media/backup-create-rs-vault/all-services.png) <br/>

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.
4. Na **Magazyny usługi Recovery Services** menu, wybierz opcję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Magazyny usługi Recovery Services** zostanie otwarte menu. Monit o podanie informacji dla **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji**.

    ![Okienko "Magazyny usługi recovery Services"](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Wpisz nazwę, która zawiera co najmniej dwóch, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.
6. Aby uzyskać **subskrypcji**, wybierz subskrypcję, której chcesz użyć. Jeśli jesteś członkiem tylko jedną subskrypcję, że nazwa będzie wyświetlana. Jeśli nie masz pewności, której subskrypcji użyć, należy go przy użyciu domyślnego (lub sugerowane) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy pracy lub nauki jest skojarzony z wieloma subskrypcjami platformy Azure.
7. Aby uzyskać **grupy zasobów** można użyć istniejącej grupy zasobów lub Utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz **Użyj istniejącej**i kliknij przycisk menu rozwijanego. Aby utworzyć nową grupę zasobów, wybierz **Utwórz nową** i wpisz nazwę. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
8. Aby uzyskać **lokalizacji** wybierz region geograficzny magazynu. Podczas tworzenia magazynu, aby chronić maszyny wirtualne, magazyn *musi* należeć do tego samego regionu maszyn wirtualnych.

   > [!IMPORTANT]
   > Jeśli masz pewności co do lokalizacji, w której istnieje maszyna wirtualna, zamknij okno dialogowe tworzenia magazynu, a następnie przejdź do listy maszyn wirtualnych w portalu. Jeśli Twoje maszyny wirtualne znajdują się w wielu regionach, utwórz magazyn usługi Recovery Services w każdym regionie. Przed przejściem do następnej lokalizacji utwórz magazyn w pierwszej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn usługi Recovery Services i usługi Azure Backup określają, automatycznie.
   >
   >

9. Gdy wszystko jest gotowe do utworzenia magazynu usługi Recovery Services, kliknij przycisk **Utwórz**.

    ![Lista magazynów kopii zapasowych](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu Usług odzyskiwania może zająć trochę czasu. Monitoruj powiadomienia o stanie wyświetlane w sekcji powiadomienia (obszar prawym górnym rogu portalu). Po utworzeniu magazynu pojawi się na liście magazynów usługi Recovery Services. Jeśli nadal nie widzisz swojego magazynu, kliknij przycisk **Odśwież**.

     ![Lista magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)
