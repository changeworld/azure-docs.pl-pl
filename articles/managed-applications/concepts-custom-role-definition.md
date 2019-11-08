---
title: Przegląd niestandardowych definicji ról w Azure Managed Applications | Microsoft Docs
description: Opisuje koncepcję tworzenia niestandardowych definicji ról dla zarządzanych aplikacji.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7f4371bea467d6d4c99a776e03cdf13070d77ac6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818392"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt definicji roli niestandardowej w Azure Managed Applications

Definicja roli niestandardowej to opcjonalny artefakt w aplikacjach zarządzanych. Służy do określania, jakie uprawnienia musi wykonać aplikacja zarządzana.

Ten artykuł zawiera omówienie artefaktu definicji roli niestandardowej i jego możliwości.

## <a name="custom-role-definition-artifact"></a>Artefakt definicji roli niestandardowej

Należy nazwać artefaktu definicji roli niestandardowej customRoleDefinition. JSON. Umieść go na tym samym poziomie, co createUiDefinition. JSON i mainTemplate. JSON w pakiecie. zip, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet ZIP i opublikować definicję aplikacji zarządzanej, zobacz [Publikowanie definicji aplikacji zarządzanej.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Schemat definicji roli niestandardowej

Plik customRoleDefinition. JSON ma właściwość `roles` najwyższego poziomu, która jest tablicą ról. Role te są uprawnieniami, które muszą działać aplikacja zarządzana. Obecnie dozwolone są tylko wbudowane role, ale można określić wiele ról. Do roli może odwoływać się identyfikator definicji roli lub nazwa roli.

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

## <a name="roles"></a>Role

Rola składa się z `$.properties.roleName` lub `id`:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Możesz użyć pola `id` lub `roleName`. Wymagany jest tylko jeden. Te pola są używane do wyszukiwania definicji roli, która powinna zostać zastosowana. Jeśli są podane oba te elementy, zostanie użyte pole `id`.

|Właściwość|Wymagana?|Opis|
|---------|---------|---------|
|id|Tak|Identyfikator wbudowanej roli. Możesz użyć pełnego identyfikatora lub tylko identyfikatora GUID.|
|Role|Tak|Nazwa wbudowanej roli.|