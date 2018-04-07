---
title: Skrypt programu PowerShell, aby wdrożyć klaster HPC systemu Windows | Dokumentacja firmy Microsoft
description: Uruchom skrypt programu PowerShell, aby wdrożyć klaster Windows HPC Pack 2012 R2 w maszynach wirtualnych platformy Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: e05562aeac0ea89ec1c3d80d2967c8f59c68d332
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Tworzenie wysokiej wydajności (HPC) klastra systemu Windows przy użyciu skryptu wdrażania HPC Pack IaaS
Uruchom HPC Pack IaaS wdrożenie skryptu PowerShell do wdrożenia całego klastra HPC Pack 2012 R2 dla obciążeń systemu Windows na maszynach wirtualnych platformy Azure. Klaster składa się z węzłem głównym przyłączonych do usługi Active Directory systemem Windows Server i Microsoft HPC Pack i zasoby, które określisz obliczeniowe dodatkowe okna. Jeśli chcesz wdrożenie klastra HPC Pack dla systemu Linux obciążeń na platformie Azure, zobacz [utworzyć klaster HPC systemu Linux przy użyciu skryptu wdrażania HPC Pack IaaS](../../linux/classic/hpcpack-cluster-powershell-script.md). 

> [!IMPORTANT] 
> Skrypt programu PowerShell opisanych w tym artykule jest tworzony klaster Microsoft HPC Pack 2012 R2 na platformie Azure przy użyciu klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> Ponadto skryptu opisane w tym artykule nie obsługuje HPC Pack 2016. Aby uzyskać informacje o szablonach usługi Resource Manager HPC Pack 2012 R2 i HPC Pack 2016, zobacz [opcji wdrażania klastrów HPC Pack na platformie Azure](../hpcpack-cluster-options.md).

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Przykładowe pliki konfiguracji
W poniższych przykładach podstaw własne wartości dla Twojego identyfikatora subskrypcji lub nazwy i nazwy usługi oraz konta.

### <a name="example-1"></a>Przykład 1
Następującego pliku konfiguracji wdraża klastra HPC Pack, który ma węzła głównego z lokalnych baz danych i obliczeniowe pięć węzłów z systemem operacyjnym Windows Server 2012 R2. Usługi w chmurze są tworzone bezpośrednio w lokalizacji zachodnie stany USA. Węzła głównego działa jako kontroler domeny w lesie domeny.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Przykład 2
Następujący plik konfiguracji wdraża klaster HPC Pack w istniejącym lesie domeny. Klaster ma 1 węzła głównego z lokalnych baz danych i węzły obliczeniowe 12 z rozszerzeniem wirtualna BGInfo zastosowane.
Automatyczne instalowanie aktualizacji systemu Windows jest wyłączona dla wszystkich maszyn wirtualnych w lesie domeny. Usługi w chmurze są tworzone bezpośrednio w lokalizacji Azja Wschodnia. Węzły obliczeniowe są tworzone trzy usługi w chmurze i kont magazynu trzy: *MyHPCCN 0001* do *MyHPCCN 0005* w *MyHPCCNService01* i  *mycnstorage01*; *MyHPCCN 0006* do *MyHPCCN0010* w *MyHPCCNService02* i *mycnstorage02*; i  *MyHPCCN-0011* do *MyHPCCN 0012* w *MyHPCCNService03* i *mycnstorage03*). Węzły obliczeniowe są tworzone na podstawie istniejącego obrazu prywatnej przechwytywane z węzła obliczeń. Automatycznie zwiększyć lub zmniejszyć włączono usługę z domyślnymi zwiększyć lub zmniejszyć odstępach czasu.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Przykład 3
Następujący plik konfiguracji wdraża klaster HPC Pack w istniejącym lesie domeny. Klaster zawiera jednego węzła głównego, jedną bazę danych serwera z dysku danych 500 GB, dwa węzły brokera systemem operacyjnym Windows Server 2012 R2 i pięć węzłów obliczeniowych systemem operacyjnym Windows Server 2012 R2. Usługi w chmurze MyHPCCNService jest tworzony w grupie koligacji *MyIBAffinityGroup*, i innych usług w chmurze są tworzone w grupie koligacji *MyAffinityGroup*. HPC interfejsu API REST harmonogramu dla zadania i portalu internetowego HPC są włączone w węźle głównym.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Przykład 4
Następujący plik konfiguracji wdraża klaster HPC Pack w istniejącym lesie domeny. Klaster ma dwa węzła głównego z lokalnych baz danych, są tworzone dwa szablony Azure węzła i dla szablonu węzła Azure są tworzone trzy węzły Azure średni rozmiar *AzureTemplate1*. Plik skryptu jest uruchamiana w węźle głównym po skonfigurowaniu węzła głównego.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
* **Błąd "Sieci wirtualnej nie istnieje"** — po uruchomieniu skryptu do wdrożenia wielu klastrów na platformie Azure jednocześnie w ramach jednej subskrypcji może zakończyć się niepowodzeniem wdrożenia co najmniej jeden z powodu błędu "sieci wirtualnej *sieci wirtualnej\_nazwa* nie istnieje. ".
  Jeśli ten błąd występuje, uruchom ponownie skrypt wdrożenia nie powiodło się.
* **Problem z dostępem do Internetu z sieci wirtualnej platformy Azure** — Jeśli utworzyć klaster z nowego kontrolera domeny za pomocą skryptu wdrażania lub ręczne podwyższenie węzła głównego maszyny Wirtualnej do kontrolera domeny, mogą wystąpić problemy z połączeniem Maszyny wirtualne z Internetem. Ten problem może wystąpić, jeśli usługa przesyłania dalej serwera DNS jest automatycznie skonfigurowana na kontrolerze domeny, a usługi przesyłania dalej serwera DNS nie jest rozpoznawane poprawnie.
  
    Aby obejść ten problem, zaloguj się do kontrolera domeny i Usuń ustawienia konfiguracji usługi przesyłania dalej lub skonfigurować prawidłowe usługi przesyłania dalej serwera DNS. Aby skonfigurować to ustawienie, w Menedżerze serwera kliknij pozycję **narzędzia** >
    **DNS** do Otwórz Menedżera DNS, a następnie kliknij dwukrotnie ikonę **usług przesyłania dalej**.
* **Problem z dostępem do sieci RDMA z maszyn wirtualnych obliczeniowych** — Jeśli Dodawanie obliczeń do systemu Windows Server lub węzeł brokera rozmiar maszyn wirtualnych przy użyciu z funkcją RDMA, takich jak A8 i A9, mogą wystąpić problemy z połączeniem z siecią aplikacji RDMA tych maszyn wirtualnych. Jedną z przyczyn się, że ten problem występuje, to jeśli rozszerzenie HpcVmDrivers nie jest zainstalowany prawidłowo w przypadku maszyn wirtualnych zostaną dodane do klastra. Na przykład rozszerzenie mogła zostać zablokowana w trakcie instalowania.
  
    Aby obejść ten problem, sprawdź najpierw stan rozszerzenia na maszynach wirtualnych. Jeśli nie zainstalowano poprawnie rozszerzenia, spróbuj usunąć węzeł z klastra HPC, a następnie ponownie Dodaj węzły. Na przykład można dodać węzła obliczeniowego maszyn wirtualnych, uruchamiając skrypt HpcIaaSNode.ps1 Dodaj w węźle głównym.

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj uruchomić test obciążenia w klastrze. Na przykład zobacz HPC Pack [Wprowadzenie — przewodnik](https://technet.microsoft.com/library/jj884144).
* Samouczek skryptu wdrażania klastra i uruchomić obciążenia HPC, zobacz [wprowadzenie do klastra HPC Pack na platformie Azure, aby uruchomić program Excel i SOA obciążeń](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Spróbuj HPC Pack narzędzia umożliwiające uruchamianie, zatrzymywanie, Dodaj i usuń węzły obliczeniowe z klastra, którą utworzysz. Zobacz [Zarządzaj węzłów obliczeniowych w HPC Pack klastra w systemie Azure](hpcpack-cluster-node-manage.md).
* Aby uzyskać umożliwiają przesyłanie zadań do klastra z komputera lokalnego, zobacz [klastra HPC przesyłania zadań z komputera lokalnego HPC Pack na platformie Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

