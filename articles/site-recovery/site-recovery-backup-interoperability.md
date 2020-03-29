---
title: Obsługa korzystania z usługi Azure Site Recovery za pomocą usługi Azure Backup
description: Zawiera omówienie sposobu, w jaki usługa Azure Site Recovery i Azure Backup mogą być używane razem.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376213"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Obsługa korzystania z usługi Site Recovery za pomocą usługi Azure Backup

W tym artykule podsumowano obsługę korzystania z [usługi site recovery](site-recovery-overview.md) wraz z [usługą Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Akcja** | **Obsługa usługi Site Recovery** | **Szczegóły**
--- | --- | ---
**Wdrażanie usług razem** | Obsługiwane | Usługi są interoperacyjne i można je skonfigurować razem.
**Kopia zapasowa/przywracanie plików** | Obsługiwane | Gdy kopia zapasowa i replikacja są włączone dla maszyny Wirtualnej i kopie zapasowe są pobierane, nie ma problemu z przywracaniem plików na maszynach wirtualnych po stronie źródłowej lub grupy maszyn wirtualnych. Replikacja jest kontynuowana w zwykły sposób, bez zmian w kondycji replikacji.
**Przywracanie dysku** | Brak bieżącej obsługi | Jeśli zostanie przywróceniu kopii zapasowej dysku, należy ponownie wyłączyć i ponownie włączyć replikację maszyny Wirtualnej.
**Przywracanie maszyny wirtualnej** | Brak bieżącej obsługi | W przypadku przywracania maszyny Wirtualnej lub grupy maszyn wirtualnych należy wyłączyć i ponownie włączyć replikację maszyny Wirtualnej.  


