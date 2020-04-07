---
title: Wdrażanie kontenera usługi LUIS w wystąpieniach kontenera platformy Azure
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera usługi LUIS w wystąpieniu kontenera platformy Azure i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: f1a0a08351a03e46d6c3a1e82b68ecea6e36c015
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757248"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Wdrażanie kontenera opisu języka (LUIS) w wystąpieniach kontenera platformy Azure

Dowiedz się, jak wdrożyć kontener [usługi Luis usług](luis-container-howto.md) Cognitive Services w [wystąpieniach kontenera](https://docs.microsoft.com/azure/container-instances/)Platformy Azure. Ta procedura pokazuje tworzenie zasobu detektora anomalii. Następnie omówimy ciągnięcie skojarzonego obrazu kontenera. Na koniec podkreślamy możliwość wykonywania aranżacji dwóch z przeglądarki. Korzystanie z kontenerów można przenieść uwagę deweloperów od zarządzania infrastrukturą zamiast skupiać się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Kontener usługi LUIS `.gz` wymaga pliku modelu, który jest pobierany w czasie wykonywania. Kontener musi mieć możliwość uzyskania dostępu do tego pliku modelu za pośrednictwem instalacji woluminu z container wystąpienie. Aby uzyskać informacje na temat tworzenia udziału plików platformy Azure, zobacz [tworzenie udziału plików](../../storage/files/storage-how-to-create-file-share.md). Zanotuj nazwę konta, klucza i nazwy udziału plików usługi Azure Storage, ponieważ będzie ich później potrzebne.

### <a name="export-and-upload-packaged-luis-app"></a>Eksportowanie i przekazywanie spakowanych aplikacji USŁUGI LUIS

Aby przekazać model usługi LUIS (spakowana aplikacja) do udziału plików platformy Azure, musisz <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">najpierw <span class="docon docon-navigate-external x-hidden-focus"> </span>wyeksportować go z portalu usługi LUIS. </a> W witrynie Azure Portal przejdź do strony **Przegląd** zasobu konta magazynu i wybierz pozycję **Udziały plików**. Wybierz ostatnio utworzoną nazwę udziału plików, a następnie wybierz przycisk **Przekaż.**

> [!div class="mx-imgBorder"]
> ![Przekazywanie do udziału plików](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Przekaż plik modelu usługi LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
