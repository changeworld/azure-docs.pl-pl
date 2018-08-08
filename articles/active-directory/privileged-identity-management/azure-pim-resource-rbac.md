---
title: Przegląd aplikacji Azure PIM zasobów RBAC | Dokumentacja firmy Microsoft
description: Zapoznaj się z omówieniem funkcji RBAC w usłudze PIM terminologii i powiadomienia
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 9b4980681cb3e7442211b06255d6eefd8a1b1170
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622358"
---
# <a name="pim-for-azure-resources"></a>Usługa PIM dla zasobów platformy Azure

Za pomocą usługi Azure Active Directory Privileged Identity Management (PIM) możesz teraz kontrolować i monitorować dostęp do zasobów platformy Azure w Twojej organizacji oraz zarządzać nim. W tym subskrypcji, grupy zasobów i nawet maszyn wirtualnych. Dowolnego zasobu w witrynie Azure portal, która korzysta z funkcji Azure roli na podstawie kontroli dostępu (RBAC) mogą korzystać z wszystkich doskonałe zabezpieczenia i funkcje zarządzania cyklem życia usługi Azure AD PIM ma do zaoferowania, i przedstawić nowe przydatne funkcje planujemy do Role usługi Azure AD wkrótce. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>Usługa PIM dla zasobów platformy Azure pomaga administratorom zasobów

- Zobacz, którzy użytkownicy i grupy są przypisane role zasobów platformy Azure, którymi administrujesz
- Włącz na żądanie, dostęp "just in time" do zarządzania zasobami, takimi jak subskrypcji, grupy zasobów i więcej
- Wygasa automatycznie z nowymi ustawieniami przypisania czasowo, dostęp do zasobów przypisanych użytkowników/grup
- Przypisz dostęp do zasobów tymczasowego dla szybkich zadań lub harmonogramów dyżurów
- Wymuszanie uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do zasobów w dowolnej roli wbudowanej lub niestandardowej 
- Uzyskiwanie raportów o działaniu zasobów skorelowane dostępu do zasobów podczas aktywnej sesji użytkownika
- Otrzymuj alerty, gdy nowych użytkowników lub grup są przypisywane dostęp do zasobów, a podczas aktywacji z kwalifikującymi się przypisaniami

Usługa Azure AD PIM można zarządzać wbudowane role zasobów platformy Azure, a także niestandardowych ról (RBAC), w tym (między innymi):

- Właściciel
- Administrator dostępu użytkowników
- Współautor
- Administrator zabezpieczeń
- Menedżer zabezpieczeń i nie tylko

>[!NOTE]
Użytkownicy lub członkowie grupy przypisane do roli właściciel lub Administrator dostępu użytkowników i administratorów globalnych, które umożliwiają Zarządzanie subskrypcją w usłudze Azure AD są administratorami zasobów. Administratorzy mogą przypisać role, konfigurowanie ustawień roli i przegląd dostępu za pomocą usługi PIM dla zasobów platformy Azure. Wyświetl listę [wbudowane role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Zadania

PIM udostępnia wygodne uzyskiwanie dostępu do aktywowania ról, wyświetlanie oczekujących aktywacji/żądań oczekujących zatwierdzeń (dla [ról katalogu usługi Azure AD](azure-ad-pim-approval-workflow.md)) i przeglądy oczekujące odpowiedzi z sekcji zadania w menu nawigacji po lewej stronie.

Podczas uzyskiwania dostępu do żadnego z elementów menu zadań z punktu wejścia Przegląd, wynikowym widoku zawiera wyniki dla ról katalogu usługi Azure AD i role zasobów platformy Azure. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Moje role zawierają listę swoje przypisania roli aktywnych i kwalifikujących się ról katalogu usługi Azure AD i role zasobów platformy Azure.

## <a name="activate-roles"></a>Uaktywnij role

Uaktywnianie ról dla zasobów platformy Azure wprowadza nową funkcjonalność, która umożliwia członkom kwalifikowania się do roli planowanie aktywacji dla przyszłej daty/godziny i wybierz czas trwania aktywacji określonych w maksymalnym (skonfigurowane przez administratorów). Dowiedz się więcej o [uaktywnianie ról usługi Azure AD, w tym miejscu](pim-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Z menu aktywacji wprowadź odpowiednią datę i godzinę rozpoczęcia aktywacji roli. Opcjonalnie skrócić czas trwania aktywacji (czas roli jest aktywny) i Wprowadź uzasadnienie, w razie potrzeby; Kliknij przycisk Aktywuj.

Jeśli data i godzina rozpoczęcia nie jest modyfikowany, rola zostanie uaktywniona w ciągu kilku sekund. Zobaczysz, że rola w kolejce do aktywacji komunikat transparentu, na stronie Moje role. Kliknij przycisk Odśwież, aby wyczyścić ten komunikat.

![](media/azure-pim-resource-rbac/my-roles.png)

Jeśli aktywacja jest zaplanowane przez czas w przyszłości, oczekujące żądanie pojawi się na karcie oczekujących żądań w menu nawigacji po lewej stronie. W przypadku aktywacji roli nie jest już wymagana, użytkownik może anulować żądanie, klikając przycisk anulowania w prawej części strony.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Odnajdywanie i zarządzanie zasobami platformy Azure

Aby znaleźć i zarządzanie rolami dla zasobu platformy Azure, wybierz zasoby platformy Azure, w menu nawigacji po lewej stronie na karcie Zarządzanie. Użyj filtrów lub pasek wyszukiwania w górnej części strony, aby znaleźć zasobu.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Pulpity nawigacyjne zasobów

Widok administratora pulpit nawigacyjny zawiera cztery główne składniki. Graficzna reprezentacja zasobu aktywacje ról w ciągu ostatnich siedmiu dni. Te dane są ograniczone do wybranego zasobu i wyświetla aktywacji dla najbardziej typowe role (właściciela, współautora, Administrator dostępu użytkowników) i wszystkich ról w połączeniu.

Z prawej strony wykresu aktywacji są dwa wykresy, które wyświetlają rozkład przypisań ról według typu przypisania, dla użytkowników i grup. Wybieranie wycinek wykresu zmienia wartość do wartości procentowej (lub odwrotnie).

![](media/azure-pim-resource-rbac/admin-view.png)

Poniżej wykresów zobaczysz liczbę użytkowników i grup za pomocą nowego przypisania roli w ciągu ostatnich 30 dni (po lewej) i listę ról, posortowane według łączna liczba przypisań (malejąco).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Zarządzanie przypisaniami ról

Administratorzy mogą zarządzać przypisaniami ról, wybierając ról lub elementów członkowskich z nawigacji po lewej stronie. Wybieranie ról umożliwia administratorom ograniczyć zakres ich zadań zarządzania, z określoną rolą, podczas gdy elementy członkowskie Wyświetla wszystkie przypisania ról użytkowników i grup dla zasobu.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
W przypadku roli do czasu aktywacji transparent powiadomienia jest wyświetlany w górnej części strony, podczas wyświetlania członkostwa.

## <a name="assign-roles"></a>Przypisz role

Aby przypisać użytkownika lub grupę do roli, wybierz rolę (Jeśli wyświetlanie role), lub kliknij przycisk Dodaj, na pasku akcji (jeśli są w widoku elementów członkowskich).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Dodawanie użytkownika lub grupy na karcie elementy członkowskie, musisz wybierz rolę z menu Dodaj, aby można było wybrać użytkownika lub grupy.

![](media/azure-pim-resource-rbac/select-role.png)

Wybierz użytkownika lub grupy z katalogu.

![](media/azure-pim-resource-rbac/choose.png)

Wybierz typ przypisania odpowiednich z menu rozwijanego. 

**Dokładnie na czas przypisania:** zawiera elementy członkowskie użytkownika lub grupy z kwalifikujących się, ale nie trwałego dostępu do roli w określonym przedziale czasu lub nieokreślony (jeśli jest skonfigurowane w ustawieniach roli). 

**Bezpośrednie przypisanie:** nie wymaga użytkownika lub grupy elementów członkowskich można aktywować przypisania roli (znanych jako trwały dostęp). Firma Microsoft zaleca korzystanie z bezpośredniego przypisania krótkoterminowej użycia takich jak zmiany na wywołanie lub czas działania poufnych, których dostęp nie będzie wymagane po ukończeniu zadania.

![](media/azure-pim-resource-rbac/membership-settings.png)

Pole wyboru poniżej listy rozwijanej Typ przypisanie umożliwia określenie Jeśli przypisanie powinno być stałe (trwale zakwalifikowane do aktywowania Just in czasu przypisania/trwałe aktywne przypisania bezpośredniego). Aby określić czas trwania określone przypisanie, usuń zaznaczenie pola wyboru i modyfikować uruchomienia i/lub pól daty i godziny zakończenia.

>[!NOTE]
Pole może być niemodyfikowalnych, jeśli inny administrator ma określony czas trwania maksymalna przypisania dla każdego typu przydziału w ustawieniach roli.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Wyświetl aktywacji i działanie zasobów platformy Azure

W przypadku potrzebujesz zobaczyć, jakie akcje określony użytkownik nie wykonał dla różnych zasobów, możesz sprawdzić działanie zasobów platformy Azure skojarzone z okresem danego aktywacji (w przypadku uprawnionych użytkowników). Rozpocznij od wybranie użytkownika z widoku elementów członkowskich lub listę elementów członkowskich w określonej roli. Wynik przedstawia widok graficzny akcje użytkownika dla zasobów platformy Azure według daty, a ostatnie aktywacje ról w tym samym przedziale czasu.

![](media/azure-pim-resource-rbac/user-details.png)

Wybieranie aktywacji określonej roli zostaną wyświetlone szczegóły uaktywnienia roli, a odpowiadającego im działania zasobów platformy Azure, który wystąpił podczas, gdy ten użytkownik był aktywny.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Modyfikuj istniejące przypisania

Aby zmodyfikować istniejące przypisania z widoku szczegółów użytkownika/grupy, wybierz opcję Zmień ustawienia na pasku akcji w górnej części strony. Po prostu w przypisanie czasu lub przypisania bezpośredniego, należy zmienić typ przypisania.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Przejrzyj, kto ma dostęp w ramach subskrypcji

Aby zapoznać się z przypisań ról w ramach subskrypcji, wybierz kartę elementy członkowskie w lewym obszarze nawigacji, lub wybierz role, a następnie wybierz określoną rolę, aby zapoznać się z elementów członkowskich. 

Wybierz pozycję Przegląd, na pasku akcji, aby wyświetlić istniejące przeglądów dostępu, a następnie wybierz pozycję Dodaj, aby utworzyć nowy Przegląd.

![](media/azure-pim-resource-rbac/owner.png)

[Dowiedz się więcej o przeglądach dostępu](pim-how-to-perform-security-review.md)

>[!NOTE]
Recenzje są obsługiwane tylko dla typów zasobów subskrypcji w tej chwili.

## <a name="configure-role-settings"></a>Konfigurowanie ustawień roli

Konfigurowanie ustawień roli definiują ustawienia domyślne stosowane do przypisania w środowisku usługi PIM. Aby zdefiniować te zasobu bazy danych, wybierz kartę ustawień roli w nawigacji po lewej stronie, lub przycisk Ustawienia roli na pasku akcji w każdej roli, aby wyświetlić bieżące opcje.

Klikając przycisk Edytuj na pasku akcji w górnej części strony umożliwia modyfikowanie każdego ustawienia.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Zmiany ustawień są rejestrowane na stronie ustawień roli w tym zaktualizowanego Data godzina ostatniej i administratora, który zmianie ustawień.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Inspekcja zasobu

Inspekcja zasobu zapewnia wgląd w całą aktywność roli dla zasobu. Można filtrować informacje przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowy.
![](media/azure-pim-resource-rbac/last-day.png) Inspekcja zasobu również zapewnia szybki dostęp, aby wyświetlić szczegóły dotyczące działania użytkownika. W widoku wszystkie akcje "Aktywuj rolę" podano linki do działań zasobu określonego obiektu żądającego.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Wystarczający zakres administracji

Najlepsze rozwiązania wystarczający tylko administracyjnej (JEA) przy użyciu zasobów przypisania roli jest proste — usługa PIM dla zasobów platformy Azure. Użytkownicy i członkowie grupy za pomocą przypisań do grup zasobów lub subskrypcji platformy Azure można aktywować istniejące przypisania roli w ograniczonym zakresie. 

Ze strony wyszukiwania Znajdź zasób podrzędny, który trzeba zarządzać.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Wybierz Moje role z menu nawigacji po lewej stronie i wybierz odpowiednią rolę, aby aktywować. Zwróć uwagę, typ przypisania są dziedziczone, ponieważ rola została przypisana w subskrypcji, a nie w grupie zasobów, jak pokazano poniżej.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Kolejne kroki

- [Wbudowane role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md)
- Dowiedz się więcej o [uaktywnianie ról usługi Azure AD, w tym miejscu](pim-how-to-activate-role.md)
- [Przepływy pracy zatwierdzania usługi PIM](azure-ad-pim-approval-workflow.md)
