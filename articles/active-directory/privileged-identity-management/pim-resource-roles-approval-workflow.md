---
title: Privileged Identity Management — przepływ pracy zatwierdzania role zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu zatwierdzania procesu dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Zarządzanie tożsamościami uprzywilejowanymi - role zasobów - zatwierdzenia

Z przepływ pracy zatwierdzania w PIM role zasobów platformy Azure Administratorzy mogą dodatkowo ochrony lub ograniczyć dostęp do kluczowych zasobów przez wymaganie zatwierdzenia, aby aktywować przypisania roli. Unikatowe role zasobów platformy Azure jest koncepcja hierarchii zasobów. Taka hierarchia umożliwia dziedziczenia przypisań ról z obiektu zasobu nadrzędnego w dół do wszystkich zasobów podrzędnych podrzędnego w kontenerze nadrzędnym. 

Na przykład Robert, administrator zasobów używa PIM można przypisać Alicja jako element członkowski kwalifikujących się do właściciela roli w ramach subskrypcji firmy Contoso. Z tym przypisaniem Alicja jest również kwalifikujących się właścicielem wszystkich kontenerów grupy zasobów w ramach subskrypcji firmy Contoso i wszystkie zasoby (na przykład maszyny wirtualne) zawartych w każdej grupie zasobów subskrypcji. Załóżmy, że istnieją trzy grupy zasobów w subskrypcji Contoso; Test firmy Fabrikam, deweloperów firmy Fabrikam i Fabrikam produkcyjną. Każda z tych grup zasobów zawiera jednej maszyny wirtualnej.

Ustawienia usługi PIM dla każdej roli zasobu i w przeciwieństwie do przypisania te ustawienia nie są dziedziczone i zastosować wyłącznie do zasobów. [Więcej informacji na temat przypisania kwalifikujących się i widoczności zasobu.](pim-resource-roles-eligible-visibility.md)

W powyższym przykładzie Roberta, który używa PIM wymagające wszystkich elementów członkowskich w roli właściciela zatwierdzenia żądania subskrypcji Contoso do aktywowania. Aby chronić zasoby zawarte w grupie zasobów produkcyjną firmy Fabrikam, Roberta, który wymaga zatwierdzenia dla członków roli właściciela tego zasobu również. Role właściciela w firmie Fabrikam testowym i deweloperów firmy Fabrikam nie wymagają zatwierdzenia, aby aktywować.

Jeśli Alicja żądań aktywacji swojej roli właściciela subskrypcji Contoso osoba zatwierdzająca musi zatwierdzić lub odrzucić swoje żądanie, zanim użytkownik staje się aktywny w roli. Ponadto, jeśli Alicja decyduje się na [zakres jej aktywacji](pim-resource-roles-activate-your-roles.md#just-enough-administration) do grupy zasobów firmy Fabrikam produkcyjną osoba zatwierdzająca musi zatwierdzić lub odrzucić to żądanie zbyt. Jednak jeśli Alicja zdecyduje się na zakres jej aktywacji do jednego lub obu tych testów Fabrikam lub deweloperów firmy Fabrikam, zatwierdzenia nie będą wymagane.

Przepływ pracy zatwierdzania nie może być konieczne dla wszystkich członków z roli. Rozważmy scenariusz, w którym organizacja wynajmuje kilka stowarzyszonych kontraktu w rozwoju aplikacji, który zostanie wykonany w subskrypcji platformy Azure. Administrator zasobów chcesz pracownikom korzystać kwalifikujących się bez wymagane zatwierdzenie, ale stowarzyszonych umowy musisz zażądać zatwierdzenia. Aby skonfigurować przepływ pracy zatwierdzania tylko kontraktu skojarzone z takimi samymi uprawnieniami jak roli można utworzyć niestandardową rolę przypisane do pracowników i wymagają zatwierdzenia do aktywowania tej niestandardowej roli zabezpieczeń. [Więcej informacji o niestandardowych rolach](pim-resource-roles-custom-role-policy.md).

Wykonaj poniższe kroki, aby skonfigurować przepływ pracy zatwierdzania i określ, kto może zatwierdzać lub odrzucać żądania.

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia w celu aktywacji

Przejdź do usługi PIM w portalu Azure, a następnie wybierz zasób z listy.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

W menu nawigacji po lewej stronie wybierz **ustawienia roli**.

Wyszukaj i wybierz rolę, a następnie kliknij przycisk **Edytuj** Aby zmodyfikować ustawienia.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

Podczas aktywacji w sekcji zaznacz pole **wymagają zatwierdzenia, aby aktywować**.

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Określanie osób zatwierdzających

Kliknij przycisk **wybierz osób zatwierdzających** otworzyć ekran wyboru.

>[!NOTE]
>Musisz wybrać co najmniej jednego użytkownika lub grupę, aby zaktualizować ustawienia. Nie ma żadnych osób zatwierdzających domyślne.

Zasób Administratorzy mogą dodawać dowolną kombinację użytkowników i grup do listy. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Żądania zatwierdzenia aktywacji

Proces żądania zatwierdzenia nie ma wpływu na procedury element członkowski należy wykonać, aby aktywować. [Przejrzyj kroki, aby aktywować rolę](pim-resource-roles-activate-your-roles.md).

Jeśli element członkowski żądanie aktywacji roli, która wymaga zatwierdzenia i rola nie jest już wymagane, element członkowski może spowodować anulowanie żądania w PIM.

Aby anulować, przejdź do usługi PIM i wybierz "Moje żądania". Znajdź żądania i kliknij przycisk "Anuluj".

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Zatwierdzanie lub odrzucanie żądań

Zatwierdzanie lub odrzucanie żądań, musi być członkiem listy osoby zatwierdzającej. W PIM wybierz "Zatwierdzać żądania" w menu nawigacji po lewej stronie na karcie i Znajdź żądania.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

Wybierz żądanie, podaj uzasadnienie decyzji, a następnie wybierz zatwierdzają lub odrzucają żądania rozpoznawania.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Powiadomienia dotyczące przepływu pracy

- Wszystkie elementy członkowskie na liście osoba zatwierdzająca są powiadomienie e-mail podczas ich przeglądu oczekuje na żądanie dla roli. Powiadomienia e-mail zawierają bezpośredniego łącza do żądania, w których osoba zatwierdzająca może zaakceptować lub odrzucić.
- Żądania są rozpoznawane przez pierwszego elementu członkowskiego listy, które są opracowywane lub nie zezwala na. 
- Gdy osoba zatwierdzająca odpowiada na żądanie, wszystkie elementy członkowskie na liście osoba zatwierdzająca jest powiadamiany o akcji. 
- Administratorzy zasobów są powiadamiani, kiedy składnik zatwierdzone staje się aktywny w danej roli. 

>[!Note]
>W przypadku których administrator zasobów zdaniem zatwierdzonych element członkowski nie powinien być aktywny one usunąć przypisania roli active w PIM. Mimo że administratorzy zasobu nie jest powiadamiany o oczekujących żądań o ile nie są oni członkami listy Osoba zatwierdzająca, mogą wyświetlać i anulowania oczekującego żądania wszystkich użytkowników, wyświetlając żądań oczekujących w PIM. 

## <a name="next-steps"></a>Kolejne kroki

[Zastosuj ustawienia PIM do unikatowych grup użytkowników](pim-resource-roles-custom-role-policy.md)
