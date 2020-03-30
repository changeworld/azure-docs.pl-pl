---
title: Maszyna wirtualna programu SQL Server na dedykowanym hoście platformy Azure
description: Dowiedz się więcej o szczegółach dotyczących uruchamiania maszyny Wirtualnej programu SQL Server na dedykowanym hoście platformy Azure.
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
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834350"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Maszyna wirtualna programu SQL Server na dedykowanym hoście platformy Azure 

W tym artykule opisano szczegóły dotyczące używania maszyny Wirtualnej programu SQL Server z [dedykowanym hostem platformy Azure.](/azure/virtual-machines/windows/dedicated-hosts) Dodatkowe informacje na temat dedykowanego hosta platformy Azure można znaleźć we wpisie w blogu [Wprowadzenie do usługi Azure Dedicated Host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Omówienie
[Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) to usługa, która zapewnia serwery fizyczne — mogą obsługiwać jedną lub więcej maszyn wirtualnych — dedykowane jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne używane w centrach danych firmy Microsoft, dostarczane jako zasób. Można aprowizować dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio do hostów aprowizowanych, niezależnie od konfiguracji najlepiej spełnia twoje potrzeby.

## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyny wirtualnej nie są obecnie obsługiwane na dedykowanych hostach.
- Obsługiwane są następujące serie maszyn wirtualnych: DSv3 i ESv3. 

## <a name="licensing"></a>Licencjonowanie

Podczas dodawania maszyny Wirtualnej programu SQL Server do dedykowanego hosta platformy Azure można wybrać jedną z dwóch różnych opcji licencjonowania. 

  - **Licencjonowanie maszyn wirtualnych SQL:** Jest to istniejąca opcja licencjonowania, w której płacisz za każdą licencję maszyny Wirtualnej programu SQL Server indywidualnie. 
  - **Licencjonowanie dedykowanego hosta:** nowy model licencjonowania dostępny dla hosta dedykowanego platformy Azure, w którym licencje programu SQL Server są powiązane i opłacane na poziomie hosta. 


Opcje na poziomie hosta dotyczące używania istniejących licencji programu SQL Server: 
  - Korzyści hybrydowe usługi Azure w wersji Azure w wersji Dla środowiska SQL Server Enterprise Edition
    - Dostępne dla klientów z pakietem SA lub subskrypcją.
    - Licencjonuj wszystkie dostępne rdzenie fizyczne i ciesz się nieograniczoną wirtualizacją (maksymalnie procesorami wirtualnymi obsługiwanymi przez hosta).
        - Aby uzyskać więcej informacji na temat stosowania korzyści hybrydowej platformy Azure do hosta dedykowanego platformy Azure, zobacz często zadawane pytania dotyczące [korzyści hybrydowych platformy Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/faq/) 
  - Licencje programu SQL Server nabyte przed 1 października
      - Program SQL Server Enterprise edition ma zarówno opcje licencji na poziomie hosta, jak i na maszynie wirtualnej. 
      - Sql Server Standard edition ma tylko opcję licencji przez maszynę wirtualną. 
          - Aby uzyskać szczegółowe informacje, zobacz [Warunki dotyczące produktów firmy Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Jeśli nie jest zaznaczona żadna opcja na poziomie dedykowanym hosta programu SQL Server, można wybrać program SQL Server AHB na poziomie poszczególnych maszyn wirtualnych, podobnie jak w przypadku maszyn wirtualnych z wieloma dzierżawami.



## <a name="provisioning"></a>Inicjowanie obsługi  
Inicjowanie obsługi administracyjnej maszyny Wirtualnej programu SQL Server na dedykowanym hoście nie różni się od innych maszyn wirtualnych platformy Azure. Można to zrobić za pomocą [usługi Azure PowerShell](../dedicated-hosts-powershell.md), [witryny Azure portal](../dedicated-hosts-portal.md)i interfejsu [wiersza polecenia platformy Azure.](../../linux/dedicated-hosts-cli.md)

Proces dodawania istniejącej maszyny Wirtualnej programu SQL Server do dedykowanego hosta wymaga przestojów, ale nie wpłynie na dane i nie będzie miał utraty danych. Niemniej jednak wszystkie bazy danych, w tym systemowe bazy danych, powinny być archiwizowane przed przeniesieniem.

## <a name="virtualization"></a>Wirtualizacja 

Jedną z zalet dedykowanego hosta jest nieograniczona wirtualizacja. Na przykład można mieć licencje dla 64 vCores, ale można skonfigurować hosta, aby miał 128 vCores, więc masz podwójne pola wirtualne, ale płacisz tylko za połowę licencji programu SQL Server. 

Ponieważ jest to twój host, możesz ustawić wirtualizację ze współczynnikiem 1:2. 

## <a name="faq"></a>Najczęściej zadawane pytania

**Pyt.: Jak działa korzyści hybrydowe platformy Azure dla licencji systemu Windows Server/SQL Server na dedykowanym hoście platformy Azure?**

Odp.: Klienci mogą używać wartości istniejących licencji systemu Windows Server i SQL Server z pakietem Software Assurance lub kwalifikujących się licencji subskrypcyjnych, aby zapłacić obniżoną stawkę na platformie Azure Dedicated Host przy użyciu korzyści hybrydowej platformy Azure. Klienci z centrum danych systemu Windows Server i programu SQL Server Enterprise Edition otrzymują nieograniczoną wirtualizację (wdrażają jak najwięcej maszyn wirtualnych systemu Windows Server na hoście, z zastrzeżeniem fizycznej pojemności serwera źródłowego), gdy licencjonują całego hosta i korzystać z korzyści hybrydowych platformy Azure.  Wszystkie obciążenia systemu Windows Server i SQL Server w usłudze Azure Dedicated Host są również uprawnione do rozszerzonych aktualizacji zabezpieczeń dla systemów Windows Server i SQL Server 2008/R2 bez dodatkowych opłat. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


