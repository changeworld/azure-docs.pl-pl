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
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 389d467bd9672743d4a086e8a1c505fb0366dba7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237109"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Włączanie i używanie usług pulpitu zdalnego dla maszyn wirtualnych systemu Linux w laboratorium w usłudze Azure Lab Services
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Włączanie pulpitu zdalnego dla maszyny Wirtualnej systemu Linux
- Jak dla nauczycieli może łączyć się z szablonu maszyny Wirtualnej za pomocą połączenia pulpitu zdalnego (RDP).
- Jak studenci się połączyć z uczniów maszyny Wirtualnej za pośrednictwem protokołu RDP

## <a name="enable-remote-desktop-for-linux-vm"></a>Włączanie pulpitu zdalnego dla maszyny Wirtualnej systemu Linux
Podczas tworzenia laboratorium, aby umożliwić nauczycielom **połączeń usług pulpitu zdalnego** dla **Linux** obrazów. **Podłączanie pulpitu zdalnego włączyć** opcja jest wyświetlana po wybraniu obrazu systemu Linux dla szablonu. Gdy ta opcja jest włączona, nauczyciele mogą łączyć się szablonu maszyny Wirtualnej i maszyn wirtualnych dla uczniów, za pośrednictwem protokołu RDP (pulpitu zdalnego). 

![Włączanie połączeń usług pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Na **Włączanie Podłączanie pulpitu zdalnego** okno komunikatu, wybierz opcję **Kontynuuj przy użyciu pulpitu zdalnego**. 

![Włączanie połączeń usług pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

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

Aby uzyskać więcej informacji dotyczących nawiązywania połączenia z maszyną wirtualną przy użyciu klienta SSH lub RDP Zobacz [nawiąż połączenie przy użyciu klienta SSH lub RDP]((#connect-using-ssh-or-rdp). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Nauczyciele nawiązywania połączenia z student maszyny Wirtualnej przy użyciu protokołu RDP
Nauczyciel/Profesor mogą łączyć się student maszyny Wirtualnej z, przełączając się **maszyn wirtualnych** wyświetlić, a następnie wybierając **połączyć** ikony. Wcześniej należy nauczycieli **publikowania** obrazu szablonu za pomocą protokołu RDP i graficzny interfejs użytkownika pakietów zainstalowanych na nim. 

![Nauczyciele nawiązywania połączenia z maszyny Wirtualnej dla uczniów](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Aby uzyskać więcej informacji dotyczących nawiązywania połączenia z maszyną wirtualną przy użyciu klienta SSH lub RDP Zobacz [nawiąż połączenie przy użyciu klienta SSH lub RDP]((#connect-using-ssh-or-rdp). 

## <a name="students-connecting-to-the-student-vm"></a>Studenci nawiązywania połączenia z maszyny Wirtualnej dla uczniów
Student może protokołu RDP w systemie do ich maszyn wirtualnych systemu Linux po właściciel laboratorium (nauczyciel/Profesor) **publikuje** szablonu maszyny Wirtualnej przy użyciu protokołu RDP i graficzny interfejs użytkownika pakiety zainstalowane na komputerze. Oto konkretne kroki: 

1. Gdy Uczeń loguje się do portalu Labs bezpośrednio (`https://labs.azure.com`) lub za pomocą łącza rejestracji (`https://labs.azure.com/register/<registrationCode>`), dla każdego ucznia laboratorium ma dostęp do jest wyświetlany Kafelek. 
2. Na kafelku, wybierz **Start** Jeśli maszyna wirtualna jest zatrzymana. 
3. Wybierz przycisk **Połącz**. Zostaną wyświetlone dwie opcje, aby nawiązać połączenie z maszyną Wirtualną: **SSH** i **pulpitu zdalnego**.

    ![Student maszyny Wirtualnej — opcje połączenia](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Nawiązywanie połączenia przy użyciu klienta SSH lub RDP
Jeśli wybierzesz **SSH** opcji, zapoznaj się z poniższymi **nawiązywanie połączenia z maszyną wirtualną** okno dialogowe:  

![Parametry połączenia SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Wybierz **kopiowania** przycisk znajdujący się obok pola tekstowego, skopiuj go do Schowka. Zapisz parametry połączenia SSH. Użyj tych parametrów połączenia z poziomu terminalu SSH (takich jak [Putty](https://www.putty.org/)) nawiązywania połączenia z maszyną wirtualną.

Jeśli wybierzesz **RDP** opcji, plik RDP jest pobierany na swojej maszynie. Zapisz go i otwórz go, aby połączyć się z komputerem. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)

