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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76268333"
---
(lub `objectIdType` **typ identyfikatora obiektu)** odnosi się do typu tożsamości, który jest nadany roli. Oprócz `DeviceId` typów `UserDefinedFunctionId` i typy, typy identyfikatorów obiektów odpowiadają właściwościom obiektów usługi Azure Active Directory.

Poniższa tabela zawiera obsługiwane typy identyfikatorów obiektów w usłudze Azure Digital Twins:

| Typ | Opis |
| --- | --- |
| UserId | Przypisuje rolę do użytkownika. |
| DeviceId | Przypisuje rolę do urządzenia. |
| DomainName | Przypisuje rolę do nazwy domeny. Każdy użytkownik o określonej nazwie domeny ma prawa dostępu odpowiedniej roli. |
| TenantId | Przypisuje rolę do dzierżawy. Każdy użytkownik, który należy do określonego identyfikatora dzierżawy usługi Azure AD ma prawa dostępu odpowiedniej roli. |
| ServicePrincipalId | Przypisuje rolę do identyfikatora obiektu jednostki usługi. |
| Identyfikator funkcji zdefiniowanych przez użytkownika | Przypisuje rolę do funkcji zdefiniowanej przez użytkownika (UDF). |