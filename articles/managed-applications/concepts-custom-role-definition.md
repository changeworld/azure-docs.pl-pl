---
title: Przegląd niestandardowych definicji ról w Azure Managed Applications | Microsoft Docs
description: Opisuje koncepcję tworzenia niestandardowych definicji ról dla zarządzanych aplikacji.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: d645eebefde473e404f7760d2bc8a67c7e3e9087
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609036"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt definicji roli niestandardowej w Azure Managed Applications

Definicja roli niestandardowej to opcjonalny artefakt w aplikacjach zarządzanych. Służy do określania, jakie uprawnienia musi wykonać aplikacja zarządzana.

Ten artykuł zawiera omówienie artefaktu definicji roli niestandardowej i jego możliwości.

## <a name="custom-role-definition-artifact"></a>Artefakt definicji roli niestandardowej

Artefakt definicji roli niestandardowej musi mieć nazwę **customRoleDefinition. JSON** i znajdować się na tym samym poziomie, co **createUiDefinition. JSON** i **mainTemplate. JSON** w pakiecie. zip, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet ZIP i opublikować definicję aplikacji zarządzanej, zobacz [Publikowanie definicji aplikacji zarządzanej.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Schemat definicji roli niestandardowej

Plik **customRoleDefinition. JSON** ma właściwość najwyższego poziomu `roles`, która jest tablicą ról. Każda z tych ról jest uprawnieniami, które aplikacja zarządzana musi działać. Obecnie dozwolone są tylko wbudowane role, ale można określić wiele ról. Do roli może odwoływać się identyfikator definicji roli lub nazwa roli.

Przykładowy kod JSON dla definicji roli niestandardowej:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="role"></a>Rola

Rola składa się z `$.properties.roleName` lub `id`.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!Note]
> Tylko jedno z pól `id` lub `roleName` jest wymagane. Te pola są używane do wyszukiwania definicji roli do zastosowania. Jeśli są podane oba te elementy, zostanie użyte pole `id`.

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|ID|*opcję*|Identyfikator wbudowanej roli. Ta właściwość może być pełnym IDENTYFIKATORem lub tylko identyfikatorem GUID.|
|Role|*opcję*|Nazwa wbudowanej roli.|