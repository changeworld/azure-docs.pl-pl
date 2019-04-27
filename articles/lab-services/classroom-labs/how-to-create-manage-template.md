---
title: Zarządzaj szablonem laboratorium na potrzeby zajęć w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać nimi klasy szablonu laboratorium w usłudze Azure Lab Services.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: b287a67c470cc1697065838e52916c285a2233a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704446"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Tworzenie i zarządzanie nimi w usłudze Azure Lab Services szablonu klasy
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Następnie możesz opublikować szablonu, aby udostępnić użytkownikom laboratorium wystąpień szablonu maszyny Wirtualnej. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon.

W tym artykule opisano, jak tworzyć i zarządzać nimi szablonu maszyny wirtualnej w laboratorium na potrzeby zajęć z usługi Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publikowanie szablonu podczas tworzenia laboratorium na potrzeby zajęć
Najpierw należy skonfigurować i publikowanie szablonu podczas tworzenia laboratorium na potrzeby zajęć.

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    2. Określ maksymalną dozwoloną **liczbę użytkowników** w laboratorium. 
    6. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stronie **Wybór specyfikacji maszyny wirtualnej** wykonaj następujące czynności:
    1. Wybierz **rozmiar** maszyn wirtualnych tworzonych w laboratorium. 
    2. Wybierz **region**, w którym mają być tworzone maszyny wirtualne. 
    3. Wybierz **obraz maszyny wirtualnej**, którego chcesz używać do tworzenia maszyn wirtualnych w laboratorium. 
    4. Wybierz opcję **Dalej**.

        ![Określanie specyfikacji maszyny wirtualnej](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stronie **Ustawianie poświadczeń** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. 
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wybierz pozycję **Utwórz**. 

        ![Ustawianie poświadczeń](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stronie **Konfigurowanie szablonu** zostanie wyświetlony stan procesu tworzenia laboratorium. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. 

    ![Konfigurowanie szablonu](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po zakończeniu konfiguracji szablonu zostanie wyświetlona następująca strona: 

    ![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Poniższe kroki są opcjonalne w tym samouczku: 
    1. Uruchom maszynę wirtualną szablonu, wybierając pozycję **Uruchom**.
    2. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. 
    3. Zainstaluj i skonfiguruj oprogramowania na maszynie wirtualnej szablonu. 
    4. **Zatrzymaj** maszynę wirtualną.  
    5. Wprowadź **opis** szablonu.

        ![Przycisk Dalej na stronie Konfigurowanie szablonu](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Wybierz przycisk **Dalej** na stronie szablonu. 
10. Na stronie **Publikowanie szablonu** wykonaj następujące czynności. 
    1. Aby natychmiast opublikować szablon, zaznacz pole wyboru *Rozumiem, że po opublikowaniu szablonu nie można go modyfikować. Ten proces można wykonać tylko raz i może on potrwać do godziny* i wybierz polecenie **Publikuj**.  

        > [!WARNING]
        > Nie można cofnąć publikowania szablonu. 
    2. Aby opublikować później, wybierz pozycję **Zachowaj na później**. Maszynę wirtualną szablonu można opublikować po zakończeniu działania kreatora. Szczegółowe informacje na temat konfigurowania i publikowania, gdy kreator zakończy pracę, można znaleźć szczegółowe informacje na temat konfigurowania i publikowania, gdy kreator zakończy pracę, zobacz publikowania w temacie szablon [sposób zarządzania laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md) artykułu.

        ![Publikowanie szablonu](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zostanie wyświetlony **postęp publikowania** szablonu. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po pomyślnym opublikowaniu szablonu zostanie wyświetlona następująca strona. Wybierz pozycję **Done** (Gotowe).

    ![Publikowanie szablonu — powodzenie](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zostanie wyświetlony **pulpit nawigacyjny** laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>Ustawianie lub zaktualizować szablon tytuł i opis
Wykonaj następujące kroki, aby ustawić tytuł i opis po raz pierwszy i zaktualizować je później. 

1. W **szablonu** sekcji, przesuń wskaźnik myszy **nazwa** szablonu lub **opis** szablonu, a następnie wybierz ją. 
2. Wprowadź **nową nazwę** lub **nowy opis** dla szablonu, a następnie naciśnij klawisz **ENTER**.

    ![Nazwę i opis szablonu](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>Konfigurowanie lub zaktualizować szablon maszyny Wirtualnej
 Nawiąż połączenie z maszyną wirtualną szablonu i zainstaluj na niej wszelkie wymagane oprogramowanie przed udostępnieniem jej swoim uczniom. Wykonaj następujące kroki, aby skonfigurować szablon maszyny Wirtualnej po raz pierwszy lub zaktualizować maszyny Wirtualnej. 

1. Zaczekaj, aż maszyna wirtualna szablonu będzie gotowa. Gdy będzie gotowa, przycisk **Uruchom** powinien być włączony. Aby uruchomić maszynę wirtualną, wybierz przycisk **Uruchom**.

    ![Uruchamianie maszyny wirtualnej szablonu](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Przejrzyj ostrzeżenie o, a następnie wybierz pozycję **Start**. 

    ![Uruchom szablon — ostrzeżenie](../media/how-to-create-manage-template/start-template-warning.png)
2. Wyświetlany jest stan w laboratorium, Kafelek w **szablonu** sekcji.

    ![Uruchom szablon — stan](../media/how-to-create-manage-template/template-start-status.png)
1. Po jej ponownym uruchomieniu, aby nawiązać połączenie z maszyną Wirtualną, wybrać **Connect**i postępuj zgodnie z instrukcjami. 

    ![Łączenie się z lub Zatrzymaj szablonu maszyny Wirtualnej](../media/how-to-create-manage-template/connect-stop-vm.png)
1. Zainstaluj wszelkie oprogramowanie wymagane, aby uczniowie mogli ukończyć laboratorium (na przykład program Visual Studio, Eksplorator usługi Azure Storage itp.). 
2. Zakończ połączenie (zamknij sesję pulpitu zdalnego) z maszyną wirtualną szablonu. 
3. **Zatrzymaj** maszynę wirtualną szablonu, wybierając pozycję **Zatrzymaj**. 

## <a name="publish-the-template-vm"></a>Publikowanie maszyny wirtualnej szablonu  
Jeśli nie Publikuj szablonu podczas tworzenia laboratorium, możesz opublikować go później. Przed opublikowaniem, możesz nawiązać połączenie z szablonu maszyny Wirtualnej i zaktualizować go przy użyciu dowolnego oprogramowania. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. 

1. Wybierz pozycję **Publikuj** w sekcji **Szablon**. 

    ![Publikowanie maszyny wirtualnej szablonu](../media/tutorial-setup-classroom-lab/public-access.png)
1. Na **opublikować szablon** okno komunikatu, zapoznaj się z komunikatem i wybierz **Publikuj**. Ten proces może zająć trochę czasu w zależności od tego, ile maszyn wirtualnych są tworzone.
    
    > [!IMPORTANT]
    > Po opublikowaniu szablonu nie można cofnąć publikowania. Mimo że można ponownie opublikowanie szablonu. 
4. Poczekaj, aż stan szablonu, aby zmienić **opublikowano**. 

    ![Stan publikowania](../media/how-to-create-manage-template/publish-status.png)
1. Przejdź do strony **Maszyny wirtualne** i sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Poczekaj, aż maszyny wirtualne zostaną utworzone. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Można uruchomić je na tej stronie lub pozwolić swoim uczniom, uruchom maszyny wirtualne. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>Ponownie opublikowanie szablonu 
Po opublikowaniu szablonu możesz wciąż się łączyć z szablonu maszyny Wirtualnej, zaktualizuj go i ponownie ją opublikować. Po ponownym opublikowaniu szablonu maszyny Wirtualnej wszystkich użytkowników, Uzyskaj odłączenia maszyn wirtualnych i ich są ponownie tworzone na podstawie zaktualizowanego szablonu. 

1. Na stronie pulpitu nawigacyjnego w środowisku laboratoryjnym wybierz **ponownie opublikować** w sekcji szablon. 

    ![Ponownie opublikować przycisku](../media/how-to-create-manage-template/republish-button.png)
2. Na **ponownie opublikowanie szablonu** okno komunikatu, Przejrzyj tekst, a następnie wybierz **ponownie opublikować** aby kontynuować. W przeciwnym razie wybierz **anulować**. 

    ![Ponowne opublikowanie wiadomości w szablonie](../media/how-to-create-manage-template/republish-template-message.png)
3. Wyświetlany jest stan ponownie opublikować na kafelku w **szablonu** sekcji.

    ![Stan ponownego publikowania](../media/how-to-create-manage-template/republish-status-publishing.png)
4. Po opublikowaniu tego szablonu, stan jest ustawiony na **opublikowano**. 

    ![Pomyślnie ponownie opublikować](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)
