---
title: Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services | Microsoft Docs
description: W tym samouczku maszyny wirtualne są dostępne w laboratorium skonfigurowanym przez nauczyciela.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: db1e60ccd7e05cb8b6923d6b2ec008cdfb76eaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501932"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services
W tym artykule opisano sposób rejestrowania się w laboratorium w klasie, wyświetlania wszystkich laboratoriów, do których można uzyskać dostęp, uruchamiania/zatrzymywania maszyny wirtualnej w laboratorium i łączenia się z maszyną wirtualną. 

## <a name="register-to-the-lab"></a>Zarejestruj się w laboratorium

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu [https://labs.azure.com](https://labs.azure.com)URL: . Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 

    > [!NOTE]
    > Konto Microsoft jest wymagane do korzystania z usług Azure Lab Services. Jeśli próbujesz zalogować się do portalu za pomocą konta innego niż Microsoft, takiego jak Konta Yahoo lub Google, postępuj zgodnie z instrukcjami, aby utworzyć konto Microsoft, które będzie połączone z Twoim kontem spoza firmy Microsoft. Następnie wykonaj kroki, aby zakończyć proces rejestracji. 
1. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
1. Poczekaj, aż maszyna wirtualna będzie gotowa. Na kafelku maszyny Wirtualnej zwróć uwagę na następujące pola:
    1. W górnej części kafelka zobaczysz **nazwę laboratorium**.
    1. Po prawej stronie zobaczysz ikonę reprezentującą **system operacyjny maszyny** Wirtualnej. W tym przykładzie jest to system operacyjny Windows. 
    1. Ikony/przyciski u dołu kafelka można wyświetlić, aby uruchomić/zatrzymać maszynę wirtualną i połączyć się z maszyną wirtualną. 
    1. Po prawej stronie przycisków zobaczysz stan maszyny Wirtualnej. Upewnij się, że stan maszyny Wirtualnej jest **zatrzymany**.

        ![Maszyna wirtualna w stanie zatrzymania](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Uruchamianie lub zatrzymywania maszyny wirtualnej
1. **Rozpocznij** maszynę wirtualną, wybierając pierwszy przycisk, jak pokazano na poniższej ilustracji. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Upewnij się, że stan maszyny Wirtualnej jest ustawiony na **Uruchomiony**. 

    ![Maszyna wirtualna w stanie uruchomionym](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Należy zauważyć, że ikona pierwszego przycisku zmieniono do reprezentowania operacji **zatrzymania.** Można wybrać ten przycisk, aby zatrzymać maszynę wirtualną. 

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz drugi przycisk, jak pokazano na poniższej ilustracji, aby **połączyć się z** maszyną wirtualną laboratorium. 

    ![Łączenie z maszyną wirtualną](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Wykonaj jedną z następujących czynności: 
    1. W przypadku maszyn wirtualnych **z systemem Windows** zapisz plik **RDP** na dysku twardym. Otwórz plik RDP, aby połączyć się z maszyną wirtualną. Użyj **nazwy użytkownika** i **hasła,** które otrzymujesz od nauczyciela/profesora, aby zalogować się do urządzenia. 
    3. W przypadku maszyn wirtualnych **systemu Linux** można połączyć się z nimi za pomocą **protokołu SSH** lub **RDP** (jeśli jest włączony). Aby uzyskać więcej informacji, zobacz [Włączanie połączenia pulpitu zdalnego dla komputerów z systemem Linux](how-to-enable-remote-desktop-linux.md). 
    1. Jeśli używasz **komputera Mac** do łączenia się z maszyną wirtualną laboratorium, postępuj zgodnie z instrukcjami w następnej sekcji. 

## <a name="progress-bar"></a>Pasek postępu 
Pasek postępu na kafelku pokazuje liczbę godzin używanych w stosunku do liczby [godzin przydziału](how-to-configure-student-usage.md#set-quotas-for-users) przypisanych do ciebie. Tym razem jest to dodatkowy czas przydzielony do Ciebie oprócz zaplanowanego czasu dla laboratorium. Kolor paska postępu i tekstu pod paskiem postępu różni się w zależności od następujących scenariuszy:

- Jeśli klasa jest w toku (w harmonogramie klasy), pasek postępu jest wyszarzony do reprezentowania godzin przydziału nie są używane. 

    ![Pasek postępu w kolorze szarym](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Jeśli przydział nie jest przypisany (zero godzin), tekst **Dostępny tylko podczas zajęć** jest wyświetlany zamiast paska postępu. 
    
    ![Stan, gdy nie ustawiono przydziału](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Jeśli zabrakło **limitu,** kolor paska postępu jest **czerwony**. 

    ![Pasek postępu w kolorze czerwonym](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Kolor paska postępu jest **niebieski,** gdy znajduje się poza zaplanowanym czasem laboratorium i użyto części czasu przydziału. 

    ![Pasek postępu w kolorze niebieskim](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Wyświetlanie wszystkich laboratoriów
Po zarejestrowaniu się w laboratoriach możesz wyświetlić wszystkie laboratoria na potrzeby zajęć, wykonując następujące czynności: 

1. Przejdź [https://labs.azure.com](https://labs.azure.com)do pliku . Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Zaloguj się w usłudze przy użyciu konta użytkownika, którego użyto do zarejestrowania się do laboratorium. 
3. Upewnij się, że są wyświetlone wszystkie laboratoria, do których masz dostęp. 

    ![Wyświetlanie wszystkich laboratoriów](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
 