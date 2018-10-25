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
ms.date: 10/17/2018
ms.author: spelluru
ms.openlocfilehash: 4b137396dd6a8ff924c9380aeb87a81b95f91414
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466226"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services
W tym artykule opisano uzyskiwanie dostępu do laboratorium, nawiązywanie połączenia z maszyną wirtualną w laboratorium i zatrzymywanie maszyny wirtualnej. 

## <a name="view-all-the-classroom-labs"></a>Wyświetlanie wszystkich laboratoriów

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. 
2. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 
3. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratoriów, do których masz dostęp. 

    ![Wyświetlanie wszystkich laboratoriów](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Nawiązywanie połączenia z maszyną wirtualną w laboratorium

1. Wybierz polecenie **Połącz** na kafelku reprezentującym maszynę wirtualną laboratorium, do której chcesz uzyskać dostęp.

    ![Wyświetlanie wszystkich laboratoriów](../media/how-to-use-classroom-lab/connect-button.png)
2. Przygotowanie maszyny wirtualnej trwa kilka minut.

    ![Uzyskiwanie stanu gotowości maszyny wirtualnej](../media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Zapisz plik RDP na dysku twardym, a następnie otwórz go. 
    
    ![Zapisywanie pliku RDP](../media/how-to-use-classroom-lab/save-rdp-file.png)
4. Użyj **nazwy użytkownika** i **hasła** uzyskanych od nauczyciela, aby zalogować się do maszyny. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Zatrzymywanie maszyny wirtualnej w laboratorium

Wybierz polecenie **Zatrzymaj** na kafelku reprezentującym laboratorium. Po zatrzymaniu maszyny wirtualnej przycisk **Uruchom** na kafelku zostanie włączony. 

## <a name="next-steps"></a>Następne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md)
- [Konfigurowanie laboratorium](../tutorial-create-custom-lab.md)

 