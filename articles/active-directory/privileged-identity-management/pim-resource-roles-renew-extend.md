---
title: Przedłużanie lub odnowienie przypisań ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przedłużyć lub odnowić przypisania ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eeba148945f7aa52dd32edc0fec4c45e2ab0748
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804131"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Przedłużanie lub odnowienie przypisań ról zasobów platformy Azure w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) wprowadza nowe kontrolki do zarządzania cyklem życia dostępu i przypisania dla zasobów platformy Azure. Administratorzy mogą przypisywać członkostwo przy użyciu właściwości daty i godziny rozpoczęcia i zakończenia. Gdy zbliża się koniec przypisania, PIM wysyła powiadomienia e-mail do odpowiednich użytkowników lub grup. Wysyła również powiadomienia e-mail do administratorów zasobu, aby zapewnić zachowanie odpowiedniego dostępu. Przypisania mogą zostać odnowione i pozostają widoczne w stanie wygaśnięcia przez maksymalnie 30 dni, nawet jeśli nie przedłużono dostępu.

## <a name="who-can-extend-and-renew"></a>Kto może ją przedłużać i odnowić?

Tylko Administratorzy zasobu mogą przedłużyć lub odnowić przypisania ról. Dotknięty członek może zażądać przedłużenia ról, które wkrótce wygasną, i zażądać odnowienia ról, które już wygasły.

## <a name="when-are-notifications-sent"></a>Kiedy są wysyłane powiadomienia?

W programie PIM powiadomienia e-mail są wysyłane do administratorów i podlegają członkom ról, których ważność wygaśnie w ciągu 14 dni i jednego dnia przed wygaśnięciem. Wysyła on dodatkową wiadomość e-mail, gdy przydział oficjalnie wygaśnie. 

Administratorzy otrzymują powiadomienia, gdy członek wygaśnięcia lub ważności żądania roli do przedłużenia lub odnowienia. Gdy określony administrator rozwiązuje żądanie, wszyscy inni administratorzy są powiadamiani o decyzji o rozwiązywaniu (zatwierdzone lub odrzucone). Następnie członek wnioskujący zostanie powiadomiony o decyzji. 

## <a name="extend-role-assignments"></a>Rozszeranie przypisań ról

Poniższe kroki przedstawiają proces żądania, rozwiązywania lub administrowania rozszerzeniem lub odnowieniem przypisania roli. 

### <a name="member-extend"></a>Przedłużenie przypisania dla członka

Członkowie przypisania roli mogą w większym zakresie wycofać przypisania ról, bezpośrednio z karty **uprawniony** lub **aktywny** na stronie **Moje role** zasobu i na stronie **Moje role** w portalu PIM. Członkowie mogą zażądać rozbudowania kwalifikujących się i aktywnych ról (przypisanych), których ważność wygaśnie w ciągu następnych 14 dni.

![Zasoby platformy Azure — strony Moje role wyświetlają kwalifikujące się role z kolumną Action](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Gdy data/godzina zakończenia przypisania przypada w ciągu 14 dni, **przycisk do** przełączenia zostanie aktywnym linkiem w interfejsie użytkownika. W poniższym przykładzie przyjęto założenie, że bieżąca data to 27 marca.

![Kolumna akcji z łączami do aktywacji lub rozbudowania](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Aby zażądać rozszerzenia tego przypisania roli, wybierz pozycję **rozszerzanie** , aby otworzyć formularz żądania.

![Okienko rozszerzonego przypisania roli z powodu pola przyczyny](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Aby wyświetlić informacje o oryginalnym przypisaniu, rozwiń węzeł **Szczegóły przydziału**. Wprowadź przyczynę żądania rozszerzenia, a następnie wybierz pozycję **rozszerzaj**.

>[!Note]
>Zalecamy uwzględnienie szczegółowych informacji o tym, dlaczego rozszerzenie jest niezbędne, oraz o tym, jak długo powinno zostać przyznane rozszerzenie (Jeśli masz te informacje).

![Rozszerzanie okienka przypisania roli z rozwiniętymi szczegółami przypisywania](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

W danej chwili Administratorzy zasobów otrzymują powiadomienie e-mail z prośbą o przejrzenie żądania rozszerzenia. Jeśli żądanie przekroczenia zostało już przesłane, na początku Azure Portal zostanie wyświetlone powiadomienie wyskakujące.

![Powiadomienie wyjaśniające, że istnieje już oczekujące rozszerzenie przypisania roli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Przejdź do strony **oczekujące żądania** w lewym okienku, aby wyświetlić stan żądania lub go anulować.

![Strona żądania zasobów platformy Azure — oczekujące żądanie oraz link do anulowania](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Administrator zatwierdził

Gdy członek przesyła żądanie przekroczenia przypisania roli, Administratorzy zasobów otrzymują powiadomienie e-mail zawierające szczegóły oryginalnego przypisania i przyczynę żądania. Powiadomienie zawiera bezpośredni link do żądania zatwierdzenia lub odrzucenia przez administratora. 

Oprócz korzystania z linku pocztą e-mail Administratorzy mogą zatwierdzać lub odrzucać żądania, przechodząc do portalu administracji PIM i wybierając pozycję **Zatwierdź żądania** w okienku po lewej stronie.

![Zasoby platformy Azure — zatwierdzanie żądań na żądanie i linki do zatwierdzenia lub odmowy](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Gdy administrator wybierze pozycję **Zatwierdź** lub **Odmów**, zostaną wyświetlone szczegóły żądania wraz z polem zawierającym uzasadnienie dla dzienników inspekcji.

![Zatwierdź żądanie przypisania roli z przyczyną żądającą, typem przypisania, czasem rozpoczęcia, czasem zakończenia i przyczynie](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Podczas zatwierdzania żądania rozszerzającego przypisanie roli Administratorzy zasobów mogą wybrać nową datę początkową, datę końcową i typ przypisania. Zmiana typu przypisania może być konieczna, jeśli administrator chce udzielić ograniczonego dostępu do wykonania określonego zadania (na przykład jeden dzień). W tym przykładzie administrator może zmienić przypisanie z **kwalifikujące** się do **aktywne**. Oznacza to, że mogą oni zapewnić dostęp do osoby żądającej bez konieczności uaktywniania jej.

### <a name="admin-extend"></a>Przedłużenie administratora

Jeśli członek roli zapomni lub nie może zażądać rozszerzenia członkostwa w roli, administrator może przedłużyć przypisanie w imieniu elementu członkowskiego. Rozszerzenia administracyjne członkostwa w rolach nie wymagają zatwierdzenia, ale powiadomienia są wysyłane do wszystkich innych administratorów po rozszerzeniu roli.

Aby zwiększyć członkostwo w roli, przejdź do widoku rola zasobu lub elementu członkowskiego w usłudze PIM. Znajdź element członkowski, który wymaga rozszerzenia. Następnie wybierz pozycję **Rozciągnij** w kolumnie Akcja.

![Zasoby platformy Azure — Strona członków z listą uprawnionych ról z linkami do rozbudowania](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Odnów przypisania ról

Chociaż koncepcyjnie przypomina proces żądania rozszerzenia, proces odnawiania wygasłego przypisania roli jest inny. Wykonując poniższe kroki, członkowie i Administratorzy mogą odnowić dostęp do wygasłych ról, jeśli jest to konieczne.

### <a name="member-renew"></a>Odnawianie przypisania dla członka

Członkowie, którzy nie mogą już uzyskiwać dostępu do zasobów, mogą uzyskać dostęp do 30-dniowej historii przydziału. W tym celu przejdź do **obszaru Moje role** w lewym okienku, a następnie wybierz kartę **wygasłe role** w sekcji role zasobów platformy Azure.

![Strona Moje role — wygasłe karty ról](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Lista ról wyświetlana domyślnie jako **kwalifikujące się role**. Użyj menu rozwijanego, aby przełączać między uprawnionymi i aktywnymi rolami przypisanymi.

Aby zażądać odnowienia dla wszystkich przypisań ról na liście, wybierz akcję **Odnów** . Następnie podaj przyczynę żądania. Warto podać czas trwania oprócz dodatkowego kontekstu, który pomaga administratorowi zasobów zdecydować o zatwierdzeniu lub odrzuceniu.

![Pole przyczyny odnowienia przypisania roli](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Po przesłaniu żądania Administratorzy zasobów są powiadamiani o oczekującym żądaniu odnowienia przypisania roli.

### <a name="admin-approves"></a>Administrator zatwierdza

Administratorzy zasobów mogą uzyskać dostęp do żądania odnowienia z linku w wiadomości e-mail lub przez uzyskanie dostępu do usługi PIM z Azure Portal i wybranie opcji **Zatwierdź żądania** w okienku po lewej stronie.

![Zasoby platformy Azure — zatwierdzanie żądań na żądanie i linki do zatwierdzenia lub odmowy](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Gdy administrator wybierze pozycję **Zatwierdź** lub **Odmów**, szczegóły żądania są wyświetlane wraz z polem, aby podać uzasadnienie dzienników inspekcji.

![Zatwierdź żądanie przypisania roli z przyczyną żądającą, typem przypisania, czasem rozpoczęcia, czasem zakończenia i przyczynie](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Podczas zatwierdzania żądania odnowienia przypisania roli Administratorzy zasobów muszą wprowadzić nową datę początkową, datę końcową i typ przypisania. 

### <a name="admin-renew"></a>Odnowienie administratora

Administratorzy zasobów mogą odnowić wygasłe przypisania ról z karty **Członkowie** w menu nawigacji po lewej stronie zasobu. Mogą również odnowić wygasłe przypisania ról z poziomu karty **wygasłe** role w roli zasobu.

Aby wyświetlić listę wszystkich wygasłych przypisań ról, na ekranie **Członkowie** wybierz pozycję **wygasłe role**.

![Zasoby platformy Azure — Strona członków lista wygasłych ról z linkami do odnowienia](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Następne kroki

- [Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
