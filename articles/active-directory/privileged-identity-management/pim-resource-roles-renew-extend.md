---
title: Rozszerzanie i przejrzeć role w zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Ten dokument zawiera opis sposobu rozszerzania i odnawiania role zasobów platformy Azure dla zasobów usługi PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: cd1524bf03256a2ed706d11a8702c7b5eff5dad4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260509"
---
# <a name="extend-and-review-roles-in-azure-resources-by-using-privileged-identity-management"></a>Rozszerzanie i przejrzeć role w zasobów platformy Azure przy użyciu Privileged Identity Management

Uprzywilejowane tożsamości zarządzania (PIM) dla zasobów platformy Azure wprowadza nowe kontrolki do zarządzania cykl dostępu i przypisania dla zasobów platformy Azure. Administratorzy mogą przypisywać członkostwa przy użyciu właściwości daty i godziny rozpoczęcia i zakończenia. Gdy zbliża się koniec przypisania PIM wysyła powiadomienia e-mail do użytkowników lub grup. Wysyła również powiadomienia e-mail do administratorom zasobu zapewni, że odpowiedni dostęp. Zadania mogą być odnawiane i pozostaje widoczna w stanie wygaśnięcia przez maksymalnie 30 dni, nawet jeśli nie zostanie rozszerzony dostępu.

## <a name="who-can-extend-and-renew"></a>Kto rozszerzania i odnawiania?

Tylko administratorzy zasobu można rozszerzyć lub odnowić przypisań ról. Dotyczy elementu członkowskiego można żądanie rozszerzenia ról, które zamierzasz wygaśnie i zażądać odnowienia ról, które są już wygasł.

## <a name="when-are-notifications-sent"></a>Kiedy mają być wysyłane powiadomienia?

PIM wysyła powiadomienia e-mail dla administratorów i odpowiednich członków ról, które są wygasa w ciągu najbliższych 14 dni oraz jeden dzień przed jego wygaśnięciem. Wysyła wiadomość e-mail dodatkowych wygaśnięcia przypisania oficjalnie. 

Administratorzy otrzymywanie powiadomień o członkiem roli wygasające lub wygasła żądania do rozszerzania lub odnowić. Gdy rozpoznaje określonego administratora, innych administratorów jest powiadamiany o decyzji rozdzielczości (zatwierdzenie lub odrzucenie). Następnie żądania elementu członkowskiego jest powiadamiany o decyzji. 

## <a name="extend-role-assignments"></a>Rozszerzanie przypisań ról

Poniższe kroki wchodzą w skład procesu żądania, rozwiązywanie lub administrowania rozszerzenia lub odnowienie przypisania roli. 

### <a name="member-extend"></a>Przedłużenie przypisania dla członka

Elementy członkowskie przypisania roli można rozszerzyć wygasające przypisań ról bezpośrednio z **kwalifikujących się** lub **Active** karcie na **Moje ról** strony zasobu i z najwyższego poziomu **Moje ról** portalu usługi PIM. Elementy Członkowskie mogą żądać rozszerzenie kwalifikujących się i aktywne (przypisane) role, które wygaśnie w ciągu następnych 14 dni.

![Rozszerzanie ról](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Podczas przypisywania celu daty i godziny jest w ciągu ostatnich 14 dni, przycisk, aby **Rozszerz** staje się aktywnego łącza w interfejsie użytkownika. W poniższym przykładzie założono 27 marca jest data bieżąca.

![Przycisk "Rozszerz"](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Aby poprosić o przedłużenie przypisania roli, wybierz **Rozszerz** aby otworzyć formularz żądania.

![Otwórz formularz żądania](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Aby wyświetlić informacje o przypisywaniu oryginalnej, rozwiń węzeł **Szczegóły przypisania**. Wprowadź przyczynę żądania rozszerzenia, a następnie wybierz **Rozszerz**.

>[!Note]
>Firma Microsoft zaleca, łącznie ze szczegółami, dlaczego wymagane jest rozszerzenie i jak długo może być przyznany rozszerzenia (Jeśli te informacje).

![Rozszerzanie przypisania roli](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

W ciągu kilku minut Administratorzy zasobów odbierania powiadomień e-mail żądania, przeglądu żądanie rozszerzenia. Jeśli już zostało przesłane żądanie, aby rozszerzyć, w górnej części portalu Azure wyjaśniający błąd pojawia się powiadomienie wyskakujące.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Przejdź do **oczekujących żądań** karty w okienku po lewej stronie, aby wyświetlić stan żądania lub Anuluj ją.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Zatwierdzenie administratora

Gdy członek przesyła żądanie, aby rozszerzyć przypisania roli, zasobów administratorzy będą otrzymywać powiadomienia e-mail, zawierający szczegóły oryginalnego przypisania i przyczynę żądania. Powiadomienie zawiera łącze do żądania dla administratora do zatwierdzenia lub odrzucenia. 

Oprócz przy użyciu następującego łącza z wiadomości e-mail, Administratorzy mogą zatwierdzać lub odrzucać żądania, przechodząc do pozycji Administracja PIM portalu i wybierając **zatwierdzać żądania** w okienku po lewej stronie.

![Zrzut ekranu przedstawiający błąd](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Jeśli Administrator może wybrać opcję **Zatwierdź** lub **Odmów**, są wyświetlane szczegóły żądania, wraz z pola do uzasadnić dzienników inspekcji.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Podczas zatwierdzania żądania, aby rozszerzyć przypisania roli, zasobów Administratorzy mogą wybrać, Nowa data rozpoczęcia, Data zakończenia i typ przypisania. Zmiana typu przydziału może być konieczne, jeśli administrator chce dostarczyć ograniczony dostęp do wykonania określonego zadania (jeden dzień, na przykład). W tym przykładzie administrator może zmienić przypisanie z **kwalifikujących się** do **Active**. Oznacza to, że można zapewniają dostęp do obiektu żądającego bez konieczności je aktywować.

### <a name="admin-extend"></a>Przedłużenie administratora

Członek roli zapomni lub nie może żądać rozszerzenia członkostwo roli, administrator można rozszerzyć przypisania imieniu elementu członkowskiego. Administracyjne rozszerzenia członkostwo roli nie wymagają zatwierdzenia, ale powiadomienia są wysyłane do innych administratorów po roli została rozszerzona.

Aby rozszerzyć członkostwo roli, przejdź do widoku zasobów roli lub elementu członkowskiego w PIM. Find — członek, który wymaga rozszerzenia. Następnie wybierz **Rozszerz** w kolumnie akcji.

![Rozszerzanie członkostwo roli](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Odnów przypisań ról

Gdy podobny do procesu o rozszerzenie, Odnów wygasłe rolą proces różni się. Wykonując poniższe kroki, członków i Administratorzy mogą odnowić dostęp do ról wygasłe, gdy jest to konieczne.

### <a name="member-renew"></a>Odnawianie przypisania dla członka

Członkowie, którzy nie mogą uzyskać dostępu do zasobów mają dostęp do 30 dni historii wygasłe przypisania. Aby to zrobić, przejdź do **Moje ról** w okienku po lewej stronie, a następnie wybierz **ważność ról** kartę w sekcji role zasobów platformy Azure.

![Na karcie "Ról wygasł"](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Lista ról, wartością domyślną będzie wyświetlany **kwalifikujących się role**. Użyj menu rozwijanego do przełączania między uprawniających i aktywne przypisane role.

Żądanie odnowienia jakichkolwiek przypisań ról na liście, wybierz **odnawiania** akcji. Następnie podaj przyczynę żądania. Warto Podaj czas trwania oprócz dodatkowy kontekst, ułatwiająca podjąć decyzję o zatwierdzenie lub odrzucenie administrator zasobów.

![Odnów przypisania roli](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Po przesłaniu żądania Administratorzy zasobów jest powiadamiany o oczekujące żądanie odnowienia przypisania roli.

### <a name="admin-approves"></a>Zatwierdza administratora

Administratorzy zasobów mogą uzyskiwać dostęp do żądania odnowienia z łącza w powiadomieniu e-mail lub uzyskiwanie dostępu do usługi PIM w portalu Azure i wybierając **zatwierdzać żądania** w lewym okienku.

![Zatwierdź żądania](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Jeśli administrator może wybrać opcję **Zatwierdź** lub **Odmów**, są wyświetlane szczegóły żądania, wraz z polem do uzasadnić dzienników inspekcji.

![Zatwierdzanie przypisania roli](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Podczas zatwierdzania żądania odnowienia przypisania roli, Administratorzy zasobów wprowadź nową datę rozpoczęcia, Data zakończenia i typ przypisania. 

### <a name="admin-renew"></a>Odnowienie administratora

Zasobów Administratorzy mogą odnowić przypisania roli wygasłe z **członków** kartę w menu nawigacji po lewej stronie zasobu. One również odnowić przypisań ról wygasłe z poziomu **wygasłe** kartę role roli zasobu.

Aby wyświetlić listę wszystkich wygasła przypisań ról **członków** ekranu wybierz **ważność ról**.

![Wygasłe role](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Następne kroki

[Wymagaj zatwierdzenia aktywacji](pim-resource-roles-approval-workflow.md)

[Aktywować rolę](pim-resource-roles-use-the-audit-log.md)


