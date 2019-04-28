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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: bc5c12d4bb92edaafcc9808da8c48106a6e0cbd5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104151"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services
W tym artykule opisano uzyskiwanie dostępu do laboratorium, nawiązywanie połączenia z maszyną wirtualną w laboratorium i zatrzymywanie maszyny wirtualnej. 

## <a name="register-to-a-lab"></a>Rejestracja w laboratorium
1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. 
2. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 
3. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
2. Zaczekaj, aż maszyna wirtualna będzie gotowa, a następnie **uruchom** ją. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Wyświetlanie wszystkich laboratoriów
Po zarejestrowaniu się w laboratoriach możesz wyświetlić wszystkie laboratoria na potrzeby zajęć, wykonując następujące czynności: 

1. Przejdź do strony [https://labs.azure.com](https://labs.azure.com). Należy pamiętać, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Zaloguj się w usłudze przy użyciu konta użytkownika, którego użyto do zarejestrowania się do laboratorium. 
3. Upewnij się, że są wyświetlone wszystkie laboratoria, do których masz dostęp. 

    ![Wyświetlanie wszystkich laboratoriów](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Nawiązywanie połączenia z maszyną wirtualną w laboratorium

1. Jeśli maszyna wirtualna nie została jeszcze uruchomiona, uruchom ją, wybierając polecenie **Uruchom** na kafelku. 
2. Wybierz polecenie **Połącz** na kafelku reprezentującym maszynę wirtualną laboratorium, do której chcesz uzyskać dostęp. 
3. Wykonaj jedną z następujących czynności: 
   1. Aby uzyskać **Windows** maszyn wirtualnych, Zapisz **RDP** pliku na dysku twardym. Otwórz plik RDP, aby połączyć się z maszyną wirtualną. Użyj **nazwa_użytkownika** i **hasło** uzyskiwanie educator/Profesor, aby zalogować się do maszyny. 
   3. Aby uzyskać **Linux** maszyn wirtualnych, skopiuj i Zapisz parametry połączenia SSH na **nawiązywanie połączenia z maszyną wirtualną** okno dialogowe. Użyj tych parametrów połączenia z poziomu terminalu SSH (takich jak [Putty](https://www.putty.org/)) nawiązywania połączenia z maszyną wirtualną.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Zatrzymywanie maszyny wirtualnej w laboratorium

Aby zatrzymać maszynę wirtualną, wybierz polecenie **Zatrzymaj** na kafelku. Po zatrzymaniu maszyny wirtualnej przycisk **Uruchom** na kafelku zostanie włączony. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
 