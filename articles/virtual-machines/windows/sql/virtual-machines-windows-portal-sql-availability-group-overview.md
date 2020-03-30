---
title: Omówienie grup dostępności
description: W tym artykule przedstawiono grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037502"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Wprowadzenie grup dostępności programu SQL Server na maszynach wirtualnych platformy Azure

W tym artykule przedstawiono grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure. 

Grupy dostępności Always On na maszynach wirtualnych platformy Azure są podobne do grup dostępności always on w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [Zawsze włączone grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagram ilustruje części pełnej grupy dostępności programu SQL Server w maszynach wirtualnych platformy Azure.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Kluczową różnicą dla grupy dostępności w maszynach wirtualnych platformy Azure jest to, że maszyny wirtualne platformy Azure wymagają [modułu równoważenia obciążenia.](../../../load-balancer/load-balancer-overview.md) Moduł równoważenia obciążenia przechowuje adresy IP dla odbiornika grupy dostępności. Jeśli masz więcej niż jedną grupę dostępności, każda grupa wymaga odbiornika. Jeden moduł równoważenia obciążenia może obsługiwać wielu odbiorników.

Ponadto w klastrze trybu failover gościa usługi Azure IaaS dla gości zalecamy pojedynczą kartę sieciową na serwer (węzeł klastra) i pojedynczą podsieć. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci są niepotrzebne w klastrze gościa maszyny wirtualnej IaaS platformy Azure. Mimo że raport z weryfikacji klastra wyświetli ostrzeżenie, że węzły są dostępne tylko w ramach jednej sieci, to ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej IaaS platformy Azure. 

Aby zwiększyć nadmiarowość i wysoką dostępność, maszyny wirtualne programu SQL Server powinny znajdować się w tych samych [strefach dostępności](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)lub w różnych [strefach dostępności.](/azure/availability-zones/az-overview) 

|  | Wersja systemu Windows Server | Wersja programu SQL Server | SQL Server Edition | WSFC Kworum Config | Dr z wieloregionem | Obsługa wielu podsieci | Obsługa istniejącej usługi AD | Dr z wielostrefowym tym samym regionem | Obsługa Dist-AG bez domeny usługi AD | Obsługa Dist-AG bez klastra |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [Interfejsu wiersza polecenia maszyny Wirtualnej SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Monitor w chmurze | Nie | Tak | Tak | Tak | Nie | Nie |
| [Szablony szybkiego startu](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Monitor w chmurze | Nie | Tak | Tak | Tak | Nie | Nie |
| [Szablon portalu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Udział plików | Nie | Nie | Nie | Nie | Nie | Nie |
| [Ręcznie](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Wszystkie | Wszystkie | Wszystkie | Wszystkie | Tak | Tak | Tak | Tak | Tak | Tak |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Gdy wszystko będzie gotowe do utworzenia grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure, zapoznaj się z tymi samouczkami.

## <a name="manually-with-azure-cli"></a>Ręcznie z platformą Azure CLI
Za pomocą interfejsu wiersza polecenia platformy Azure do konfigurowania i wdrażania grupy dostępności jest zalecana opcja, ponieważ jest to najlepsze pod względem prostoty i szybkości wdrażania. Za pomocą narzędzia Azure CLI tworzenie klastra trybu failover systemu Windows, łączenie maszyn wirtualnych programu SQL Server z klastrem, a także tworzenie odbiornika i modułu wewnętrznego równoważenia obciążenia można osiągnąć w mniej niż 30 minut. Ta opcja nadal wymaga ręcznego utworzenia grupy dostępności, ale automatyzuje wszystkie inne niezbędne kroki konfiguracji. 

Aby uzyskać więcej informacji, zobacz Używanie interfejsu wiersza polecenia platformy [Azure SQL VM, aby skonfigurować grupę dostępności zawsze włączone dla programu SQL Server na maszynie Wirtualnej platformy Azure.](virtual-machines-windows-sql-availability-group-cli.md) 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatyczne z szablonami szybki start platformy Azure
Szablony szybki start platformy Azure wykorzystują dostawcę zasobów maszyny Wirtualnej SQL do wdrażania klastra trybu failover systemu Windows, dołączania do niego maszyn wirtualnych programu SQL Server, tworzenia odbiornika i konfigurowania wewnętrznego modułu równoważenia obciążenia. Ta opcja nadal wymaga ręcznego utworzenia grupy dostępności i wewnętrznego modułu równoważenia obciążenia (ILB), ale automatyzuje i upraszcza wszystkie inne niezbędne kroki konfiguracji (w tym konfigurację modułu równoważenia obciążenia). 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy dostępności zawsze włączone dla programu SQL Server na maszynie wirtualnej platformy Azure za pomocą szablonu szybkiego startu platformy Azure.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="automatically-with-an-azure-portal-template"></a>Automatycznie z szablonem portalu platformy Azure

[Automatycznie konfiguruj grupę dostępności zawsze włączone w usłudze Azure VM — Menedżer zasobów](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Ręcznie w witrynie Azure portal

Maszyny wirtualne można również utworzyć samodzielnie bez szablonu. Najpierw wykonaj wymagania wstępne, a następnie utwórz grupę dostępności. Zobacz następujące tematy: 

- [Konfigurowanie wymagań wstępnych dla grup dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Utwórz grupę dostępności zawsze włączone, aby poprawić dostępność i odzyskiwanie po awarii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie grupy dostępności programu SQL Server always on na maszynach wirtualnych platformy Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md)
