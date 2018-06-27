---
title: Wgląd dzierżawy na poziomie Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat dzierżawy całej wgląd w Centrum zabezpieczeń Azure.
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
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 2c95b06ce34b850d1bfaf60e47d6e5fede148a38
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025814"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Uzyskaj wgląd w całej dzierżawy dla Centrum zabezpieczeń Azure
Ten artykuł ułatwia rozpoczęcie pracy, wykonując kilka czynności, które zmaksymalizować korzyści, które udostępnia Centrum zabezpieczeń Azure. Wykonanie tych czynności umożliwia wgląd we wszystkich subskrypcji platformy Azure, które są połączone z dzierżawą usługi Azure Active Directory i efektywnie zarządzać stan zabezpieczeń w organizacji na dużą skalę, stosując zasady zabezpieczeń w wielu Subskrypcje w sposób aggregative.

## <a name="management-groups"></a>Grupy zarządzania
Grupy zarządzania platformy Azure umożliwiają zarządzanie dostępu, zasad i raportowania w grupach subskrypcji, jak również wydajne zarządzanie całą nieruchomości Azure, wykonując czynności w głównej grupie zarządzania. Każdej dzierżawy usługi Azure AD otrzymuje grupę pojedynczego zarządzania najwyższego poziomu o nazwie głównej grupy zarządzania. Tej grupy zarządzania głównego jest wbudowana w hierarchii, aby wszystkie grupy zarządzania i subskrypcje fold do niego. Ta grupa umożliwia globalnych zasad i przypisań RBAC mają być zastosowane na poziomie katalogu. 

Grupa zarządzania głównego jest tworzona automatycznie po wykonaniu dowolnej z następujących czynności: 
1. Zgódź się na używanie grup zarządzania platformy Azure, przechodząc do **grup zarządzania** w [portalu Azure](https://portal.azure.com).
2. Utwórz grupę zarządzania za pośrednictwem wywołania interfejsu API.
3. Utwórz grupę zarządzania przy użyciu programu PowerShell.

Aby uzyskać szczegółowy przegląd grup zarządzania, zobacz [organizowania zasobów z grupami zarządzania platformy Azure](../azure-resource-manager/management-groups-overview.md) artykułu.

## <a name="create-a-management-group-in-the-azure-portal"></a>Utwórz grupę zarządzania w portalu Azure
Można zorganizować subskrypcje do grupy zarządzania i zastosować zasad zarządzania do grupy zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady stosowane do grupy zarządzania. Gdy grupy zarządzania nie są wymagane, aby dołączyć Centrum zabezpieczeń, zdecydowanie zaleca się utworzenie co najmniej jedną grupę zarządzania, dzięki utworzeniu głównej grupy zarządzania. Po utworzeniu grupy wszystkie subskrypcje w ramach dzierżawy usługi Azure AD zostaną połączone do niego. Aby uzyskać instrukcje dotyczące programu PowerShell i uzyskać więcej informacji, zobacz [tworzyć grupy zarządzania na potrzeby zarządzanie zasobami i organizacji](../azure-resource-manager/management-groups-create.md).

 
1. Zaloguj się w [Portalu Azure](http://portal.azure.com).
2. Wybierz **wszystkie usługi** > **grup zarządzania**.
3. Na stronie głównej wybierz **zarządzania nowej grupy.** 

    ![Główna grupa](./media/security-center-management-groups/main.png) 
4.  Wypełnij pola Identyfikator grupy zarządzania. 
    - **Identyfikator grupy zarządzania** jest unikatowym identyfikatorem katalogu, który służy do przesyłania poleceń w danej grupie zarządzania. Tego identyfikatora nie można edytować po utworzeniu, ponieważ jest używany do identyfikowania tej grupy w systemie Azure. 
    - Pole Nazwa wyświetlana jest nazwa, która jest wyświetlana w portalu Azure. Nazwa wyświetlana oddzielne pole jest opcjonalne, tworząc zarządzania grupy i można go zmienić w dowolnym momencie.  

      ![Przycisk Utwórz](./media/security-center-management-groups/create_context_menu.png)  
5.  Wybierz **Zapisz**

### <a name="view-management-groups-in-the-azure-portal"></a>Wyświetlanie grup zarządzania w portalu Azure
1. Zaloguj się do [portalu Azure](http://portal.azure.com).
2. Aby wyświetlić grupy zarządzania, wybierz **wszystkie usługi** w menu głównym platformy Azure.
3. W obszarze **ogólne**, wybierz pozycję **grup zarządzania**.

    ![Utwórz grupę zarządzania](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Udziel widoczność na poziomie dzierżawy i możliwość przypisania zasad

Aby uzyskać wgląd w stan zabezpieczeń wszystkie subskrypcje w zarejestrowany w dzierżawie usługi Azure AD, roli RBAC z wystarczającymi uprawnieniami odczytu jest wymagana do przypisania w głównej grupie zarządzania.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Podniesienie uprawnień dostępu administratora globalnego w usłudze Azure Active Directory
Administrator dzierżawy usługi Azure Active Directory nie ma bezpośredniego dostępu do subskrypcji platformy Azure. Jednak jako administrator katalogu, mają prawo do podniesienia uprawnień siebie do roli, który ma dostęp. Administrator dzierżawy usługi Azure AD musi podnieść poziom się do administratora dostępu użytkowników na poziomie głównym grupy zarządzania, może on przypisać role RBAC. Dla instrukcji środowiska PowerShell i dodatkowe informacje, zobacz [podniesienie uprawnień dostępu administratora globalnego w usłudze Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Zaloguj się do [portalu Azure](https://portal.azure.com) lub [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com).

2. Na liście nawigacji kliknij **usługi Azure Active Directory** , a następnie kliknij przycisk **właściwości**.

   ![Azure AD właściwości — zrzut ekranu](./media/security-center-management-groups/aad-properties.png)

3. W obszarze **administratora globalnego mogą zarządzać subskrypcji platformy Azure i grup zarządzania**, ustawiona przełącznik na **tak**.

   ![Administrator globalny można zarządzać subskrypcjami platformy Azure i grup zarządzania — zrzut ekranu](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Jeśli zostanie ustawiona przełącznik **tak**, konta administratora globalnego (aktualnie zalogowany użytkownik) jest dodawany do roli Administrator dostępu użytkowników, w Azure RBAC w zakresie głównym (`/`), która udziela dostępu do widoku i tworzenia raportów w wszystkie subskrypcje platformy Azure skojarzony z dzierżawą usługi Azure AD.

   - Jeśli zostanie ustawiona przełącznik **nr**, konta administratora globalnego (aktualnie zalogowany użytkownik) zostanie usunięty z roli Administrator dostępu użytkowników w programie Azure RBAC. Nie można wyświetlić wszystkie subskrypcje platformy Azure, które są skojarzone z dzierżawy usługi Azure AD i można wyświetlać i zarządzać nimi tylko subskrypcji platformy Azure do których przyznano Ci dostęp.

4. Kliknij przycisk **zapisać** można zapisać ustawienia.

    - To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowanego użytkownika.

5. Wykonaj zadania, które należy upewnić się na podwyższonego poziomu dostępu. Gdy wszystko będzie gotowe, ustawiona przełącznik do **nr**.

### <a name="assign-rbac-roles-to-users"></a>Przypisz role RBAC dla użytkowników
Gdy administrator dzierżawy ma z podwyższonym poziomem uprawnień dostępu, rolę RBAC można przypisać, do odpowiednich użytkowników na poziomie głównym grupy zarządzania. Aby przypisać zalecaną rolą jest [ **czytnika**](../role-based-access-control/built-in-roles.md#reader). Ta rola jest wymagana w celu zapewnienia widoczność na poziomie dzierżawy. Przypisaną rolę będzie automatycznie propagowane do wszystkich grup zarządzania i subskrypcje w ramach głównej grupy zarządzania. Aby uzyskać więcej informacji o rolach RBAC, zobacz [dostępnych ról](../active-directory/active-directory-assign-admin-roles-azure-portal.md#available-roles).

1. Zainstaluj program [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Uruchom następujące polecenia: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń administratora globalnego. 

    ![Zrzut ekranu monitu zaloguj się](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Przyznaj czytnika uprawnienia roli, uruchamiając następujące polecenie:

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
Po role RBAC zostały przypisane do użytkowników, administrator dzierżawy należy się usunąć z roli administrator dostępu użytkowników.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) lub [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com).

2. Na liście nawigacji kliknij **usługi Azure Active Directory** , a następnie kliknij przycisk **właściwości**.

3. W obszarze **administratora globalnego mogą zarządzać subskrypcji platformy Azure i grup zarządzania**, ustawiona przełącznik na **nr**.

4. Kliknij przycisk **zapisać** można zapisać ustawienia.

### <a name="open-or-refresh-security-center"></a>Otwórz lub Odśwież Centrum zabezpieczeń
Po przypisaniu role RBAC Otwórz lub Odśwież Centrum zabezpieczeń Azure, aby sprawdzić, czy masz wgląd w wszystkie subskrypcje w obszarze dzierżawy usługi Azure AD. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz **wszystkie usługi** w menu głównym Azure wybierz **Centrum zabezpieczeń**.
3. W **omówienie**, jest wykres pokrycia subskrypcji. 
    ![Zrzut ekranu wykresu pokrycia subskrypcji](./media/security-center-management-groups/security-center-subscription-coverage.png)
4. Polecenie **pokrycia** Lista subskrypcji objętych usługą. 
    ![Zrzut ekranu listy pokrycia subskrypcji](./media/security-center-management-groups/security-center-coverage.png)

## <a name="adding-subscriptions-to-a-management-groups"></a>Dodawanie subskrypcji do grupy zarządzania
Można dodać subskrypcji do utworzonej grupy zarządzania. Te kroki są wymagane do uzyskania widoczność całej dzierżawcy i globalne zasady i zarządzanie dostępem.

1. W obszarze **grup zarządzania**, wybierz grupę zarządzania, aby dodać subskrypcję.

    ![Wybierz grupę zarządzania, aby dodać subskrypcję do](./media/security-center-management-groups/management-group-subscriptions.png)

2. Wybierz **Dodaj istniejącą**.

    ![Dodaj istniejącą](./media/security-center-management-groups/add-existing.png)

3. Wprowadź subskrypcję w obszarze **Dodaj istniejący zasób** i kliknij przycisk **zapisać**.

4. Powtórz kroki od 1 do 3, aż zostaną dodane wszystkie subskrypcje w zakresie.

 > [!NOTE]
 > Grupy zarządzania mogą zawierać zarówno subskrypcji i grup zarządzania podrzędnych. Po przypisaniu użytkownik roli RBAC do nadrzędnej grupy zarządzania, dostępu jest dziedziczona przez subskrypcje podrzędne grupy zarządzania. Zasadami ustawionymi w nadrzędnej grupie zarządzania również są dziedziczone przez elementy podrzędne. 

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wgląd dzierżawy na poziomie Centrum zabezpieczeń Azure. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Zarządzanie i reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md)

