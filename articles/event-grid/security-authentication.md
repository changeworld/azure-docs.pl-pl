---
title: Azure Event Grid zabezpieczenia i uwierzytelnianie
description: Opis usługi Azure Event Grid i pojęć z nią związanych.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 8fe85685a41e05b5132157453a6dcbc81c2399af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825768"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid zabezpieczenia i uwierzytelnianie 

Azure Event Grid ma trzy typy uwierzytelniania:

* Dostarczanie zdarzeń elementu webhook
* Subskrypcje zdarzeń
* Publikowanie tematów niestandardowych

## <a name="webhook-event-delivery"></a>Dostarczanie zdarzeń elementu webhook

Elementy webhook to jeden z wielu sposobów odbierania zdarzeń z Azure Event Grid. Gdy nowe zdarzenie jest gotowe, usługa Event Grid ogłasza żądanie HTTP do skonfigurowanego punktu końcowego przy użyciu zdarzenia w treści żądania.

Podobnie jak w przypadku wielu innych usług, które obsługują elementy webhook, Event Grid wymaga potwierdzenia własności punktu końcowego elementu webhook przed rozpoczęciem dostarczania zdarzeń do tego punktu końcowego. To wymaganie uniemożliwia złośliwemu użytkownikowi przelanie punktu końcowego ze zdarzeniami. W przypadku korzystania z jednej z trzech usług platformy Azure wymienionych poniżej infrastruktura platformy Azure automatycznie obsługuje tę weryfikację:

* Azure Logic Apps za pomocą [łącznika Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation za pośrednictwem [elementu webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions z [wyzwalaczem Event Grid](../azure-functions/functions-bindings-event-grid.md)

Jeśli używasz innego typu punktu końcowego, takiego jak oparty na wyzwalaczu HTTP funkcja platformy Azure, kod punktu końcowego musi uczestniczyć w uzgadnianiu walidacji z Event Grid. Event Grid obsługuje dwa sposoby walidacji subskrypcji.

1. **Uzgadnianie ValidationCode (** programowe): w przypadku kontrolowania kodu źródłowego dla punktu końcowego ta metoda jest zalecana. W momencie tworzenia subskrypcji zdarzeń Event Grid wysyła do punktu końcowego zdarzenie weryfikacji subskrypcji. Schemat tego zdarzenia jest podobny do dowolnego innego zdarzenia Event Grid. Część danych tego zdarzenia zawiera właściwość `validationCode`. Aplikacja weryfikuje, czy żądanie weryfikacji dotyczy oczekiwanej subskrypcji zdarzeń, i echo kod weryfikacyjny do Event Grid. Ten mechanizm uzgadniania jest obsługiwany we wszystkich wersjach Event Grid.

2. **Uzgadnianie ValidationURL (ręczne)** : w niektórych przypadkach nie można uzyskać dostępu do kodu źródłowego punktu końcowego w celu zaimplementowania uzgadniania ValidationCode. Na przykład w przypadku korzystania z usługi innej firmy (np. [zapier](https://zapier.com) lub [IFTTT](https://ifttt.com/)) nie można programowo odpowiedzieć na kod weryfikacyjny.

   Począwszy od wersji 2018-05-01-Preview, Event Grid obsługuje ręczne uzgadnianie walidacji. Jeśli tworzysz subskrypcję zdarzeń za pomocą zestawu SDK lub narzędzia korzystającego z interfejsu API w wersji 2018-05-01-Preview lub nowszej, Event Grid wysyła Właściwość `validationUrl` w części danych zdarzenia walidacji subskrypcji. Aby ukończyć uzgadnianie, Znajdź ten adres URL w danych zdarzenia i ręcznie Wyślij do niego żądanie GET. Możesz użyć klienta REST lub przeglądarki sieci Web.

   Podany adres URL jest prawidłowy przez 5 minut. W tym czasie stan aprowizacji subskrypcji zdarzeń jest `AwaitingManualAction`. Jeśli weryfikacja ręczna nie zostanie zakończona w ciągu 5 minut, stan aprowizacji zostanie ustawiony na `Failed`. Przed rozpoczęciem walidacji ręcznej należy utworzyć subskrypcję zdarzeń.

    Ten mechanizm uwierzytelniania wymaga również, aby punkt końcowy elementu webhook zwracał kod stanu HTTP 200, aby wiedział, że wpis dla zdarzenia sprawdzania poprawności został zaakceptowany, zanim będzie można go umieścić w trybie walidacji ręcznej. Innymi słowy, jeśli punkt końcowy zwraca 200, ale nie zwraca ponownie odpowiedzi na weryfikację, tryb przechodzi do trybu walidacji ręcznej. Jeśli adres URL sprawdzania poprawności jest dostępny w ciągu 5 minut, uzgadnianie walidacji jest uznawane za pomyślne.

> [!NOTE]
> Sprawdzanie poprawności przy użyciu certyfikatów z podpisem własnym nie jest obsługiwane. Zamiast tego użyj podpisanego certyfikatu z urzędu certyfikacji (CA).

### <a name="validation-details"></a>Szczegóły walidacji

* W momencie tworzenia/aktualizowania subskrypcji zdarzeń Event Grid ogłasza zdarzenie weryfikacji subskrypcji w docelowym punkcie końcowym. 
* Zdarzenie zawiera wartość nagłówka "AEG-Event-Type: SubscriptionValidation".
* Treść zdarzenia ma ten sam schemat co inne zdarzenia Event Grid.
* Właściwość eventType zdarzenia jest `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Właściwość Data zdarzenia zawiera właściwość `validationCode` z losowo generowanym ciągiem. Na przykład "validationCode: acb13...".
* Dane zdarzenia zawierają również właściwość `validationUrl` z adresem URL w celu ręcznej weryfikacji subskrypcji.
* Tablica zawiera tylko zdarzenie walidacji. Inne zdarzenia są wysyłane w oddzielnym żądaniu po ECHA kodu weryfikacyjnego.
* Zestawy SDK EventGrid dataplanes mają klasy odpowiadające danym zdarzenia weryfikacji subskrypcji i odpowiedzi na weryfikację subskrypcji.

Przykład SubscriptionValidationEvent pokazano w następującym przykładzie:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Aby udowodnić własność punktu końcowego, echo kod weryfikacyjny we właściwości validationResponse, jak pokazano w następującym przykładzie:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Należy zwrócić kod stanu odpowiedzi HTTP 200. Akceptowany protokół HTTP 202 nie został rozpoznany jako prawidłowa odpowiedź na weryfikację subskrypcji Event Grid. Żądanie HTTP musi zakończyć się w ciągu 30 sekund. Jeśli operacja nie zakończy się w ciągu 30 sekund, operacja zostanie anulowana i może zostać ponownie podjęta po 5 sekundach. Jeśli wszystkie próby zakończą się niepowodzeniem, będzie ono traktowane jako błąd uzgadniania walidacji.

Można też ręcznie zweryfikować subskrypcję, wysyłając żądanie GET do adresu URL walidacji. Subskrypcja zdarzeń pozostaje w stanie oczekiwania do momentu zweryfikowania. Adres URL walidacji używa portu 553. Jeśli reguły zapory blokują port 553, reguły mogą wymagać aktualizacji w celu pomyślnego uzgodnienia ręcznego.

Aby zapoznać się z przykładem obsługi uzgadniania subskrypcji, zobacz [ C# przykład](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Lista kontrolna

Podczas tworzenia subskrypcji zdarzeń, jeśli widzisz komunikat o błędzie, taki jak "próba zweryfikowania dostarczonego https:\//Your-Endpoint-here nie powiodła się. Aby uzyskać więcej informacji, odwiedź stronę https:\//aka.ms/esvalidation ", co oznacza, że wystąpił błąd uzgadniania walidacji. Aby rozwiązać ten problem, sprawdź następujące aspekty:

* Czy masz kontrolę nad kodem aplikacji w docelowym punkcie końcowym? Jeśli na przykład używasz funkcji platformy Azure opartej na wyzwalaczu HTTP, czy masz dostęp do kodu aplikacji, aby wprowadzić w nim zmiany?
* Jeśli masz dostęp do kodu aplikacji, zaimplementuj mechanizm uzgadniania oparty na ValidationCode, jak pokazano w powyższym przykładzie.

* Jeśli nie masz dostępu do kodu aplikacji (na przykład jeśli używasz usługi innej firmy, która obsługuje elementy webhook), możesz użyć mechanizmu uzgadniania ręcznego. Upewnij się, że korzystasz z wersji interfejsu API 2018-05-01-Preview lub nowszej (Zainstaluj Event Grid rozszerzenia interfejsu wiersza polecenia platformy Azure), aby otrzymać validationUrl w zdarzeniu walidacji. Aby zakończyć ręczne uzgadnianie walidacji, Pobierz wartość właściwości `validationUrl` i przejdź do tego adresu URL w przeglądarce internetowej. Jeśli sprawdzanie poprawności zakończy się pomyślnie, w przeglądarce internetowej powinien zostać wyświetlony komunikat informujący o pomyślnym sprawdzeniu poprawności. Zobaczysz, że provisioningState subskrypcji zdarzeń to "powodzenie". 

### <a name="event-delivery-security"></a>Zabezpieczenia dostarczania zdarzeń

Punkt końcowy elementu webhook można zabezpieczyć przez dodanie parametrów zapytania do adresu URL elementu webhook podczas tworzenia subskrypcji zdarzeń. Ustaw jeden z tych parametrów zapytania jako wpis tajny, taki jak [token dostępu](https://en.wikipedia.org/wiki/Access_token). Element webhook może użyć klucza tajnego do rozpoznawania zdarzenia z Event Grid z prawidłowymi uprawnieniami. Event Grid będą zawierać te parametry zapytania w każdym dostarczeniu zdarzenia do elementu webhook.

Podczas edytowania subskrypcji zdarzeń parametry zapytania nie są wyświetlane ani zwracane, chyba że w [interfejsie wiersza polecenia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)platformy Azure jest używany parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) .

Na koniec należy pamiętać, że Azure Event Grid obsługuje tylko punkty końcowe elementu webhook protokołu HTTPS.

## <a name="event-subscription"></a>Subskrypcja zdarzeń

Aby subskrybować zdarzenie, musisz udowodnić, że masz dostęp do źródła i obsługi zdarzeń. Dowód, że jesteś członkiem elementu webhook, został objęty poprzednią sekcją. Jeśli używasz procedury obsługi zdarzeń, która nie jest elementem webhook (takim jak centrum zdarzeń lub magazyn kolejki), musisz mieć dostęp do zapisu do tego zasobu. To sprawdzenie uprawnień uniemożliwia wysyłanie zdarzeń do zasobu przez nieautoryzowanego użytkownika.

Użytkownik musi mieć uprawnienie **Microsoft. EventGrid/EventSubscriptions/Write** do zasobu, który jest źródłem zdarzenia. To uprawnienie jest wymagane, ponieważ piszesz nową subskrypcję w zakresie zasobu. Wymagany zasób różni się w zależności od tego, czy subskrybujesz temat systemowy czy temat niestandardowy. Oba typy zostały opisane w tej sekcji.

### <a name="system-topics-azure-service-publishers"></a>Tematy systemowe (wydawcy usług platformy Azure)

W przypadku tematów systemowych należy mieć uprawnienia do napisania nowej subskrypcji zdarzeń w zakresie zasobu, który publikuje zdarzenie. Format zasobu to: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Na przykład w celu subskrybowania zdarzenia na koncie magazynu o nazwie Moja **ACCT**wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write dla: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tematy niestandardowe

W przypadku niestandardowych tematów należy mieć uprawnienia do zapisywania nowej subskrypcji zdarzeń w zakresie tematu usługi Event Grid. Format zasobu to: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Na przykład w celu subskrybowania niestandardowego tematu o nazwie Moja **tematu**wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write do: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikowanie tematów niestandardowych

Tematy niestandardowe wykorzystują sygnaturę dostępu współdzielonego (SAS) lub uwierzytelnianie klucza. Zalecamy używanie sygnatury dostępu współdzielonego, ale uwierzytelnianie klucza zapewnia proste programowanie i jest zgodne z wieloma istniejącymi wydawcami elementów webhook. 

Wartość uwierzytelniania należy uwzględnić w nagłówku HTTP. Dla sygnatury dostępu współdzielonego Użyj **AEG-SAS-token** dla wartości nagłówka. W przypadku uwierzytelniania za pomocą klucza Użyj wartości nagłówka **AEG-SAS-Key** .

### <a name="key-authentication"></a>Uwierzytelnianie klucza

Uwierzytelnianie klucza jest najprostszą formą uwierzytelniania. Użyj formatu: `aeg-sas-key: <your key>`

Na przykład przekazujesz klucz z:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Tokeny sygnatury dostępu współdzielonego dla Event Grid obejmują zasób, czas wygaśnięcia i sygnaturę. Format tokenu sygnatury dostępu współdzielonego to: `r={resource}&e={expiration}&s={signature}`.

Zasób jest ścieżką do tematu usługi Event Grid, do którego są wysyłane zdarzenia. Na przykład prawidłowa ścieżka zasobu to: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Sygnatura jest generowana z klucza.

Na przykład prawidłowa wartość **AEG-SAS-token** to:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Poniższy przykład tworzy token sygnatury dostępu współdzielonego do użycia z Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Access Control zarządzania

Azure Event Grid umożliwia kontrolowanie poziomu dostępu nadawanego różnym użytkownikom w celu wykonywania różnych operacji zarządzania, takich jak subskrypcje zdarzeń listy, tworzenie nowych i generowanie kluczy. Event Grid używa kontroli dostępu opartej na rolach (RBAC) na platformie Azure.

### <a name="operation-types"></a>Typy operacji

Event Grid obsługuje następujące akcje:

* Microsoft. EventGrid/*/Read
* Microsoft. EventGrid/*/Write
* Microsoft. EventGrid/*/Delete
* Microsoft. EventGrid/eventSubscriptions/getFullUrl/akcja
* Microsoft. EventGrid/tematy/listKeys/akcja
* Microsoft. EventGrid/tematy/regenerateKey/akcja

Ostatnie trzy operacje zwracają potencjalnie tajne informacje, które są filtrowane z normalnych operacji odczytu. Zaleca się ograniczenie dostępu do tych operacji. 

### <a name="built-in-roles"></a>Wbudowane role

Event Grid udostępnia dwie wbudowane role do zarządzania subskrypcjami zdarzeń. Są one ważne podczas implementowania [domen zdarzeń](event-domains.md) , ponieważ zapewniają użytkownikom uprawnienia wymagane do subskrybowania tematów w domenie zdarzeń. Role te są skoncentrowane na subskrypcjach zdarzeń i nie udzielają dostępu do akcji, takich jak tworzenie tematów.

[Te role można przypisać do użytkownika lub grupy](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Współautor EventGrid EventSubscription**: Zarządzanie operacjami subskrypcji Event Grid

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**: Odczytuj Event Grid subskrypcje

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Role niestandardowe

Jeśli musisz określić uprawnienia, które są inne niż wbudowane role, możesz utworzyć role niestandardowe.

Poniżej przedstawiono Event Grid przykładowe definicje ról, które umożliwiają użytkownikom podejmowanie różnych akcji. Te role niestandardowe różnią się od wbudowanych ról, ponieważ udzielają szerszego dostępu niż tylko subskrypcje zdarzeń.

**EventGridReadOnlyRole. JSON**: Zezwalaj tylko na operacje tylko do odczytu.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole. JSON**: Zezwalaj na ograniczone akcje post, ale nie Zezwalaj na akcje usuwania.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole. JSON**: zezwala na wszystkie akcje związane z siatką zdarzeń.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Role niestandardowe można tworzyć przy użyciu [programu PowerShell](../role-based-access-control/custom-roles-powershell.md), [interfejsu wiersza polecenia platformy Azure i usługi](../role-based-access-control/custom-roles-cli.md) [rest](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [Informacje o Event Grid](overview.md)
