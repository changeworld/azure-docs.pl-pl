---
title: Uzyskaj wgląd w usługi Azure Security Center dla całej dzierżawy | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zarządzać postawą zabezpieczeń na dużą skalę, stosując zasady do wszystkich subskrypcji połączonych z dzierżawą usługi Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 734876380d22f5d4d6dae0dd375b238fd5f6ffed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74559344"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Uzyskaj wgląd w usługi Azure Security Center dla całej dzierżawy
W tym artykule wyjaśniono, jak zarządzać postawą zabezpieczeń organizacji na dużą skalę, stosując zasady zabezpieczeń do wszystkich subskrypcji platformy Azure połączonych z dzierżawą usługi Azure Active Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Grupy zarządzania
Grupy zarządzania platformy Azure umożliwiają efektywne zarządzanie dostępem, zasadami i raportowaniem grup subskrypcji, a także efektywne zarządzanie całą zasobą platformy Azure przez wykonywanie akcji w głównej grupie zarządzania. Każda dzierżawa usługi Azure AD otrzymuje jedną grupę zarządzania najwyższego poziomu o nazwie głównej grupy zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Ta grupa umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. 

Główna grupa zarządzania jest tworzona automatycznie po wykonaniu dowolnej z następujących czynności: 
1. Zgłoś użycie grup zarządzania platformy Azure, przechodząc do **grup zarządzania** w [witrynie Azure portal.](https://portal.azure.com)
2. Tworzenie grupy zarządzania za pośrednictwem wywołania interfejsu API.
3. Tworzenie grupy zarządzania za pomocą programu PowerShell.

Aby uzyskać szczegółowe omówienie grup zarządzania, zobacz [organizowanie zasobów za pomocą grup zarządzania platformy Azure](../azure-resource-manager/management-groups-overview.md) artykułu.

## <a name="create-a-management-group-in-the-azure-portal"></a>Tworzenie grupy zarządzania w witrynie Azure portal
Subskrypcje można organizować w grupy zarządzania i stosować zasady nadzoru do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady zastosowane do tej grupy zarządzania. Chociaż grupy zarządzania nie są wymagane do wbudowanego centrum zabezpieczeń, zdecydowanie zaleca się utworzenie co najmniej jednej grupy zarządzania w celu utworzenia głównej grupy zarządzania. Po utworzeniu grupy wszystkie subskrypcje w ramach dzierżawy usługi Azure AD zostaną z nią połączone. Aby uzyskać instrukcje dotyczące programu PowerShell i innych informacji, zobacz [Tworzenie grup zarządzania zasobami i organizacją](../azure-resource-manager/management-groups-create.md).

 
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz pozycję Wszystkie > **grupy zarządzania** **usługami**.
3. Na stronie głównej wybierz pozycję **Nowa grupa zarządzania.** 

    ![Główna grupa](./media/security-center-management-groups/main.png) 
4.  Wypełnij pole Identyfikator grupy zarządzania. 
    - **Identyfikator grupy zarządzania** jest unikatowym identyfikatorem katalogu, który jest używany do przesyłania poleceń w tej grupie zarządzania. Ten identyfikator nie jest edytowalny po utworzeniu, ponieważ jest używany w całym systemie platformy Azure do identyfikowania tej grupy. 
    - Pole nazwy wyświetlanej to nazwa wyświetlana w witrynie Azure portal. Oddzielna nazwa wyświetlana jest opcjonalnym polem podczas tworzenia grupy zarządzania i może zostać zmieniona w dowolnym momencie.  

      ![Utwórz](./media/security-center-management-groups/create_context_menu.png)  
5.  Wybierz pozycję **Zapisz**.

### <a name="view-management-groups-in-the-azure-portal"></a>Wyświetlanie grup zarządzania w witrynie Azure portal
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Aby wyświetlić grupy zarządzania, wybierz **pozycję Wszystkie usługi** w menu głównym platformy Azure.
3. W obszarze **Ogólne**wybierz pozycję **Grupy zarządzania**.

    ![Tworzenie grupy zarządzania](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Udziel widoczności na poziomie dzierżawy i możliwości przypisywania zasad

Aby uzyskać wgląd w poziom zabezpieczeń wszystkich subskrypcji zarejestrowanych w dzierżawie usługi Azure AD, rola RBAC z wystarczającymi uprawnieniami do odczytu musi być przypisana do głównej grupy zarządzania.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Zwiększ poziom dostępu administratora globalnego w usłudze Azure Active Directory
Administrator dzierżawy usługi Azure Active Directory nie ma bezpośredniego dostępu do subskrypcji platformy Azure. Jednak jako administrator katalogu mają prawo podnieść się do roli, która ma dostęp. Administrator dzierżawy usługi Azure AD musi podnieść się do administratora dostępu do użytkownika na poziomie głównej grupy zarządzania, aby można było przypisać role RBAC. Aby uzyskać instrukcje i dodatkowe informacje programu PowerShell, zobacz [Podnoszenie poziomu dostępu administratora globalnego w usłudze Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com).

2. Na liście nawigacji kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Właściwości**.

   ![Właściwości usługi Azure AD — zrzut ekranu](./media/security-center-management-groups/aad-properties.png)

3. W obszarze **Zarządzanie programem Access dla zasobów platformy Azure**ustaw przełącznik na **Tak**.

   ![Administrator globalny może zarządzać subskrypcjami platformy Azure i grupami zarządzania — zrzut ekranu](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Po ustawieniu przełącznika na Tak, zostanie przypisana rola administratora dostępu użytkownika w usłudze Azure RBAC w zakresie głównym (/). Daje to uprawnienia do przypisywania ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Ten przełącznik jest dostępny tylko dla użytkowników, którym przypisano rolę administratora globalnego w usłudze Azure AD.

   - Po ustawieniu przełącznika na Nie, rola administratora dostępu użytkownika w usłudze Azure RBAC zostanie usunięta z konta użytkownika. Nie można już przypisywać ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Możesz wyświetlać i zarządzać tylko subskrypcjami platformy Azure i grupami zarządzania, do których udzielono dostępu.

4. Kliknij **przycisk Zapisz,** aby zapisać ustawienie.

    - To ustawienie nie jest właściwością globalną i dotyczy tylko aktualnie zalogowanego użytkownika.

5. Wykonaj zadania, które należy wykonać przy podwyższonym poziomie dostępu. Po zakończeniu ustaw przełącznik z powrotem na **Nie**.


### <a name="assign-rbac-roles-to-users"></a>Przypisywanie ról RBAC do użytkowników
Aby uzyskać wgląd do wszystkich subskrypcji, administratorzy dzierżawy muszą przypisać odpowiednią rolę RBAC do wszystkich użytkowników, których chcą zapewnić widoczność w całej dzierżawie, w tym siebie, na poziomie głównej grupy zarządzania. Zalecane role do przypisania to **Administrator zabezpieczeń** lub **Czytnik zabezpieczeń**. Ogólnie rzecz biorąc rola Administrator zabezpieczeń jest wymagana do stosowania zasad na poziomie głównym, podczas gdy program Security Reader wystarczy, aby zapewnić widoczność na poziomie dzierżawy. Aby uzyskać więcej informacji na temat uprawnień przyznanych przez te role, zobacz [opis roli wbudowanej administratora zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin) lub opis roli [wbudowanej czytnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Przypisywanie ról RBAC do użytkowników za pośrednictwem witryny Azure portal: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
1. Aby wyświetlić grupy zarządzania, wybierz **pozycję Wszystkie usługi** w menu głównym platformy Azure, a następnie wybierz pozycję Grupy **zarządzania**.
1.  Wybierz grupę zarządzania i kliknij **szczegóły**.

    ![Zrzut ekranu przedstawiający szczegóły grup zarządzania](./media/security-center-management-groups/management-group-details.PNG)
 
1. Następnie kliknij **pozycję Kontrola dostępu (IAM),** a następnie **przypisania ról**.

1. Kliknij **pozycję Dodaj przypisanie roli**.

1. Wybierz rolę do przypisania i użytkownika, a następnie kliknij przycisk **Zapisz**.  
   
   ![Zrzut ekranu przedstawiający rolę Dodaj program programu Security Reader](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Przypisywanie ról RBAC do użytkowników programu PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Zainstaluj [program Azure PowerShell](/powershell/azure/install-az-ps).
2. Uruchom następujące polecenia: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń administratora globalnego. 

    ![Zrzut ekranu monitu o logowanie](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Udziel uprawnień roli czytelnika, uruchamiając następujące polecenie:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Aby usunąć rolę, użyj następującego polecenia: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Otwieranie lub odświeżanie Centrum zabezpieczeń
Po uzyskaniu podwyższonego poziomu dostępu otwórz lub odśwież usługę Azure Security Center, aby sprawdzić, czy masz wgląd we wszystkie subskrypcje w ramach dzierżawy usługi Azure AD. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
2. Upewnij się, że w ujrzesz wszystkie subskrypcje w selektorze subskrypcji, które chcesz wyświetlić w umykarze zabezpieczeń.

    ![Zrzut ekranu przedstawiający selektor subskrypcji](./media/security-center-management-groups/subscription-selector.png)

1. Wybierz **pozycję Wszystkie usługi** w menu głównym platformy Azure, a następnie wybierz pozycję Centrum **zabezpieczeń**.
2. W **przeglądzie**znajduje się wykres pokrycia subskrypcji.

    ![Zrzut ekranu przedstawiający wykres pokrycia subskrypcji](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Kliknij **na Pokrycie,** aby wyświetlić listę subskrypcji objętych. 

    ![Zrzut ekranu przedstawiający listę subskrypcji](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Usuwanie podwyższonego poziomu dostępu 
Po przypisaniu ról RBAC do użytkowników administrator dzierżawy powinien usunąć się z roli administratora dostępu użytkownika.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com).

2. Na liście nawigacji kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Właściwości**.

3. W obszarze **Administrator globalny może zarządzać subskrypcjami i grupami zarządzania platformy Azure**ustaw przełącznik na **Nie**.

4. Kliknij **przycisk Zapisz,** aby zapisać ustawienie.



## <a name="adding-subscriptions-to-a-management-group"></a>Dodawanie subskrypcji do grupy zarządzania
Subskrypcje można dodawać do utworzonej grupy zarządzania. Te kroki nie są obowiązkowe dla uzyskania widoczności całej dzierżawy i globalnego zarządzania zasadami i dostępem.

1. W obszarze **Grupy zarządzania**wybierz grupę zarządzania, do jak chcesz dodać subskrypcję.

    ![Wybierz grupę zarządzania, do jakaś subskrypcję](./media/security-center-management-groups/management-group-subscriptions.png)

2. Wybierz **pozycję Dodaj istniejący**.

    ![Dodawanie istniejących](./media/security-center-management-groups/add-existing.png)

3. Wprowadź subskrypcję w obszarze **Dodaj istniejący zasób** i kliknij pozycję **Zapisz**.

4. Powtarzaj kroki od 1 do 3, aż do dodasz wszystkie subskrypcje w zakresie.

   > [!NOTE]
   > Grupy zarządzania mogą zawierać zarówno subskrypcje, jak i podrzędne grupy zarządzania. Po przypisaniu użytkownika rolę RBAC do nadrzędnej grupy zarządzania, dostęp jest dziedziczony przez subskrypcje podrzędnej grupy zarządzania. Zasady ustawione w nadrzędnej grupie zarządzania są również dziedziczone przez dzieci podrzędne. 

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak uzyskać wgląd w całej dzierżawie dla usługi Azure Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)

