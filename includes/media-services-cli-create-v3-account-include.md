---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 3157f5db58be5735fa7b194393b0ab6d0e143a91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
## <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Najpierw należy utworzyć konto usługi Media Services. W tej sekcji przedstawiono elementy potrzebne do utworzenia konta przy użyciu interfejsu wiersza polecenia w wersji 2.0.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów przy użyciu poniższego polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów, takich jak konta usługi Azure Media Services i skojarzone konta usługi Storage, oraz zarządzania nimi.

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. 

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). Konta tylko obiektów blob nie są dozwolone jako **główne**. Jeśli chcesz dowiedzieć się więcej o kontach magazynu, zobacz [Opcje konta usługi Azure Storage](../articles/storage/common/storage-account-options.md). 

Aby uzyskać więcej informacji na temat używania kont magazynu w usłudze Media Services, zobacz [Konta magazynu](../articles/media-services/latest/storage-account-concept.md).

Poniższe polecenie tworzy konto usługi Storage, które ma zostać skojarzone z kontem usługi Media Services. W poniższym skrypcie możesz zastąpić wartość `storageaccountforams` swoją wartością. Nazwa konta musi mieć długość mniejszą niż 24.

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Poniższe polecenie interfejsu wiersza polecenia platformy Azure tworzy nowe konto usługi Media Services. Można zastąpić następujące wartości: `amsaccount` `storageaccountforams` (musi odpowiadać wartości nadanej kontu magazynu) i `amsResourceGroup` (musi odpowiadać wartości nadanej grupie zasobów).

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
