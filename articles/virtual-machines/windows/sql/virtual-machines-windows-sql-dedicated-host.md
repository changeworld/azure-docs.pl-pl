---
title: SQL Server maszynę wirtualną na dedykowanym hoście platformy Azure
description: Więcej informacji na temat uruchamiania SQL Server maszyny wirtualnej na dedykowanym hoście platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8b29bbce1511b022def522d46c74b99967a76ea3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204525"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server maszynę wirtualną na dedykowanym hoście platformy Azure 

W tym artykule przedstawiono szczegółowe informacje dotyczące używania maszyny wirtualnej SQL Server z [dedykowanym hostem platformy Azure](/azure/virtual-machines/windows/dedicated-hosts). Dodatkowe informacje na temat dedykowanego hosta platformy Azure można znaleźć w wpisie w blogu [wprowadzającym dedykowany Host platformy Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Przegląd
[Dedykowany Host platformy Azure](/azure/virtual-machines/windows/dedicated-hosts) to usługa, która zapewnia serwerom fizycznym możliwość hostowania co najmniej jednej maszyny wirtualnej — dedykowanej dla jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne, które są używane w centrach danych firmy Microsoft, dostępne jako zasoby. Można udostępnić dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio na hostach, w których konfiguracja najlepiej odpowiada Twoim potrzebom.


[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]


## <a name="licensing"></a>Licencjonowanie

Podczas dodawania maszyny wirtualnej SQL Server do dedykowanego hosta platformy Azure można wybrać jedną z dwóch różnych opcji licencjonowania. 

  - **Licencjonowanie maszyn wirtualnych SQL**: Jest to istniejąca opcja licencjonowania, w przypadku której płacisz osobno za każdą licencję na SQL Server maszyny wirtualnej. 
  - **Dedykowane Licencjonowanie hosta**: Nowy model licencjonowania dostępny dla dedykowanego hosta platformy Azure, w którym SQL Server licencje są powiązane i płatne na poziomie hosta. 


Opcje na poziomie hosta dotyczące korzystania z istniejących licencji SQL Server: 
  - Korzyść użycia hybrydowego platformy Azure SQL Server Enterprise Edition
    - Dostępne dla klientów z pakietem SA lub subskrypcją.
    - Licencjonowanie wszystkich dostępnych rdzeni fizycznych i korzystanie z nieograniczonej wirtualizacji (maksymalnie procesorów wirtualnych vCPU obsługiwane przez hosta).
        - Aby uzyskać więcej informacji na temat stosowania Korzyść użycia hybrydowego platformy Azure do dedykowanego hosta platformy Azure, zapoznaj się z [korzyść użycia hybrydowego platformy Azure często zadawanych pytań](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licencje SQL Server nabyte przed 1 października
      - Wersja SQL Server Enterprise ma opcje licencji na poziomie hosta i przez maszynę wirtualną. 
      - Wersja SQL Server Standard ma tylko opcję licencji na maszynę wirtualną. 
          - Aby uzyskać szczegółowe informacje, zobacz [warunki produktu firmy Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Jeśli nie wybrano SQL Server dedykowanej opcji na poziomie hosta, wówczas SQL Server AHB można wybrać na poziomie poszczególnych maszyn wirtualnych, podobnie jak w przypadku maszyn wirtualnych z wieloma dzierżawcami.



## <a name="provisioning"></a>Inicjowanie obsługi administracyjnej  
Inicjowanie obsługi maszyny wirtualnej SQL Server na dedykowanym hoście nie różni się od żadnej innej maszyny wirtualnej platformy Azure. Można to zrobić za pomocą [Azure PowerShell](../dedicated-hosts-powershell.md), [Azure Portal](../dedicated-hosts-portal.md)i [interfejsu wiersza polecenia platformy Azure](../../linux/dedicated-hosts-cli.md).

Proces dodawania istniejącej maszyny wirtualnej SQL Server do dedykowanego hosta wymaga przestoju, ale nie wpłynie na dane i nie będzie miał utraty danych. Niemniej jednak przed przeniesieniem należy utworzyć kopię zapasową wszystkich baz danych, w tym systemowych baz danych.

## <a name="virtualization"></a>Wirtualizacja 

Jedną z zalet dedykowanego hosta jest nieograniczona wirtualizacja. Na przykład możesz mieć licencje na 64 rdzeni wirtualnych, ale można skonfigurować hosta tak, aby miał 128 rdzeni wirtualnych, więc otrzymujesz podwójny rdzeni wirtualnych, ale płacisz tylko za połowę licencji na SQL Server. 

Ponieważ jest to Twój host, kwalifikujesz się do ustawienia wirtualizacji o współczynniku 1:2. 

## <a name="faq"></a>Często zadawane pytania

**Pyt.: Jak działa Korzyść użycia hybrydowego platformy Azure dla licencji systemu Windows Server/SQL Server na dedykowanym hoście platformy Azure?**

Odp.: Klienci mogą używać wartości swoich istniejących licencji systemu Windows Server i SQL Server z pakietem Software Assurance lub uprawniających licencji subskrypcyjnych, aby uregulować ograniczoną stawkę za dedykowany Host platformy Azure przy użyciu Korzyść użycia hybrydowego platformy Azure. Klienci korzystający z systemu Windows Server Datacenter i SQL Server Enterprise Edition uzyskują nieograniczoną wirtualizację (w zależności od liczby maszyn wirtualnych z systemem Windows Server można wdrożyć na hoście z uwzględnieniem fizycznej pojemności serwera bazowego), gdy uzyskują licencję na cały Host i użyj Korzyść użycia hybrydowego platformy Azure.  Wszystkie obciążenia systemu Windows Server i SQL Server na dedykowanym hoście platformy Azure są również uprawnione do rozszerzonych aktualizacji zabezpieczeń dla systemów Windows Server i SQL Server 2008/R2 bez dodatkowych opłat. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


