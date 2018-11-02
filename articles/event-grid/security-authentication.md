---
title: Usługa Azure Event Grid zabezpieczeń i uwierzytelniania
description: Opis usługi Azure Event Grid i pojęć z nią związanych.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: babanisa
ms.openlocfilehash: fe13c424a3da91e92a04cceb807b98fd1ffe4db0
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914043"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid zabezpieczeń i uwierzytelniania 

Usługa Azure Event Grid ma trzy typy uwierzytelniania:

* Dostarczanie zdarzeń elementu WebHook
* Subskrypcje zdarzeń
* Publikowanie tematu niestandardowego

## <a name="webhook-event-delivery"></a>Dostarczanie zdarzeń elementu WebHook

Elementy Webhook są jednym z wielu sposobów, aby odbierać zdarzenia z usługi Azure Event Grid. Gdy jest to nowe zdarzenie jest gotowy, usługa EventGrid publikuje żądania HTTP do punktu końcowego skonfigurowanego ze zdarzeniem w treści żądania.

Podobnie jak wiele innych usług, które obsługują elementy webhook EventGrid wymaga potwierdzenia "własność" punktu końcowego usługi elementu Webhook, zanim zacznie dostarczanie zdarzeń do określonego punktu końcowego. To wymaganie jest zapobiec podejrzewający punktu końcowego docelowy punkt końcowy podczas dostarczania zdarzeń z EventGrid. Jednak przy użyciu jednej z trzech usług platformy Azure wymienionych poniżej, infrastruktury platformy Azure automatycznie obsługuje ten sprawdzania poprawności:

* Usługi Azure Logic Apps
* Usługa Azure Automation
* Usługa Azure Functions EventGrid wyzwalacza.

Jeśli używasz dowolnego typu punktu końcowego, takie jak wyzwalacz HTTP na podstawie funkcji platformy Azure, swój kod punktu końcowego musi wziąć udział w uzgadniania weryfikacji za pomocą EventGrid. EventGrid obsługuje dwa modele uzgadnianie różnych sprawdzania poprawności:

1. **Uzgadnianie ValidationCode**: podczas tworzenia subskrypcji zdarzeń EventGrid wpisy "zdarzenie sprawdzania poprawności subskrypcji" do punktu końcowego usługi. Schemat tego zdarzenia jest podobne do innych EventGridEvent i zawiera część danych to zdarzenie `validationCode` właściwości. Gdy aplikacja sprawdziła, to żądanie weryfikacji subskrypcji oczekiwanego zdarzenia, kod aplikacji musi reagowała wyświetlania ponownie kod sprawdzania poprawności, aby EventGrid. Ten mechanizm uzgadniania jest obsługiwana we wszystkich wersjach EventGrid.

2. **Uzgadnianie ValidationURL (ręczne uzgadnianie)**: W niektórych przypadkach, może nie mieć kontroli kodu źródłowego punktu końcowego w celu wykonania uzgadniania ValidationCode na podstawie. Na przykład, jeśli używasz usługi innych firm (np. [Zapier](https://zapier.com) lub [IFTTT](https://ifttt.com/)), można programowo nie może odpowiadać z powrotem kod sprawdzania poprawności. Począwszy od wersji 2018-05-01-preview EventGrid obsługuje teraz uzgadniania ręcznej weryfikacji. Jeżeli tworzysz subskrypcję zdarzeń przy użyciu zestawu SDK lub narzędzia, która używa interfejsu API w wersji 2018-05-01-preview lub nowszej, wysyła EventGrid `validationUrl` właściwość w ramach części danych zdarzeń sprawdzania poprawności subskrypcji. Aby ukończyć uzgadnianie, po prostu GET żądania na ten adres URL, za pomocą klienta REST lub przy użyciu przeglądarki sieci web. Adres URL podany sprawdzania poprawności jest prawidłowy tylko w przypadku około 10 minut. W tym czasie jest stan aprowizacji subskrypcji zdarzeń `AwaitingManualAction`. Jeśli nie wykonasz ręcznej weryfikacji w ciągu 10 minut, stanu aprowizacji jest równa `Failed`. Należy utworzyć subskrypcję zdarzeń ponownie, przed rozpoczęciem ręcznej weryfikacji.

Ten mechanizm ręcznej weryfikacji jest w wersji zapoznawczej. Aby jej użyć, musisz zainstalować [rozszerzenie usługi Event Grid](/cli/azure/azure-cli-extensions-list) dla [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Instalację można wykonać za pomocą polecenia `az extension add --name eventgrid`. Jeśli korzystasz z interfejsu API REST, upewnij się, używasz `api-version=2018-05-01-preview`.

### <a name="validation-details"></a>Szczegóły sprawdzania poprawności

* W momencie utworzenie/aktualizacja subskrypcji zdarzeń usługi Event Grid publikuje zdarzenie sprawdzania poprawności subskrypcji do docelowego punktu końcowego. 
* Zdarzenie zawiera wartość nagłówka "Æg event-type: SubscriptionValidation".
* Treść zdarzeń ma ten sam schemat, jak inne zdarzenia usługi Event Grid.
* Typ zdarzenia właściwości zdarzenia `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Właściwości danych zdarzenia zawiera `validationCode` właściwości z ciągiem generowanym losowo. Na przykład "validationCode: acb13...".
* Jeśli używasz interfejsu API w wersji 2018-05-01-preview dane zdarzenia obejmowały `validationUrl` właściwość adres URL ręcznej weryfikacji subskrypcji.
* Tablica zawiera tylko zdarzenia sprawdzania poprawności. Inne zdarzenia są wysyłane w oddzielnym żądaniu po odsyłania kod sprawdzania poprawności.
* Zestawy SDK płaszczyzny danych EventGrid mają klas odpowiadających danych zdarzeń sprawdzania poprawności subskrypcji i odpowiedź weryfikacji subskrypcji.

W poniższym przykładzie przedstawiono przykładowy SubscriptionValidationEvent:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Aby udowodnić własność punktu końcowego, odsyłania kod sprawdzania poprawności we właściwości validationResponse, jak pokazano w poniższym przykładzie:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Lub można ręcznie sprawdzić poprawność subskrypcji, wysyłając żądanie GET do adresu URL sprawdzania poprawności. Subskrypcja zdarzeń pozostaje w stanie oczekiwania, aż zweryfikowane.

Można znaleźć przykładowy języka C#, pokazujący sposób obsługi uzgadnianie sprawdzania poprawności subskrypcji na https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs.

### <a name="checklist"></a>Lista kontrolna

Podczas tworzenia subskrypcji zdarzeń, jeśli jest wyświetlany komunikat o błędzie, taki jak "próby sprawdź poprawność podanego punktu końcowego https://your-endpoint-here nie powiodło się. Aby uzyskać więcej informacji, odwiedź stronę https://aka.ms/esvalidation"oznacza, że jest błąd uzgadnianie sprawdzania poprawności. Aby rozwiązać ten problem, sprawdź następujące aspekty:

* Masz kontrolę nad kodem aplikacji w docelowy punkt końcowy? Na przykład jeśli piszesz wyzwalacz HTTP na podstawie funkcji platformy Azure, czy masz dostęp do kodu aplikacji, aby wprowadzić zmiany?
* Jeśli masz dostęp do kodu aplikacji, należy zaimplementować mechanizm uzgadnianie ValidationCode na podstawie, jak pokazano w powyższym przykładzie.

* Jeśli nie masz dostępu do kodu aplikacji (na przykład, jeśli używasz usługi innej firmy, która obsługuje elementy webhook), można użyć mechanizmu ręczne uzgadniania. Upewnij się, że używasz wersji interfejsu API 2018-05-01-preview lub nowszej (Instalacja rozszerzenia siatki zdarzeń wiersza polecenia platformy Azure) do odbierania validationUrl w przypadku sprawdzania poprawności. Aby ukończyć uzgadnianie ręcznej weryfikacji, należy uzyskać wartość `validationUrl` właściwości i odwiedź tego adresu URL w przeglądarce sieci web. Jeśli weryfikacja zakończy się pomyślnie, powinien zostać wyświetlony komunikat w przeglądarce sieci web, czy Weryfikacja powiodła się. Zobaczysz, że provisioningState subskrypcji zdarzeń to "Powodzenie". 

### <a name="event-delivery-security"></a>Zabezpieczenia dostarczania zdarzeń

Punkt końcowy usługi elementu webhook można zabezpieczyć, dodając parametry zapytania do adresu URL elementu webhook, podczas tworzenia subskrypcji zdarzeń. Ustaw jeden z tych parametrów zapytania jako klucz tajny, takich jak [token dostępu](https://en.wikipedia.org/wiki/Access_token). Element webhook umożliwiają Rozpoznawaj zdarzenia pochodzi z usługi Event Grid przy użyciu prawidłowe uprawnienia. Usługa Event Grid będzie zawierać te parametry zapytań w każdym dostarczanie zdarzeń do elementu webhook.

Podczas edytowania subskrypcji zdarzeń, parametry zapytania nie są wyświetlane lub zwracane, chyba że [— obejmują — pełny — — adres url punktu końcowego](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parametr jest używany na platformie Azure [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Na koniec jest należy pamiętać, że usługi Azure Event Grid obsługuje tylko punkty końcowe HTTPS elementu webhook.

## <a name="event-subscription"></a>Subskrypcja zdarzeń

Aby subskrybować zdarzenie, muszą potwierdzić, że masz dostęp do źródła zdarzeń i obsługi. Potwierdzające, że jesteś właścicielem elementu WebHook zostało opisane w poprzedniej sekcji. Jeśli używasz programu obsługi zdarzeń, który nie jest element WebHook (np. event hub lub kolejki magazynu), należy się dostęp do zapisu do tego zasobu. To sprawdzenie uprawnień uniemożliwia nieautoryzowany użytkownik wysyła zdarzenia do zasobu.

Konieczne jest posiadanie **Microsoft.EventGrid/EventSubscriptions/Write** uprawnienia dla zasobu, który jest źródłem zdarzeń. To uprawnienie jest konieczne, ponieważ nowej subskrypcji w zakresie zasobów jest pisany. Wymagany zasób różni się zależnie od tego, czy w przypadku subskrybowania tematu system lub tematu niestandardowego. Oba typy są opisane w tej sekcji.

### <a name="system-topics-azure-service-publishers"></a>Tematy dotyczące systemu (wydawcy usług platformy Azure)

Tematy systemu musisz mieć uprawnienie do zapisu nowej subskrypcji zdarzeń w zakresie zasobów publikowanie zdarzenia. Format zasobu jest następujący: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Na przykład, aby subskrybować zdarzenie na koncie magazynu o nazwie **myacct**, musisz mieć uprawnienie Microsoft.EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tematy niestandardowe

Niestandardowe tematy musisz mieć uprawnienie do zapisu nowej subskrypcji zdarzeń w zakresie tematu usługi event grid. Format zasobu jest następujący: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Na przykład do subskrybowania tematu niestandardowego o nazwie **mytopic**, musisz mieć uprawnienie Microsoft.EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikowanie tematu niestandardowego

Tematy niestandardowe Użyj sygnatury dostępu współdzielonego (SAS) lub uwierzytelniania za pomocą klucza. Firma Microsoft zaleca sygnatury dostępu Współdzielonego, ale uwierzytelnianie za pomocą klucza udostępnia proste programowanie i jest zgodny z wielu istniejących wydawców elementu webhook. 

Wartość uwierzytelniania jest uwzględniona w nagłówku HTTP. Sygnatury dostępu Współdzielonego, można użyć **Æg sygnatury dostępu współdzielonego token** dla wartości nagłówka. W przypadku uwierzytelniania za pomocą klucza Użyj **Æg sygnatury dostępu współdzielonego klucza** dla wartości nagłówka.

### <a name="key-authentication"></a>Uwierzytelnianie za pomocą klucza

Uwierzytelnianie za pomocą klucza jest najprostsza forma uwierzytelniania. Użyj formatu: `aeg-sas-key: <your key>`

Na przykład polega na przekazaniu klucza przy użyciu:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Tokeny sygnatur dostępu Współdzielonego dla usługi Event Grid obejmują zasobu, czasu wygaśnięcia i podpis. Format tokenu sygnatury dostępu Współdzielonego: `r={resource}&e={expiration}&s={signature}`.

Zasób jest ścieżka do tematu usługi event grid do którego są wysyłane zdarzenia. Na przykład ścieżka prawidłowy zasób jest: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Podpis jest generowanie z kluczem.

Na przykład prawidłowy **Æg sygnatury dostępu współdzielonego token** wartość to:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Poniższy przykład tworzy token SAS do użycia z usługą Event Grid:

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

## <a name="management-access-control"></a>Kontrola dostępu do zarządzania

Usługa Azure Event Grid umożliwia kontrolowanie poziomu dostępu do różnych użytkowników może wykonać różne operacje zarządzania, takich jak lista subskrypcji zdarzeń, tworzenie nowych i generowanie kluczy. Kontrola dostępu oparta na rolach platformy Azure (RBAC) korzysta z usługi Event Grid.

### <a name="operation-types"></a>Typy operacji

Usługa Event Grid obsługuje następujące akcje:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Ostatnie trzy operacje zwracają potencjalnie poufne informacje, które pobiera przefiltrowane z normalnych operacji odczytu. Zalecane jest, można ograniczyć dostęp do tych operacji. 

### <a name="built-in-roles"></a>Wbudowane role

Zarządzanie subskrypcjami zdarzeń usługi Event Grid zapewnia dwie wbudowane role. Są one ważne podczas implementowania [domen zdarzeń](event-domains.md) ponieważ zapewniają użytkownikom uprawnienia muszą subskrybować tematy w domenie zdarzeń. Te role koncentrują się na subskrypcji zdarzeń i nie przyznać dostęp do akcji, takich jak tworzenie tematów.

Możesz [przypisania tych ról użytkownikowi lub grupie](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Współautor EventSubscription EventGrid (wersja zapoznawcza)**: zarządzanie operacjami subskrypcji usługi Event Grid

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
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

**Czytnik EventSubscription EventGrid (wersja zapoznawcza)**: odczytu subskrypcji usługi Event Grid

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
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

Jeśli musisz określić uprawnienia, które są inne niż wbudowane role mogą tworzyć role niestandardowe.

Poniżej przedstawiono przykładowe definicje ról usługi Event Grid, których użytkownicy mogą wykonać różne operacje. Te role niestandardowe różnią się od wbudowanych ról, ponieważ powodują udzielenie szerszy dostęp niż po prostu subskrypcji zdarzeń.

**EventGridReadOnlyRole.json**: Zezwalaj tylko na operacji tylko do odczytu.

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

**EventGridNoDeleteListKeysRole.json**: Zezwalaj na akcje po ograniczony, ale nie zezwalaj akcje usuwania.

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

**EventGridContributorRole.json**: zezwala na wszystkie akcje siatki zdarzeń.

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

Można tworzyć niestandardowe role z [PowerShell](../role-based-access-control/custom-roles-powershell.md), [wiersza polecenia platformy Azure](../role-based-access-control/custom-roles-cli.md), i [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Event Grid](overview.md)
