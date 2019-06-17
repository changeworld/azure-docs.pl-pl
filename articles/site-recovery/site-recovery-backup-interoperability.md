---
title: Wsparcie dla korzystania z usługi Azure Site Recovery przy użyciu usługi Azure Backup | Dokumentacja firmy Microsoft
description: Zawiera omówienie sposobu użycia usługi Azure Site Recovery i Azure Backup można ze sobą.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035779"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Obsługa użycia Site Recovery przy użyciu usługi Azure Backup

Ten artykuł zawiera podsumowanie obsługi przy użyciu [usługi Site Recovery](site-recovery-overview.md) wraz z [usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Akcja** | **Obsługa odzyskiwania lokacji** | **Szczegóły**
--- | --- | ---
**Wdrażanie usług ze sobą** | Obsługiwane | Usługi mogą współdziałać z i konfigurować je ze sobą.
**Plik kopii zapasowej/przywracania** | Obsługiwane | Podczas tworzenia kopii zapasowej i replikacji są włączone dla maszyny Wirtualnej i kopie zapasowe są wykonywane, nie problemu w występuje Przywracanie plików na po stronie źródła maszyn wirtualnych lub grupy maszyn wirtualnych. Replikacja jest kontynuowana w zwykły sposób nie wpływa na kondycji replikacji.
**Przywracania kopii zapasowej dysku** | Brak bieżącej obsługi | W przypadku przywracania kopii zapasowej dysku, należy wyłączyć i ponownie włączyć replikację dla maszyny Wirtualnej ponownie.
**Przywracanie kopii zapasowej maszyny Wirtualnej** | Brak bieżącej obsługi | Jeśli tworzenie kopii zapasowej lub przywracanie maszyny Wirtualnej lub grupy maszyn wirtualnych, należy wyłączyć i ponownie włączyć replikację dla maszyny Wirtualnej.  


