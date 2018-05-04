---
title: Przepływ pracy zatwierdzania role zasobów platformy Azure Privileged Identity Management | Dokumentacja firmy Microsoft
description: Zawiera opis procesu przepływu pracy zatwierdzania zasobów platformy Azure.
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
ms.openlocfilehash: 7781c858a5c0e4db8593df0cf77b868b6fd23622
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Przepływ pracy zatwierdzania role zasobów platformy Azure Privileged Identity Management

Z procedury zatwierdzania w zarządzania tożsamości uprzywilejowanych (PIM) dla ról zasobów platformy Azure Administratorzy mogą dodatkowo ochrona lub ograniczyć dostęp do kluczowych zasobów. Oznacza to, że administratorzy mogą wymagać zatwierdzenia, aby aktywować przypisania roli. 

Koncepcja hierarchii zasobów jest unikatowy dla ról zasobów platformy Azure. Taka hierarchia umożliwia dziedziczenia przypisań ról z obiektu zasobu nadrzędnego w dół do wszystkich zasobów podrzędnych w kontenerze nadrzędnym. 

Na przykład: Robert, administrator zasobów używa PIM można przypisać Alicja jako element członkowski kwalifikujących się do właściciela roli w ramach subskrypcji firmy Contoso. Z tym przypisaniem Alicja jest kwalifikujących się właścicielem wszystkich kontenerów grupy zasobów w ramach subskrypcji firmy Contoso. Alicja jest również kwalifikujących się właścicielem wszystkich zasobów (np. maszyny wirtualne) w każdej grupie zasobów subskrypcji. 

Załóżmy, że istnieją trzy grupy zasobów w subskrypcji Contoso: Test firmy Fabrikam, Fabrikam deweloperów i Fabrikam produkcyjną. Każda z tych grup zasobów zawiera jednej maszyny wirtualnej.

Ustawienia usługi PIM są konfigurowane dla poszczególnych ról zasobu. W przeciwieństwie do przypisania te ustawienia nie są dziedziczone i zastosować wyłącznie do zasobów. [Dowiedz się więcej o kwalifikujących się zadania i widoczność zasobów](pim-resource-roles-eligible-visibility.md).

Przykładzie: Roberta, który używa usługi PIM wymagające wszystkich elementów członkowskich w roli właściciela Contoso subskrypcji żądania zatwierdzenia aktywacji. Aby lepiej chronić zasoby w grupie zasobów produkcyjną firmy Fabrikam, Roberta, który wymaga zatwierdzenia dla członków roli właściciela tego zasobu. Role właściciela w firmie Fabrikam testowym i deweloperów firmy Fabrikam nie wymagają zatwierdzenia aktywacji.

Gdy Alicja żądań aktywacji jego rolę właściciela subskrypcji firmy Contoso, osoba zatwierdzająca musi zatwierdzić lub odrzucić swoje żądanie, zanim użytkownik staje się aktywny w roli. Jeśli Alicja zdecyduje się na [zakres jej aktywacji](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) do grupy zasobów produkcyjną firmy Fabrikam, osoba zatwierdzająca musi zatwierdzić lub odrzucić to żądanie, za. Ale jeśli Alicja decyduje się na zakres jej aktywacji do jednego lub obu tych testów Fabrikam lub deweloperów firmy Fabrikam, zatwierdzenia nie jest wymagane.

Przepływ pracy zatwierdzania nie może być konieczne dla wszystkich członków z roli. Rozważmy scenariusz, w którym organizacja wynajmuje kilka stowarzyszonych kontraktu ułatwiające projektowanie aplikacji, który zostanie wykonany w subskrypcji platformy Azure. Jako administrator zasobów pracownicy mają mieć dostęp kwalifikujących się bez wymagane zatwierdzenie, ale stowarzyszonych umowy musisz zażądać zatwierdzenia. Aby skonfigurować przepływ pracy zatwierdzania dla tylko stowarzyszonych kontraktu, można utworzyć niestandardową rolę z takimi samymi uprawnieniami jak od roli przypisanej do pracowników. Można zażądać zatwierdzenia do aktywowania tej niestandardowej roli zabezpieczeń. [Więcej informacji o niestandardowych rolach](pim-resource-roles-custom-role-policy.md).

Aby skonfigurować przepływ pracy zatwierdzania i określ, kto może zatwierdzanie lub odrzucanie żądań, należy użyć poniższych procedur.

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia w celu aktywacji

1. Przejdź do usługi PIM w portalu Azure, a następnie wybierz zasób z listy.

   ![Okienko "Zasobów platformy azure" z wybranego zasobu](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. W okienku po lewej stronie wybierz **ustawienia roli**.

3. Wyszukaj i wybierz rolę, a następnie wybierz **Edytuj** Aby zmodyfikować ustawienia.

   ![Przycisk "Edytuj" dla roli operatora](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. W **aktywacji** zaznacz **wymagają zatwierdzenia, aby aktywować** pole wyboru.

   !["Aktywacja" części ustawień roli](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Określanie osób zatwierdzających

Kliknij przycisk **wybierz osób zatwierdzających** otworzyć **Wybieranie użytkownika lub grupy** okienka.

>[!NOTE]
>Musisz wybrać co najmniej jednego użytkownika lub grupę, aby zaktualizować ustawienia. Nie ma żadnych osób zatwierdzających domyślne.

Zasób Administratorzy mogą dodawać dowolną kombinację użytkowników i grup do listy. 

!["Wybierz użytkownika lub grupę" okienko z użytkownikiem, który został wybrany](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Żądania zatwierdzenia aktywacji

Oczekiwanie na zatwierdzenie nie ma wpływu na tej procedury, która jest elementem członkowskim należy wykonać, aby aktywować. [Przejrzyj kroki, aby aktywować rolę](pim-resource-roles-activate-your-roles.md).

Jeśli element członkowski żądanie aktywacji roli, która wymaga zatwierdzenia i rola nie jest już wymagane, element członkowski można anulować żądania w PIM.

Aby anulować, przejdź do usługi PIM i wybierz **Moje żądania**. Znajdź żądania i wybierz **anulować**.

![Okienko "Moje żądania"](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Zatwierdzanie lub odrzucanie żądań

Zatwierdzanie lub odrzucanie żądań, użytkownik musi należeć do listy osoby zatwierdzającej. 

1. PIM, wybierz **zatwierdzać żądania** z karty w menu po lewej stronie i Znajdź żądania.

   ![W okienku "Zatwierdzać żądania"](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Wybierz żądanie, podaj uzasadnienie decyzji, a następnie wybierz **Zatwierdź** lub **Odmów**. Żądanie jest rozpoznana.

   ![Wybrane żądanie szczegółowe informacje](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Powiadomienia dotyczące przepływu pracy

Poniżej przedstawiono informacje na temat powiadomień przepływu pracy:

- Wszystkie elementy członkowskie na liście osoba zatwierdzająca są powiadomienie e-mail podczas ich przeglądu oczekuje na żądanie dla roli. Powiadomienia e-mail zawierają bezpośredniego łącza do żądania, gdzie osoba zatwierdzająca można zatwierdzać lub odrzucać.
- Żądania są rozpoznawane przez pierwszego elementu członkowskiego z listy, który zaakceptuje lub nie zezwala na. 
- Gdy osoba zatwierdzająca odpowiada na żądanie, wszystkie elementy członkowskie na liście osoba zatwierdzająca jest powiadamiany o akcji. 
- Administratorzy zasobów są powiadamiani, kiedy składnik zatwierdzone staje się aktywny w danej roli. 

>[!Note]
>Administrator zasobu, który określa, że zatwierdzone Członkowskie nie powinny być active można usunąć przypisania roli active w PIM. Mimo że administratorzy zasobu nie jest powiadamiany o oczekujących żądań o ile nie są oni członkami listy Osoba zatwierdzająca, mogą wyświetlać i anulowania oczekującego żądania wszystkich użytkowników, wyświetlając żądań oczekujących w PIM. 

## <a name="next-steps"></a>Kolejne kroki

[Zastosuj ustawienia PIM do unikatowych grup użytkowników](pim-resource-roles-custom-role-policy.md)
