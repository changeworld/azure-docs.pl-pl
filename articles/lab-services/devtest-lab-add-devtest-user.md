---
title: Dodawanie właścicieli i użytkowników w laboratoriach devtest platformy Azure| Dokumenty firmy Microsoft
description: Dodawanie właścicieli i użytkowników w laboratoriach devtest platformy Azure przy użyciu portalu Azure lub programu PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284280"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Dodawanie właścicieli i użytkowników w laboratoriach devtest platformy Azure
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Program Access w laboratoriach DevTest w usłudze Azure jest kontrolowany przez [kontrolę dostępu opartą na rolach platformy Azure (RBAC).](../role-based-access-control/overview.md) Za pomocą RBAC, można segregować obowiązki w zespole do *ról,* gdzie można przyznać tylko ilość dostępu niezbędną do wykonywania zadań. Trzy z tych ról RBAC to *Właściciel,* *DevTest Labs User*i *Contributor*. W tym artykule dowiesz się, jakie akcje można wykonać w każdej z trzech głównych ról RBAC. W tym miejscu dowiesz się, jak dodawać użytkowników do laboratorium — zarówno za pośrednictwem portalu, jak i za pośrednictwem skryptu programu PowerShell oraz jak dodawać użytkowników na poziomie subskrypcji.

## <a name="actions-that-can-be-performed-in-each-role"></a>Akcje, które można wykonać w każdej roli
Istnieją trzy główne role, które można przypisać użytkownikowi:

* Właściciel
* Użytkownik DevTest Labs
* Współautor

W poniższej tabeli przedstawiono akcje, które mogą być wykonywane przez użytkowników w każdej z tych ról:

| **Akcje, które użytkownicy w tej roli mogą wykonywać** | **Użytkownik DevTest Labs** | **Właściciel** | **Współautor** |
| --- | --- | --- | --- |
| **Zadania laboratoryjne** | | | |
| Dodawanie użytkowników do laboratorium |Nie |Tak |Nie |
| Aktualizowanie ustawień kosztów |Nie |Tak |Tak |
| **Zadania podstawowe maszyny Wirtualnej** | | | |
| Dodawanie i usuwanie obrazów niestandardowych |Nie |Tak |Tak |
| Dodawanie, aktualizowanie i usuwanie formuł |Tak |Tak |Tak |
| Obrazy portalu Azure Marketplace z białą listą |Nie |Tak |Tak |
| **Zadania maszyny Wirtualnej** | | | |
| Tworzenie maszyn wirtualnych |Tak |Tak |Tak |
| Uruchamianie, zatrzymywania i usuwanie maszyn wirtualnych |Tylko maszyny wirtualne utworzone przez użytkownika |Tak |Tak |
| Aktualizowanie zasad maszyn wirtualnych |Nie |Tak |Tak |
| Dodawanie/usuwanie dysków z danymi do/z maszyn wirtualnych |Tylko maszyny wirtualne utworzone przez użytkownika |Tak |Tak |
| **Zadania artefaktów** | | | |
| Dodawanie i usuwanie repozytoriów artefaktów |Nie |Tak |Tak |
| Stosowanie artefaktów |Tak |Tak |Tak |

> [!NOTE]
> Gdy użytkownik tworzy maszynę wirtualną, ten użytkownik jest automatycznie przypisywany do roli **właściciela** utworzonej maszyny Wirtualnej.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Dodawanie właściciela lub użytkownika na poziomie laboratorium
Właściciele i użytkownicy mogą być dodawane na poziomie laboratorium za pośrednictwem witryny Azure portal. Użytkownik może być użytkownikiem zewnętrznym z prawidłowym [kontem Microsoft (MSA).](devtest-lab-faq.md#what-is-a-microsoft-account)
Poniższe kroki prowadzą użytkownika przez proces dodawania właściciela lub użytkownika do laboratorium w laboratorium usługi Azure DevTest Labs:

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz żądane laboratorium.
4. Na bloku laboratorium wybierz **pozycję Konfiguracja i zasady**. 
5. Na stronie **Konfiguracja i zasady** wybierz pozycję **Kontrola dostępu (IAM)** z menu po lewej stronie. 
6. Wybierz **pozycję Dodaj przypisanie roli** na pasku narzędzi, aby dodać użytkownika do roli.
1. W oknie **Dodawanie uprawnień** wykonaj następujące czynności: 
    1. Wybierz rolę (na przykład: DevTest Labs User). Sekcja [Akcje, które mogą być wykonywane w każdej roli](#actions-that-can-be-performed-in-each-role) zawiera listę różnych akcji, które mogą być wykonywane przez użytkowników w właściciela, DevTest użytkownika i współautora ról.
    2. Wybierz użytkownika, który ma zostać dodany do roli. 
    3. Wybierz **pozycję Zapisz**. 
11. Po powrocie do **users** bloku, użytkownik został dodany.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Dodawanie użytkownika zewnętrznego do laboratorium przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Oprócz dodawania użytkowników w witrynie Azure portal, można dodać użytkownika zewnętrznego do laboratorium przy użyciu skryptu programu PowerShell. W poniższym przykładzie zmodyfikuj wartości parametrów w obszarze **Wartości, aby zmienić** komentarz.
Można pobrać `subscriptionId`, `labResourceGroup`i `labName` wartości z bloku laboratorium w witrynie Azure portal.

> [!NOTE]
> Przykładowy skrypt zakłada, że określony użytkownik został dodany jako gość do usługi Active Directory i zakończy się niepowodzeniem, jeśli tak nie jest. Aby dodać użytkownika nie w usłudze Active Directory do laboratorium, użyj portalu Azure, aby przypisać użytkownika do roli, jak pokazano w [sekcji, Dodaj właściciela lub użytkownika na poziomie laboratorium](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Dodawanie właściciela lub użytkownika na poziomie subskrypcji
Uprawnienia platformy Azure są propagowane z zakresu nadrzędnego do zakresu podrzędnego na platformie Azure. W związku z tym właściciele subskrypcji platformy Azure, która zawiera laboratoria są automatycznie właścicielami tych laboratoriów. Są również właścicielami maszyn wirtualnych i innych zasobów utworzonych przez użytkowników laboratorium i usługi Azure DevTest Labs. 

Dodatkowych właścicieli można dodać do laboratorium za pomocą bloku laboratorium w [witrynie Azure portal.](https://go.microsoft.com/fwlink/p/?LinkID=525040) Jednak zakres administracji dodanego właściciela jest węższy niż zakres właściciela subskrypcji. Na przykład dodani właściciele nie mają pełnego dostępu do niektórych zasobów, które są tworzone w ramach subskrypcji przez usługę DevTest Labs. 

Aby dodać właściciela do subskrypcji platformy Azure, wykonaj następujące kroki:

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **Subskrypcje.**
3. Wybierz żądaną subskrypcję.
4. Wybierz **ikonę programu Access.** 
   
    ![Dostęp do użytkowników](./media/devtest-lab-add-devtest-user/access-users.png)
5. Na bloku **Użytkownicy** wybierz pozycję **Dodaj**.
   
    ![Dodawanie użytkownika](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Na bloku **Wybierz rolę** wybierz pozycję **Właściciel**.
7. W bloku **Dodaj użytkowników** wprowadź adres e-mail lub nazwę użytkownika, którego chcesz dodać jako właściciela. Jeśli nie można znaleźć użytkownika, zostanie wyświetlony komunikat o błędzie wyjaśniający problem. Jeśli użytkownik zostanie znaleziony, ten użytkownik znajduje się na liście w polu tekstowym **Użytkownik.**
8. Wybierz nazwę użytkownika znajdującego się.
9. Wybierz przycisk **Wybierz**.
10. Wybierz **przycisk OK,** aby zamknąć blok **Dodaj dostęp.**
11. Po powrocie do **users** bloku, użytkownik został dodany jako właściciel. Ten użytkownik jest teraz właścicielem wszystkich laboratoriów utworzonych w ramach tej subskrypcji, a tym samym jest w stanie wykonywać zadania właściciela. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

