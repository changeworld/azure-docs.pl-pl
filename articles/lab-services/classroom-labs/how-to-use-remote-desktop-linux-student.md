---
title: Używanie pulpitu zdalnego dla systemu Linux w usługach Azure Lab | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać pulpitu zdalnego dla maszyn wirtualnych systemu Linux w laboratorium w usługach Azure Lab Services.
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
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585075"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Używanie pulpitu zdalnego dla maszyn wirtualnych systemu Linux w laboratorium usługi Azure Lab Services w klasie
W tym artykule pokazano, jak uczniowie mogą łączyć się z maszyną wirtualną systemu Linux (VM) w laboratorium przy użyciu protokołu RDP/SSH. 

Instruktor musi włączyć funkcję połączenia pulpitu zdalnego, zanim uczniowie będą mogli połączyć się z maszyną wirtualną laboratorium klasy. Aby uzyskać instrukcje dotyczące sposobu włączania funkcji połączenia pulpitu zdalnego, zobacz [Włączanie pulpitu zdalnego dla maszyn wirtualnych systemu Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Włączenie **połączenia pulpitu zdalnego** otwiera port **RDP** tylko na komputerach z systemem Linux. Instruktor może połączyć się z komputerem Linux przy użyciu protokołu SSH po raz pierwszy i zainstalować pakiety RDP i GUI, dzięki czemu można później połączyć się z komputerem z systemem Linux przy użyciu protokołu RDP. 

## <a name="connect-to-the-student-vm"></a>Łączenie się z maszyną wirtualną dla studentów
Studenci mogą RDP w ich linux maszyn wirtualnych po właściciel laboratorium (nauczyciel / profesor) **publikuje** szablon maszyny Wirtualnej z pakietów RDP i GUI zainstalowane na komputerze. Oto konkretne kroki: 

1. Gdy uczeń loguje się bezpośrednio do`https://labs.azure.com`portalu Labs ( )`https://labs.azure.com/register/<registrationCode>`lub za pomocą linku rejestracyjnego ( ), wyświetlany jest kafelek dla każdego laboratorium, do których student ma dostęp. 
2. Na kafelku przełącz przycisk, aby uruchomić maszynę wirtualną, jeśli jest w stanie zatrzymania. 
3. Wybierz przycisk **Połącz**. Dostępne są dwie opcje łączenia się z maszyną wirtualną: **SSH** i **Pulpit zdalny**.

    ![Student VM - opcje połączenia](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Łączenie za pomocą protokołu SSH lub RDP
Jeśli wybierzesz opcję **SSH,** zostanie wyświetlone następujące okno dialogowe **Połącz z maszyną wirtualną:**  

![Ciąg połączenia SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Zaznacz przycisk **Kopiuj** obok pola tekstowego, aby skopiować go do schowka. Zapisz parametry połączenia SSH. Użyj tego ciągu połączenia z terminala SSH (jak [Putty](https://www.putty.org/)), aby połączyć się z maszyną wirtualną.

Jeśli wybierzesz opcję **RDP,** plik RDP zostanie pobrany na komputer. Zapisz go i otwórz, aby połączyć się z urządzeniem. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak włączyć funkcję połączenia pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium klasy, zobacz [Włączanie pulpitu zdalnego dla maszyn wirtualnych systemu Linux](how-to-enable-remote-desktop-linux.md). 

