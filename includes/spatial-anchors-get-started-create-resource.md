---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: cee446e54a37ce47fc82d72724e831b8270f3769
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908488"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

1. Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Utwórz zasób**.

3. Wyszukaj ciąg „Spatial Anchors” na pasku wyszukiwania.

   ![Wyszukiwanie usługi Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Wybierz **przestrzenne kotwic** aby otworzyć okno dialogowe i wybrać **Utwórz**.

5. W formularzu **Konto usługi Spatial Anchors** wykonaj następujące czynności:

   1. Określ unikatową nazwę zasobu.
   2. Wybierz subskrypcję do dołączenia do zasobu.
   3. Utwórz grupę zasobów, wybierając pozycję **Utwórz nową**, a następnie nadaj grupie zasobów nazwę **myResourceGroup** i wybierz przycisk **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Wybierz lokalizację (region), w której zasób zostanie umieszczony.
   5. Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

   ![Tworzenie zasobu](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Po pomyślnym ukończeniu tworzenia zasobu możesz wyświetlić jego właściwości. Kopiuj zasób **Accountid** wartości do edytora tekstu, ponieważ będzie on wymagane później.

   ![Wyświetlanie właściwości zasobu](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. W obszarze **ustawienia**, wybierz opcję **klucz**, a następnie skopiuj do edytora tekstu **klucz podstawowy** wartość. Ta wartość to `Account Key` i zostanie ona użyta później.

   ![Wyświetlanie klucza konta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
