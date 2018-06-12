---
title: Azure Privileged Identity Management przepływów pracy | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przepływów pracy w zarządzania tożsamości uprzywilejowanych (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 6e14d91eae367ef62d1ff2e4cb6ddeb47c9fbbea
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261019"
---
# <a name="approvals"></a>Zatwierdzenia

## <a name="overview"></a>Przegląd

Z zatwierdzenia Privileged Identity Management można skonfigurować role, aby wymagały zatwierdzenia aktywacji i wybierz jednego lub wielu użytkowników lub grup w ramach delegowanego osób zatwierdzających. Zachowaj odczytu, aby dowiedzieć się, jak skonfigurować role i wybierz osób zatwierdzających.


## <a name="new-terminology"></a>Nowe terminologia

*Kwalifikujące się roli użytkownika* — kwalifikujących się roli użytkownik jest użytkownikiem w organizacji, który jest przypisany do roli usługi Azure AD jako kwalifikujących się (rola wymaga aktywacji).

*Delegowane osoba zatwierdzająca* — delegowanego osoba zatwierdzająca jest jednym lub wielu osób lub grup w ramach usługi Azure AD odpowiedzialny za zatwierdzanie żądań aktywacji ról.

## <a name="scenarios"></a>Scenariusze

Prywatnej wersji zapoznawczej obsługuje następujące scenariusze:

**Jako uprzywilejowane roli administratora pararozaniliny można wykonywać następujące czynności:**

-   [Włącz zatwierdzenia dla określonych ról](#enable-approval-for-specific-roles)

-   [Określ osoba zatwierdzająca użytkowników i grupy, aby zatwierdzić żądań](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Wyświetl historię żądań i zatwierdzania dla wszystkich ról uprzywilejowanych](#view-request-and-approval-history-for-all-privileged-roles)

**Wyznaczone osoby zatwierdzającej można:**

-   [Wyświetlanie oczekujących zatwierdzeń (liczba żądań)](#view-pending-approvals-requests)

-   [Zatwierdzanie lub odrzucanie żądań w celu podniesienia roli (pojedynczego i/lub masowo)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Podaj uzasadnienie dla moich lub odrzuceniu](#provide-justification-for-my-approval/rejection) 

**Kwalifikujące się roli użytkownika może:**

-   [Żądania aktywacji roli, która wymaga zatwierdzenia](#request-activation-of-a-role-that-requires-approval)

-   [Wyświetl stan żądanie aktywacji](#view-the-status-of-your-request-to-activate)

-   [Wykonać zadanie w usłudze Azure AD, jeśli Aktywacja została zatwierdzona](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Nawigacja

Zaktualizowaliśmy nawigacji do obsługi zatwierdzenia

![](media/azure-ad-pim-approval-workflow/image001.png)

Domyślna strona początkowa zapewnia wygodny dostęp do informacji na temat usługi PIM oraz Nowa dokumentacja zatwierdzenia.

![](media/azure-ad-pim-approval-workflow/image002.png)

Dodaliśmy również nową sekcję dla wszystkich użytkowników PIM, "Moje Historia inspekcji". W tym miejscu znajdziesz wszystkie informacje istotne dla Twojej tożsamości. W tym wszystkie swoje żądania oczekujące i ukończone, wszelkich decyzji podjętych dotyczące żądań, które należy rozwiązać i wszystkich poprzednich aktywacji roli w jednej lokalizacji.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Włącz zatwierdzenia dla określonych ról

Aby włączyć zatwierdzenia dla konkretnej roli, należy najpierw wybrać role katalogu z lewym pasku nawigacyjnym.

![](media/azure-ad-pim-approval-workflow/image004.png)

Znajdź i wybierz ustawienia w obszarze nawigacji po lewej stronie ról katalogu

![](media/azure-ad-pim-approval-workflow/image006.png)

Wybierz role uprzywilejowane:

![](media/azure-ad-pim-approval-workflow/image009.png)

Zaznacz pole wyboru "Włącz" w wymagają zatwierdzenia sekcji:

![](media/azure-ad-pim-approval-workflow/image011.png)

Po włączeniu bloku rozwinie się następujące informacje:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Jeśli nie określono żadnych osób zatwierdzających, PRA(s) stają się osoby zatwierdzające domyślne. PRA(s) będzie potrzebować zatwierdzić wszystkie żądania aktywacji dla tej roli.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Określ osoba zatwierdzająca użytkowników i grupy, aby zatwierdzić żądań

Aby delegować zatwierdzenia, kliknij opcję "Wybierz osób zatwierdzających":

![](media/azure-ad-pim-approval-workflow/image015.png)

Podczas ładowania bloku wybierz osób zatwierdzających może wyszukać określonego użytkownika lub grupy za pomocą pasek wyszukiwania w górnej lub wybierając z listy wstępnie wypełniane, a następnie kliknij "Wybierz" po zakończeniu:

![](media/azure-ad-pim-approval-workflow/image017.png)

Uwaga: Można wybrać wielu użytkowników lub grup w czasie.

Wybór będą wyświetlane na liście wybranych osób zatwierdzających, jak pokazano poniżej:

![](media/azure-ad-pim-approval-workflow/image019.png)

Aby usunąć osoba zatwierdzająca, po prostu kliknij przycisk Usuń przy jego nazwie.

Aby dodać dodatkowe osób zatwierdzających, powtórz ten proces.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Wyświetl historię żądań i zatwierdzania dla wszystkich ról uprzywilejowanych

Aby wyświetlić historię żądania i zatwierdzania dla wszystkich ról uprzywilejowanych, wybierz Historia inspekcji na pulpicie nawigacyjnym:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Sortowanie danych w ramach akcji i wyszukaj "Zatwierdzone aktywacji"

### <a name="view-pending-approvals-requests"></a>Wyświetlanie oczekujących zatwierdzeń (liczba żądań)

Zatwierdzającą delegowanego będzie otrzymywał powiadomienia e-mail, kiedy żądanie oczekuje na zatwierdzenie. Aby wyświetlić te żądania w portalu usługi PIM, z poziomu pulpitu nawigacyjnego (w obszarze nawigacji nowe) wybierz kartę "Oczekujących żądań zatwierdzenia" na pasku nawigacyjnym po lewej stronie.

![](media/azure-ad-pim-approval-workflow/image023.png)

Z tego miejsca zobaczysz listę żądania w oczekiwaniu na zatwierdzenie:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Zatwierdzanie lub odrzucanie żądań w celu podniesienia roli (pojedynczego i/lub masowo)

Wybierz żądania, które chcesz zaakceptować lub odrzucić, a następnie kliknij przycisk w pasku akcji, który odpowiada decyzji:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Podaj uzasadnienie dla moich lub odrzuceniu

Spowoduje to otwarcie nowego bloku na zatwierdzenie lub odrzucenie jednocześnie wiele żądań. Wprowadź uzasadnienie decyzji, a następnie kliknij przycisk Zatwierdź (lub odmawiać zezwolenia) u dołu lub bloku:

![](media/azure-ad-pim-approval-workflow/image029.png)

Po zakończeniu procesu żądania symbolu stanu będzie odzwierciedlać wprowadzone decyzji (w tym przykładzie decyzji jest zatwierdzić):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Żądania aktywacji roli, która wymaga zatwierdzenia

Mogą zostać rozpoczęte żądają aktywacji roli, która wymaga zatwierdzenia od starego nawigacji PIM lub nowe nawigacji, ponieważ proces aktywacji roli jest taka sama. Po prostu wybierz rolę z listy ról do aktywacji:

![](media/azure-ad-pim-approval-workflow/image033.png)

Jeśli ról uprzywilejowanych wymaga uwierzytelniania wieloskładnikowego, zostanie wyświetlony monit przeprowadzenie tego zadania:

![](media/azure-ad-pim-approval-workflow/image035.png)

Po jego zakończeniu, kliknij przycisk Aktywuj i uzasadnić (jeśli jest to wymagane):

![](media/azure-ad-pim-approval-workflow/image037.png)

Obiekt żądający zostanie wyświetlone powiadomienie, że żądanie oczekuje na zatwierdzenie:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Wyświetl stan żądanie aktywacji

Wyświetlanie stanu oczekującego żądania, aby aktywować musi być dostępny z nowego nawigacji. Na pasku nawigacyjnym po lewej stronie wybierz kartę "Moje żądania":

![](media/azure-ad-pim-approval-workflow/image041.png)

Stan żądania domyślnie przyjmowana jest jako "Oczekujące", ale można Przełącz, aby wyświetlić wszystkie lub odrzucone żądania.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Wykonać zadanie w usłudze Azure AD, jeśli Aktywacja została zatwierdzona

Gdy żądanie zostanie zatwierdzone, rola jest aktywna i może kontynuować pracę, która wymaga tej roli.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Następne kroki

Twoja opinia jest przydatna do nas. Sprawdź możesz udostępniać komentarze i opinie NAS tutaj!
