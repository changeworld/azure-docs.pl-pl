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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 3be1da54b16a24ce3c4431dfe26eb778cea5c83d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694672"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Samouczek: uzyskiwanie dostępu do laboratorium w usłudze Azure Lab Services
W tym samouczku Ty, jako osoba ucząca się, nawiążesz połączenie z maszyną wirtualną w laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Użycie linku rejestracji 
> * Nawiązywanie połączenia z maszyną wirtualną

## <a name="use-the-registration-link"></a>Użycie linku rejestracji

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu URL: [https://labs.azure.com](https://labs.azure.com). Należy pamiętać, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 
2. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
3. Zaczekaj, aż maszyna wirtualna będzie gotowa, a następnie **uruchom** ją. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

1. Wybierz polecenie **Połącz** na kafelku odpowiadającym maszynie wirtualnej laboratorium, do której chcesz uzyskać dostęp. 

    ![Łączenie z maszyną wirtualną](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Wykonaj jedną z następujących czynności: 
    1. Aby uzyskać **Windows** maszyn wirtualnych, Zapisz **RDP** pliku na dysku twardym. Otwórz plik RDP, aby połączyć się z maszyną wirtualną. Użyj **nazwy użytkownika** i **hasło** uzyskiwanie educator/Profesor do logowania się na tym komputerze. 
    3. Dla **Linux** maszyn wirtualnych, można użyć **SSH** lub **RDP** (jeśli jest włączony), aby połączyć się z nimi. Aby uzyskać więcej informacji, zobacz [Włączanie połączeń usług pulpitu zdalnego dla maszyny z systemem Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku użyto linku rejestracji otrzymanego od nauczyciela w celu uzyskania dostępu do laboratorium.

Jako właściciel laboratorium możesz sprawdzać, kto zarejestrował się w Twoim laboratorium, i śledzić użycie maszyn wirtualnych. Przejdź do następnego samouczka, aby dowiedzieć się, jak śledzić użycie laboratorium:

> [!div class="nextstepaction"]
> [Śledzenie użycia laboratorium](tutorial-track-usage.md) 
