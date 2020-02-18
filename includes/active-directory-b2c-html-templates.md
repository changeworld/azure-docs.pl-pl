---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: 264604bfdf0c514e6464854f431addbc9d2dcdef
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373121"
---
## <a name="sample-templates"></a>Przykładowe szablony
Przykładowe szablony do dostosowywania interfejsu użytkownika można znaleźć tutaj:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Ten projekt zawiera następujące szablony:
- [Ocean niebieski](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Szare](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Aby użyć przykładu:

1. Sklonuj repozytorium na komputerze lokalnym. Wybierz folder szablonu `/ocean_blue` lub `/slate_gray`.
1. Przekaż wszystkie pliki znajdujące się w folderze templates i folderze `/assets` do magazynu obiektów blob, zgodnie z opisem w poprzednich sekcjach.
1. Następnie otwórz każdy plik `\*.html` w folderze głównym `/ocean_blue` lub `/slate_gray`Zastąp wszystkie wystąpienia względnych adresów URL adresami URL plików CSS, obrazów i czcionek przekazanych w kroku 2. Na przykład:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Do 
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Zapisz pliki `\*.html` i przekaż je do magazynu obiektów BLOB.
1. Teraz zmodyfikuj zasady, wskazując na plik HTML, jak wspomniano wcześniej.
1. Jeśli widzisz brakujące czcionki, obrazy lub CSS, Sprawdź odwołania w zasadach rozszerzeń i plikach \*. html.
