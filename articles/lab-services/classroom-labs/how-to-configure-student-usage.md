---
title: Konfigurowanie ustawień użycia w laboratorium na potrzeby zajęć z usługi Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować liczbę użytkowników na potrzeby laboratorium, Uzyskaj je zarejestrowane w usłudze laboratorium, kontrolować liczbę godzin, które mogą używać maszyny Wirtualnej i nie tylko.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 494c285f1c096a84925d9d9a4fb98409960e5230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703782"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurowanie zasad i ustawień użycia
W tym artykule opisano, jak dodać użytkowników do laboratorium, Uzyskaj je zarejestrowane w usłudze laboratorium, kontrolować liczbę godzin, które mogą używać maszyny Wirtualnej i nie tylko. 


## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium
Jeśli masz **ograniczać** włączone, dodawanie użytkowników (adresy e-mail) do listy.

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**.
2. Na pasku narzędzi wybierz pozycję **Dodaj użytkowników**. 

    ![Przycisk Dodaj użytkowników](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stronie **Dodawanie użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu, rozdzielając je średnikami. 

    ![Dodawanie adresów e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz pozycję **Zapisz**. Na liście zostaną wyświetlone adresy e-mail użytkowników oraz ich stan rejestracji. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Udostępnij link do rejestracji przy użyciu uczniów
Aby wysłać łącze rejestracji dla uczniów i studentów, użyj jednej z następujących metod. Pierwsza metoda dowiesz się, jak wysyłać wiadomości e-mail dla uczniów i studentów z łączem rejestracji i opcjonalną wiadomość. Druga metoda dowiesz się, jak uzyskać łącze rejestracji, które można udostępniać innym osobom dowolny sposób, który ma. 

Jeśli **ograniczać** jest włączona w środowisku laboratoryjnym, tylko użytkownicy na liście Użytkownicy mogą używać łącze do zarejestrowania do laboratorium. Ta opcja jest włączona domyślnie. 

### <a name="send-email-to-users"></a>Wyślij wiadomość e-mail do użytkowników
Usługa Azure Lab Services umożliwia nauczycielom poczty e-mail laboratorium zaproszeń do wszystkich lub wybranych uczniów bez konieczności używania innego klient poczty e-mail. Nauczyciele mogą umieść kursor na indywidualnych uczniów na liście widoczna ikona wiadomości e-mail dla każdego ucznia lub wybierz co najmniej jeden studentów i używać **wysłać zaproszenia** na pasku narzędzi. Ta funkcja wysyła wiadomość e-mail z linkiem rejestracji i komunikat (jeśli istnieje) dodany przez uczniów. Po zmianie stanu zaproszenia na **zaproszenie zostało wysłane** tak, aby nauczyciele mogą zachować informacje o uczniów, które otrzymały już link rejestracji i daty, który został wysłany.

1. Jeśli jeszcze nie jesteś na tej stronie, przełącz się do widoku **Użytkownicy**. 
2. Wybierz z listy określonych lub wszystkich użytkowników. Aby wybrać określonych użytkowników, zaznacz pola wyboru w kolumnie pierwszej listy. Aby wybrać wszystkich użytkowników, zaznacz pole wyboru przed tytuł pierwszą kolumnę (**nazwa**) lub zaznacz wszystkie pola wyboru dla wszystkich użytkowników na liście. Można wyświetlić stan **stanu zaproszenia** na tej liście.  Na poniższej ilustracji stan zaproszenie dla wszystkich studentów jest ustawiony na **zaproszenie nie zostało wysłane**. 

    ![Wybierz uczniów](../media/tutorial-setup-classroom-lab/select-students.png)
1. Wybierz **ikonę poczty e-mail (koperta)** na jeden z wierszy (lub) select **wysłać zaproszenia** na pasku narzędzi. Możesz również umieścić kursor myszy nazwę uczniów na liście, aby wyświetlić ikonę poczty e-mail. 

    ![Wyślij łącze rejestracji za pośrednictwem poczty e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na **Wyślij link rejestracji za pośrednictwem poczty e-mail** strony, wykonaj następujące kroki: 
    1. Typ **opcjonalną wiadomość** , którą chcesz wysłać dla uczniów i studentów. Wiadomości e-mail automatycznie dołącza łącze rejestracji. 
    2. Na **Wyślij link rejestracji za pośrednictwem poczty e-mail** wybierz opcję **wysyłania**. Zostanie wyświetlony stan zaproszenia zmiana **wysyłanie zaproszenia** a **zaproszenie zostało wysłane**. 
        
        ![Zaproszenia wysłane](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Uzyskaj link do rejestracji
1. Przełącz się do **użytkowników** widoku, wybierając **użytkowników** w menu po lewej stronie. 
2. Wybierz kafelek **Pobierz link rejestracji**.

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Zamknij**. 
4. Udział **łącze rejestracji** z student tak, aby student można zarejestrować klasy. 

## <a name="view-users-registered-with-the-lab"></a>Wyświetlenie użytkowników zarejestrowanych w laboratorium

Wybierz **użytkowników** w menu po lewej stronie, aby zapoznać się z listą użytkowników zarejestrowane w usłudze laboratorium. 

![Lista użytkowników zarejestrowane w usłudze laboratorium](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Ustawianie przydziałów dla poszczególnych użytkowników
Można ustawić limity przydziału dla poszczególnych użytkowników wykonując następujące kroki: 

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**.
2. Wybierz **limitu przydziału dla poszczególnych użytkowników: bez ograniczeń** na pasku narzędzi. 
3. Na **limitu przydziału dla poszczególnych użytkowników** zaznacz jedną z następujących opcji: 
    1. **Brak**. Użytkownicy mogą używać ich maszyn wirtualnych tylko w trakcie zaplanowanego czasu lub właściciel laboratorium zmieni się na maszynach wirtualnych dla nich.
    2. **Bez ograniczeń (ustawienie domyślne)**. Użytkownicy mogą używać ich maszyn wirtualnych bez ograniczeń czasowych.
    3. **Określ liczbę godzin na użytkownika**. Użytkownicy mogą używać ich maszyn wirtualnych przez liczbę godzin (wymienionymi poniżej), poza zaplanowanym terminie. Jeśli wybierzesz tę opcję, wprowadź **liczby godzin** w polu tekstowym. 

        ![Liczba godzin na użytkownika](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Wybierz pozycję **Zapisz**. 
5. Teraz widać zmienionymi wartościami na pasku narzędzi: **Limit przydziału dla poszczególnych użytkowników: &lt;liczby godzin&gt;**. 

    ![Limit przydziału dla poszczególnych użytkowników](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> [Zaplanowany czas maszyny wirtualne działania](how-to-create-schedules.md) nie uwzględniane w limicie przydziału przydzielony do użytkownika. Limit przydziału jest czas poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

### <a name="add-users-by-uploading-a-csv-file"></a>Aby dodać użytkowników, przekazywanie pliku CSV
Możesz również dodać użytkowników, przekazując plik CSV z adresów e-mail użytkowników.

1. Utwórz plik CSV z adresów e-mail użytkowników w jednej kolumnie.

    ![Limit przydziału dla poszczególnych użytkowników](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na **użytkowników** strony laboratorium, wybierz opcję **przekazywanie CSV** na pasku narzędzi.

    ![CSV przycisk Przekaż](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Wybierz plik CSV zawierający adresy e-mail użytkowników. Po wybraniu **Otwórz** po wybraniu pliku CSV, zapoznaj się z poniższymi **dodawania użytkowników** okna. Lista adresów e-mail jest wypełniony adresy e-mail z pliku CSV. 

    ![Dodaj okno Użytkownicy wypełniane przy użyciu adresów e-mail, z pliku CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Wybierz **Zapisz** w **dodawania użytkowników** okna. 
5. Upewnij się, że widzisz użytkowników na liście użytkowników. 

    ![Lista dodanych użytkowników](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Zarządzanie maszynami wirtualnymi użytkownika
Gdy uczniowie rejestru za pomocą usługi Azure Lab Services przy użyciu rejestracji link, pod warunkiem, zostanie wyświetlony przypisane maszyny wirtualne dla uczniów i studentów w **maszyn wirtualnych** kartę. 

![Maszyny wirtualne przypisane dla uczniów i studentów](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Możesz wykonać następujące zadania na ucznia maszyny Wirtualnej: 

- Jeśli maszyna wirtualna jest uruchomiona, Zatrzymaj Maszynę wirtualną. 
- Uruchom Maszynę wirtualną, jeśli maszyna wirtualna jest zatrzymana. 
- Nawiąż połączenie z maszyną wirtualną. 
- Usuń maszynę Wirtualną. 
- Wyświetl liczbę godzin, aby użytkownicy używali maszyny wirtualnej. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Aktualizowanie liczby maszyn wirtualnych w laboratorium
Aby zaktualizować liczbę maszyn wirtualnych w środowisku laboratoryjnym, wykonaj następujące kroki **maszyn wirtualnych** strony:

1. Wybierz **maszyn wirtualnych** w menu po lewej stronie. 
2. Wybierz **pojemności laboratorium: &lt;numer&gt; następującą liczbę maszyn:** na pasku narzędzi. 
3. Wprowadź **numer** maszyn wirtualnych.
4. Wybierz pozycję **Zapisz**.

    ![Maszyny wirtualne w laboratorium](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)
