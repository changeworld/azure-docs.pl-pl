---
title: 'Szybki Start: Tworzenie konta usług Cognitive Services w witrynie Azure portal'
titleSuffix: Microsoft Docs
description: Dowiedz się, jak utworzyć konto umożliwiające dostęp do usług Azure Cognitive Services.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: aahi
ms.reviewer: gibattag
ms.openlocfilehash: 232e0aa64eef4f6829813efff6e0abffd0a78518
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605134"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Szybki Start: Tworzenie konta usług Cognitive Services w witrynie Azure portal

Użyj tego przewodnika Szybki Start, aby rozpocząć korzystanie z usług Azure Cognitive Services. Te usługi są reprezentowane przez platformę Azure [zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), które pozwalają połączyć się z co najmniej jedną z wielu Cognitive Services interfejsami API dostępnymi.

## <a name="prerequisites"></a>Wymagania wstępne

* Ważnej subskrypcji platformy Azure. Możesz [Tworzenie konta usługi](https://azure.microsoft.com/free/) za darmo.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Tworzyć i subskrybować zasobów usług Azure Cognitive Services

1. Zaloguj się do [witryny Azure portal](http://portal.azure.com)i kliknij przycisk **+ Utwórz zasób**.
    
    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. W obszarze Azure Marketplace wybierz **SI i uczenie maszynowe**. Jeśli nie widzisz usługi interesuje Cię, kliknij polecenie **holograficznych** Aby wyświetlić cały katalog interfejsów API usług Cognitive Services.

    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na **Utwórz** Podaj następujące informacje:

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług cognitive services. Firma Microsoft zaleca używanie nazwę opisową, na przykład *MyNameFaceAPIAccount*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi cognitive Services. Różne lokalizacje może wprowadzić opóźnienie, ale nie mają wpływu na dostępność Twojego zasobu w czasie wykonywania. |
    | **Warstwa cenowa** | Koszt konta usług Cognitive Services zależy od wybranych opcji i użycie. Aby uzyskać więcej informacji, zobacz omówienie interfejsu API [cennik](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupa zasobów** | [Grupy zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) który będzie zawierał zasobu usług Cognitive Services. Można utworzyć nową grupę lub Dodaj go do istniejących grup. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Dostęp do zasobu 

Po utworzeniu zasobu możesz do niego dostęp na pulpicie nawigacyjnym platformy Azure, jeśli został przypięty. W przeciwnym razie można znaleźć w **grup zasobów**.

W ramach zasobu usług Cognitive Services, można użyć adresu URL punktu końcowego i klucze w **Przegląd** sekcji, aby rozpocząć tworzenie interfejsu API wywołuje w swoich aplikacjach.

![Ekran zasobów](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek przetwarzania interfejsu API języka C# na komputer](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>Zobacz także

* [Szybki Start: Wyodrębnianie tekstu odręcznego z obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Samouczek: Tworzenie aplikacji do wykrywania i ramki twarzy na obrazie](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Tworzenie strony sieci Web wyszukiwania niestandardowego](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrowanie Language Understanding (LUIS) z botem przy użyciu platformy Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
