---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189031"
---
## <a name="sample-templates"></a>Przykładowe szablony
Przykładowe szablony do dostosowywania interfejsu użytkownika można znaleźć tutaj:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Ten projekt zawiera następujące szablony:
- [Błękitny Ocean](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Szary łupek](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Aby użyć próbki:

1. Sklonuj repozytorium na komputerze lokalnym. Wybierz folder `/ocean_blue` szablonu lub `/slate_gray`.
1. Przekaż wszystkie pliki w folderze `/assets` szablonu i folderze do magazynu obiektów Blob zgodnie z opisem w poprzednich sekcjach.
1. Następnie otwórz `\*.html` każdy plik w katalogu `/ocean_blue` `/slate_gray`głównym jednego lub , zastąp wszystkie wystąpienia względnych adresów URL adresami URL plikami css, obrazów i czcionek przesłanych w kroku 2. Przykład:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Do
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Zapisz `\*.html` pliki i przekaż je do magazynu obiektów Blob.
1. Teraz zmodyfikuj zasady, wskazując plik HTML, jak wspomniano wcześniej.
1. Jeśli widzisz brakujące czcionki, obrazy lub CSS, sprawdź odwołania w \*zasadach rozszerzeń i plikach .html.
