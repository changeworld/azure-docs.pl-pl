---
title: Azure utworzyć elementu interfejsu użytkownika definicji | Dokumentacja firmy Microsoft
description: W tym artykule opisano elementy do użycia podczas konstruowania definicji interfejsu użytkownika portalu Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: cbfc9c02cbde8bcf9a253144ff41497676c98f13
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="createuidefinition-elements"></a>Elementy CreateUiDefinition
W tym artykule opisano schematu i właściwości dla wszystkich elementów obsługiwanych CreateUiDefinition. 

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

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| name | Yes | Wewnętrzny identyfikator, aby odwołać konkretne wystąpienie elementu. Najbardziej typowe użycie w nazwie elementu znajduje się w `outputs`, gdzie wartości określonych elementów danych wyjściowych są mapowane do parametrów szablonu. Można go powiązać wartość wyjściowa elementu `defaultValue` innego elementu. |
| type | Yes | Formant interfejsu użytkownika do renderowania elementu. Aby uzyskać listę obsługiwanych typów, zobacz [elementy](#elements). |
| Etykiety | Yes | Wyświetlany tekst elementu. Niektóre typy elementu zawiera wiele etykiet, może to być obiekt zawierający wiele ciągów. |
| Wartość domyślna | Nie | Wartość domyślna elementu. Niektóre typy elementu obsługuje złożone domyślne wartości, wartość może być obiektem. |
| toolTip | Nie | Tekst wyświetlany w etykietce narzędzia elementu. Podobnie jak `label`, niektóre elementy obsługi wielu ciągów Porada narzędzia. Można ją osadzić łącza wbudowanego przy użyciu składni języka Markdown.
| Ograniczenia | Nie | Co najmniej jednej właściwości, które są używane, aby dostosować zachowanie sprawdzania poprawności elementu. Obsługiwane właściwości ograniczenia zależy od typu elementu. Niektóre typy elementu nie obsługuje dostosowywania sprawdzania poprawności, a w związku z tym mają właściwość nie ograniczeń. |
| opcje | Nie | Dodatkowe właściwości, które dostosowują zachowanie elementu. Podobnie jak `constraints`, obsługiwanych właściwości zależy od typu elementu. |
| Widoczne | Nie | Wskazuje, czy element jest wyświetlany. Jeśli `true`, element i elementy podrzędne stosowane są wyświetlane. Wartość domyślna to `true`. Użyj [funkcje logiczne](create-uidefinition-functions.md#logical-functions) dynamicznie kontrolować wartość tej właściwości.

## <a name="elements"></a>Elementy

Dokumentację dla każdego elementu zawiera przykładowe interfejsu użytkownika, schematem, uwagi na zachowanie elementu (zazwyczaj dotyczących sprawdzania poprawności i dostosowywania obsługiwanych) i przykładowe dane wyjściowe.

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
- [Microsoft.Network.AvailabilityZoneDropDown](microsoft-network-availabilityzonedropdown.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Kolejne kroki
Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
