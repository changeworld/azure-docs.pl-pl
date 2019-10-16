---
title: Skonfiguruj ustawienia użycia w obszarze laboratoria Azure Lab Services | Microsoft Docs
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324342"
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

    ![Lista użytkowników](../media/how-to-configure-student-usage/users-list-new.png)

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

1. Przejdź do strony **Użytkownicy** w laboratorium. 
2. Wybierz pozycję **Zaproś wszystkie** z paska narzędzi. 
3. Wprowadź **komunikat** dla użytkowników. Jest to opcjonalny krok.
4. Następnie wybierz pozycję **Wyślij**.

    ![Zapraszanie wszystkich użytkowników](../media/how-to-configure-student-usage/invite-all.png)

    Stan tej operacji zostanie wyświetlony w kolumnie **zaproszenie** na liście **Użytkownicy** . Wiadomość e-mail z zaproszeniem będzie zawierać link rejestracji, którego użytkownicy mogą użyć do zarejestrowania się w laboratorium. 

### <a name="invite-selected-users"></a>Zaproś wybranych użytkowników

1. Wybierz użytkownika lub wielu użytkowników z listy. 
2. Następnie wybierz ikonę **koperty** , która jest widoczna w wybranym wierszu (lub) wybierz pozycję **Zaproś** na pasku narzędzi. 

    ![Zaproś wybranych użytkowników](../media/how-to-configure-student-usage/invite-selected-users.png)
3. W oknie **wysyłanie zaproszenia** pocztą e-mail wprowadź opcjonalny **komunikat**, a następnie wybierz pozycję **Wyślij**. 

    ![Wyślij wiadomość e-mail do wybranych użytkowników](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Stan tej operacji zostanie wyświetlony w kolumnie **zaproszenie** na liście **Użytkownicy** . Wiadomość e-mail z zaproszeniem będzie zawierać link rejestracji, którego użytkownicy mogą użyć do zarejestrowania się w laboratorium.

1. Jeśli jeszcze nie jesteś na tej stronie, przełącz się do widoku **Użytkownicy**. 

## <a name="get-registration-link"></a>Pobierz łącze rejestracji
Możesz również uzyskać link rejestracji z portalu i wysłać go przy użyciu własnej aplikacji klienckiej poczty e-mail. 

1. Przejdź do widoku **Użytkownicy** , wybierając pozycję **Użytkownicy** w menu po lewej stronie. 
2. Wybierz **... (wielokropek)** na pasku narzędzi, a następnie wybierz pozycję **link do rejestracji**.

    ![Link rejestracji ucznia](../media/how-to-configure-student-usage/registration-link-button.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj** . Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/how-to-configure-student-usage/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz pozycję **gotowe**. 
4. Wyślij **link rejestracji** do ucznia, aby student mógł zarejestrować się dla klasy. 

## <a name="view-users-registered-with-the-lab"></a>Wyświetlenie użytkowników zarejestrowanych w laboratorium

Wybierz pozycję **Użytkownicy** w menu po lewej stronie, aby wyświetlić listę użytkowników zarejestrowanych w laboratorium. 

![Lista użytkowników zarejestrowanych w laboratorium](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Ustawianie przydziałów dla użytkowników
Można ustawić przydziały dla poszczególnych użytkowników, wykonując następujące czynności: 

1. Wybierz pozycję **Użytkownicy** w menu po lewej stronie, jeśli strona nie jest już aktywna. 
2. Wybierz pozycję **przydział na użytkownika: &lt;number @ no__t-2 godz** . na pasku narzędzi. 
3. Na stronie **przydziały na użytkownika** Określ liczbę godzin, które chcesz nadać każdemu użytkownikowi (studentowi) poza zaplanowanym czasem klasy, a następnie wybierz pozycję **Zapisz**.

    ![Przydział na użytkownika](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Na pasku narzędzi są teraz widoczne zmienione wartości: **limit przydziału na użytkownika: &lt;number godzin @ no__t-2**. 

    ![Przydział na użytkownika — po](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Zaplanowany czas działania maszyn wirtualnych](how-to-create-schedules.md) nie jest uwzględniany w stosunku do przydziału przydzielonego dla użytkownika. Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="set-additional-quota-for-a-specific-user"></a>Ustawianie dodatkowego przydziału dla określonego użytkownika
Dla użytkownika można ustawić oddzielny przydział. Aby to zrobić, wykonaj następujące kroki:

1. Wybierz użytkownika (student) z listy użytkowników na stronie **Użytkownicy** .
2. Następnie wybierz pozycję **Dopasuj przydział** na pasku narzędzi. 

    ![Przycisk Dostosuj przydział](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Wprowadź liczbę **dodatkowych godzin** dla wybranego użytkownika lub użytkowników, a następnie wybierz pozycję **Zastosuj**. 

    ![Dodatkowy przydział dla użytkownika](../media/how-to-configure-student-usage/additional-quota.png)
4. Zaktualizowane użycie użytkownika będzie widoczne w kolumnie **użycie** . 

    ![Nowe użycie dla użytkownika](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
