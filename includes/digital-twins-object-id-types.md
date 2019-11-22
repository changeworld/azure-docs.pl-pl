---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307264"
---
`objectIdType` (lub **Typ identyfikatora obiektu**) odnosi się do typu tożsamości nadawanej roli. Oprócz typów `DeviceId` i `UserDefinedFunctionId`, typy identyfikatorów obiektów odpowiadają właściwościom obiektów Azure Active Directory.

Poniższa tabela zawiera obsługiwane typy identyfikatorów obiektów w usłudze Azure Digital bliźniaczych reprezentacji:

| Typ | Opis |
| --- | --- |
| UserId | Przypisuje rolę użytkownikowi. |
| DeviceId | Przypisuje rolę do urządzenia. |
| DomainName | Przypisuje rolę do nazwy domeny. Każdy użytkownik z określoną nazwą domeny ma prawa dostępu do odpowiedniej roli. |
| tenantId | Przypisuje rolę do dzierżawcy. Każdy użytkownik, który należy do określonego identyfikatora dzierżawy usługi Azure AD, ma prawa dostępu do odpowiedniej roli. |
| servicePrincipalId | Przypisuje rolę do identyfikatora obiektu jednostki usługi. |
| UserDefinedFunctionId | Przypisuje rolę do funkcji zdefiniowanej przez użytkownika (UDF). |