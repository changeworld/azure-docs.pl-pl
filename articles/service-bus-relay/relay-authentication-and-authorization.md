---
title: Usługa Azure Relay uwierzytelnianie i autoryzacja | Dokumentacja firmy Microsoft
description: Omówienie uwierzytelniania sygnatury dostępu współdzielonego (SAS) w usłudze Azure Relay
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 206cca95c590a01f69d3664fb87398bc2fcb4ad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595513"
---
# <a name="azure-relay-authentication-and-authorization"></a>Usługa Azure Relay uwierzytelnianie i autoryzacja

Aplikacje mogą uwierzytelniać się do usługi Azure Relay przy użyciu uwierzytelniania sygnatury dostępu współdzielonego (SAS). Uwierzytelniania sygnatury dostępu Współdzielonego umożliwia aplikacjom uwierzytelnianie w usłudze Azure Relay przy użyciu klucza dostępu skonfigurowane na przestrzeń nazw usługi Relay. Ten klucz służy następnie do wygenerowania tokenu sygnatura dostępu współdzielonego, której klienci mogą używać do uwierzytelniania w usłudze relay.

## <a name="shared-access-signature-authentication"></a>Uwierzytelniania sygnatury dostępu współużytkowanego

[Uwierzytelniania sygnatury dostępu Współdzielonego](../service-bus-messaging/service-bus-sas.md) umożliwia udzielenie użytkownikom dostępu do zasobów usługi Azure Relay przy użyciu określonych praw. Uwierzytelniania sygnatury dostępu Współdzielonego obejmuje konfigurację klucza kryptograficznego ze skojarzonymi prawami do zasobu. Klienci mogą zatem uzyskiwać dostęp do tego zasobu, prezentując tokenu sygnatury dostępu Współdzielonego, który składa się z identyfikatora URI, dostęp do zasobu i wygaśnięcia podpisany przy użyciu skonfigurowanego klucza.

Klucze sygnatur dostępu współdzielonego można skonfigurować w przestrzeni nazw usługi Relay. W przeciwieństwie do komunikatów usługi Service Bus, [połączeń hybrydowych usługi Relay](relay-hybrid-connections-protocol.md) obsługuje nadawców nieautoryzowani lub anonimowe. Możesz włączyć anonimowy dostęp do jednostki podczas tworzenia, jak pokazano na poniższym zrzucie ekranu z portalu:

![][0]

Aby korzystać z sygnatury dostępu Współdzielonego, można skonfigurować [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) obiektu w przestrzeni nazw usługi Relay, który składa się z następujących czynności:

* *KeyName* określający reguły.
* *PrimaryKey* jest klucz kryptograficzny używany do logowania i weryfikacji tokenów sygnatur dostępu Współdzielonego.
* *Klucz pomocniczy* jest klucz kryptograficzny używany do logowania i weryfikacji tokenów sygnatur dostępu Współdzielonego.
* *Prawa* reprezentujący kolekcję nasłuchiwania, wysyłania lub zarządzania prawa.

Reguły autoryzacji konfigurowane na poziomie przestrzeni nazw można przyznać dostęp do wszystkich połączeń w przestrzeni nazw usługi relay dla klientów z tokenami podpisany przy użyciu odpowiedniego klucza. Maksymalnie 12 takiego upoważnienia można skonfigurować reguły w przestrzeni nazw usługi Relay. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z wszelkie prawa, które jest konfigurowana dla każdej przestrzeni nazw podczas najpierw jest zainicjowany.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu Współdzielonego, wygenerowane za pomocą określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token sygnatury dostępu Współdzielonego jest generowana z użyciem HMAC-funkcję skrótu SHA256 o ciągu zasobu, który składa się z identyfikatora URI zasobu, do której dostęp jest zgłoszone i utraty ważności za pomocą klucza kryptograficznego skojarzony z regułą autoryzacji.

Obsługa uwierzytelniania sygnatury dostępu Współdzielonego dla usługi Azure Relay jest dostępne w ramach zestawu Azure .NET SDK w wersji 2.0 lub nowszej. Sygnatury dostępu Współdzielonego obejmuje obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkie interfejsy API, które akceptują parametry połączenia jako parametru obejmują obsługę parametrów połączenia sygnatury dostępu Współdzielonego.

## <a name="next-steps"></a>Kolejne kroki

- Czytaj dalej [uwierzytelniania usługi Service Bus za pomocą sygnatur dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md) Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego.
- Zobacz [przewodnik dotyczący protokołu połączeń hybrydowych usługi Azure Relay](relay-hybrid-connections-protocol.md) szczegółowe informacje dotyczące możliwości połączeń hybrydowych.
- Dla odpowiednich informacji o usłudze Service BUS do uwierzytelniania i autoryzacji, zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png