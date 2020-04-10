---
title: Włączanie dysków udostępnionych dla dysków zarządzanych platformy Azure
description: Konfigurowanie dysku zarządzanego platformy Azure przy użyciu dysków udostępnionych (wersja zapoznawcza), tak aby można było go udostępniać na wielu maszynach wirtualnych
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0dbb1844d4c670abfdc5562580b0ee8b4549b6bd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008357"
---
# <a name="enable-shared-disk"></a>Włączanie dysku udostępnionego

W tym artykule opisano, jak włączyć funkcję dysków udostępnionych (wersja zapoznawcza) dla dysków zarządzanych platformy Azure. Dyski udostępnione platformy Azure (wersja zapoznawcza) to nowa funkcja dla dysków zarządzanych platformy Azure, która umożliwia jednoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych .Azure shared disks (preview) is a new feature for Azure managed disks that enables you to attach a managed disk to multiple virtual machines (VMs) simultaneously. Dołączanie dysku zarządzanego do wielu maszyn wirtualnych umożliwia wdrożenie nowych lub migrację istniejących aplikacji klastrowanych na platformę Azure. 

Jeśli szukasz informacji o pojęciach dotyczących dysków zarządzanych, na których włączono udostępnione dyski, zapoznaj się z [programem Azure shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]