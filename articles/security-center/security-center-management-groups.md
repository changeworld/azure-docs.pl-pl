---
title: Uzyskaj wgląd w całej dzierżawie usługi Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o uzyskiwaniu wglądu w całej dzierżawie w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: terrylan
ms.openlocfilehash: 800ec83b3599dba716e7a4a015b9b8c1745a0975
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144571"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Uzyskaj wgląd w całej dzierżawie usługi Azure Security Center
Ten artykuł pomaga Ci rozpocząć pracę, wykonując kilka akcji, które zmaksymalizować korzyści, jakie oferuje usługa Azure Security Center. Wykonanie tych akcji umożliwia wgląd na wszystkich subskrypcji platformy Azure, które są połączone z dzierżawą usługi Azure Active Directory i skutecznie zarządzać poziom bezpieczeństwa w organizacji na dużą skalę, stosując zasady zabezpieczeń w wielu Subskrypcje w sposób aggregative.

## <a name="management-groups"></a>Grupy zarządzania
Grupy zarządzania systemu Azure umożliwia wydajne zarządzanie dostępem, zasad i raportowanie na temat grup subskrypcji, jak również skutecznie zarządzać całą standardami platformy Azure, wykonując czynności w głównej grupie zarządzania. Każda dzierżawa usługi Azure AD, otrzymuje pojedynczej najwyższego poziomu grupy zarządzania o nazwie głównej grupy zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Ta grupa umożliwia globalnych zasad i przypisania RBAC, które mają być stosowane na poziomie katalogu. 

Głównej grupy zarządzania jest tworzona automatycznie po wykonaniu dowolnej z następujących czynności: 
1. Zgoda na korzystanie z grup zarządzania systemu Azure, przechodząc do **grup zarządzania** w [witryny Azure portal](https://portal.azure.com).
2. Utwórz grupę zarządzania za pośrednictwem wywołania interfejsu API.
3. Utwórz grupę zarządzania przy użyciu programu PowerShell.

Aby uzyskać szczegółowe omówienie grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../azure-resource-manager/management-groups-overview.md) artykułu.

## <a name="create-a-management-group-in-the-azure-portal"></a>Tworzenie grupy zarządzania w witrynie Azure portal
Można organizować subskrypcji do grup zarządzania i zastosowania zasad zarządzania do grupy zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady zastosowane do tej grupy zarządzania. Podczas gdy grupy zarządzania nie są wymagane do dołączania usługi Security Center, zdecydowanie zaleca się utworzenie co najmniej jednej grupy zarządzania, dzięki utworzeniu głównej grupy zarządzania. Po utworzeniu grupy wszystkie subskrypcje w ramach dzierżawy usługi Azure AD zostaną połączone do niego. Aby uzyskać instrukcje dotyczące programu PowerShell i uzyskać więcej informacji, zobacz [Tworzenie grupy zarządzania, zasobów i organizacji zarządzanie](../azure-resource-manager/management-groups-create.md).

 
1. Zaloguj się w [Portalu Azure](http://portal.azure.com).
2. Wybierz **wszystkich usług** > **grup zarządzania**.
3. Na stronie głównej wybierz **nowym rozwiązaniem do zarządzania grupy.** 

    ![Główna grupa](./media/security-center-management-groups/main.png) 
4.  Wypełnij pola Identyfikator grupy zarządzania. 
    - **Identyfikator grupy zarządzania** jest unikatowy identyfikator katalogu, który służy do przesyłania poleceń w tej grupie zarządzania. Ten identyfikator nie można edytować, po utworzeniu, ponieważ jest używany do identyfikowania tej grupy w systemie Azure. 
    - Nazwa wyświetlana tego pola jest nazwa, która jest wyświetlana w witrynie Azure portal. Nazwę wyświetlaną osobne pole jest opcjonalne, tworząc zarządzania grupy i można ją zmienić w dowolnym momencie.  

      ![Przycisk Utwórz](./media/security-center-management-groups/create_context_menu.png)  
5.  Wybierz **Zapisz**

### <a name="view-management-groups-in-the-azure-portal"></a>Wyświetlanie grup zarządzania w witrynie Azure portal
1. Zaloguj się do [witryny Azure portal](http://portal.azure.com).
2. Aby wyświetlić grupy zarządzania, wybierz **wszystkich usług** w menu głównym platformy Azure.
3. W obszarze **ogólne**, wybierz opcję **grup zarządzania**.

    ![Utwórz grupę zarządzania](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Udziel widoczność na poziomie dzierżawy i możliwość przypisania zasad

Aby uzyskać wgląd w stan zabezpieczeń wszystkich subskrypcji zarejestrowany w dzierżawie usługi Azure AD, rolę RBAC z wystarczającymi uprawnieniami do odczytu jest wymagany do przypisania w głównej grupie zarządzania.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Podniesienie poziomu dostępu administratora globalnego usługi Azure Active Directory
Administrator dzierżawy usługi Azure Active Directory nie ma bezpośredni dostęp do subskrypcji platformy Azure. Jednak jako administrator katalogu, mają prawo do wyniesienia rozgrywek się do roli, który ma dostęp. Administrator dzierżawy usługi Azure AD musi podnieść sam administrator dostępu użytkowników na poziomie grupy zarządzania głównym, może on przypisać role RBAC. Aby uzyskać instrukcje dotyczące programu PowerShell i dodatkowe informacje, zobacz [podniesienie poziomu dostępu administratora globalnego usługi Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com).

2. Na liście w okienku nawigacji kliknij **usługi Azure Active Directory** a następnie kliknij przycisk **właściwości**.

   ![Właściwości usługi AD platformy Azure — zrzut ekranu](./media/security-center-management-groups/aad-properties.png)

3. W obszarze **Administrator globalny może zarządzać subskrypcjami platformy Azure i grup zarządzania**, ustaw przełącznik na **tak**.

   ![Administrator globalny może zarządzać subskrypcjami platformy Azure i zarządzania grupy — zrzut ekranu](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Po ustawieniu przełącznika na **tak**, konta administratora globalnego (aktualnie zalogowany użytkownik) zostanie dodany do roli Administrator dostępu użytkowników w usłudze Azure RBAC w zakresie głównym (`/`), która przyznaje dostęp do widoku i raportowanie na wszystkie subskrypcje systemu Azure skojarzone z dzierżawą usługi Azure AD.

   - Po ustawieniu przełącznika na **nie**, konta administratora globalnego (aktualnie zalogowany użytkownik) zostanie usunięty z roli Administrator dostępu użytkowników w RBAC platformy Azure. Nie można wyświetlić wszystkie subskrypcje systemu Azure, które są skojarzone z dzierżawą usługi Azure AD i można wyświetlać i zarządzać nimi tylko subskrypcji platformy Azure do których użytkownik ma dostęp.

4. Kliknij przycisk **Zapisz** można zapisać ustawień użytkownika.

    - To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowanego użytkownika.

5. Wykonaj zadania, które należy wprowadzić w podwyższonego poziomu dostępu. Gdy wszystko będzie gotowe, ustaw przełącznik na **nie**.

### <a name="open-or-refresh-security-center"></a>Otwórz lub Odśwież usługi Security Center
Gdy mieli podwyższony poziom dostępu, Otwórz lub Odśwież usługę Azure Security Center, aby sprawdzić, czy masz wgląd we wszystkie subskrypcje w ramach dzierżawy usługi Azure AD. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Upewnij się, że wybrano wszystkie subskrypcje w selektorze subskrypcji, który chcesz wyświetlić w usłudze Security Center.
    ![Zrzut ekranu selektor subskrypcji](./media/security-center-management-groups/subscription-selector.png)
1. Wybierz **wszystkich usług** w menu głównym platformy Azure i w wybierz **usługi Security Center**.
2. W **Przegląd**, znajduje się wykres pokrycie subskrypcji. 
    ![Zrzut ekranu wykresu pokrycie subskrypcji](./media/security-center-management-groups/security-center-subscription-coverage.png)
3. Kliknij pozycję **pokrycia** Aby wyświetlić listę subskrypcji objętych usługą. 
    ![Zrzut ekranu listy pokrycie subskrypcji](./media/security-center-management-groups/security-center-coverage.png)

### <a name="assign-rbac-roles-to-users"></a>Przypisz role RBAC do użytkowników
Gdy administrator dzierżawy ma podwyższonego poziomu dostępu, można przypisać rolę RBAC, do odpowiednich użytkowników na głównym poziomie grupy zarządzania. Zalecaną rolą można przypisać jest [ **czytnika**](../role-based-access-control/built-in-roles.md#reader). Ta rola jest wymagana w celu zapewnienia widoczności na poziomie dzierżawy. Przypisaną rolę będzie automatycznie propagowane do wszystkich grup zarządzania, jak i subskrypcje w ramach głównej grupy zarządzania. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [dostępnych ról](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles). 

1. Zainstaluj program [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Uruchom następujące polecenia: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń administratora globalnego. 

    ![Zaloguj się zrzut ekranu monitu](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Udzielić uprawnienia roli Czytelnik, uruchamiając następujące polecenie:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Aby usunąć rolę, użyj następującego polecenia: 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>Usuń podwyższonego poziomu dostępu 
Gdy role RBAC zostały przypisane do użytkowników, administrator dzierżawy należy usunąć samego z roli administrator dostępu użytkownika.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com).

2. Na liście w okienku nawigacji kliknij **usługi Azure Active Directory** a następnie kliknij przycisk **właściwości**.

3. W obszarze **Administrator globalny może zarządzać subskrypcjami platformy Azure i grup zarządzania**, ustaw przełącznik na **nie**.

4. Kliknij przycisk **Zapisz** można zapisać ustawień użytkownika.



## <a name="adding-subscriptions-to-a-management-groups"></a>Dodawanie subskrypcji do grupy zarządzania
Subskrypcje można dodać do grupy zarządzania, który został utworzony. Te kroki nie są wymagane do uzyskania widoczność w całej dzierżawie i globalnych zasad oraz zarządzanie dostępem.

1. W obszarze **grup zarządzania**, wybierz grupę zarządzania, aby dodać subskrypcję.

    ![Wybierz grupę zarządzania, aby dodać subskrypcję do](./media/security-center-management-groups/management-group-subscriptions.png)

2. Wybierz **dodawania istniejących**.

    ![Dodaj istniejącą](./media/security-center-management-groups/add-existing.png)

3. Wprowadź subskrypcję w ramach **Dodaj istniejący zasób** i kliknij przycisk **Zapisz**.

4. Powtórz kroki od 1 do 3, aż zostaną dodane wszystkie subskrypcje w zakresie.

 > [!NOTE]
 > Grupy zarządzania może zawierać zarówno subskrypcje, jak i podrzędne grupy zarządzania. W przypadku przypisania użytkownika rolę RBAC do nadrzędnej grupy zarządzania, dostęp jest dziedziczona przez podrzędne grupy zarządzania subskrypcjami. Zasadami ustawionymi w nadrzędna grupa zarządzania również są dziedziczone przez elementy podrzędne. 

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uzyskać wgląd w całej dzierżawie usługi Azure Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Zarządzanie i odpowiadanie na alerty zabezpieczeń w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)

