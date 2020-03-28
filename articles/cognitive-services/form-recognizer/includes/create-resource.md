---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205829"
---
Przejdź do witryny <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Azure portal i" target="_blank">utwórz nowy zasób aparatu rozpoznawania formularzy, utwórz nowy zasób <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>aparatu rozpoznawania formularzy . W okienku **Tworzenie** podaj następujące informacje:

|    |    |
|--|--|
| **Nazwa** | Opisowa nazwa zasobu. Zalecamy użycie nazwy opisowej, na przykład *MyNameFormRecognizer*. |
| **Subskrypcja** | Wybierz subskrypcję platformy Azure, której udzielono dostępu. |
| **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność środowiska wykonawczego zasobu. |
| **Warstwa cenowa** | Koszt zasobu zależy od wybranego poziomu cenowego i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API .
| **Grupa zasobów** | [Grupa zasobów platformy Azure,](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) która będzie zawierać twój zasób. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

> [!IMPORTANT]
> Zwykle podczas tworzenia zasobu usługi Cognitive Service w witrynie Azure portal, masz możliwość utworzenia klucza subskrypcji wielu usług (używane w wielu usługach kognitywnych) lub klucz subskrypcji jednej usługi (używany tylko z określoną usługą poznawczą). Jednak ponieważ aparat rozpoznawania formularzy jest wersją zapoznawczą, nie jest uwzględniony w subskrypcji wielu usług i nie można utworzyć subskrypcji z jedną usługą, chyba że używasz łącza podanego w powitalnej wiadomości e-mail.

Po zakończeniu wdrażania zasobu aparatu rozpoznawania formularzy znajdź go i wybierz z listy **Wszystkie zasoby** w portalu. Następnie wybierz kartę **Szybki start,** aby wyświetlić dane subskrypcji. Zapisz wartości **Key1** i **Endpoint** w lokalizacji tymczasowej. Użyjesz ich w poniższych krokach.
