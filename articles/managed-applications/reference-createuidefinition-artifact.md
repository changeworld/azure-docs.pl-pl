---
title: Dokumentacja artefaktu createUiDefinition aplikacji zarządzanej platformy Azure
description: Ten artykuł jest odwołaniem do artefaktu createUiDefinition.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5168e977a7bc289ec8981ef1df8cddd5cc6df678
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336480"
---
# <a name="reference-user-interface-elements-artifact"></a>Informacje ogólne: Artefakt elementów interfejsu użytkownika

Ten artykuł jest odwołaniem do artefaktu *createUiDefinition. JSON* w Azure Managed Applications. Aby uzyskać więcej informacji na temat tworzenia elementów interfejsu użytkownika, zobacz [Tworzenie elementów interfejsu użytkownika](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Elementy interfejsu użytkownika

Poniższy kod JSON przedstawia przykład pliku *createUiDefinition. JSON* dla Azure Managed Applications:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie aplikacji zarządzanej z niestandardowymi akcjami i zasobami](tutorial-create-managed-app-with-custom-provider.md)
- [Odwoła Artefakt szablonu wdrożenia](reference-main-template-artifact.md)
- [Odwoła Wyświetl artefakt definicji](reference-view-definition-artifact.md)