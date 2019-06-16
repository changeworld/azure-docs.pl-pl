---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "67135027"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

W okienku nawigacji po lewej stronie w witrynie Azure portal wybierz **Utwórz zasób**.

Użyj pola wyszukiwania, aby wyszukać **przestrzenne kotwic**.

   ![Wyszukiwanie usługi Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Wybierz **przestrzenne kotwic**. W oknie dialogowym wybierz **Utwórz**.

W **przestrzenne konta kotwic** okno dialogowe:

- Wprowadź nazwę unikatowy zasób za pomocą regularnego znaków alfanumerycznych.
- Wybierz subskrypcję, której chcesz dołączyć zasobu.
- Utwórz grupę zasobów, wybierając **Utwórz nową**. Nadaj mu nazwę **myResourceGroup** i wybierz **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Wybierz lokalizację (region), w której chcesz umieścić zasobu.
- Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

   ![Tworzenie zasobu](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po utworzeniu zasobu portalu Azure pokaże, że wdrożenie zostało zakończone. Kliknij pozycję **Przejdź do zasobu**.

![Zakończono wdrożenie](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Następnie możesz wyświetlić właściwości zasobu. Kopiuj zasób **Accountid** wartości do edytora tekstu, ponieważ będzie on potrzebny później.

   ![Właściwości zasobu](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

W obszarze **ustawienia**, wybierz opcję **klucz**. Kopiuj **klucz podstawowy** wartości do edytora tekstu. Ta wartość jest `Account Key`. Będzie on potrzebny później.

   ![Klucz konta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
