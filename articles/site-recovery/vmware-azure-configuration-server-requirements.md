---
title: Wymagania dotyczące serwera konfiguracji dotyczące odzyskiwania po awarii oprogramowania VMware na platformę Azure przy użyciu Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano obsługę i wymagania dotyczące wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii oprogramowania VMware na platformie Azure przy użyciu Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: d83b99ea540d6232f4c0786d3a743f97332e1c9f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792321"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Wymagania dotyczące serwera konfiguracji dotyczące odzyskiwania po awarii oprogramowania VMware na platformę Azure

Lokalny serwer konfiguracji jest wdrażany w przypadku używania [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.

- Serwer konfiguracji koordynuje komunikację między lokalnym programem VMware i platformą Azure. Umożliwia również zarządzanie replikacją danych.
- [Dowiedz się więcej](vmware-azure-architecture.md) o składnikach i procesach serwera konfiguracji.

## <a name="configuration-server-deployment"></a>Wdrożenie serwera konfiguracji

W przypadku odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure należy wdrożyć serwer konfiguracji jako maszynę wirtualną VMware.

- Site Recovery udostępnia szablon komórki jajowe pobrany z Azure Portal i zaimportowania go do vCenter Server w celu skonfigurowania maszyny wirtualnej serwera konfiguracji.
- Podczas wdrażania serwera konfiguracji przy użyciu szablonu komórki jajowe, maszyna wirtualna automatycznie spełnia wymagania wymienione w tym artykule.
- Zdecydowanie zalecamy skonfigurowanie serwera konfiguracji przy użyciu szablonu komórki jajowe. Jeśli jednak konfigurujesz odzyskiwanie po awarii dla maszyn wirtualnych VMware i nie można użyć szablonu komórki jajowe, możesz wdrożyć serwer konfiguracji, korzystając z [poniższych instrukcji](physical-azure-set-up-source.md).
- Jeśli wdrażasz serwer konfiguracji na potrzeby odzyskiwania po awarii lokalnych maszyn fizycznych na platformie Azure, postępuj zgodnie z instrukcjami w [tym artykule](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Następne kroki
Skonfiguruj odzyskiwanie po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
