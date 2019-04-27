---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710019"
---
W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **grup zasobów** > **chmury-shell-storage -\<your_region >**  >   **\<nazwa_konta_magazynu >**.

![Znajdź konto magazynu w usłudze Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

W **Przegląd** stronie konta magazynu, wybierz **pliki**.

Wybierz udział automatycznie generowanych plików, a następnie wybierz **przekazywanie**. Ten udział plików jest zainstalowany w usłudze Cloud Shell jako `clouddrive`.

![Znajdź przycisku przekazywania](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Kliknij selektor pliku i wybierz swój plik ZIP, a następnie kliknij przycisk **przekazywanie**. 

W usłudze Cloud Shell za pomocą `ls` Aby sprawdzić, widoczny przekazany plik ZIP w domyślnym `clouddrive` udostępniania.

```azurecli-interactive
ls clouddrive
```
