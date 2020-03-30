---
title: Zdarzenie tworzenia puli usługi Azure Batch
description: Odwołanie do zdarzenia tworzenia puli partii, które jest emitowane po utworzeniu puli. Zawartość dziennika będzie udostępniać ogólne informacje o puli.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022193"
---
# <a name="pool-create-event"></a>Zdarzenie utworzenia puli

 To zdarzenie jest emitowane po utworzeniu puli. Zawartość dziennika będzie udostępniać ogólne informacje o puli. Należy zauważyć, że jeśli rozmiar docelowy puli jest większy niż 0 węzłów obliczeniowych, zdarzenie rozpoczęcia rozmiaru rozmiaru puli nastąpi natychmiast po tym zdarzeniu.

 W poniższym przykładzie przedstawiono treść zdarzenia tworzenia `CloudServiceConfiguration` puli dla puli utworzonej przy użyciu właściwości.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`displayName`|Ciąg|Wyświetlana nazwa puli.|
|`vmSize`|Ciąg|Rozmiar maszyn wirtualnych w puli. Wszystkie maszyny wirtualne w puli są tego samego rozmiaru. <br/><br/> Aby uzyskać informacje o dostępnych rozmiarach maszyn wirtualnych dla pul usług w chmurze (pul utworzonych za pomocą cloudServiceConfiguration), zobacz [Rozmiary usług w chmurze](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Usługa Batch obsługuje wszystkie rozmiary `ExtraSmall`maszyn wirtualnych usług w chmurze z wyjątkiem .<br/><br/> Aby uzyskać informacje o dostępnych rozmiarach maszyn wirtualnych dla pul przy użyciu obrazów z portalu Virtual Machines Marketplace (pule utworzone za pomocą virtualMachineConfiguration) zobacz [Rozmiary maszyn wirtualnych](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) lub [Rozmiary dla maszyn wirtualnych](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Usługa Batch obsługuje wszystkie rozmiary maszyn wirtualnych platformy Azure oprócz `STANDARD_A0` i maszyn z usługi Premium Storage (seria `STANDARD_GS`, `STANDARD_DS` i `STANDARD_DSV2`).|
|`imageType`|Ciąg|Metoda wdrażania obrazu. Obsługiwane wartości `virtualMachineConfiguration` są lub`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Typ złożony|Konfiguracja usługi w chmurze dla puli.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Typ złożony|Konfiguracja maszyny wirtualnej dla puli.|
|[`networkConfiguration`](#bk_netconf)|Typ złożony|Konfiguracja sieci dla puli.|
|`resizeTimeout`|Time|Limit czasu alokacji węzłów obliczeniowych do puli określonej dla ostatniej operacji ponownego rozmiaru w puli.  (Początkowe rozmiary podczas tworzenia puli są liczone jako zmiana rozmiaru).|
|`targetDedicatedNodes`|Int32|Liczba dedykowanych węzłów obliczeniowych, które są wymagane dla puli.|
|`targetLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych o niskim priorytecie, które są wymagane dla puli.|
|`enableAutoScale`|Wartość logiczna|Określa, czy rozmiar puli jest automatycznie dostosowywany w czasie.|
|`enableInterNodeCommunication`|Wartość logiczna|Określa, czy pula jest skonfigurowana do bezpośredniej komunikacji między węzłami.|
|`isAutoPool`|Wartość logiczna|Określa, czy pula została utworzona za pomocą mechanizmu Autopool zadania.|
|`maxTasksPerNode`|Int32|Maksymalna liczba zadań, które można uruchomić jednocześnie w jednym węźle obliczeniowym w puli.|
|`vmFillType`|Ciąg|Określa sposób dystrybucji zadań przez usługę Batch między węzłami obliczeniowymi w puli. Prawidłowe wartości to Spread lub Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceConfiguration (Konfiguracja usługi w chmurze)

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`osFamily`|Ciąg|Rodzina systemu operacyjnego azure gości do zainstalowania na maszynach wirtualnych w puli.<br /><br /> Możliwe wartości:<br /><br /> **2** – Rodzina systemów operacyjnych 2, odpowiednik dodatku SP1 dla systemu Windows Server 2008 R2.<br /><br /> **3** – Rodzina systemów operacyjnych 3, odpowiednik systemu Windows Server 2012.<br /><br /> **4** – Rodzina systemów operacyjnych 4, odpowiednik systemu Windows Server 2012 R2.<br /><br /> Aby uzyskać więcej informacji, zobacz [Wersje systemu operacyjnego gościa platformy Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|Ciąg|Wersja systemu operacyjnego gościa platformy Azure do zainstalowania na maszynach wirtualnych w puli.<br /><br /> Wartość domyślna **\*** jest, która określa najnowszą wersję systemu operacyjnego dla określonej rodziny.<br /><br /> Aby uzyskać inne dozwolone wartości, zobacz [Wersje systemu operacyjnego gościa platformy Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>konfiguracja virtualMachineConfiguracja

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Typ złożony|Określa informacje o platformie lub obrazie marketplace do użycia.|
|`nodeAgentId`|Ciąg|Jednostka SKU agenta węzła usługi Batch aprowizowana w węźle obliczeniowym.|
|[`windowsConfiguration`](#bk_winconf)|Typ złożony|Określa ustawienia systemu operacyjnego Windows na maszynie wirtualnej. Nie należy określać tej właściwości, jeśli imageReference odwołuje się do obrazu systemu operacyjnego Linux.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>imageReference (wniosek o obraz

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`publisher`|Ciąg|Wydawca obrazu.|
|`offer`|Ciąg|Oferta obrazu.|
|`sku`|Ciąg|Jednostka SKU obrazu.|
|`version`|Ciąg|Wersja obrazu.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsKonfiguracja

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Wartość logiczna|Wskazuje, czy maszyna wirtualna jest włączona dla aktualizacji automatycznych. Jeśli ta właściwość nie jest określona, wartość domyślna jest true.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>konfiguracja sieci

|Nazwa elementu|Typ|Uwagi|
|------------------|--------------|----------|
|`subnetId`|Ciąg|Określa identyfikator zasobu podsieci, w której tworzone są węzły obliczeniowe puli.|
