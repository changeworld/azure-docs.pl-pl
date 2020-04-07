---
title: Tworzenie zasobu usługi LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: 88289686e5b091ef3ec309ee9b54ee0f895c8c22
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754378"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Tworzenie zasobów usługi LUIS w witrynie Azure portal

1. Użyj [tego łącza,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) aby rozpocząć tworzenie zasobów usługi LUIS w witrynie Azure portal.

1. Wprowadź wszystkie wymagane ustawienia:

    |Nazwa|Przeznaczenie|
    |--|--|
    |Nazwa subskrypcji| subskrypcji, która zostanie naliczona za zasób.|
    |Grupa zasobów| Niestandardowa nazwa grupy zasobów, którą wybierzesz lub utworzysz. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.|
    |Nazwa| Wybrana nazwa niestandardowa używana jako niestandardowa poddomena dla kwerend końcowych tworzenia i przewidywania.|
    |Lokalizacja tworzenia|Region skojarzony z modelem.|
    |Warstwa cenowa tworzenia|Warstwa cenowa określa maksymalną transakcję na sekundę i miesiąc.|
    |Lokalizacja środowiska uruchomieniowego|Region skojarzony z opublikowanym czasem wykonywania punktu końcowego przewidywania.|
    |Warstwa cenowa środowiska uruchomieniowego|Warstwa cenowa określa maksymalną transakcję na sekundę i miesiąc.|

    > [!div class="mx-imgBorder"]
    > [![Tworzenie zasobu opisu języka](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Kliknij **przycisk Przejrzyj + utwórz** i poczekaj na utworzenie zasobu.
1. Po utworzeniu obu zasobów, nadal w witrynie Azure portal, wybierz nowy **zasób** tworzenia, a następnie Szybki start, aby uzyskać adres **URL punktu końcowego** tworzenia i **klucz** do tworzenia programowo.

> [!TIP]
> Aby użyć zasobów, w portalu usługi LUIS [przypisz zasoby](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).