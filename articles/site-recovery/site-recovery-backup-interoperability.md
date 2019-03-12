---
title: Usługa Azure Site Recovery — współdziałanie kopii zapasowej | Dokumentacja firmy Microsoft
description: Zawiera omówienie sposobu użycia usługi Azure Site Recovery i Azure Backup można ze sobą.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731870"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Usługa Site Recovery i kopii zapasowych współdziałanie — informacje

Ten artykuł zawiera wskazówki dotyczące pomyślnie za pomocą kopii zapasowych maszyn wirtualnych IaaS platformy Azure i maszyn wirtualnych platformy Azure odzyskiwanie po awarii.

## <a name="azure-backup"></a>Azure Backup

Usługa Azure Backup pomaga w ochronie danych dla lokalnych serwerów, maszyn wirtualnych, zwirtualizowanych obciążeń, serwerów SQL, serwerów programu SharePoint i więcej. Usługa Azure Site Recovery organizuje i zarządza odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, lokalnych maszyn wirtualnych i serwerów fizycznych.

## <a name="azure-site-recovery"></a>Azure Site Recovery

Istnieje możliwość konfigurowania usługi Azure Backup i Azure Site Recovery na maszynie Wirtualnej lub grupy maszyn wirtualnych. Obydwa te produkty są zgodne. Kilka scenariuszy, w którym współdziałanie usługi Azure Site Recovery i kopii zapasowych staje się ważne są następujące:

### <a name="file-backuprestore"></a>Plik kopii zapasowej/przywracania

Jeśli kopii zapasowej i replikacji są włączone i jest wykonywana kopia zapasowa, nie istnieje żaden problem z przywracaniem wszystkie pliki na maszynę Wirtualną po stronie źródła lub grupy maszyn wirtualnych. Replikacja będzie nadal w zwykły sposób nie wpływa na kondycji replikacji.

### <a name="disk-backuprestore"></a>Przywracania kopii zapasowej dysku

W przypadku przywracania z kopii zapasowej dysku ochrony maszyny wirtualnej została ponownie włączyć.

### <a name="vm-backuprestore"></a>Przywracanie kopii zapasowej maszyny Wirtualnej

Kopia zapasowa i przywracanie maszyn wirtualnych lub grupy maszyn wirtualnych nie jest obsługiwana. Aby umożliwić jej pracę, ochrony musi być ponownie włączyć.

**Scenariusz** | **Obsługiwane przez usługę Azure Site Recovery?** | **Obejście problemu, jeśli istnieje**  
--- | --- | ---
Kopia zapasowa plików/folderów | Yes | Nie dotyczy
Wykonywanie kopii zapasowej dysku | Nie jest obecnie | Wyłącz i włącz ochronę
Kopia zapasowa maszyny Wirtualnej | Nie | Wyłącz i włącz ochronę
