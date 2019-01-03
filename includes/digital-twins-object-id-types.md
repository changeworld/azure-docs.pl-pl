---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795901"
---
`objectIdType` (Lub **typ identyfikatora obiektu**) odwołuje się do typu tożsamości, który znajduje się do roli. Z wyjątkiem `DeviceId` i `UserDefinedFunctionId` typy, typy identyfikatora obiektu odpowiadają właściwości obiektów w usłudze Azure Active Directory.

Poniższa tabela zawiera typy identyfikatorów obsługiwany obiekt w reprezentacji urządzeń cyfrowych platformy Azure:

| Typ | Opis |
| --- | --- |
| UserId | Przypisanie roli do użytkownika. |
| DeviceId | Przypisanie roli do urządzenia. |
| DomainName | Przypisuje rolę, aby wskazywała nazwę domeny. Każdego użytkownika mającego określoną nazwę domeny ma prawa dostępu do odpowiedniej roli. |
| TenantId | Przypisuje rolę do dzierżawy. Każdy użytkownik, który należy do określonego Identyfikatora dzierżawy usługi Azure AD ma prawa dostępu do odpowiedniej roli. |
| servicePrincipalId | Przypisanie roli do identyfikatora obiektu jednostki usługi |
| UserDefinedFunctionId | Przypisuje rolę funkcji zdefiniowanej przez użytkownika (UDF). |