---
title: Utwórz konto usług Cognitive Services w witrynie Azure portal
titlesuffix: Azure Cognitive Services
description: Jak utworzyć konta interfejsów API usługi Azure Cognitive Services w witrynie Azure portal.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: garye
ms.openlocfilehash: 7df429aa848c6f9fb9abe09fbf4357db20fcde6a
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472858"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Szybki start: Utwórz konto usług Cognitive Services w witrynie Azure portal

W tym przewodniku Szybki Start dowiesz się, jak utworzyć konto usług Azure Cognitive Services i utworzyć subskrypcję usługi jednego lub wielu usług. Te usługi są reprezentowane przez platformę Azure [zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), które umożliwiają łączenie się z jednego lub wielu interfejsów API usługi Azure Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

* Ważnej subskrypcji platformy Azure. [Tworzenie konta usługi](https://azure.microsoft.com/free/) za darmo.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Tworzyć i subskrybować zasobów usług Azure Cognitive Services

Przed rozpoczęciem pracy należy wiedzieć, że istnieją dwa rodzaje subskrypcji usług Azure Cognitive Services. Pierwsza to subskrypcji do pojedynczej usługi, takie jak przetwarzanie obrazów lub usług przetwarzania mowy. Subskrypcja jednousługowa jest ograniczona do tego zasobu. Druga to subskrypcja wielu usług dla usług Azure Cognitive Services. Ta subskrypcja zezwala przy użyciu pojedynczej subskrypcji dla większości usług Azure Cognitive Services. Ta opcja również konsoliduje rozliczeń. Zobacz [cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) Aby uzyskać dodatkowe informacje.

>[!WARNING]
> W tej chwili tych usług **nie** obsługi wielu usług kluczy: Usługa QnA Maker usług przetwarzania mowy, Custom Vision i wykrywanie anomalii.

W kolejnych sekcjach prowadzą użytkownika przez proces tworzenia subskrypcji jednej lub wielu usług.

### <a name="single-service-subscription"></a>Usługę pojedynczej subskrypcji

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij przycisk **+ Utwórz zasób**.

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
    | **Grupa zasobów** | [Grupy zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) który będzie zawierał zasobu usług Cognitive Services. Można utworzyć nową grupę lub dodaj je do wcześniej istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen.png)

### <a name="multi-service-subscription"></a>Wielu usług subskrypcji

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij przycisk **+ Utwórz zasób**.

    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Znajdź pasek wyszukiwania i wprowadź: **Cognitive Services**.

    ![Wyszukiwanie poznawcze](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Wybierz **usług Cognitive Services**.

    ![Select Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Na **Utwórz** Podaj następujące informacje:

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług cognitive services. Firma Microsoft zaleca używanie nazwę opisową, na przykład *MyCognitiveServicesAccount*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi cognitive Services. Różne lokalizacje może wprowadzić opóźnienie, ale nie mają wpływu na dostępność Twojego zasobu w czasie wykonywania. |
    | **Warstwa cenowa** | Koszt konta usług Cognitive Services zależy od wybranych opcji i użycie. Aby uzyskać więcej informacji, zobacz omówienie interfejsu API [cennik](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupa zasobów** | [Grupy zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) który będzie zawierał zasobu usług Cognitive Services. Można utworzyć nową grupę lub dodaj je do wcześniej istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

## <a name="access-your-resource"></a>Dostęp do zasobu

> [!NOTE]
> Właściciele subskrypcji można wyłączyć tworzenie konta usług Cognitive Services dla subskrypcji i grupy zasobów, stosując [usługa Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), przypisywanie definicji zasad "nie dozwolone typy zasobów" i określając **Microsoft.CognitiveServices/accounts** jako typ zasobu docelowego.

Po utworzeniu zasobu możesz do niego dostęp na pulpicie nawigacyjnym platformy Azure, jeśli został przypięty. W przeciwnym razie można znaleźć w **grup zasobów**.

W ramach zasobu usług Cognitive Services, można użyć adresu URL punktu końcowego i klucze w **Przegląd** sekcji, aby rozpocząć tworzenie interfejsu API wywołuje w swoich aplikacjach.

![Ekran zasobów](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uwierzytelnianie żądań usług Azure Cognitive Services](authentication.md)

## <a name="see-also"></a>Zobacz także

* [Szybki start: Wyodrębnianie tekstu odręcznego z obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Samouczek: Utwórz aplikację, aby wykryć i ramek twarzy na obrazie](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Tworzenie strony sieci Web wyszukiwania niestandardowego](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrowanie Language Understanding (LUIS) z botem przy użyciu platformy Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
