---
title: Migrowanie z Umowa Enterprise do interfejsów API umów klienta firmy Microsoft — Azure | Microsoft Docs
description: Ten artykuł pomaga zrozumieć konsekwencje migracji programu Microsoft Umowa Enterprise (EA) do umowy klienta firmy Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 20d83c48fb4ad60b091dc87b224a053690251a48
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481716"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrowanie z Umowa Enterprise do interfejsów API umów klienta firmy Microsoft

Ten artykuł pomaga zrozumieć strukturę danych, interfejs API i inne różnice w zakresie integracji systemu między kontami Umowa Enterprise (EA) i Microsoft Customer Agreement (MCA). Azure Cost Management obsługuje interfejsy API dla obu typów kont. Przed kontynuowaniem zapoznaj się z artykułem [dotyczącym konfiguracji konta rozliczeń dla](../billing/mca-setup-account.md) klientów firmy Microsoft.

Organizacje z istniejącym kontem EA powinni zapoznać się z tym artykułem w połączeniu z konfigurowaniem konta MCA. Wcześniej odnowienie konta EA wymagało pewnej minimalnej ilości pracy do przeniesienia ze starej rejestracji do nowej. Jednak migracja do konta MCA wymaga dodatkowego nakładu pracy. Dodatkowe nakłady pracy wynikają z zmian w podstawowym podsystemie rozliczeniowym, co wpływa na wszystkie interfejsy API związane z kosztami i oferty usług.

## <a name="mca-apis-and-integration"></a>Interfejsy API i integracja MCA

Interfejsy API MCA i Nowa integracja umożliwiają:

- Pełna dostępność interfejsu API za pomocą natywnych interfejsów API platformy Azure.
- Skonfiguruj wiele faktur w pojedynczym koncie rozliczania.
- Uzyskaj dostęp do połączonego interfejsu API za pomocą usługi platformy Azure, użycia witryny Marketplace i zakupów w portalu Marketplace.
- Wyświetlanie kosztów w profilach rozliczeń (takich jak rejestracje) przy użyciu Azure Cost Management.
- Uzyskaj dostęp do nowych interfejsów API, aby wyświetlić koszty, otrzymywać powiadomienia o kosztach przekroczenia wstępnie zdefiniowanych progów i automatycznie eksportować pierwotne dane.

## <a name="migration-checklist"></a>Lista kontrolna migracji

Poniższe elementy ułatwiają przejście do interfejsów API MCA.

- Zapoznaj się z nowym [kontem rozliczeń umowy klienta firmy Microsoft](../billing/billing-mca-overview.md).
- Określ, które interfejsy API są używane, i sprawdź, które z nich zostały zastąpione w następnej sekcji.
- Zapoznaj się z [Azure Resource Manager interfejsów API REST](/rest/api/azure).
- Jeśli nie korzystasz już z Azure Resource Manager interfejsów API, [zarejestruj swoją aplikację kliencką w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Zaktualizuj kod programistyczny, aby [korzystać z uwierzytelniania usługi Azure AD](/rest/api/azure/#create-the-request).
- Zaktualizuj kod programistyczny, aby zastąpić wywołania interfejsu API EA z wywołaniami interfejsu API MCA.
- Aktualizacja obsługi błędów w celu używania nowych kodów błędów.
- Zapoznaj się z dodatkowymi ofertami integracji, takimi jak Cloudyn i Power BI, na potrzeby innych wymaganych działań.

## <a name="ea-apis-replaced-with-mca-apis"></a>Interfejsy API EA zostały zamienione na interfejsy API MCA

Interfejsy API EA używają klucza interfejsu API do uwierzytelniania i autoryzacji. Interfejsy API MCA używają uwierzytelniania usługi Azure AD.

| Przeznaczenie | INTERFEJS API EA | INTERFEJS API MCA |
| --- | --- | --- |
| Saldo i kredyty | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft. rozliczenia/billingAccounts/billingProfiles/availableBalanceussae |
| Użycie (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft. zużycie/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Użycie (CSV) | [/UsageDetails/Download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/UsageDetails/Submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft. zużycie/usageDetails/pobieranie](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Użycie witryny Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft. zużycie/usageDetails/pobieranie](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Okresy rozliczeniowe | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft. rozliczenia/billingAccounts/billingProfiles/faktury |
| Arkusz cen | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. rozliczenia/billingAccounts/billingProfiles/arkusza cen/domyślny/pobieranie format = JSON|CSV Microsoft. rozliczenia/billingAccounts/.../billingProfiles/.../faktury/... Format/pricesheet/default/Download = JSON|CSV Microsoft. rozliczenia/billingAccounts/... /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Zakupy rezerwacji | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft. rozliczenia/billingAccounts/billingProfiles/Transactions |
| Zalecenia dotyczące rezerwacji | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft. zużycie/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Użycie rezerwacji | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft. zużycie/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft. zużycie/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> usługa platformy Azure i użycie witryny Marketplace innych firm są dostępne w [interfejsie API szczegóły użycia](/rest/api/consumption/usagedetails).

Następujące interfejsy API są dostępne dla kont rozliczania MCA:

| Przeznaczenie | Microsoft Customer Agreement (MCA) — interfejs API |
| --- | --- |
| Konta rozliczeniowe<sup>2</sup> | Microsoft. rozliczenia/billingAccounts |
| Profile rozliczeń<sup>2</sup> | Microsoft. rozliczenia/billingAccounts/billingProfiles |
| Sekcja faktury<sup>2</sup> | Microsoft. rozliczenia/billingAccounts/invoiceSections |
| Faktury | Microsoft. rozliczenia/billingAccounts/billingProfiles/faktury |
| Subskrypcje rozliczeń | {Scope}/billingSubscriptions |

<sup>2</sup> interfejsy API zwracają listy obiektów, które są zakresami, w których działają Cost Management środowiska Azure Portal i interfejsów API. Aby uzyskać więcej informacji na temat zakresów Cost Management, zobacz [Opis zakresów i pracuj z](understand-work-scopes.md)nich.

W przypadku używania jakichkolwiek istniejących interfejsów API EA należy je zaktualizować do obsługi kont rozliczeń MCA. W poniższej tabeli przedstawiono inne zmiany integracji:

| Przeznaczenie | Stara oferta | Nowa oferta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Pakiet zawartości usługi [Microsoft Insights](/power-bi/desktop-connect-azure-consumption-insights) i łącznik | [Microsoft Azure Consumption Insights Power BI aplikacji](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) i [łącznika Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Interfejsy API do uzyskiwania salda i kredytów

Interfejs API [podsumowania salda Get](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) zawiera miesięczne podsumowanie dotyczące:

- Salda
- Nowe zakupy
- Opłaty za usługę Azure Marketplace
- Korekty
- Opłaty za użycie nadwyżkowe usługi

Wszystkie interfejsy API użycia są zastępowane przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie do usługi REST](/rest/api/azure/#create-the-request).

Interfejs API podsumowania salda get jest zastępowany przez interfejs API Microsoft. rozliczeń/billingAccounts/billingProfiles/availableBalance.

Aby uzyskać dostęp do dostępnych sald z interfejsem API dostępnego salda:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Interfejsy API do uzyskiwania kosztów i użycia

Uzyskaj dzienny podział kosztów z używania usługi platformy Azure, użycie witryny Marketplace innej firmy i inne zakupy w witrynie Marketplace przy użyciu następujących interfejsów API. Następujące oddzielne interfejsy API zostały scalone dla usług platformy Azure i użycia witryny Marketplace innej firmy. Stare interfejsy API są zastępowane przez interfejs API [Microsoft. zużycie/usageDetails](/rest/api/consumption/usagedetails) . Powoduje to dodanie zakupów w portalu Marketplace, które wcześniej były wyświetlane tylko w podsumowaniu bilansu do daty.

- [Pobierz szczegóły użycia/Pobierz](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Pobierz szczegóły użycia/Prześlij](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Pobierz szczegóły użycia/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Pobierz szczegóły użycia/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Pobierz koszt sklepu Marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Pobierz koszt sklepu Marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Wszystkie interfejsy API użycia są zastępowane przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie do usługi REST](/rest/api/azure/#create-the-request).

Wszystkie powyższe interfejsy API są zastępowane przez interfejs API użycie/szczegóły użycia.

Aby uzyskać szczegółowe informacje dotyczące użycia interfejsu API szczegółów użycia:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Interfejs API szczegółów użycia, podobnie jak w przypadku wszystkich interfejsów API Cost Management, jest dostępny w wielu zakresach. W przypadku kosztów zafakturowanych, jak tradycyjnie otrzymywać na poziomie rejestracji, użyj zakresu profilu rozliczania.  Aby uzyskać więcej informacji na temat zakresów Cost Management, zobacz [Opis zakresów i pracuj z](understand-work-scopes.md)nich.

| Typ | Format identyfikatora |
| --- | --- |
| Konto billingowe | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Profil rozliczeniowy | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subskrypcja | `/subscriptions/{subscriptionId}` |
| Grupa zasobów | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Użyj następujących parametrów QueryString, aby zaktualizować dowolny kod programistyczny.

| Stare parametry | Nowe parametry |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nieobsługiwane |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Treść odpowiedzi również uległa zmianie.

Stara treść odpowiedzi:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nowa treść odpowiedzi:

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

Nazwa właściwości zawierającej tablicę rekordów użycia została zmieniona z danych na _wartość_. Każdy rekord używany do posiadania płaskiej listy szczegółowych właściwości. Jednak każdy rekord teraz wszystkie szczegóły znajdują się teraz w zagnieżdżonej właściwości o nazwie _Properties_, z wyjątkiem tagów. Nowa struktura jest spójna z innymi interfejsami API platformy Azure. Niektóre nazwy właściwości zostały zmienione. W poniższej tabeli przedstawiono odpowiednie właściwości.

| Stara Właściwość | Nowa właściwość | Uwagi |
| --- | --- | --- |
| AccountId | Nie dotyczy | Twórca subskrypcji nie jest śledzony. Użyj invoiceSectionId (tak samo jak departmentId). |
| AccountNameAccountOwnerId i AccountOwnerEmail | Nie dotyczy | Twórca subskrypcji nie jest śledzony. Użyj invoiceSectionName (tak samo jak departmentname). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Należy pamiętać, że te właściwości są przeciwieństwem. Jeśli isAzureCreditEnabled ma wartość true, ChargesBilledSeparately ma wartość false. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | Dokładne wartości ciągu mogą się różnić. |
| ConsumedServiceId | Brak | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Data i usageStartDate | date | &nbsp;  |
| Day | Brak | Analizuje dzień od daty. |
| DepartmentId | invoiceSectionId | Dokładne wartości są różne. |
| DepartmentName | invoiceSectionName | Dokładne wartości ciągu mogą się różnić. W razie potrzeby skonfiguruj sekcje faktur, aby dopasować działy. |
| Koszt rozszerzony i koszt | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Jest opłatą cykliczną | Brak | &nbsp;  |
| Lokalizacja | location | &nbsp;  |
| MeterCategory | meterCategory | Dokładne wartości ciągu mogą się różnić. |
| Identyfikator miernika | meterId | Dokładne wartości ciągu różnią się. |
| MeterName | meterName | Dokładne wartości ciągu mogą się różnić. |
| MeterRegion | meterRegion | Dokładne wartości ciągu mogą się różnić. |
| MeterSubCategory | meterSubCategory | Dokładne wartości ciągu mogą się różnić. |
| Month | Brak | Analizuje miesiąc od daty. |
| Nazwa oferty | Brak | Użyj PublisherName i productOrderName. |
| OfferId | Brak | &nbsp;  |
| Order Number | Brak | &nbsp;  |
| partNumber | Brak | Używaj meterId i productOrderName do unikatowego identyfikowania cen. |
| Plan Name | productOrderName | &nbsp;  |
| Product (Produkt) | Product (Produkt) |   |
| Produktu | productId | Dokładne wartości ciągu różnią się. |
| Publisher Name | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Dokładne wartości ciągu różnią się. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Brak | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Nie dotyczy | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Dokładne wartości ciągu mogą się różnić. |
| ServiceTier | meterSubCategory | Dokładne wartości ciągu mogą się różnić. |
| StoreServiceIdentifier | Nie dotyczy | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tagi | tagów | Właściwość Tags ma zastosowanie do obiektu głównego, a nie do właściwości właściwości zagnieżdżonych. |
| UnitOfMeasure | unitOfMeasure | Dokładne wartości ciągu różnią się. |
| usageEndDate | date | &nbsp;  |
| Year | Brak | Analizuje rok od daty. |
| Nowy | billingCurrency | Waluta używana do naliczania opłat. |
| Nowy | billingProfileId | Unikatowy identyfikator profilu rozliczeń (analogicznie jak rejestracja). |
| Nowy | billingProfileName | Nazwa profilu rozliczeń (taka sama jak rejestracja). |
| Nowy | chargeType | Użyj, aby odróżnić użycie usług platformy Azure, użycie witryny Marketplace i zakupy. |
| Nowy | invoiceId | Unikatowy identyfikator faktury. Puste dla bieżącego, otwartego miesiąca. |
| Nowy | publisherType | Typ wydawcy dla zakupów. Puste do użycia. |
| Nowy | serviceFamily | Typ zakupu. Puste do użycia. |
| Nowy | servicePeriodEndDate | Data końcowa zakupionej usługi. |
| Nowy | servicePeriodStartDate | Data rozpoczęcia zakupionej usługi. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Interfejs API okresów rozliczeniowych został zastąpiony przez interfejs API faktur

Konta rozliczeń MCA nie używają okresów rozliczeniowych. Zamiast tego używają faktur do określania zakresu kosztów do określonych okresów rozliczeniowych. [Interfejs API okresów rozliczeniowych](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) jest zastępowany przez interfejs API faktur. Wszystkie interfejsy API użycia są zastępowane przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie do usługi REST](/rest/api/azure/#create-the-request).

Aby uzyskać faktury za pomocą interfejsu API faktur:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Interfejsy API arkusza cen

W tej sekcji omówiono istniejące interfejsy API arkusza cen oraz zamieszczono zalecenia dotyczące przejścia do interfejsu API arkusza cen dla umów klientów firmy Microsoft. Omówiono w nim również interfejs API arkusza cen dla umów klientów firmy Microsoft i objaśnia pola w arkuszach cen. [Karta Enterprise Get Prices](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) i [Enterprise Get okress](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API są zastępowane przez interfejs API arkusza cen dla umów klienta firmy Microsoft (Microsoft. rozliczenia/billingAccounts/billingProfiles/arkusza cen). Nowy interfejs API obsługuje zarówno formaty JSON, jak i CSV w formatach asynchronicznych. Wszystkie interfejsy API użycia są zastępowane przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie do usługi REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Rozliczenia interfejsów API przedsiębiorstwa

Używane są rozliczenia interfejsów API przedsiębiorstwa z rejestracjami w przedsiębiorstwie w celu uzyskania informacji o cenie i okresie rozliczeniowym. Uwierzytelnianie i autoryzacja używane Azure Active Directory tokeny sieci Web.

W celu uzyskania odpowiednich cen za określoną rejestrację w przedsiębiorstwie przy użyciu arkusza cen i interfejsów API okresu rozliczeniowego:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Interfejs API arkusza cen dla umów dla klientów firmy Microsoft

Skorzystaj z interfejsu API arkusza cen dla umów dla klientów firmy Microsoft, aby wyświetlić ceny wszystkich usług użycia platformy Azure i korzystania z portalu Marketplace. Ceny pokazywane dla profilu rozliczania mają zastosowanie do wszystkich subskrypcji należących do profilu rozliczeń.

Użyj interfejsu API arkusza cen, aby wyświetlić wszystkie dane arkusza cen usług użycia platformy Azure w formacie CSV:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Użyj interfejsu API arkusza cen, aby wyświetlić wszystkie dane arkusza cen usług użycia platformy Azure w formacie JSON:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Użycie interfejsu API zwraca arkusz cen dla całego konta. Można jednak również uzyskać skróconą wersję arkusza cen w formacie PDF. Podsumowanie obejmuje użycie platformy Azure i usług zużycia w witrynie Marketplace, które są rozliczane za określoną fakturę. Faktura jest identyfikowana przez {invoiceId}, która jest taka sama jak **numer faktury** wyświetlany w plikach PDF podsumowania faktury. Oto przykład.

![Przykładowy obraz pokazujący numer faktury odpowiadający InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Aby wyświetlić informacje o fakturze przy użyciu interfejsu API arkusza cen w formacie CSV:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Aby wyświetlić informacje o fakturze za pomocą interfejsu API arkusza cen w formacie JSON:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Możesz również sprawdzić szacowane ceny dla dowolnego użycia platformy Azure lub usługi zużycia w portalu Marketplace w bieżącym otwartym cyklu rozliczeniowym lub okresie usługi.

Aby wyświetlić szacowane ceny usług zużycia przy użyciu interfejsu API arkusza cen w formacie CSV:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Aby wyświetlić szacowane ceny usług zużycia przy użyciu interfejsu API arkusza cen w formacie JSON:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Interfejsy API arkusza cen umowy klienta firmy Microsoft są *asynchronicznymi interfejsami API REST*. Odpowiedzi na interfejsy API zmieniły się ze starszych synchronicznych interfejsów API. Treść odpowiedzi interfejsu API również uległa zmianie.

#### <a name="old-response-body"></a>Stara treść odpowiedzi

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

#### <a name="new-response-body"></a>Nowa treść odpowiedzi

Interfejsy API obsługują format [asynchroniczny usługi Azure REST](../azure-resource-manager/resource-manager-async-operations.md) . Wywołaj interfejs API za pomocą polecenia GET, a otrzymasz następującą odpowiedź:

```
No Response Body

HTTP Status 202 Accepted
```

Następujące nagłówki są wysyłane z lokalizacją wyjściową:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Wprowadź inne wywołanie do lokalizacji. Odpowiedź na wywołanie GET jest taka sama do momentu osiągnięcia stanu ukończenia lub niepowodzenia operacji. Po zakończeniu odpowiedź do lokalizacji GET Call zwraca adres URL pobierania. Tak jak w przypadku wykonania operacji w tym samym czasie. Oto przykład:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Klient może również wykonać wywołanie GET dla `Azure-AsyncOperation`. Punkt końcowy zwraca stan operacji.

W poniższej tabeli przedstawiono pola w starszym interfejsie API arkusza cen usługi Enterprise get. Obejmuje ona odpowiednie pola w nowym arkuszu cen dla umów klientów firmy Microsoft:

| Stara Właściwość | Nowa właściwość | Uwagi |
| --- | --- | --- |
| billingPeriodId  | _Nie dotyczy_ | Nie dotyczy. W przypadku umów dla klientów firmy Microsoft, faktura i skojarzony arkusz cen zostały zastąpione koncepcją billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Dokładne wartości ciągu mogą się różnić. |
| includedQuantity  | includedQuantity | Nie dotyczy usług w umowach klientów firmy Microsoft. |
| partNumber  | _Nie dotyczy_ | Zamiast tego należy użyć kombinacji productOrderName (takich jak offerId) i meterid. |
| unitPrice  | unitPrice | Cena jednostkowa ma zastosowanie do usług używanych w umowach klienta firmy Microsoft. |
| currencyCode  | pricingCurrency | Umowy klienta firmy Microsoft mają reprezentacje cenowe w walucie cenowej i w walucie rozliczeniowej. CurrencyCode odpowiada pricingCurrency w umowach klientów firmy Microsoft. |
| offerId | productOrderName | Zamiast OfferId można użyć productOrderName, ale nie jest to takie samo, jak OfferId. Jednak productOrderName i licznik decyduje o cenach w umowach dla klientów firmy Microsoft związanych z meterId i OfferId w starszych rejestracjach. |

## <a name="consumption-price-sheet-api-operations"></a>Operacje interfejsu API arkusza cen zużycia

W przypadku umów Enterprise należy użyć interfejsu API arkusza cen użycia [Get](/rest/api/consumption/pricesheet/get) i [Get według okresu rozliczeniowego](/rest/api/consumption/pricesheet/getbybillingperiod) dla zakresu według identyfikatora subskrypcji lub okresu rozliczeniowego. Interfejs API używa uwierzytelniania usługi Azure Resource Management.

Aby uzyskać informacje o arkuszu cen dla zakresu przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Aby uzyskać informacje o arkuszu cen według okresu rozliczeniowego za pomocą interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Zamiast powyższych punktów końcowych interfejsu API należy używać następujących elementów do umów dla klientów firmy Microsoft:

**Interfejs API arkusza cen dla umów klienta firmy Microsoft (asynchroniczny interfejs API REST)**

Ten interfejs API jest przeznaczony dla umów klienta firmy Microsoft i zawiera dodatkowe atrybuty.

**Arkusz cen dla zakresu profilu rozliczeń na koncie rozliczeniowym**

Ten interfejs API jest istniejącym interfejsem API. Została zaktualizowana w celu dostarczenia arkusza cen dla profilu rozliczeń na koncie rozliczeniowym.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Arkusz cen dla zakresu według konta rozliczeń

Uwierzytelnianie Azure Resource Manager jest używane podczas uzyskiwania arkusza cen w zakresie rejestracji na koncie rozliczeniowym.

Aby uzyskać arkusz cen na koncie rejestracji na koncie rozliczeniowym:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

W przypadku umowy klienta firmy Microsoft należy skorzystać z informacji podanych w poniższej sekcji. Zawiera właściwości pola używane dla umów klienta firmy Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Arkusz cen dla zakresu profilu rozliczeń na koncie rozliczeniowym

Zaktualizowany arkusz cen według interfejsu API konta rozliczeniowego pobiera arkusz cen w formacie CSV. Aby uzyskać arkusz cen w zakresie profilu rozliczania dla MCA:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

W zakresie rejestracji w ramach umowy EA odpowiedź i właściwości interfejsu API są identyczne. Właściwości są zgodne z tymi samymi właściwościami MCA.

W poniższej tabeli przedstawiono starsze właściwości [interfejsów API arkusza cen Azure Resource Manager](/rest/api/consumption/pricesheet) i te same nowe właściwości.

| Stara Właściwość interfejsu API arkusza cen Azure Resource Manager  | Nowa właściwość interfejsu API arkusza cen umowy klienta firmy Microsoft   | Opis |
| --- | --- | --- |
| Identyfikator miernika | _meterId_ | Unikatowy identyfikator miernika. Analogicznie jak meterId. |
| Nazwa miernika | meterName | Nazwa miernika. Licznik oznacza zasób do wdrożenia usługi platformy Azure. |
| Kategoria miernika  | usługa | Nazwa kategorii klasyfikacji dla miernika. Taka sama jak usługa w arkuszu cen umowy Microsoft Customer Agreement. Dokładne wartości ciągu różnią się. |
| Podkategoria miernika | meterSubCategory | Nazwa kategorii klasyfikacji podrzędnej miernika. Na podstawie klasyfikacji zestawu funkcji wysokiego poziomu w usłudze. Na przykład podstawowa baza danych SQL DB a standardowa baza danych SQL. |
| Region miernika | meterRegion | &nbsp;  |
| Jednostka | _Nie dotyczy_ | Można analizować z unitOfMeasure. |
| Jednostka miary | unitOfMeasure | &nbsp;  |
| Numer części | _Nie dotyczy_ | Zamiast partNumber należy używać productOrderName i MeterId do unikatowego identyfikowania ceny dla profilu rozliczeń. Pola są wyświetlane na podstawie faktury MCA zamiast partNumber w fakturach MCA. |
| Cena jednostkowa | unitPrice | Cena jednostkowa umowy klienta firmy Microsoft. |
| Kod waluty | pricingCurrency | Umowy dla klientów firmy Microsoft reprezentują ceny w walucie cenowej i walucie rozliczeniowej. Kod waluty jest taki sam jak pricingCurrency w umowach klientów firmy Microsoft. |
| Uwzględnione | includedQuantity | Nie dotyczy usług w umowach klientów firmy Microsoft. Pokaż z wartościami równymi zero. |
|  Identyfikator oferty  | productOrderName | Zamiast OfferId należy użyć productOrderName. Nie taka sama jak OfferId, jednak productOrderName i miernik decydują o cenach w umowach klientów firmy Microsoft. Powiązane z meterId i OfferId w starszych rejestracjach. |

Cena umów dla klientów firmy Microsoft jest definiowana inaczej niż w przypadku umów Enterprise Agreement. Cena usług w ramach rejestracji w przedsiębiorstwie jest unikatowa dla produktów, PartNumber, mierników i ofert. PartNumber nie jest używana w umowach klientów firmy Microsoft.

Cena usługi zużycia na platformie Azure, która jest częścią umowy klienta firmy Microsoft, jest unikatowa dla productOrderName i meterId. Reprezentują one licznik usług i plan produktu.

Aby uzgodnić arkusz cen i użycie w interfejsie API szczegółów użycia, można użyć productOrderName i meterId.

Użytkownicy, którzy mają prawo właściciela profilu rozliczeniowego, współautor, czytelnika i Menedżera faktury, mogą pobrać arkusz cen.

Arkusz cen zawiera ceny usług, których cena jest oparta na użyciu. Usługi obejmują użycie platformy Azure i użycie witryny Marketplace. Najnowsza cena na końcu każdego okresu usługi jest zablokowana i stosowana do użycia w ramach jednego okresu usługi. W przypadku usług użycia platformy Azure okres usługi jest zwykle miesiącem kalendarzowym.

### <a name="retired-price-sheet-api-fields"></a>Pola interfejsu API arkusza cen wycofanych

Następujące pola nie są dostępne w interfejsie API arkusza cen w programie Microsoft Customer Agreement lub mają te same pola.

|Wycofane pole| Opis|
|---|---|
| billingPeriodId | Nie ma zastosowania. Odnosi się do InvoiceId dla MCA. |
| offerId | Nie dotyczy. Odnosi się do productOrderName w MCA. |
| meterCategory  | Nie dotyczy. Odnosi się do usługi w ramach MCA. |
| jednostka | Nie dotyczy. Można analizować z unitOfMeasure. |
| currencyCode | Analogicznie jak pricingCurrency w MCA. |
| meterLocation | Analogicznie jak meterRegion w MCA. |
| partNumber partnumber | Nie dotyczy, ponieważ numer części nie znajduje się na liście faktur MCA. Zamiast partNumber Użyj kombinacji meterId i productOrderName, aby jednoznacznie identyfikować ceny. |
| totalIncludedQuantity | Nie dotyczy. |
| pretaxStandardRate  | Nie dotyczy. |

## <a name="reservation-instance-charge-api-replaced"></a>Zamieniony interfejs API obciążeniowego wystąpienia rezerwacji

Możesz uzyskać transakcje rozliczeń za zakupy rezerwacji przy użyciu [interfejsu API z opłatą za wystąpienie zarezerwowane](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Nowy interfejs API obejmuje wszystkie zakupy, w tym oferty w portalu Marketplace innych firm. Wszystkie interfejsy API użycia są zastępowane przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie do usługi REST](/rest/api/azure/#create-the-request). Interfejs API naliczania wystąpień zarezerwowanych jest zastępowany przez interfejs API transakcji.

Aby uzyskać rezerwację transakcji zakupu z interfejsem API transakcji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Zamienione interfejsy API zaleceń

Zalecenia dotyczące zakupu wystąpień zarezerwowanych dla programu umożliwiają użycie maszyny wirtualnej w ciągu ostatnich 7, 30 lub 60 dni. Interfejsy API zapewniają również zalecenia dotyczące zakupu rezerwacji. Obejmują one następujące raporty:

- [Interfejs API rekomendacji udostępnionego wystąpienia zarezerwowanego](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Interfejs API zaleceń pojedynczego wystąpienia zastrzeżonego](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Wszystkie interfejsy API użycia są zastępowane przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie do usługi REST](/rest/api/azure/#create-the-request). Interfejsy API rekomendacji zastrzeżeń wymienione wcześniej zostały zastąpione przez interfejs API [Microsoft. zużycie/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) .

Aby uzyskać zalecenia dotyczące rezerwacji z interfejsem API zaleceń dotyczących rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Zamienione interfejsy API użycia rezerwacji

Użycie rezerwacji w ramach rejestracji można uzyskać przy użyciu interfejsu API Użycie wystąpień zarezerwowanych. Jeśli rejestracja zawiera więcej niż jedno wystąpienie zarezerwowane, możesz również uzyskać użycie wszystkich zakupów wystąpień zarezerwowanych przy użyciu tego interfejsu API.

Obejmują one następujące raporty:

- [Szczegóły Użycie wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Podsumowanie Użycie wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Wszystkie interfejsy API użycia są zastępowane przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [wprowadzenie do usługi REST](/rest/api/azure/#create-the-request). Wymienione wcześniej interfejsy API rekomendacji zastrzeżeń są zastępowane przez interfejsy API [Microsoft. zużycie/reservationDetails](/rest/api/consumption/reservationsdetails) i [Microsoft. reservationSummaries](/rest/api/consumption/reservationssummaries) .

Aby uzyskać szczegółowe informacje dotyczące rezerwacji przy użyciu interfejsu API szczegółów rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Aby uzyskać podsumowanie rezerwacji z interfejsem API podsumowań rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Przenieś z Cloudyn do Cost Management

Organizacje korzystające z usługi [Cloudyn](https://cloudyn.com) powinni zacząć używać [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) do dowolnych potrzeb związanych z zarządzaniem kosztami. Cost Management jest dostępna w Azure Portal bez dołączania i opóźnień na osiem godzin. Aby uzyskać więcej informacji, zobacz [dokumentację Cost Management](index.yml).

Za pomocą Azure Cost Management można:

- Wyświetlanie kosztów z upływem czasu względem wstępnie zdefiniowanego budżetu. Analizuj dzienne wzorce kosztów, aby identyfikować i przetrzymywać anomalie. Podziel koszty według tagów, grupy zasobów, usługi i lokalizacji.
- Utwórz budżety, aby ustawić limity użycia i kosztów i otrzymywać powiadomienia o osiągnięciu ważnych progów. Skonfiguruj automatyzację przy użyciu grup akcji, aby wyzwalać zdarzenia niestandardowe i wymuszać stałe limity dla Twoich warunków.
- Zoptymalizuj koszty i użycie, korzystając z rekomendacji Azure Advisor. Odkryj optymalizacje zakupu z rezerwacjami, niedownsizeymi nieużywanymi maszynami wirtualnymi i Usuń nieużywane zasoby do pozostania w ramach budżetów.
- Zaplanuj eksportowanie danych o kosztach i użyciu, aby opublikować plik CSV na koncie magazynu codziennie. Automatyzowanie integracji z systemami zewnętrznymi w celu synchronizowania danych dotyczących rozliczeń i aktualności.

## <a name="power-bi-integration"></a>Integracja z usługą Power BI

Power BI można również użyć do raportowania kosztów. [Łącznik Azure Cost Management](/power-bi/desktop-connect-azure-cost-management) dla Power BI Desktop może służyć do tworzenia zaawansowanych, niestandardowych raportów, które pomagają lepiej zrozumieć wydatki na korzystanie z platformy Azure. Łącznik Azure Cost Management obecnie obsługuje klientów z umową klienta firmy Microsoft lub Umowa Enterprise (EA).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [dokumentację Cost Management](index.yml) , aby dowiedzieć się, jak monitorować i kontrolować wydatki na platformę Azure. Lub, jeśli chcesz zoptymalizować użycie zasobów przy użyciu Cost Management.
