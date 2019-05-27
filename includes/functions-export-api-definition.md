---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131725"
---
## <a name="export-an-api-definition"></a>Eksportowanie definicji interfejsu API
Masz definicji interfejsu OpenAPI dla funkcji, z [Tworzenie definicji interfejsu OpenAPI dla funkcji](../articles/azure-functions/functions-openapi-definition.md). Następnym krokiem w ramach tego procesu jest wyeksportować definicję interfejsu API usługi PowerApps i Microsoft Flow można używać go w niestandardowego interfejsu API.

> [!IMPORTANT]
> Należy pamiętać, że użytkownik musi być podpisany na platformie Azure przy użyciu tych samych poświadczeń, których używasz dla usługi PowerApps i Microsoft Flow dzierżawy. Dzięki temu platformy Azure, aby utworzyć niestandardowy interfejs API i udostępnić go zarówno dla usługi PowerApps i Microsoft Flow.

1. W [witryny Azure portal](https://portal.azure.com), kliknij nazwę swojej aplikacji funkcji (takich jak **function-demo-energy**) > **funkcje platformy** > **definicji interfejsu API** .

    ![Definicja interfejsu API](media/functions-export-api-definition/api-definition.png)

1. Kliknij przycisk **eksportowanie do usługi PowerApps i Flow**.

    ![Źródło definicji interfejsu API](media/functions-export-api-definition/export-api-1.png)

1. W okienku po prawej stronie Użyj ustawień określonych w tabeli.

    |Ustawienie|Opis|
    |--------|------------|
    |**Tryb eksportu**|Wybierz **Express** do automatycznego generowania niestandardowego interfejsu API. Wybieranie **ręczne** definicji eksportowanie interfejsu API, ale następnie należy zaimportować go do usług PowerApps i Microsoft Flow ręcznie. Aby uzyskać więcej informacji, zobacz [eksportowanie do usług PowerApps i Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Środowisko**|Wybierz środowisko, w którym mają być zapisywane niestandardowego interfejsu API. Aby uzyskać więcej informacji, zobacz [środowiska — omówienie (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) lub [środowiska — omówienie (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nazwa niestandardowego interfejsu API**|Wprowadź nazwę, na przykład `Turbine Repair`.|
    |**Nazwa klucza interfejsu API**|Wprowadź nazwę, która powinna zostać wyświetlona twórcy aplikacji i przepływów w Interfejsie użytkownika niestandardowego interfejsu API. Należy zauważyć, że przykład zawiera przydatne informacje.|
 
    ![Eksportowanie do usług PowerApps i Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Kliknij przycisk **OK**. Niestandardowy interfejs API jest teraz utworzone i dodane do środowiska, wskazana.