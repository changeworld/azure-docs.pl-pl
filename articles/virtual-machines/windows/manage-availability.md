---
title: Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure
description: Dowiedz się, jak korzystać z wielu maszyn wirtualnych w celu zapewnienia wysokiej dostępności aplikacji systemu Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f9914b84b63f271c7dd7d1b8f7dbc3b69511605
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561185"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure 

Dowiedz się, jak skonfigurować wiele maszyn wirtualnych i zarządzać nimi, aby zapewnić wysoką dostępność aplikacji systemu Windows na platformie Azure. Można także [zarządzać dostępnością maszyn wirtualnych z systemem Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać instrukcje dotyczące tworzenia i używania zestawów dostępności w przypadku korzystania z klasycznego modelu wdrażania, zobacz [jak skonfigurować zestaw dostępności](classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat równoważenia obciążenia maszyn wirtualnych, zobacz [równoważenie obciążenia maszyn wirtualnych](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Wyświetlanie architektur referencyjnych na potrzeby uruchamiania aplikacji N-warstwowych na SQL Server w IaaS

* [Aplikacja N-warstwowa systemu Windows na platformie Azure z SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Uruchamianie aplikacji N-warstwowej w wielu regionach platformy Azure w celu zapewnienia wysokiej dostępności](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
