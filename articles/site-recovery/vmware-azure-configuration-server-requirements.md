---
title: Wymagania serwera konfiguracji odzyskiwania po awarii VMware w usłudze Azure Site Recovery
description: W tym artykule opisano obsługę techniczną i wymagania dotyczące wdrażania serwera konfiguracji odzyskiwania po awarii VMware na platformie Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257409"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Wymagania serwera konfiguracji odzyskiwania po awarii VMware na platformie Azure

Wdrażasz lokalny serwer konfiguracji podczas korzystania z [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure.

- Serwer konfiguracji koordynuje komunikację między lokalnymi urządzeniami VMware i platformą Azure. Zarządza również replikacją danych.
- [Dowiedz się więcej](vmware-azure-architecture.md) o składnikach i procesach serwera konfiguracji.

## <a name="configuration-server-deployment"></a>Wdrożenie serwera konfiguracji

W przypadku odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure należy wdrożyć serwer konfiguracji jako maszynę wirtualną VMware.

- Usługa Site Recovery udostępnia szablon ova pobrany z portalu Azure i zaimportowany do serwera vCenter Server w celu skonfigurowania maszyny Wirtualnej serwera konfiguracji.
- Podczas wdrażania serwera konfiguracji przy użyciu szablonu ova maszyna wirtualna automatycznie spełnia wymagania wymienione w tym artykule.
- Zdecydowanie zaleca się skonfigurowanie serwera konfiguracji przy użyciu szablonu ova. Jeśli jednak konfigurujesz odzyskiwanie po awarii dla maszyn wirtualnych VMware i nie możesz użyć szablonu OVA, możesz wdrożyć serwer konfiguracji, korzystając z [tych instrukcji.](physical-azure-set-up-source.md)
- Jeśli wdrażasz serwer konfiguracji do odzyskiwania po awarii lokalnych komputerów fizycznych na platformie Azure, postępuj zgodnie z instrukcjami w [tym artykule](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Następne kroki
Konfigurowanie odzyskiwania po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
