---
title: Użyj interfejsu wiersza polecenia maszyny Wirtualnej SQL Azure, aby skonfigurować zawsze włączonej grupy dostępności programu SQL Server na Maszynie wirtualnej platformy Azure
description: 'Użyj wiersza polecenia platformy Azure, aby utworzyć klaster pracy awaryjnej Windows odbiornika grupy dostępności i wewnętrznego modułu równoważenia obciążenia na maszynę Wirtualną programu SQL Server na platformie Azure. '
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
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fd33966a7c7d2ea72cdc98a23f601687d9577dde
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825213"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Użyj interfejsu wiersza polecenia maszyny Wirtualnej SQL Azure, aby skonfigurować zawsze włączonej grupy dostępności programu SQL Server na Maszynie wirtualnej platformy Azure
W tym artykule opisano sposób używania [wiersza polecenia platformy Azure SQL VM](https://docs.microsoft.com/mt-mt/cli/azure/ext/sqlvm-preview/sqlvm?view=azure-cli-2018-03-01-hybrid) do wdrożenia Windows Failover Cluster (WSFC) i Dodaj maszyny wirtualne SQL Server do klastra, a także tworzenie wewnętrznego modułu równoważenia obciążenia i odbiornika zawsze włączonej grupy dostępności.  Rzeczywiste wdrożenie grupy dostępności Always On nadal odbywa się ręcznie za pomocą programu SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Wymagania wstępne
Aby zautomatyzować konfiguracji zawsze włączonej grupy dostępności przy użyciu interfejsu wiersza polecenia maszyny Wirtualnej SQL Azure, musi już mieć następujące wymagania wstępne: 
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów z kontrolerem domeny. 
- Co najmniej jeden przyłączonych do domeny [maszyn wirtualnych na platformie Azure działającej wersji programu SQL Server 2016 (lub nowszego) przedsiębiorstwa](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) w *tego samego zestawu dostępności lub w różnych strefach dostępności* które posiadają [zarejestrowany za pomocą dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
 
## <a name="create-storage-account-as-a-cloud-witness"></a>Tworzenie konta magazynu jako monitor w chmurze
Klaster wymaga konta magazynu, która będzie działać jako monitor w chmurze. Można użyć dowolnego istniejącego konta magazynu lub utworzyć nowe konto magazynu. Jeśli chcesz użyć istniejącego konta magazynu, przejdź do następnej sekcji. 

Poniższy fragment kodu tworzy konto magazynu: 
```cli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Może zostać wyświetlony błąd `az sql: 'vm' is not in the 'az sql' command group` korzystania z nieaktualną wersją wiersza polecenia platformy Azure. Pobierz [najnowszą wersję interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) Aby pokonać ten błąd.

## <a name="define-windows-failover-cluster-metadata"></a>Zdefiniuj metadanych klastra pracy awaryjnej Windows
Interfejs wiersza polecenia maszyny Wirtualnej SQL Azure [grupy maszyn wirtualnych sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) polecenia grupa zarządza metadanych usługi Windows Failover Cluster (WSFC), który jest hostem grupy dostępności. Metadanych klastra zawiera domeny usługi AD, konta klastra, konta magazynu ma być używany jako monitor w chmurze, a wersja programu SQL Server. Użyj [Tworzenie grupy maszyn wirtualnych sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) zdefiniowanie metadanych dla usługi WSFC, tak, aby po dodaniu pierwszej maszyny Wirtualnej programu SQL Server, tworzenia klastra zgodnie z definicją. 

Poniższy fragment kodu definiuje metadanych dla klastra:
```cli
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="add-sql-server-vms-to-cluster"></a>Dodaj maszyny wirtualne SQL Server do klastra
Dodawanie pierwszej maszyny Wirtualnej programu SQL Server do klastra tworzy klaster. [Az sql vm Dodaj grupa](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) polecenie tworzy klaster o nazwie podanej wcześniej instaluje rolę klastra na maszynach wirtualnych serwera SQL i dodaje je do klastra. Zastosowań kolejnych `az sql vm add-to-group` polecenie dodaje dodatkowe maszyny wirtualne programu SQL Server do nowo utworzonego klastra. 

Poniższy fragment kodu tworzy klaster i dodaje do pierwszej maszyny Wirtualnej programu SQL Server: 

```cli
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Użyj tego polecenia, aby dodać wszystkie pozostałe maszyny wirtualne SQL Server do klastra, tylko modyfikowanie `-n` parametr dla nazwy maszyny Wirtualnej programu SQL Server. 

## <a name="create-availability-group"></a>Utwórz grupę dostępności
Ręcznie utwórz grupę dostępności, tak jak zwykle, za pomocą [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), lub [języka Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Czy **nie** utworzyć odbiornik, w tym momencie, ponieważ jest to realizowane wiersza polecenia platformy Azure, w poniższych sekcjach.  

## <a name="create-internal-load-balancer"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia

Zawsze włączone odbiornika grupy dostępności (grupy dostępności) wymaga wewnętrznego modułu równoważenia obciążenia platformy Azure (ILB). Wewnętrznego modułu równoważenia obciążenia zapewnia "pływający" adres IP dla odbiornika grupy dostępności, która umożliwia szybsze trybu failover i ponowne nawiązanie połączenia. Jeśli maszyny wirtualne SQL Server w grupie dostępności są częścią tego samego zestawu dostępności, wówczas można użyć podstawowego modułu równoważenia obciążenia; w przeciwnym razie należy użyć standardowego modułu równoważenia obciążenia.  **Wewnętrznego modułu równoważenia obciążenia musi należeć do tej samej sieci wirtualnej jako wystąpienia maszyny Wirtualnej programu SQL Server.** 

Poniższy fragment kodu tworzy wewnętrznego modułu równoważenia obciążenia:

```cli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > Publiczny zasób adresu IP dla każdej maszyny Wirtualnej serwera SQL powinien mieć standardowej jednostki SKU, aby zapewnić ich zgodność przy użyciu standardowego modułu równoważenia obciążenia. Aby określić jednostki SKU publicznego zasobu adresu IP maszyny Wirtualnej, przejdź do usługi **grupy zasobów**, wybierz opcję usługi **publiczny adres IP** zasób żądanego programu SQL Server maszyny Wirtualnej i Znajdź wartość w obszarze **jednostki SKU**  z **Przegląd** okienka.  

## <a name="create-availability-group-listener"></a>Utwórz odbiornik grupy dostępności
Gdy grupa dostępności została utworzona ręcznie, można utworzyć odbiornik, używając [az sql vm odbiornika grupy dostępności-](https://docs.microsoft.com/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- **Identyfikator zasobu podsieci** jest wartością `/subnets/<subnetname>` dołączany do Identyfikatora zasobu zasobu sieci wirtualnej. Aby określić identyfikator zasobu podsieci, wykonaj następujące czynności:
   1. Przejdź do grupy zasobów w [witryny Azure portal](https://portal.azure.com). 
   1. Wybierz zasób sieci wirtualnej. 
   1. Wybierz **właściwości** w **ustawienia** okienka. 
   1. Zidentyfikuj identyfikator zasobu sieci wirtualnej i Dołącz `/subnets/<subnetname>`na końcu go, aby utworzyć identyfikator podsieci zasobu. Na przykład:
        - Mój zasób sieci wirtualnej jest identyfikator `/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`.
        - Moja nazwa podsieci jest `default`.
        - Dlatego jest identyfikator zasobu podsieci `/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Poniższy fragment kodu spowoduje utworzenie odbiornika grupy dostępności:

```cli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM’s hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Program SQL Server w maszynie Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [Informacje o wersji maszyny Wirtualnej programu SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Przełączanie Modele licencjonowania maszynę Wirtualną programu SQL Server](virtual-machines-windows-sql-ahb.md)
* [Omówienie zawsze włączonych grup dostępności &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfiguracja wystąpienia serwera dla zawsze włączonych grup dostępności &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administrowanie grupy dostępności &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorowanie dostępności grupy &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)
* [Przegląd instrukcji języka Transact-SQL dla zawsze włączonych grup dostępności &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Przegląd poleceń cmdlet programu PowerShell dla zawsze włączonych grup dostępności &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
