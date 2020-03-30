---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66416068"
---
* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc należy zaplanować migrację maszyn wirtualnych podczas istniejącego okna obsługi. 

* Konwersja jest nieodwracalna. 

* Należy pamiętać, że wszyscy użytkownicy z roli [współautora maszyny wirtualnej](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) nie będzie mógł zmienić rozmiar maszyny wirtualnej (jak mogą one wstępnie konwersji). Dzieje się tak, ponieważ maszyny wirtualne z dyskami zarządzanymi wymagają od użytkownika uprawnienia Microsoft.Compute/disks/write na dyskach systemu operacyjnego.

* Należy przetestować konwersję. Przeprowadź migrację testowej maszyny wirtualnej, zanim przeprowadzisz migrację w środowisku produkcyjnym.

* Podczas konwersji cofany jest przydział maszyny wirtualnej. Maszyna wirtualna otrzyma nowy adres IP przy jej ponownym uruchomieniu po konwersji. W razie potrzeby do maszyny wirtualnej można [przypisać statyczny adres IP](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Przejrzyj minimalną wersję agenta maszyny Wirtualnej platformy Azure wymaganą do obsługi procesu konwersji. Aby uzyskać informacje na temat sprawdzania i aktualizowania wersji agenta, zobacz [Minimalna obsługa wersji dla agentów maszyn wirtualnych na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)