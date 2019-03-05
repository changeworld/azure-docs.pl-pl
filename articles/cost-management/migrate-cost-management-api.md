---
title: Migracja z umową Enterprise Agreement do interfejsów API z umowy klienta firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia zrozumienie wpływu na interfejsy API, podczas migracji z programu Microsoft Enterprise Agreement (EA) z umową klienta firmy Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/02/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: a12360e60026fd4251cc232caccd1bc52a34d2ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344879"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migracja z umową Enterprise Agreement do interfejsów API umowy klienta firmy Microsoft

Ten artykuł ułatwia zrozumienie wpływu na interfejsy API, podczas migracji z programu Microsoft Enterprise Agreement (EA) z umową klienta firmy Microsoft. Następujące nowe interfejsy API są używane do wyświetlania, koszty i ceny:

- Interfejsu API arkusza cen dla klientów firmy Microsoft
  - Arkusz cen za pomocą faktury
  - Arkusz cen, rozliczeń profilu.
  - Arkusz cen dla okresu rozliczeniowego Otwórz
- Szczegóły użycia interfejsu API dla klientów firmy Microsoft
  - Szczegóły użycia za pomocą faktury dla rozliczeń profilu (Filtruj według części faktury)
    - Szczegóły użycia według daty użycia
    - Szczegóły użycia, aby uzyskać konto rozliczeniowe
    - Szczegóły użycia dla fakturowania profilu
    - Szczegóły użycia dla sekcji faktury
    - Szczegóły użycia dla subskrypcji
  - Szczegóły użycia od początku miesiąca
    - Szczegóły użycia, aby uzyskać konto rozliczeniowe
    - Szczegóły użycia dla fakturowania profilu
    - Szczegóły użycia dla sekcji faktury
    - Szczegóły użycia dla subskrypcji
  - Szczegóły użycia, okres rozliczeniowy
- Środki na korzystanie z interfejsu API dla umowy klienta firmy Microsoft
  - Bieżące saldo podsumowanie interfejsu API przez profil rozliczeń
  - Środki zdarzenia interfejsu API rozliczeń profilu.
  - Środki na wiele interfejsów API rozliczeń profilu.
- Interfejs API zapytań
- Opłaty za interfejs API
  - Opłaty za interfejs API przez konto rozliczeniowe
  - Opłaty za interfejs API rozliczeń profilu.
  - Opłaty za interfejs API przez sekcję faktury
- Interfejs API listy faktury
  - Karta profilu.
  - Za konto rozliczeniowe
- Interfejsy API rezerwacji
- Integracja interfejsu API usługi Power BI
- Eksportowanie interfejsu API
  - za konto rozliczeniowe
  - Karta profilu.
  - W sekcji faktury
- Transakcji interfejsu API
- Budżetów interfejsu API
- Koszt zagregowane interfejsu API

## <a name="effect-on-automation"></a>Wpływ na usługi automation

Interfejsy API używane za zgodą klienta Microsoft różnią się od tych w ramach umowy Enterprise Agreement. Może być zdefiniowano interfejsu API usługi automation wcześniej przy użyciu przedsiębiorstwa opartego na kluczach interfejsów API lub interfejsy API oparte na usłudze Azure Resource Manager dla rejestracji enterprise. Jeśli tak, należy zaktualizować konfigurację usługi automation nowszych interfejsów API używany z umowy klienta firmy Microsoft.

Aby wyświetlić listę starszych nieobsługiwany interfejsów API przez umowy klienta firmy Microsoft, zobacz [nieobsługiwany interfejsów API](#unsupported-apis).

Interfejsy API dla umowy klienta firmy Microsoft obsługują tylko uwierzytelnianie usługi Azure Resource Manager — nie obsługują uwierzytelniania opartego na kluczu interfejsu API.

## <a name="pricesheet-apis-show-prices-for-azure-services"></a>Interfejsy API arkusza cen Pokaż ceny za usługi platformy Azure

Interfejsy API arkusza cen dla umowy klienta firmy Microsoft zawierają uzyskać informacje dotyczące wszystkich usług, które są oceniane w przypadku rozliczeń cen na podstawie użycia ilości. Interfejs API nie obejmuje uprawnień zakupów. Na przykład uprawnienie zakupów w portalu Marketplace, wystąpienia zarezerwowanego ceny i oferty pomocy technicznej. Interfejs API zapewnia unikatową ceny dla każdej usługi określonego licznika i kolejność nazwy produktu.

### <a name="view-the-pricesheet-for-azure-consumption-services-by-billing-profile"></a>Aby wyświetlić arkusza cen dla usług Azure Consumption, rozliczeń profilu

Ceny dla wszystkich usług Azure Consumption pierwszy i innych firm mogą wyświetlać w arkusz cen na profil rozliczeniowy. Wyświetl ceny dla usługi użycia platformy Azure za pomocą interfejsu API. Podane dla profil rozliczeniowy ceny dotyczą wszystkich subskrypcji, które należą do profil rozliczeniowy.

- Format pliku CSV
  - POST -`https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31& format=csv`
- JSON format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json`

### <a name="view-current-open-billing-or-service-period-estimated-prices"></a>Widok bieżący otwartych rozliczeń lub usługi okres szacowane ceny

Aby wyświetlić szacowany ceny dla każdej usługi w środowisku otwartym bieżącego cyklu rozliczeniowego są wykonywane lub czasu usługi, możesz wyświetlić arkusza cen w formacie CSV lub JSON.

- Format pliku CSV
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`
- JSON format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

Podana w arkusza cen dla bieżącego okresu usługi zawsze ceny najnowszymi cenami dla licznika. Ceny są odświeżane raz dziennie, przy użyciu najnowszymi cenami dla zamówienia licznika i produkt.

Podczas przeglądania kosztów na podstawie ceny w otwartym okresie, są szacowane koszty. Ostatnia cena zablokowane na końcu okresu otwieranie usługi dotyczy użycie otwartego okresu.

Otwórz okres jest bieżącego czasu usługi, w których użycie nie zostało jeszcze sklasyfikowane wyceny i rozliczeń.

### <a name="view-invoice-by-billing-profile-prices"></a>Wyświetl fakturę przez rozliczeń ceny profilu

Ceny dla usługi użycia platformy Azure, które są rozliczane można wyświetlić w określonej faktury dla profil rozliczeniowy. Wyświetl ceny usług, które są wymienione na fakturze za pomocą interfejsu API. Faktura jest identyfikowany przez numer faktury.

- Format pliku CSV
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`

- JSON format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

`{invoiceId}` Wymienione wcześniej jest taka sama jak **płatnika** wyświetlane w pliku PDF Podsumowanie faktury. Oto przykład.

![Przykładowy obraz przedstawiający numer faktury, która odnosi się do identyfikatora faktury](./media/migrate-cost-management-api/invoicesummary.png)

## <a name="pricesheet-api-asynchronous-response"></a>Odpowiedź asynchroniczna interfejsu API arkusza cen

Interfejsy API obsługują [Azure REST asynchroniczne](../azure-resource-manager/resource-manager-async-operations.md) formatu. Po wywołaniu metody interfejsów API przy użyciu GET zobaczysz następującą odpowiedź:

```
            No Response Body
            HTTP Status 202 Accepted
```

 Następujące nagłówki są wysyłane z lokalizacją danych wyjściowych.

```
Location: https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
Azure-AsyncOperation: https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
   Retry-After: 10
 OData-EntityId: {operationId}
```

Klient może utworzyć wywołanie GET do lokalizacji. Odpowiedzi na wywołanie GET jest taka sama, jak pokazano wcześniej, dopóki nie osiągnie stan ukończenia lub niepowodzenia operacji przykładowe informacje. Po zakończeniu odpowiedzi na wywołanie GET dla lokalizacji zwraca tę samą odpowiedź tak, jakby operacja została wykonana w tym samym czasie. Oto przykład.

```
HTTP Status 200
i.e.
            {
    “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
    “name”: {operationId},
    “type”: “Microsoft.Consumption/operationResults”,
    “properties” : {
          “downloadUrl”: {urltoblob},
          “vaildTill”: “Date”
}
}
```

Ponadto klient może być wywołanie GET `Azure-AsyncOperation`. Punkt końcowy zwraca stan operacji. Oto przykład:

```
HTTP Status 200
               {
"id": “providers/Microsoft.Consumption/operationStatus/{operationId}",
"name": "operationId",
"status" : "Succeeded | Failed | Canceled",
"startTime": "<DateLiteral per ISO8601>",
"endTime": "<DateLiteral per ISO8601>",
"percentComplete": <double between 0 and 100>
"properties": {
    /\* The resource provider can choose the values here, but it should only be
       returned on a successful operation (status being "Succeeded"). \*/
},
"error" : {
    /\* This is the OData v4 format, used by the RPC and will go into the
     v2.2 Azure REST API guidelines \*/
    "code": "BadArgument",
    "message": "The provided database &#39;foo&#39; has an invalid username."
}
}
```

### <a name="pricesheet-fields"></a>Pola arkusza cen

Zapoznaj się z informacjami o [opis warunków w arkuszu cen dla umowy klienta Microsoft](../billing/billing-mca-understand-pricesheet.md) Aby wyświetlić wszystkie pola arkusza cen wraz z opisami.

Ceny na podstawie użycia, cenę użycia jest zablokowana do czasu usługi od początku okresu. Cena usługi jest stosowane i zablokowane w dniu jej zakupu. Okres service jest zazwyczaj miesiąca kalendarzowego dla usług platformy Azure.

Cena usługi Azure Consumption w umowie klienta firmy Microsoft jest unikatowy dla zamówienia produktów oraz miernika.

Dostępne są następujące pola, które służy do jednoznacznego identyfikowania cena dla usług Azure Consumption:

- productOrderName
- meterId (również meterName)

Cena dla umowy klienta firmy Microsoft jest zdefiniowane inaczej niż umowy Enterprise Agreement, których cena dla usług w rejestracji jest unikatowy dla:

- Product (Produkt)
- PartNumber
- Miernik
- oferty

### <a name="pricesheet-support"></a>Obsługa arkusza cen

Interfejsu API arkusza cen obsługuje obecnie tylko ceny umowy klienta firmy Microsoft dla systemu Azure firmy Microsoft i użycie innych usług. Nie obsługuje on ceny dla usługi Marketplace, Reserved Instances i korzyści hybrydowe platformy Azure (AHUB).

Interfejs API zapewnia tylko cena jednostki, która jest rozliczana korzystająca z usługi. Cena jest równe lub mniejsze niż cena wynegocjowanym.

## <a name="query-for-usage-and-costs"></a>Zapytanie do użycia i kosztów

Interfejs API szczegółów użycia zapewnia dostęp do platformy Azure i innych firm Marketplace usługi użycia i kosztów. Ceny są uzależnione od wynegocjowanym ceny dla umowy. Klienci z umowami EA przechodzenia do umowy klienta należy używać szczegóły użycia dla konta rozliczeniowego lub rozliczeń zakresu profilu. Może być zapytanie w dowolnym zakresie, z następującymi wyjątkami:

|   | Umowy klienta | Umowa Enterprise | Poszczególne umowy (PAYG) |
| --- | --- | --- | --- |
| Konto billingowe | Yes | Yes | ND |
| Profile rozliczeń (faktura) | Yes | Nie dotyczy. Zobacz konta rozliczeniowego. | Nie dotyczy. Zobacz subskrypcji. |
| Sekcje faktur | Nie. Profile rozliczeń za pomocą filtru. | ND | ND |
| Działy EA | ND | Yes | ND |
| Konta rejestracji umowy EA | ND | Yes | Nie dotyczy. Zobacz subskrypcji. |
| Grupy zarządzania | Nie. Użyj konta rozliczeniowego. | Yes | Yes |
| Subskrypcje | Yes | Yes | Yes |
| Grupy zasobów | Nie. Używać subskrypcji z filtrem. | Nie. Używać subskrypcji z filtrem. | Nie. Używać subskrypcji z filtrem. |

Właściwości zwrócony przez UsageDetails na typ umowy są różne. Zobacz [dokumentacji interfejsu API UsageDetails](/rest/api/consumption/usagedetails/) pełną listę.

### <a name="power-bi-integration"></a>Integracja z usługą Power BI

Klienci z umowami EA przechodzenia do umowy klienta Microsoft zaprzestania korzystania z żadnych istniejących integracji przy użyciu usługi Power BI, Microsoft Azure Consumption Insights przy użyciu informacji o ich umowy EA. Zamiast tego można użyć:

- Usługa Power BI Desktop — Tworzenie nowej usługi Power BI raporty za pomocą umowy Microsoft Customer [łącznika usługi Azure Cost Management](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights).
- Usługa Power BI — przejście od [Microsoft Azure Consumption Insights](https://docs.microsoft.com/power-bi/service-connect-to-azure-consumption-insights) zawartości pakietu aplikacji usługi Azure Cost Management (umowy klienta), dostępne pod adresem [AppSource](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview).

## <a name="unsupported-apis"></a>Nieobsługiwana interfejsów API

Następujące interfejsy API umowy Enterprise nie są obsługiwane przez umowy klienta firmy Microsoft. Alternatywy dla nieobsługiwanego interfejsy API są opisane.

[Pobierz arkusz cen za subskrypcję](/rest/api/consumption/pricesheet/get/) nie jest obsługiwany. Wyświetlane są ceny dla zakresu subscriptionId. Zamiast tego można użyć **arkusz cen, rozliczeń profilu**. Cena usługi w zakresie subskrypcji jest taka sama jak cena w zakresie rozliczeń profilu. Użytkownika, wywołując interfejs API musi mieć wymagane uprawnienia.

**Cena arkusz — Pobierz przez okres rozliczeniowy** nie jest obsługiwany. Otrzymuje arkusza cen dla zakresu, identyfikator subskrypcji i okresu rozliczeniowego. Zamiast tego można użyć **arkusz cen, rozliczeń profilu**. Cena usługi w zakresie subskrypcji jest równa cen usługi w zakresie rozliczeń profilu. Użytkownika, wywołując interfejs API musi mieć wymagane uprawnienia.

[Arkusz cen dla klientów korporacyjnych rejestrację](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) nie jest obsługiwane. Ponadto [arkusza cen dla klientów korporacyjnych rejestrację w danym okresie rozliczeniowym](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) nie jest obsługiwane. Podaj zarówno stawkę w ramach każdego miernika danego rejestracji i okres rozliczeniowy. Zamiast tego można użyć **arkusz cen, rozliczeń profilu**. Arkusz cen w zakresie rejestracji jest arkusz cen dotyczących profil rozliczeniowy. Użytkownika, wywołując interfejs API musi mieć wymagane uprawnienia.

## <a name="see-also"></a>Zobacz także
- Dowiedz się więcej o [interfejsów API REST użycia platformy Azure](/rest/api/consumption/).
