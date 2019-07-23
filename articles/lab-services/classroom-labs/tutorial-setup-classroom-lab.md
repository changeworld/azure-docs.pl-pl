---
title: Konfigurowanie laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services | Microsoft Docs
description: W tym samouczku skonfigurujesz laboratorium do użycia podczas zajęć.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 0c50a321cbeb0d07a5039038ff796df00463ac8a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385668"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Samouczek: Konfigurowanie laboratorium na potrzeby zajęć 
W tym samouczku skonfigurujesz laboratorium na potrzeby zajęć z maszynami wirtualnymi używanymi przez uczniów podczas zajęć.  

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie laboratorium na potrzeby zajęć
> * Dodawanie użytkowników do laboratorium
> * Wysyłanie linków rejestracyjnych do uczniów

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium zajęć na koncie laboratorium, musisz być członkiem jednej z tych ról na koncie laboratorium: Właściciel, Twórca laboratorium lub Współautor. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do roli właściciela.

Właściciel laboratorium może dodawać innych użytkowników do roli **Twórca laboratorium**. Właściciel laboratorium dodaje na przykład nauczycieli akademickich do roli twórcy laboratorium. Następnie nauczyciele akademiccy tworzą laboratoria za pomocą maszyn wirtualnych dla swoich zajęć. Studenci rejestrują się w laboratoriach przy użyciu linku rejestracji otrzymanego od nauczycieli akademickich. Po zarejestrowaniu się studenci mogą używać maszyn wirtualnych w laboratoriach do wykonywania zadań na zajęciach lub pracy domowej. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników do roli Twórca laboratorium, zobacz [Dodawanie użytkownika do roli Twórca laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Należy pamiętać, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    2. Określ maksymalną **liczbę maszyn wirtualnych** w laboratorium. Liczbę maszyn wirtualnych można zmniejszyć lub zwiększyć po utworzeniu laboratorium lub w istniejącym laboratorium. Aby uzyskać więcej informacji, zobacz [Update number of VMs in a lab (Aktualizowanie liczby maszyn wirtualnych w laboratorium)](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stronie **Wybór specyfikacji maszyny wirtualnej** wykonaj następujące czynności:
    1. Wybierz **rozmiar** maszyn wirtualnych tworzonych w laboratorium. Dozwolone są obecnie, **małe**, **średnie**, **średnie (Wirtualizacja)** , **duże**i **GPU** .
    3. Wybierz **obraz maszyny wirtualnej**, którego chcesz używać do tworzenia maszyn wirtualnych w laboratorium. W przypadku wybrania obrazu systemu Linux zostanie wyświetlona opcja włączenia dla niego połączenia pulpitu zdalnego. Aby uzyskać szczegółowe informacje, zobacz [Włączanie usługi Podłączanie pulpitu zdalnego dla systemu Linux](how-to-enable-remote-desktop-linux.md).
    4. Wybierz opcję **Dalej**.

        ![Określanie specyfikacji maszyny wirtualnej](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stronie **Ustawianie poświadczeń** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. 
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wybierz pozycję **Utwórz**. 

        ![Ustaw poświadczenia](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stronie **Konfigurowanie szablonu** zostanie wyświetlony stan procesu tworzenia laboratorium. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. 

    ![Konfigurowanie szablonu](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po zakończeniu konfiguracji szablonu zostanie wyświetlona następująca strona: 

    ![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Na stronie **Konfigurowanie szablonu** wykonaj następujące kroki: Te kroki samouczka są **opcjonalne**.
    2. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli jest to maszyna wirtualna z szablonem systemu Linux, wybierz, czy chcesz nawiązać połączenie przy użyciu protokołu SSH, czy RDP (Jeśli włączono protokół RDP).
    1. Wybierz pozycję **zresetuj hasło** , aby zresetować hasło dla maszyny wirtualnej. 
    1. Zainstaluj i skonfiguruj oprogramowania na maszynie wirtualnej szablonu. 
    1. **Zatrzymaj** maszynę wirtualną.  
    1. Wprowadź **opis** szablonu.
9. Wybierz przycisk **Dalej** na stronie szablonu. 
10. Na stronie **Publikowanie szablonu** wykonaj następujące czynności. 
    1. Aby opublikować szablon natychmiast, wybierz pozycję **Publikuj**.  

        > [!WARNING]
        > Nie można cofnąć publikowania szablonu. 
    2. Aby opublikować później, wybierz pozycję **Zachowaj na później**. Możesz opublikować maszynę wirtualną szablonu po zakończeniu pracy kreatora. Aby uzyskać szczegółowe informacje na temat konfigurowania i publikowania po zakończeniu pracy kreatora, zobacz sekcję [Publikowanie szablonu](how-to-create-manage-template.md#publish-the-template-vm) w artykule [jak zarządzać](how-to-manage-classroom-labs.md) z pomocą Labs.

        ![Publikowanie szablonu](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zostanie wyświetlony **postęp publikowania** szablonu. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po pomyślnym opublikowaniu szablonu zostanie wyświetlona następująca strona. Wybierz pozycję **Done** (Gotowe).

    ![Publikowanie szablonu — powodzenie](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zostanie wyświetlony **pulpit nawigacyjny** laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Przejdź na stronę **Maszyny wirtualne**, wybierając pozycję Maszyny wirtualne w menu po lewej stronie lub wybierając kafelek Maszyny wirtualne. Sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**. Domyślnie włączona jest opcja **Ogranicz dostęp**. Gdy to ustawienie jest włączone, a użytkownik nie znajduje się na liście użytkowników, nie może zarejestrować się w laboratorium nawet przy użyciu linku rejestracji. Tylko użytkownicy na liście mogą zarejestrować się w laboratorium przy użyciu przesłanego linku rejestracji. Podczas tej procedury dodasz użytkowników do listy. Możesz też wyłączyć opcję **Ogranicz dostęp**, co umożliwi użytkownikom zarejestrowanie się w laboratorium, pod warunkiem, że mają link rejestracji. 
2. Na pasku narzędzi wybierz pozycję **Dodaj użytkowników**. 

    ![Przycisk Dodaj użytkowników](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stronie **Dodawanie użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu, rozdzielając je średnikami. 

    ![Dodawanie adresów e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz pozycję **Zapisz**. Na liście zostaną wyświetlone adresy e-mail użytkowników oraz ich stan rejestracji. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/users-list-new.png)

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

## <a name="set-a-schedule-for-the-lab"></a>Ustaw harmonogram dla laboratorium
Jeśli skonfigurowano ustawienie limitu przydziału na **0 godzin (tylko harmonogram)** , należy ustawić harmonogram dla laboratorium. W tym samouczku ustawisz harmonogram jako cykliczny Harmonogram tygodniowy.

1. Przejdź do strony **harmonogramy** , a następnie wybierz pozycję **Dodaj harmonogram** na pasku narzędzi. 

    ![Przycisk dodawania harmonogramu na stronie harmonogramów](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stronie **Dodawanie harmonogramu** przejdź na pierwszy **tydzień** . 
3. W polu **dni harmonogramu (wymagane)** wybierz dni, w których ma obowiązywać harmonogram. W poniższym przykładzie wybrano poniedziałek-piątek. 
4. W polu **od** wprowadź **datę rozpoczęcia harmonogramu** lub wybierz datę, wybierając przycisk **Kalendarz** . To pole jest wymagane. 
5. W polu **Data zakończenia harmonogramu**wprowadź lub wybierz datę końcową, w której mają zostać zamknięte maszyny wirtualne. 
6. W polu **czas rozpoczęcia**wybierz godzinę, o której mają zostać uruchomione maszyny wirtualne. Czas rozpoczęcia jest wymagany, jeśli czas zatrzymania nie jest ustawiony. Wybierz pozycję **Usuń zdarzenie uruchomienia** , jeśli chcesz określić tylko czas zatrzymania. Jeśli **czas rozpoczęcia** jest wyłączony, wybierz pozycję **Dodaj zdarzenie uruchomienia** obok listy rozwijanej, aby ją włączyć. 
7. W polu **czas zatrzymania**wybierz godzinę, o której maszyny wirtualne mają być zamykane. Czas zatrzymania jest wymagany, jeśli nie ustawiono czasu rozpoczęcia. Wybierz pozycję **Usuń zdarzenie zatrzymania** , jeśli chcesz określić tylko godzinę rozpoczęcia. Jeśli **czas zatrzymania** jest wyłączony, zaznacz pole wyboru **Dodaj zdarzenie zatrzymania** obok listy rozwijanej, aby je włączyć.
8. Dla **strefy czasowej (wymagane)** wybierz strefę czasową dla określonych godzin rozpoczęcia i zakończenia.  
9. W polu **uwagi**wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
10. Wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Wyślij wiadomość e-mail z linkiem rejestracji

1. Jeśli jeszcze nie jesteś na tej stronie, przełącz się do widoku **Użytkownicy**. 
2. Wybierz pozycję określone lub wszyscy użytkownicy na liście. Aby wybrać określonych użytkowników, zaznacz pola wyboru w pierwszej kolumnie listy. Aby zaznaczyć opcję Wszyscy użytkownicy, zaznacz pole wyboru na początku tytułu pierwszej kolumny (**Nazwa**) lub zaznacz wszystkie pola wyboru dla wszystkich użytkowników na liście. Stan **zaproszenia** można zobaczyć na tej liście.  Na poniższej ilustracji stan zaproszenia dla wszystkich uczniów jest ustawiony na **zaproszenie nie zostało wysłane**. 

    ![Wybierz uczniów](../media/tutorial-setup-classroom-lab/select-students.png)
1. Wybierz **ikonę wiadomości e-mail (koperta)** w jednym z wierszy (lub) wybierz pozycję **Wyślij zaproszenie** na pasku narzędzi. Możesz również umieścić wskaźnik myszy nad nazwą ucznia na liście, aby zobaczyć ikonę wiadomości e-mail. 

    ![Wyślij link rejestracji za pośrednictwem poczty e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na stronie **wysyłanie linku do rejestracji za pośrednictwem poczty e-mail** wykonaj następujące czynności: 
    1. Wpisz **opcjonalny komunikat** , który chcesz wysłać do uczniów. Wiadomość e-mail automatycznie zawiera link rejestracji. 
    2. Na stronie **wysyłanie linku do rejestracji za pośrednictwem poczty e-mail** wybierz pozycję **Wyślij**. Zobaczysz stan zaproszenia, które zmienia się w celu **wysłania zaproszenia** , a następnie do **wysłania zaproszenia**. 
        
        ![Wysłane zaproszenia](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku utworzono i skonfigurowano laboratorium na potrzeby zajęć. Aby dowiedzieć się, jak uczeń może uzyskać dostęp do maszyny wirtualnej w laboratorium przy użyciu linku rejestracyjnego, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z maszyną wirtualną w laboratorium używanym podczas zajęć](tutorial-connect-virtual-machine-classroom-lab.md)

