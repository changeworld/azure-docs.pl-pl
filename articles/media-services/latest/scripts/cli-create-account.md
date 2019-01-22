---
title: Tworzenie konta usługi Azure Media Services — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Użyj skryptu interfejsu wiersza polecenia platformy Azure, aby utworzyć konto usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 2eeb47c2e0f96eca1ca9b852a2be6ca3102ba71e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353349"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Przykład użycia interfejsu wiersza polecenia: Tworzenie konta usługi Azure Media Services

Skrypt interfejsu wiersza polecenia platformy Azure w tym temacie pokazuje sposób tworzenia konta usługi Azure Media Services. Konto usługi Media Services oraz powiązane z nią konto magazynu muszą być znajdować się w tym samym centrum danych i tej samej grupie zasobów.

## <a name="prerequisites"></a>Wymagania wstępne 

Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Tworzy konto usługi Media Services. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Tworzy jednostkę usługi i hasło oraz konfiguruje je na potrzeby dostępu do usługi Azure Media Services. 
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../cli-samples.md).
