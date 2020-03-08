---
title: Usługa Azure Event Grid zabezpieczeń i uwierzytelniania
description: W tym artykule opisano różne sposoby uwierzytelniania dostępu do zasobów Event Grid (webhook, subskrypcje, tematy niestandardowe)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925104"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Uwierzytelnianie dostępu do zasobów Event Grid

Usługa Azure Event Grid ma trzy typy uwierzytelniania:

* Dostarczanie zdarzeń elementu WebHook
* Subskrypcje zdarzeń
* Publikowanie tematu niestandardowego

## <a name="webhook-event-delivery"></a>Dostarczanie zdarzeń elementu WebHook

Elementy Webhook są jednym z wielu sposobów, aby odbierać zdarzenia z usługi Azure Event Grid. Gdy nowe zdarzenie jest gotowy, usługi Event Grid wysyła żądanie HTTP do punktu końcowego skonfigurowanego ze zdarzeniem w treści żądania.

Podobnie jak wielu innych usług, które obsługują elementy webhook usługi Event Grid wymaga udowodnić własność punktu końcowego usługi elementu Webhook, zanim zacznie dostarczanie zdarzeń do określonego punktu końcowego. To wymaganie zapobiega przepełnieniu punktu końcowego usługi za pomocą zdarzeń przez złośliwego użytkownika. Gdy używasz dowolnego z trzech usług platformy Azure wymienionych poniżej infrastruktury platformy Azure automatycznie obsługuje tej weryfikacji:

* Azure Logic Apps za pomocą [łącznika Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation za pośrednictwem [elementu webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions z [wyzwalaczem Event Grid](../azure-functions/functions-bindings-event-grid.md)

Jeśli używasz dowolnego typu punktu końcowego, takie jak wyzwalacz HTTP na podstawie funkcji platformy Azure, swój kod punktu końcowego musi wziąć udział w uzgadniania weryfikacji za pomocą usługi Event Grid. Usługa Event Grid obsługuje sprawdzanie poprawności subskrypcji na dwa sposoby.

1. **Uzgadnianie ValidationCode (** programowe): w przypadku kontrolowania kodu źródłowego dla punktu końcowego ta metoda jest zalecana. Tworzenie subskrypcji zdarzeń usługi Event Grid wysyła zdarzenie sprawdzania poprawności subskrypcji do punktu końcowego usługi. Schemat tego zdarzenia jest podobne do innych zdarzeń usługi Event Grid. Część danych tego zdarzenia zawiera właściwość `validationCode`. Aplikacja sprawdza, czy żądanie weryfikacji dotyczy subskrypcji zdarzeń oczekiwanego i funkcją kod sprawdzania poprawności do usługi Event Grid. Ten mechanizm uzgadniania jest obsługiwana we wszystkich wersjach usługi Event Grid.

2. **Uzgadnianie ValidationURL (ręczne)** : w niektórych przypadkach nie można uzyskać dostępu do kodu źródłowego punktu końcowego w celu zaimplementowania uzgadniania ValidationCode. Na przykład w przypadku korzystania z usługi innej firmy (np. [zapier](https://zapier.com) lub [IFTTT](https://ifttt.com/)) nie można programowo odpowiedzieć na kod weryfikacyjny.

   Począwszy od wersji 2018-05-01-preview usługi Event Grid obsługuje uzgadniania ręcznej weryfikacji. Jeśli tworzysz subskrypcję zdarzeń za pomocą zestawu SDK lub narzędzia korzystającego z interfejsu API w wersji 2018-05-01-Preview lub nowszej, Event Grid wysyła Właściwość `validationUrl` w części danych zdarzenia walidacji subskrypcji. Aby ukończyć uzgadnianie, należy znaleźć tego adresu URL w danych zdarzeń i ręcznie Wyślij żądanie Pobierz do niego. Można użyć klienta REST lub przeglądarki sieci web.

   Podany adres URL jest prawidłowy przez 5 minut. W tym czasie stan aprowizacji subskrypcji zdarzeń jest `AwaitingManualAction`. Jeśli weryfikacja ręczna nie zostanie zakończona w ciągu 5 minut, stan aprowizacji zostanie ustawiony na `Failed`. Należy utworzyć subskrypcję zdarzeń ponownie, przed rozpoczęciem ręcznej weryfikacji.

    Ten mechanizm uwierzytelniania wymaga również, aby punkt końcowy elementu webhook zwracał kod stanu HTTP 200, aby wiedział, że wpis dla zdarzenia sprawdzania poprawności został zaakceptowany, zanim będzie można go umieścić w trybie walidacji ręcznej. Innymi słowy, jeśli punkt końcowy zwraca 200, ale nie zwraca ponownie odpowiedzi na weryfikację, tryb przechodzi do trybu walidacji ręcznej. Jeśli w ciągu 5 minut znajduje się adres URL sprawdzania poprawności, uzgadnianie weryfikacji jest uznawane za pomyślne.

> [!NOTE]
> Sprawdzanie poprawności przy użyciu certyfikatów z podpisem własnym nie jest obsługiwane. Zamiast tego użyj podpisanego certyfikatu z urzędu certyfikacji (CA).

### <a name="validation-details"></a>Szczegóły sprawdzania poprawności

* W momencie utworzenie/aktualizacja subskrypcji zdarzeń usługi Event Grid publikuje zdarzenie sprawdzania poprawności subskrypcji do docelowego punktu końcowego. 
* Zdarzenie zawiera wartość nagłówka "Æg event-type: SubscriptionValidation".
* Treść zdarzeń ma ten sam schemat, jak inne zdarzenia usługi Event Grid.
* Właściwość eventType zdarzenia jest `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Właściwość Data zdarzenia zawiera właściwość `validationCode` z losowo generowanym ciągiem. Na przykład "validationCode: acb13...".
* Dane zdarzenia zawierają również właściwość `validationUrl` z adresem URL w celu ręcznej weryfikacji subskrypcji.
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
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
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

Należy zwrócić kod stanu odpowiedzi HTTP 200. Akceptowany protokół HTTP 202 nie został rozpoznany jako prawidłowa odpowiedź na weryfikację subskrypcji Event Grid. Żądanie HTTP musi zakończyć się w ciągu 30 sekund. Jeśli operacja nie zakończy się w ciągu 30 sekund, operacja zostanie anulowana i może zostać ponowiona po 5 sekundach. Jeśli wszystkie próby zakończą się niepowodzeniem, będzie ono traktowane jako błąd uzgadniania walidacji.

Lub można ręcznie sprawdzić poprawność subskrypcji, wysyłając żądanie GET do adresu URL sprawdzania poprawności. Subskrypcja zdarzeń pozostaje w stanie oczekiwania, aż zweryfikowane. Adres URL walidacji używa portu 553. Jeśli reguły zapory blokują port 553, reguły mogą wymagać aktualizacji w celu pomyślnego uzgodnienia ręcznego.

Aby zapoznać się z przykładem obsługi uzgadniania subskrypcji, zobacz [ C# przykład](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Lista kontrolna

Podczas tworzenia subskrypcji zdarzeń, jeśli widzisz komunikat o błędzie, taki jak "próba zweryfikowania dostarczonego https:\//Your-Endpoint-here nie powiodła się. Aby uzyskać więcej informacji, odwiedź stronę https:\//aka.ms/esvalidation ", co oznacza, że wystąpił błąd uzgadniania walidacji. Aby rozwiązać ten problem, sprawdź następujące aspekty:

* Czy kontrolujesz kod aplikacji działający w docelowym punkcie końcowym? Na przykład jeśli piszesz wyzwalacz HTTP na podstawie funkcji platformy Azure, czy masz dostęp do kodu aplikacji, aby wprowadzić zmiany?
* Jeśli masz dostęp do kodu aplikacji, należy zaimplementować mechanizm uzgadnianie ValidationCode na podstawie, jak pokazano w powyższym przykładzie.

* Jeśli nie masz dostępu do kodu aplikacji (na przykład, jeśli używasz usługi innej firmy, która obsługuje elementy webhook), można użyć mechanizmu ręczne uzgadniania. Upewnij się, że używasz wersji interfejsu API 2018-05-01-preview lub nowszej (Instalacja rozszerzenia siatki zdarzeń wiersza polecenia platformy Azure) do odbierania validationUrl w przypadku sprawdzania poprawności. Aby zakończyć ręczne uzgadnianie walidacji, Pobierz wartość właściwości `validationUrl` i przejdź do tego adresu URL w przeglądarce internetowej. Jeśli weryfikacja zakończy się pomyślnie, powinien zostać wyświetlony komunikat w przeglądarce sieci web, czy Weryfikacja powiodła się. Zobaczysz, że provisioningState subskrypcji zdarzeń to "Powodzenie". 

### <a name="event-delivery-security"></a>Zabezpieczenia dostarczania zdarzeń

#### <a name="azure-ad"></a>Azure AD

Punkt końcowy elementu webhook można zabezpieczyć za pomocą Azure Active Directory do uwierzytelniania i autoryzowania Event Grid do publikowania zdarzeń w punktach końcowych. Konieczne będzie utworzenie aplikacji Azure Active Directory, utworzenie zasady roli i usługi w aplikacji, która autoryzuje Event Grid, i skonfigurowanie subskrypcji zdarzeń do korzystania z aplikacji usługi Azure AD. [Dowiedz się, jak skonfigurować usługi AAD za pomocą Event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parametry zapytania
Punkt końcowy usługi elementu webhook można zabezpieczyć, dodając parametry zapytania do adresu URL elementu webhook, podczas tworzenia subskrypcji zdarzeń. Ustaw jeden z tych parametrów zapytania jako wpis tajny, taki jak [token dostępu](https://en.wikipedia.org/wiki/Access_token). Element webhook umożliwiają Rozpoznawaj zdarzenia pochodzi z usługi Event Grid przy użyciu prawidłowe uprawnienia klucza tajnego. Usługa Event Grid będzie zawierać te parametry zapytań w każdym dostarczanie zdarzeń do elementu webhook.

Podczas edytowania subskrypcji zdarzeń parametry zapytania nie są wyświetlane ani zwracane, chyba że w [interfejsie wiersza polecenia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)platformy Azure jest używany parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) .

Na koniec jest należy pamiętać, że usługi Azure Event Grid obsługuje tylko punkty końcowe HTTPS elementu webhook.

## <a name="event-subscription"></a>Subskrypcja zdarzeń

Aby subskrybować zdarzenie, muszą potwierdzić, że masz dostęp do źródła zdarzeń i obsługi. Potwierdzające, że jesteś właścicielem elementu WebHook zostało opisane w poprzedniej sekcji. Jeśli używasz programu obsługi zdarzeń, który nie jest element WebHook (np. event hub lub kolejki magazynu), należy się dostęp do zapisu do tego zasobu. To sprawdzenie uprawnień uniemożliwia nieautoryzowany użytkownik wysyła zdarzenia do zasobu.

Użytkownik musi mieć uprawnienie **Microsoft. EventGrid/EventSubscriptions/Write** do zasobu, który jest źródłem zdarzenia. To uprawnienie jest konieczne, ponieważ nowej subskrypcji w zakresie zasobów jest pisany. Wymagany zasób różni się zależnie od tego, czy w przypadku subskrybowania tematu system lub tematu niestandardowego. Oba typy są opisane w tej sekcji.

### <a name="system-topics-azure-service-publishers"></a>Tematy dotyczące systemu (wydawcy usług platformy Azure)

Tematy systemu musisz mieć uprawnienie do zapisu nowej subskrypcji zdarzeń w zakresie zasobów publikowanie zdarzenia. Format zasobu to: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Na przykład w celu subskrybowania zdarzenia na koncie magazynu o nazwie Moja **ACCT**wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write dla: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tematy niestandardowe

Niestandardowe tematy musisz mieć uprawnienie do zapisu nowej subskrypcji zdarzeń w zakresie tematu usługi event grid. Format zasobu to: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Na przykład w celu subskrybowania niestandardowego tematu o nazwie Moja **tematu**wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write do: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikowanie tematu niestandardowego

Tematy niestandardowe Użyj sygnatury dostępu współdzielonego (SAS) lub uwierzytelniania za pomocą klucza. Firma Microsoft zaleca sygnatury dostępu Współdzielonego, ale uwierzytelnianie za pomocą klucza udostępnia proste programowanie i jest zgodny z wielu istniejących wydawców elementu webhook. 

Wartość uwierzytelniania jest uwzględniona w nagłówku HTTP. Dla sygnatury dostępu współdzielonego Użyj **AEG-SAS-token** dla wartości nagłówka. W przypadku uwierzytelniania za pomocą klucza Użyj wartości nagłówka **AEG-SAS-Key** .

### <a name="key-authentication"></a>Uwierzytelnianie za pomocą klucza

Uwierzytelnianie za pomocą klucza jest najprostsza forma uwierzytelniania. Użyj formatu: `aeg-sas-key: <your key>`

Na przykład polega na przekazaniu klucza przy użyciu:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Tokeny sygnatur dostępu Współdzielonego dla usługi Event Grid obejmują zasobu, czasu wygaśnięcia i podpis. Format tokenu sygnatury dostępu współdzielonego to: `r={resource}&e={expiration}&s={signature}`.

Zasób jest ścieżka do tematu usługi event grid do którego są wysyłane zdarzenia. Na przykład prawidłowa ścieżka zasobu to: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Podpis jest generowanie z kluczem.

Na przykład prawidłowa wartość **AEG-SAS-token** to:

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

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Wszystkie zdarzenia lub dane zapisywane na dysku przez usługę Event Grid są szyfrowane przez klucz zarządzany przez firmę Microsoft, dzięki czemu są szyfrowane w stanie spoczynku. Ponadto maksymalny okres czasu, przez jaki zdarzenia lub dane są przechowywane, wynosi 24 godziny zgodnie z [zasadami ponowienia Event Grid](delivery-and-retry.md). Event Grid automatycznie usunie wszystkie zdarzenia lub dane po 24 godzinach lub czas wygaśnięcia zdarzenia na żywo, w zależności od tego, która wartość jest mniejsza.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [Informacje o Event Grid](overview.md)
