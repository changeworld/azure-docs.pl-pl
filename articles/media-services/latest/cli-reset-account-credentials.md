---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — resetowanie poświadczeń konta | Microsoft Docs
description: Resetowanie poświadczeń konta i przywracanie ustawień pliku app.config przy użyciu skryptu interfejsu wiersza polecenia.
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
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 63f2abe7a3890efbaf4c79186467a3eb20d8afda
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382991"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Przykład interfejsu wiersza polecenia platformy Azure: resetowanie poświadczeń konta

Skrypt interfejsu wiersza polecenia platformy Azure pokazuje sposób resetowania poświadczeń konta i przywracania ustawień pliku app.config.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Przykładowy skrypt

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Następne kroki

* [az ams](/cli/azure/ams)
* [Resetowanie poświadczeń](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
