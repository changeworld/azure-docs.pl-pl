---
title: Uwierzytelnianie i autoryzacja usługi Azure Relay | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie uwierzytelniania sygnatury dostępu współdzielonego (SAS) za pomocą usługi Azure Relay.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514582"
---
# <a name="azure-relay-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja usługi Azure Relay

Aplikacje mogą uwierzytelniać się w usłudze Azure Relay przy użyciu uwierzytelniania sygnatury dostępu współdzielonego (SAS). Uwierzytelnianie sas umożliwia aplikacjom uwierzytelnianie w usłudze Azure Relay przy użyciu klucza dostępu skonfigurowanego w obszarze nazw przekazywania. Następnie można użyć tego klucza do wygenerowania tokenu podpisu dostępu udostępnionego, którego klienci mogą używać do uwierzytelniania w usłudze przekazywania.

## <a name="shared-access-signature-authentication"></a>Uwierzytelnianie podpisu dostępu współdzielonego

[Uwierzytelnianie SAS](../service-bus-messaging/service-bus-sas.md) umożliwia udzielenie użytkownikowi dostępu do zasobów usługi Azure Relay z określonymi prawami. Uwierzytelnianie sygnatury dostępu Współdzielonego obejmuje konfigurację klucza kryptograficznego z skojarzonymi prawami w zasobie. Klienci mogą następnie uzyskać dostęp do tego zasobu, przedstawiając token sygnatury dostępu współdzielonego, który składa się z identyfikatora URI zasobu, do którego uzyskuje się dostęp, i wygaśnięcia podpisanego za pomocą skonfigurowanego klucza.

Klucze sygnatury dostępu Współdzielonego można skonfigurować w przestrzeni nazw przekazu. W przeciwieństwie do obsługi wiadomości usługi Service Bus, [usługi Relay Hybrid Connections](relay-hybrid-connections-protocol.md) obsługuje nieautoryzowanych lub anonimowych nadawców. Podczas tworzenia jednostki można włączyć dostęp anonimowy, jak pokazano na poniższym zrzucie ekranu z portalu:

![][0]

Aby użyć sygnatury dostępu Współdzielonego, można skonfigurować obiekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) w obszarze nazw przekazu, który składa się z następujących elementów:

* *KeyName,* który identyfikuje regułę.
* *PrimaryKey* to klucz kryptograficzny używany do podpisywania/sprawdzania poprawności tokenów sygnatury dostępu Współdzielonego.
* *SecondaryKey* jest kluczem kryptograficznym używanym do podpisywania/sprawdzania poprawności tokenów sygnatury dostępu Współdzielonego.
* *Prawa* reprezentujące kolekcję praw nasłuchiwać, wysyłać lub zarządzać prawami przyznanymi.

Reguły autoryzacji skonfigurowane na poziomie obszaru nazw mogą udzielić dostępu do wszystkich połączeń przekazywania w obszarze nazw dla klientów z tokenami podpisanymi przy użyciu odpowiedniego klucza. W przestrzeni nazw przekazywania można skonfigurować maksymalnie 12 takich reguł autoryzacji. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) ze wszystkimi prawami jest skonfigurowany dla każdego obszaru nazw, gdy jest po raz pierwszy aprowizowana.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu współdzielonego wygenerowanego przy użyciu określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token sygnatury dostępu Współdzielonego jest generowany przy użyciu HMAC-SHA256 ciągu zasobu, który składa się z identyfikatora URI zasobu, do którego ma być zgłoszony dostęp, oraz wygaśnięcia z kluczem kryptograficznym skojarzonym z regułą autoryzacji.

Obsługa uwierzytelniania SAS dla usługi Azure Relay jest uwzględniona w zestawie Azure .NET SDK w wersji 2.0 i nowszych. SAS obejmuje obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkie interfejsy API, które akceptują parametry połączenia jako parametr, obejmują obsługę ciągów połączeń sygnatury dostępu Współdzielonego.

## <a name="next-steps"></a>Następne kroki

- Kontynuuj czytanie [uwierzytelniania usługi Service Bus za pomocą podpisów dostępu współdzielonego, aby](../service-bus-messaging/service-bus-sas.md) uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego.
- Szczegółowe informacje na temat możliwości połączeń hybrydowych można znaleźć [w przewodniku po protokole połączeń hybrydowych](relay-hybrid-connections-protocol.md) usługi Azure Relay.
- Aby uzyskać odpowiednie informacje dotyczące uwierzytelniania i autoryzacji usługi Service Bus Messaging, zobacz [Uwierzytelnianie i autoryzacja usługi Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png