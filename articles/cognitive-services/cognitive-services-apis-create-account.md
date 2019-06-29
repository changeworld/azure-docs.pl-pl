---
title: Utwórz konto usług Cognitive Services w witrynie Azure portal
titlesuffix: Azure Cognitive Services
description: Jak utworzyć konta interfejsów API usługi Azure Cognitive Services w witrynie Azure portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445850"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Tworzenie konta usług Cognitive Services w witrynie Azure portal

W tym przewodniku Szybki Start dowiesz się, jak utworzyć konto usług Azure Cognitive Services i Utwórz konto, które ma subskrypcję usługi jednego lub wielu usług. Te usługi są reprezentowane przez platformę Azure [zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), które umożliwiają łączenie się z jednego lub wielu interfejsów API usługi Azure Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

* Ważnej subskrypcji platformy Azure. [Tworzenie konta usługi](https://azure.microsoft.com/free/) za darmo.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Utwórz nowy zasób usług Azure Cognitive Services

Przed utworzeniem zasobu, musi mieć grupę zasobów platformy Azure. Każde konto usług Cognitive Services (i jego skojarzonego zasobu platformy Azure) musi należeć do grupy zasobów platformy Azure. Po utworzeniu konta masz opcję, aby utworzyć nową grupę zasobów lub użyć istniejącego. W tym artykule przedstawiono sposób tworzenia nowej grupy zasobów.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij przycisk **+ Utwórz zasób**.

    ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Usługi Cognitive Services za pomocą można znaleźć w następujący sposób:
    * Za pomocą paska wyszukiwania, a następnie wprowadź nazwę usługi, które chcesz subskrybować.
        * Aby utworzyć zasób dla wielu usług subskrypcji, wprowadź **usług Cognitive Services** w wyszukiwaniu paska i wybierz **usług Cognitive Services** zasobów.

        ![Wyszukiwanie poznawcze](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Aby wyświetlić wszystkie dostępne usługi cognitive services, wybierz **SI i uczenie maszynowe**w obszarze **portalu Azure Marketplace**. Jeśli nie widzisz usługi interesuje Cię, kliknij polecenie **holograficznych** i przewiń do **usług Cognitive Services**. Kliknij przycisk **więcej** Aby wyświetlić cały katalog interfejsów API usług Cognitive Services.
    
        ![Wybierz interfejsy API usług Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na **Utwórz** Podaj następujące informacje:

    > [!IMPORTANT]
    > Usługi lokalizacji platformy Azure, należy pamiętać, ponieważ może on potrzebny podczas wywoływania usług Azure Cognitive Services.

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług cognitive services. Firma Microsoft zaleca używanie nazwę opisową, na przykład *MyCognitiveServicesAccount*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Location** | Lokalizacja wystąpienia usługi cognitive Services. Różne lokalizacje może wprowadzić opóźnienie, ale nie mają wpływu na dostępność Twojego zasobu w czasie wykonywania. |
    | **Warstwa cenowa** | Koszt konta usług Cognitive Services zależy od wybranych opcji i użycie. Aby uzyskać więcej informacji, zobacz omówienie interfejsu API [cennik](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupa zasobów** | [Grupy zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) który będzie zawierał zasobu usług Cognitive Services. Można utworzyć nową grupę lub dodaj je do wcześniej istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Uzyskiwanie kluczy subskrypcji

Po utworzeniu zasobu możesz do niego dostęp na pulpicie nawigacyjnym platformy Azure, jeśli został przypięty. W przeciwnym razie można znaleźć w **grup zasobów**. Po wybraniu zasobu, zaznaczając można uzyskać klucze **klucze** w obszarze **zarządzania zasobami**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupa zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych zasobów skojarzonych z grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk więcej (...) po prawej stronie listy.
3. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

## <a name="see-also"></a>Zobacz także

* [Uwierzytelnianie żądań usług Azure Cognitive Services](authentication.md)
* [Co to są usługi Azure Cognitive Services?](Welcome.md)
* [Obsługuje język naturalny](language-support.md)
* [Obsługa kontenerów platformy docker](cognitive-services-container-support.md)
