---
title: Omówienie zasobów Azure PIM RBAC | Dokumentacja firmy Microsoft
description: Zapoznaj się z omówieniem funkcji RBAC w PIM tym terminologii i powiadomienia
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: f19be17cccc3d1bf0e94fe1c156afb546909d565
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699227"
---
# <a name="pim-for-azure-resources"></a>PIM dla zasobów platformy Azure

Z usługi Azure Active Directory uprzywilejowanych tożsamości zarządzania (PIM), mogą teraz zarządzać, kontrolę i monitorowanie dostępu do zasobów platformy Azure w ramach danej organizacji. W tym subskrypcji, grupy zasobów i nawet maszyn wirtualnych. Dowolnego zasobu w portalu Azure, która korzysta z funkcji Azure roli na podstawie kontroli dostępu (RBAC) korzystać ze wszystkich niezwykłych zabezpieczeń i możliwości zarządzania cyklem życia Azure AD PIM musi oferować i nowe, fantastyczne funkcje planujemy doprowadzić do Wkrótce role w AD platformy Azure. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM zasobów Azure ułatwia administratorom zasobów

- Zobacz, którzy użytkownicy i grupy są przypisane role dla zasobów platformy Azure, którą możesz administrować
- Włącz na żądanie, "just in time" dostęp do zarządzania zasobami subskrypcji, grupy zasobów i więcej
- Dostęp do zasobów przypisanych użytkowników/grupy automatycznie przy użyciu nowych ustawień przypisania granicy czasu wygaśnięcia
- Przypisz dostęp do zasobów tymczasowego szybkie zadań lub harmonogramów na wywołanie
- Wymusić uwierzytelnianie wieloskładnikowe, aby uzyskać dostęp do zasobów w dowolnej roli wbudowanych lub niestandardowych 
- Uzyskaj raporty dotyczące działania zasobów skorelowane dostępu do zasobów podczas aktywnej sesji użytkownika
- Alerty, gdy nowych użytkowników lub grup są przypisane dostęp do zasobów i aktywują przypisania kwalifikujących się

Azure AD PIM można zarządzać wbudowane role zasobów Azure, a także niestandardowych ról (RBAC), w tym (między innymi):

- Właściciel
- Administrator dostępu użytkowników
- Współautor
- Administrator zabezpieczeń
- Menedżer zabezpieczeń i inne

>[!NOTE]
Użytkowników lub członków grupy przypisane do ról właściciel lub Administrator dostępu użytkowników i administratorów globalnych, które umożliwiają Zarządzanie subskrypcją w usłudze Azure AD są administratorami zasobów. Administratorzy może Przypisz role, skonfiguruj ustawienia roli, a następnie przejrzyj dostępu do zasobów Azure przy użyciu usługi PIM. Wyświetl listę [wbudowane role zasobów Azure](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Zadania

PIM umożliwia wygodne dostęp do aktywowania usługi ról, wyświetlanie oczekujących aktywacji /, oczekujących żądań zatwierdzenia (dla [ról katalogu usługi Azure AD](azure-ad-pim-approval-workflow.md)) i monitoruje oczekuje na Twoją odpowiedź z sekcji zadań menu nawigacji po lewej stronie.

Podczas uzyskiwania dostępu do żadnego z elementów menu zadania z punktu wejścia Przegląd, wynikowy widok zawiera wyniki zarówno usługi Azure AD directory ról i zasobów Azure. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Moje role zawierają listę przypisania roli active i kwalifikujących się ról katalogu usługi Azure AD i ról zasobów Azure.

## <a name="activate-roles"></a>Uaktywnij role

Aktywacja ról zasobów Azure wprowadza nową funkcjonalność, która umożliwia członkom roli kwalifikujących się do planowania aktywacji dla przyszłych daty/godziny i wybierz czas trwania aktywacji określonym terminie (skonfigurowanych przez administratorów). Dowiedz się więcej o [Aktywacja usługi Azure AD ról tutaj](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Z menu aktywacji wprowadź odpowiednią datę i godzinę rozpoczęcia aktywowania roli. Opcjonalnie Zmniejsz czas trwania aktywacji (czas roli jest aktywny), a następnie wprowadź uzasadnienie, jeśli jest to wymagane; Kliknij przycisk Aktywuj.

W przypadku braku modyfikacji datę i godzinę, w ciągu kilku sekund zostanie uaktywniona roli. Pojawi się, że rola w kolejce w celu aktywacji komunikat transparentu na stronie Moja ról. Kliknij przycisk Odśwież, aby wyczyścić tę wiadomość.

![](media/azure-pim-resource-rbac/my-roles.png)

Jeśli aktywacja jest zaplanowane na czas datę w przyszłości, oczekujące żądania będą wyświetlane na karcie oczekujących żądań w menu nawigacji po lewej stronie. W przypadku aktywacji ról nie jest już wymagana, użytkownik może anulować żądanie, klikając przycisk Anuluj w prawej części strony.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Odnajdywanie i zarządzania zasobami Azure

Aby znaleźć i zarządzanie rolami dla zasobów Azure, wybierz zasobów platformy Azure na karcie Zarządzanie w menu nawigacji po lewej stronie. Użyj filtrów lub pasek wyszukiwania w górnej części strony, aby znaleźć zasobu.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Pulpity nawigacyjne zasobów

Widok administratora pulpit nawigacyjny zawiera cztery podstawowe składniki. Graficzna reprezentacja zasobu roli aktywacji w ciągu ostatnich siedmiu dni. Te dane zakresie znajduje się wybrany zasób i wyświetla aktywacji dla najbardziej typowe role (właściciela, współautora, Administrator dostępu użytkowników), a wszystkie role, w połączeniu.

Po prawej stronie wykresu aktywacji są dwa wykresy dystrybucji przypisań ról przez typ przydziału dla użytkowników i grup. Wybór wycinek wykresu zmienia wartość na wartość procentową (lub odwrotnie).

![](media/azure-pim-resource-rbac/admin-view.png)

Poniżej wykresy zobacz temat liczby użytkowników i grup z nowych przypisań ról w ciągu ostatnich 30 dni (po lewej), a lista ról posortowane według całkowitej przypisania (malejąco).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Zarządzanie przypisaniami ról

Administratorzy mogą zarządzać przypisań ról, wybierając ról lub elementy członkowskie z lewym pasku nawigacyjnym. Wybieranie ról pozwala administratorom zakres ich zadań zarządzania do konkretnej roli, gdy elementy członkowskie Wyświetla wszystkie przypisania ról użytkowników i grup dla zasobu.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Jeśli masz roli do czasu aktywacji transparencie powiadomienia jest wyświetlane w górnej części strony, gdy wyświetlanie członkostwa.

## <a name="assign-roles"></a>Przypisz role

Aby przypisać do roli użytkownika lub grupę, wybierz rolę (Jeśli wyświetlanie role), lub kliknij przycisk Dodaj na pasku akcji (jeśli są w widoku elementów członkowskich).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Dodawanie użytkownika lub grupy na karcie elementy członkowskie, musisz wybierz z menu Dodaj rolę, zanim będzie można wybrać użytkownika lub grupę.

![](media/azure-pim-resource-rbac/select-role.png)

Wybierz użytkownika lub grupy z katalogu.

![](media/azure-pim-resource-rbac/choose.png)

Wybierz typ odpowiednie przypisanie z menu rozwijanego. 

**Tylko w czasie przypisania:** zawiera członków użytkownika lub grupy z kwalifikujących się, ale nie trwały dostęp do roli w określonym przedziale czasu lub nieskończoność (jeśli została skonfigurowana w ustawieniach roli). 

**Bezpośrednie przypisywanie:** nie wymaga użytkownika lub grupę elementów członkowskich aktywować przypisania roli (nazywane trwały dostęp). Firma Microsoft zaleca korzystanie z bezpośredniego przypisania krótkoterminowej Użyj takie jak zmiany na wywołanie lub czas działania poufne, których dostęp nie będzie wymagany po zakończeniu zadania.

![](media/azure-pim-resource-rbac/membership-settings.png)

Pole wyboru poniżej listy rozwijanej Typ przypisania służy do określenia Jeśli przypisanie powinno być stałe (trwale kwalifikuje się do aktywowania tylko w czasie przypisania/trwale active dla przypisania bezpośredniego). Aby określić czas trwania danego przydziału, usuń zaznaczenie pola wyboru i zmodyfikować uruchomienia i/lub kończyć pól daty i godziny.

>[!NOTE]
Pole wyboru może być unmodifiable, jeśli inny administrator ma określone przypisania maksymalny czas trwania dla każdego typu przydziału w ustawieniach roli.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Wyświetl aktywacji i działania zasobów Azure

W przypadku, gdy potrzebujesz zobaczyć, jakie akcje trwało określonego użytkownika na różnych zasobów, można przejrzeć działania zasobów Azure skojarzonego z okresem danego aktywacji (w przypadku uprawnionych użytkowników). Rozpocznij od wybór użytkownika z widoku elementów członkowskich lub z listy elementów członkowskich w określonej roli. Wynik zawiera graficzne przedstawienie akcje użytkownika zasobów Azure według daty i ostatnie aktywacji roli w tym samym czasie.

![](media/azure-pim-resource-rbac/user-details.png)

Wybieranie aktywacji konkretnej roli zostaną wyświetlone szczegóły aktywacji dla roli, a odpowiadające mu działanie zasobów Azure, który wystąpił podczas aktywnego użytkownika.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Zmodyfikuj istniejące przypisania

Aby zmodyfikować istniejące przypisania z widoku szczegółów użytkownika/grupy, wybierz opcję Zmień ustawienia na pasku akcji w górnej części strony. Zmień typ przypisania tylko w przypisanie czasu lub bezpośredniego przypisania.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Sprawdź, kto ma dostęp w ramach subskrypcji

Aby przejrzeć przypisań ról w ramach subskrypcji, wybierz kartę członków z nawigacji po lewej stronie, lub wybierz role, a następnie wybierz pozycję konkretnej roli można przejrzeć elementy członkowskie. 

Wybierz przeglądu na pasku akcji Wyświetl istniejący recenzje dostępu i wybierz pozycję Dodaj, aby utworzyć nowy przeglądu.

![](media/azure-pim-resource-rbac/owner.png)

[Dowiedz się więcej o przeglądami dostępu](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Przeglądy są obsługiwane tylko dla typów zasobów subskrypcji w tym momencie.

## <a name="configure-role-settings"></a>Konfigurowanie ustawień roli

Konfigurowanie ustawień roli zdefiniować ustawienia domyślne stosowane do przypisania w środowisku usługi PIM. Aby zdefiniować te dla zasobu, wybierz kartę Ustawienia roli z nawigacji po lewej stronie, lub przycisk ustawień roli na pasku akcji w każdej roli, aby wyświetlić bieżące opcje.

Klikając przycisk Edytuj na pasku akcji w górnej części strony umożliwia modyfikowanie każdego ustawienia.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Zmiany ustawień są rejestrowane na stronie Ustawienia roli, łącznie z ostatniego Data aktualizacji i administratora, który zmienił ustawień.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Inspekcja zasobu

Inspekcji zasobów umożliwia wyświetlanie wszystkich działań roli dla zasobu. Można filtrować dane przy użyciu wstępnie zdefiniowanych datę lub zakres niestandardowych.
![](media/azure-pim-resource-rbac/last-day.png) Inspekcji zasobów również zapewnia szybki dostęp, aby wyświetlić szczegóły działania użytkownika. W widoku wszystkie akcje "Uaktywnij rolę" podano linki do działania zasobu określonego obiektu żądającego.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Wystarczającego administracji

Przy użyciu wystarczającego najlepsze praktyki administracyjne (JEA) z przypisaniami roli zasobu jest proste — PIM zasobów Azure. Użytkownicy i członkowie grupy z przydziałami w subskrypcji platformy Azure lub grupy zasobów można aktywować ich istniejącym przypisaniu roli na zmniejszenie zakresu. 

Ze strony wyszukiwania Znajdź podrzędnego zasób, który trzeba zarządzać.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Wybierz z menu nawigacji po lewej stronie Moje ról, a następnie wybierz odpowiednią rolę, aby aktywować. Powiadomienie typ przydziału jest dziedziczone, ponieważ rola została przypisana w subskrypcji, a nie w grupie zasobów, jak pokazano poniżej.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Kolejne kroki

- [Wbudowane role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md)
- Dowiedz się więcej o [aktywowanie tutaj ról usługi Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [PIM przepływów pracy](azure-ad-pim-approval-workflow.md)
