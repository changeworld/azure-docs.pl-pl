---
title: Włączanie pulpitu zdalnego dla systemu Linux w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak włączyć pulpit zdalny dla maszyn wirtualnych z systemem Linux w laboratorium w Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: c67ca111bf87c9dbfa69c93149d29dbd32767fbd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350753"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Włącz pulpit zdalny dla maszyn wirtualnych z systemem Linux w laboratorium w Azure Lab Services
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Włączanie pulpitu zdalnego dla maszyny wirtualnej z systemem Linux
- Jak nauczyciel może połączyć się z szablonową maszyną wirtualną za pośrednictwem Podłączanie pulpitu zdalnego (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Włączanie pulpitu zdalnego dla maszyny wirtualnej z systemem Linux
Podczas tworzenia laboratorium nauczyciele mogą włączyć Podłączanie **pulpitu zdalnego** dla obrazów systemu **Linux** . Opcja **włącz Podłączanie pulpitu zdalnego** jest wyświetlana, gdy dla szablonu wybrano obraz systemu Linux. Gdy ta opcja jest włączona, nauczyciele mogą łączyć się z MASZYNami wirtualnymi z szablonami i uczniami za pośrednictwem protokołu RDP (Pulpit zdalny). 

![Włączanie połączenia pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

W oknie dialogowym **Włączanie komunikatu Podłączanie pulpitu zdalnego** wybierz pozycję **Kontynuuj z pulpit zdalny**. 

![Włączanie połączenia pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Włączenie usługi **Podłączanie pulpitu zdalnego** otwiera tylko port **RDP** na maszynach z systemem Linux. Jeśli protokół RDP jest już zainstalowany i skonfigurowany w obrazie maszyny wirtualnej (na przykład: Ubuntu Data Science Virtual Machine Image), ty/uczniowie mogą łączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP, nie wykonując żadnych dodatkowych kroków.
> 
> Jeśli na obrazie maszyny wirtualnej nie zainstalowano i skonfigurowano protokół RDP, należy połączyć się z maszyną z systemem Linux przy użyciu protokołu SSH po raz pierwszy, a następnie zainstalować pakiety RDP i GUI, aby umożliwić uczniom łączenie się z maszyną z systemem Linux przy użyciu protokołu RDP. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../../virtual-machines/linux/use-remote-desktop.md). Następnie opublikujesz obraz tak, aby studenci mogli na maszynach wirtualnych z systemem Linux mieć protokół RDP. 

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Obecnie jest obsługiwane Podłączanie pulpitu zdalnego dla następujących systemów operacyjnych:

- openSUSE przestępnie 42,3
- CentOS — based 7,5
- Debian 9 "rozciąganie"
- Ubuntu Server 16,04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Nauczyciele łączące się z maszyną wirtualną przy użyciu protokołu RDP
Nauczyciele muszą połączyć się z szablonową maszyną wirtualną przy użyciu protokołu SSH, a następnie zainstalować na niej pakiety RDP i GUI. Następnie nauczyciele mogą połączyć się z maszynami wirtualnymi z systemem Linux przy użyciu protokołu RDP, wykonując następujące czynności: 

Zobaczysz opcję **pulpit zdalny** , aby połączyć się z SZABLONową maszyną wirtualną w momencie tworzenia laboratorium. 

![Nawiązywanie połączenia z szablonem za pośrednictwem protokołu RDP w momencie tworzenia](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Na stronie głównej laboratorium zobaczysz opcję **pulpit zdalny** po utworzeniu laboratorium i uruchomieniu szablonu VM. Uruchom szablonową maszynę wirtualną, jeśli nie została już uruchomiona. 

![Nawiązywanie połączenia z szablonem za pośrednictwem protokołu RDP po utworzeniu laboratorium](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszyną wirtualną przy użyciu protokołu SSH lub RDP, zobacz [nawiązywanie połączenia przy użyciu protokołu SSH lub RDP] ((#connect-using-SSH-lub-RDP). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Nauczyciele łączące się z maszyną wirtualną uczniów przy użyciu protokołu RDP
Nauczyciel/profesor może połączyć się z maszyną wirtualną ucznia, przełączając się do widoku **Virtual Machines** i wybierając ikonę **Połącz** . Wcześniej nauczyciele muszą **opublikować** obraz szablonu z zainstalowanymi pakietami RDP i graficznym interfejsem użytkownika. 

![Nauczyciele łączące się z maszyną wirtualną ucznia](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszyną wirtualną przy użyciu protokołu SSH lub RDP, zobacz [nawiązywanie połączenia przy użyciu protokołu SSH lub RDP] ((#connect-using-SSH-lub-RDP). 

## <a name="connect-using-ssh-or-rdp"></a>Nawiązywanie połączenia przy użyciu protokołu SSH lub RDP
W przypadku wybrania opcji **SSH** zostanie wyświetlone okno dialogowe **Połącz z maszyną wirtualną** :  

![Parametry połączenia SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Wybierz przycisk **Kopiuj** obok pola tekstowego, aby skopiować go do Schowka. Zapisz parametry połączenia SSH. Użyj tych parametrów połączenia z terminalu SSH (jak tutaj [](https://www.putty.org/)), aby nawiązać połączenie z maszyną wirtualną.

W przypadku wybrania opcji **RDP** na komputerze zostanie pobrany plik RDP. Zapisz go i Otwórz, aby nawiązać połączenie z maszyną. 

## <a name="next-steps"></a>Następne kroki
Po włączeniu funkcji Podłączanie pulpitu zdalnego przez instruktora studenci mogą łączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP/SSH. Aby uzyskać więcej informacji, zobacz [Korzystanie z pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium z klasą](how-to-use-remote-desktop-linux-student.md). 