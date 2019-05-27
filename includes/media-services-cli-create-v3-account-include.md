---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: feec6a695ad867d26d32904d020648b029f9da35
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155748"
---
## <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Najpierw należy utworzyć konto usługi Media Services. W tej sekcji przedstawiono, co jest potrzebne do tworzenia konta przy użyciu wiersza polecenia platformy Azure.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów przy użyciu poniższego polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów, takich jak konta usługi Azure Media Services i skojarzone konta usługi Storage, oraz zarządzania nimi.

Można zastąpić `amsResourceGroup` z wartością.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Aby uzyskać więcej informacji na temat używania kont magazynu w usłudze Media Services, zobacz [Konta magazynu](../articles/media-services/latest/storage-account-concept.md).

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). Konta tylko obiektów blob nie są dozwolone jako **główne**. Jeśli chcesz dowiedzieć się więcej o kontach magazynu, zobacz [Opcje konta usługi Azure Storage](../articles/storage/common/storage-account-options.md). 

W tym przykładzie utworzymy konto ogólnego przeznaczenia w wersji 2 magazynu LRS w warstwie Standardowa. Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU dla środowiska produkcyjnego weź pod uwagę użycie parametru `--sku Standard_RAGRS`, co zapewnia replikację geograficzną na potrzeby zapewnienia ciągłości działania. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Poniższe polecenie tworzy konto usługi Storage, które ma zostać skojarzone z kontem usługi Media Services. W poniższym skrypcie możesz zastąpić wartość `storageaccountforams` swoją wartością. `amsResourceGroup` musi być zgodna wartość, która udostępniła dla grupy zasobów w poprzednim kroku. Nazwa konta magazynu musi mieć długość mniejszą niż 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Poniższe polecenie interfejsu wiersza polecenia platformy Azure tworzy nowe konto usługi Media Services. Można zastąpić następujące wartości: `amsaccount` `storageaccountforams` (musi odpowiadać wartości nadanej kontu magazynu) i `amsResourceGroup` (musi odpowiadać wartości nadanej grupie zasobów).

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
