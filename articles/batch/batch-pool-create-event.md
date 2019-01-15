---
title: Zdarzenie utworzenia puli usługi Azure Batch | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca puli usługi Batch, Utwórz zdarzenie.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 794b3c83ff58967ef8169bed98f7b369335029ae
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259844"
---
# <a name="pool-create-event"></a>Zdarzenie utworzenia puli

 To zdarzenie jest emitowane po utworzeniu puli. Zawartość dziennika udostępni ogólne informacje o puli. Należy pamiętać, że jeśli rozmiar docelowej puli jest większa niż 0 węzłów obliczeniowych, zdarzenie rozpoczęcia zmiany rozmiaru puli będzie występować natychmiast po tym zdarzeniu.

 Poniższy przykład pokazuje, treść puli tworzenie zdarzenia dla puli, utworzony za pomocą właściwości CloudServiceConfiguration.

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

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|id|Ciąg|Identyfikator puli.|
|displayName|Ciąg|Nazwa wyświetlana puli.|
|vmSize|Ciąg|Rozmiar maszyn wirtualnych w puli. Wszystkie maszyny wirtualne w puli mają taki sam rozmiar. <br/><br/> Aby uzyskać informacji na temat dostępnych rozmiarów maszyn wirtualnych dla usług w chmurze pul (pul utworzonych za pomocą cloudServiceConfiguration), zobacz [rozmiary usług Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Usługa Batch obsługuje wszystkie rozmiary maszyn wirtualnych usług chmury, oprócz `ExtraSmall`.<br/><br/> Informacji na temat maszyny Wirtualnej dostępne rozmiary pul przy użyciu obrazów z rynku maszyn wirtualnych (pul utworzonych za pomocą virtualMachineConfiguration) dla [rozmiary maszyn wirtualnych](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) lub [rozmiary wirtualnych Maszyny](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Usługa Batch obsługuje wszystkie rozmiary maszyn wirtualnych platformy Azure oprócz `STANDARD_A0` i maszyn z usługi Premium Storage (seria `STANDARD_GS`, `STANDARD_DS` i `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Typ złożony|Konfiguracji usługi w chmurze dla puli.|
|[virtualMachineConfiguration](#bk_vmconf)|Typ złożony|Konfiguracja maszyny wirtualnej w puli.|
|[networkConfiguration](#bk_netconf)|Typ złożony|Konfiguracja sieci dla puli.|
|resizeTimeout|Time|Limit czasu alokacji węzłów obliczeniowych w puli, określony dla ostatniej operacji zmiany rozmiaru w puli.  (Początkowego rozmiaru po utworzeniu puli jest liczona jako zmiany rozmiaru.)|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|enableAutoScale|wartość logiczna|Określa, czy rozmiar puli automatycznie dostosowuje się wraz z upływem czasu.|
|enableInterNodeCommunication|wartość logiczna|Określa, czy pula jest skonfigurowany do bezpośredniej komunikacji między węzłami.|
|isAutoPool|wartość logiczna|Określa, czy pula została utworzona za pośrednictwem mechanizmu AutoPool zadania.|
|maxTasksPerNode|Int32|Maksymalna liczba zadań, które można uruchomić jednocześnie w jednym węźle obliczeń w puli.|
|vmFillType|Ciąg|Definiuje sposób rozdzielania zadań przez usługę Partia zadań między węzły obliczeń w puli. Prawidłowe wartości są dystrybuowane lub pakietu.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|osFamily|Ciąg|Rodzina systemów operacyjnych gościa platformy Azure do zainstalowania na maszynach wirtualnych w puli.<br /><br /> Możliwe wartości:<br /><br /> **2** — 2 rodziny systemów operacyjnych, równoważne z systemu Windows Server 2008 R2 z dodatkiem SP1.<br /><br /> **3** — 3 rodziny systemów operacyjnych, odpowiednikiem w systemie Windows Server 2012.<br /><br /> **4** — 4 rodziny systemów operacyjnych, równoważne do systemu Windows Server 2012 R2.<br /><br /> Aby uzyskać więcej informacji, zobacz [wersji systemu operacyjnego gościa Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Ciąg|Wersja systemu operacyjnego gościa platformy Azure do zainstalowania na maszynach wirtualnych w puli.<br /><br /> Wartość domyślna to **\*** określający najnowszej wersji systemu operacyjnego dla określonej rodziny.<br /><br /> Aby uzyskać inne dozwolonych wartości, zobacz [wersji systemu operacyjnego gościa Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Typ złożony|Określa informacje dotyczące platformy lub obrazu z witryny Marketplace do użycia.|
|nodeAgentSKUId|Ciąg|Jednostka SKU agenta węzła partii aprowizowane w węźle obliczeniowym.|
|[windowsConfiguration](#bk_winconf)|Typ złożony|Określa ustawienia systemu operacyjnego Windows na maszynie wirtualnej. Ta właściwość nie może być określona, jeśli imageReference odwołuje się do obrazu systemu operacyjnego Linux.|

###  <a name="bk_imgref"></a> imageReference

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|Wydawcy|Ciąg|Wydawca obrazu.|
|oferty|Ciąg|Oferta obrazu.|
|jednostka SKU|Ciąg|Jednostka SKU obrazu.|
|version|Ciąg|Wersja obrazu.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|enableAutomaticUpdates|Wartość logiczna|Wskazuje, czy maszyna wirtualna jest włączona dla aktualizacji automatycznych. Jeśli ta właściwość nie zostanie określony, wartość domyślna to true.|

###  <a name="bk_netconf"></a> Konfiguracja sieci

|Nazwa elementu|Typ|Uwagi|
|------------------|--------------|----------|
|subnetId|Ciąg|Określa identyfikator zasobu podsieci, w którym są tworzone w puli węzłów obliczeniowych.|
