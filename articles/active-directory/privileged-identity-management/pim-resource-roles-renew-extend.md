---
title: Odnawianie przydziałów ról zasobów platformy Azure w usłudze PIM — Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozszerzyć lub odnowić przydziały ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022913"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Rozszerzanie lub odnawianie przydziałów ról zasobów platformy Azure w dziale Zarządzanie tożsamościami uprzywilejowanymi

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) zapewnia formanty do zarządzania cyklem życia dostępu i przydziału zasobów platformy Azure. Administratorzy mogą przypisywać role przy użyciu właściwości daty-godziny rozpoczęcia i zakończenia. Gdy zbliża się koniec przydziału, zarządzanie tożsamością uprzywilejowaną wysyła powiadomienia e-mail do użytkowników lub grup, których dotyczy problem. Wysyła również powiadomienia e-mail do administratorów zasobu, aby zapewnić, że odpowiedni dostęp jest obsługiwany. Przydziały mogą być odnawiane i pozostają widoczne w stanie wygasłym przez okres do 30 dni, nawet jeśli dostęp nie zostanie przedłużony.

## <a name="who-can-extend-and-renew"></a>Kto może przedłużyć i odnowić?

Tylko administratorzy zasobu mogą rozszerzać lub odnawiać przypisania ról. Użytkownik lub grupa, których dotyczy problem, może zażądać rozszerzenia ról, które wkrótce wygasną, i zażądać odnowienia ról, które już wygasły.

## <a name="when-are-notifications-sent"></a>Kiedy są wysyłane powiadomienia?

Zarządzanie tożsamościami uprzywilejowanymi wysyła powiadomienia e-mail do administratorów i użytkowników, których dotyczy problem, lub grup ról, które wygasają w ciągu 14 dni i jeden dzień przed wygaśnięciem. Wysyła dodatkową wiadomość e-mail po oficjalnym wygaśnięciu przypisania.

Administratorzy otrzymują powiadomienia, gdy użytkownik lub grupa przypisała wygasające lub wygasłe żądania roli, aby przedłużyć lub odnowić. Gdy określony administrator rozwiązuje żądanie, wszyscy inni administratorzy są powiadamiani o decyzji dotyczącej rozwiązania (zatwierdzonej lub odrzuconej). Następnie użytkownik lub grupa żądająca jest powiadamiana o decyzji.

## <a name="extend-role-assignments"></a>Rozszerzanie przypisań ról

Poniższe kroki opisują proces żądania, rozwiązywania lub administrowania rozszerzeniem lub odnowieniem przypisania roli.

### <a name="self-extend-expiring-assignments"></a>Samodzielne przedłużanie wygasających przydziałów

Użytkownicy lub grupy przypisane do roli mogą rozszerzać wygasające przypisania ról bezpośrednio z karty **Kwalifikujące** lub **Aktywne** na stronie **Moje role** zasobu i ze strony **Moje role** najwyższego poziomu w portalu Zarządzania tożsamościami uprzywilejowanymi. Użytkownicy lub grupy mogą zażądać rozszerzenia kwalifikujących się i aktywnych (przypisanych) ról, które wygasną w ciągu najbliższych 14 dni.

![Zasoby platformy Azure — strona Moje role z listą kwalifikujących się ról z kolumną Akcja](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Gdy data-godzina zakończenia przydziału znajduje się w ciągu 14 dni, przycisk **Rozszerz** staje się aktywnym łączem w interfejsie użytkownika. W poniższym przykładzie załóżmy, że bieżąca data to 27 marca.

![Kolumna Akcja z łączami do aktywowania lub rozszerzania](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Aby zażądać rozszerzenia tego przypisania roli, wybierz pozycję **Rozszerz,** aby otworzyć formularz żądania.

![Rozszerzanie okienka przypisania roli za pomocą pola Przyczyna](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Aby wyświetlić informacje o oryginalnym przypisaniu, rozwiń **szczegóły przydziału**. Wprowadź przyczynę żądania rozszerzenia, a następnie wybierz pozycję **Rozszerz**.

>[!NOTE]
>Zalecamy podanie szczegółów, dlaczego rozszerzenie jest konieczne i jak długo należy przyznać rozszerzenie (jeśli masz te informacje).

![Rozszerzanie okienka przypisania ról z rozwiniętymi szczegółami przydziału](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

W ciągu kilku chwil administratorzy zasobów otrzymują powiadomienie e-mail z prośbą o przejrzenie żądania rozszerzenia. Jeśli żądanie rozszerzenia zostało już przesłane, w portalu zostanie wyświetlone powiadomienie platformy Azure.

![Powiadomienie wyjaśniające, że istnieje już oczekujące rozszerzenie przypisania roli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Przejdź do strony **Oczekujące żądania,** aby wyświetlić stan żądania lub anulować je.

![Zasoby platformy Azure — strona oczekujące żądania z listą żądanych oczekujących i łączem do anuluj](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Rozszerzenie zatwierdzone przez administratora

Gdy użytkownik lub grupa przesyła żądanie rozszerzenia przypisania roli, administratorzy zasobów otrzymują powiadomienie e-mail zawierające szczegóły oryginalnego przypisania i przyczynę żądania. Powiadomienie zawiera bezpośrednie łącze do żądania zatwierdzenia lub odrzucenia przez administratora.

Oprócz używania następującego łącza z wiadomości e-mail administratorzy mogą zatwierdzać lub odrzucać żądania, przechodząc do portalu administracji zarządzanie tożsamościami uprzywilejowanymi i wybierając pozycję **Zatwierdź żądania** w lewym okienku.

![Zasoby platformy Azure — zatwierdzanie żądań strony zawierającej listę żądań i łączy do zatwierdzenia lub odrzucenia](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Gdy administrator wybierze **opcję Zatwierdź** lub **Odmów,** wyświetlane są szczegóły żądania wraz z polem, które zawiera uzasadnienie biznesowe dzienników inspekcji.

![Zatwierdzanie żądania przypisania roli z przyczyną żądania, typem przydziału, godziną rozpoczęcia, godziną zakończenia i przyczyną](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Podczas zatwierdzania żądania rozszerzenia przydziału ról administratorzy zasobów mogą wybrać nową datę rozpoczęcia, datę zakończenia i typ przydziału. Zmiana typu przydziału może być konieczna, jeśli administrator chce zapewnić ograniczony dostęp do wykonania określonego zadania (na przykład jednego dnia). W tym przykładzie administrator może zmienić przypisanie z **Kwalifikujące się** na **Aktywne**. Oznacza to, że mogą zapewnić dostęp do żądającego bez konieczności ich aktywacji.

### <a name="admin-initiated-extension"></a>Rozszerzenie zainicjowane przez administratora

Jeśli użytkownik przypisany do roli nie żąda rozszerzenia przypisania roli, administrator może rozszerzyć przypisanie w imieniu użytkownika. Rozszerzenia administracyjne przypisywania ról nie wymagają zatwierdzenia, ale powiadomienia są wysyłane do wszystkich innych administratorów po rozszerzeniu roli.

Aby rozszerzyć przypisanie roli, przejdź do roli zasobu lub widoku przydziału w zarządzania tożsamościami uprzywilejowanymi. Znajdź przypisanie, które wymaga rozszerzenia. Następnie wybierz **pozycję Rozszerz** w kolumnie akcji.

![Zasoby platformy Azure — strona przydziałów z listą kwalifikujących się ról z łączami do rozszerzenia](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Odnawianie przypisań ról

Chociaż koncepcyjnie podobne do procesu żądania rozszerzenia, proces odnowienia wygasłe przypisanie roli jest inna. Wykonując następujące kroki, przydziały i administratorzy mogą odnowić dostęp do wygasłych ról, gdy jest to konieczne.

### <a name="self-renew"></a>Samoodnajdnienie

Użytkownicy, którzy nie mają już dostępu do zasobów, mogą uzyskać dostęp do do 30 dni wygasłej historii przydziałów. Aby to zrobić, przejdź do **moich ról** w lewym okienku, a następnie wybierz **wygasłe role** kartę w sekcji Role zasobów platformy Azure.

![Moja strona role — karta Role wygasłe](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Lista ról wyświetlanych domyślnie **do ról kwalifikujących**. Użyj menu rozwijanego, aby przełączać się między przypisanymi rolami Kwalifikującymi i Aktywnymi.

Aby zażądać odnowienia dla dowolnego z przypisań ról na liście, wybierz akcję **Odnów.** Następnie podaj przyczynę żądania. Warto podać czas trwania oprócz dodatkowego kontekstu lub uzasadnienia biznesowego, które może pomóc administratorowi zasobów w podjęciu decyzji o zatwierdzeniu lub odrzuceniu.

![Okno Odnawianie przypisania roli z polem Przyczyna](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Po przesłaniu żądania administratorzy zasobów są powiadamiani o oczekującym żądaniu odnowienia przypisania roli.

### <a name="admin-approves"></a>Administrator zatwierdza

Administratorzy zasobów mogą uzyskać dostęp do żądania odnowienia z łącza w powiadomieniu e-mail lub uzyskując dostęp do zarządzania tożsamościami uprzywilejowanymi z portalu Azure i wybierając **zatwierdź żądania** z lewego okienka.

![Zasoby platformy Azure — zatwierdzanie żądań strony zawierającej listę żądań i łączy do zatwierdzenia lub odrzucenia](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Gdy administrator wybierze **opcję Zatwierdź** lub **Odmów,** szczegóły żądania są wyświetlane wraz z polem, które zawiera uzasadnienie biznesowe dzienników inspekcji.

![Zatwierdzanie żądania przypisania roli z przyczyną żądania, typem przydziału, godziną rozpoczęcia, godziną zakończenia i przyczyną](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Podczas zatwierdzania żądania odnowienia przydziału ról administratorzy zasobów muszą wprowadzić nową datę rozpoczęcia, datę zakończenia i typ przydziału.

### <a name="admin-renew"></a>Odnowienie administratora

Administratorzy zasobów mogą odnawiać wygasłe przypisania ról na karcie **Członkowie** w menu nawigacji po lewej stronie zasobu. Mogą również odnawiać wygasłe przypisania ról z poziomu karty **Role wygasłe** roli zasobu.

Aby wyświetlić listę wszystkich wygasłych przypisań ról, na ekranie **Członkowie** wybierz pozycję **Role wygasłe**.

![Zasoby platformy Azure — lista wygasłych ról strony Członków z łączami do odnowienia](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Następne kroki

- [Zatwierdzanie lub odrzucanie żądań ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-approval-workflow.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-configure-role-settings.md)
