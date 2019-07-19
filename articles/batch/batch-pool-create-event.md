---
title: Zdarzenie tworzenia puli Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia utworzenia puli usługi Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: a86ce329a19272eb83c431af395b330f75111361
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323292"
---
# <a name="pool-create-event"></a>Zdarzenie utworzenia puli

 To zdarzenie jest emitowane po utworzeniu puli. Zawartość dziennika spowoduje udostępnienie ogólnych informacji o puli. Należy pamiętać, że jeśli rozmiar docelowy puli ma więcej niż 0 węzłów obliczeniowych, zdarzenie rozpoczęcia zmiany rozmiaru puli będzie podążać bezpośrednio po tym zdarzeniu.

 Poniższy przykład przedstawia treść puli Utwórz zdarzenie dla puli utworzonej przy użyciu właściwości CloudServiceConfiguration.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Type|Uwagi|
|-------------|----------|-----------|
|id|Ciąg|Identyfikator puli.|
|displayName|Ciąg|Nazwa wyświetlana puli.|
|vmSize|String|Rozmiar maszyn wirtualnych w puli. Wszystkie maszyny wirtualne w puli mają ten sam rozmiar. <br/><br/> Aby uzyskać informacje o dostępnych rozmiarach maszyn wirtualnych dla pul Cloud Services (pule utworzone za pomocą cloudServiceConfiguration), zobacz [rozmiary Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Program Batch obsługuje wszystkie Cloud Services rozmiary maszyn `ExtraSmall`wirtualnych z wyjątkiem.<br/><br/> Aby uzyskać informacje o dostępnych rozmiarach maszyn wirtualnych dla pul przy użyciu obrazów z witryny Virtual Machines Marketplace (pule utworzone za pomocą virtualMachineConfiguration), zobacz [rozmiary Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) lub [rozmiary dla Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Usługa Batch obsługuje wszystkie rozmiary maszyn wirtualnych platformy Azure oprócz `STANDARD_A0` i maszyn z usługi Premium Storage (seria `STANDARD_GS`, `STANDARD_DS` i `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Typ złożony|Konfiguracja usługi w chmurze dla puli.|
|[virtualMachineConfiguration](#bk_vmconf)|Typ złożony|Konfiguracja maszyny wirtualnej dla puli.|
|[networkConfiguration](#bk_netconf)|Typ złożony|Konfiguracja sieci dla puli.|
|resizeTimeout|Time|Limit czasu przydziału węzłów obliczeniowych do puli określonej dla operacji ostatniej zmiany rozmiaru w puli.  (Początkowe rozmiary podczas tworzenia puli jest traktowane jako zmiana rozmiaru).|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są żądane dla puli.|
|enableAutoScale|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się w miarę upływu czasu.|
|enableInterNodeCommunication|Bool|Określa, czy pula jest skonfigurowana do bezpośredniej komunikacji między węzłami.|
|isAutoPool|Bool|Określa, czy pula została utworzona za pomocą mechanizmu autopuli zadań.|
|maxTasksPerNode|Int32|Maksymalna liczba zadań, które można uruchamiać współbieżnie w jednym węźle obliczeniowym w puli.|
|vmFillType|String|Definiuje, w jaki sposób usługa Batch dystrybuuje zadania między węzłami obliczeniowymi w puli. Prawidłowe wartości to rozmieszczenie lub pakiet.|

###  <a name="bk_csconf"></a>cloudServiceConfiguration

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|Rodzina systemów operacyjnych|Ciąg|Rodzina systemów operacyjnych gościa platformy Azure do zainstalowania na maszynach wirtualnych w puli.<br /><br /> Możliwe wartości to:<br /><br /> **2** — Rodzina systemów operacyjnych 2, równoważna z systemem Windows Server 2008 R2 z dodatkiem SP1.<br /><br /> **3** — Rodzina systemów operacyjnych 3, równoważna z systemem Windows Server 2012.<br /><br /> **4** — Rodzina systemów operacyjnych 4, równoważna z systemem Windows Server 2012 R2.<br /><br /> Aby uzyskać więcej informacji, zobacz [wersje systemu operacyjnego gościa platformy Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Ciąg|Wersja systemu operacyjnego gościa platformy Azure do zainstalowania na maszynach wirtualnych w puli.<br /><br /> Wartość domyślna to **\*** określa najnowszą wersję systemu operacyjnego dla określonej rodziny.<br /><br /> Aby uzyskać inne dozwolone wartości, zobacz [wersje systemu operacyjnego gościa platformy Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Typ złożony|Określa informacje o platformie lub obrazie witryny Marketplace do użycia.|
|nodeAgentSKUId|String|Jednostka SKU agenta węzła partii obsługiwana w węźle obliczeniowym.|
|[windowsConfiguration](#bk_winconf)|Typ złożony|Określa ustawienia systemu operacyjnego Windows na maszynie wirtualnej. Ta właściwość nie może być określona, jeśli elementu imagereference odwołuje się do obrazu systemu operacyjnego Linux.|

###  <a name="bk_imgref"></a>Elementu imagereference

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|publisher|String|Wydawca obrazu.|
|sprzedaży|String|Oferta obrazu.|
|sku|Ciąg|Jednostka SKU obrazu.|
|version|String|Wersja obrazu.|

###  <a name="bk_winconf"></a>windowsConfiguration

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|enableAutomaticUpdates|Boolean|Wskazuje, czy dla maszyny wirtualnej włączono automatyczne aktualizacje. Jeśli ta właściwość nie jest określona, wartość domyślna to true.|

###  <a name="bk_netconf"></a>networkConfiguration

|Nazwa elementu|Type|Uwagi|
|------------------|--------------|----------|
|subnetId|String|Określa identyfikator zasobu podsieci, w którym są tworzone węzły obliczeniowe puli.|
