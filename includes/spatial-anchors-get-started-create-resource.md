---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67135027"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

Przejdź do <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>.

W lewym okienku nawigacji w witrynie Azure portal wybierz pozycję **Utwórz zasób**.

Użyj pola wyszukiwania, aby wyszukać **zakotwiczenia przestrzenne**.

   ![Wyszukiwanie usługi Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Wybierz **zakotwiczenia przestrzenne**. W oknie dialogowym wybierz pozycję **Utwórz**.

W oknie **dialogowym Konto zakotwiczenia przestrzennego:**

- Wprowadź unikatową nazwę zasobu przy użyciu zwykłych znaków alfanumerycznych.
- Wybierz subskrypcję, do której chcesz dołączyć zasób.
- Utwórz grupę zasobów, wybierając pozycję **Utwórz nowy**. Nazwij to **myResourceGroup** i wybierz **PRZYCISK OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Wybierz lokalizację (region), w której ma być umieszczany zasób.
- Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

   ![Tworzenie zasobu](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po utworzeniu zasobu usługa Azure Portal pokaże, że wdrożenie zostało zakończone. Kliknij pozycję **Przejdź do zasobu**.

![Zakończono wdrożenie](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Następnie można wyświetlić właściwości zasobu. Skopiuj wartość **identyfikatora konta** zasobu do edytora tekstu, ponieważ będzie potrzebna później.

   ![Właściwości zasobu](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

W obszarze **Ustawienia**wybierz **pozycję Klawisz**. Skopiuj wartość **klucza podstawowego** do edytora tekstu. Ta wartość `Account Key`jest . Będzie on potrzebny później.

   ![Klucz konta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
