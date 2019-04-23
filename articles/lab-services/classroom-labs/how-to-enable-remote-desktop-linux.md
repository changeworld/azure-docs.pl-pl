---
title: Włączanie pulpitu zdalnego dla systemu Linux w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć Pulpit zdalny dla maszyn wirtualnych systemu Linux w laboratorium w usłudze Azure Lab Services.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 6985bd0bbae858ad258e723ef4d6d6d687b2c86e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005353"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Włączanie i używanie usług pulpitu zdalnego dla maszyn wirtualnych systemu Linux w laboratorium w usłudze Azure Lab Services
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Włączanie pulpitu zdalnego dla maszyny Wirtualnej systemu Linux
- Jak dla nauczycieli może łączyć się z szablonu maszyny Wirtualnej za pomocą połączenia pulpitu zdalnego (RDP).
- Jak studenci się połączyć z uczniów maszyny Wirtualnej za pośrednictwem protokołu RDP

## <a name="enable-remote-desktop-for-linux-vm"></a>Włączanie pulpitu zdalnego dla maszyny Wirtualnej systemu Linux
Podczas tworzenia laboratorium, aby umożliwić nauczycielom **połączeń usług pulpitu zdalnego** dla **Linux** obrazów. **Podłączanie pulpitu zdalnego włączyć** opcja jest wyświetlana po wybraniu obrazu systemu Linux dla szablonu. Gdy ta opcja jest włączona, nauczyciele mogą łączyć się szablonu maszyny Wirtualnej i maszyn wirtualnych dla uczniów, za pośrednictwem protokołu RDP (pulpitu zdalnego). 

![Włączanie połączeń usług pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

> [!IMPORTANT] 
> Włączanie **połączeń usług pulpitu zdalnego** tylko otwiera **RDP** port na maszynach z systemem Linux. Możesz jako nauczyciel, połącz się po raz pierwszy przy użyciu protokołu SSH maszyny z systemem Linux i zainstalować pakiety protokołu RDP i graficznego interfejsu użytkownika, dzięki czemu można nawiązać połączenie później przy użyciu protokołu RDP maszyny z systemem Linux. Następnie możesz **publikowania** obrazu tak, aby uczniowie może protokołu RDP w systemie uczniów maszyn wirtualnych systemu Linux. 

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Obecnie połączeń usług pulpitu zdalnego jest obsługiwana dla następujących systemów operacyjnych:

- openSUSE Leap 42.3
- Opartych na systemie centOS w wersji 7.5
- Debian 9 "Stretch."
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Nauczyciele nawiązywania połączenia z szablonu maszyny Wirtualnej przy użyciu protokołu RDP
Nauczyciele musi nawiązać połączenie z szablonu maszyny Wirtualnej przy użyciu protokołu SSH, najpierw, a następnie zainstalować pakiety protokołu RDP i graficznego interfejsu użytkownika na nim. Następnie nauczycieli następujące kroki służą połączyć się z maszyn wirtualnych systemu Linux przy użyciu protokołu RDP: 

Zostanie wyświetlony **pulpitu zdalnego** opcję nawiązywania połączenia z szablonu maszyny Wirtualnej w czasie tworzenia laboratorium. 

![Łączenie do szablonu, za pośrednictwem protokołu RDP w czasie tworzenia](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Zostanie wyświetlony **pulpitu zdalnego** opcja na stronie głównej laboratorium, po utworzeniu laboratorium i szablonu maszyny Wirtualnej jest uruchomiona. Jeśli nie jest już uruchomiona, uruchom szablon maszyny Wirtualnej. 

![Łączenie do szablonu, za pośrednictwem protokołu RDP po utworzeniu laboratorium](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Po wybraniu **RDP** opcji pobiera plik RDP. Można otworzyć, aby nawiązać połączenie z maszyny z systemem Linux. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Nauczyciele nawiązywania połączenia z student maszyny Wirtualnej przy użyciu protokołu RDP
Właściciel laboratorium (nauczyciel/Profesor) mogą łączyć się student maszyny Wirtualnej z, przełączając się **maszyn wirtualnych** wyświetlić, a następnie wybierając **połączyć** ikony. Wcześniej należy nauczycieli **publikowania** obrazu szablonu za pomocą protokołu RDP i graficzny interfejs użytkownika pakietów zainstalowanych na nim. 

![Nauczyciele nawiązywania połączenia z maszyny Wirtualnej dla uczniów](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

## <a name="students-connecting-to-the-student-vm"></a>Studenci nawiązywania połączenia z maszyny Wirtualnej dla uczniów
Student może protokołu RDP w systemie do ich maszyn wirtualnych systemu Linux po właściciel laboratorium (nauczyciel/Profesor) **publikuje** szablonu maszyny Wirtualnej przy użyciu protokołu RDP i graficzny interfejs użytkownika pakiety zainstalowane na komputerze. Oto konkretne kroki: 

1. Gdy Uczeń loguje się do portalu Labs bezpośrednio (`http://labs.azure.com`) lub za pomocą łącza rejestracji (`http://labs.azure.com/register/<registrationCode>`), dla każdego ucznia laboratorium ma dostęp do jest wyświetlany Kafelek. 
2. Na kafelku, wybierz **Start** Jeśli maszyna wirtualna jest zatrzymana. 
3. Wybierz przycisk **Połącz**. Ta akcja spowoduje pobranie pliku RDP na swojej maszynie. Zapisz go i Otwórz, aby nawiązać połączenie z maszyny z systemem Linux za pośrednictwem protokołu RDP. 

    ![Pobierz protokołu RDP maszyny Wirtualnej — dla uczniów](../media/how-to-enable-remote-desktop-linux/student-rdp-download.png)

    Można nadal połączyć się z maszyną wirtualną systemu Linux, przy użyciu protokołu SSH. Wybierz **... (wielokropek)**  do wyświetlenia opcji protokołu SSH. 
    
    ![Student maszyny Wirtualnej — SSH](../media/how-to-enable-remote-desktop-linux/student-ssh.png)

    Skopiuj i Zapisz parametry połączenia SSH na **nawiązywanie połączenia z maszyną wirtualną** okno dialogowe. Użyj tych parametrów połączenia z poziomu terminalu SSH (takich jak [Putty](https://www.putty.org/)) nawiązywania połączenia z maszyną wirtualną. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)

