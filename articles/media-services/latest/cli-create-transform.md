---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie przekształcenia | Microsoft Docs
description: Tworzenie przekształcenia interfejsu wiersza polecenia przy użyciu skryptu interfejsu wiersza polecenia platformy Azure.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 7fea8d997d25d6075e30620e7fd3527003c6a8bb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128792"
---
# <a name="cli-example-create-a-transform"></a>Przykład użycia interfejsu wiersza polecenia: Tworzenie przekształcenia

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”). Zawsze należy sprawdzić, czy przekształcenie z wybraną nazwą i „przepisem” już istnieje. Jeśli tak, należy użyć go ponownie.

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Można określić tylko ścieżkę do niestandardowego pliku JSON domyślnego kodera dla [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), zobacz przykład [kodowania z niestandardowym przekształceniem](custom-preset-cli-howto.md) .
>
> Nie można przekazać nazwy pliku podczas korzystania z [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Następne kroki

[AZ AMS Transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
