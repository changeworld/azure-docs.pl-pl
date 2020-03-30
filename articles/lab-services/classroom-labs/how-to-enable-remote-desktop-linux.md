---
title: Włączanie pulpitu zdalnego dla systemu Linux w usługach Azure Lab | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć pulpit zdalny dla maszyn wirtualnych systemu Linux w laboratorium w usłudze Azure Lab Services.
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
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270864"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Włączanie pulpitu zdalnego dla maszyn wirtualnych systemu Linux w laboratorium w usługach Azure Lab Services
W tym artykule pokazano, jak wykonać następujące zadania:

- Włączanie pulpitu zdalnego dla maszyny Wirtualnej systemu Linux
- Jak nauczyciel może łączyć się z szablonem maszyny Wirtualnej za pośrednictwem usługi Podłączanie pulpitu zdalnego (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Włączanie pulpitu zdalnego dla maszyny Wirtualnej systemu Linux
Podczas tworzenia laboratorium nauczyciele mogą włączyć **połączenie pulpitu zdalnego** dla obrazów **linuksa.** Opcja **Włącz połączenie pulpitu zdalnego** jest wyświetlana, gdy dla szablonu jest wybrany obraz systemu Linux. Gdy ta opcja jest włączona, nauczyciele mogą łączyć się z szablonami maszyn wirtualnych i maszyn wirtualnych uczniów za pośrednictwem protokołu RDP (pulpit zdalny). 

![Włączanie połączenia pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

W oknie komunikatu **Włączanie połączenia pulpitu zdalnego** wybierz pozycję **Kontynuuj z pulpitem zdalnym**. 

![Włączanie połączenia pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Włączenie **połączenia pulpitu zdalnego** otwiera port **RDP** tylko na komputerach z systemem Linux. Jeśli protokół RDP jest już zainstalowany i skonfigurowany na obrazie maszyny wirtualnej (na przykład: obraz maszyny wirtualnej nauki o danych Ubuntu), ty/studenci mogą łączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP bez konieczności poczynienia żadnych dodatkowych kroków.
> 
> Jeśli obraz maszyny Wirtualnej nie ma zainstalowanego i skonfigurowany rdp, należy połączyć się z komputerem Linux przy użyciu SSH po raz pierwszy i zainstalować pakiety RDP i GUI, dzięki czemu ty/studenci mogą połączyć się z komputerem Linux przy użyciu protokołu RDP później. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie pulpitu zdalnego w celu łączenia się z maszyną wirtualną z systemem Linux na platformie Azure](../../virtual-machines/linux/use-remote-desktop.md). Następnie można opublikować obraz, dzięki czemu uczniowie mogą RDP w student maszyn wirtualnych z systemem Linux. 

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Obecnie połączenie pulpitu zdalnego jest obsługiwane dla następujących systemów operacyjnych:

- openSUSE Leap 42.3
- CentOS oparty na 7,5
- Debian 9 "Rozciągnięcie"
- Serwer Ubuntu 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Łączenie z maszyną wirtualną szablonu 
Nauczyciele muszą najpierw połączyć się z szablonem maszyny Wirtualnej przy użyciu protokołu SSH i zainstalować na niej pakiety RDP i GUI. Następnie nauczyciele mogą używać protokołu RDP do łączenia się z szablonem maszyny Wirtualnej: 

1. Jeśli na pasku narzędzi zostanie wyświetlony temat **Dostosuj szablon,** zaznacz go. Następnie wybierz pozycję **Kontynuuj** w oknie dialogowym **Dostosowywanie szablonu.** Ta akcja uruchamia szablon maszyny Wirtualnej.  

    ![Dostosowywanie szablonu](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Po uruchomieniu szablonu maszyny Wirtualnej można wybrać **szablon Połącz,** a następnie **połączyć za pośrednictwem protokołu SSH** na pasku narzędzi. 

    ![Łączenie się z szablonem za pośrednictwem protokołu RDP po utworzeniu laboratorium](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Zostanie wyświetlone następujące okno dialogowe **Połącz z maszyną wirtualną.** Zaznacz przycisk **Kopiuj** obok pola tekstowego, aby skopiować go do schowka. Zapisz parametry połączenia SSH. Użyj tego ciągu połączenia z terminala SSH (jak [Putty](https://www.putty.org/)), aby połączyć się z maszyną wirtualną.
 
    ![Ciąg połączenia SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Zainstaluj pakiety RDP i GUI, abyś/studenci mogli później połączyć się z komputerem z systemem Linux przy użyciu protokołu RDP. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie pulpitu zdalnego w celu łączenia się z maszyną wirtualną z systemem Linux na platformie Azure](../../virtual-machines/linux/use-remote-desktop.md). Następnie można opublikować obraz, dzięki czemu uczniowie mogą RDP w student maszyn wirtualnych z systemem Linux.
5. Po zainstalowaniu tych pakietów można użyć **szablonu Połącz z na** pasku narzędzi, a następnie wybrać **opcję Połącz za pośrednictwem protokołu RDP,** aby połączyć się z szablonem maszyny Wirtualnej za pośrednictwem protokołu RDP. Zapisz plik RDP i użyj go do połączenia się z szablonem maszyny Wirtualnej za pośrednictwem protokołu RDP. 

## <a name="next-steps"></a>Następne kroki
Po włączeniu funkcji połączenia pulpitu zdalnego przez instruktora uczniowie mogą łączyć się ze swoimi maszynami wirtualnymi za pośrednictwem protokołu RDP/SSH. Aby uzyskać więcej informacji, zobacz [Używanie pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium w klasie](how-to-use-remote-desktop-linux-student.md). 