---
title: Wdrażanie kontenera LUIS w usłudze Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener LUIS w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689450"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Wdrażanie kontenera Language Understanding (LUIS) w usłudze Azure Container Instances

Dowiedz się, jak wdrożyć kontener Cognitive Services [Luis](luis-container-howto.md) do usługi Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu wykrywania anomalii. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Kontener LUIS wymaga pliku modelu `.gz`, który jest pobierany w czasie wykonywania. Kontener musi być w stanie uzyskać dostęp do tego pliku modelu za pośrednictwem instalacji woluminu z wystąpienia kontenera. Aby uzyskać informacje na temat tworzenia udziału plików platformy Azure, zobacz [Tworzenie udziału plików](../../storage/files/storage-how-to-create-file-share.md). Zanotuj nazwę konta usługi Azure Storage, klucz i nazwę udziału plików, ponieważ będą potrzebne później.

### <a name="export-and-upload-packaged-luis-app"></a>Eksportuj i przekazuj spakowane aplikacje LUIS

Aby można było przekazać model LUIS (spakowaną aplikację) do udziału plików platformy Azure, należy <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">najpierw <span class="docon docon-navigate-external x-hidden-focus"> </span>wyeksportować go z portalu usługi Luis </a>. W Azure Portal przejdź do strony **Przegląd** zasobu konto magazynu i wybierz pozycję **udziały plików**. Wybierz ostatnio utworzoną nazwę udziału plików, a następnie wybierz przycisk **Przekaż** .

> [!div class="mx-imgBorder"]
> ![przekazać do udziału plików](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Przekaż plik modelu LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
