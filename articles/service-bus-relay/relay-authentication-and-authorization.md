---
title: Azure przekazywania uwierzytelniania i autoryzacji | Dokumentacja firmy Microsoft
description: Omówienie uwierzytelniania dostępu do sygnatury dostępu Współdzielonego w przekaźnika usługi Azure
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 86a9cf2c1106180ba5c8c65849042784bfd2afcd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28018121"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure przekazywania uwierzytelniania i autoryzacji

Aplikacje mogą uwierzytelniać do przekazywania Azure przy użyciu uwierzytelniania dostępu do sygnatury dostępu Współdzielonego. Uwierzytelniania sygnatury dostępu Współdzielonego umożliwia aplikacjom uwierzytelniania w usłudze Azure przekazywania przy użyciu klawisza dostępu skonfigurowane na przestrzeń nazw przekazywania. Można następnie użyć tego klucza do wygenerowania tokenu sygnatura dostępu współdzielonego, której klienci mogą używać do uwierzytelniania usługi przekazywania.

## <a name="shared-access-signature-authentication"></a>Uwierzytelniania sygnatury dostępu współużytkowanego

[Uwierzytelniania sygnatury dostępu Współdzielonego](../service-bus-messaging/service-bus-sas.md) umożliwia udzielić użytkownikowi dostępu do zasobów Azure przekazywania przy użyciu określonych praw. Uwierzytelniania sygnatury dostępu Współdzielonego obejmuje konfigurację klucza kryptograficznego ze skojarzonymi prawami do zasobu. Klienci mogą następnie uzyskać dostęp do tego zasobu z uwzględnieniem tokenu sygnatury dostępu Współdzielonego, który składa się z identyfikatora URI uzyskują dostęp do zasobu i wygaśnięcia podpisana za pomocą skonfigurowanego klucza.

Na przestrzeni nazw przekazywania, można skonfigurować klucze dla skojarzeń zabezpieczeń. W przeciwieństwie do komunikatów usługi Service Bus, [połączeń hybrydowych przekazywania](relay-hybrid-connections-protocol.md) obsługuje nadawców nieautoryzowani lub anonimowe. Dostęp anonimowy dla jednostki można włączyć podczas tworzenia, jak pokazano na poniższym ekranie zrzut z portalu:

![][0]

Aby użyć sygnatury dostępu Współdzielonego, można skonfigurować [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) obiektu na przekazywania przestrzeni nazw, która składa się z następujących czynności:

* *KeyName* , które identyfikują regułę.
* *PrimaryKey* klucz kryptograficzny używany do logowania/sprawdzania poprawności tokeny sygnatury dostępu Współdzielonego.
* *Klucz pomocniczy* klucz kryptograficzny używany do logowania/sprawdzania poprawności tokeny sygnatury dostępu Współdzielonego.
* *Prawa* reprezentujący kolekcję elementów nasłuchiwania, wysyłania lub zarządzania przyznano prawa.

Reguły autoryzacji skonfigurowana na poziomie przestrzeni nazw mogą udzielać dostępu do wszystkich połączeń przekazywania w przestrzeni nazw dla klientów z tokenami podpisany przy użyciu odpowiedniego klucza. Maksymalnie 12 zezwolenia można skonfigurować reguły w przestrzeni nazw przekazywania. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z dowolnymi prawami jest konfigurowana dla każdej przestrzeni nazw podczas najpierw zostanie zainicjowana.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu Współdzielonego wygenerowanych przy użyciu określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Tokenu sygnatury dostępu Współdzielonego jest generowany przy użyciu HMAC SHA256 do ciągu zasobu, która składa się z identyfikatora URI zasobu, do którego dostęp jest określona i wygaśnięcia za pomocą klucza kryptograficznego skojarzonych z regułą autoryzacji.

Obsługę uwierzytelniania sygnatury dostępu Współdzielonego dla przekazywania Azure jest częścią zestawu Azure .NET SDK w wersji 2.0 lub nowszy. Sygnatury dostępu Współdzielonego zapewnia obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkich interfejsów API, które przyjmują ciąg połączenia jako parametr obsługują parametry połączenia SAS.

## <a name="next-steps"></a>Kolejne kroki

- Materiały [uwierzytelniania usługi Service Bus za pomocą sygnatury dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md) Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego.
- Zobacz [przewodnik protokołu połączeń hybrydowych przekazywania Azure](relay-hybrid-connections-protocol.md) szczegółowe informacje dotyczące możliwości połączeń hybrydowych było możliwe.
- Dla odpowiednich informacji na temat obsługi komunikatów magistrali usług uwierzytelniania i autoryzacji, zobacz [magistrali usług uwierzytelniania i autoryzacji](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png