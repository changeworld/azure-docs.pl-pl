---
title: Zarządzanie szablonem laboratorium klasy w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć szablon laboratorium klasowego i zarządzać nim w Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 08fbe9565356dc1b7db952fdd265770fef600ca8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989046"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Tworzenie szablonu klasy i zarządzanie nim w Azure Lab Services
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Następnie należy opublikować szablon w celu udostępnienia dla użytkowników laboratorium wystąpień z szablonową maszyną wirtualną. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon.

W tym artykule opisano sposób tworzenia maszyny wirtualnej szablonu i zarządzania nią w laboratorium z klasą Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publikowanie szablonu podczas tworzenia laboratorium zajęć
Aby dowiedzieć się, jak opublikować szablon podczas tworzenia laboratorium zajęć, zobacz [Tworzenie laboratorium zajęć](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Ustawianie lub aktualizowanie tytułu i opisu szablonu
Wykonaj następujące kroki, aby ustawić tytuł i opis po raz pierwszy, i zaktualizuj je później. 

1. Na stronie **szablon** wprowadź nowy **tytuł** dla laboratorium.  
2. Wprowadź nowy **Opis** szablonu. Gdy przenosisz fokus z pola tekstowego, zostanie ono automatycznie zapisane. 

    ![Nazwa i opis szablonu](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aktualizowanie maszyny wirtualnej szablonu
Aby zaktualizować maszynę wirtualną szablonu, wykonaj następujące czynności.  

1. Zaczekaj na uruchomienie szablonu VM, a następnie wybierz pozycję **Połącz z szablonem** na pasku narzędzi, aby połączyć się z SZABLONową maszyną wirtualną, a następnie postępuj zgodnie z instrukcjami. Jeśli jest to maszyna z systemem Windows, zostanie wyświetlona opcja pobrania pliku RDP. 
1. Po nawiązaniu połączenia z szablonem i dokonaniu zmian nie będzie on już miał takiej samej konfiguracji jak maszyny wirtualne ostatnio opublikowane dla użytkowników. Zmiany szablonów nie zostaną odzwierciedlone na istniejących maszynach wirtualnych użytkowników do momentu ponownego opublikowania.

    ![Łączenie z maszyną wirtualną szablonu](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Zainstaluj wszelkie oprogramowanie wymagane, aby uczniowie mogli ukończyć laboratorium (na przykład program Visual Studio, Eksplorator usługi Azure Storage itp.). 
1. Zakończ połączenie (zamknij sesję pulpitu zdalnego) z maszyną wirtualną szablonu. 
1. **Zatrzymaj** maszynę wirtualną szablonu, wybierając pozycję **Zatrzymaj szablon**. 
1. Wykonaj kroki opisane w następnej sekcji, aby **opublikować** zaktualizowany szablon maszyny wirtualnej. 

## <a name="publish-the-template-vm"></a>Publikowanie maszyny wirtualnej szablonu  
Jeśli szablon nie zostanie opublikowany podczas tworzenia laboratorium, można opublikować go później. Przed opublikowaniem warto nawiązać połączenie z szablonową maszyną wirtualną i zaktualizować je za pomocą dowolnego oprogramowania. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w tym procesie to liczba maszyn wirtualnych, które zostały określone podczas ich pierwszego publikowania lub co zostało określone na stronie Pula maszyn wirtualnych. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. 

1. Na stronie **szablon** wybierz pozycję **Publikuj** na pasku narzędzi. 
1. W polu **Opublikuj komunikat szablonu** Przejrzyj komunikat, a następnie wybierz pozycję **Publikuj**. Ten proces może zająć trochę czasu w zależności od liczby tworzonych maszyn wirtualnych.

    ![Przycisk Opublikuj](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Po opublikowaniu szablonu nie można cofnąć publikowania. Szablon można ponownie opublikować. 
1. Stan procesu publikowania można zobaczyć na stronie szablon. Poczekaj na zmianę stanu szablonu na **opublikowany**. 

    ![Stan publikowania](../media/how-to-create-manage-template/publish-status.png)
1. Przejdź do strony **Maszyny wirtualne** i sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Poczekaj, aż maszyny wirtualne zostaną utworzone. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Możesz je uruchomić na tej stronie lub pozwolić uczniom na uruchamianie maszyn wirtualnych. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
