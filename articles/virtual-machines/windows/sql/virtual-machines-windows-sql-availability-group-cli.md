---
title: Konfigurowanie grupy dostępności (Azure CLI)
description: Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć klaster trybu failover systemu Windows, odbiornik grup dostępności i wewnętrzny moduł równoważenia obciążenia na maszynie Wirtualnej programu SQL Server na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022366"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Konfigurowanie grupy dostępności zawsze włączone dla programu SQL Server na maszynie Wirtualnej platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
W tym artykule opisano sposób używania [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/vm?view=azure-cli-latest/) do wdrażania klastra trybu failover systemu Windows, dodawania maszyn wirtualnych programu SQL Server do klastra oraz tworzenia wewnętrznego modułu równoważenia obciążenia i odbiornika dla grupy dostępności zawsze włączone. Wdrażanie grupy Dostępność zawsze włączone jest nadal wykonywane ręcznie za pośrednictwem programu SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Wymagania wstępne
Aby zautomatyzować konfigurację grupy dostępności zawsze włączone przy użyciu interfejsu wiersza polecenia platformy Azure, musisz mieć następujące wymagania wstępne: 
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów z kontrolerem domeny. 
- Co najmniej jedna maszyna wirtualna przyłączona do domeny [na platformie Azure z systemem SQL Server 2016 (lub nowsza) Wersja Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) w tym samym zestawie dostępności lub w różnych *strefach dostępności,* które zostały [zarejestrowane u dostawcy zasobów maszyny Wirtualnej SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- [Interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 
- Dwa dostępne (nie używane przez żadną jednostkę) adresy IP. Jednym z nich jest wewnętrzny moduł równoważenia obciążenia. Drugi dotyczy odbiornika grupy dostępności w tej samej podsieci co grupa dostępności. Jeśli używasz istniejącego modułu równoważenia obciążenia, potrzebujesz tylko jednego dostępnego adresu IP dla odbiornika grupy dostępności. 

## <a name="permissions"></a>Uprawnienia
Aby skonfigurować grupę Dostępność zawsze włączone, potrzebne są następujące uprawnienia do kont przy użyciu interfejsu wiersza polecenia platformy Azure: 

- Istniejące konto użytkownika domeny z **uprawnieniem Utwórz obiekt komputera** w domenie. Na przykład konto administratora domeny zazwyczaj ma wystarczające account@domain.comuprawnienia (na przykład: ). _To konto powinno również być częścią lokalnej grupy administratorów na każdej maszynie wirtualnej, aby utworzyć klaster._
- Konto użytkownika domeny, które steruje usługą PROGRAMU SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Krok 1: Tworzenie konta magazynu jako monitora w chmurze
Klaster potrzebuje konta magazynu, aby działać jako monitor chmury. Można użyć dowolnego istniejącego konta magazynu lub utworzyć nowe konto magazynu. Jeśli chcesz użyć istniejącego konta magazynu, przejdź do następnej sekcji. 

Następujący fragment kodu tworzy konto magazynu: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Błąd może zostać `az sql: 'vm' is not in the 'az sql' command group` wyświetlony, jeśli używasz przestarzałej wersji interfejsu wiersza polecenia platformy Azure. Pobierz [najnowszą wersję interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) aby ominąć ten błąd.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Krok 2: Definiowanie metadanych klastra trybu failover systemu Windows
Grupa poleceń grupy maszyn wirtualnych interfejsu wiersza polecenia Azure [AZ sql](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) zarządza metadanymi usługi WSFC (Windows Server Failover Cluster), która obsługuje grupę dostępności. Metadane klastra obejmują domenę usługi Active Directory, konta klastra, konta magazynu, które mają być używane jako monitor chmury, oraz wersję programu SQL Server. Użyj [az sql vm group create,](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) aby zdefiniować metadane dla WSFC, tak aby po dodaniu pierwszej maszyny Wirtualnej programu SQL Server klaster został utworzony zgodnie z definicją. 

Poniższy fragment kodu definiuje metadane klastra:
```azurecli-interactive
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Krok 3: Dodawanie maszyn wirtualnych programu SQL Server do klastra
Dodanie pierwszej maszyny Wirtualnej programu SQL Server do klastra tworzy klaster. Polecenie [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) tworzy klaster o wcześniej podanej nazwie, instaluje rolę klastra na maszynach wirtualnych programu SQL Server i dodaje je do klastra. Kolejne zastosowania polecenia `az sql vm add-to-group` dodają więcej maszyn wirtualnych programu SQL Server do nowo utworzonego klastra. 

Poniższy fragment kodu tworzy klaster i dodaje do niego pierwszą maszynę wirtualną programu SQL Server: 

```azurecli-interactive
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
To polecenie służy do dodawania innych maszyn wirtualnych programu SQL Server do klastra. Zmodyfikuj `-n` tylko parametr nazwy maszyny Wirtualnej programu SQL Server. 

## <a name="step-4-create-the-availability-group"></a>Krok 4: Tworzenie grupy dostępności
Ręcznie utwórz grupę dostępności w zwykły sposób, używając [programu SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), Programu [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)lub [Transact-SQL.](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 

>[!IMPORTANT]
> *Nie* należy tworzyć odbiornika w tej chwili, ponieważ odbywa się to za pośrednictwem interfejsu wiersza polecenia platformy Azure w poniższych sekcjach.  

## <a name="step-5-create-the-internal-load-balancer"></a>Krok 5: Tworzenie wewnętrznego modułu równoważenia obciążenia

Odbiornik grupy zawsze włączone dostępność wymaga wewnętrznego wystąpienia modułu Azure Load Balancer. Wewnętrzny moduł równoważenia obciążenia zapewnia "zmienny" adres IP dla odbiornika grupy dostępności, który umożliwia szybsze praca awaryjna i ponowne połączenie. Jeśli maszyny wirtualne programu SQL Server w grupie dostępności są częścią tego samego zestawu dostępności, można użyć modułu równoważenia obciążenia basic. W przeciwnym razie należy użyć standardowego modułu równoważenia obciążenia.  

> [!NOTE]
> Wewnętrzny moduł równoważenia obciążenia powinien znajdować się w tej samej sieci wirtualnej, co wystąpienia maszyn wirtualnych programu SQL Server. 

Poniższy fragment kodu tworzy wewnętrzny moduł równoważenia obciążenia:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Publiczny zasób IP dla każdej maszyny Wirtualnej programu SQL Server powinien mieć standardową jednostkę SKU, która będzie zgodna z modułem równoważenia obciążenia Standard. Aby określić jednostkę SKU publicznego zasobu IP maszyny Wirtualnej, przejdź do **grupy zasobów**, wybierz zasób publiczny **adres IP** dla żądanej maszyny Wirtualnej programu SQL Server i znajdź wartość w obszarze **Jednostka SKU** w **okienku Przegląd.**  

## <a name="step-6-create-the-availability-group-listener"></a>Krok 6: Tworzenie odbiornika grupy dostępności
Po ręcznym utworzeniu grupy dostępności można utworzyć odbiornik za pomocą [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*Identyfikator zasobu podsieci* `/subnets/<subnetname>` jest wartością dołączaną do identyfikatora zasobu zasobu sieci wirtualnej. Aby zidentyfikować identyfikator zasobu podsieci:
   1. Przejdź do grupy zasobów w [witrynie Azure portal](https://portal.azure.com). 
   1. Wybierz zasób sieci wirtualnej. 
   1. Wybierz **pozycję Właściwości** w okienku **Ustawienia.** 
   1. Zidentyfikuj identyfikator zasobu `/subnets/<subnetname>` dla sieci wirtualnej i dołącz ją na jej końcu, aby utworzyć identyfikator zasobu podsieci. Przykład:
      - Identyfikator zasobu sieci wirtualnej to:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Nazwa podsieci to:`default`
      - W związku z tym identyfikator zasobu podsieci jest:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Poniższy fragment kodu tworzy odbiornik grupy dostępności:

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modyfikowanie liczby replik w grupie dostępności
Istnieje dodatkowa warstwa złożoności podczas wdrażania grupy dostępności na maszynach wirtualnych programu SQL Server hostowanych na platformie Azure. Dostawca zasobów i grupa maszyn wirtualnych zarządzają teraz zasobami. W związku z tym podczas dodawania lub usuwania replik w grupie dostępności, istnieje dodatkowy krok aktualizowania metadanych odbiornika z informacjami o maszynach wirtualnych programu SQL Server. Podczas modyfikowania liczby replik w grupie dostępności należy również użyć polecenia [az sql vm group ag-listener update,](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) aby zaktualizować odbiornik metadanymi maszyn wirtualnych programu SQL Server. 


### <a name="add-a-replica"></a>Dodawanie repliki

Aby dodać nową replikę do grupy dostępności:

1. Dodaj maszynę wirtualną programu SQL Server do klastra:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Program SQL Server Management Studio służy do dodawania wystąpienia programu SQL Server jako repliki w grupie dostępności.
1. Dodaj metadane maszyny Wirtualnej programu SQL Server do odbiornika:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Usuwanie repliki

Aby usunąć replikę z grupy dostępności:

1. Usuń replikę z grupy dostępności przy użyciu programu SQL Server Management Studio. 
1. Usuń metadane maszyny Wirtualnej programu SQL Server z detektora:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Usuń maszynę wirtualną programu SQL Server z klastra:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Usuwanie odbiornika grupy dostępności
Jeśli później trzeba usunąć odbiornik grupy dostępności skonfigurowany za pomocą interfejsu wiersza polecenia platformy Azure, należy przejść przez dostawcę zasobów maszyny Wirtualnej SQL. Ponieważ odbiornik jest zarejestrowany za pośrednictwem dostawcy zasobów maszyny Wirtualnej SQL, po prostu usunięcie go za pośrednictwem programu SQL Server Management Studio jest niewystarczające. 

Najlepszą metodą jest usunięcie go za pośrednictwem dostawcy zasobów maszyny Wirtualnej SQL przy użyciu następującego fragmentu kodu w wierszu polecenia platformy Azure. W ten sposób usuwa metadane odbiornika grupy dostępności z dostawcy zasobów maszyny Wirtualnej SQL. Również fizycznie usuwa odbiornik z grupy dostępności. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Informacje o wersji dla maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Przełączanie modeli licencjonowania dla maszyny Wirtualnej programu SQL Server](virtual-machines-windows-sql-ahb.md)
* [Omówienie grup dostępności zawsze włączone &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfiguracja wystąpienia serwera dla grup dostępności zawsze włączone &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administrowanie grupą dostępności &#40;&#41;programu SQL Server](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorowanie grup dostępności &#40;&#41;programu SQL Server](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Omówienie instrukcji Transact-SQL dla grup dostępności always on &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Omówienie poleceń cmdlet programu PowerShell dla grup dostępności always on &#40;programu SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
