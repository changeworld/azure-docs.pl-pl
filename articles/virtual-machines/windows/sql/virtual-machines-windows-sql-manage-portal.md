---
title: Zarządzanie maszyny wirtualne SQL Server na platformie Azure przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostęp do zasobu maszyny wirtualnej SQL w witrynie Azure portal przez maszynę Wirtualną programu SQL Server hostowana na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59a85e855c9fab9f2a3437c83c867b8076f55049
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607224"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Zarządzanie maszyny wirtualne SQL Server na platformie Azure przy użyciu witryny Azure portal

Istnieje punkt dostępu do zarządzania maszyny Wirtualnej programu SQL Server na platformie Azure przy użyciu [witryny Azure portal](https://portal.azure.com). 

**Maszyn wirtualnych SQL** zasób to teraz usługi niezależne zarządzanie, która pozwala na wyświetlanie wszystkich maszyn wirtualnych programu SQL Server jednocześnie i modyfikowanie ustawień do programu SQL Server w wersji dedykowanej: 

![Maszyny wirtualne zasobu bazy danych SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Uwagi

- **Maszyn wirtualnych SQL** zasób jest zalecaną metodą do wyświetlania i zarządzania maszynami wirtualnymi z serwera SQL. Jednakże, obecnie **maszyn wirtualnych SQL** zasób nie obsługuje zarządzania [wsparcie (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) maszyny wirtualne SQL Server. Aby zarządzać ustawieniami dla maszyn wirtualnych EOS programu SQL Server, należy użyć przestarzałego [Karta Konfiguracja programu SQL Server](#access-sql-server-configuration-tab) zamiast tego. 
- **Maszyn wirtualnych SQL** zasobów jest dostępna tylko dla maszyn wirtualnych serwera SQL, które mają [zarejestrowanego dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-sql-virtual-machine-resource"></a>Dostęp do zasobu bazy danych maszyny wirtualnej SQL
Aby uzyskać dostęp do zasobów maszyn wirtualnych SQL, wykonaj następujące czynności:

1. Otwórz [portal Azure](https://portal.azure.com). 
1. Wybierz **wszystkich usług**. 
1. Typ `SQL virtual machines` w polu wyszukiwania.
1. (Opcjonalnie): Wybierz ikonę gwiazdki obok **maszyn wirtualnych SQL** można dodać tej opcji do menu Ulubione. 
1. Wybierz **maszyn wirtualnych SQL**. 

   ![Znajdowanie maszyn wirtualnych w wszystkie usługi maszyny Wirtualnej SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Spowoduje to wyświetlenie listy wszystkich maszynach wirtualnych serwera SQL dostępnych w ramach subskrypcji. Wybierz, czy chcesz zarządzać uruchomić **maszyn wirtualnych SQL** zasobów. Użyj pola wyszukiwania, jeśli maszyna wirtualna serwera SQL nie jest łatwe do rozwiązania. 

![Wszystkie dostępne maszyny wirtualne SQL](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

Wybranie maszyny Wirtualnej programu SQL Server spowoduje otwarcie **maszyn wirtualnych SQL** zasobów: 


![Maszyny wirtualne zasobu bazy danych SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > **Maszyn wirtualnych SQL** zasób jest dedykowany ustawień programu SQL Server. Wybierz nazwę maszyny Wirtualnej w **maszyny wirtualnej** pola, aby przejść do ustawień, które są specyficzne dla maszyny Wirtualnej, ale nie wyłącznie dla programu SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Karta Konfiguracja programu SQL Server dostęp
Na karcie Konfiguracja programu SQL Server jest przestarzała. W tej chwili jest jedyną metodą, aby zarządzać [wsparcie (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) maszyny wirtualne SQL Server oraz maszyny wirtualne SQL Server, które nie zostały jeszcze [zarejestrowanego dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Dostępu przestarzałe karta konfiguracji programu SQL server, musisz przejść do **maszyn wirtualnych** zasobów. Aby to zrobić, wykonaj następujące czynności:

1. Otwórz [portal Azure](https://portal.azure.com). 
1. Wybierz **wszystkich usług**. 
1. Typ `virtual machines` w polu wyszukiwania.
1. (Opcjonalnie): Wybierz ikonę gwiazdki obok **maszyn wirtualnych** można dodać tej opcji do menu Ulubione. 
1. Wybierz **maszyn wirtualnych**. 

   ![Wyszukaj maszyn wirtualnych](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Spowoduje to wyświetlenie listy wszystkich maszyn wirtualnych w subskrypcji. Wybierz, czy chcesz zarządzać uruchomić **maszyny wirtualnej** zasobów. Użyj pola wyszukiwania, jeśli maszyna wirtualna serwera SQL nie jest łatwe do rozwiązania. 
1. Wybierz **konfiguracji programu SQL Server** w **ustawienia** okienko, aby zarządzać serwerem SQL. 

![Konfiguracja programu SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na maszynie Wirtualnej Windows wskazówki dotyczące cen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


