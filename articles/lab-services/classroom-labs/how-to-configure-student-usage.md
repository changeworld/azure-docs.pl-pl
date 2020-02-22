---
title: Skonfiguruj ustawienia użycia w obszarze laboratoria Azure Lab Services
description: Dowiedz się, jak skonfigurować liczbę użytkowników dla laboratorium, pobrać je z laboratorium, określić liczbę godzin, przez które mogą korzystać z maszyny wirtualnej, i nie tylko.
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
ms.openlocfilehash: 9fda1295bcdcd44b8a92c101c2aa445197ce6d4a
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539241"
---
# <a name="add-and-manage-lab-users"></a>Dodawanie użytkowników laboratorium i zarządzanie nimi
W tym artykule opisano sposób dodawania użytkowników do laboratorium, uzyskiwania ich zarejestrowanych w laboratorium, kontrolowania liczby godzin, w których mogą korzystać z maszyny wirtualnej, i nie tylko. 


## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**. Domyślnie włączona jest opcja **Ogranicz dostęp**. Gdy to ustawienie jest włączone, a użytkownik nie znajduje się na liście użytkowników, nie może zarejestrować się w laboratorium nawet przy użyciu linku rejestracji. Tylko użytkownicy na liście mogą zarejestrować się w laboratorium przy użyciu przesłanego linku rejestracji. Podczas tej procedury dodasz użytkowników do listy. Możesz też wyłączyć opcję **Ogranicz dostęp**, co umożliwi użytkownikom zarejestrowanie się w laboratorium, pod warunkiem, że mają link rejestracji. 
2. Wybierz pozycję **Dodaj użytkowników** na pasku narzędzi, a następnie wybierz pozycję **Dodaj według adresów e-mail**. 

    ![Przycisk Dodaj użytkowników](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stronie **Dodawanie użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu, rozdzielając je średnikami. 

    ![Dodawanie adresów e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz pozycję **Zapisz**. Na liście zostaną wyświetlone adresy e-mail użytkowników oraz ich stan rejestracji. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Nazwy użytkowników na liście zostaną wyświetlone po ich zarejestrowaniu do laboratorium. Nazwa wyświetlana na liście jest zbudowana przy użyciu imion i nazwiska użytkownika w Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Dodawanie użytkowników przez przekazanie pliku CSV
Możesz również dodać użytkowników, przekazując plik CSV z adresami e-mail użytkowników.

1. Utwórz plik CSV z adresami e-mail użytkowników w jednej kolumnie.

    ![Plik CSV z użytkownikami](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na stronie **Użytkownicy** laboratorium wybierz pozycję **Dodaj użytkowników** na pasku narzędzi, a następnie wybierz pozycję Przekaż plik **CSV**.

    ![Przycisk Przekaż wolumin CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Wybierz plik CSV z adresami e-mail użytkownika. Gdy wybierzesz pozycję **Otwórz** po wybraniu pliku CSV, zobaczysz następujące okno **dodawania użytkowników** . Lista adresów e-mail zostanie wypełniona z użyciem adresów e-mail z pliku CSV. 

    ![Okno dodawania użytkowników wypełnione adresami e-mail z pliku CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. W oknie **Dodawanie użytkowników** wybierz pozycję **Zapisz** . 
5. Upewnij się, że użytkownicy są wyświetlani na liście użytkowników. 

    ![Lista dodanych użytkowników](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Wyślij zaproszenia do użytkowników
Aby wysłać link rejestracji do uczniów, użyj jednej z poniższych metod. Pierwsza metoda pokazuje, jak wysyłać wiadomości e-mail do uczniów przy użyciu linku rejestracji i opcjonalnej wiadomości. Druga metoda pokazuje, jak uzyskać link do rejestracji, który można udostępniać innym osobom w dowolny sposób. 

W przypadku włączenia **ograniczenia dostępu** dla laboratorium tylko użytkownicy z listy użytkowników mogą korzystać z linku rejestracji w celu zarejestrowania się w laboratorium. Ta opcja jest domyślnie włączona. 

### <a name="invite-all-users"></a>Zapraszanie wszystkich użytkowników
1. Przejdź do widoku **Użytkownicy** , jeśli nie masz już na stronie, a następnie wybierz pozycję **Zaproś wszystkie** na pasku narzędzi. 

    ![Wybierz uczniów](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Na stronie **wysyłanie zaproszenia** pocztą e-mail wprowadź opcjonalną wiadomość, a następnie wybierz pozycję **Wyślij**. Wiadomość e-mail automatycznie zawiera link rejestracji. Możesz uzyskać ten link rejestracji, wybierając pozycję **... (wielokropek)** na pasku narzędzi i w **łączu rejestracji**. 

    ![Wyślij link rejestracji za pośrednictwem poczty e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Stan **zaproszenia** zostanie wyświetlony na liście **Użytkownicy** . Stan powinien zostać zmieniony na **wysłanie** , a następnie do **wysłania \<Data >** . 

    Aby uzyskać więcej informacji na temat dodawania uczniów do klasy i zarządzania ich użyciem laboratorium, zobacz [jak skonfigurować użycie ucznia](how-to-configure-student-usage.md).

### <a name="invite-selected-users"></a>Zaproś wybranych użytkowników

1. Wybierz użytkownika lub wielu użytkowników z listy. 
2. Następnie wybierz ikonę **koperty** , która jest widoczna w wybranym wierszu (lub) wybierz pozycję **Zaproś** na pasku narzędzi. 

    ![Zaproś wybranych użytkowników](../media/how-to-configure-student-usage/invite-selected-users.png)
3. W oknie **wysyłanie zaproszenia** pocztą e-mail wprowadź opcjonalny **komunikat**, a następnie wybierz pozycję **Wyślij**. 

    ![Wyślij wiadomość e-mail do wybranych użytkowników](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Stan tej operacji zostanie wyświetlony w kolumnie **zaproszenie** na liście **Użytkownicy** . Wiadomość e-mail z zaproszeniem zawiera link rejestracji, którego użytkownicy mogą użyć do zarejestrowania się w laboratorium.

1. Przejdź do widoku **Użytkownicy** , jeśli nie jesteś już na stronie. 

## <a name="get-registration-link"></a>Pobierz łącze rejestracji
Możesz również uzyskać link rejestracji z portalu i wysłać go przy użyciu własnej aplikacji klienckiej poczty e-mail. 

1. Przejdź do widoku **Użytkownicy** , wybierając pozycję **Użytkownicy** w menu po lewej stronie. 
2. Wybierz **... (wielokropek)** na pasku narzędzi, a następnie wybierz pozycję **link do rejestracji**.

    ![Link rejestracji ucznia](../media/how-to-configure-student-usage/registration-link-button.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj** . Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/how-to-configure-student-usage/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz pozycję **gotowe**. 
4. Wyślij **link rejestracji** do ucznia, aby student mógł zarejestrować się dla klasy. 

## <a name="view-registered-users"></a>Wyświetl zarejestrowanych użytkowników

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft.
3. Na stronie **My labs** (Moje laboratoria) wybierz laboratorium, którego użycie chcesz śledzić. 
4. Wybierz kafelek **Users** (Użytkownicy) lub pozycję **Users** (Użytkownicy) w menu po lewej stronie. Zostanie wyświetlona lista uczniów, którzy zarejestrowali się w Twoim laboratorium.  

    ![Zarejestrowani użytkownicy](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Ustawianie przydziałów dla użytkowników
Można ustawić przydziały dla poszczególnych użytkowników, wykonując następujące czynności: 

1. Wybierz pozycję **Użytkownicy** w menu po lewej stronie, jeśli strona nie jest już aktywna. 
2. Wybierz pozycję **przydział na użytkownika: numer \<> godziny** na pasku narzędzi. 
3. Na stronie **przydziały na użytkownika** Określ liczbę godzin, które chcesz nadać każdemu użytkownikowi (studentowi) poza zaplanowanym czasem klasy, a następnie wybierz pozycję **Zapisz**.

    ![Przydział na użytkownika](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Na pasku narzędzi zostaną wyświetlone zmienione wartości: **limit przydziału na użytkownika: \<liczba godzin >** a także na liście Użytkownicy.

    ![Przydział na użytkownika — po](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Zaplanowany czas działania maszyn wirtualnych](how-to-create-schedules.md) nie jest uwzględniany w stosunku do przydziału przydzielonego dla użytkownika. Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="set-additional-quota-for-a-specific-user"></a>Ustawianie dodatkowego przydziału dla określonego użytkownika
Możesz określić dodatkowy przydział dla użytkownika. Ten limit przydziału jest poza wspólnym zestawem przydziału ustawionym dla wszystkich użytkowników w poprzedniej sekcji. Na przykład jeśli użytkownik (jako instruktor) ustawił limit przydziału dla wszystkich użytkowników na 10 godzin i ustawił dodatkowy przydział 5 godzin dla określonych użytkowników, będzie miał 15 (10 + 5) godzin przydziału. Jeśli w późniejszym czasie zmienisz wspólny limit przydziału na, powiedzmy 15, a następnie użytkownik uzyska 20 (15 + 5) godzin przydziału. Należy pamiętać, że ten całkowity limit przydziału jest poza zaplanowanym terminem. Czas spędzony przez student w maszynie wirtualnej laboratorium w czasie planowania nie jest uwzględniany w stosunku do tego przydziału. 

Aby to zrobić, wykonaj następujące kroki:

1. Wybierz użytkownika (student) z listy użytkowników na stronie **Użytkownicy** .
2. Następnie wybierz pozycję **Dopasuj przydział** na pasku narzędzi. 

    ![Przycisk Dostosuj przydział](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Wprowadź liczbę **dodatkowych godzin** dla wybranego użytkownika lub użytkowników, a następnie wybierz pozycję **Zastosuj**. 

    ![Dodatkowy przydział dla użytkownika](../media/how-to-configure-student-usage/additional-quota.png)
4. Zobaczysz zaktualizowane użycie dla użytkownika w kolumnie **użycie** . 

    ![Nowe użycie dla użytkownika](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Konta uczniów
Aby dodać uczniów do laboratorium zajęć, użyj swoich kont e-mail. Mogą być używane następujące typy kont e-mail:

- Konto e-mail ucznia, które jest dostarczane przez Azure Active Directory Office 365 (AAD). 
- Konto e-mail firmy Microsoft, takie jak `@outlook.com`, `@hotmail.com`, `@msn.com`lub `@live.com`.
- Konto e-mail inne niż firmy Microsoft, na przykład udostępniane przez firmę Yahoo lub Google. Jednak te typy kont muszą być połączone z konto Microsoft.
- Konto usługi GitHub. To konto musi być połączone z konto Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Korzystanie z konta e-mail innego niż Microsoft
Studenci mogą korzystać z kont e-mail innych niż firmy Microsoft w celu zarejestrowania i zalogowania się do laboratorium zajęć.  Jednak rejestracja wymaga, aby uczniowie najpierw utworzyły konto Microsoft, które są połączone ze swoimi adresami e-mail spoza firmy Microsoft.

Wielu uczniów może już mieć konto Microsoft połączony z adresami e-mail spoza firmy Microsoft. Na przykład uczniowie mają już konto Microsoft, jeśli użyły swojego adresu e-mail od innych produktów lub usług firmy Microsoft, takich jak Office, Skype, OneDrive lub Windows.  

Gdy student kliknie adres URL rejestracji, aby zalogować się do klasy, zostanie wyświetlony monit o podanie adresu e-mail i hasła. Jeśli student podejmie próbę zalogowania się przy użyciu niekonto Microsoft, który nie ma powiązanego konto Microsoft, student otrzyma następujący komunikat o błędzie: 

![Komunikat o błędzie](../media/how-to-configure-student-usage/cant-find-account.png)

Aby zarejestrować się w celu uzyskania konto Microsoft, uczniowie powinni przejść do [http://signup.live.com](http://signup.live.com).  

> [!IMPORTANT]
> Gdy uczniowie logują się do laboratorium z klasą, nie mają możliwości utworzenia konto Microsoft. Dlatego zalecamy uwzględnienie tego linku do rejestracji w wiadomości e-mail z rejestracją w laboratorium zajęć, która jest wysyłana do uczniów, którzy korzystają z kont innych niż Microsoft.

### <a name="using-a-github-account"></a>Korzystanie z konta usługi GitHub
Studenci mogą także użyć istniejącego konta usługi GitHub do zarejestrowania się i zalogować się do laboratorium zajęć. Jeśli student ma już konto Microsoft połączony z kontem usługi GitHub, może się zalogować i podać hasło, jak pokazano w poprzedniej sekcji. Jeśli konto usługi GitHub nie zostało jeszcze połączone z konto Microsoft, należy wybrać **Opcje logowania**:

![Link opcji logowania](../media/how-to-configure-student-usage/signin-options.png)

Na stronie **Opcje logowania** wybierz pozycję **Zaloguj się za pomocą usługi GitHub**.

![Zaloguj się przy użyciu linku GitHub](../media/how-to-configure-student-usage/signin-github.png)

Na koniec są wyświetlane monity o utworzenie konto Microsoft połączonego z kontem usługi GitHub. Odbywa się to automatycznie po wybraniu przez studenta **przycisku Dalej**.  Student jest następnie natychmiast zalogowany i połączony z laboratorium zajęć.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
