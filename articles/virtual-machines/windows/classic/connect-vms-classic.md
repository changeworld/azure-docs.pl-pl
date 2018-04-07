---
title: Połączenie maszyn wirtualnych systemu Windows w usłudze w chmurze | Dokumentacja firmy Microsoft
description: Połączenie maszyn wirtualnych systemu Windows utworzone z klasycznym modelu wdrażania usługi w chmurze Azure lub sieci wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: b5778336b2dfb3d65cb678c96525ec22da1634a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Połączenie maszyn wirtualnych systemu Windows utworzone z klasycznym modelu wdrażania z wirtualnych sieci lub usługę w chmurze
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Maszyny wirtualne systemu Windows utworzone z klasycznym modelu wdrażania są zawsze umieszczane w usłudze w chmurze. Usługi w chmurze pełni funkcję kontenera i zapewnia unikatową publicznej nazwy DNS, publiczny adres IP i zestaw punktów końcowych na dostęp do maszyny wirtualnej za pośrednictwem Internetu. Usługi w chmurze może być w sieci wirtualnej, ale nie jest wymagane.

Jeśli usługa w chmurze nie jest w sieci wirtualnej, jest nazywany *autonomiczny* usługi w chmurze. Maszyny wirtualne w usłudze w chmurze autonomiczny komunikować się z innych maszyn wirtualnych przy użyciu innych maszyn wirtualnych publicznej nazwy DNS, a ruch przechodzi przez Internet. Jeśli usługa w chmurze jest w sieci wirtualnej maszyn wirtualnych w tej usłudze w chmurze może komunikować się z innych maszyn wirtualnych w sieci wirtualnej bez wysyłania cały ruch w Internecie.

Jeśli w tej samej usłudze w chmurze autonomicznych maszyn wirtualnych, nadal używać zestawy dostępności i równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [maszyn wirtualnych równoważenia obciążenia](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [Zarządzaj dostępnością maszyn wirtualnych](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Nie można jednak zorganizować maszyn wirtualnych w podsieciach lub połączyć autonomiczna usługa chmury do sieci lokalnej. Oto przykład:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu maszyny wirtualnej jest dobrym pomysłem jest [Dodaj dysk danych](attach-disk.md) więc usług i obciążeń lokalizację do przechowywania danych.
