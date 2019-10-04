---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949044"
---
@No__t-0 (lub **Typ identyfikatora obiektu**) odnosi się do typu tożsamości nadawanej roli. Oprócz typów `DeviceId` i `UserDefinedFunctionId` typy identyfikatorów obiektów odpowiadają właściwościom obiektów Azure Active Directory.

Poniższa tabela zawiera obsługiwane typy identyfikatorów obiektów w usłudze Azure Digital bliźniaczych reprezentacji:

| Typ | Opis |
| --- | --- |
| Nazwa | Przypisuje rolę użytkownikowi. |
| Identyfikator | Przypisuje rolę do urządzenia. |
| DomainName | Przypisuje rolę do nazwy domeny. Każdy użytkownik z określoną nazwą domeny ma prawa dostępu do odpowiedniej roli. |
| TenantId | Przypisuje rolę do dzierżawcy. Każdy użytkownik, który należy do określonego identyfikatora dzierżawy usługi Azure AD, ma prawa dostępu do odpowiedniej roli. |
| ServicePrincipalId | Przypisuje rolę do identyfikatora obiektu jednostki usługi. |
| UserDefinedFunctionId | Przypisuje rolę do funkcji zdefiniowanej przez użytkownika (UDF). |