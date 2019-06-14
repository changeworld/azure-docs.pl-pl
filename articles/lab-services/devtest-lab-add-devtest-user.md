---
title: Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs przy użyciu witryny Azure portal lub programu PowerShell
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311423"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Dostęp w usłudze Azure DevTest Labs jest kontrolowany przez [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). Korzystając z modelu RBAC można segregowanie zadań w ramach zespołu do *role* gdzie możesz udzielać uprawnień dostępu, które są niezbędne użytkownikom do wykonywania swoich zadań. Trzy z tych ról RBAC są *właściciela*, *użytkownik usługi DevTest Labs*, i *Współautor*. W tym artykule dowiesz się, jakie akcje mogą być wykonywane w każdym z trzech głównych ról RBAC. W tym miejscu dowiesz się, jak dodać użytkowników do laboratorium — zarówno w portalu, jak i za pomocą skryptu programu PowerShell oraz sposób dodawania użytkowników na poziomie subskrypcji.

## <a name="actions-that-can-be-performed-in-each-role"></a>Akcje, które mogą być wykonywane w każdej roli
Istnieją trzy główne role, przypisać użytkownika:

* Właściciel
* Użytkownik usługi DevTest Labs
* Współautor

W poniższej tabeli przedstawiono akcje, które mogą być wykonywane przez użytkowników w każdej z tych ról:

| **Akcje użytkowników w tej roli mogą wykonywać.** | **Użytkownik usługi DevTest Labs** | **Właściciel** | **Współautor** |
| --- | --- | --- | --- |
| **Zadania laboratorium** | | | |
| Dodawanie użytkowników do laboratorium |Nie |Yes |Nie |
| Aktualizowanie ustawień kosztów |Nie |Yes |Tak |
| **Zadania podstawowej maszyny Wirtualnej** | | | |
| Dodawanie i usuwanie niestandardowych obrazów |Nie |Yes |Tak |
| Dodawanie, aktualizowanie i usuwanie formuły |Yes |Yes |Tak |
| Lista dozwolonych Azure Marketplace obrazów |Nie |Yes |Yes |
| **Zadań maszyn wirtualnych** | | | |
| Tworzenie maszyn wirtualnych |Yes |Yes |Tak |
| Uruchamianie, zatrzymywanie i usuwanie maszyn wirtualnych |Tylko maszyny wirtualne utworzone przez użytkownika |Yes |Yes |
| Zaktualizuj zasady maszyn wirtualnych |Nie |Yes |Tak |
| Dodawanie/usuwanie dysków z danymi z maszyn wirtualnych |Tylko maszyny wirtualne utworzone przez użytkownika |Yes |Tak |
| **Zadania artefaktu** | | | |
| Dodawanie i usuwanie repozytoriów artefaktu |Nie |Yes |Yes |
| Zastosuj artefaktów |Yes |Yes |Tak |

> [!NOTE]
> Gdy użytkownik tworzy Maszynę wirtualną, ten użytkownik jest automatycznie przypisywana do **właściciela** roli utworzonej maszyny Wirtualnej.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Dodawanie właściciela lub użytkowników na poziomie laboratorium
Na poziomie laboratorium za pośrednictwem witryny Azure portal można dodać właścicieli i użytkowników. Może nim być użytkownik zewnętrzny przy użyciu prawidłowego [konta Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Poniższe kroki ułatwiają dodanie właściciela lub użytkowników do laboratorium Azure DevTest Labs:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.
4. W bloku laboratorium wybierz **konfiguracji i zasad**. 
5. Na **konfiguracji i zasad** wybierz opcję **kontrola dostępu (IAM)** z menu po lewej stronie. 
6. Wybierz **Dodaj przypisanie roli** na pasku narzędzi, aby dodać użytkownika do roli.
1. W **Dodaj uprawnienia** okna, wykonaj następujące czynności: 
    1. Wybierz rolę (na przykład: DevTest Labs User). Sekcja [akcje, które mogą być wykonywane w każdej roli](#actions-that-can-be-performed-in-each-role) wymieniono różne akcje, które mogą być wykonywane przez użytkowników w roli właściciel, użytkownik usługi DevTest i współautor.
    2. Wybierz użytkownika, które mają zostać dodane do roli. 
    3. Wybierz pozycję **Zapisz**. 
11. Po powrocie do **użytkowników** bloku użytkownik został dodany.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Dodawanie użytkownika zewnętrznego do laboratorium przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Oprócz dodawania użytkowników w witrynie Azure portal, można dodać użytkownika zewnętrznego do laboratorium przy użyciu skryptu programu PowerShell. W poniższym przykładzie zmodyfikuj wartości parametrów w obszarze **wartości w celu zmiany** komentarz.
Możesz pobrać `subscriptionId`, `labResourceGroup`, i `labName` wartości z bloku laboratorium w witrynie Azure portal.

> [!NOTE]
> Przykładowy skrypt założono, że określony użytkownik został dodany do usługi Active Directory jako Gość i zakończy się niepowodzeniem, jeśli nie jest wymagane. Aby dodać użytkownika nie w usłudze Active Directory do laboratorium, należy użyć witryny Azure portal do przypisania do roli użytkownika, jak pokazano w sekcji [dodać właściciela lub użytkownika na poziomie laboratorium](#add-an-owner-or-user-at-the-lab-level).   
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

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Dodawanie właściciela lub użytkowników na poziomie subskrypcji
Uprawnienia platformy Azure są propagowane w zakresie nadrzędnym zakresem podrzędnych na platformie Azure. W związku z tym właścicieli subskrypcji platformy Azure, która zawiera labs są wykonywane automatycznie właścicieli tych laboratoriów. Właścicielem również maszyny wirtualne i inne zasoby utworzone przez użytkowników laboratorium i usługa Azure DevTest Labs. 

Możesz dodać dodatkowych właścicieli do laboratorium za pomocą bloku laboratorium w [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Dodano właściciela zakres administracji jest jednak bardziej wąskie niż zakres właścicielem subskrypcji. Na przykład dodano właścicieli ma pełny dostęp do niektórych zasobów, które są tworzone w ramach subskrypcji w usłudze DevTest Labs. 

Aby dodać właściciela do subskrypcji platformy Azure, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **subskrypcje** z listy.
3. Wybierz odpowiednią subskrypcję.
4. Wybierz **dostępu** ikony. 
   
    ![Dostęp użytkowników](./media/devtest-lab-add-devtest-user/access-users.png)
5. Na **użytkowników** bloku wybierz **Dodaj**.
   
    ![Dodawanie użytkownika](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Na **wybierz rolę** bloku wybierz **właściciela**.
7. Na **dodawania użytkowników** bloku, wprowadź adres e-mail lub nazwa użytkownika, które chcesz dodać jako właściciela. Jeśli użytkownik nie zostanie znaleziony, otrzymasz komunikat o błędzie wyjaśniający problem. Jeśli użytkownik zostanie znaleziony, ten użytkownik znajduje się w obszarze **użytkownika** pola tekstowego.
8. Wybierz nazwę znajduje się użytkownik.
9. Wybierz **wybierz**.
10. Wybierz **OK** zamknąć **Dodaj dostęp** bloku.
11. Po powrocie do **użytkowników** bloku użytkownik został dodany jako właściciela. Ten użytkownik jest obecnie właścicielem dowolnego labs utworzonych w ramach tej subskrypcji, a ten sposób jest w stanie wykonywać zadania właściciela. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

