---
title: Przepływ pracy zatwierdzania dla ról zasobów platformy Azure w Privileged Identity Management | Dokumentacja firmy Microsoft
description: Zawiera opis procesu przepływu pracy zatwierdzania dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dcb306bda8ef7d93314390bd9a90327ffdd14a0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619641"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Przepływ pracy zatwierdzania dla ról zasobów platformy Azure w Privileged Identity Management

Za pomocą przepływu pracy zatwierdzania w Privileged Identity Management (PIM) dla ról zasobów platformy Azure Administratorzy mogą dodatkowo ochrony lub ograniczyć dostęp do kluczowych zasobów. Oznacza to, że administratorzy mogą wymagać od zatwierdzenia do aktywowania przypisań ról. 

Koncepcja hierarchii zasobu jest unikatowe dla ról zasobów platformy Azure. Taka hierarchia umożliwia dziedziczenia przypisań ról z obiektu zasobu nadrzędnego w dół do wszystkich zasobów podrzędnych w kontenerze nadrzędnym. 

Na przykład: Roberta, administratorem zasobów przypisuje Alicja jako kwalifikującego się elementu członkowskiego do roli właściciel, w ramach subskrypcji firmy Contoso przy użyciu usługi PIM. Z tym przypisaniem Alicja jest kwalifikujących się właścicielem wszystkich kontenerów grupy zasobów w ramach subskrypcji firmy Contoso. Alicja jest również właścicielem kwalifikujących się wszystkie zasoby (takie jak maszyny wirtualne) w każdej grupie zasobów subskrypcji. 

Załóżmy, że istnieją trzy grupy zasobów w subskrypcji firmy Contoso: testów firmy Fabrikam, deweloperów firmy Fabrikam i produkcyjne firmy Fabrikam. Każda z tych grup zasobów zawiera jedną maszynę wirtualną.

Ustawień usługi PIM są skonfigurowane dla każdej roli zasobu. W przeciwieństwie do przypisania te ustawienia nie są dziedziczone i zastosować wyłącznie do roli zasobu. [Dowiedz się więcej o kwalifikującymi się przypisaniami i widoczność zasobów](pim-resource-roles-eligible-visibility.md).

Przykładzie: Robert korzysta z usługi PIM będą musieli wszystkich elementów członkowskich w roli właściciela zatwierdzenia żądania subskrypcji Contoso zostanie uaktywniony. Aby lepiej chronić zasoby w grupie zasobów Prod firmy Fabrikam, Robert wymaga zatwierdzenia dla członków roli właściciela tego zasobu. Role właściciela w firmie Fabrikam testowych i deweloperskich firmy Fabrikam nie wymagają zatwierdzenia aktywacji.

Gdy Alicja żądań aktywacji jej roli właściciela subskrypcji firmy Contoso, osoba zatwierdzająca musi zatwierdzić lub odrzucić swoje żądanie, zanim użytkownik staje się aktywny w roli. Jeśli Alicja zdecyduje się na [zakres jej aktywacji](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) do grupy zasobów Prod firmy Fabrikam, osoba zatwierdzająca musi Zatwierdź lub Odrzuć to żądanie zbyt. Ale jeśli Alicja zdecyduje się na zakres jej aktywacji do jednego lub obu tych testów Fabrikam lub deweloperów firmy Fabrikam, zatwierdzenia nie jest wymagane.

Przepływ pracy zatwierdzania nie może być wymagane dla wszystkich członków roli. Rozważmy scenariusz, w których Twoja organizacja zatrudnia kilka kojarzy umowy, aby pomóc w rozwoju aplikacji, która jest uruchamiana w subskrypcji platformy Azure. Będąc administratorem zasobów pracownicy mają mieć dostępu uprawnionego bez wymagane zatwierdzenie, ale kojarzy kontraktu musi on zażądać zatwierdzenia. Aby skonfigurować przepływ pracy zatwierdzania dla tylko kojarzy kontraktu, utworzeniem roli niestandardowej takie same uprawnienia, jak rola przypisana do pracowników. Może wymagać zatwierdzenia do aktywowania tej roli niestandardowej. [Dowiedz się więcej o niestandardowych rolach](pim-resource-roles-custom-role-policy.md).

Aby skonfigurować przepływ pracy zatwierdzania i określ, kto może zatwierdzać lub odrzucać żądania, należy użyć poniższych procedur.

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia w celu aktywacji

1. Przejdź do usługi PIM w witrynie Azure portal, a następnie wybierz zasób z listy.

   ![Okienko "Zasobów platformy azure" z wybranego zasobu](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. W okienku po lewej stronie wybierz **ustawień roli**.

3. Wyszukaj i wybierz rolę, a następnie wybierz **Edytuj** na modyfikowanie ustawień.

   ![Przycisk "Edit" dla roli operatora](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. W **aktywacji** zaznacz **Wymagaj zatwierdzenia do aktywowania** pole wyboru.

   ![Sekcja "Aktywacja" Ustawienia roli](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Określanie osób zatwierdzających

Kliknij przycisk **wybierz osoby zatwierdzające** otworzyć **zaznacz użytkownika lub grupę** okienka.

>[!NOTE]
>Musisz wybrać co najmniej jednego użytkownika lub grupę, aby zaktualizować ustawienia. Nie istnieją żadne osoby zatwierdzające domyślne.

Zasób Administratorzy mogą dodawać dowolną kombinację użytkowników i grup, do listy osób zatwierdzających. 

![W okienku "Wybierz użytkownika lub grupy" przy użyciu wybranego przez użytkownika](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Żądania zatwierdzenia do aktywowania

Żądanie zatwierdzenia nie ma wpływu na procedury, która członka należy wykonać, aby aktywować. [Przejrzyj procedurę, aby aktywować rolę](pim-resource-roles-activate-your-roles.md).

Jeśli członek żądanie aktywacji roli wymagającej zatwierdzenia, rola nie jest już wymagany element członkowski może anulować żądanie w usłudze PIM.

Aby anulować, przejdź do usługi PIM, a następnie wybierz pozycję **Moje żądania**. Zlokalizuj na żądanie i wybierz **anulować**.

![Okienko "Moje żądania"](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Zatwierdź lub Odrzuć żądania

Aby zatwierdzić lub odrzucić żądanie, musi być członkiem listy osoby zatwierdzającej. 

1. W usłudze PIM, wybierz **zatwierdzanie żądań** z karty w menu po lewej stronie i Znajdź żądania.

   ![Okienko "Zatwierdź żądania"](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Wybierz żądanie, podać uzasadnienie, aby decyzję, a następnie wybierz **Zatwierdź** lub **Odmów**. Następnie usunięciu żądania.

   ![Wybrane żądania wraz ze szczegółowymi informacjami](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Powiadomienia dotyczące przepływu pracy

Poniżej przedstawiono informacje na temat powiadomienia dotyczące przepływu pracy:

- Wszyscy członkowie listy Osoba zatwierdzająca powiadomienie za pośrednictwem poczty e-mail, gdy żądanie dla roli oczekuje na ich przeglądu. Powiadomienia e-mail zawierają bezpośredni link do żądania, gdy osoba zatwierdzająca mogli zatwierdzać lub odrzucać.
- Żądania są rozpoznawane przez pierwszego elementu członkowskiego listy, która zatwierdza lub odrzuca. 
- Gdy osoba zatwierdzająca odpowiada na żądanie, wszyscy członkowie listy Osoba zatwierdzająca powiadomienie o akcji. 
- Administratorom zasobów są powiadamiani, gdy zatwierdzonego elementu członkowskiego staje się aktywny w danej roli. 

>[!Note]
>Administrator zasobu, który uważa, że zatwierdzonego elementu członkowskiego nie powinien być aktywny, można usunąć przypisania roli active w usłudze PIM. Mimo że administratorom zasobów nie są powiadamiani o oczekujących żądań, chyba że są one członków listy osoby zatwierdzającej, mogą wyświetlać i anulować oczekujące żądania wszystkim użytkownikom wyświetlanie oczekujących żądań w usłudze PIM. 

## <a name="next-steps"></a>Kolejne kroki

[Stosowanie ustawień usługi PIM dla unikatowych grup użytkowników](pim-resource-roles-custom-role-policy.md)
