---
title: Tworzenie zasobu usług Cognitive Services w witrynie Azure portal
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do usługi Azure Cognitive Services przez tworzenie i subskrybowanie zasobu w witrynie Azure portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219479"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Tworzenie zasobu usług Cognitive Services przy użyciu portalu Azure

Ten przewodnik Szybki start umożliwia rozpoczęcie korzystania z usług Azure Cognitive Services. Po utworzeniu zasobu usługi Cognitive Service w witrynie Azure portal otrzymasz punkt końcowy i klucz do uwierzytelniania aplikacji.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)platformy Azure.

## <a name="create-a-new-azure-cognitive-services-resource"></a>Tworzenie nowego zasobu usług Azure Cognitive Services

1. Utworzyć zasób

    #### <a name="multi-service-resource"></a>[Zasób wielodochowy](#tab/multiservice)
    
    Zasób wielu usług nosi nazwę **Cognitive Services** w portalu. [Tworzenie zasobu usług Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    W tej chwili zasób wielousługowy umożliwia dostęp do następujących usług Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Przetwarzanie obrazów  | Content Moderator                                    | Rozpoznawanie twarzy               | Language Understanding (LUIS) | Analiza tekstu   |
    | Tekst usługi Translator  | Wyszukiwanie Bing w wersji 7 <br>(Web, Obraz, Aktualności, Wideo, Wizualne) | Wyszukiwanie niestandardowe Bing | Wyszukiwanie jednostek Bing            | Automatyczne sugerowanie Bing |
    | Sprawdzanie pisowni Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Zasób z jedną usługą](#tab/singleservice)

    Poniższe łącza za pomocą poniższych łączy można utworzyć zasób dla dostępnych usług Cognitive Services:

    | Obraz                      | Mowa                  | Język                          | Decyzja             | Wyszukiwanie                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Wizja komputerowa](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Usługi mowy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Wciągający czytnik](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Narzędzie do wykrywania anomalii](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Interfejs API wyszukiwania Bing w wersji 7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Usługa niestandardowej wizji](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Rozpoznawanie osoby mówiącej](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Rozumienie języka (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Moderator zawartości](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Wyszukiwanie niestandardowe Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Rozpoznawanie twarzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizacja](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Wyszukiwanie jednostek Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Rozpoznawanie pisma odręcznego](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Sprawdzanie pisowni bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Tekst tłumacza](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatyczne sugerowanie Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na stronie **Tworzenie** podaj następujące informacje:

    #### <a name="multi-service-resource"></a>[Zasób wielodochowy](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług kognitywnych. Na przykład *MyCognitiveServicesResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność środowiska wykonawczego zasobu. |
    | **Warstwa cenowa** | Koszt konta usług Cognitive Services zależy od dostępnych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API .
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób usług Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kliknij przycisk **Utwórz**.

    #### <a name="single-service-resource"></a>[Zasób z jedną usługą](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług kognitywnych. Na przykład *TextAnalyticsResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność środowiska wykonawczego zasobu. |
    | **Warstwa cenowa** | Koszt konta usług Cognitive Services zależy od dostępnych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API .
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób usług Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kliknij przycisk **Utwórz**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Pobierz klucze do zasobu

1. Po pomyślnym wdrożeniu zasobu kliknij **pozycję Przejdź do zasobu** w obszarze **Następne kroki**.

    ![Wyszukiwanie usług Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. W okienku szybkiego startu, które zostanie otwarte, można uzyskać dostęp do klucza i punktu końcowego.

    ![Pobierz klucz i punkt końcowy](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych zasobów zawartych w grupie.

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Lokalizowanie grupy zasobów zawierającej zasób, który ma zostać usunięty
3. Kliknij prawym przyciskiem myszy listę grupy zasobów. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

## <a name="see-also"></a>Zobacz też

* [Uwierzytelnij żądania w usługach Azure Cognitive Services](authentication.md)
* [Co to są usługi Azure Cognitive Services?](Welcome.md)
* [Obsługa języka naturalnego](language-support.md)
* [Obsługa kontenerów platformy Docker](cognitive-services-container-support.md)
