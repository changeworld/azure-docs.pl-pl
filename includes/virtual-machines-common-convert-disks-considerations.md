---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bf26af7fa4b1b31514fb82c5e28a85154b2e274a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158469"
---
* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc należy zaplanować migrację maszyn wirtualnych podczas istniejącego okna obsługi. 

* Konwersja jest nieodwracalna. 

* Należy pamiętać, że wszelkie użytkowników za pomocą [Współautor maszyny wirtualnej](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) roli nie będzie można zmienić rozmiar maszyny Wirtualnej (tak jak byłoby to możliwe wstępne konwersji). Jest to spowodowane maszyny wirtualne z dyskami zarządzanymi wymagają użytkownik musi mieć uprawnienie Microsoft.Compute/disks/write na dyskach systemu operacyjnego.

* Należy przetestować konwersję. Przeprowadź migrację testowej maszyny wirtualnej, zanim przeprowadzisz migrację w środowisku produkcyjnym.

* Podczas konwersji cofany jest przydział maszyny wirtualnej. Maszyna wirtualna otrzyma nowy adres IP przy jej ponownym uruchomieniu po konwersji. W razie potrzeby do maszyny wirtualnej można [przypisać statyczny adres IP](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Oryginalne wirtualne dyski twarde i konto magazynu używane przez maszynę wirtualną przed konwersją nie są usuwane. Nadal będą za nie naliczane opłaty. Aby uniknąć naliczania opłat za te artefakty, usuń oryginalne obiekty blob dysków VHD po upewnieniu się, że konwersja została zakończona.

* Przejrzyj minimalna wersja agenta maszyny Wirtualnej platformy Azure wymagane do obsługi procesu konwersji. Aby uzyskać informacje na temat sposobu Sprawdź i zaktualizuj swoją wersję agenta, zobacz [minimalna obsługiwana wersja dla agentów maszyn wirtualnych na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)