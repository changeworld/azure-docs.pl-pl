---
title: Migracja z umową Enterprise Agreement do interfejsów API z umowy klienta firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci konsekwencje migracji Microsoft Enterprise Agreement (EA) z umową klienta firmy Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 283808c0bd3f5297011b25619d6f978c99d4dc32
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439230"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migracja z umową Enterprise Agreement do interfejsów API umowy klienta firmy Microsoft

Ten artykuł pomaga zrozumieć strukturę danych, interfejsu API i inne różnice integracji systemu między kontami Enterprise Agreement (EA) i umowy klienta firmy Microsoft (MCA). Usługa Azure Cost Management obsługuje interfejsy API dla obu typów kont. Przegląd [konfiguracji konta rozliczeniowego dla](../billing/billing-mca-setup-account.md) artykułu umowy klienta firmy Microsoft, przed kontynuowaniem.

Organizacje z istniejącym kontem EA należy przejrzeć ten artykuł, w połączeniu z konfiguracją konta MCA. Wcześniej odnawiania konta EA wymagane pewne minimalnym nakładzie pracy, można przenieść z rejestracji starej do nowej. Migrowanie do kont MCA wymaga jednak wykonania dodatkowych nakładów pracy. Jest dodatkowego nakładu pracy ze względu na zmiany w podstawowym systemie rozliczeń, które mają wpływ na wszystkie powiązane koszty interfejsy API i ofert usług.

## <a name="mca-apis-and-integration"></a>Interfejsy API MCA i integracja

Interfejsy API MCA i nową opcję integracji umożliwiają:

- Masz pełną dostępność interfejsu API za pośrednictwem natywnych interfejsów API platformy Azure.
- W ramach pojedynczego konta rozliczeniowego, należy skonfigurować wiele faktur.
- Dostęp do połączonych interfejsu API z użycia usług platformy Azure, użycie witryny Marketplace innych firm i zakupów w portalu Marketplace.
- Wyświetlanie kosztów dla rozliczeń profile (taka sama jak rejestracji) przy użyciu usługi Azure Cost Management.
- Dostęp do nowych interfejsów API wyświetlania kosztów, Otrzymuj powiadomienia, gdy koszty przekracza wstępnie zdefiniowanych progów i wyeksportować dane pierwotne automatycznie.

## <a name="migration-checklist"></a>Lista kontrolna migracji

Poniższe elementy pomocy przejście do MCA interfejsów API.

- Zapoznaj się z nowym [umowy klienta Microsoft konto rozliczeniowe](../billing/billing-mca-overview.md).
- Ustal, które interfejsy API, można użyć, a te, które są zastępowane w poniższej sekcji.
- Zapoznaj się z [interfejsów API REST usługi Azure Resource Manager](/rest/api/azure).
- Jeśli jeszcze nie używasz interfejsów API usługi Azure Resource Manager, [zarejestrować aplikację kliencką w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aktualizowanie kodu do [uwierzytelnianie usługi Azure AD](/rest/api/azure/#create-the-request).
- Aktualizowanie kodu do zastępowania wywołań interfejsu API EA z wywołaniami interfejsu API MCA.
- Błąd podczas obsługi do użycia nowe kody błędów aktualizacji.
- Przegląd dodatkowa integracja ofert, takich jak Cloudyn i usługi Power BI dla innych niezbędne działania.

## <a name="ea-apis-replaced-with-mca-apis"></a>Interfejsy API EA zastąpione MCA interfejsów API

Interfejsy API EA używają klucza interfejsu API, uwierzytelniania i autoryzacji. Interfejsy API MCA używają uwierzytelniania usługi Azure AD.

| Przeznaczenie | INTERFEJS API EA | INTERFEJS API MCA |
| --- | --- | --- |
| Informacji o saldach i środki na korzystanie z | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Użycie (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Sposób użycia (CSV) | [/ usagedetails/pobierania](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) [ /usagedetails/Prześlij](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Użycie witryny Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Okresy fakturowania | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Arkusz cen | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format = json|CSV Microsoft.Billing/billingAccounts/.../billingProfiles/.../invoices/... /pricesheet/default/download format=json|CSV Microsoft.Billing/billingAccounts/... / billingProfiles /... /Providers/Microsoft.consumption/pricesheets/Download  |
| Zakup rezerwacji | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Zalecenia dotyczące rezerwacji | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Użycie rezerwacji | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> usługi platformy azure i użycia witryny Marketplace innych firm są dostępne z [interfejs API użycia szczegóły](/rest/api/consumption/usagedetails).

Następujące interfejsy API są dostępne dla konta rozliczeniowego MCA:

| Przeznaczenie | Interfejs API (MCA) umowy klienta firmy Microsoft |
| --- | --- |
| Rozliczanie konta<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Profile rozliczeń<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Faktury sekcje<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faktury | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Rozliczeń subskrypcji | {zakresu} / billingSubscriptions |

<sup>2</sup> interfejs API zwraca listę obiektów, które są zakresy, gdzie Cost Management napotka w witrynie Azure portal i interfejsy API obsługi. Aby uzyskać więcej informacji na temat zakresów Cost Management, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).

Jeśli używasz żadnych istniejących interfejsów API EA, należy je zaktualizować w celu obsługi konta rozliczeniowego MCA. W poniższej tabeli przedstawiono inne zmiany integracji:

| Przeznaczenie | Stary oferty | Nowa oferta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) zawartości pakietu i programu connector | [Aplikacja Microsoft Azure zużycie Insights Power BI](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) i [ łącznika Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Pobieranie informacji o saldach i środki na korzystanie z interfejsów API

[Pobierz podsumowania salda](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) interfejs API udostępnia miesięczne podsumowania:

- Salda
- Nowe zakupy
- Opłaty za usługi platformy Azure Marketplace
- Korekty
- Opłata za nadwyżkę usługi

Wszystkie interfejsy API zużycia są zastępowane przez natywnych interfejsów API platformy Azure, używanego przez usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie z użyciem usług REST](/rest/api/azure/#create-the-request).

Interfejs API Pobierz podsumowanie salda zastępuje Microsoft.Billing/billingAccounts/billingProfiles/availableBalance interfejsu API.

Aby wyświetlić dostępne sald z dostępnych interfejsów API saldo:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Interfejsy API, aby uzyskać kosztów i użycia

Uzyskiwanie codzienne podział kosztów użycia usług platformy Azure firm portalu Marketplace i użycie innych zakupów w portalu Marketplace przy użyciu następujących interfejsów API. Następujące interfejsy API oddzielne zostały scalone usług platformy Azure i użycia witryny Marketplace innych firm. Stary interfejsy API są zastępowane przez [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) interfejsu API. Dodaje zakupów w portalu Marketplace, które zostały wcześniej wyświetlane tylko w saldo podsumowania do daty.

- [Pobieranie szczegółów użycia/pobierania](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Pobieranie szczegółów/przedstawia sposób użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Pobieranie szczegółów użycia/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Pobieranie szczegółów użycia/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Pobierz portalu marketplace, opłata za magazyn/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Pobierz portalu marketplace, opłata za magazyn/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Wszystkie interfejsy API zużycia są zastępowane przez natywnych interfejsów API platformy Azure, używanego przez usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie z użyciem usług REST](/rest/api/azure/#create-the-request).

Wszystkie interfejsy API poprzedniego są zastępowane przez interfejs API szczegóły zużycie/użycia.

Aby uzyskać szczegóły użycia za pomocą interfejsu API szczegółów użycia:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Interfejs API szczegóły użycia, podobnie jak w przypadku wszystkich interfejsów API zarządzania kosztami, jest dostępna w wielu zakresach. Invoiced kosztów rozliczeń zakresu profilu używać jako tradycyjnie otrzyma na poziomie rejestracji.  Aby uzyskać więcej informacji na temat zakresów Cost Management, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).

| Type | Format Identyfikatora |
| --- | --- |
| Konto billingowe | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Profil rozliczeniowy | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subskrypcja | `/subscriptions/{subscriptionId}` |
| Grupa zasobów | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Następujące parametry querystring umożliwia aktualizację wszelkiego kodu programowania.

| Stary parametrów | Nowe parametry |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nieobsługiwane |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Treść odpowiedzi jest również zostały zmienione.

Stary treści odpowiedzi:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nowych treści odpowiedzi:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Nazwa właściwości zawierający tablicę rekordów użycia zmieniła się z danych _wartości_. Każdy rekord mają płaską listę szczegółowych właściwości. Jednak każdy rekord teraz wszystkie szczegółowe informacje znajdują się teraz w zagnieżdżonych właściwości o nazwie _właściwości_, z wyjątkiem znaczników. Nowa struktura jest spójna z innymi interfejsami API platformy Azure. Zmieniono nazwy niektórych właściwości. W poniższej tabeli przedstawiono odpowiednich właściwości.

| Stary właściwości | Nowa właściwość | Uwagi |
| --- | --- | --- |
| Identyfikator konta | ND | Procedura tworzenia subskrypcji nie jest śledzone. Użyj invoiceSectionId (tak jak departmentId). |
| AccountNameAccountOwnerId i AccountOwnerEmail | ND | Procedura tworzenia subskrypcji nie jest śledzone. Użyj invoiceSectionName (tak jak departmentName). |
| Informacje dodatkowe | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Należy pamiętać, że te właściwości są przeciwieństwem. Obowiązuje isAzureCreditEnabled ChargesBilledSeparately będzie mieć wartość false. |
| Użyta ilość | quantity | &nbsp; |
| Użyta usługa | consumedService | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| Identyfikator użytej usługi | Brak | &nbsp; |
| Centrum kosztów | costCenter | &nbsp; |
| Data i usageStartDate | date | &nbsp;  |
| Dzień | Brak | Analizuje dni od daty. |
| Identyfikator działu | invoiceSectionId | Dokładne wartości są różne. |
| Nazwa działu | invoiceSectionName | Dokładnie taki ciąg znaków wartości mogą się różnić. Skonfiguruj faktury sekcji, aby dopasować działów, jeśli to konieczne. |
| ExtendedCost i kosztów | costInBillingCurrency | &nbsp;  |
| Identyfikator wystąpienia | resourceId | &nbsp;  |
| Opłata jest cykliczna | Brak | &nbsp;  |
| Lokalizacja | location | &nbsp;  |
| Kategoria miernika | meterCategory | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| Identyfikator miernika | meterId | Dokładnie taki ciąg znaków wartości są różne. |
| Nazwa miernika | lmeterName | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| Region miernika | meterRegion | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| Podkategoria miernika | meterSubCategory | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| Miesiąc | Brak | Analizuje miesiąc od daty. |
| Nazwa oferty | Brak | Użyj argumenty publisherName i productOrderName. |
| OfferId | Brak | &nbsp;  |
| Numer zamówienia | Brak | &nbsp;  |
| PartNumber | Brak | Użyj meterId i productOrderName do unikatowego identyfikowania ceny. |
| Nazwa planu | productOrderName | &nbsp;  |
| Product (Produkt) | Product (Produkt) |   |
| Identyfikator produktu | productId | Dokładnie taki ciąg znaków wartości są różne. |
| Nazwa wydawcy | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| GuidZasobu | meterId | Dokładnie taki ciąg znaków wartości są różne. |
| Lokalizacja zasobu | resourceLocation | &nbsp;  |
| Identyfikator lokalizacji zasobu | Brak | &nbsp;  |
| Współczynnik zasobów | effectivePrice | &nbsp;  |
| Identyfikator administratora usługi | ND | &nbsp;  |
| Informacje o usłudze 1 | serviceInfo1 | &nbsp;  |
| Informacje o usłudze 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| ServiceTier | meterSubCategory | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| Identyfikator usługi magazynu | ND | &nbsp;  |
| Identyfikator GUID subskrypcji | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Nazwa subskrypcji | subscriptionName | &nbsp;  |
| Tagi | tags | Właściwość tags ma zastosowanie do obiektu głównego, aby nie właściwość zagnieżdżonych właściwości. |
| Jednostka miary | unitOfMeasure | Dokładnie taki ciąg znaków wartości są różne. |
| usageEndDate | date | &nbsp;  |
| Rok | Brak | Analizuje roku od daty. |
| (nowe)  | billingCurrency | Waluta używana opłaty. |
| (nowe)  | billingProfileId | Unikatowy identyfikator profil rozliczeniowy, (tak jak rejestracji). |
| (nowe)  | billingProfileName | Nazwa profilu rozliczeń, (tak jak rejestracji). |
| (nowe)  | chargeType | Służy do rozróżnienia użycia usługi Azure Marketplace i użycie zakupów. |
| (nowe)  | invoiceId | Unikatowy identyfikator dla faktury. Pusta dla miesiąca bieżącej, Otwórz. |
| (nowe)  | publisherType | Typ wydawcy w celu dokonywania zakupów. Pusta do użycia. |
| (nowe)  | serviceFamily | Typ zakupu. Pusta do użycia. |
| (nowe)  | servicePeriodEndDate | Data zakończenia zakupionej usługi. |
| (nowe)  | servicePeriodStartDate | Data rozpoczęcia dla usług zakupionych. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Interfejs API okresy zastępuje faktur API rozliczeń

Nie należy używać konta rozliczeniowego MCA okresów rozliczeniowych. Zamiast tego należy użyć faktur w zakresie kosztów określonych okresów rozliczeniowych. [Interfejs API rozliczeń okresy](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) jest zastępowany przez interfejs API faktur. Wszystkie interfejsy API zużycia są zastępowane przez natywnych interfejsów API platformy Azure, używanego przez usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie z użyciem usług REST](/rest/api/azure/#create-the-request).

Aby uzyskać faktur za pomocą interfejsu API faktury:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Interfejsy API arkusza cen

W tej sekcji omówiono istniejących interfejsów API arkusza cen i zalecenia, aby przejść do interfejsu API arkusza cen dla umowy klienta firmy Microsoft. Również w tym artykule omówiono interfejsu API arkusza cen dla umowy klienta firmy Microsoft i wyjaśniono pola w arkuszach cen. [Enterprise Pobierz arkusz cen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) i [przedsiębiorstwa uzyskiwanie okresów rozliczeniowych](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) interfejsy API są zastępowane przez interfejs API arkusza cen dla umowy klienta firmy Microsoft (Microsoft.Billing/billingAccounts/billingProfiles / arkusza cen). Nowy interfejs API obsługuje CSV i JSON, formatuje w asynchronicznej formatach REST. Wszystkie interfejsy API zużycia są zastępowane przez natywnych interfejsów API platformy Azure, używanego przez usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie z użyciem usług REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Interfejsy API przedsiębiorstwa rozliczeń

Użyto interfejsów API rozliczeń przedsiębiorstwa z rejestracji Enterprise można pobrać cen i rozliczeń informacji okresie. Uwierzytelnianie i autoryzacja używać tokenów sieci web usługi Azure Active Directory.

Aby uzyskać odpowiednie ceny dla określonego rejestracji Enterprise przy użyciu arkusza cen i interfejsów API rozliczeń okres:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API arkusza cen dla klientów firmy Microsoft

Użyj interfejsu API arkusza cen dla umowy klienta firmy Microsoft, aby wyświetlić ceny dla wszystkich usług zużycie użycia platformy Azure i Portal Marketplace. Podane dla profil rozliczeniowy ceny dotyczą wszystkich subskrypcji, które należą do profil rozliczeniowy.

Aby wyświetlić wszystkie dane arkusza cen usługi Azure Consumption w formacie CSV, należy użyć interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Wyświetlać wszystkie dane arkusza cen usługi Azure Consumption w formacie JSON za pomocą interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Korzystanie z interfejsu API zwraca arkusza cen dla całego konta. Jednakże możesz także uzyskać skrócona wersja arkusza cen w formacie PDF. Podsumowanie zawiera użycia platformy Azure i Portal Marketplace usługi użycia, które są rozliczane dla określonej faktury. Faktura jest identyfikowane za pomocą {identyfikatora faktury}, która jest taka sama jak **płatnika** objętego plików PDF Podsumowanie faktury. Oto przykład.

![Przykładowy obraz przedstawiający numer faktury, która odnosi się do identyfikatora faktury](./media/migrate-cost-management-api/invoicesummary.png)

Aby wyświetlić informacje o fakturze za pomocą interfejsu API arkusza cen w formacie CSV:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Aby wyświetlić informacje o fakturze za pomocą interfejsu API arkusza cen w formacie JSON:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Widać również szacowane ceny dla dowolnej usługi zużycie użycia platformy Azure lub portalu Marketplace w bieżącym okresie rozliczeniowym Otwórz lub czasu usługi.

Aby wyświetlić szacowany ceny za użycie usług za pomocą interfejsu API arkusza cen w formacie CSV:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Aby wyświetlić szacowany ceny za użycie usług za pomocą interfejsu API arkusza cen w formacie JSON:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Interfejsy API arkusza cen umowy klienta firmy Microsoft są *asynchronicznych interfejsów API REST*. Odpowiedzi dla interfejsów API zmieniła się z starszych synchronicznych interfejsów API. Treść odpowiedzi interfejsu API jest również zostały zmienione.

#### <a name="old-response-body"></a>Stary treść odpowiedzi

Oto przykład synchronicznej odpowiedzi interfejsu API REST:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Nowe treść odpowiedzi

Interfejsy API obsługują [Azure REST asynchroniczne](../azure-resource-manager/resource-manager-async-operations.md) formatu. Wywoływanie interfejsu API przy użyciu GET i otrzymywać następującą odpowiedź:

```
No Response Body

HTTP Status 202 Accepted
```

Następujące nagłówki są wysyłane z lokalizacją danych wyjściowych:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Wprowadź inny GET wywołania do lokalizacji. Odpowiedzi na wywołanie GET jest taka sama, dopóki nie osiągnie stan ukończenia lub niepowodzenia operacji. Po zakończeniu odpowiedzi do lokalizacji wywołanie GET zwraca adres URL pobierania. Tak jak w przypadku operacji zostało wykonane w tym samym czasie. Oto przykład:

```
HTTP Status 200

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

Klient może być wywołanie GET `Azure-AsyncOperation`. Punkt końcowy zwraca stan operacji.

W poniższej tabeli przedstawiono pola w starszych API arkusza przedsiębiorstwa uzyskiwanie ceny. Obejmuje odpowiednich pól w nowy arkusz cen dla umowy klienta firmy Microsoft:

| Stary właściwości | Nowa właściwość | Uwagi |
| --- | --- | --- |
| billingPeriodId  | _Nie dotyczy_ | Nie dotyczy. W przypadku umowy klienta firmy Microsoft, faktury i arkusza cen skojarzone zastąpić pojęcie billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Dokładnie taki ciąg znaków wartości mogą się różnić. |
| includedQuantity  | includedQuantity | Nie dotyczy usługi w ramach umowy klienta firmy Microsoft. |
| PartNumber  | _Nie dotyczy_ | Zamiast tego należy użyć kombinacji productOrderName (tak jak identyfikatora oferty) i meterid. |
| Cena jednostkowa  | Cena jednostkowa | Cena ma zastosowanie do usług używane w umowach klientów firmy Microsoft. |
| currencyCode  | pricingCurrency | Umowy klienta firmy Microsoft mają reprezentacji ceny w walucie cen i waluta rozliczeniowa. CurrencyCode odpowiada pricingCurrency w umowy klienta firmy Microsoft. |
| offerId | productOrderName | Zamiast identyfikatora oferty można użyć productOrderName, ale nie jest taka sama jak identyfikatora oferty. Jednak productOrderName i licznik stwierdzić, że ceny w umowy klienta firmy Microsoft związane z meterId i identyfikatora oferty w starszej wersji rejestracji. |

## <a name="consumption-price-sheet-api-operations"></a>Operacje interfejsu API arkusza cen zużycie

Dla umów Enterprise Agreement użyto interfejsu API arkusza cen zużycie [uzyskać](/rest/api/consumption/pricesheet/get) i [uzyskać przez okres rozliczeniowy](/rest/api/consumption/pricesheet/getbybillingperiod) operacje dotyczące zakresu, identyfikator subskrypcji lub okresu rozliczeniowego. Interfejs API korzysta z uwierzytelniania usługi Azure Resource Management.

Aby uzyskać informacje arkusza cen dla zakresu przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Aby uzyskać informacje arkusz cen, rozliczeń okres przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Zamiast wymienionych wyżej punktów końcowych interfejsu API użyć poniższych zapytań dla umowy klienta firmy Microsoft:

**Interfejs API z arkusza cen dla umowy klienta firmy Microsoft (asynchronicznego interfejsu API REST)**

Ten interfejs API jest dla umowy klienta firmy Microsoft i zapewnia dodatkowe atrybuty.

**Arkusz cen dla danego zakresu profilu rozliczeń w konta rozliczeniowego**

Ten interfejs API jest istniejącym interfejsem API. Zaktualizowano się zapewnienie arkusz cen profil rozliczeniowy, w ramach konta rozliczeniowego.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Arkusz cen dla zakresu przez konto rozliczeniowe

Uwierzytelnianie usługi Azure Resource Manager jest używany, gdy otrzymasz arkusz cen w zakresie rejestracji w ramach konta rozliczeniowego.

Aby uzyskać arkusz cen na koncie rejestracji w ramach konta rozliczeniowego:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Dla umowy klienta firmy Microsoft skorzystaj z informacji w poniższej sekcji. Zapewnia właściwości pola używane dla umów Microsoft Customer.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Arkusz cen, rozliczeń zakresu profilu w konto rozliczeniowe

Zaktualizowany arkusz cen za konto rozliczeniowe interfejsu API pobiera arkusz cen w formacie CSV. Aby uzyskać arkusz cen w zakresie rozliczeń profilu dla MCA:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

W zakresie rejestracji EA odpowiedzi interfejsu API i właściwości są identyczne. Właściwości odpowiadają te same właściwości MCA.

Starsze właściwości [interfejsów API usługi Azure Resource Manager cena arkusza](/rest/api/consumption/pricesheet) i te same właściwości nowego znajdują się w poniższej tabeli.

| Stary właściwość API arkusza cen zasobów platformy Azure Menedżera  | Właściwości nowego interfejsu API arkusza cen umowy klienta Microsoft   | Opis |
| --- | --- | --- |
| Identyfikator miernika | _meterId_ | Unikatowy identyfikator miernika. Taka sama jak meterId. |
| Nazwa miernika | lmeterName | Nazwa licznika. Licznik reprezentuje zasób do wdrożenia usługi platformy Azure. |
| Kategoria miernika  | usługa | Nazwa kategorii klasyfikacji dla licznika. Taka sama jak usługi w arkuszu cen umowy klienta firmy Microsoft. Dokładnie taki ciąg znaków wartości są różne. |
| Podkategoria miernika | meterSubCategory | Nazwa kategorii podrzędna miernika. Na podstawie klasyfikacji zróżnicowania zestaw funkcji wysokiego poziomu usługi. Na przykład podstawowa baza danych SQL w stosunku do standardowych bazy danych SQL |
| Region miernika | meterRegion | &nbsp;  |
| Jednostka | _Nie dotyczy_ | Może być pochodzącą z analizy unitOfMeasure. |
| Jednostka miary | unitOfMeasure | &nbsp;  |
| Numer części | _Nie dotyczy_ | Zamiast partNumber należy użyć productOrderName i MeterId do unikatowego identyfikowania cena na profil rozliczeniowy. Pola są wyświetlane na fakturze MCA zamiast partNumber MCA faktur. |
| Cena jednostkowa | Cena jednostkowa | Cena jednostkowa umowy klienta firmy Microsoft. |
| Kod waluty | pricingCurrency | Umowy klienta firmy Microsoft stanowią ceny w cenie waluty i waluta rozliczeniowa. Kod waluty jest taka sama jak pricingCurrency w umowy klienta firmy Microsoft. |
| Uwzględnione | includedQuantity | Nie dotyczy usług w umowy klienta firmy Microsoft. Pokaż o wartości zero. |
|  Identyfikator oferty  | productOrderName | Zamiast identyfikatora oferty należy użyć productOrderName. Nie taka sama, jak identyfikatora oferty, jednak productOrderName i licznik określają, cen w umowy klienta firmy Microsoft. Powiązane meterId i identyfikatora oferty starszych rejestracji. |

Cena dla umowy klienta firmy Microsoft jest zdefiniowany inaczej niż umowy Enterprise Agreement. Cena usługi w ramach rejestracji Enterprise jest unikatowy dla produktu, PartNumber, miernika i oferty. PartNumber nie jest używany w umowy klienta firmy Microsoft.

Cena usługi Azure Consumption będącej częścią umowy klienta firmy Microsoft jest unikatowy dla productOrderName i meterId. Reprezentują one mierników usługi i plan produktu.

Aby uzgodnić między arkusz cen i użycia w interfejsie API szczegóły użycia, można użyć productOrderName i meterId.

Użytkownicy, którzy mają rozliczeń profilu Właściciel, współautor, Czytelnik i faktury praw manager można pobrać arkusz cen.

Arkusz cen zawiera ceny za usługi, których cena zależy od użycia. Usługi obejmują użycie platformy Azure i użycie witryny Marketplace. Najnowszymi cenami na końcu każdego okresu usługi jest zablokowana i jest stosowane do użycia w okresie jednej usługi. Dla usług Azure zużycie czasu usługi jest zwykle w danym miesiącu kalendarzowym.

### <a name="retired-price-sheet-api-fields"></a>Wycofane pola interfejsu API arkusza cen

Następujące pola nie są dostępne w interfejsów API arkusza cen umowy klienta firmy Microsoft lub mają te same pola.

|Wycofane pola| Opis|
|---|---|
| billingPeriodId | Nie dotyczy. MCA odnosi się do identyfikatora faktury. |
| offerId | Nie dotyczy. Odnosi się do productOrderName w MCA. |
| meterCategory  | Nie dotyczy. Odnosi się do usługi w MCA. |
| jednostka | Nie dotyczy. Może być pochodzącą z analizy unitOfMeasure. |
| currencyCode | Taka sama jak pricingCurrency w MCA. |
| meterLocation | Taka sama jak meterRegion w MCA. |
| partNumber partnumber | Nie dotyczy, ponieważ nie ma na liście numer części MCA faktur. Zamiast partnumber należy użyć kombinacji meterId i productOrderName do unikatowego identyfikowania ceny. |
| totalIncludedQuantity | Nie dotyczy. |
| pretaxStandardRate  | Nie dotyczy. |

## <a name="reservation-instance-charge-api-replaced"></a>Rezerwacja opłaty interfejsu API Instance zastąpione

Można uzyskać rozliczeń transakcji dla zakupu rezerwacji z [interfejsu API opłata za zastrzeżone Instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Nowy interfejs API obejmuje wszystkie zakupy, w tym ofert z portalu Marketplace innych firm. Wszystkie interfejsy API zużycia są zastępowane przez natywnych interfejsów API platformy Azure, używanego przez usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie z użyciem usług REST](/rest/api/azure/#create-the-request). Zastrzeżone interfejsu API opłaty Instance zastępuje transakcji interfejsu API.

Aby uzyskać rezerwacji transakcji zakupu za pomocą interfejsu API transakcji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Zalecenia dotyczące interfejsów API zastąpione

Zalecenia zakupu zarezerwowane wystąpienia interfejsów API zapewniają wykorzystania maszyny wirtualnej w ciągu ostatnich 7, 30 lub 60 dni. Interfejsy API zapewniają również zalecenia zakupu rezerwacji. Obejmują one:

- [Udostępniony interfejs API zaleceń wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Interfejs API zaleceń pojedyncze wystąpienie zarezerwowane](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Wszystkie interfejsy API zużycia są zastępowane przez natywnych interfejsów API platformy Azure, używanego przez usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie z użyciem usług REST](/rest/api/azure/#create-the-request). Zalecenia dotyczące rezerwacji, interfejsy API wymienionymi wcześniej są zastępowane przez [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) interfejsu API.

Aby uzyskać zalecenia rezerwacji z interfejsu API zaleceń rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Interfejsy API użycia rezerwacji zastąpione

Użycie rezerwacji można rozpocząć w rejestracji z zarezerwowanych interfejs API użycia wystąpienia. Jeśli istnieje więcej niż jednego zastrzeżonego wystąpienia w rejestracji, możesz także uzyskać użycia wszystkich wystąpień zarezerwowanych zakupów, za pomocą tego interfejsu API.

Obejmują one:

- [Szczegóły użycia wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Podsumowanie użycia wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary)

Wszystkie interfejsy API zużycia są zastępowane przez natywnych interfejsów API platformy Azure, używanego przez usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie z użyciem usług REST](/rest/api/azure/#create-the-request). Zalecenia dotyczące rezerwacji, interfejsy API wymienionymi wcześniej są zastępowane przez [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) i [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) interfejsów API.

Aby wyświetlić szczegóły rezerwacji z interfejsem API szczegóły rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Aby uzyskać rezerwacji podsumowania tabel za pomocą interfejsu API podsumowań z okresu rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Przenoszenie z platformy Cloudyn do usługi Cost Management

Organizacje za pomocą [Cloudyn](https://cloudyn.com) powinien rozpocząć korzystanie z [usługi Azure Cost Management](https://azure.microsoft.com/services/cost-management/) dla potrzeb związanych z zarządzaniem żadnych kosztów. Usługa Cost Management jest dostępna w witrynie Azure portal z nie dołączania i opóźnieniami ośmiu godzin. Aby uzyskać więcej informacji, zobacz [dokumentacji Cost Management](index.yml).

Za pomocą usługi Azure Cost Management możesz wykonywać następujące czynności:

- Wyświetlanie kosztów wraz z upływem czasu, względem wstępnie zdefiniowanych budżetu. Analizowanie codziennie wzorców kosztów do identyfikowania i Zatrzymaj wydatków anomalii. Rozbicie kosztów według tagów, grupy zasobów, usług i lokalizacji.
- Tworzenie budżetów ustawieniu limitów użycia i kosztów i Otrzymuj powiadomienie, gdy progi ważne są w stanie. Konfigurowanie usługi automation przy użyciu grup akcji do wyzwalacza zdarzenia niestandardowe i wymuszanie limitów twarde zgodnie z Twoimi wymaganiami.
- Optymalizowanie kosztów i użycia wraz z zaleceniami z usługi Azure Advisor. Odnajdowanie optymalizacje zakupu za pomocą rezerwacji, downsize rozłączania maszyn wirtualnych i usunąć nieużywane zasoby w celu pozostania w budżet.
- Planowanie kosztów i użycia eksportu danych można opublikować pliku CSV do swojego konta magazynu codziennie. Zautomatyzuj Integracja z systemami zewnętrznymi, aby zachować danych dotyczących rozliczeń, synchronizację i bądź na bieżąco.

## <a name="power-bi-integration"></a>Integracja z usługą Power BI

Jeśli używasz usługi Power BI koszt raportowania, należy przejść do następujących:

- Microsoft Azure przez wgląd aplikacja Power BI
- Łącznik Azure Consumption Insights pulpitu


Łącznik jest zalecane w przypadku organizacji, którzy chcą mieć największą elastyczność. Jednak aplikacja Power BI jest również dostępne dla konfiguracji szybkie.

- Zainstaluj [platformy Microsoft Azure Consumption Insights usługi Power BI aplikacji](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Połącz się z łącznikiem Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)

Starsze Consumption Insights pakiet zawartości i łącznika pracy na poziomie rejestracji. Wymaga ona przynajmniej uprawnień odczytu. Nowa aplikacja użycia szczegółowych informacji w usłudze Power BI i nowy łącznik Azure Consumption Insights są dostępne dla rozliczeń profilów użytkowników. Powinno się używać w zespołach, które potrzebują dodatkowych opcji przeglądania kosztów lub aby przejrzeć koszty dla rozliczeń profile [analiza kosztów](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) witryny Azure portal.

## <a name="next-steps"></a>Kolejne kroki

- Odczyt [dokumentacji Cost Management](index.yml) dowiesz się, jak monitorować i kontrolować wydatków platformy Azure. Lub, jeśli chcesz optymalizować wykorzystanie zasobów w usłudze Cost Management.
