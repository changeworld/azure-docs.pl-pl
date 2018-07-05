---
title: Platformy Azure Privileged Identity Management przepływów pracy | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przepływów pracy zatwierdzania w Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 025bcd0cde8d73cfdd4d79a77256a1705950f90a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444650"
---
# <a name="approvals"></a>Zatwierdzenia

## <a name="overview"></a>Przegląd

Zatwierdzanie Privileged Identity Management konfigurowania ról, aby wymagały zatwierdzenia aktywacji i wybrać jednego lub wielu użytkowników lub grup w ramach delegowanego osób zatwierdzających. Zachowaj informacje na temat sposobu konfigurowania ról, a następnie wybierz pozycję osób zatwierdzających.


## <a name="new-terminology"></a>Nową terminologią

*Uprawniony użytkownik roli* — kwalifikowania się do roli użytkownika jest użytkownikiem w Twojej organizacji, który jest przypisany do roli usługi Azure AD jako uprawnionych (rola wymaga składnika Aktywacja).

*Delegowane osoba zatwierdzająca* — delegowanego osoba zatwierdzająca jest jednym lub wielu osób lub grup w usłudze Azure AD odpowiedzialny za zatwierdzanie żądań uaktywniać swoje role.

## <a name="scenarios"></a>Scenariusze

Prywatna wersja zapoznawcza obsługuje następujące scenariusze:

**Jako uprzywilejowanych ról administratora pararozaniliny możesz wykonywać następujące czynności:**

-   [Włącz zatwierdzenia dla określonych ról](#enable-approval-for-specific-roles)

-   [Określ osobę zatwierdzającą użytkowników i/lub grup do żądań zatwierdzenia](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Wyświetl historię żądania i zatwierdzenia dla ról uprzywilejowanych wszystko](#view-request-and-approval-history-for-all-privileged-roles)

**Jako wyznaczonej osoby zatwierdzającej możesz wykonywać następujące czynności:**

-   [Wyświetlanie oczekujących zatwierdzeń (żądań)](#view-pending-approvals-requests)

-   [Zatwierdzanie lub odrzucanie żądań o podniesienie uprawnień roli, (pojedynczy i/lub zbiorcze)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Podaj uzasadnienie dla mojego lub odrzuceniu](#provide-justification-for-my-approval/rejection) 

**Jako uprawniony użytkownik roli możesz wykonywać następujące czynności:**

-   [żądanie aktywacji roli, która wymaga zatwierdzenia](#request-activation-of-a-role-that-requires-approval)

-   [Wyświetl stan żądanie aktywacji](#view-the-status-of-your-request-to-activate)

-   [Ukończenia zadania w usłudze Azure AD, jeśli Aktywacja została zatwierdzona.](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Nawigacja

Zaktualizowaliśmy nawigacji do obsługi zatwierdzenia

![](media/azure-ad-pim-approval-workflow/image001.png)

Wartość domyślna strona docelowa zapewnia wygodny dostęp do informacji na temat usługi PIM oraz Nowa dokumentacja zatwierdzenia.

![](media/azure-ad-pim-approval-workflow/image002.png)

Dodaliśmy również nową sekcję dla wszystkich użytkowników usługi PIM, "Moja Historia inspekcji". Znajdziesz tutaj wszystkie informacje istotne dla Twojej tożsamości. Obejmuje to wszystkie oczekujące i ukończonych żądań, podejmuje wprowadzone dotyczące żądań, które można rozwiązać, a wszystkie ostatnie aktywacje ról w jednym dogodnym miejscu.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Włącz zatwierdzenia dla określonych ról

Aby włączyć zatwierdzenia dla określonej roli, należy najpierw wybrać ról w katalogu, w lewym obszarze nawigacji.

![](media/azure-ad-pim-approval-workflow/image004.png)

Znajdź i wybierz ustawienia w obszarze nawigacji po lewej stronie role katalogu

![](media/azure-ad-pim-approval-workflow/image006.png)

Wybierz role uprzywilejowane:

![](media/azure-ad-pim-approval-workflow/image009.png)

Wybierz pozycję "Włącz" w sekcji zatwierdzenia wymagają:

![](media/azure-ad-pim-approval-workflow/image011.png)

Po włączeniu bloku będzie rozszerzyć, aby wyświetlić następujące informacje:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Jeśli nie określisz żadnych osób zatwierdzających, PRA(s) stają się osobami zatwierdzającymi domyślne. PRA(s) będzie potrzebować zatwierdzić wszystkie żądania aktywacji dla tej roli.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Określ osobę zatwierdzającą użytkowników i/lub grup do żądań zatwierdzenia

Aby delegować zatwierdzenia, kliknij opcję "Wybierz osoby zatwierdzające":

![](media/azure-ad-pim-approval-workflow/image015.png)

Podczas ładowania bloku wybierz osoby zatwierdzające mogą wyszukać konkretnego użytkownika lub grupy, korzystając z paska wyszukiwania u góry lub wybierając z listy wstępnie wypełnione, a następnie kliknij pozycję "Wybierz", po zakończeniu:

![](media/azure-ad-pim-approval-workflow/image017.png)

Uwaga: Można wybrać wielu użytkowników lub grup w danym momencie.

Wybór pojawi się na liście wybrane osoby zatwierdzające, jak pokazano poniżej:

![](media/azure-ad-pim-approval-workflow/image019.png)

Aby usunąć osobę zatwierdzającą, po prostu kliknij przycisk Usuń obok nazwy użytkownika.

Aby dodać dodatkowe osób zatwierdzających, powtórz te czynności.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Wyświetl historię żądania i zatwierdzenia dla ról uprzywilejowanych wszystko

Aby wyświetlić historię żądania i zatwierdzania wszystkich uprzywilejowanych ról, wybierz pozycję Historia inspekcji na pulpicie nawigacyjnym:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Sortowanie danych według akcji i poszukaj "Aktywacja Approved"

### <a name="view-pending-approvals-requests"></a>Wyświetlanie oczekujących zatwierdzeń (żądań)

Jako delegowany osoby zatwierdzającej będzie otrzymywał powiadomienia e-mail, kiedy żądanie oczekuje na zatwierdzenie. Aby wyświetlić te żądania w portalu usługi PIM, z poziomu pulpitu nawigacyjnego (w nowy sposób nawigacji) wybierz kartę "Oczekujące żądania zatwierdzenia" na pasku nawigacyjnym po lewej stronie.

![](media/azure-ad-pim-approval-workflow/image023.png)

W tym miejscu zobaczysz listę żądań oczekujących na zatwierdzenie:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Zatwierdzanie lub odrzucanie żądań o podniesienie uprawnień roli, (pojedynczy i/lub zbiorcze)

Zaznacz żądania, które chcesz zatwierdzić lub odrzucić, a następnie kliknij przycisk na pasku akcji, który odpowiada podjęcie decyzji:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Podaj uzasadnienie dla mojego lub odrzuceniu

Spowoduje to otwarcie nowego bloku Zatwierdź lub Odrzuć wiele żądań jednocześnie. Wprowadź uzasadnienie decyzji, a następnie kliknij pozycję Zatwierdź (lub Odrzuć) u dołu lub bloku:

![](media/azure-ad-pim-approval-workflow/image029.png)

Po zakończeniu procesu żądania w symbolu stanu będzie odzwierciedlać decyzję podjęliśmy (w tym przykładzie decyzji jest zatwierdzanie):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Żądanie aktywacji roli, która wymaga zatwierdzenia

Mogą zostać rozpoczęte żądanie aktywacji roli, która wymaga zatwierdzenia od starego nawigacji PIM lub nową nawigację jako proces aktywacji roli pozostają bez zmian. Po prostu wybierz rolę z listy ról, aby aktywować:

![](media/azure-ad-pim-approval-workflow/image033.png)

Jeśli roli uprzywilejowanej wymaga uwierzytelniania wieloskładnikowego, zostanie wyświetlony monit, aby najpierw wykonać to zadanie:

![](media/azure-ad-pim-approval-workflow/image035.png)

Po zakończeniu, kliknij pozycję Aktywuj i uzasadnić (jeśli jest to wymagane):

![](media/azure-ad-pim-approval-workflow/image037.png)

Osoby zgłaszającej żądanie zostanie wyświetlone powiadomienie, że żądanie oczekuje na zatwierdzenie:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Wyświetl stan żądanie aktywacji

Wyświetlanie stanu oczekującego żądania aktywacji musi być dostępny z nową nawigację. Na pasku nawigacyjnym po lewej stronie wybierz kartę "Moje żądania":

![](media/azure-ad-pim-approval-workflow/image041.png)

Stan żądania wartością domyślną jest "Pending", ale można przełączać w celu wyświetlenia wszystkich lub odrzucone żądania.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Ukończenia zadania w usłudze Azure AD, jeśli Aktywacja została zatwierdzona.

Gdy żądanie zostanie zatwierdzone, rola jest aktywna i może kontynuować pracę, aby wymaga tej roli.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Kolejne kroki

Twoja opinia jest dla nas bardzo cenne. Możesz udostępnić komentarze i opinie nam tutaj!
