---
title: Włączanie dysków udostępnionych dla dysków zarządzanych platformy Azure
description: Konfigurowanie dysku zarządzanego platformy Azure przy użyciu dysków udostępnionych (wersja zapoznawcza), tak aby można było go udostępniać na wielu maszynach wirtualnych
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970381"
---
# <a name="enable-shared-disk"></a>Włączanie dysku udostępnionego

W tym artykule opisano, jak włączyć funkcję dysków udostępnionych (wersja zapoznawcza) dla dysków zarządzanych platformy Azure. Dyski udostępnione platformy Azure (wersja zapoznawcza) to nowa funkcja dla dysków zarządzanych platformy Azure, która umożliwia jednoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych .Azure shared disks (preview) is a new feature for Azure managed disks that enables you to attach a managed disk to multiple virtual machines (VMs) simultaneously. Dołączanie dysku zarządzanego do wielu maszyn wirtualnych umożliwia wdrożenie nowych lub migrację istniejących aplikacji klastrowanych na platformę Azure. 

Jeśli szukasz informacji o pojęciach dotyczących dysków zarządzanych, na których włączono udostępnione dyski, zapoznaj się z [programem Azure shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]