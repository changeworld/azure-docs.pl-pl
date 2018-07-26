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
ms.openlocfilehash: 0ff9bf829e2dbe1bca078360ccded94bad63d9a6
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249465"
---
## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
Magazyn usługi Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone wraz z upływem czasu. Magazyn usługi Recovery Services zawiera także zasady tworzenia kopii zapasowych, które są skojarzone z chronionych maszyn wirtualnych.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do Twojej subskrypcji w [witryny Azure portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz **wszystkich usług**.

    ![Wybierz wszystkie usługi](./media/backup-create-rs-vault/click-all-services.png)

3. W **wszystkich usług** okna dialogowego wprowadź **usługi Recovery Services**. Filtruje listę zasobów zgodnie z dane wejściowe. Na liście zasobów wybierz **Magazyny usługi Recovery Services**.

    ![Wprowadź i wybierz pozycję magazynów usługi Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.
    
4. Na **Magazyny usługi Recovery Services** pulpitu nawigacyjnego, wybierz opcję **Dodaj**.

    ![Dodaj magazyn usługi Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Magazyn usługi Recovery Services** zostanie otwarte okno dialogowe. Podaj wartości dla **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji**.

    ![Konfigurowanie magazynu usługi Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: Wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa, ale nie więcej niż 50 znaków. Nazwa musi zaczynać się literą i zawierać tylko litery, cyfry i łączniki.
   - **Subskrypcja**: Wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jedną subskrypcję, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, należy użyć subskrypcji domyślna (zalecane). Istnieje wiele opcji tylko wtedy, gdy pracy lub konto służbowe jest skojarzone z więcej niż jedną subskrypcję platformy Azure.
   - **Grupa zasobów**: Użyj istniejącej grupy zasobów lub Utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz **Użyj istniejącej**, a następnie wybierz zasób w polu listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz **Utwórz nową** i wprowadź nazwę. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
   - **Lokalizacja**: Wybierz region geograficzny magazynu. Aby utworzyć magazyn chronić maszyny wirtualne, magazyn **musi** należeć do tego samego regionu maszyn wirtualnych.

      > [!IMPORTANT]
      > Jeśli nie masz pewności lokalizacji maszyny Wirtualnej, należy zamknąć okno dialogowe. Przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w wielu regionach, utwórz magazyn usługi Recovery Services w każdym regionie. Utwórz magazyn w pierwszej lokalizacji, przed przystąpieniem do tworzenia magazynu pod kątem innej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn usługi Recovery Services i usługi Azure Backup określają, automatycznie.
      >
      >

5. Gdy wszystko będzie gotowe utworzyć magazyn usługi Recovery Services, wybierz pozycję **Utwórz**.

    ![Utwórz magazyn usługi Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Może upłynąć trochę czasu, aby utworzyć magazyn usługi Recovery Services. Monitoruj powiadomienia o stanie wyświetlane w **powiadomienia** obszar w prawym górnym rogu portalu. Po utworzeniu magazynu jest widoczna na liście magazynów usługi Recovery Services. Jeśli nie widzisz swojego magazynu wybierz **Odśwież**.

     ![Odśwież listę magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)
