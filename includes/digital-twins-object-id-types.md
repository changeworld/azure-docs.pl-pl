---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 94baeb3d459b700cc95d88fb82f995957640aab6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012229"
---
(Lub **Typ identyfikatora obiektu**) odnosi się do typu tożsamości nadawanej roli. `objectIdType` Oprócz typów `UserDefinedFunctionId` i typy identyfikatorów obiektów odpowiadają właściwościom obiektów Azure Active Directory. `DeviceId`

Poniższa tabela zawiera obsługiwane typy identyfikatorów obiektów w usłudze Azure Digital bliźniaczych reprezentacji:

| Type | Opis |
| --- | --- |
| UserId | Przypisuje rolę użytkownikowi. |
| DeviceId | Przypisuje rolę do urządzenia. |
| DomainName | Przypisuje rolę do nazwy domeny. Każdy użytkownik z określoną nazwą domeny ma prawa dostępu do odpowiedniej roli. |
| TenantId | Przypisuje rolę do dzierżawcy. Każdy użytkownik, który należy do określonego identyfikatora dzierżawy usługi Azure AD, ma prawa dostępu do odpowiedniej roli. |
| ServicePrincipalId | Przypisuje rolę do identyfikatora obiektu jednostki usługi. |
| UserDefinedFunctionId | Przypisuje rolę do funkcji zdefiniowanej przez użytkownika (UDF). |