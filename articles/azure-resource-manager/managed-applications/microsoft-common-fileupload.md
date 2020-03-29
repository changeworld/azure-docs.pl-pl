---
title: Element interfejsu użytkownika fileupload
description: Zawiera opis elementu interfejsu użytkownika microsoft.common.fileupload dla witryny Azure portal. Umożliwia użytkownikom przekazywanie plików podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 61e1c9fe07fdd29ebc00e7e3491472d073bc4e5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652491"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Element interfejsu użytkownika microsoft.common.fileupload

Formant, który umożliwia użytkownikowi określenie jednego lub więcej plików do przekazania.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Jeśli plik options.multiple jest fałszywy, a options.uploadMode jest plikiem, dane wyjściowe mają zawartość pliku jako ciąg JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Jeśli options.multiple jest true and'options.uploadMode jest plik, a następnie dane wyjściowe ma zawartość plików jako tablicy JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Jeśli options.multiple jest false i options.uploadMode jest url, a następnie dane wyjściowe ma adres URL jako ciąg JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Jeśli options.multiple jest true i options.uploadMode jest url, a następnie dane wyjściowe ma listę adresów URL jako tablicy JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Podczas testowania createuidedefition niektóre przeglądarki (takie jak Google Chrome) obcinają adresy URL generowane przez element Microsoft.Common.FileUpload w konsoli przeglądarki. Aby skopiować pełne adresy URL, może być konieczne kliknięcie prawym przyciskiem myszy poszczególnych łączy.

## <a name="remarks"></a>Uwagi

- `constraints.accept`określa typy plików, które są wyświetlane w oknie dialogowym pliku przeglądarki. Zobacz [specyfikację HTML5](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) dla dozwolonych wartości. Wartość domyślna to **null**.
- Jeśli `options.multiple` jest **ustawiona**na true , użytkownik może wybrać więcej niż jeden plik w oknie dialogowym pliku przeglądarki. Wartość domyślna to **fałsz**.
- Ten element obsługuje przesyłanie plików w dwóch `options.uploadMode`trybach na podstawie wartości programu . Jeśli **plik** jest określony, dane wyjściowe ma zawartość pliku jako obiekt blob. Jeśli **adres URL** jest określony, plik jest przekazyany do lokalizacji tymczasowej, a dane wyjściowe ma adres URL obiektu blob. Tymczasowe obiekty blob zostaną usunięte po 24 godzinach. Wartością domyślną jest **plik**.
- Przekazany plik jest chroniony. Wyjściowy adres URL zawiera [token sygnatury dostępu](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Współdzielonego do uzyskiwania dostępu do pliku podczas wdrażania.
- Wartość określa `options.openMode` sposób odczytywania pliku. Jeśli oczekuje się, że plik będzie tekstem zwykłym, należy określić **tekst**; w przeciwnym razie określ **plik binarny**. Wartością domyślną jest **tekst**.
- Jeśli `options.uploadMode` jest ustawiona `options.openMode` na **plik** i jest ustawiona na **binarną,** dane wyjściowe są zakodowane w formacie base64.
- `options.encoding`określa kodowanie, które ma być używane podczas odczytywania pliku. Wartością domyślną jest **UTF-8**i `options.openMode` jest używana tylko wtedy, gdy jest **ustawiona**na tekst .

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
