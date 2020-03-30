---
title: Zarządzanie szablonem laboratorium w klasie w usłudze Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć szablon laboratorium w klasie i zarządzać nim w usługach Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539034"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Tworzenie szablonu klasy i zarządzanie nim w usługach Azure Lab Services
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Następnie należy opublikować szablon, aby wystąpienia szablonu maszyny Wirtualnej dostępne dla użytkowników laboratorium. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon.

W tym artykule opisano sposób tworzenia i zarządzania maszyną wirtualną szablonu w laboratorium klasy usługi Azure Lab Services. 

## <a name="set-or-update-template-title-and-description"></a>Ustawianie lub aktualizowanie tytułu i opisu szablonu
Skorzystaj z poniższych kroków, aby ustawić tytuł i opis po raz pierwszy, a następnie zaktualizować je później. 

1. Na stronie **Szablon** wprowadź nowy **tytuł** laboratorium.  
2. Wprowadź nowy **opis** szablonu. Gdy kładziesz fokus poza pole tekstowe, zostanie ono automatycznie zapisane. 

    ![Nazwa i opis szablonu](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aktualizowanie szablonu maszyny Wirtualnej
Aby zaktualizować szablon maszyny Wirtualnej, należy wykonać następujące kroki.  

1. Poczekaj na rozpoczęcie maszyny Wirtualnej szablonu, a następnie wybierz pozycję **Połącz z szablonem** na pasku narzędzi, aby połączyć się z szablonem maszyny Wirtualnej i postępuj zgodnie z instrukcjami. Jeśli jest to komputer z systemem Windows, zostanie wyświetlna opcja pobrania pliku RDP. 
1. Po nawiązaniu połączenia z szablonem i wprowadzeniu zmian nie będzie on już miał takiej samej konfiguracji, jak maszyny wirtualne ostatnio opublikowane użytkownikom. Zmiany szablonu zostaną odzwierciedlone na istniejących maszynach wirtualnych użytkowników dopiero po ponownym opublikowaniu.

    ![Łączenie z maszyną wirtualną szablonu](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Zainstaluj wszelkie oprogramowanie wymagane, aby uczniowie mogli ukończyć laboratorium (na przykład program Visual Studio, Eksplorator usługi Azure Storage itp.). 
1. Zakończ połączenie (zamknij sesję pulpitu zdalnego) z maszyną wirtualną szablonu. 
1. **Zatrzymaj** szablon maszyny Wirtualnej, wybierając **szablon Stop**. 
1. Wykonaj kroki opisane w następnej sekcji, aby **opublikować** zaktualizowany szablon maszyny Wirtualnej. 

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
## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, uzyskaj dostęp do laboratoriów szkolnych](how-to-use-classroom-lab.md)
