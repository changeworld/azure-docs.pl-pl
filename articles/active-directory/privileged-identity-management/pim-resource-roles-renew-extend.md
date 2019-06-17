---
title: Rozszerzanie lub odnawianie przypisań ról zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzanie lub odnawianie przypisań ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a064fc67bf94ba6aa443e429fe83179d84cada84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602695"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Rozszerzanie lub odnawianie przypisań ról zasobów platformy Azure w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) wprowadza nowe formanty do zarządzania cyklem życia dostępu i przypisania dla zasobów platformy Azure. Administratorzy mogą przypisywać członkostwa przy użyciu właściwości Data / Godzina rozpoczęcia i zakończenia. Gdy zbliża się zakończenie przypisania PIM wysyła powiadomienia e-mail do użytkowników lub grup. Wysyła również powiadomienia e-mail administratorom zasobu w celu zapewnienia utrzymania odpowiedni dostęp. Zadania mogą być odnawiane i będą one nadal widoczne wygasła przez maksymalnie 30 dni, nawet jeśli nie zostanie przedłużony dostęp.

## <a name="who-can-extend-and-renew"></a>Kto może rozszerzyć i odnowić?

Tylko administratorzy zasobu można rozszerzyć lub odnawiania przypisań ról. Dotyczy składowej może poprosić o przedłużenie role, które wkrótce wygasną i limit czasu żądania odnowienia role, które są już wygasł.

## <a name="when-are-notifications-sent"></a>Kiedy są wysyłane powiadomienia

Usługa PIM wysyła powiadomienia e-mail dla administratorów i odpowiednich członków ról, które wkrótce wygasną w ciągu 14 dni i jeden dzień przed wygaśnięciem. Wysyła wiadomość e-mail dodatkowych wygaśnięcia przydziału oficjalnie. 

Administratorzy otrzymywać powiadomienia, gdy członek roli wygasające lub wygasłe żądania rozszerzanie lub odnawianie. Podczas określonego administratora jest rozpoznawany jako żądania, inni administratorzy powiadomienie o decyzji rozdzielczość (zatwierdzenie lub odrzucenie). Następnie żądania elementu członkowskiego zostanie powiadomiony o decyzji. 

## <a name="extend-role-assignments"></a>Rozszerzenia przypisań ról

Poniższe kroki wchodzą w skład procesu żądania, rozwiązywanie lub administrowania rozszerzenia lub odnowienia przypisania roli. 

### <a name="member-extend"></a>Element członkowski rozszerzenia

Elementy członkowskie przypisania roli można rozszerzyć wygasające przypisań ról bezpośrednio z **kwalifikujących się** lub **Active** karcie **Moje role** stronę zasobu i na najwyższym poziomie **Moje role** stronie portalu usługi PIM. Członkowie mogą poprosić o przedłużenie kwalifikujących się i aktywnych ról (przypisanych), które wygaśnie w ciągu najbliższych 14 dni.

![Rozszerzanie ról](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Gdy przypisanie Data / Godzina zakończenia jest w ciągu 14 dni, przycisk, aby **Rozszerz** staje się aktywny odnośnik w interfejsie użytkownika. W poniższym przykładzie przyjęto założenie, bieżąca data jest 27 marca.

![Rozszerzanie przycisku](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Aby wnioskiem o przedłużenie okresu tego przypisania ról, wybierz pozycję **Rozszerz** można otworzyć formularz żądania.

![Otwórz formularz żądania](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Aby wyświetlić informacje o przypisywaniu oryginalnej, rozwiń węzeł **Szczegóły przypisania**. Podaj przyczynę żądania rozszerzenia, a następnie wybierz **Rozszerz**.

>[!Note]
>Firma Microsoft zaleca, łącznie ze szczegółami, dlaczego wymagane jest rozszerzenie oraz jak długo może być przyznany rozszerzenia (Jeśli masz te informacje).

![Rozszerzanie przypisania roli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

W ciągu kilku chwil administratorom zasobów otrzymywać powiadomienia e-mail żądania, przeglądu żądanie rozszerzenia. Jeśli zostało już przesłane żądanie, aby rozszerzyć, w górnej części witryny Azure portal, wyjaśniające, błąd pojawia się powiadomienie wyskakujące.

![Powiadomienie wyjaśniające, błąd](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Przejdź do **oczekujących żądań** karty w okienku po lewej stronie, aby wyświetlić stan swojego żądania lub Anuluj ją.

![Oczekujące żądania](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Zatwierdzenie administratora

Gdy członek przesyła żądanie rozszerzenia przypisania roli, administratorom zasobów otrzymywać powiadomienia e-mail, który zawiera szczegółowe informacje, oryginalnym przypisania i przyczynę żądania. Powiadomienie zawiera bezpośredni link do żądania dla administratora zatwierdzić lub odrzucić. 

Oprócz korzystając z następującego linku z wiadomości e-mail, administratorzy mogli zatwierdzać lub odrzucać żądania, przechodząc do administrację usługi PIM w portalu, a następnie polecenie **zatwierdzanie żądań** w okienku po lewej stronie.

![Zrzut ekranu przedstawiający błąd](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Gdy Administrator wybierze **Zatwierdź** lub **Odmów**, są wyświetlane szczegóły żądania, oraz pole, aby podać uzasadnienie dla dzienników inspekcji.

![Zatwierdź żądanie przypisania roli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Po zatwierdzeniu żądanie rozszerzenia przypisania roli administratorom zasobów można wybrać nową datę rozpoczęcia, Data zakończenia i typ przypisania. Zmiana typu przypisania, może być konieczne, jeśli administrator chce, aby zapewnić ograniczony dostęp do wykonania konkretnego zadania (jeden dzień, na przykład). W tym przykładzie administrator może zmienić przypisanie z **kwalifikujących się** do **Active**. Oznacza to, że można zapewniają dostęp do obiektu żądającego bez konieczności ich do aktywowania.

### <a name="admin-extend"></a>Przedłużenie administratora

Członek roli zapomni lub jest w stanie wnioskiem o przedłużenie członkostwa w roli, administrator można rozszerzyć przypisania imieniu elementu członkowskiego. Rozszerzenia administracyjne członkostwo roli nie wymagają zatwierdzenia, ale powiadomienia są wysyłane do innych administratorów po został rozszerzony roli.

Aby rozszerzyć członkostwo w roli, przejdź do widoku roli lub elementu członkowskiego zasobów w usłudze PIM. Znajdź element członkowski, który wymaga rozszerzenia. Następnie wybierz pozycję **Rozszerz** w kolumnie akcji.

![Rozszerzanie członkostwo w roli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Odnów przypisań ról

Gdy zachowuje się podobnie jak proces do żądania rozszerzenia, procesu odnowienia przypisania roli wygasłe różni się. Wykonując następujące kroki, członków i administratorów można odnowić dostęp do wygasłe role, gdy jest to konieczne.

### <a name="member-renew"></a>Element członkowski odnowienia

Członkowie, którzy nie mogą uzyskać dostępu do zasobów mogą uzyskiwać dostęp do 30 dni historii wygasłe przypisania. Aby to zrobić, przejdź do **Moje role** w okienku po lewej stronie, a następnie wybierz **wygasłe role** kartę w sekcji role zasobów platformy Azure.

![Karta wygasłe role](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Na liście ról pokazano wartość domyślna to **kwalifikujące się role**. Użyj menu rozwijanego menu, aby przełączać się między kwalifikujących się i aktywnych przypisane role.

Żądanie odnowienia dla każdego przypisania roli na liście, wybierz **Odnów** akcji. Podaj przyczynę żądania. Warto Podaj czas oprócz dodatkowy kontekst, ułatwiające administrator zasobów zdecydować zatwierdzić lub odrzucić.

![Odnowienia przypisania roli](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Po przesłaniu żądania administratorom zasobów jest powiadamiany o oczekujące żądanie odnowienia przypisania roli.

### <a name="admin-approves"></a>Zatwierdza administratora

Administratorom zasobów mogą uzyskiwać dostęp do żądania odnowienia z linku powiadomienia e-mail lub uzyskiwanie dostępu do usługi PIM w witrynie Azure portal i wybierając **zatwierdzanie żądań** z okienka po lewej stronie.

![Zatwierdzanie żądań](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Gdy administrator wybierze **Zatwierdź** lub **Odmów**, Szczegóły żądania są wyświetlane wraz z polem, aby podać uzasadnienie dla dzienników inspekcji.

![Zatwierdź przypisanie roli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Po zatwierdzeniu żądania odnowienia przypisania roli administratorom zasobów musisz wprowadzić nową datę rozpoczęcia, Data zakończenia i typ przypisania. 

### <a name="admin-renew"></a>Odnowienie administratora

Administratorom zasobów można odnowić przypisań ról wygasłe z **członków** kartę w lewym menu nawigacji zasobu. Przypisania ról wygasłe z poziomu ich odnowić także **wygasłe** kartę role roli zasobu.

Aby wyświetlić listę wszystkich wygasła przypisań ról **członków** ekranu, wybierz opcję **wygasłe role**.

![Wygasłe role](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Kolejne kroki

- [Zatwierdź lub Odrzuć żądania dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
