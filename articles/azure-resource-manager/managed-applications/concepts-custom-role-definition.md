---
title: Omówienie niestandardowych definicji ról
description: W tym artykule opisano koncepcję tworzenia niestandardowych definicji ról dla aplikacji zarządzanych.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650762"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt definicji roli niestandardowej w aplikacjach zarządzanych platformy Azure

Definicja roli niestandardowej jest opcjonalnym artefaktem w aplikacjach zarządzanych. Służy do określenia, jakie uprawnienia aplikacja zarządzana musi wykonywać swoje funkcje.

Ten artykuł zawiera omówienie artefaktu definicji roli niestandardowej i jego możliwości.

## <a name="custom-role-definition-artifact"></a>Artefakt definicji roli niestandardowej

Należy nadać nazwę niestandardowej definicji roli artefakturoledefinition.json. Umieść go na tym samym poziomie co createUiDefinition.json i mainTemplate.json w pakiecie .zip, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet zip i opublikować definicję aplikacji zarządzanej, zobacz [Publikowanie definicji aplikacji zarządzanej.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Schemat niestandardowej definicji roli

Plik customRoleDefinition.json ma właściwość `roles` najwyższego poziomu, która jest tablicą ról. Te role są uprawnienia, które aplikacja zarządzana musi działać. Obecnie dozwolone są tylko wbudowane role, ale można określić wiele ról. Do roli można odwoływać się identyfikator definicji roli lub nazwa roli.

Przykładowy JSON dla niestandardowej definicji roli:

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

## <a name="roles"></a>Role

Rola składa się z `$.properties.roleName` : `id`

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Można użyć `id` pola lub `roleName` pola. Wymagany jest tylko jeden. Pola te są używane do wyszukiwania definicji roli, które powinny być stosowane. Jeśli oba są `id` dostarczane, pole zostanie użyte.

|Właściwość|Wymagana?|Opis|
|---------|---------|---------|
|id|Tak|Identyfikator wbudowanej roli. Możesz użyć pełnego identyfikatora lub tylko identyfikatora GUID.|
|Rolename|Tak|Nazwa wbudowanej roli.|