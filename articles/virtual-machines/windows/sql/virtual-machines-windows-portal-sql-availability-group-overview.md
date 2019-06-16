---
title: -Maszyn wirtualnych platformy Azure — omówienie grup dostępności serwera SQL | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
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
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325825"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych platformy Azure #

W tym artykule przedstawiono grup dostępności programu SQL Server na maszynach wirtualnych platformy Azure. 

Zawsze włączone grupy dostępności na maszynach wirtualnych platformy Azure są podobne do zawsze włączonych grup dostępności w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [zawsze włączonych grup dostępności (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Na diagramie przedstawiono części pełną grupy dostępności SQL Server na maszynach wirtualnych platformy Azure.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Główną różnicą grupy dostępności na maszynach wirtualnych platformy jest, że maszyn wirtualnych platformy Azure wymaga [moduł równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md). Moduł równoważenia obciążenia zawiera adresy IP dla odbiornika grupy dostępności. Każda grupa wymaga odbiornik, jeśli masz więcej niż jednej grupy dostępności. Jeden moduł równoważenia obciążenia może obsługiwać wiele odbiorników.

Ponadto w klastrze trybu failover gościa maszyny Wirtualnej IaaS platformy Azure, zalecamy jedną kartą Sieciową na serwer (węzeł klastra) i pojedynczej podsieci. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci są niepotrzebne w klastrze gościa maszyny wirtualnej IaaS platformy Azure. Mimo że raport z weryfikacji klastra wyświetli ostrzeżenie, że węzły są dostępne tylko w ramach jednej sieci, to ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej IaaS platformy Azure. 

|  | Wersja systemu Windows Server | Wersja programu SQL Server | Wersja programu SQL Server | Konfiguracja kworum WSFC | Odzyskiwanie po awarii przy użyciu wielu regionów | Obsługa wielu podsieci | Obsługa istniejącej AD | Odzyskiwanie po awarii przy użyciu tego samego regionu w wielu strefach | Obsługa dist AG nie ma domeny usługi AD | Obsługa dist AG z żadnego klastra |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [INTERFEJS WIERSZA POLECENIA SQL MASZYNY WIRTUALNEJ](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Monitor w chmurze | Nie | Yes | Yes | Yes | Nie | Nie |
| [Szablony szybkiego startu](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Monitor w chmurze | Nie | Yes | Yes | Yes | Nie | Nie |
| [Szablon portalu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Udział plików | Nie | Nie | Nie | Nie | Nie | Nie |
| [Ręcznie](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Wszyscy | Wszyscy | Wszyscy | Wszyscy | Yes | Yes | Yes | Yes | Yes | Yes |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Gdy wszystko jest gotowe do tworzenia grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure, odnoszą się do tych samouczków.

## <a name="manually-with-azure-cli"></a>Ręcznie przy użyciu wiersza polecenia platformy Azure
Przy użyciu wiersza polecenia platformy Azure pozwalają skonfigurować i wdrożyć do grupy dostępności jest to zalecana opcja, ponieważ stanowi on pod względem prostotą i szybkością wdrożenia. Za pomocą wiersza polecenia platformy Azure, utworzenie klastra pracy awaryjnej Windows, przyłączanie maszyn wirtualnych programu SQL Server w klastrze, a także tworzenie odbiornika i wewnętrznego modułu równoważenia obciążenia wszystkie można osiągnąć w mniej niż 30 minut. Ta opcja nadal wymaga ręcznego utworzenia grupy dostępności, ale automatyzuje wszystkie inne wymagane kroki konfiguracji. 

Aby uzyskać więcej informacji, zobacz [Użyj SQL VM wiersza polecenia platformy Azure na konfigurowanie zawsze włączonej grupy dostępności programu SQL Server na Maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatycznie przy użyciu szablonów szybkiego startu platformy Azure
Korzystanie z szablonów szybkiego startu platformy Azure dostawca zasobów maszyny Wirtualnej SQL do wdrożenia klastra pracy awaryjnej Windows, przyłączyć się do niej maszyny wirtualne SQL Server, utwórz odbiornik i konfigurowanie wewnętrznego modułu równoważenia obciążenia. Ta opcja nadal wymaga ręcznego utworzenia grupy dostępności i wewnętrznego modułu równoważenia obciążenia (ILB), ale pozwala zautomatyzować i upraszcza wszystkie inne wymagane kroki konfiguracji (w tym konfigurację wewnętrznego modułu równoważenia obciążenia). 

Aby uzyskać więcej informacji, zobacz [szablon szybkiego startu platformy Azure Użyj Konfigurowanie zawsze włączonej grupy dostępności dla programu SQL Server na Maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatycznie przy użyciu szablonu portalu platformy Azure

[Konfigurowanie zawsze włączonej grupy dostępności na maszynie Wirtualnej platformy Azure automatycznie — Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Ręcznie w witrynie Azure portal

Można również tworzyć maszyny wirtualne samodzielnie bez szablonu. Najpierw należy spełnić wymagania wstępne, a następnie utworzyć grupy dostępności. Zobacz następujące tematy: 

- [Skonfiguruj wymagania wstępne dotyczące programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Utwórz zawsze włączoną grupę dostępności do zwiększenia dostępności i odzyskiwania po awarii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie programu SQL Server zawsze włączona grupa dostępności na maszynach wirtualnych platformy Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md)
