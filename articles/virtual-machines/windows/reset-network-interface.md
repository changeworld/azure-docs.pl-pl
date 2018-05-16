---
title: Jak można zresetować interfejsu sieciowego dla maszyny Wirtualnej systemu Windows Azure | Dokumentacja firmy Microsoft
description: Pokazuje, jak można zresetować interfejsu sieciowego dla maszyny Wirtualnej systemu Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 7c78c80e5eaceb210a50549e2885e56c0d9ec7f1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Jak można zresetować interfejsu sieciowego dla maszyny Wirtualnej systemu Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Nie można nawiązać połączenia z do maszyny wirtualnej systemu Windows Azure (VM) firmy Microsoft, po wyłącza domyślnego interfejsu sieciowego (NIC) lub ręcznie ustawia statyczny adres IP dla karty sieciowej. W tym artykule przedstawiono sposób resetowania interfejsu sieciowego dla maszyny Wirtualnej systemu Windows Azure, która rozwiązuje problem połączenia zdalnego.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Resetuj interfejsu sieciowego

### <a name="for-classic-vms"></a>Klasycznych maszyn wirtualnych

Aby zresetować interfejsu sieciowego, wykonaj następujące kroki:

1.  Przejdź do witryny [Azure Portal]( https://ms.portal.azure.com).
2.  Wybierz **maszyn wirtualnych (klasyczne)**.
3.  Wybierz maszynę wirtualną, których dotyczy.
4.  Wybierz **adresów IP**.
5.  Jeśli **przydziału prywatnego adresu IP** nie jest **statycznych**, Zmień, aby **statycznych**.
6.  Zmień **adres IP** do innego adresu IP, który jest dostępny w podsieci.
7.  Wybierz opcję Zapisz.
8.  Maszyna wirtualna zostanie uruchomiony ponownie zainicjować nowe karty interfejsu Sieciowego w systemie.
9.  Spróbuj RDP na komputerze. W przypadku powodzenia można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można zachować. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Dla maszyn wirtualnych wdrożonych w modelu grupy zasobów

1.  Przejdź do witryny [Azure Portal]( https://ms.portal.azure.com).
2.  Wybierz maszynę wirtualną, których dotyczy.
3.  Wybierz **interfejsy sieciowe**.
4.  Wybierz interfejs sieciowy skojarzony z tym komputerze
5.  Wybierz **konfiguracje adresów IP**.
6.  Wybierz adres IP. 
7.  Jeśli **przydziału prywatnego adresu IP** nie jest **statycznych**, Zmień, aby **statycznych**.
8.  Zmień **adres IP** do innego adresu IP, który jest dostępny w podsieci.
9. Maszyna wirtualna zostanie uruchomiony ponownie zainicjować nowe karty interfejsu Sieciowego w systemie.
10. Spróbuj RDP na komputerze. W przypadku powodzenia można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można zachować. 

## <a name="delete-the-unavailable-nics"></a>Usuń niedostępny kart sieciowych
Po można pulpitu zdalnego do maszyny, należy usunąć starego kart sieciowych, aby uniknąć potencjalnych problemów:

1.  Otwórz Menedżera urządzeń.
2.  Wybierz **widoku** > **Pokaż ukryte urządzenia**.
3.  Wybierz **karty sieciowe**. 
4.  Sprawdź, czy karty jako "Karta sieciowa Microsoft Hyper-V".
5.  Możesz zobaczyć niedostępne karty przedstawione jako wyszarzone. Kliknij prawym przyciskiem myszy kartę, a następnie wybierz Odinstaluj.

    ![Obraz karty interfejsu Sieciowego](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Tylko odinstalować niedostępny kart o nazwie "Karta sieciowa Microsoft Hyper-V". Po odinstalowaniu wszystkich innych kart ukryte może to spowodować dodatkowe problemy.
    >
    >

6.  Teraz wszystkie karty niedostępny powinny zostać wyczyszczone z systemu.
