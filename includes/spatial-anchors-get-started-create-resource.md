---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305145"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

W okienku nawigacji po lewej stronie w witrynie Azure portal wybierz **Utwórz zasób**.

Użyj pola wyszukiwania, aby wyszukać **przestrzenne kotwic**.

   ![Wyszukiwanie usługi Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Wybierz **przestrzenne kotwic**. W oknie dialogowym wybierz **Utwórz**.

W **przestrzenne konta kotwic** okno dialogowe:

- Wprowadź nazwę unikatowy zasób.
- Wybierz subskrypcję, której chcesz dołączyć zasobu.
- Utwórz grupę zasobów, wybierając **Utwórz nową**. Nadaj mu nazwę **myResourceGroup** i wybierz **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Wybierz lokalizację (region), w której chcesz umieścić zasobu.
- Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

   ![Tworzenie zasobu](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po utworzeniu zasobu możesz wyświetlić właściwości zasobu. Kopiuj zasób **Accountid** wartości do edytora tekstu, ponieważ będzie on potrzebny później.

   ![Właściwości zasobu](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

W obszarze **ustawienia**, wybierz opcję **klucz**. Kopiuj **klucz podstawowy** wartości do edytora tekstu. Ta wartość jest `Account Key`. Będzie on potrzebny później.

   ![Klucz konta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
