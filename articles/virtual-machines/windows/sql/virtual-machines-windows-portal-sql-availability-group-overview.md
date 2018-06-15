---
title: -Maszyn wirtualnych platformy Azure — omówienie grup dostępności serwera SQL | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono grup dostępności programu SQL Server na maszynach wirtualnych Azure.
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
ms.openlocfilehash: 4a531fb87d9cd2743138ba7a027bdf0d132b9747
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29396435"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych Azure #

W tym artykule przedstawiono grup dostępności programu SQL Server na maszynach wirtualnych platformy Azure. 

Zawsze włączone grupy dostępności na maszynach wirtualnych platformy Azure są podobne do zawsze włączonych grup dostępności lokalnie. Aby uzyskać więcej informacji, zobacz [zawsze włączonych grup dostępności (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Diagram przedstawia części pełną grupy dostępności serwera SQL w maszynach wirtualnych platformy Azure.

![Grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Najważniejsza różnica dla grupy dostępności w maszynach wirtualnych platformy Azure jest, że maszyn wirtualnych platformy Azure, wymaga [modułu równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md). Moduł równoważenia obciążenia zawiera adresy IP dla odbiornika grupy dostępności. Jeśli masz więcej niż jednej grupy dostępności w każdej grupie wymaga odbiornik. Jeden moduł równoważenia obciążenia może obsługiwać wiele odbiorników.

Gdy wszystko będzie gotowe do tworzenia aroup dostępności programu SQL Server na maszynach wirtualnych platformy Azure można znaleźć samouczki.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Automatycznie utworzyć grupy dostępności na podstawie szablonu

[Konfigurowanie zawsze włączonej grupy dostępności w maszynie Wirtualnej platformy Azure automatycznie - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Ręcznie utwórz grupę dostępności w portalu Azure

Można również utworzyć maszyny wirtualne samodzielnie bez szablonu. Najpierw spełnić wymagania wstępne, a następnie utworzyć grupy dostępności. Zobacz następujące tematy: 

- [Konfigurowanie wymagań wstępnych dla programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Utwórz zawsze włączonej grupy dostępności, aby zwiększyć dostępność i odzyskiwanie po awarii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie programu SQL Server, zawsze w grupie dostępności na maszynach wirtualnych Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md).
