---
title: Zarządzanie laboratoriami na potrzeby zajęć w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium na potrzeby zajęć, wyświetlać wszystkie laboratorium na potrzeby zajęć, plk, rejestracja połączyć z użytkownikiem laboratorium lub usunąć laboratorium.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 48056d6e2988dd674351aca83526032175c355b6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214397"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Zarządzanie laboratoriami na potrzeby zajęć w usłudze Azure Lab Services 
W tym artykule opisano, jak utworzyć i skonfigurować laboratorium na potrzeby zajęć, wyświetlić wszystkie laboratorium na potrzeby zajęć lub usuń laboratorium na potrzeby zajęć.

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, musisz być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium jest automatycznie dodawane do tej roli. Właściciel laboratorium można dodać innych użytkowników do roli Twórca laboratorium za pomocą procedury w następującym artykule: [dodać użytkownika do roli Twórca laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com).
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i kont Microsoft.
3. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** laboratorium na potrzeby zajęć. 
    2. Wybierz **rozmiar** maszyny wirtualnej, której zamierzasz używać podczas zajęć.
    3. Wybierz **obraz**, za pomocą którego ma zostać utworzona maszyna wirtualna.
    4. Określ **domyślnych poświadczeń** służące do zalogowania się do maszyn wirtualnych w środowisku laboratoryjnym.
    7. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. Zostanie wyświetlony **pulpit nawigacyjny** laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium na potrzeby zajęć](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurowanie zasad użycia

1. Wybierz pozycję **Zasady użytkowania**. 
2. W ustawieniach **Zasady użytkowania** wprowadź **liczbę użytkowników**, którzy mogą korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Zasady użytkowania](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurowanie szablonu
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Opublikuj szablon, aby wystąpienia maszyny wirtualnej szablonu były dostępne dla użytkowników laboratorium.  

### <a name="set-template-title-and-description"></a>Ustaw szablon tytuł i opis
1. W sekcji **Szablon** wybierz pozycję **Edytuj** (ikona ołówka) dla szablonu. 
2. W oknie **Widok użytkownika** wprowadź **tytuł** szablonu.
3. Wprowadź **opis** szablonu.
4. Wybierz pozycję **Zapisz**.

    ![Opis laboratorium używanego podczas zajęć](../media/how-to-manage-classroom-labs/lab-description.png)

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

## <a name="view-all-classroom-labs"></a>Wyświetl wszystkie laboratorium na potrzeby zajęć
1. Przejdź do [portalu usługi Azure Lab Services](https://labs.azure.com).
2. Upewnij się, czy widzisz wszystkie laboratoriów w ramach laboratorium wybranego konta. 

    ![Wszystkie warsztaty](../media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać konto różnych laboratorium. Zostanie wyświetlony laboratoriów w ramach laboratorium wybranego konta. 

## <a name="delete-a-classroom-lab"></a>Usuń laboratorium na potrzeby zajęć
1. Na kafelku dla laboratorium wybierz wielokropek (...) w prawym górnym rogu. 

    ![Wybieranie laboratorium](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Wybierz pozycję **Usuń**. 

    ![Usuwanie przycisku](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na **Delete lab** okno dialogowe, wybierz opcję **Usuń**. 

    ![Okno dialogowe Usuwanie](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Zarządzanie maszynami wirtualnymi dla uczniów
Gdy uczniowie rejestru za pomocą usługi Azure Lab Services przy użyciu rejestracji link, pod warunkiem, zostanie wyświetlony przypisane maszyny wirtualne dla uczniów i studentów w **maszyn wirtualnych** kartę. 

![Maszyny wirtualne przypisane dla uczniów i studentów](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Możesz wykonać następujące zadania na ucznia maszyny Wirtualnej: 

- Jeśli maszyna wirtualna jest uruchomiona, Zatrzymaj Maszynę wirtualną. 
- Uruchom Maszynę wirtualną, jeśli maszyna wirtualna jest zatrzymana. 
- Nawiąż połączenie z maszyną wirtualną. 
- Usuń maszynę Wirtualną. 

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md)
- [Konfigurowanie laboratorium](../tutorial-create-custom-lab.md)
