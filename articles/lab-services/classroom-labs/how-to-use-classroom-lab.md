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
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: 8d3d08c772c28ff4e3a32e736253ba69b03684f0
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012314"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services
W tym artykule opisano sposób rejestracji w laboratorium zajęć, wyświetlania wszystkich laboratoriów, do których można uzyskać dostęp, uruchamiania/zatrzymywania maszyny wirtualnej w laboratorium oraz nawiązywania połączenia z maszyną wirtualną. 

## <a name="register-to-the-lab"></a>Zarejestruj się w laboratorium

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu URL: [https://labs.azure.com](https://labs.azure.com). Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 
2. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
3. Zaczekaj, aż maszyna wirtualna będzie gotowa. Na kafelku maszyny wirtualnej Zwróć uwagę na następujące pola:
    1. W górnej części kafelka zobaczysz **nazwę laboratorium**.
    1. Po prawej stronie zostanie wyświetlona ikona reprezentująca **system operacyjny (OS)** maszyny wirtualnej. W tym przykładzie jest to system operacyjny Windows. 
    1. Zobaczysz ikonę/przyciski w dolnej części kafelka, aby uruchomić/zatrzymać maszynę wirtualną, a następnie nawiązać połączenie z maszyną wirtualną. 
    1. Na prawo od przycisków zobaczysz stan maszyny wirtualnej. Upewnij się, że stan maszyny wirtualnej jest zatrzymany.

        ![Maszyna wirtualna w stanie zatrzymania](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Uruchamianie lub zatrzymywanie maszyny wirtualnej
1. **Uruchom** maszynę wirtualną, wybierając pierwszy przycisk, jak pokazano na poniższej ilustracji. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Upewnij się, że stan maszyny wirtualnej to uruchomiono. 

    ![Maszyna wirtualna w stanie uruchomienia](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Zauważ, że ikona pierwszego przycisku została zmieniona, aby reprezentować operację **zatrzymania** . Możesz wybrać ten przycisk, aby zatrzymać maszynę wirtualną. 

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz drugi przycisk, jak pokazano na poniższej ilustracji, aby **nawiązać połączenie** z maszyną wirtualną laboratorium. 

    ![Łączenie z maszyną wirtualną](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Wykonaj jedną z następujących czynności: 
    1. W przypadku maszyn wirtualnych z **systemem Windows** Zapisz plik **RDP** na dysku twardym. Otwórz plik RDP, aby nawiązać połączenie z maszyną wirtualną. Zaloguj się na komputerze przy użyciu **nazwy użytkownika** i **hasła** uzyskanych z nauczycieli/profesor. 
    3. W przypadku maszyn wirtualnych z **systemem Linux** można użyć protokołu **SSH** lub **RDP** (jeśli jest włączony), aby połączyć się z nimi. Aby uzyskać więcej informacji, zobacz [Włączanie usługi Podłączanie pulpitu zdalnego dla komputerów z systemem Linux](how-to-enable-remote-desktop-linux.md). 
    1. Jeśli używasz **komputera Mac** do nawiązywania połączenia z maszyną wirtualną laboratorium, postępuj zgodnie z instrukcjami w następnej sekcji. 

## <a name="connect-to-a-vms-ui-using-rdp-on-a-mac"></a>Nawiązywanie połączenia z interfejsem użytkownika maszyny wirtualnej przy użyciu protokołu RDP na komputerze Mac
W tej sekcji pokazano, jak student może połączyć się z interfejsem użytkownika maszyny wirtualnej z komputera Mac przy użyciu protokołu RDP. Oto szybkie kroki, po których następuje szczegóły: 

1. Zainstaluj Pulpit zdalny Microsoft na komputerze Mac.
2. Nawiązywanie połączenia z interfejsem użytkownika maszyny wirtualnej z komputera Mac przy użyciu protokołu RDP. 

### <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalowanie Pulpit zdalny Microsoft na komputerze Mac
1. Otwórz sklep App Store na komputerze Mac, a następnie wyszukaj **pulpit zdalny Microsoft**.

    ![Pulpit zdalny Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Zainstaluj najnowszą wersję programu Pulpit zdalny Microsoft. 

### <a name="access-the-vms-ui-from-your-mac-using-rdp"></a>Dostęp do interfejsu użytkownika maszyny wirtualnej z komputera Mac przy użyciu protokołu RDP
1. Otwórz plik **RDP** pobrany na komputerze z zainstalowanym **pulpit zdalny Microsoft** . Należy rozpocząć łączenie się z maszyną wirtualną. 

    ![Łączenie z interfejsem użytkownika maszyny wirtualnej](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Wybierz pozycję **Kontynuuj** , jeśli zostanie wyświetlony następujący błąd. 

    ![Błąd certyfikatu](../media/how-to-use-classroom-lab/certificate-error.png)
1. Powinien zostać wyświetlony interfejs użytkownika maszyny wirtualnej. 

    > [!NOTE]
    > Poniższy przykład dotyczy maszyny wirtualnej z systemem CentOS Linux. 

    ![INTERFEJS UŻYTKOWNIKA MASZYNY WIRTUALNEJ](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>Pasek postępu 
Pasek postępu na kafelku pokazuje liczbę godzin użycia w odniesieniu do liczby godzin [](how-to-configure-student-usage.md#set-quotas-for-users) przypisanych do użytkownika. Ten czas jest dodatkowym czasem przydzielonym do czasu zaplanowanym dla laboratorium. Kolor paska postępu i tekstu pod paskiem postępu różnią się w zależności od następujących scenariuszy:

- Jeśli klasa jest w toku (w ramach harmonogramu klasy), pasek postępu jest wyszarzony do reprezentowania godzin przydziału nie jest używany. 

    ![Pasek postępu w kolorze szarym](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Jeśli limit przydziału nie zostanie przypisany (zero godzin), tekst **dostępny podczas klas** jest pokazywany zamiast paska postępu. 
    
    ![Stan, gdy nie ustawiono limitu przydziału](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Jeśli wykorzystano **limit przydziału**, kolor paska postępu jest **czerwony**. 

    ![Pasek postępu w kolorze czerwonym](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Kolor paska postępu jest **niebieski** , gdy jest on poza zaplanowanym terminem dla laboratorium i został użyty jakiś czas przydziału. 

    ![Pasek postępu w kolorze niebieskim](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Wyświetlanie wszystkich laboratoriów
Po zarejestrowaniu się w laboratoriach możesz wyświetlić wszystkie laboratoria na potrzeby zajęć, wykonując następujące czynności: 

1. Przejdź do strony [https://labs.azure.com](https://labs.azure.com). Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Zaloguj się w usłudze przy użyciu konta użytkownika, którego użyto do zarejestrowania się do laboratorium. 
3. Upewnij się, że są wyświetlone wszystkie laboratoria, do których masz dostęp. 

    ![Wyświetlanie wszystkich laboratoriów](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
 