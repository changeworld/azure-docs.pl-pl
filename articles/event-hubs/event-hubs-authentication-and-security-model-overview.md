---
title: Omówienie modelu uwierzytelniania i zabezpieczeń usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 5264930dcb802c2a58abc179bdd0041acc9f58d0
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311373"
---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs

Model zabezpieczeń usługi Azure Event Hubs spełnia następujące wymagania:

* Tylko klientów, które są dostępne prawidłowe poświadczenia może wysyłać dane do Centrum zdarzeń.
* Klient nie może spersonifikować innego klienta.
* Nieautoryzowanego klienta, który może zostać zablokowany wysyłania danych do Centrum zdarzeń.

## <a name="client-authentication"></a>Uwierzytelnianie klienta

Model zabezpieczeń usługi Event Hubs jest oparty na kombinacji [dostępu sygnatury dostępu Współdzielonego](../service-bus-messaging/service-bus-sas.md) tokeny i *wydawców zdarzeń*. Wydawca zdarzeń definiuje wirtualnego punktu końcowego dla Centrum zdarzeń. Wydawcą może służyć tylko do wysyłania komunikatów do Centrum zdarzeń. Nie jest możliwe odbieranie komunikatów z wydawcą.

Zazwyczaj Centrum zdarzeń zostają jednego wydawcy na klienta. Wszystkie komunikaty, które są wysyłane do żadnych wydawców Centrum zdarzeń są dodawane do kolejki w tym Centrum zdarzeń. Wydawcy Włącz szczegółowej kontroli dostępu i ograniczania przepustowości.

Każdy klient usługi Event Hubs jest przypisany unikatowy tokenem, który zostanie przekazany do klienta. Tokeny są tworzone w taki sposób, że każdy unikatowy token nieograniczony dostęp do różnych unikatowy wydawcy. Klient, który posiada tokenu można wysłać tylko jednego wydawcy, ale nie inne wydawcy. Jeśli wielu klientów współużytkować ten sam token, każde z nich udostępnia wydawcy.

Chociaż nie jest to zalecane, istnieje możliwość wyposażyć urządzeń z tokenami określającymi udzielenie bezpośredni dostęp do Centrum zdarzeń. Dowolne urządzenie, która przechowuje ten token może wysyłać komunikaty bezpośrednio do tego Centrum zdarzeń. Takie urządzenia nie będą podlegać ograniczania. Ponadto urządzenie nie może być na liście zabronionych numerów wysyłaniu do tego Centrum zdarzeń.

Wszystkie tokeny są podpisane za pomocą klucza sygnatury dostępu Współdzielonego. Zwykle wszystkie tokeny są podpisane za pomocą tego samego klucza. Klienci nie są znane klucza; Zapobiega to produkcyjny tokenów innych klientów.

### <a name="create-the-sas-key"></a>Utwórz klucz sygnatury dostępu Współdzielonego

Podczas tworzenia przestrzeni nazw usługi Event Hubs, usługa automatycznie generuje klucz sygnatury dostępu Współdzielonego 256-bitowe o nazwie **RootManageSharedAccessKey**. Ta reguła ma skojarzone pary klucze podstawowe i pomocnicze, które przyznać wysyłania nasłuchiwania i Zarządzanie prawami do przestrzeni nazw. Można również utworzyć dodatkowych kluczy. Zaleca się, że tworzenia klucza, że przyznaje wysyłać uprawnienia do konkretnego zdarzenia koncentratora. W pozostałej części tego tematu, zakłada się, o nazwie ten klucz **EventHubSendKey**.

W poniższym przykładzie jest tworzony klucz tylko do wysyłania podczas tworzenia Centrum zdarzeń:

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

Można generować tokeny przy użyciu klucza sygnatury dostępu Współdzielonego. Musi mieć tylko jeden token na klienta. Następnie można wyprodukować tokeny przy użyciu następującej metody. Wszystkie tokeny są generowane przy użyciu **EventHubSendKey** klucza. Każdy token jest przypisany unikatowy identyfikator URI.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Podczas wywoływania tej metody, należy określić identyfikator URI jako `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Wszystkie tokeny identyfikator URI jest taki sam, z wyjątkiem produktów `PUBLISHER_NAME`, powinny być różne dla każdego tokenu. W idealnym przypadku `PUBLISHER_NAME` reprezentuje identyfikator klienta, który odbiera token.

Ta metoda generuje token o następującej strukturze:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Czas wygaśnięcia tokenu jest określona w sekundach od 1 stycznia 1970. Poniżej przedstawiono przykład tokenu:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Zwykle tokeny mają cykl życia, który podobny lub przekroczenie czasu działania klienta. Jeśli klient ma możliwość uzyskać nowy token, można tokeny z krótszy czas działania.

### <a name="sending-data"></a>Wysyłanie danych

Po utworzeniu tokenów każdego klienta jest udostępniane z własną unikatową tokenu.

Gdy klient wysyła dane do Centrum zdarzeń, znaczniki jego żądanie wysłania z tokenem. Osobie atakującej z podsłuchiwaniu i kradzież token, komunikacja między klientem a Centrum zdarzeń musi nastąpić za pośrednictwem kanału szyfrowanego.

### <a name="blacklisting-clients"></a>List dozwolonych klientów

Token zostanie ukradzione przez osobę atakującą, osoba atakująca może personifikować klienta, których token został kradzieży. List dozwolonych klienta renderuje ten klient nie można użyć do czasu otrzymania nowy token, który używa innego wydawcę.

## <a name="authentication-of-back-end-applications"></a>Uwierzytelnianie przy użyciu zaplecza aplikacji

Na potrzeby uwierzytelniania aplikacji zaplecza, które wykorzystują dane generowane przez klientów usługi Event Hubs, usługa Event Hubs wykorzystuje model zabezpieczeń, która jest podobna do modelu, który służy do tematów usługi Service Bus. Centra zdarzeń w grupy odbiorców jest odpowiednikiem subskrypcję tematu usługi Service Bus. Klienta można utworzyć grupy odbiorców, jeśli żądanie utworzenia grupy odbiorców jest powiązany token że przyznaje zarządzać uprawnieniami do Centrum zdarzeń lub przestrzeni nazw, do którego należy Centrum zdarzeń. Klient może wykorzystywać dane z grupy odbiorców, jeśli żądania odbierania towarzyszy token, który przyznaje prawa receive w danej grupie odbiorców, Centrum zdarzeń lub przestrzeni nazw, do którego należy Centrum zdarzeń.

Bieżąca wersja usługi Service Bus nie obsługuje zasady sygnatury dostępu Współdzielonego dla pojedynczych subskrypcji. To samo dotyczy grupy konsumentów usługi Event Hubs. Obsługa sygnatury dostępu Współdzielonego zostanie dodana dla obu funkcji w przyszłości.

W przypadku braku uwierzytelniania sygnatury dostępu Współdzielonego dla grupy konsumentów poszczególnych kluczy SAS służy do bezpiecznego wszystkie grupy konsumentów ze wspólnego klucza. Takie podejście umożliwia aplikacji do pracy z danymi z dowolnej grupy konsumentów Centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Event Hubs, można znaleźć w następujących tematach:

* [Omówienie usługi Event Hubs]
* [Omówienie sygnatur dostępu współdzielonego]
* [Przykładowe aplikacje korzystające z usługi Event Hubs]

[Omówienie usługi Event Hubs]: event-hubs-what-is-event-hubs.md
[Przykładowe aplikacje korzystające z usługi Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Omówienie sygnatur dostępu współdzielonego]: ../service-bus-messaging/service-bus-sas.md

