---
title: Konfigurowanie aplikacji migracji platformy Azure
description: Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure do oceny i migracji maszyn wirtualnych VMware.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337664"
---
# <a name="set-up-an-azure-migrate-appliance"></a>Konfigurowanie urządzenia migracji platformy Azure

W tym artykule podsumowano opcje konfigurowania urządzenia migracji platformy Azure. 

Urządzenie migracji platformy Azure jest wdrożeniem urządzenia w środowisku lokalnym i jest używane w wielu scenariuszach.

**Scenariusz** | **Szczegóły**
--- | ---
Ocena maszyn wirtualnych VMware za pomocą programu Azure Migrate:Server Assessment | Odnajduj aplikacje i zależności maszyn wirtualnych<br/><br/> Zbieranie metadanych maszyny i metadanych wydajności do oceny.
Ocena maszyn wirtualnych funkcji Hyper V za pomocą programu Azure Migrate:Server Assessment | Odkryj maszyny wirtualne funkcji Hyper V<br/><br/> Zbieranie metadanych maszyny i metadanych wydajności do oceny.
Ocena maszyn fizycznych | Odkrywanie maszyn jako serwerów fizycznych<br/><br/> Zbieranie metadanych maszyny i metadanych wydajności do oceny.
Replikowanie maszyn wirtualnych VMware z migracją bez agenta. | Replikuj maszyny wirtualne VMware bez instalowania czegokolwiek na maszynach wirtualnych, które chcesz replikować.


## <a name="deployment-options"></a>Opcje wdrożenia

Urządzenie można wdrożyć na kilka sposobów.

**Scenariusz** | **Szablonu** | **Skrypt** 
--- | --- | --- | ---
**Ocena maszyn wirtualnych VMware** | Wdrażanie z pobranym szablonem ova.<br/><br/> Dowiedz się, jak wdrożyć urządzenie [przy użyciu szablonu](how-to-set-up-appliance-vmware.md)lub uruchomić [samouczek oceny](tutorial-prepare-vmware.md) i wdrożyć urządzenie z szablonem podczas samouczka.  | Wdrażanie przy użyciu skryptu instalatora programu PowerShell.<br/><br/>  [Przejrzyj](deploy-appliance-script.md) instrukcje dotyczące skryptów urządzenia.
**Ocena maszyn wirtualnych funkcji Hyper V** | Wdrażanie z pobranym szablonem VHD. <br/><br/> Dowiedz się, jak wdrożyć urządzenie [przy użyciu szablonu](how-to-set-up-appliance-vmware.md)lub uruchomić [samouczek oceny](tutorial-prepare-vmware.md) i wdrożyć urządzenie z szablonem podczas samouczka. | Wdrażanie przy użyciu skryptu instalatora programu PowerShell.<br/><br/> [Przejrzyj](deploy-appliance-script.md) instrukcje dotyczące skryptów urządzenia. 
**Ocena serwerów fizycznych** | Brak szablonu. | Wdrażanie przy użyciu skryptu instalatora programu PowerShell.<br/><br/> Przejrzyj [instrukcje dotyczące skryptu urządzenia](how-to-set-up-appliance-physical.md)lub uruchom [samouczek oceny](tutorial-prepare-physical.md)i wdrażaj urządzenie podczas samouczka.
**Replikowanie maszyn wirtualnych VMware (bez agenta)** | Wdrażanie z pobranym szablonem ova.<br/><br/> Jeśli zostały już ocenione maszyny wirtualne, które replikujesz, urządzenie jest już skonfigurowane podczas oceny.<br/><br/> Jeśli replikujesz maszyny wirtualne VMware bez ich oceny, dowiedz się, jak wdrożyć urządzenie przy użyciu szablonu lub uruchom [samouczek migracji bez agenta](tutorial-migrate-vmware.md)i wdrożyć urządzenie z szablonem podczas samouczka. | Wdrażanie przy użyciu skryptu instalatora programu PowerShell. <br/><br/> [Przejrzyj](deploy-appliance-script.md) instrukcje dotyczące skryptów urządzenia. 




## <a name="next-steps"></a>Następne kroki

[Przejrzyj](migrate-appliance.md) wymagania dotyczące urządzeń.