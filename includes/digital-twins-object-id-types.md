---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268333"
---
`objectIdType` (lub **Typ identyfikatora obiektu**) odnosi się do typu tożsamości nadawanej roli. Oprócz typów `DeviceId` i `UserDefinedFunctionId`, typy identyfikatorów obiektów odpowiadają właściwościom obiektów Azure Active Directory.

Poniższa tabela zawiera obsługiwane typy identyfikatorów obiektów w usłudze Azure Digital bliźniaczych reprezentacji:

| Typ | Opis |
| --- | --- |
| UserId | Przypisuje rolę użytkownikowi. |
| DeviceId | Przypisuje rolę do urządzenia. |
| DomainName | Przypisuje rolę do nazwy domeny. Każdy użytkownik z określoną nazwą domeny ma prawa dostępu do odpowiedniej roli. |
| TenantId | Przypisuje rolę do dzierżawcy. Każdy użytkownik, który należy do określonego identyfikatora dzierżawy usługi Azure AD, ma prawa dostępu do odpowiedniej roli. |
| ServicePrincipalId | Przypisuje rolę do identyfikatora obiektu jednostki usługi. |
| UserDefinedFunctionId | Przypisuje rolę do funkcji zdefiniowanej przez użytkownika (UDF). |