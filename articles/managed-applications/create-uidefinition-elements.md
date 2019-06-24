---
title: Platforma Azure utworzyć elementu definicji interfejsu użytkownika | Dokumentacja firmy Microsoft
description: W tym artykule opisano elementy do użycia podczas konstruowania definicji interfejsu użytkownika dla witryny Azure portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 41a583a77f85bb1524112fa20d9098e18bc4f431
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587942"
---
# <a name="createuidefinition-elements"></a>Elementy CreateUiDefinition
W tym artykule opisano schemat i właściwości dla wszystkich obsługiwanych elementów CreateUiDefinition. 

## <a name="schema"></a>Schemat

Schemat dla większości elementów jest w następujący sposób:

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

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| name | Yes | Identyfikator wewnętrzny, aby odwoływać się do określonego wystąpienia elementu. Najbardziej typowe użycia nazwy elementu `outputs`, których wartości określonych elementów danych wyjściowych są mapowane na parametrów szablonu. Umożliwia także go powiązać wartości danych wyjściowych elementu do `defaultValue` innego elementu. |
| type | Yes | Kontrolka interfejsu użytkownika do renderowania elementu. Aby uzyskać listę obsługiwanych typów, zobacz [elementy](#elements). |
| label | Yes | Tekst wyświetlany elementu. Niektóre typy elementów zawiera wiele etykiet, może to być obiekt zawierający wiele ciągów. |
| defaultValue | Nie | Wartość domyślna elementu. Niektóre typy elementów obsługują wartości domyślne złożone, może to być obiekt. |
| toolTip | Nie | Tekst wyświetlany w etykietce narzędzia elementu. Podobnie jak `label`, niektóre elementy obsługi wielu ciągów Porada narzędzia. Linków w tekście mogą być osadzone przy użyciu składni języka Markdown.
| constraints | Nie | Jedną lub więcej właściwości, które są używane do dostosowywania zachowania poprawności elementu. Obsługiwane właściwości ograniczenia zależą od typu elementu. Niektóre typy elementu nie obsługuje dostosowywania zachowania poprawności, a zatem mieć żadnej właściwości ograniczenia. |
| options | Nie | Dodatkowe właściwości, które dostosować zachowanie elementu. Podobnie jak `constraints`, obsługiwanych właściwości zależą od typu elementu. |
| visible | Nie | Wskazuje, czy element jest wyświetlany. Jeśli `true`, element i elementy podrzędne stosowane są wyświetlane. Wartość domyślna to `true`. Użyj [funkcje logiczne](create-uidefinition-functions.md#logical-functions) dynamicznie kontrolować wartość tej właściwości.

## <a name="elements"></a>Elementy

Dokumentacja dla każdego elementu zawiera przykładowy interfejs użytkownika, schematem, uwagi na zachowanie elementu (zwykle w odniesieniu do weryfikacji i dostosowywania obsługiwanych) i przykładowe dane wyjściowe.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
