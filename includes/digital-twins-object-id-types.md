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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534130"
---
`objectIdType` (Lub **typ identyfikatora obiektu**) odwołuje się do typu tożsamości, który znajduje się do roli. Z wyjątkiem `DeviceId` i `UserDefinedFunctionId` typy, typy identyfikatora obiektu odpowiadają właściwości obiektów w usłudze Azure Active Directory.

Poniższa tabela zawiera typy identyfikatorów obsługiwany obiekt w reprezentacji urządzeń cyfrowych platformy Azure:

| Type | Opis |
| --- | --- |
| UserId | Przypisanie roli do użytkownika. |
| DeviceId | Przypisanie roli do urządzenia. |
| DomainName | Przypisuje rolę, aby wskazywała nazwę domeny. Każdego użytkownika mającego określoną nazwę domeny ma prawa dostępu do odpowiedniej roli. |
| TenantId | Przypisuje rolę do dzierżawy. Każdy użytkownik, który należy do określonego Identyfikatora dzierżawy usługi Azure AD ma prawa dostępu do odpowiedniej roli. |
| ServicePrincipalId | Przypisanie roli do identyfikatora obiektu jednostki usługi |
| UserDefinedFunctionId | Przypisuje rolę funkcji zdefiniowanej przez użytkownika (UDF). |