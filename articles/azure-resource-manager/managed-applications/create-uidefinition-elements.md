---
title: Tworzenie elementów definicji interfejsu użytkownika
description: W tym artykule opisano elementy do użycia podczas konstruowania definicji interfejsu użytkownika dla witryny Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086703"
---
# <a name="createuidefinition-elements"></a>Elementy CreateUiDefinition

W tym artykule opisano schemat i właściwości dla wszystkich obsługiwanych elementów CreateUiDefinition. 

## <a name="schema"></a>Schemat

Schemat dla większości elementów jest następujący:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Właściwość | Wymagany | Opis |
| -------- | -------- | ----------- |
| name | Tak | Wewnętrzny identyfikator do odwoływania się do określonego wystąpienia elementu. Najczęstszym zastosowaniem nazwy elementu `outputs`jest w programie , gdzie wartości wyjściowe określonych elementów są mapowane na parametry szablonu. Można również użyć go do powiązania wartości wyjściowej elementu `defaultValue` z innym elementem. |
| type | Tak | Formant interfejsu użytkownika do renderowania dla elementu. Aby uzyskać listę obsługiwanych typów, zobacz [Elementy](#elements). |
| label | Tak | Wyświetlany tekst elementu. Niektóre typy elementów zawierają wiele etykiet, więc wartość może być obiektem zawierającym wiele ciągów. |
| Defaultvalue | Nie | Wartość domyślna elementu. Niektóre typy elementów obsługują złożone wartości domyślne, więc wartość może być obiektem. |
| Etykietka narzędzia | Nie | Tekst wyświetlany w końcówce narzędzia elementu. Podobnie `label`jak w przypadku niektórych elementów, niektóre elementy obsługują wiele ciągów końcówki narzędzia. Łącza wbudowane mogą być osadzone przy użyciu składni Markdown.
| Ograniczenia | Nie | Co najmniej jedna właściwa, które są używane do dostosowywania zachowania sprawdzania poprawności elementu. Obsługiwane właściwości dla ograniczeń różnią się w zależności od typu elementu. Niektóre typy elementów nie obsługują dostosowywania zachowania sprawdzania poprawności, a zatem nie mają właściwości ograniczeń. |
| opcje | Nie | Dodatkowe właściwości, które dostosowują zachowanie elementu. Podobnie `constraints`jak , obsługiwane właściwości różnią się w zależności od typu elementu. |
| Widoczne | Nie | Wskazuje, czy element jest wyświetlany. Jeśli `true`zostanie wyświetlony element i odpowiednie elementy podrzędne. Wartością domyślną jest `true`. Użyj [funkcji logicznych,](create-uidefinition-functions.md#logical-functions) aby dynamicznie kontrolować wartość tej właściwości.

## <a name="elements"></a>Elementy

Dokumentacja dla każdego elementu zawiera przykład interfejsu użytkownika, schemat, uwagi na temat zachowania elementu (zwykle dotyczące sprawdzania poprawności i obsługiwane dostosowywania) i przykładowe dane wyjściowe.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Skrzynka microsoft.common.infobox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Blokowanie podyszewaniem do tekstu w witrynie Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
