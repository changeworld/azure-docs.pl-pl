---
title: Zarządzanie klasy laboratoriów w usług Azure laboratorium | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium klas, wyświetlić wszystkie klasy labs, plk rejestracji połączyć z użytkownikiem laboratorium lub usunąć laboratorium.
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
ms.openlocfilehash: f8cf5a46e1d1e3242fd46900b56f7d8cf15d3fc5
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082527"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Zarządzanie klasy laboratoriów w usług Azure laboratorium 
W tym artykule opisano, jak utworzyć i skonfigurować laboratorium klas, wyświetlić wszystkie klasy labs lub usunąć laboratorium klasy.

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium klas w ramach konta laboratorium, musi być członkiem **twórcy laboratorium** roli w ramach konta laboratorium. Właściciela laboratorium można dodać użytkownika do roli twórcy laboratorium za pomocą kroków w następującym artykule: [dodać użytkownika do roli twórcy laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com).
2. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** laboratorium na potrzeby zajęć. 
    2. Wybierz **rozmiar** maszyny wirtualnej, której zamierzasz używać podczas zajęć.
    3. Wybierz **obraz**, za pomocą którego ma zostać utworzona maszyna wirtualna.
    4. Określ **domyślnych poświadczeń** na potrzeby rejestrowania maszyn wirtualnych w laboratorium.
    7. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. Zostanie wyświetlony **pulpitu nawigacyjnego** dla laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium klas](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurowanie zasad użycia

1. Wybierz pozycję **Zasady użytkowania**. 
2. W ustawieniach **Zasady użytkowania** wprowadź **liczbę użytkowników**, którzy mogą korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Zasady użytkowania](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurowanie szablonu
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Opublikuj szablon, aby udostępnić użytkownikom laboratorium wystąpienia szablonu maszyny Wirtualnej.  

### <a name="set-template-title-and-description"></a>Ustaw szablon tytuł i opis
1. W sekcji **Szablon** wybierz pozycję **Edytuj** (ikona ołówka) dla szablonu. 
2. W oknie **Widok użytkownika** wprowadź **tytuł** szablonu.
3. Wprowadź **opis** szablonu.
4. Wybierz pozycję **Zapisz**.

    ![Opis laboratorium używanego podczas zajęć](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>Konfigurowanie szablonu maszyny Wirtualnej
 Nawiązać połączenia z szablonu maszyny Wirtualnej i instalowanie oprogramowania wymagane na nim przed udostępnieniem jej studentów użytkownika. 

1. Zaczekaj, aż szablonu maszyny wirtualnej jest gotowy. Po wykonaniu tych czynności **Start** przycisku powinno być włączone. Aby uruchomić maszynę Wirtualną, zaznacz **Start**.

    ![Uruchom szablonu maszyny Wirtualnej](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Aby nawiązać połączenie z maszyną Wirtualną, wybierz **Connect**i postępuj zgodnie z instrukcjami. 

    ![Połączenie do szablonu maszyny Wirtualnej](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Instalowanie oprogramowania, które są wymagane dla uczniów lub studentów w celu laboratorium (na przykład Visual Studio, Eksploratora usługi Storage Azure itp.). 
2. Odłącz (Zamknij sesję pulpitu zdalnego) z szablonu maszyny Wirtualnej. 
3. **Zatrzymaj** szablonu maszyny Wirtualnej, wybierając **zatrzymać**. 

    ![Zatrzymaj szablonu maszyny Wirtualnej](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


### <a name="publish-the-template"></a>Opublikuj szablon 
Podczas publikowania szablonu usługi laboratorium Azure tworzy maszyny wirtualne w laboratorium przy użyciu szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. 

1. Wybierz **publikowania** w **szablonu** sekcji. 

    ![Opublikuj szablon maszyny Wirtualnej](../media/tutorial-setup-classroom-lab/public-access.png)
1. W **publikowania** wybierz **opublikowano** opcji. 
2. Teraz, wybierz **publikowania** przycisku. Ten proces może zająć pewien czas w zależności od liczby maszyn wirtualnych są tworzone, która jest taka sama jak liczba użytkowników, którzy mogą w laboratorium.
    
    > [!IMPORTANT]
    > Po opublikowaniu szablonu nie można anulować jego publikacji. 
4. Przełącz się do **maszyn wirtualnych** strony i upewnij się, że widoczny maszyn wirtualnych, które znajdują się w **anulowaniem przypisania** stanu. Te maszyny wirtualne nie zostały jeszcze przypisane do studenta. 

    ![Maszyny wirtualne](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Poczekaj, aż do utworzenia maszyn wirtualnych. Należy je w **zatrzymane** stanu. Można uruchomić uczniów maszyny Wirtualnej, połączenie z maszyną Wirtualną, Zatrzymaj maszynę Wirtualną i Usuń maszynę Wirtualną na tej stronie. Można je uruchomić w tej strony lub pozwolić Twojej studentów start maszyn wirtualnych. 

    ![Maszyny wirtualne w stanie zatrzymania](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>Wysyłanie linków rejestracyjnych do uczniów

1. Przełącz się do **pulpitu nawigacyjnego** widoku. 
2. Wybierz kafelek **Rejestracja użytkownika**.

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na **rejestracja użytkownika** okno dialogowe, wybierz opcję **Zamknij**. 

## <a name="view-all-classroom-labs"></a>Wyświetl wszystkie laboratoria klas
1. Przejdź do [portalu Azure usługi laboratorium](https://labs.azure.com).
2. Upewnij się, że widoczne wszystkie laboratoriów w ramach laboratorium wybranego konta. 

    ![Wszystkie labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać konto różnych laboratorium. Zostanie wyświetlony laboratoriów w ramach laboratorium wybranego konta. 

## <a name="delete-a-classroom-lab"></a>Usuń laboratorium klas
1. Na kafelku dla laboratorium wybierz wielokropek (...) w prawym górnym rogu. 

    ![Wybieranie laboratorium](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Wybierz pozycję **Usuń**. 

    ![Usuwanie przycisku](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na **laboratorium Delete** okno dialogowe, wybierz opcję **usunąć**. 

    ![Okno dialogowe Usuwanie](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Zarządzenie maszynami wirtualnymi dla użytkowników domowych
Po rejestru studentów z usługami laboratorium Azure przy użyciu rejestracji łącza, pod warunkiem, zobacz przypisane do studentów na maszynach wirtualnych **maszyn wirtualnych** kartę. 

![Maszyny wirtualne przypisane do uczniów lub studentów](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Umożliwia wykonywanie następujących zadań na uczniów maszyny Wirtualnej: 

- Jeśli maszyna wirtualna jest uruchomiona, Zatrzymaj Maszynę wirtualną. 
- Uruchom Maszynę wirtualną, po zatrzymaniu maszyny Wirtualnej. 
- Nawiąż połączenie z maszyną wirtualną. 
- Usuń maszynę Wirtualną. 

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md)
- [Konfigurowanie laboratorium](../tutorial-create-custom-lab.md)
