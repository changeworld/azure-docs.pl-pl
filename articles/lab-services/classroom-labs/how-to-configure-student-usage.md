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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 86f22864c416ad2a90bea09c02675d6eb3322308
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385625"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurowanie ustawień i zasad użytkowania
W tym artykule opisano sposób dodawania użytkowników do laboratorium, uzyskiwania ich zarejestrowanych w laboratorium, kontrolowania liczby godzin, w których mogą korzystać z maszyny wirtualnej, i nie tylko. 


## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium
Jeśli masz włączoną opcję **Ogranicz dostęp** , Dodaj użytkowników (adresy e-mail) do listy.

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**.
2. Na pasku narzędzi wybierz pozycję **Dodaj użytkowników**. 

    ![Przycisk Dodaj użytkowników](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stronie **Dodawanie użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu, rozdzielając je średnikami. 

    ![Dodawanie adresów e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz pozycję **Zapisz**. Na liście zostaną wyświetlone adresy e-mail użytkowników oraz ich stan rejestracji. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Udostępnianie linku rejestracji uczniom
Aby wysłać link rejestracji do uczniów, użyj jednej z poniższych metod. Pierwsza metoda pokazuje, jak wysyłać wiadomości e-mail do uczniów przy użyciu linku rejestracji i opcjonalnej wiadomości. Druga metoda pokazuje, jak uzyskać link do rejestracji, który można udostępniać innym osobom w dowolny sposób. 

W przypadku włączenia **ograniczenia dostępu** dla laboratorium tylko użytkownicy z listy użytkowników mogą korzystać z linku rejestracji w celu zarejestrowania się w laboratorium. Ta opcja jest domyślnie włączona. 

### <a name="send-email-to-users"></a>Wyślij wiadomość e-mail do użytkowników
Azure Lab Services umożliwia nauczycielom wysyłanie zaproszeń do laboratorium pocztą e-mail do wszystkich lub wybranych uczniów bez konieczności korzystania z innego klienta poczty e-mail. Nauczyciele mogą przesuwać się na poszczególnych uczniów na liście, aby zobaczyć ikonę wiadomości e-mail dla każdego ucznia lub wybrać jednego lub więcej uczniów i użyć opcji **Wyślij zaproszenie** na pasku narzędzi. Ta funkcja wysyła wiadomość e-mail z linkiem rejestracji i wiadomością (jeśli istnieje) dodaną przez nauczycieli. Po wysłaniu zaproszenia stan zaproszenia zostanie zmieniony na **zaproszenie wysłane** , dzięki czemu nauczyciele mogą śledzić, którzy uczniowie już otrzymali link rejestracji i datę wysłania.

1. Jeśli jeszcze nie jesteś na tej stronie, przełącz się do widoku **Użytkownicy**. 
2. Wybierz pozycję określone lub wszyscy użytkownicy na liście. Aby wybrać określonych użytkowników, zaznacz pola wyboru w pierwszej kolumnie listy. Aby zaznaczyć opcję Wszyscy użytkownicy, zaznacz pole wyboru na początku tytułu pierwszej kolumny (**Nazwa**) lub zaznacz wszystkie pola wyboru dla wszystkich użytkowników na liście. Stan **zaproszenia** można zobaczyć na tej liście.  Na poniższej ilustracji stan zaproszenia dla wszystkich uczniów jest ustawiony na **zaproszenie nie zostało wysłane**. 

    ![Wybierz uczniów](../media/tutorial-setup-classroom-lab/select-students.png)
1. Wybierz **ikonę wiadomości e-mail (koperta)** w jednym z wierszy (lub) wybierz pozycję **Wyślij zaproszenie** na pasku narzędzi. Możesz również umieścić wskaźnik myszy nad nazwą ucznia na liście, aby zobaczyć ikonę wiadomości e-mail. 

    ![Wyślij link rejestracji za pośrednictwem poczty e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na stronie **wysyłanie linku do rejestracji za pośrednictwem poczty e-mail** wykonaj następujące czynności: 
    1. Wpisz **opcjonalny komunikat** , który chcesz wysłać do uczniów. Wiadomość e-mail automatycznie zawiera link rejestracji. 
    2. Na stronie **wysyłanie linku do rejestracji za pośrednictwem poczty e-mail** wybierz pozycję **Wyślij**. Zobaczysz stan zaproszenia, które zmienia się w celu **wysłania zaproszenia** , a następnie do **wysłania zaproszenia**. 
        
        ![Wysłane zaproszenia](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Pobierz łącze rejestracji
1. Przejdź do widoku **Użytkownicy** , wybierając pozycję **Użytkownicy** w menu po lewej stronie. 
2. Wybierz kafelek **Pobierz link rejestracji**.

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Zamknij**. 
4. Udostępnij **link rejestracji** studentowi, aby student mógł zarejestrować się dla klasy. 

## <a name="view-users-registered-with-the-lab"></a>Wyświetlenie użytkowników zarejestrowanych w laboratorium

Wybierz pozycję **Użytkownicy** w menu po lewej stronie, aby wyświetlić listę użytkowników zarejestrowanych w laboratorium. 

![Lista użytkowników zarejestrowanych w laboratorium](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Ustawianie przydziałów dla użytkowników
Można ustawić przydziały dla poszczególnych użytkowników, wykonując następujące czynności: 

1. Wybierz pozycję **Użytkownicy** w menu po lewej stronie, jeśli strona nie jest już aktywna. 
2. Wybierz **przydział dla użytkownika: 10 godzin** na pasku narzędzi. 
3. Na stronie przydziały na **użytkownika** Określ liczbę godzin, które chcesz nadać każdemu użytkownikowi (student): 
    1. **Łączna liczba godzin laboratorium na użytkownika**. Użytkownicy mogą korzystać z swoich maszyn wirtualnych przez określoną liczbę godzin (określoną dla tego pola), a **także w zaplanowanym czasie**. W przypadku wybrania tej opcji wprowadź **liczbę godzin** w polu tekstowym. 

        ![Liczba godzin na użytkownika](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 godzin (tylko harmonogram)** . Użytkownicy mogą używać swoich maszyn wirtualnych tylko w trakcie zaplanowanego czasu lub w przypadku, gdy właściciel laboratorium włączy dla nich maszyny wirtualne.

        ![Zero godzin — zaplanowana godzina](../media/how-to-configure-student-usage/zero-hours.png)
    4. Wybierz pozycję **Zapisz**. 
5. Na pasku narzędzi są teraz widoczne zmienione wartości: **Przydział na użytkownika: &lt;liczba godzin&gt;** . 

    ![Przydział na użytkownika](../media/how-to-configure-student-usage/quota-per-user.png)



> [!IMPORTANT]
> Przed wysłaniem linku rejestracji do uczniów, nauczyciele muszą ustawić harmonogram klasy, jeśli wybierze 0 godzin przydziału lub określić godziny przydziału dla laboratorium.
>
> [Zaplanowany czas działania maszyn wirtualnych](how-to-create-schedules.md) nie jest uwzględniany w stosunku do przydziału przydzielonego dla użytkownika. Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

### <a name="add-users-by-uploading-a-csv-file"></a>Dodawanie użytkowników przez przekazanie pliku CSV
Możesz również dodać użytkowników, przekazując plik CSV z adresami e-mail użytkowników.

1. Utwórz plik CSV z adresami e-mail użytkowników w jednej kolumnie.

    ![Przydział na użytkownika](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na stronie **Użytkownicy** laboratorium wybierz pozycję **Przekaż wolumin CSV** na pasku narzędzi.

    ![Przycisk Przekaż wolumin CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Wybierz plik CSV z adresami e-mail użytkownika. Gdy wybierzesz pozycję **Otwórz** po wybraniu pliku CSV, zobaczysz następujące okno **dodawania użytkowników** . Lista adresów e-mail zostanie wypełniona z użyciem adresów e-mail z pliku CSV. 

    ![Okno dodawania użytkowników wypełnione adresami e-mail z pliku CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. W oknie **Dodawanie użytkowników** wybierz pozycję **Zapisz** . 
5. Upewnij się, że użytkownicy są wyświetlani na liście użytkowników. 

    ![Lista dodanych użytkowników](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Zarządzanie maszynami wirtualnymi użytkowników
Gdy studenci zarejestrują się za pomocą Azure Lab Services przy użyciu dostępnego linku rejestracji, zobaczysz maszyny wirtualne przypisane do uczniów na karcie **maszyny wirtualne** . 

![Maszyny wirtualne przypisane do uczniów](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Na maszynie wirtualnej ucznia można wykonywać następujące zadania: 

- Zatrzymaj maszynę wirtualną, jeśli maszyna wirtualna jest uruchomiona. 
- Uruchom maszynę wirtualną, jeśli maszyna wirtualna jest zatrzymana. 
- Nawiąż połączenie z maszyną wirtualną. 
- Usuń maszynę wirtualną. 
- Wyświetl liczbę godzin używania maszyny wirtualnej przez użytkowników. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Aktualizacja liczby maszyn wirtualnych w laboratorium
Aby zaktualizować liczbę maszyn wirtualnych w laboratorium, wykonaj następujące czynności na stronie **Virtual Machines** :

1. W menu po lewej stronie wybierz pozycję **maszyny wirtualne** . 
2. Wybierz **pojemność laboratorium: &lt;liczba&gt; komputerów** na pasku narzędzi. 
3. Wprowadź **liczbę** maszyn wirtualnych.
4. Wybierz pozycję **Zapisz**.

    ![Maszyny wirtualne w laboratorium](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
