---
title: Usługa Azure Service Bus uwierzytelnianie i autoryzacja | Dokumentacja firmy Microsoft
description: Uwierzytelnianie aplikacji do usługi Service Bus przy użyciu uwierzytelniania sygnatury dostępu współdzielonego (SAS).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7c5a45504b7c44d97ff2250663ef9c47ef6e3595
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714510"
---
# <a name="service-bus-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja w usłudze Service Bus

Aplikacje uzyskują dostęp do zasobów usługi Azure Service Bus przy użyciu uwierzytelniania tokenu sygnatury dostępu współdzielonego (SAS). Za pomocą sygnatury dostępu Współdzielonego, aplikacje obecne token do usługi Service Bus, który został podpisany za pomocą klucza symetrycznego znane zarówno do wystawcy tokenów i Service Bus (dlatego "udostępnione") i ten klucz jest bezpośrednio skojarzone z regułą określone prawa dostępu, takich jak uprawnienia do udzielania odbieranie nasłuchiwania lub wysyłać wiadomości. Zasady sygnatury dostępu Współdzielonego to albo skonfigurowany w przestrzeni nazw lub bezpośrednio na jednostki, takie jak kolejki lub tematu, co umożliwia kontrolę dostępu szczegółowe.

Tokeny sygnatur dostępu Współdzielonego wygenerowany przez klienta usługi Service Bus bezpośrednio lub mogą być generowane przez niektóre pośrednich token wystawianie punktu końcowego, z którym klient wchodzi w interakcję. Na przykład system może wymagać klienta do wywołania usługi Active Directory chronionych w sieci web usługi punkt końcowy autoryzacji aby potwierdzić swoją tożsamość i prawa dostępu do systemu, a usługi sieci web, a następnie zwraca odpowiednie usługi Service Bus tokenu. Ten token sygnatury dostępu Współdzielonego można łatwo generować za pomocą dostawcy tokenu usługi Service Bus, dołączony do zestawu SDK platformy Azure. 

> [!IMPORTANT]
> Jeśli używasz usługi Azure Active Directory Access Control (znany także jako usługa Access Control Service lub usługi ACS) usługi Service Bus, należy pamiętać, że obsługa ta metoda teraz ograniczone i należy zmigrować aplikacja korzysta z sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) i [w tym artykule](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Uwierzytelniania sygnatury dostępu współużytkowanego

[Uwierzytelniania sygnatury dostępu Współdzielonego](service-bus-sas.md) umożliwia udzielenie użytkownikom dostępu do zasobów usługi Service Bus, przy użyciu określonych praw. Uwierzytelniania sygnatury dostępu Współdzielonego w usłudze Service Bus obejmuje konfigurację klucza kryptograficznego ze skojarzonymi prawami do zasobu usługi Service Bus. Klienci mogą zatem uzyskiwać dostęp do tego zasobu, prezentując tokenu sygnatury dostępu Współdzielonego, który składa się z identyfikatora URI, dostęp do zasobu i wygaśnięcia podpisany przy użyciu skonfigurowanego klucza.

Klucze sygnatur dostępu współdzielonego można skonfigurować w przestrzeni nazw usługi Service Bus. Klucz ma zastosowanie do wszystkich jednostek obsługi komunikatów w ramach tej przestrzeni nazw. Klucze można również skonfigurować na tematów i kolejek usługi Service Bus. Sygnatury dostępu Współdzielonego jest również obsługiwana na [usługi Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Aby korzystać z sygnatury dostępu Współdzielonego, można skonfigurować [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) obiektu w przestrzeni nazw, kolejki lub tematu. Ta reguła zawiera następujące elementy:

* *Nazwa klucza*: określa reguły.
* *PrimaryKey*: klucz kryptograficzny używany do logowania i weryfikacji tokenów sygnatur dostępu Współdzielonego.
* *Klucz pomocniczy*: klucz kryptograficzny używany do logowania i weryfikacji tokenów sygnatur dostępu Współdzielonego.
* *Prawa*: reprezentuje kolekcję **nasłuchiwania**, **wysyłania**, lub **Zarządzaj** prawa.

Reguły autoryzacji konfigurowane na poziomie przestrzeni nazw można przyznać dostęp do wszystkich jednostek w przestrzeni nazw dla klientów z tokenami podpisany przy użyciu odpowiedniego klucza. Można skonfigurować maksymalnie 12 takie zasady autoryzacji w przestrzeni nazw, kolejki lub tematu usługi Service Bus. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z wszelkie prawa, które jest konfigurowana dla każdej przestrzeni nazw podczas najpierw jest zainicjowany.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu Współdzielonego, wygenerowane za pomocą określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token sygnatury dostępu Współdzielonego jest generowana z użyciem HMAC-funkcję skrótu SHA256 o ciągu zasobu, który składa się z identyfikatora URI zasobu, do której dostęp jest zgłoszone i utraty ważności za pomocą klucza kryptograficznego skojarzony z regułą autoryzacji.

Obsługa uwierzytelniania sygnatury dostępu Współdzielonego dla usługi Service Bus jest dostępna w przypadku zestawu Azure .NET SDK w wersji 2.0 lub nowszej. Sygnatury dostępu Współdzielonego obejmuje obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkie interfejsy API, które akceptują parametry połączenia jako parametru obejmują obsługę parametrów połączenia sygnatury dostępu Współdzielonego.

## <a name="next-steps"></a>Kolejne kroki

- Czytaj dalej [uwierzytelniania usługi Service Bus za pomocą sygnatur dostępu współdzielonego](service-bus-sas.md) Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego.
- Jak [migracji z usługi Azure Active Directory Access Control (ACS) do autoryzacji sygnatura dostępu współdzielonego](service-bus-migrate-acs-sas.md).
- [Zmiany można włączyć usługi ACS przestrzenie nazw](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Dla odpowiednich informacji na temat usługi Azure Relay uwierzytelniania i autoryzacji, zobacz [usługi Azure Relay uwierzytelnianie i autoryzacja](../service-bus-relay/relay-authentication-and-authorization.md). 

