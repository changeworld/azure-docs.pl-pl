---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: a2ee8705be3f34b6df113c68d88e375411f84bf2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440336"
---
## <a name="deploy-template-from-cloud-shell"></a>Wdrażanie szablonu za pomocą usługi Cloud Shell

Do wdrożenia swojego szablonu możesz użyć usługi [Cloud Shell](../articles/cloud-shell/overview.md). Jednak musisz najpierw załadować szablon do konta magazynu dla usługi Cloud Shell. Jeśli nie używasz usługi Cloud Shell, zobacz [Overview of Azure Cloud Shell (Omówienie usługi Azure Cloud Shell)](../articles/cloud-shell/overview.md), aby uzyskać informacje o jej konfigurowaniu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz swoją grupę zasobów usługi Cloud Shell. Wzorzec nazwy to `cloud-shell-storage-<region>`.

   ![Wybieranie grupy zasobów](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Wybierz konto magazynu dla usługi Cloud Shell.

   ![Wybieranie konta magazynu](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Wybierz pozycję **Obiekty blob**.

   ![Wybieranie pozycji Obiekty blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Wybierz pozycję **+ Kontener**.

   ![Dodawanie kontenera](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Kontener należy podać nazwę i poziom dostępu. Przykładowy szablon w tym artykule nie zawiera żadnych poufnych informacji, co pozwala anonimowy dostęp do odczytu. Kliknij przycisk **OK**.

   ![Podaj wartości kontenera](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Wybierz kontener, który został utworzony.

   ![Wybierz nowy kontener](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Wybierz pozycję **Przekaż**.

   ![Przekazywanie obiektu blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Znajdź i przekaż swój szablon.

   ![Przekazywanie pliku](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po przekazał, wybierz szablon.

   ![Wybierz nowy szablon](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Skopiuj adres URL.

   ![Skopiuj adres URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otwórz wiersz polecenia.

   ![Otwieranie usługi Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
