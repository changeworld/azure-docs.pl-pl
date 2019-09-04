---
title: Utwórz zasób Cognitive Services w Azure Portal
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z usługą Azure Cognitive Services, tworząc i subskrybując zasób w Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274659"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Tworzenie zasobu Cognitive Services przy użyciu Azure Portal

Skorzystaj z tego przewodnika Szybki Start, aby utworzyć zasób usługi Azure Cognitive Services przy użyciu Azure Portal. Po pomyślnym utworzeniu zasobu Cognitive Services zostanie uzyskany punkt końcowy i klucz, którego można użyć do uwierzytelniania aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Tworzenie nowego zasobu usługi Azure Cognitive Services

Przed utworzeniem zasobu Cognitive Services należy mieć grupę zasobów platformy Azure, która będzie zawierać zasób. W przypadku tworzenia nowego zasobu można utworzyć nową grupę zasobów lub użyć istniejącej grupy. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i kliknij pozycję **+ Utwórz zasób**.

    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Jak wyjaśniono wcześniej, można utworzyć zasób Cognitive Services na dwa sposoby — za pomocą zasobu wielousługowego lub zasobu pojedynczego usługi.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Zasób obejmujący wiele usług](#tab/multiservice)

    Aby utworzyć zasób obejmujący wiele usług, wprowadź **Cognitive Services** na pasku wyszukiwania.

    ![Wyszukaj Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    Na stronie Cognitive Services wybierz pozycję **Utwórz**.

    ![Utwórz konto Cognitive Services](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Zasób pojedynczego usługi](#tab/singleservice)

    Aby wyświetlić wszystkie dostępne usługi poznawcze, wybierz pozycję **AI + Machine Learning**w obszarze **Azure Marketplace**. Jeśli nie widzisz interesującej Cię usługi, kliknij pozycję **Zobacz wszystko** i przewiń do **Cognitive Services**. Kliknij pozycję **Zobacz więcej** , aby wyświetlić cały katalog Cognitive Services.

    Po wybraniu usługi kliknij pozycję **Utwórz**.
    
    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. Na stronie **Tworzenie** podaj następujące informacje:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Zasób obejmujący wiele usług](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług poznawczej. Na przykład *MyCognitiveServicesResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Location** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. Zapamiętaj swoją lokalizację platformy Azure, ponieważ może być potrzebna podczas wywoływania Cognitive Services platformy Azure. |
    | **Warstwa cenowa** | Koszt konta Cognitive Services zależy od wybranych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kliknij przycisk **Utwórz**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Zasób pojedynczego usługi](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług poznawczej. Na przykład *TextAnalyticsResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Location** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. Zapamiętaj swoją lokalizację platformy Azure, ponieważ może być potrzebna podczas wywoływania Cognitive Services platformy Azure. |
    | **Warstwa cenowa** | Koszt konta Cognitive Services zależy od wybranych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kliknij przycisk **Utwórz**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Pobierz klucze dla zasobu

Po pomyślnym utworzeniu zasobu otrzymasz wyskakujące powiadomienie w prawym górnym rogu ekranu. Na stronie powiadomienia kliknij pozycję **Przejdź do zasobu** , aby wyświetlić utworzony zasób usługi poznawczej. 

![Przejdź do zasobu usługi poznawczej](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

W otwartym okienku szybkiego startu możesz uzyskać dostęp do punktu końcowego i klucza.

![Pobierz klucz i punkt końcowy](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Warstwy cenowe i rozliczenia

Warstwy cenowe (oraz opłata naliczana) są zależne od liczby wysyłanych transakcji przy użyciu informacji o uwierzytelnianiu. Każda warstwa cenowa określa:
* Maksymalna liczba dozwolonych transakcji na sekundę (TPS).
* funkcje usługi są włączone w ramach warstwy cenowej.
* Koszt wstępnie zdefiniowanej liczby transakcji. Przekroczenie tej kwoty spowoduje dodatkową opłatą określoną w [szczegółach cennika](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) usługi.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów znajdujących się w grupie.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk więcej (...) po prawej stronie listy.
3. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

## <a name="see-also"></a>Zobacz także

* [Uwierzytelnianie żądań w usłudze Azure Cognitive Services](authentication.md)
* [Co to jest platforma Azure Cognitive Services?](Welcome.md)
* [Obsługa języka naturalnego](language-support.md)
* [Obsługa kontenerów platformy Docker](cognitive-services-container-support.md)
