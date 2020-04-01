---
title: Dostęp do laboratorium w usłudze Azure Lab Services | Microsoft Docs
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77134035"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Samouczek: dostęp do laboratorium w usłudze Azure Lab Services
W tym samouczku Ty, jako osoba ucząca się, nawiążesz połączenie z maszyną wirtualną w laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Zarejestruj się w laboratorium
> * Uruchamianie maszyny wirtualnej
> * Łączenie z maszyną wirtualną

## <a name="register-to-the-lab"></a>Zarejestruj się w laboratorium

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu [https://labs.azure.com](https://labs.azure.com)URL: . Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 

    > [!NOTE]
    > Konto Microsoft jest wymagane do korzystania z usług Azure Lab Services. Jeśli próbujesz zalogować się do portalu za pomocą konta innego niż Microsoft, takiego jak Konta Yahoo lub Google, postępuj zgodnie z instrukcjami, aby utworzyć konto Microsoft, które będzie połączone z Twoim kontem spoza firmy Microsoft. Następnie wykonaj kroki, aby zakończyć proces rejestracji. 
1. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
1. Poczekaj, aż maszyna wirtualna będzie gotowa. Na kafelku maszyny Wirtualnej zwróć uwagę na następujące pola:
    1. W górnej części kafelka zobaczysz **nazwę laboratorium**.
    1. Po prawej stronie zobaczysz ikonę reprezentującą **system operacyjny maszyny** Wirtualnej. W tym przykładzie jest to system operacyjny Windows. 
    1. Pasek postępu na kafelku pokazuje liczbę godzin używanych w stosunku do liczby [godzin przydziału](how-to-configure-student-usage.md#set-quotas-for-users) przypisanych do ciebie. Tym razem jest to dodatkowy czas przydzielony do Ciebie oprócz zaplanowanego czasu dla laboratorium. 
    1. Ikony/przyciski u dołu kafelka można wyświetlić, aby uruchomić/zatrzymać maszynę wirtualną i połączyć się z maszyną wirtualną. 
    1. Po prawej stronie przycisków zobaczysz stan maszyny Wirtualnej. Upewnij się, że stan maszyny Wirtualnej jest **zatrzymany**. 

        ![Maszyna wirtualna w stanie zatrzymania](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej
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

## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto linku rejestracji otrzymanego od nauczyciela w celu uzyskania dostępu do laboratorium.

Jako właściciel laboratorium możesz sprawdzać, kto zarejestrował się w Twoim laboratorium, i śledzić użycie maszyn wirtualnych. Przejdź do następnego samouczka, aby dowiedzieć się, jak śledzić użycie laboratorium:

> [!div class="nextstepaction"]
> [Śledzenie użycia laboratorium](tutorial-track-usage.md) 
