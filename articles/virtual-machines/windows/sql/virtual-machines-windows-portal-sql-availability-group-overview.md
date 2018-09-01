---
title: -Maszyn wirtualnych platformy Azure — omówienie grup dostępności serwera SQL | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b71ba12f6c533d67b04366f05b9334e1993823fa
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382032"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych platformy Azure #

W tym artykule przedstawiono grup dostępności programu SQL Server na maszynach wirtualnych platformy Azure. 

Zawsze włączone grupy dostępności na maszynach wirtualnych platformy Azure są podobne do zawsze włączonych grup dostępności w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [zawsze włączonych grup dostępności (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Na diagramie przedstawiono części pełną grupy dostępności SQL Server na maszynach wirtualnych platformy Azure.

![Grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Główną różnicą grupy dostępności na maszynach wirtualnych platformy jest, że maszyn wirtualnych platformy Azure wymaga [moduł równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md). Moduł równoważenia obciążenia zawiera adresy IP dla odbiornika grupy dostępności. Każda grupa wymaga odbiornik, jeśli masz więcej niż jednej grupy dostępności. Jeden moduł równoważenia obciążenia może obsługiwać wiele odbiorników.

Gdy wszystko jest gotowe do tworzenia grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure, odnoszą się do tych samouczków.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Automatyczne tworzenie grupy dostępności na podstawie szablonu

[Konfigurowanie zawsze włączonej grupy dostępności na maszynie Wirtualnej platformy Azure automatycznie — Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Ręcznie utworzyć grupy dostępności w witrynie Azure portal

Można również tworzyć maszyny wirtualne samodzielnie bez szablonu. Najpierw należy spełnić wymagania wstępne, a następnie utworzyć grupy dostępności. Zobacz następujące tematy: 

- [Skonfiguruj wymagania wstępne dotyczące programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Utwórz zawsze włączoną grupę dostępności do zwiększenia dostępności i odzyskiwania po awarii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie programu SQL Server zawsze włączona grupa dostępności na maszynach wirtualnych platformy Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md).
