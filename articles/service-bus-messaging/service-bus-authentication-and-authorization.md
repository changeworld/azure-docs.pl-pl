---
title: Uwierzytelnianie i autoryzacja usługi Azure Service Bus | Dokumenty firmy Microsoft
description: Uwierzytelnianie aplikacji w usłudze Service Bus przy użyciu uwierzytelniania sygnatury dostępu współdzielonego (SAS).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013244"
---
# <a name="service-bus-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja w usłudze Service Bus

Aplikacje uzyskują dostęp do zasobów usługi Azure Service Bus przy użyciu uwierzytelniania tokenu usługi Shared Access Signature (SAS). W przypadku usługi SAS aplikacje prezentują token usługi Service Bus, który został podpisany przy zastosowaniu klucza symetrycznego znanego zarówno wystawcy tokenu, jak i usługi Service Bus (stąd "udostępniony") i ten klucz jest bezpośrednio skojarzony z regułą przyznającą określone prawa dostępu, takie jak uprawnienie do odbierania/nasłuchiwania lub wysyłania wiadomości. Reguły sygnatury dostępu Współdzielonego są skonfigurowane w obszarze nazw lub bezpośrednio na jednostkach, takich jak kolejka lub temat, co pozwala na precyzyjną kontrolę dostępu.

Tokeny sygnatury dostępu Współdzielonego mogą być generowane bezpośrednio przez klienta usługi Service Bus lub mogą być generowane przez jakiś pośredni token emitujący punkt końcowy, z którym klient wchodzi w interakcję. Na przykład system może wymagać od klienta wywołania chronionego punktu końcowego usługi sieci Web autoryzacji usługi active directory w celu udowodnienia jego tożsamości i praw dostępu do systemu, a następnie usługa sieci web zwraca odpowiedni token usługi Service Bus. Ten token sygnatury dostępu Współdzielonego można łatwo wygenerować przy użyciu dostawcy tokenu usługi Service Bus zawarte w zestawie Azure SDK. 

> [!IMPORTANT]
> Jeśli używasz usługi Azure Active Directory Access Control (znany również jako usługa kontroli dostępu lub usługi ACS) z usługą Service Bus, należy pamiętać, że obsługa tej metody jest teraz ograniczona i należy przeprowadzić migrację aplikacji do korzystania z sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) i ten [artykuł](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
Integracja usługi Azure Active Directory (Azure AD) dla zasobów usługi Service Bus zapewnia kontrolę dostępu opartą na rolach (RBAC) dla szczegółowej kontroli nad dostępem klienta do zasobów. Kontroli dostępu opartej na rolach (RBAC) można użyć do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniony przez usługę Azure AD w celu zwrócenia tokenu OAuth 2.0. Token może służyć do autoryzowania żądania dostępu do zasobu usługi Service Bus (kolejka, temat, itp.).

Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelnianie przy użyciu tożsamości zarządzanych](service-bus-managed-service-identity.md)
- [Uwierzytelnianie przy użyciu aplikacji](authenticate-application.md)

> [!IMPORTANT]
> Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2.0 zwracanego przez usługę Azure AD zapewnia doskonałe bezpieczeństwo i łatwość użycia za pośrednictwem sygnatur dostępu współdzielonego (SAS). Dzięki usłudze Azure AD nie ma potrzeby przechowywania tokenów w kodzie i potencjalnych luk w zabezpieczeniach. Firma Microsoft zaleca, aby używać przy użyciu usługi Azure AD z aplikacjami usługi Azure Service Bus, jeśli to możliwe. 


## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego
[Uwierzytelnianie SAS](service-bus-sas.md) umożliwia udzielenie użytkownikowi dostępu do zasobów usługi Service Bus z określonymi prawami. Uwierzytelnianie sygnatury dostępu Współdzielonego w usłudze Service Bus obejmuje konfigurację klucza kryptograficznego z skojarzonymi prawami w zasobie usługi Service Bus. Klienci mogą następnie uzyskać dostęp do tego zasobu, przedstawiając token sygnatury dostępu współdzielonego, który składa się z identyfikatora URI zasobu, do którego uzyskuje się dostęp, i wygaśnięcia podpisanego za pomocą skonfigurowanego klucza.

Klucze sygnatury dostępu Współdzielonego można skonfigurować w obszarze nazw usługi Service Bus. Klucz ma zastosowanie do wszystkich jednostek obsługi wiadomości w tej przestrzeni nazw. Można również skonfigurować klucze w kolejkach i tematach usługi Service Bus. Sas jest również obsługiwany w [usłudze Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Aby użyć sygnatury dostępu Współdzielonego, można skonfigurować obiekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) w obszarze nazw, w kolejce lub w temacie. Ta reguła składa się z następujących elementów:

* *KeyName*: identyfikuje regułę.
* *PrimaryKey*: klucz kryptograficzny używany do podpisywania/sprawdzania poprawności tokenów sygnatury dostępu Współdzielonego.
* *SecondaryKey*: klucz kryptograficzny używany do podpisywania/sprawdzania poprawności tokenów sygnatury dostępu Współdzielonego.
* *Prawa:* reprezentuje kolekcję praw **nasłuchiwać,** **wysyłać**lub **zarządzać.**

Reguły autoryzacji skonfigurowane na poziomie obszaru nazw mogą udzielić dostępu do wszystkich jednostek w obszarze nazw dla klientów z tokenami podpisanymi przy użyciu odpowiedniego klucza. Można skonfigurować maksymalnie 12 takich reguł autoryzacji w obszarze nazw, kolejce lub temacie usługi Service Bus. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) ze wszystkimi prawami jest skonfigurowany dla każdego obszaru nazw, gdy jest po raz pierwszy aprowizowana.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu współdzielonego wygenerowanego przy użyciu określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token sygnatury dostępu Współdzielonego jest generowany przy użyciu HMAC-SHA256 ciągu zasobu, który składa się z identyfikatora URI zasobu, do którego ma być zgłoszony dostęp, oraz wygaśnięcia z kluczem kryptograficznym skojarzonym z regułą autoryzacji.

Obsługa uwierzytelniania SAS dla usługi Service Bus jest uwzględniona w zestawie Azure .NET SDK w wersji 2.0 i nowszej. SAS obejmuje obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkie interfejsy API, które akceptują parametry połączenia jako parametr, obejmują obsługę ciągów połączeń sygnatury dostępu Współdzielonego.

## <a name="next-steps"></a>Następne kroki

- Kontynuuj czytanie [uwierzytelniania usługi Service Bus za pomocą podpisów dostępu współdzielonego, aby](service-bus-sas.md) uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego.
- Jak [przeprowadzić migrację z kontroli dostępu usługi Azure Active Directory (ACS) do autoryzacji podpisu dostępu współdzielonego](service-bus-migrate-acs-sas.md).
- [Zmiany w przestrzeniach nazw włączonych w u. ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Aby uzyskać odpowiednie informacje dotyczące uwierzytelniania i autoryzacji usługi Azure Relay, zobacz [Uwierzytelnianie i autoryzacja usługi Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md). 

