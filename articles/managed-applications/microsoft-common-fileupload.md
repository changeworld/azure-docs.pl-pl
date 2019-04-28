---
title: Azure elementu interfejsu użytkownika FileUpload | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.FileUpload interfejsu użytkownika dla witryny Azure portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 92a5f7c058904015cb22a239b7e7c4938ae1fdae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61044654"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI element
Formant, który umożliwia użytkownikowi określenie co najmniej jeden plik do przekazania.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- `constraints.accept` Określa typy plików, które są wyświetlane w oknie dialogowym pliku w przeglądarce. Zobacz [specyfikacji HTML5](https://www.w3.org/TR/html5/forms.html#attr-input-accept) dla dozwolone wartości. Wartość domyślna to **null**.
- Jeśli `options.multiple` ustawiono **true**, użytkownik może wybrać więcej niż jednego pliku w oknie dialogowym pliku w przeglądarce. Wartość domyślna to **false**.
- Ten element obsługuje przekazywania plików w dwóch trybach, w oparciu o wartość `options.uploadMode`. Jeśli **pliku** jest określony, dane wyjściowe zawierają zawartość pliku jako obiekt blob. Jeśli **adresu url** zostanie określony, plik jest przekazywany do tymczasowej lokalizacji, a dane wyjściowe zawierają adres URL obiektu blob. Tymczasowe obiekty BLOB zostaną wyczyszczone po 24 godzinach. Wartość domyślna to **pliku**.
- Przekazany plik jest chroniony. Adres URL danych wyjściowych zawiera [tokenu sygnatury dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) do uzyskiwania dostępu do pliku podczas wdrażania.
- Wartość `options.openMode` Określa, jak odczytać pliku. Jeśli plik powinien być zwykłego tekstu, należy określić **tekstu**; w przeciwnym razie, określ **binarne**. Wartość domyślna to **tekstu**.
- Jeśli `options.uploadMode` jest ustawiona na **pliku** i `options.openMode` ustawiono **binarne**, dane wyjściowe są zakodowane w formacie base64.
- `options.encoding` Określa kodowanie do użycia podczas odczytu pliku. Wartość domyślna to **UTF-8**i jest używana tylko wtedy, gdy `options.openMode` ustawiono **tekstu**.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Jeśli options.uploadMode jest plikiem options.multiple ma wartość false, następnie dane wyjściowe zawierają zawartość pliku jako ciąg JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Jeśli obowiązuje options.multiple and'options.uploadMode jest plik, a następnie dane wyjściowe zawierają zawartość plików w postaci tablicy JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Jeśli adres url jest options.uploadMode options.multiple ma wartość false, następnie dane wyjściowe zawierają adresu URL jako ciąg JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Jeśli adres url jest options.uploadMode options.multiple ma wartość true, następnie dane wyjściowe zawierają listę adresów URL jako tablicę JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Podczas testowania CreateUiDefinition, niektóre przeglądarki (np. Google Chrome) obciąć adresy URL wygenerowanym przez dany element Microsoft.Common.FileUpload w konsoli przeglądarki. Może być konieczne kliknięcie prawym przyciskiem myszy poszczególnymi łączami do skopiowania pełne adresy URL.


## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
