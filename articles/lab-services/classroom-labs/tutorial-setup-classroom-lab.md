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
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: fe41728b6f08ba767dbcb40d0595b9f7cdc79615
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420203"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Samouczek: konfigurowanie laboratorium na potrzeby zajęć 
W tym samouczku skonfigurujesz laboratorium na potrzeby zajęć z maszynami wirtualnymi używanymi przez uczniów podczas zajęć.  

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie laboratorium na potrzeby zajęć
> * Konfigurowanie laboratorium na potrzeby zajęć
> * Wysyłanie linków rejestracyjnych do uczniów

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, musisz być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Właściciel laboratorium może dodać innych użytkowników do roli Twórca laboratorium, wykonując kroki opisane w następującym artykule: [Dodawanie użytkownika do roli twórcy laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** laboratorium na potrzeby zajęć. 
    2. Wybierz **rozmiar** maszyny wirtualnej, której zamierzasz używać podczas zajęć.
    3. Wybierz **obraz**, za pomocą którego ma zostać utworzona maszyna wirtualna.
    4. Określ **domyślne poświadczenia** logowania do maszyn wirtualnych w laboratorium. 
    7. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Po utworzeniu laboratorium wybierz pozycję **Przejdź do mojego laboratorium**. 

    ![Przechodzenie do mojego laboratorium](../media/tutorial-setup-classroom-lab/go-to-my-lab.png)
1. Zostanie wyświetlony **pulpit nawigacyjny** laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurowanie zasad użycia

1. Wybierz pozycję **Zasady użytkowania**. 
2. W ustawieniach **Zasady użytkowania** wprowadź **liczbę użytkowników**, którzy mogą korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Zasady użytkowania](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>Konfigurowanie szablonu 
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Opublikuj szablon, aby wystąpienia maszyny wirtualnej szablonu były dostępne dla użytkowników laboratorium. 

### <a name="set-title-and-description"></a>Ustawianie tytułu i opisu
1. W sekcji **Szablon** wybierz pozycję **Edytuj** (ikona ołówka) dla szablonu. 
2. W oknie **Widok użytkownika** wprowadź **tytuł** szablonu.
3. Wprowadź **opis** szablonu.
4. Wybierz pozycję **Zapisz**.

    ![Opis laboratorium używanego podczas zajęć](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>Konfigurowanie maszyny wirtualnej szablonu
 Nawiąż połączenie z maszyną wirtualną szablonu i zainstaluj na niej wszelkie wymagane oprogramowanie przed udostępnieniem jej swoim uczniom. 

1. Zaczekaj, aż maszyna wirtualna szablonu będzie gotowa. Gdy będzie gotowa, przycisk **Uruchom** powinien być włączony. Aby uruchomić maszynę wirtualną, wybierz przycisk **Uruchom**.

    ![Uruchamianie maszyny wirtualnej szablonu](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Aby nawiązać połączenie z maszyną wirtualną, wybierz pozycję **Połącz** i postępuj zgodnie z instrukcjami. 

    ![Łączenie z maszyną wirtualną szablonu](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Zainstaluj wszelkie oprogramowanie wymagane, aby uczniowie mogli ukończyć laboratorium (na przykład program Visual Studio, Eksplorator usługi Azure Storage itp.). 
2. Zakończ połączenie (zamknij sesję pulpitu zdalnego) z maszyną wirtualną szablonu. 
3. **Zatrzymaj** maszynę wirtualną szablonu, wybierając pozycję **Zatrzymaj**. 

    ![Zatrzymywanie maszyny wirtualnej szablonu](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>Publikowanie szablonu 
Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. 

1. Wybierz pozycję **Publikuj** w sekcji **Szablon**. 

    ![Publikowanie maszyny wirtualnej szablonu](../media/tutorial-setup-classroom-lab/public-access.png)
1. W oknie **Publikowanie** wybierz opcję **Opublikowany**. 
2. Teraz wybierz przycisk **Publikuj**. Ten proces może zająć trochę czasu w zależności od liczby tworzonych maszyn wirtualnych. Jest ona taka sama, jak dozwolona liczba użytkowników w laboratorium.
    
    > [!IMPORTANT]
    > Po opublikowaniu szablonu nie można cofnąć publikowania. 
4. Przejdź do strony **Maszyny wirtualne** i sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. 

    ![Maszyny wirtualne](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Poczekaj, aż maszyny wirtualne zostaną utworzone. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Wysyłanie linków rejestracyjnych do uczniów

1. Przejdź do widoku **Pulpit nawigacyjny**. 
2. Wybierz kafelek **Rejestracja użytkownika**.

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Zamknij**. 


## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono i skonfigurowano laboratorium na potrzeby zajęć. Aby dowiedzieć się, jak uczeń może uzyskać dostęp do maszyny wirtualnej w laboratorium przy użyciu linku rejestracyjnego, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z maszyną wirtualną w laboratorium używanym podczas zajęć](tutorial-connect-virtual-machine-classroom-lab.md)

