---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752852"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

1. Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Utwórz zasób**.

3. Wyszukaj ciąg „Spatial Anchors” na pasku wyszukiwania.

   ![Wyszukiwanie usługi Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Wybierz pozycję **Spatial Anchors (wersja zapoznawcza)**, aby otworzyć okno dialogowe, i wybierz pozycję **Utwórz**.

5. W formularzu **Konto usługi Spatial Anchors** wykonaj następujące czynności:

   1. Określ unikatową nazwę zasobu.
   2. Wybierz subskrypcję do dołączenia do zasobu.
   3. Utwórz grupę zasobów, wybierając pozycję **Utwórz nową**, a następnie nadaj grupie zasobów nazwę **myResourceGroup** i wybierz przycisk **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Wybierz lokalizację (region), w której zasób zostanie umieszczony.
   5. Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

   ![Tworzenie zasobu](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Po pomyślnym ukończeniu tworzenia zasobu możesz wyświetlić jego właściwości. Zanotuj wartość **identyfikatora konta** zasobu, ponieważ będzie ona potrzebna później.

   ![Wyświetlanie właściwości zasobu](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. W obszarze **Ustawienia** wybierz pozycję **Klucz** i zanotuj wartość pozycji **Klucz podstawowy**. Ta wartość to `Account Key` i zostanie ona użyta później.

   ![Wyświetlanie klucza konta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
