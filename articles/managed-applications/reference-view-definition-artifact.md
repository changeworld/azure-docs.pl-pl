---
title: Odwołanie artefaktu definicji widoku aplikacji zarządzanej przez platformę Azure
description: Ten artykuł jest odwołaniem do wyświetlania artefaktu definicji.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: e60f26fe0a7144d768bac020d62c61cb92594914
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336545"
---
# <a name="reference-view-definition-artifact"></a>Informacje ogólne: Wyświetlanie artefaktu definicji

Ten artykuł jest odwołaniem do artefaktu *galerii. JSON* w Azure Managed Applications. Aby uzyskać więcej informacji na temat konfiguracji widoków tworzenia, zobacz [artefakt definicji widoku](concepts-view-definition.md).

## <a name="view-definition"></a>Wyświetl definicję

Poniższy kod JSON przedstawia przykład pliku *galerii. JSON* dla Azure Managed Applications:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie aplikacji zarządzanej z niestandardowymi akcjami i zasobami](tutorial-create-managed-app-with-custom-provider.md)
- [Odwoła Artefakt elementów interfejsu użytkownika](reference-createuidefinition-artifact.md)
- [Odwoła Artefakt szablonu wdrożenia](reference-main-template-artifact.md)