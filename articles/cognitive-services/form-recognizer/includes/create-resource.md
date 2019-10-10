---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: c2d3119283b5b75eeca6b0e0737f62eee00e81c6
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237421"
---
Po otrzymaniu dostępu do aparatu rozpoznawania formularzy otrzymasz powitalną wiadomość e-mail z kilkoma łączami i zasobami. Użyj linku "Azure Portal" w tym komunikacie, aby otworzyć Azure Portal i utworzyć zasób aparatu rozpoznawania formularza. W okienku **Tworzenie** podaj następujące informacje:

|    |    |
|--|--|
| **Nazwa** | Opisowa nazwa zasobu. Zalecamy użycie nazwy opisowej, na przykład *MyNameFormRecognizer*. |
| **Subskrypcja** | Wybierz subskrypcję platformy Azure, której udzielono dostępu. |
| **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
| **Warstwa cenowa** | Koszt zasobu zależy od wybranej warstwy cenowej i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
| **Grupa zasobów** | [Grupa zasobów platformy Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , która będzie zawierać zasób. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

> [!IMPORTANT]
> Zwykle podczas tworzenia zasobu usługi poznawczej w Azure Portal można utworzyć klucz subskrypcji dla wielu usług (używany przez wiele usług poznawczych) lub klucz subskrypcji jednej usługi (używany tylko w przypadku konkretnej usługi poznawczej). Jednak ponieważ aparat rozpoznawania formularzy jest wersją zapoznawczą, nie jest uwzględniony w subskrypcji usługi MFA i nie można utworzyć subskrypcji pojedynczej usługi, chyba że zostanie użyty link podany w powitalnej wiadomości e-mail.

Po zakończeniu wdrażania zasobu aparatu rozpoznawania formularzy Znajdź i wybierz go z listy **wszystkie zasoby** w portalu. Następnie wybierz kartę **klucze** , aby wyświetlić klucze subskrypcji. Oba te klucze umożliwiają aplikacji dostęp do zasobu. Skopiuj wartość **klucza 1**.