---
title: Kontrola dostępu oparta na rolach w witrynie Azure Portal | Microsoft Docs
description: Rozpocznij zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach w witrynie Azure Portal. Przypisz uprawnienia do swoich zasobów za pomocą przypisań ról.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 4ac7fda78f456a233c8dba90a6a50e19774991df
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203657"
---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>Korzystanie z kontroli dostępu opartej na rolach do zarządzania dostępem do zasobów subskrypcji platformy Azure
> [!div class="op_single_selector"]
> * [Zarządzanie dostępem użytkowników lub grup](role-assignments-users.md)
> * [Zarządzanie dostępem do zasobów](role-assignments-portal.md)

Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań. Ten artykuł ułatwia rozpoczęcie pracy z kontrolą dostępu opartą na rolach w witrynie Azure Portal. Jeśli chcesz uzyskać więcej szczegółowych informacji na temat sposobu, w jaki RBAC ułatwia zarządzanie dostępem, zobacz [Co to jest kontrola dostępu oparta na rolach](overview.md).

W ramach każdej subskrypcji można przyznać maksymalnie 2000 przypisań ról. 

## <a name="view-access"></a>Wyświetlanie dostępu
Z poziomu głównego bloku zasobu, grupy zasobów lub subskrypcji w witrynie [Azure Portal](https://portal.azure.com) można zobaczyć, kto ma dostęp do tego zasobu, grupy zasobów lub subskrypcji. Na przykład jeśli chcesz zobaczyć, kto ma dostęp do jednej z grup zasobów:

1. Wybierz pozycję **Grupy zasobów** na pasku nawigacyjnym po lewej stronie.  
    ![Grupy zasobów — ikona](./media/role-assignments-portal/resourcegroups_icon.png)
2. Wybierz nazwę grupy zasobów z bloku **Grupy zasobów**.
3. Z menu po lewej stronie wybierz opcję **Kontrola dostępu (IAM)**.  
4. W bloku Kontrola dostępu znajduje się lista wszystkich użytkowników, grup i aplikacji, którym został udzielony dostęp do grupy zasobów.  
   
    ![Blok użytkowników — dostęp dziedziczony a przypisany (zrzut ekranu)](./media/role-assignments-portal/view-access.png)

Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisywany specjalnie do grupy zasobów albo dziedziczony z przypisania do subskrypcji nadrzędnej.

> [!NOTE]
> Klasyczni administratorzy i współadministratorzy są traktowani jako właściciele subskrypcji w nowym modelu RBAC.

## <a name="add-access"></a>Dodawanie dostępu
Dostęp udzielany jest w ramach zasobu, grupy zasobów lub subskrypcji, która jest zakresem przypisania roli.

1. Kliknij przycisk **Dodaj** w bloku Kontrola dostępu.  
2. W bloku **Wybierz rolę** wybierz rolę, którą chcesz przypisać.
3. Wybierz użytkownika, grupę lub aplikację w katalogu, którym chcesz udzielić dostępu. Możesz przeszukiwać katalog przy użyciu nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.  
   
    ![Blok dodawania użytkowników — zrzut ekranu wyszukiwania](./media/role-assignments-portal/grant-access2.png)
4. Wybierz przycisk **OK**, aby utworzyć przypisanie. Wyskakujące okienko **Dodawanie użytkownika** śledzi postęp procesu.  
    ![Pasek postępu dodawania użytkownika — zrzut ekranu](./media/role-assignments-portal/addinguser_popup.png)

Po pomyślnym dodaniu przypisania roli będzie ono wyświetlane w bloku **Użytkownicy**.

## <a name="remove-access"></a>Usuwanie dostępu
1. Umieść kursor na nazwie przypisania, które chcesz usunąć. Obok nazwy pojawi się pole wyboru.
2. Użyj pól wyboru, aby wybrać co najmniej jedno przypisanie roli.
2. Wybierz pozycję **Usuń**.  
3. Wybierz pozycję **Tak**, aby potwierdzić usunięcie.

Przypisań dziedziczonych nie można usunąć. Aby usunąć odziedziczone przypisanie, należy to zrobić w zakresie, w którym je utworzono. W kolumnie **Zakres** obok pola **Dziedziczone** znajduje się link umożliwiający przejście do zasobów, w ramach których ta rola została przypisana. Przejdź do zasobu wskazanego w tym miejscu, aby usunąć przypisanie roli.

![Blok Użytkownicy — dostęp dziedziczony wyłącza przycisk usuwania (zrzut ekranu)](./media/role-assignments-portal/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Inne narzędzia do zarządzania dostępem
Za pomocą poleceń Azure RBAC można przypisywać role i zarządzać dostępem w narzędziach innych niż witryna Azure Portal.  Skorzystaj z linków, aby dowiedzieć się więcej o wymaganiach wstępnych i zacząć korzystać z poleceń Azure RBAC.

* [Azure PowerShell](role-assignments-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](role-assignments-cli.md)
* [Interfejs API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Następne kroki
* [Tworzenie raportu historii zmian dostępu](change-history-report.md)
* Zobacz [Wbudowane role RBAC](built-in-roles.md)
* Definiowanie własnych [niestandardowych ról dla kontroli dostępu opartej na rolach na platformie Azure](custom-roles.md)

