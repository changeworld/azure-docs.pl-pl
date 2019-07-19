---
title: Utwórz zasób Cognitive Services w Azure Portal
titlesuffix: Azure Cognitive Services
description: Rozpocznij pracę z usługą Azure Cognitive Services, tworząc i subskrybując zasób w Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: e9616f3014288e4b2580f474c49c646928db5a08
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334247"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Tworzenie zasobu Cognitive Services przy użyciu Azure Portal

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć pracę z usługą Azure Cognitive Services przy użyciu Azure Portal. Cognitive Services są reprezentowane przez [zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) platformy Azure utworzone w ramach subskrypcji platformy Azure. Po utworzeniu zasobu Użyj kluczy i punktu końcowego wygenerowanego do uwierzytelniania aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Tworzenie nowego zasobu usługi Azure Cognitive Services

Przed utworzeniem zasobu Cognitive Services należy mieć grupę zasobów platformy Azure, która będzie zawierać zasób. W przypadku tworzenia nowego zasobu można utworzyć nową grupę zasobów lub użyć istniejącej grupy. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i kliknij pozycję **+ Utwórz zasób**.

    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Dostępne Cognitive Services można znaleźć w następujących sposób:
    * Użyj paska wyszukiwania i wprowadź nazwę usługi, którą chcesz subskrybować.
        * Aby utworzyć zasób obejmujący wiele usług, wprowadź **Cognitive Services** na pasku wyszukiwania, a następnie wybierz zasób **Cognitive Services** .

        ![Wyszukaj Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Aby wyświetlić wszystkie dostępne usługi poznawcze, wybierz pozycję **AI + Machine Learning**w obszarze **Azure Marketplace**. Jeśli nie widzisz interesującej Cię usługi, kliknij pozycję **Zobacz wszystko** i przewiń do **Cognitive Services**. Kliknij przycisk **więcej** , aby wyświetlić cały katalog interfejsy API usług Cognitive Services.
    
        ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na stronie **Tworzenie** podaj następujące informacje:

    > [!IMPORTANT]
    > Zapamiętaj swoją lokalizację platformy Azure, ponieważ może być potrzebna podczas wywoływania Cognitive Services platformy Azure.

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług poznawczej. Na przykład *MyCognitiveServicesAccount*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Location** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
    | **Warstwa cenowa** | Koszt konta Cognitive Services zależy od wybranych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
    | **Grupa zasobów** | [Grupa zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , która będzie zawierać zasób Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>Pobierz klucze dla zasobu

Po utworzeniu zasobu możesz uzyskać do niego dostęp z poziomu pulpitu nawigacyjnego platformy Azure, jeśli został on przypięty. W przeciwnym razie można je znaleźć w **grupach zasobów**. Po wybraniu zasobu można pobrać klucze, wybierając pozycję **klucze** w obszarze **Zarządzanie zasobami**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

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
