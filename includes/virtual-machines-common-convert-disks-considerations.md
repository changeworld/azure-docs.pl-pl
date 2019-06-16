---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416068"
---
* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc należy zaplanować migrację maszyn wirtualnych podczas istniejącego okna obsługi. 

* Konwersja jest nieodwracalna. 

* Należy pamiętać, że wszelkie użytkowników za pomocą [Współautor maszyny wirtualnej](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) roli nie będzie można zmienić rozmiar maszyny Wirtualnej (tak jak byłoby to możliwe wstępne konwersji). Jest to spowodowane maszyny wirtualne z dyskami zarządzanymi wymagają użytkownik musi mieć uprawnienie Microsoft.Compute/disks/write na dyskach systemu operacyjnego.

* Należy przetestować konwersję. Przeprowadź migrację testowej maszyny wirtualnej, zanim przeprowadzisz migrację w środowisku produkcyjnym.

* Podczas konwersji cofany jest przydział maszyny wirtualnej. Maszyna wirtualna otrzyma nowy adres IP przy jej ponownym uruchomieniu po konwersji. W razie potrzeby do maszyny wirtualnej można [przypisać statyczny adres IP](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Przejrzyj minimalna wersja agenta maszyny Wirtualnej platformy Azure wymagane do obsługi procesu konwersji. Aby uzyskać informacje na temat sposobu Sprawdź i zaktualizuj swoją wersję agenta, zobacz [minimalna obsługiwana wersja dla agentów maszyn wirtualnych na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)