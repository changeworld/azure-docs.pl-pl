---
title: Model uwierzytelniania i zabezpieczeń — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: W tym artykule opisano model uwierzytelniania i zabezpieczeń usługi Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b347423c28b4c615f90f325ead462b9d3e8e9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822582"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Usługa Azure Event Hubs — uwierzytelnianie i model zabezpieczeń

Model zabezpieczeń usługi Azure Event Hubs, spełnia następujące wymagania:

* Tylko klientów, które są dostępne prawidłowe poświadczenia mogą wysyłać dane do Centrum zdarzeń.
* Klient nie może personifikować innego klienta.
* Nieautoryzowanego klienta może zostać zablokowany z wysyłania danych do Centrum zdarzeń.

## <a name="client-authentication"></a>Uwierzytelnianie klienta

Model zabezpieczeń usługi Event Hubs jest oparty na kombinacji [sygnatury dostępu współdzielonego (SAS)](../service-bus-messaging/service-bus-sas.md) tokenów i *wydawców zdarzeń*. Wydawca zdarzeń definiuje wirtualnego punktu końcowego dla Centrum zdarzeń. Wydawca należy używać tylko do wysyłania komunikatów do Centrum zdarzeń. Nie jest możliwe, aby otrzymać komunikaty od wydawcy.

Zazwyczaj Centrum zdarzeń, wykorzystuje jedną wydawcą na klienta. Wszystkie komunikaty, które są wysyłane do wszystkich wydawców Centrum zdarzeń są dodawane do kolejki w tym Centrum zdarzeń. Wydawcy włączyć bardziej precyzyjnej kontroli dostępu i ograniczania przepustowości.

Każdy klient usługi Event Hubs jest przypisany unikatowy token, który zostanie przekazany do klienta. Tokeny są tworzone w taki sposób, że każdy unikatowy token udziela dostępu do innego wydawcy unikatowy. Klient, który posiada token można wysyłać tylko do jednego wydawcy, ale nie innego wydawcy. Jeśli wielu klientów mają ten sam token, każde z nich udostępnia wydawcy.

Chociaż nie jest to zalecane, istnieje możliwość pożyczkowe urządzeń za pomocą tokenów, które mają przyznaną bezpośredni dostęp do Centrum zdarzeń. Każde urządzenie, które zawiera ten token wiadomości można wysyłać bezpośrednio do tego Centrum zdarzeń. Z zastrzeżeniem ograniczania przepustowości nie będzie takiego urządzenia. Ponadto urządzenie nie jest na czarnej liście wysyłaniu do tego Centrum zdarzeń.

Wszystkie tokeny są podpisane za pomocą klucza sygnatury dostępu Współdzielonego. Zazwyczaj wszystkie tokeny są podpisane za pomocą tego samego klucza. Klienci nie rozpoznają określony klucz; Zapobiega to produkcyjny tokenów innych klientów.

### <a name="create-the-sas-key"></a>Tworzenie klucza SAS

Podczas tworzenia przestrzeni nazw usługi Event Hubs, usługa automatycznie generuje klucz sygnatury dostępu Współdzielonego 256-bitowe o nazwie **RootManageSharedAccessKey**. Ta reguła ma skojarzoną parą klucze podstawowe i pomocnicze, które mają przyznaną wysyłania nasłuchiwania i zarządzania prawami do przestrzeni nazw. Można również utworzyć dodatkowe klucze. Zalecane jest, utworzenia klucza, że przyznaje wysyłać uprawnienia określonym Centrum zdarzeń. W pozostałej części tego tematu, zakłada się, o nazwie ten klucz **EventHubSendKey**.

Poniższy przykład tworzy klucz tylko do wysłania, podczas tworzenia Centrum zdarzeń:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generowanie tokenów

Można generować tokeny przy użyciu klucza sygnatury dostępu Współdzielonego. Musi mieć tylko jeden token na klienta. Następnie można tworzyć tokeny przy użyciu następującej metody. Wszystkie tokeny są generowane przy użyciu **EventHubSendKey** klucza. Każdy token jest przypisany unikatowy identyfikator URI. Parametr "resource" odnosi się do identyfikatora URI punktu końcowego usługi (w tym przypadku Centrum zdarzeń).

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Po wywołaniu tej metody, identyfikator URI powinien być określony jako `https://<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Wszystkie tokeny identyfikator URI jest identyczna, z wyjątkiem produktów `PUBLISHER_NAME`, powinna ona być inna dla każdego tokenu. W idealnym przypadku `PUBLISHER_NAME` reprezentuje identyfikator klienta, który odbiera token.

Ta metoda generuje token o następującej strukturze:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Czas wygaśnięcia tokenu jest określony w sekundach od dnia 1 stycznia 1970. Oto przykład tokenu:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Zwykle tokeny mają czas, który przypomina lub przekracza czas działania klienta. Jeśli klient ma możliwość uzyskania nowego tokenu, można tokenów z informacjami o krótszej życia.

### <a name="sending-data"></a>Wysyłanie danych

Po utworzeniu tokeny własny unikatowy token był zaopatrzony każdego klienta.

Gdy klient wysyła dane do Centrum zdarzeń, tagi jego żądanie wysłania z tokenem. Aby zapobiec atakujący korzystający z podsłuchiwaniu i kradzież tokenu, komunikacja między klientem i Centrum zdarzeń musi wystąpić za pośrednictwem szyfrowanego kanału.

### <a name="blacklisting-clients"></a>Wpisywanie na czarną listę klientów

Jeśli token zostanie ukradzione przez osobę atakującą, osoba atakująca może personifikować klienta, którego token zostały skradzione. Blacklisting klienta powoduje wyświetlenie tego klienta bezużyteczne do czasu otrzymania nowego tokenu, który używa innego wydawcy.

## <a name="authentication-of-back-end-applications"></a>Uwierzytelnianie aplikacji zaplecza

Do uwierzytelniania aplikacji zaplecza, które korzystają z danych generowanych przez klientów usługi Event Hubs, usługa Event Hubs wykorzystuje model zabezpieczeń, który jest podobny do modelu, który służy do tematów usługi Service Bus. Grupy konsumentów usługi Event Hubs jest równoważne z subskrypcji tematu usługi Service Bus. Klienta można utworzyć grupy odbiorców, jeśli żądanie utworzenia grupy odbiorców jest powiązany za pomocą tokenu, że przyznaje zarządzać uprawnieniami dla Centrum zdarzeń lub przestrzeni nazw, do której należy Centrum zdarzeń. Klient może zużywać dane z grupy konsumentów, jeśli żądanie odbioru jest powiązany za pomocą tokenu, który przyznaje prawa odbioru w danej grupie odbiorców Centrum zdarzeń i przestrzeni nazw, do której należy Centrum zdarzeń.

Bieżąca wersja usługi Service Bus nie obsługuje zasad sygnatury dostępu Współdzielonego dla poszczególnych subskrypcji. To samo dotyczy grup konsumentów usługi Event Hubs. Obsługa sygnatury dostępu Współdzielonego zostanie dodana dla obu funkcji w przyszłości.

W przypadku braku uwierzytelniania sygnatury dostępu Współdzielonego dla grupy konsumentów poszczególnych klucze sygnatur dostępu Współdzielonego służy również do zabezpieczenia wszystkich grup odbiorców za pomocą klucza wspólnego. Takie podejście umożliwia aplikacji korzystanie z danych pochodzących z dowolnych z grupy konsumentów Centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Event Hubs, odwiedź następujące tematy:

* [Omówienie usługi Event Hubs]
* [Omówienie sygnatur dostępu współdzielonego]
* [Przykładowe aplikacje korzystające z usługi Event Hubs]

[Omówienie usługi Event Hubs]: event-hubs-what-is-event-hubs.md
[Przykładowe aplikacje korzystające z usługi Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Omówienie sygnatur dostępu współdzielonego]: ../service-bus-messaging/service-bus-sas.md

