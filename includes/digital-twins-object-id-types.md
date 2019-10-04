---
title: plik dołączany
description: plik dołączany
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827685"
---
@No__t-0 (lub **Typ identyfikatora obiektu**) odnosi się do typu tożsamości nadawanej roli. Oprócz typów `DeviceId` i `UserDefinedFunctionId` typy identyfikatorów obiektów odpowiadają właściwościom obiektów Azure Active Directory.

Poniższa tabela zawiera obsługiwane typy identyfikatorów obiektów w usłudze Azure Digital bliźniaczych reprezentacji:

| Typ | Opis |
| --- | --- |
| Nazwa | Przypisuje rolę użytkownikowi. |
| Identyfikator | Przypisuje rolę do urządzenia. |
| DomainName | Przypisuje rolę do nazwy domeny. Każdy użytkownik z określoną nazwą domeny ma prawa dostępu do odpowiedniej roli. |
| TenantId | Przypisuje rolę do dzierżawcy. Każdy użytkownik, który należy do określonego identyfikatora dzierżawy usługi Azure AD, ma prawa dostępu do odpowiedniej roli. |
| servicePrincipalId | Przypisuje rolę do identyfikatora obiektu jednostki usługi. |
| UserDefinedFunctionId | Przypisuje rolę do funkcji zdefiniowanej przez użytkownika (UDF). |