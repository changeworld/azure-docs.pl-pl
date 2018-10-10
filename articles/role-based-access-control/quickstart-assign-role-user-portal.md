---
title: Szybki start — udzielanie dostępu użytkownikowi za pomocą kontroli RBAC i witryny Azure Portal | Microsoft Docs
description: Użyj kontroli dostępu na podstawie ról (RBAC), aby przyznać uprawnienia użytkownikowi, przypisując rolę w witrynie Azure Portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 74ecca671409b6e163bc0db29d66167d240b645c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092526"
---
# <a name="quickstart-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Szybki start: udzielanie dostępu użytkownikowi za pomocą kontroli RBAC i witryny Azure Portal

Kontrola dostępu na podstawie ról (RBAC, Role Based Access Control) to sposób zarządzania dostępem do zasobów na platformie Azure. W tym przewodniku Szybki start udzielisz użytkownikowi praw dostępu do tworzenia maszyn wirtualnych i zarządzania nimi w grupie zasobów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz pozycję **Dodaj**, aby otworzyć blok **Grupa zasobów**.

   ![Dodawanie nowej grupy zasobów](./media/quickstart-assign-role-user-portal/resource-group.png)

1. W polu **Nazwa grupy zasobów** wprowadź ciąg **rbac-quickstart-resource-group**.

1. Wybierz subskrypcję i lokalizację.

1. Wybierz pozycję **Utwórz**, aby utworzyć grupę zasobów.

1. Wybierz pozycję **Odśwież**, aby odświeżyć listę grup zasobów.

   Nowa grupa zasobów zostanie wyświetlona na liście grup zasobów.

   ![Lista grup zasobów](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udzielanie dostępu

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli.

1. Na liście **grup zasobów** wybierz nową grupę zasobów **rbac-quickstart-resource-group**.

1. Wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)**, aby wyświetlić bieżącą listę przypisań ról.

   ![Blok Kontrola dostępu (IAM) dla grupy zasobów](./media/quickstart-assign-role-user-portal/access-control.png)

1. Wybierz pozycję **Dodaj**, aby otworzyć okienko **Dodaj uprawnienia**.

   Jeśli nie masz uprawnień do przypisywania ról, nie zobaczysz opcji **Dodaj**.

   ![Okienko dodawania uprawnień](./media/quickstart-assign-role-user-portal/add-permissions.png)

1. Z listy rozwijanej **Rola** wybierz pozycję **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz siebie lub innego użytkownika.

1. Wybierz pozycję **Zapisz**, aby utworzyć przypisanie roli.

   Po kilku chwilach użytkownik zostanie przypisany do roli Współautor maszyny wirtualnej w zakresie grupy zasobów rbac-quickstart-resource-group.

   ![Przypisanie roli Współautor maszyny wirtualnej](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Usuwanie dostępu

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli.

1. Na liście przypisań ról dodaj znacznik wyboru obok użytkownika z rolą Współautor maszyny wirtualnej.

1. Wybierz pozycję **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. W komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

## <a name="clean-up"></a>Czyszczenie

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz pozycję **rbac-quickstart-resource-group**, aby otworzyć grupę zasobów.

1. Wybierz pozycję **Usuń grupę zasobów**, aby usunąć grupę zasobów.

   ![Usuwanie grupy zasobów](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. W bloku **Czy na pewno chcesz usunąć** wpisz nazwę grupy zasobów: **rbac-quickstart-resource-group**.

1. Wybierz pozycję **Usuń**, aby usunąć grupę zasobów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: udzielanie dostępu użytkownikowi za pomocą kontroli RBAC i programu PowerShell](tutorial-role-assignments-user-powershell.md)

