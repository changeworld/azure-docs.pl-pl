---
title: Aktywacja subskrypcji platformy Azure i kont | Dokumentacja firmy Microsoft
description: Włącz dostęp za pomocą interfejsów API usługi Azure Resource Manager dla nowych i istniejących kont i rozwiązać typowe problemy z konta.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/01/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: dbbbc7ee87d53f65d51b20fd5b8ffcb6c4930f15
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="activate-azure-subscriptions-and-accounts-with-azure-cost-management"></a>Aktywacja subskrypcji platformy Azure i kont za pomocą usługi Azure Management koszt

Dodanie lub uaktualnienie poświadczeń usługi Azure Resource Manager umożliwia Azure koszt zarządzania odnaleźć wszystkie konta i subskrypcji w ramach dzierżawy usługi Azure. Jeśli masz także włączona na maszynach wirtualnych rozszerzenia diagnostyki Azure, Azure kosztów zarządzania mogą zbierać metryki rozszerzonej, takie jak procesor CPU i pamięci. W tym artykule opisano sposób włączania dostępu przy użyciu interfejsów API usługi Azure Resource Manager dla nowych i istniejących kont. Zawiera również opis rozwiązywania typowych problemów z konta.

Azure kosztów zarządzania nie może uzyskać dostępu większość danych subskrypcji platformy Azure, gdy subskrypcja jest _nieaktywowani_. Należy zmodyfikować _nieaktywowani_ kont, dzięki czemu Azure kosztów zarządzania mogą uzyskiwać do nich dostęp.

## <a name="required-azure-permissions"></a>Wymagane uprawnienia Azure

Określone uprawnienia są wymagane do wykonania procedur w tym artykule. Użytkownik lub administrator dzierżawy musi mieć oba następujące uprawnienia:

- Uprawnienia do rejestrowania aplikacji CloudynCollector z dzierżawy usługi Azure AD.
- Możliwość przypisywanie aplikacji do roli w Twojej subskrypcji platformy Azure.

W Twojej subskrypcji platformy Azure, musi mieć swoje konta `Microsoft.Authorization/*/Write` dostępu przypisany CloudynCollector aplikacji. Ta akcja jest przyznawane za pośrednictwem [właściciela](../role-based-access-control/built-in-roles.md#owner) roli lub [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) roli.

Jeśli Twoje konto jest przypisany **współautora** roli, nie masz odpowiednich uprawnień, aby przypisać tę aplikację. Błąd podczas próby przypisać aplikację CloudynCollector do subskrypcji platformy Azure.

### <a name="check-azure-active-directory-permissions"></a>Sprawdź uprawnienia usługi Azure Active Directory

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. W portalu Azure wybierz **usługi Azure Active Directory**.
3. W usłudze Azure Active Directory, zaznacz **ustawienia użytkownika**.
4. Sprawdź **rejestracji aplikacji** opcji.
    - Jeśli wartość jest ustawiona na **tak**, a następnie użytkownicy niebędący administratorami można zarejestrować aplikacji usługi AD. To ustawienie oznacza, że każdy użytkownik w dzierżawie usługi Azure AD można zarejestrować aplikacji. Możesz przejść do Azure wymagane uprawnienia do subskrypcji.  
    ![Rejestracji aplikacji](./media/activate-subs-accounts/app-register.png)
    - Jeśli **rejestracji aplikacji** ustawiono opcję **nr**, następnie tylko użytkownicy administracyjni dzierżawcy można zarejestrować aplikacji usługi Azure Active Directory. Administrator dzierżawy musi zarejestrować aplikację CloudynCollector.


## <a name="add-an-account-or-update-a-subscription"></a>Dodaj konto lub zaktualizować subskrypcji

Po dodaniu aktualizacji konta subskrypcji, można przyznać dostęp do usługi Azure kosztów zarządzania do danych Azure.

### <a name="add-a-new-account-subscription"></a>Dodaj nowe konto (subskrypcji)

1. W portalu Azure kosztów zarządzania kliknij koło zębate symbol w prawym górnym i wybierz **kont chmury**.
2. Kliknij przycisk **Dodaj nowe konto** i **Dodaj nowe konto** zostanie wyświetlone okno. Wprowadź wymagane informacje.  
    ![Dodaj nowe pole konta](./media/activate-subs-accounts//add-new-account.png)

### <a name="update-a-subscription"></a>Aktualizowanie subskrypcji

1. Jeśli chcesz zaktualizować _nieaktywowani_ subskrypcji, która już istnieje w Azure koszt zarządzania, w przystawce Zarządzanie kontami kliknij symbol ołówka edycji z prawej strony nadrzędnej _identyfikator GUID dzierżawy_. Subskrypcje są zgrupowane w dzierżawy nadrzędnej, więc należy unikać indywidualnie aktywowania subskrypcji.
    ![Ponowne odnajdywanie subskrypcji](./media/activate-subs-accounts/existing-sub.png)
2. W razie potrzeby wprowadź identyfikator dzierżawy. Jeśli nie znasz Identyfikatora dzierżawy, wykonaj następujące kroki, aby ją znaleźć:
    1. Zaloguj się do [portalu Azure](https://portal.azure.com).
    2. W portalu Azure wybierz **usługi Azure Active Directory**.
    3. Aby uzyskać identyfikator dzierżawy, wybierz pozycję **Właściwości** dla swojej dzierżawy usługi Azure AD.
    4. Skopiuj katalog Identyfikatora GUID. Ta wartość jest Twoim identyfikatorem dzierżawy.
    Aby uzyskać więcej informacji, zobacz [uzyskanie Identyfikatora dzierżawy](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
3. W razie potrzeby wybierz szybkość identyfikatora Jeśli nie znasz Identyfikatora szybkość, wykonaj następujące kroki, aby ją znaleźć.
    1. W prawym górnym rogu portalu Azure, kliknij przycisk informacje o użytkowniku, a następnie kliknij przycisk **wyświetlić mojego rachunku**.
    2. W obszarze **konta rozliczeniowego**, kliknij przycisk **subskrypcje**.
    3. W obszarze **Moje subskrypcje**, wybierz subskrypcję.
    4. Częstotliwość identyfikator jest wyświetlany w obszarze **identyfikator oferty**. Skopiuj identyfikator oferty dla subskrypcji.
4. Dodaj nowe konto (lub edytować subskrypcję) kliknij pozycję **zapisać** (lub **dalej**). Użytkownik jest przekierowywany do portalu Azure.
5. Zaloguj się do portalu. Kliknij przycisk **Akceptuj** do autoryzacji moduł zbierający Azure kosztów zarządzania dostęp do konta platformy Azure.

    W przypadku przekierowany do strony zarządzania Azure kosztów zarządzania kontami i Twojej subskrypcji został zaktualizowany o **active** stan konta. Powinna zostać wyświetlona zielony znacznik wyboru w kolumnie Menedżera zasobów.

    Jeśli nie widzisz symbolu zielonym znacznikiem wyboru dla co najmniej jednego z subskrypcji, oznacza to, nie masz uprawnień do tworzenia aplikacji czytnika (CloudynCollector) dla subskrypcji. Użytkownik z uprawnieniami wyższej subskrypcji musi Powtórz ten proces.

Obejrzyj [łączenie usługi Azure Resource Manager z usługą Azure Management koszt](https://youtu.be/oCIwvfBB6kk) wideo, który przeprowadzi Cię przez proces.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Typowych problemów konfiguracji pośrednie enterprise

Przy pierwszym użyciu portalu Azure koszt zarządzania, jeśli jesteś użytkownikiem umowy Enterprise Agreement lub Cloud Solution Provider (CSP) można napotkać następujące komunikaty:

- *Określony klucz interfejsu API nie jest kluczem najwyższego poziomu rejestrowania* wyświetlane w **ustawić zapasowej Azure kosztów zarządzania** kreatora.
- *Bezpośrednia rejestracja — nie* wyświetlane w portalu umowy Enterprise Agreement.
- *Nie użycia znaleziono danych w ciągu ostatnich 30 dni. Skontaktuj się z dystrybutora, aby upewnić się, znaczników zostały włączone dla konta platformy Azure* wyświetlane w portalu zarządzania Azure kosztów.

Poprzednie komunikaty wskazują zakupiono Azure Enterprise Agreement u odsprzedawcy lub dostawcy usług Kryptograficznych. Ze sprzedawcą lub dostawcy usług Kryptograficznych, trzeba włączyć _znacznika_ dla konta platformy Azure, dzięki czemu można wyświetlać dane w Azure kosztów zarządzania.

Oto jak rozwiązać problemy:

1. Należy włączyć odsprzedawcą _znacznika_ dla Twojego konta. Aby uzyskać instrukcje, zobacz [pośrednie przewodnik dołączania klienta](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Możesz wygenerować klucz Azure Enterprise Agreement do użycia z usługą Azure Management kosztów. Aby uzyskać instrukcje, zobacz [zarejestrować Azure Enterprise Agreement i widok danych kosztów](https://docs.microsoft.com/en-us/azure/cost-management/quick-register-ea).

Tylko administrator usługi Azure można włączyć zarządzanie kosztów. Uprawnienia administratora współpracującego są niewystarczające.

Przed wygenerowaniem klucz interfejsu API umowy Enterprise Azure Konfigurowanie Azure koszt zarządzania, należy włączyć interfejsu API rozliczenia Azure zgodnie z instrukcjami na:

- [Omówienie API raportowania dla przedsiębiorstw](../billing/billing-enterprise-api.md)
- [Microsoft Azure enterprise portal interfejsu API raportowania](https://ea.azure.com/helpdocs/reportingAPI) w obszarze **Włączanie dostępu do interfejsu API danych**

Może należy również podać Administratorzy działu, właściciele konta i uprawnienia administratorów przedsiębiorstwa _wyświetlić opłat_ przy użyciu interfejsu API rozliczeń.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie zostało już ukończone pierwszy samouczek koszt zarządzania, przeczytaj je na [Przejrzyj użycia i koszty](tutorial-review-usage.md).
