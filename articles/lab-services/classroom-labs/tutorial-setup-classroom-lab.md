---
title: Konfigurowanie laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services | Microsoft Docs
description: W tym samouczku można użyć usługi Azure Lab Services, aby skonfigurować laboratorium w klasie z maszynami wirtualnymi, które są używane przez uczniów w klasie.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 166ec4db2a2891d25a1e80526f8c1bd9770f9eef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77592224"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Samouczek: konfigurowanie laboratorium na potrzeby zajęć 
W tym samouczku skonfigurujesz laboratorium na potrzeby zajęć z maszynami wirtualnymi używanymi przez uczniów podczas zajęć.  

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie laboratorium na potrzeby zajęć
> * Dodawanie użytkowników do laboratorium
> * Ustawianie harmonogramu laboratorium
> * Wyślij wiadomość e-mail z zaproszeniem do uczniów

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku skonfigurujesz laboratorium z maszynami wirtualnymi dla swojej klasy. Aby skonfigurować laboratorium w klasie na koncie laboratorium, musisz być członkiem jednej z tych ról na koncie laboratorium: Właściciel, Twórca laboratorium lub Współautor. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do roli właściciela. Możesz więc użyć konta użytkownika użytego do utworzenia konta laboratorium w celu utworzenia laboratorium w klasie. 

Oto typowy przepływ pracy podczas korzystania z usług Azure Lab Services:

1. Twórca konta laboratorium dodaje innych użytkowników do roli **Twórca laboratorium.** Na przykład twórca/administrator konta laboratorium dodaje profesorów do roli **Twórca laboratorium,** aby mogli tworzyć laboratoria dla swoich klas. 
2. Następnie profesorowie tworzą laboratoria z maszynami wirtualnymi dla swoich klas i wysyłają linki rejestracyjne do uczniów w klasie. 
3. Studenci rejestrują się w laboratoriach przy użyciu linku rejestracji otrzymanego od nauczycieli akademickich. Po zarejestrowaniu się studenci mogą używać maszyn wirtualnych w laboratoriach do wykonywania zadań na zajęciach lub pracy domowej. 

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć
W tym kroku utworzysz laboratorium dla swojej klasy na platformie Azure. 

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Należy zauważyć, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Wybierz **pozycję Nowe laboratorium**. 
    
    ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** laboratorium i wybierz pozycję **Dalej**.  

        ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Na stronie **poświadczenia maszyny wirtualnej** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. Określ **nazwę** i **hasło** użytkownika, a następnie wybierz przycisk **Dalej**.  

        ![Nowe okno laboratorium](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Na stronie **Zasady laboratorium** wybierz pozycję **Zakończ**. 

        ![Przydział dla każdego użytkownika](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Powinien zostać wyświetlony następujący ekran, który pokazuje stan tworzenia maszyny wirtualnej szablonu. Ta operacja trwa do 20 minut. 

    ![Stan tworzenia maszyny wirtualnej szablonu](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stronie **Szablon** wykonaj następujące kroki: Te kroki są **opcjonalne** dla samouczka.

    1. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli jest to maszyna wirtualna szablonu systemu Linux, możesz wybrać, czy chcesz połączyć się przy użyciu protokołu SSH lub RDP (jeśli protokół RDP jest włączony).
    3. Zainstaluj i skonfiguruj oprogramowanie wymagane dla twojej klasy na szablonie maszyny Wirtualnej. 
    4. **Zatrzymaj** szablon maszyny Wirtualnej.  

## <a name="publish-the-template-vm"></a>Publikowanie maszyny wirtualnej szablonu
W tym kroku można opublikować szablon maszyny Wirtualnej. Podczas publikowania szablonu maszyny Wirtualnej usługi Azure Lab Services tworzy maszyny wirtualne w laboratorium przy użyciu szablonu. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon.

1. Na stronie **Szablon** wybierz pozycję **Publikuj** na pasku narzędzi. 

    ![Przycisk Opublikuj szablon](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nie można cofnąć publikowania szablonu. 
2. Na stronie **Publikowanie szablonu** wprowadź liczbę maszyn wirtualnych, które chcesz utworzyć w laboratorium, a następnie wybierz pozycję **Publikuj**. 

    ![Szablon publikowania — liczba maszyn wirtualnych](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Zostanie wyświetlony **stan publikowania** szablonu na stronie. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Poczekaj, aż publikowanie zostanie zakończone, a następnie przełącz się do strony **puli maszyn wirtualnych,** wybierając **maszyny wirtualne** w menu po lewej stronie lub wybierając kafelek **maszyny wirtualne.** Sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Gdy nauczyciel włącza maszynę wirtualną dla uczniów, nie ma to wpływu na przydział dla ucznia. Przydział dla użytkownika określa liczbę godzin pracy dostępnych dla użytkownika poza zaplanowanym czasem zajęć. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Ustawianie przydziałów dla użytkowników](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Ustawianie harmonogramu laboratorium
Utwórz zaplanowane zdarzenie dla laboratorium, tak aby maszyny wirtualne w laboratorium były automatycznie uruchamiane/zatrzymywane o określonych porach. Przydział użytkownika (domyślnie: 10 godzin) określony wcześniej jest dodatkowy czas przypisany do każdego użytkownika poza tym zaplanowanym czasie. 

1. Przełącz się do strony **Harmonogramy** i wybierz pozycję **Dodaj zaplanowane zdarzenie** na pasku narzędzi. 

    ![Przycisk Dodaj harmonogram na stronie Harmonogramy](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stronie **Dodawanie zaplanowanego zdarzenia** wykonaj następujące czynności:
    1. Upewnij się, że **standard** jest wybrany **typ zdarzenia**.  
    2. Wybierz **datę rozpoczęcia** dla klasy. 
    4. Wybierz **godzinę rozpoczęcia,** w której mają być uruchamiane maszyny wirtualne.
    5. Wybierz **czas zatrzymania,** w którym maszyny wirtualne mają zostać zamknięte. 
    6. Wybierz **określoną strefę czasową** dla określonych godzin rozpoczęcia i zatrzymania. 
3. Na tej samej stronie **Dodaj zaplanowane wydarzenie** wybierz bieżący harmonogram w sekcji **Powtarzanie.**  

    ![Przycisk Dodaj harmonogram na stronie Harmonogramy](../media/how-to-create-schedules/select-current-schedule.png)
5. W oknie dialogowym **Powtarzanie** wykonaj następujące czynności:
    1. Upewnij się, że dla pola **Powtórz** jest ustawiony **co tydzień.** 
    2. Wybierz dni, w których harmonogram ma obowiązywać. W poniższym przykładzie wybrano od poniedziałku do piątku. 
    3. Wybierz **datę zakończenia** dla harmonogramu.
    8. Wybierz **pozycję Zapisz**. 

        ![Ustawianie harmonogramu powtarzania](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Teraz na stronie **Dodaj zaplanowane wydarzenie** w polu **Notatki (opcjonalnie)** wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
4. Na stronie **Dodawanie zaplanowanego wydarzenia** wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Przejdź do daty rozpoczęcia w kalendarzu, aby sprawdzić, czy harmonogram jest ustawiony.
    
    ![Zaplanuj w kalendarzu](../media/how-to-create-schedules/schedule-calendar.png)

    Aby uzyskać więcej informacji na temat tworzenia harmonogramów zajęć i zarządzania nimi, zobacz [Tworzenie harmonogramu dla laboratoriów w klasie i zarządzanie nim.](how-to-create-schedules.md)


## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**. Domyślnie włączona jest opcja **Ogranicz dostęp**. Gdy to ustawienie jest włączone, a użytkownik nie znajduje się na liście użytkowników, nie może zarejestrować się w laboratorium nawet przy użyciu linku rejestracji. Tylko użytkownicy na liście mogą zarejestrować się w laboratorium przy użyciu przesłanego linku rejestracji. Podczas tej procedury dodasz użytkowników do listy. Możesz też wyłączyć opcję **Ogranicz dostęp**, co umożliwi użytkownikom zarejestrowanie się w laboratorium, pod warunkiem, że mają link rejestracji. 
2. Wybierz **pozycję Dodaj użytkowników** na pasku narzędzi, a następnie wybierz pozycję Dodaj według adresu **e-mail**. 

    ![Przycisk Dodaj użytkowników](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stronie **Dodawanie użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu, rozdzielając je średnikami. 

    ![Dodawanie adresów e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz **pozycję Zapisz**. Na liście zostaną wyświetlone adresy e-mail użytkowników oraz ich stan rejestracji. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/users-list-new.png)

    Zobaczysz nazwy użytkowników na liście po zarejestrowaniu ich w laboratorium. 
    

## <a name="send-invitation-emails-to-users"></a>Wysyłanie e-maili z zaproszeniami do użytkowników

1. Przełącz się do widoku **Użytkownicy,** jeśli nie znajdujesz się już na stronie, i wybierz pozycję **Zaproś wszystkich** na pasku narzędzi. 

    ![Wybierz uczniów](../media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na stronie **Wyślij zaproszenie pocztą e-mail** wprowadź opcjonalną wiadomość, a następnie wybierz pozycję **Wyślij**. Wiadomość e-mail automatycznie zawiera link rejestracyjny. Możesz uzyskać ten link rejestracyjny, wybierając **... (wielokropek)** na pasku narzędzi i **łącze Rejestracja**. 

    ![Wyślij link rejestracyjny przez e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Stan **zaproszenia** jest widoczny na liście **Użytkownicy.** Stan powinien zmienić się na **Wysyłanie,** a następnie **na Wysłane w &lt;dniu&gt;**. 

    Aby uzyskać więcej informacji na temat dodawania uczniów do klasy i zarządzania ich wykorzystaniem w laboratorium, zobacz [Jak skonfigurować użycie uczniów](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono laboratorium dla swojej klasy na platformie Azure. Aby dowiedzieć się, jak uczeń może uzyskać dostęp do maszyny wirtualnej w laboratorium przy użyciu linku rejestracyjnego, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z maszyną wirtualną w laboratorium używanym podczas zajęć](tutorial-connect-virtual-machine-classroom-lab.md)

