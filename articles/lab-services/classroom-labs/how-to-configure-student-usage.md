---
title: Konfigurowanie ustawień użycia w laboratoriach szkolnych usługi Azure Lab Services
description: Dowiedz się, jak skonfigurować liczbę uczniów dla laboratorium, uzyskać ich zarejestrowane w laboratorium, kontrolować liczbę godzin, które mogą korzystać z maszyny Wirtualnej i więcej.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159459"
---
# <a name="add-and-manage-lab-users"></a>Dodawanie użytkowników laboratorium i zarządzanie nimi

W tym artykule opisano sposób dodawania użytkowników uczniów do laboratorium, rejestrowania ich w laboratorium, kontrolowania liczby dodatkowych godzin, które mogą korzystać z maszyny wirtualnej i nie tylko. 

## <a name="add-users-to-a-lab"></a>Dodawanie użytkowników do laboratorium

W tej sekcji dodasz uczniów do laboratorium ręcznie lub przesyłając plik CSV. Wykonaj następujące czynności:

1. W lewym okienku wybierz pozycję **Użytkownicy**. 

    Domyślnie opcja **Ogranicz dostęp** jest włączona i, o ile nie znajdują się na liście użytkowników, uczniowie nie mogą zarejestrować się w laboratorium, nawet jeśli mają łącze rejestracyjne. Tylko wymienieni użytkownicy mogą zarejestrować się w laboratorium za pomocą wysyłanego linku rejestracyjnego. Podczas tej procedury dodasz użytkowników do listy. Alternatywnie można wyłączyć **Ogranicz dostęp,** co pozwala uczniom zarejestrować się w laboratorium, o ile mają link rejestracyjny. 

1. U góry okienka **Użytkownicy** wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję Dodaj według **adresu e-mail**. 

    ![Przycisk "Dodaj użytkowników"](../media/how-to-configure-student-usage/add-users-button.png)

1. W okienku **Dodaj użytkowników** wprowadź adresy e-mail uczniów w oddzielnych wierszach lub w jednym wierszu oddzielonym średnikami. 

    ![Dodawanie adresów e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Wybierz **pozycję Zapisz**. 

    Na liście są wyświetlane adresy e-mail i stany bieżących użytkowników, niezależnie od tego, czy są oni zarejestrowani w laboratorium, czy nie. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Po zarejestrowaniu uczniów w laboratorium na liście wyświetlane są ich nazwiska. Nazwa wyświetlana na liście jest konstruowana przy użyciu imienia i nazwiska uczniów w usłudze Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Dodawanie użytkowników przez przesłanie pliku CSV

Możesz również dodać użytkowników, przesyłając plik CSV zawierający ich adresy e-mail.

1. W programie Microsoft Excel utwórz plik CSV zawierający listę adresów e-mail uczniów w jednej kolumnie.

    ![Lista użytkowników w pliku CSV](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. U góry okienka **Użytkownicy** wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję Przekaż **plik CSV**.

    ![Przycisk "Prześlij CSV"](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Wybierz plik CSV zawierający adresy e-mail uczniów, a następnie wybierz pozycję **Otwórz**.

    Okno **Dodaj użytkowników** wyświetla listę adresów e-mail z pliku CSV. 

    ![Okno "Dodaj użytkowników" z adresami e-mail z pliku CSV](../media/how-to-configure-student-usage/add-users-window.png)

1. Wybierz **pozycję Zapisz**. 

1. W okienku **Użytkownicy** wyświetl listę dodanych uczniów. 

    ![Lista dodanych użytkowników w okienku "Użytkownicy"](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Wysyłanie zaproszeń do użytkowników

Aby wysłać łącze rejestracyjne do nowych użytkowników, użyj jednej z następujących metod. 

Jeśli opcja **Ogranicz dostęp** jest włączona dla laboratorium, tylko wymienieni użytkownicy mogą używać łącza rejestracji do zarejestrowania się w laboratorium. Ta opcja jest domyślnie włączona. 

### <a name="invite-all-users"></a>Zaproś wszystkich użytkowników

Ta metoda pokazuje, jak wysłać wiadomość e-mail z linkiem rejestracyjnym i opcjonalną wiadomością do wszystkich wymienionych uczniów.

1. W okienku **Użytkownicy** wybierz pozycję **Zaproś wszystkich**. 

    ![Przycisk "Zaproś wszystkich"](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. W oknie **Wyślij zaproszenie pocztą e-mail** wprowadź opcjonalną wiadomość, a następnie wybierz pozycję **Wyślij**. 

    Wiadomość e-mail automatycznie zawiera link rejestracyjny. Aby uzyskać i zapisać link do rejestracji oddzielnie, wybierz wielokropek (**...**) w górnej części okienka **Użytkownicy,** a następnie wybierz **łącze Rejestracja**. 

    ![Okno "Wyślij link rejestracyjny pocztą e-mail"](../media/tutorial-setup-classroom-lab/send-email.png)

    W kolumnie **Zaproszenie** na liście **Użytkownicy** jest wyświetlany stan zaproszenia dla każdego dodanego użytkownika. Stan powinien zmienić się na **Wysyłanie,** a następnie **na Wysłane w dniu \<>**. 

### <a name="invite-selected-users"></a>Zapraszanie wybranych użytkowników

Ta metoda pokazuje, jak zaprosić tylko niektórych uczniów i uzyskać link rejestracyjny, który możesz udostępnić innym osobom.

1. W okienku **Użytkownicy** wybierz studenta lub wielu uczniów na liście. 

1. W wierszu wybranego ucznia wybierz ikonę **koperty** lub na pasku narzędzi wybierz pozycję **Zaproś**. 

    ![Zapraszanie wybranych użytkowników](../media/how-to-configure-student-usage/invite-selected-users.png)

1. W oknie **Wyślij zaproszenie pocztą e-mail** wprowadź **opcjonalną wiadomość**, a następnie wybierz pozycję **Wyślij**. 

    ![Wysyłanie wiadomości e-mail do wybranych użytkowników](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    W okienku **Użytkownicy** wyświetlany jest stan tej operacji w kolumnie **Zaproszenie** w tabeli. Wiadomość e-mail z zaproszeniem zawiera link rejestracyjny, za pomocą którego uczniowie mogą zarejestrować się w laboratorium.

## <a name="get-the-registration-link"></a>Uzyskaj link rejestracyjny

W tej sekcji możesz uzyskać link rejestracyjny z portalu i wysłać go za pomocą własnej aplikacji e-mail. 

1. W okienku **Użytkownicy** wybierz **łącze Rejestracja**.

    ![Link rejestracji ucznia](../media/how-to-configure-student-usage/registration-link-button.png)

1. W oknie **Rejestracja użytkownika** wybierz pozycję **Kopiuj**, a następnie wybierz pozycję **Gotowe**. 

    ![Okno "Rejestracja użytkownika"](../media/how-to-configure-student-usage/registration-link.png)

    Link zostanie skopiowany do Schowka. 
    
1. W aplikacji e-mail wklej link rejestracyjny, a następnie wyślij wiadomość e-mail do ucznia, aby uczeń mógł zarejestrować się w klasie. 

## <a name="view-registered-users"></a>Wyświetlanie zarejestrowanych użytkowników

1. Przejdź do witryny [azure lab services](https://labs.azure.com) w sieci Web. 
1. Wybierz pozycję **Zaloguj się**, a następnie wprowadź poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft.
1. Na stronie **Moje laboratoria** wybierz laboratorium, którego użycie chcesz śledzić. 
1. W lewym okienku wybierz pozycję **Użytkownicy**lub wybierz kafelek **Użytkownicy.** 

    W okienku **Użytkownicy** wyświetlana jest lista uczniów, którzy zarejestrowali się w laboratorium.  

    ![Lista zarejestrowanych użytkowników](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Ustawianie przydziałów dla użytkowników

Dla każdego ucznia można ustawić limit godzin, wykonując następujące czynności: 

1. W okienku **Użytkownicy** wybierz **pozycję \<Przydział na użytkownika: liczba> godzin** na pasku narzędzi. 
1. W oknie **Przydział na użytkownika** określ liczbę godzin, które chcesz dać każdemu uczniowi poza zaplanowanym czasem zajęć, a następnie wybierz pozycję **Zapisz**.

    ![Okno "Przydział na użytkownika"](../media/how-to-configure-student-usage/quota-per-user.png)    

    Zmienione wartości są teraz wyświetlane na **przyobliczanie na użytkownika: \<liczba godzin>** na pasku narzędzi i na liście użytkowników, jak pokazano poniżej:

    ![Godziny przydziału na użytkownika](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Zaplanowany czas pracy maszyn wirtualnych](how-to-create-schedules.md) nie jest wliczany do przydziału przydzielonego studentowi. Przydział jest dla czasu poza zaplanowanych godzin, które student spędza na maszynach wirtualnych. 

## <a name="set-additional-quotas-for-specific-users"></a>Ustawianie dodatkowych przydziałów dla określonych użytkowników

Można określić przydziały dla niektórych uczniów poza wspólne przydziały, które zostały ustawione dla wszystkich użytkowników w poprzedniej sekcji. Na przykład, jeśli jako instruktor ustawisz limit dla wszystkich uczniów na 10 godzin i ustawisz dodatkowy przydział 5 godzin dla określonego ucznia, ten uczeń otrzyma 15 (10 + 5) godzin przydziału. Jeśli później zmienisz wspólny przydział na, powiedzmy, 15, uczeń otrzyma 20 (15 + 5) godzin przydziału. Należy pamiętać, że ten ogólny przydział jest poza zaplanowanym czasem. Czas, który uczeń spędza na maszynie wirtualnej laboratorium w zaplanowanym czasie nie jest wliczany do tego przydziału. 

Aby ustawić dodatkowe przydziały, wykonaj następujące czynności:

1. W okienku **Użytkownicy** wybierz ucznia z listy, a następnie wybierz pozycję **Dopasuj przydział** na pasku narzędzi. 

    ![Przycisk "Dostosuj przydział"](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. W obszarze **Dostosuj \<przydział dla wybranego użytkownika lub adres e-mail użytkownika>** wprowadź liczbę dodatkowych godzin pracy w laboratorium, które chcesz przyznać wybranej studentce lub studentom, a następnie wybierz pozycję **Zastosuj**. 

    !["Dostosuj przydział ..." Okno](../media/how-to-configure-student-usage/additional-quota.png)

    Kolumna **Użycie** wyświetla zaktualizowany przydział dla wybranych uczniów. 

    ![Nowe użycie dla użytkownika](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Konta uczniów

Aby dodać uczniów do laboratorium w klasie, używasz ich kont e-mail. Uczniowie mogą mieć następujące typy kont e-mail:

- Studenckie konto e-mail udostępniane przez wystąpienie usługi Azure Active Directory dla usługi Office 365 dla twojej uczelni. 
- Konto e-mail domeny firmy Microsoft, takie jak *outlook.com*, *hotmail.com* *, msn.com*lub *live.com*.
- Konto e-mail inne niż Microsoft, takie jak konto dostarczone przez Yahoo! lub Google. Jednak te typy kont muszą być połączone z kontem Microsoft.
- Konto usługi GitHub. To konto musi być połączone z kontem Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Korzystanie z konta e-mail firmy innych niż Microsoft
Uczniowie mogą używać kont e-mail innych firm, aby zarejestrować się i zalogować się do laboratorium w klasie.  Jednak rejestracja wymaga, aby najpierw utworzyć konto Microsoft, które jest połączone z ich adres e-mail firmy innych niż Microsoft.

Wielu uczniów może mieć już konto Microsoft, które jest połączone z ich adresem e-mail firmy innych niż Microsoft. Na przykład uczniowie mają już konto Microsoft, jeśli używali swojego adresu e-mail z innymi produktami lub usługami firmy Microsoft, takimi jak Office, Skype, OneDrive lub Windows.  

Gdy uczniowie używają linku rejestracyjnego do logowania się do klasy, są monitowani o podanie adresu e-mail i hasła. Uczniowie, którzy próbują zalogować się za pomocą konta innych niż Microsoft, które nie jest połączone z kontem Microsoft, otrzymają następujący komunikat o błędzie: 

![Komunikat o błędzie podczas logowania](../media/how-to-configure-student-usage/cant-find-account.png)

Oto link dla studentów, aby [zarejestrować się na konto Microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Gdy uczniowie logują się do laboratorium w klasie, nie mają możliwości utworzenia konta Microsoft. Z tego powodu zalecamy dołączenie tego linku do rejestracji w wiadomości e-mail z rejestracją w laboratorium klasy, http://signup.live.comktórą wysyłasz do uczniów korzystających z kont innych niż Microsoft.

### <a name="use-a-github-account"></a>Korzystanie z konta Usługi GitHub
Uczniowie mogą również korzystać z istniejącego konta Usługi GitHub, aby zarejestrować się i zalogować się do laboratorium w klasie. Jeśli mają już konto Microsoft połączone z kontem GitHub, uczniowie mogą zalogować się i podać swoje hasło, jak pokazano w poprzedniej sekcji. 

Jeśli nie połączyli jeszcze swojego konta GitHub z kontem Microsoft, mogą wykonać następujące czynności:

1. Wybierz łącze **Opcje logowania,** jak pokazano poniżej:

    ![Łącze "Opcje logowania"](../media/how-to-configure-student-usage/signin-options.png)

1. W oknie **Opcje logowania** wybierz pozycję Zaloguj się za **pomocą githuuba**.

    ![Łącze "Zaloguj się za pomocą githubu"](../media/how-to-configure-student-usage/signin-github.png)

    W wierszu uczniowie następnie utworzyć konto Microsoft, który jest połączony z ich konta GitHub. Łączenie odbywa się automatycznie po **wybraniu przycisku Dalej**. Następnie są one natychmiast zalogowane i połączone z laboratorium w klasie.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Eksportowanie listy użytkowników do pliku CSV

1. Przejdź do okienka **Użytkownicy.**
1. Na pasku narzędzi wybierz wielokropek (**...**), a następnie wybierz **pozycję Eksportuj PLIK CSV**. 

    ![Przycisk "Eksportuj CSV"](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- Dla administratorów: [tworzenie kont laboratoryjnych i zarządzanie nimi](how-to-manage-lab-accounts.md)
- Dla właścicieli [laboratoriów: tworzenie laboratoriów i zarządzanie nimi](how-to-manage-classroom-labs.md) oraz [konfigurowanie szablonów i publikowanie](how-to-create-manage-template.md)
- Dla użytkowników [laboratoriów: dostęp do laboratoriów szkolnych](how-to-use-classroom-lab.md)
