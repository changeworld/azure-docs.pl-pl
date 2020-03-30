---
title: Jak połączyć się z maszyną wirtualną usług Azure Lab Services z komputera Mac | Dokumenty firmy Microsoft
description: Ten artykuł
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503090"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Łączenie się z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac
W tej sekcji pokazano, jak uczeń może połączyć się z maszyną wirtualną w laboratorium klasy z komputera Mac przy użyciu protokołu RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalowanie pulpitu zdalnego firmy Microsoft na komputerze Mac
1. Otwórz sklep App Store na komputerze Mac i wyszukaj program **Microsoft Remote Desktop**.

    ![Pulpit zdalny firmy Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Zainstaluj najnowszą wersję pulpitu zdalnego firmy Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Dostęp do maszyny Wirtualnej z komputera Mac przy użyciu protokołu RDP
1. Otwórz plik **RDP** pobrany na komputer z **zainstalowanym pulpitem zdalnym firmy Microsoft.** Należy rozpocząć łączenie się z maszyną wirtualną. 

    ![Łączenie z maszyną wirtualną](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Wybierz **przycisk Kontynuuj,** jeśli zostanie wyświetlone następujące ostrzeżenie. 

    ![Ostrzeżenie o certyfikacie](../media/how-to-use-classroom-lab/certificate-error.png)
1. Powinna zostać wyświetlenia maszyny Wirtualnej. 

    > [!NOTE]
    > Poniższy przykład dotyczy maszyny Wirtualnej z systemem Linux CentOS. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak łączyć się z maszynami wirtualnymi z systemem Linux przy użyciu protokołu RDP, zobacz [Używanie pulpitu zdalnego dla maszyn wirtualnych systemu Linux](how-to-use-remote-desktop-linux-student.md)


