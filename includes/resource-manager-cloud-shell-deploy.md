---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 7c081b3bc5f9e6273f680b24897f9aced4999afa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66129023"
---
## <a name="deploy-template-from-cloud-shell"></a>Wdrażanie szablonu za pomocą usługi Cloud Shell

Do wdrożenia swojego szablonu możesz użyć usługi [Cloud Shell](../articles/cloud-shell/overview.md). Aby wdrożyć szablon zewnętrznych, podaj identyfikator URI szablonu dokładnie tak jak w przypadku każdego wdrożenia zewnętrznych. Aby wdrożyć szablon lokalnego, musisz najpierw załadować swój szablon do konta magazynu dla usługi Cloud Shell. W tej sekcji opisano, jak załadować szablon do swojego konta powłoki w chmurze i wdrożyć go jako plik lokalny. Jeśli nie znasz usługi Cloud Shell, zobacz [Omówienie usługi Azure Cloud Shell](../articles/cloud-shell/overview.md) informacji o jej konfigurowaniu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz swoją grupę zasobów usługi Cloud Shell. Wzorzec nazwy to `cloud-shell-storage-<region>`.

   ![Wybierz grupę zasobów](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Wybierz konto magazynu dla usługi Cloud Shell.

   ![Wybierz konto magazynu](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Wybierz pozycję **Obiekty blob**.

   ![Wybieranie pozycji Obiekty blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Wybierz pozycję **+ Kontener**.

   ![Dodaj kontener](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Kontener należy podać nazwę i poziom dostępu. Przykładowy szablon w tym artykule nie zawiera żadnych poufnych informacji, co pozwala anonimowy dostęp do odczytu. Kliknij przycisk **OK**.

   ![Podaj wartości kontenera](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Wybierz kontener, który został utworzony.

   ![Wybierz nowy kontener](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Wybierz pozycję **Przekaż**.

   ![Przekazywanie obiektu blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Znajdź i przekaż swój szablon.

   ![Przekaż plik](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po przekazał, wybierz szablon.

   ![Wybierz nowy szablon](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Skopiuj adres URL.

   ![Kopiuj adres URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otwórz wiersz polecenia.

   ![Otwieranie usługi Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
