---
title: Włącz dyski udostępnione dla usługi Azure Managed disks
description: Skonfiguruj dysk zarządzany na platformie Azure z udostępnionymi dyskami (wersja zapoznawcza), aby umożliwić udostępnianie go na wielu maszynach wirtualnych
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471707"
---
# <a name="enable-shared-disk"></a>Włącz dysk udostępniony

W tym artykule opisano sposób włączania funkcji dysków udostępnionych (wersja zapoznawcza) dla usługi Azure Managed Disks. Azure Shared disks (wersja zapoznawcza) to nowa funkcja dysków zarządzanych przez platformę Azure, która umożliwia równoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych. Dołączenie dysku zarządzanego do wielu maszyn wirtualnych pozwala wdrożyć nowe lub migrować istniejące aplikacje klastrowane na platformę Azure. 

Jeśli szukasz informacji koncepcyjnych dotyczących dysków zarządzanych, które mają włączone dyski udostępnione, zapoznaj się z [usługą Azure Shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]