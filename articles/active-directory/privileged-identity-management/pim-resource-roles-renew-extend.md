---
title: Privileged Identity Management zasobów Azure - rozszerzania i odnawiania role | Dokumentacja firmy Microsoft
description: Ten dokument zawiera opis sposobu rozszerzania i odnawiania role zasobów platformy Azure dla zasobów usługi PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Zarządzanie tożsamościami uprzywilejowanymi - role zasobów — rozszerzenie lub odnowić

PIM dla zasobów systemu Azure wprowadzono nowe kontrolki do zarządzania cykl dostępu i przypisania dla zasobów platformy Azure. Administratorzy mogą przypisywać członkostwa przy użyciu właściwości daty i godziny rozpoczęcia i zakończenia. Gdy zbliża się koniec przypisania PIM wysyła powiadomienia e-mail do odpowiednich członków (to może być użytkownik lub grupa) i administratorom zasobu, które zapewni odpowiedni dostęp. W przypadku dostępu nie jest przedłużana z powodu braku działań ze strony, zadania mogą być odnawiane i pozostaje widoczna w stanie wygaśnięcia przez maksymalnie 30 dni.

## <a name="who-can-extend-and-renew"></a>Kto rozszerzania i odnawiania?

Tylko administratorzy zasobu można rozszerzyć lub odnowić przypisań ról. Dotyczy elementu członkowskiego można żądanie rozszerzenia ról, które niedługo wygasną i żądania odnowienia ról już wygasł.

## <a name="when-are-notifications-sent"></a>Kiedy mają być wysyłane powiadomienia?

PIM wysyła powiadomienia e-mail dla administratorów i odpowiednich członków ról wygasa w ciągu ostatnich 14 dni i jeden dzień przed jego wygaśnięciem. Po wygaśnięciu oficjalnie przypisania, zostanie wysłana wiadomość e-mail dodatkowych. 

Administratorzy otrzymywanie powiadomień o członkiem roli wygasające lub wygasła żądania do rozszerzania lub odnowić. Gdy określonego administratora rozpoznaje żądanie innych administratorów są powiadamiani o decyzji rozdzielczości (zatwierdzenie lub odrzucenie) i żądania elementu członkowskiego jest powiadamiany o decyzji. 

## <a name="extend-role-assignments"></a>Rozszerzanie przypisań ról

Poniższe kroki przedstawiają kroki i interfejsu użytkownika związane z żądania, rozwiązywanie lub administrowania rozszerzenia lub odnowienie przypisania roli. 

### <a name="member-extend"></a>Rozszerzanie elementu członkowskiego

Elementy członkowskie przypisania roli można żądać rozszerzenie wygasające przypisań ról bezpośrednio z "Uprawniających" lub "Active" karcie na "Mój ról" strony zasobu i z najwyższym poziomie Moje ról w portalu usługi PIM. Elementy Członkowskie mogą żądać rozszerzenie kwalifikujących się i aktywne (przypisane) role, które wygaśnie w ciągu następnych 14 dni.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

W przypadku przypisania celu daty i godziny w ciągu ostatnich 14 dni, przycisk "Rozszerz" staną się aktywnego łącza w interfejsie użytkownika. W poniższym przykładzie założono 27 marca jest data bieżąca.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Aby poprosić o przedłużenie tej roli przypisania, kliknij przycisk "Rozszerz", aby otworzyć formularz żądania.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Rozwiń węzeł "Szczegóły przypisania" Aby wyświetlić informacje o przypisywaniu oryginalnego. Wprowadź przyczynę żądania rozszerzenia, a następnie kliknij polecenie "Rozszerz".

>[!Note]
>Firma Microsoft zaleca, łącznie ze szczegółami, dlaczego wymagane jest rozszerzenie i jak długo powinna mieć rozszerzenie (jeśli jest znane).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

W ciągu kilku chwil zasobów Administratorzy otrzymają wiadomość e-mail z powiadomieniem proces żądania przeglądu żądanie rozszerzenia. Jeśli żądanie rozszerzenie zostało już przesłane, wyskakujących powiadomień będą wyświetlane w górnej części portalu Azure wyjaśniający błąd.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Można znaleźć na karcie "oczekujące żądania" w menu nawigacji po lewej stronie, aby wyświetlić stan lub anulować żądanie.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Zatwierdzenie administratora

Gdy członek przesyła żądanie, aby rozszerzyć przypisania roli, Administratorzy zasobów otrzymywać wiadomość e-mail z powiadomieniem zawierające szczegóły oryginalnego przypisania i Przyczyna dostarczony przez obiekt żądający. Powiadomienie zawiera łącze do żądania dla administratora do zatwierdzenia lub odrzucenia. 

Oprócz następujące łącze w wiadomości e-mail, Administratorzy mogą zatwierdzać lub odrzucać żądania, przechodząc do portalu administracyjnego usługi PIM i wybierając z menu nawigacji po lewej stronie "Zatwierdź żądania".

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Jeśli Administrator może wybrać opcję Zatwierdź lub Odmów szczegóły żądania są wyświetlane wraz z polem uzasadnić dzienników inspekcji.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Podczas zatwierdzania żądania, aby rozszerzyć przypisania roli, zasobów, Administratorzy mogą wybrać nowego początkową i końcową daty i godziny oraz przypisanie typu. Zmiana typu przydziału może być konieczne, jeśli administrator chce dostarczyć ograniczony dostęp do wykonania określonego zadania (jeden dzień na przykład). W tym przykładzie administrator może zmienić przypisanie z kwalifikujących się na aktywny, udostępniając obiektu żądającego bez konieczności je aktywować.

### <a name="admin-extend"></a>Rozszerzanie administratora

W przypadku członka roli zapomni lub nie może żądać rozszerzenia członkostwo roli administrator może rozszerzać przypisania imieniu elementu członkowskiego. Administracyjne rozszerzenia członkostwo roli nie wymagają zatwierdzenia, ale powiadomienia są wysyłane do innych administratorów po zakończeniu rozszerzenia roli.

Do rozszerzenia roli członkostwa, przejdź do widoku roli lub elementu członkowskiego zasobów w PIM. Find — członek, wymagających rozszerzenie, a następnie kliknij polecenie "Rozszerz" w kolumnie akcji.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Odnów przypisań ról

Gdy podobny, Odnów wygasłe rolą proces różni się od żąda rozszerzenie zarówno członków, jak i administratorów. Wykonując kroki poniższych elementów członkowskich, a administratorzy mogą odnowić dostęp do ról wygasłe, gdy jest to konieczne.

### <a name="member-renew"></a>Odnów elementu członkowskiego

Członkowie, którzy nie mogą uzyskać dostępu do zasobów można uzyskać dostępu do maksymalnie 30 dni historii przypisania wygasłe przechodząc do Moje ról na lewym pasku nawigacyjnym PIM i wybierając kartę "Wygasł role" w sekcji role zasobów platformy Azure.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Lista ról wyświetlane domyślnie przypisania kwalifikujących się. Użyj listy rozwijanej do przełączania między uprawniających i aktywne przypisane role.

Żądanie odnowienia dla żadnej roli przypisań na liście wybierz akcję "Odnawiania" i podaj przyczynę żądania. Warto Podaj czas trwania oprócz dodatkowy kontekst, które mogą pomóc podjąć decyzję o zatwierdzenie lub odrzucenie administrator zasobów.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Po przesłaniu żądania Administratorzy zasobów jest powiadamiany o oczekujące żądanie odnowienia przypisania roli.

### <a name="admin-approves"></a>Zatwierdza administratora

Administratorzy zasobów są dostępne żądanie odnowienia łącze w powiadomieniu e-mail lub uzyskiwanie dostępu do usługi PIM w portalu Auzre, a następnie wybierając polecenie "Zatwierdź żądania" w menu nawigacji po lewej stronie.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Jeśli administrator może wybrać opcję Zatwierdź lub Odmów szczegóły żądania są wyświetlane wraz z polem uzasadnić dzienników inspekcji.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Podczas zatwierdzania żądania odnowienia przypisania roli, Administratorzy zasobu musi wprowadzić nowe początkową i końcową daty i godziny oraz przypisanie typu. 

### <a name="admin-renew"></a>Odnów administratora

Administratorzy zasobów mogą odnowić przypisań ról wygasłe na karcie elementy członkowskie w menu nawigacji po lewej stronie zasobu lub z poziomu kartę role wygasłe roli zasobu.

Na ekranie członków wybierz wygasłe role, aby wyświetlić listę wszystkich przypisań ról wygasły.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Kolejne kroki

[Wymagaj zatwierdzenia aktywacji](pim-resource-roles-approval-workflow.md)

[Aktywować rolę](pim-resource-roles-use-the-audit-log.md)


