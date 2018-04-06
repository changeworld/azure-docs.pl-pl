---
title: Skrypt programu PowerShell, aby wdrożyć klaster HPC systemu Linux | Dokumentacja firmy Microsoft
description: Uruchom skrypt programu PowerShell, aby wdrożyć klaster systemu Linux HPC Pack 2012 R2 w maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 66affb47190ba0c6fccaae8e8267b310682aee46
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Utwórz Linux, wysokiej wydajności (HPC) klastra przy użyciu skryptu wdrażania HPC Pack IaaS
Uruchom HPC Pack IaaS wdrożenie skryptu PowerShell do wdrożenia całego klastra HPC Pack 2012 R2 dla systemu Linux obciążeń na maszynach wirtualnych platformy Azure. Klaster składa się z węzłem głównym przyłączonych do usługi Active Directory systemem Windows Server i Microsoft HPC Pack i węzły obliczeniowe, które Uruchom jedno z dystrybucje systemu Linux obsługiwane przez HPC Pack. Jeśli chcesz wdrożyć klaster HPC Pack w obciążeń Azure dla systemu Windows, zobacz [utworzyć klaster HPC systemu Windows przy użyciu skryptu wdrażania HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
> [!IMPORTANT] 
> Skrypt programu PowerShell opisanych w tym artykule jest tworzony klaster Microsoft HPC Pack 2012 R2 na platformie Azure przy użyciu klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> Ponadto skryptu opisane w tym artykule nie obsługuje HPC Pack 2016. Aby uzyskać informacje o szablonach usługi Resource Manager HPC Pack 2012 R2 i HPC Pack 2016, zobacz [opcji wdrażania klastrów HPC Pack na platformie Azure](../hpcpack-cluster-options.md).


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Przykładowy plik konfiguracji
Następujący plik konfiguracji tworzy kontroler domeny i lasu domen i wdraża klastra HPC Pack, który ma jednego węzła głównego z lokalnych baz danych i węzły obliczeniowe Linux 10. Usługi w chmurze są tworzone bezpośrednio w lokalizacji Azja Wschodnia. Węzły obliczeniowe systemu Linux są tworzone w dwóch usług w chmurze i dwóch kont magazynu (oznacza to, *MyLnxCN 0001* do *MyLnxCN 0005* w *MyLnxCNService01* i *mylnxstorage01*, i *MyLnxCN 0006* do *MyLnxCN 0010* w *MyLnxCNService02* i *mylnxstorage02*). Węzły obliczeniowe są tworzone na podstawie obrazu OpenLogic CentOS Linux w wersji 7.0. 

Zastąp wartości dla nazwy usługi oraz konta i nazwę subskrypcji.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Rozwiązywanie problemów
* **Błąd "Sieci wirtualnej nie istnieje"**. Po uruchomieniu skryptu wdrażania HPC Pack IaaS wdrażania wielu klastrów na platformie Azure jednocześnie w ramach jednej subskrypcji może zakończyć się niepowodzeniem wdrożenia co najmniej jeden z powodu błędu "sieci wirtualnej *sieci wirtualnej\_nazwa* nie istnieje".
  Jeśli ten błąd występuje, uruchom ponownie skrypt wdrożenia nie powiodło się.
* **Problem z dostępem do Internetu z sieci wirtualnej platformy Azure**. Jeśli utworzysz klaster HPC Pack z nowego kontrolera domeny za pomocą skryptu wdrażania lub ręczne podwyższenie węzła głównego maszyny Wirtualnej do kontrolera domeny, mogą wystąpić problemy z połączeniem z Internetem maszyn wirtualnych w sieci wirtualnej platformy Azure. Może to występować, jeśli usługa przesyłania dalej serwera DNS jest automatycznie skonfigurowana na kontrolerze domeny, a usługi przesyłania dalej serwera DNS nie jest rozpoznawane poprawnie.
  
    Aby obejść ten problem, zaloguj się do kontrolera domeny i Usuń ustawienia konfiguracji usługi przesyłania dalej lub skonfigurować prawidłowe usługi przesyłania dalej serwera DNS. Aby to zrobić, w Menedżerze serwera kliknij pozycję **narzędzia** > **DNS** do Otwórz Menedżera DNS, a następnie kliknij dwukrotnie ikonę **usług przesyłania dalej**.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md) uzyskać informacji o obsługiwanych dystrybucjach systemu Linux, przenoszenia danych i przesyłanie zadań do klastra HPC Pack z systemem Linux węzły obliczeniowe.
* Samouczki, które użyć skryptu, aby utworzyć klaster i uruchom obciążenia HPC systemu Linux zobacz:
  * [Uruchamianie NAMD z pakietem Microsoft HPC w węzłach obliczeń systemu Linux na platformie Azure](hpcpack-cluster-namd.md)
  * [Uruchamianie OpenFOAM z pakietem Microsoft HPC w węzłach obliczeń systemu Linux na platformie Azure](hpcpack-cluster-openfoam.md)
  * [Uruchomienie GWIAZDĘ — CCM + z pakietem Microsoft HPC w systemie Linux obliczeniowe węzłów na platformie Azure](hpcpack-cluster-starccm.md)

