---
title: Zarządzanie maszynami wirtualnymi programu SQL Server na platformie Azure przy użyciu witryny Azure Portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp do zasobu maszyny wirtualnej SQL w portalu Azure dla maszyny wirtualnej programu SQL Server hostowanego na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243213"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Zarządzanie maszynami wirtualnymi programu SQL Server na platformie Azure przy użyciu portalu Azure

W [portalu Azure](https://portal.azure.com)zasób **maszyn wirtualnych SQL** jest niezależną usługą zarządzania. Można go używać do wyświetlania wszystkich maszyn wirtualnych programu SQL Server jednocześnie i modyfikowania ustawień dedykowanych programowi SQL Server: 

![Zasób maszyn wirtualnych SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Uwagi

- Zaleca się używanie zasobu **maszyn wirtualnych SQL** do wyświetlania maszyn wirtualnych programu SQL Server i zarządzania nimi na platformie Azure. Ale obecnie **zasób maszyn wirtualnych SQL** nie obsługuje zarządzania [maszynami wirtualnymi](virtual-machines-windows-sql-server-2008-eos-extend-support.md) programu SQL Server po zakończeniu tej usługi. Aby zarządzać ustawieniami maszyn wirtualnych programu SQL Server na końcu, należy użyć przestarzałej [karty konfiguracji programu SQL Server.](#access-the-sql-server-configuration-tab) 
- **Zasób maszyn wirtualnych SQL** jest dostępny tylko dla maszyn wirtualnych programu SQL Server, które [zarejestrowały się u dostawcy zasobów maszyny wirtualnej SQL.](virtual-machines-windows-sql-register-with-resource-provider.md) 


## <a name="access-the-sql-virtual-machines-resource"></a>Dostęp do zasobu maszyn wirtualnych SQL
Aby uzyskać dostęp do zasobu **maszyn wirtualnych SQL,** wykonaj następujące czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). 
1. Wybierz **pozycję Wszystkie usługi**. 
1. Wprowadź **maszyny wirtualne SQL** w polu wyszukiwania.
1. (Opcjonalnie): Wybierz gwiazdkę obok **maszyn wirtualnych SQL,** aby dodać tę opcję do menu **Ulubione.** 
1. Wybierz **maszyny wirtualne SQL**. 

   ![Znajdowanie maszyn wirtualnych programu SQL Server we wszystkich usługach](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Portal zawiera listę wszystkich maszyn wirtualnych programu SQL Server dostępnych w ramach subskrypcji. Wybierz ten, który ma zostać otwarty zasób **maszyn wirtualnych SQL.** Użyj pola wyszukiwania, jeśli maszyna wirtualna programu SQL Server nie jest wyświetlana. 

   ![Wszystkie dostępne maszyny wirtualne programu SQL Server](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Wybranie maszyny wirtualnej programu SQL Server powoduje otwarcie zasobu **maszyn wirtualnych SQL:** 


   ![Zasób maszyn wirtualnych SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **Zasób maszyn wirtualnych SQL** jest przeznaczony dla dedykowanych ustawień programu SQL Server. Wybierz nazwę maszyny wirtualnej w polu **maszyny wirtualnej,** aby otworzyć ustawienia, które są specyficzne dla maszyny wirtualnej, ale nie wyłączne dla programu SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Dostęp do karty konfiguracji programu SQL Server
Karta **konfiguracji programu SQL Server** została przestarzała. Obecnie jest to jedyna metoda zarządzania [maszynami](virtual-machines-windows-sql-server-2008-eos-extend-support.md) wirtualnymi programu SQL Server i maszynami wirtualnymi programu SQL Server, które nie zostały [zarejestrowane u dostawcy zasobów maszyny wirtualnej SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)

Aby uzyskać dostęp do przestarzałej karty **konfiguracji programu SQL Server,** przejdź do zasobu **Maszyny wirtualne.** Wykonaj następujące czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). 
1. Wybierz **pozycję Wszystkie usługi**. 
1. Wprowadź **maszyny wirtualne** w polu wyszukiwania.
1. (Opcjonalnie): Wybierz gwiazdkę obok **maszyny wirtualne,** aby dodać tę opcję do menu **Ulubione.** 
1. Wybierz pozycję **Maszyny wirtualne**. 

   ![Wyszukiwanie maszyn wirtualnych](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Portal wyświetla listę wszystkich maszyn wirtualnych w ramach subskrypcji. Wybierz ten, który ma zostać otwarty zasób **maszyny wirtualne.** Użyj pola wyszukiwania, jeśli maszyna wirtualna programu SQL Server nie jest wyświetlana. 
1. Wybierz **konfigurację programu SQL Server** w okienku **Ustawienia,** aby zarządzać maszyną wirtualną programu SQL Server. 

   ![Konfiguracja programu SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


