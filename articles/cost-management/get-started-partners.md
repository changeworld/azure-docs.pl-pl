---
title: Wprowadzenie do Azure Cost Management dla partnerów
description: W tym artykule wyjaśniono, w jaki sposób partnerzy używają Azure Cost Management funkcji i jak umożliwiają Cost Management dostęp dla swoich klientów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377696"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Wprowadzenie do Azure Cost Management dla partnerów

Azure Cost Management jest natywnie dostępna dla partnerów, którzy zostali dołączeni do umowy klienta firmy Microsoft. W tym artykule wyjaśniono, w jaki sposób partnerzy używają funkcji [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) . Opisano w nim również, jak partnerzy zapewniają klientom Cost Management dostęp do nich. Klienci korzystający z programu CSP mogą korzystać z funkcji Cost Management, jeśli są włączone przez partnera CSP.

Partnerzy programu CSP używają Cost Management do:

- Poznanie kosztów zafakturowanych i kojarzenie kosztów z klientami, subskrypcjami, grupami zasobów i usługami.
- Uzyskaj intuicyjny wgląd w koszty platformy Azure w [analizie kosztów](quick-acm-cost-analysis.md) dzięki możliwościom analizowania kosztów według klientów, subskrypcji, grupy zasobów, zasobu, licznika, usługi i wielu innych wymiarów.
- Wyświetlanie kosztów zasobów, w przypadku których uzyskano środki na rzecz partnerów (PEC) stosowane w analizie kosztów.
- Skonfiguruj powiadomienia i automatyzację przy użyciu [budżetów](tutorial-acm-create-budgets.md) programowych i alertów, gdy koszty przekraczają budżety.
- Włącz zasady Azure Resource Manager, które zapewniają klientom dostęp do Cost Management danych. Klienci mogą następnie wyświetlać dane dotyczące kosztów zużycia dla swoich subskrypcji [, używając stawek płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/calculator/)użyciem.

Wszystkie funkcje dostępne w Azure Cost Management są również dostępne w interfejsach API REST. Użyj interfejsów API do automatyzowania zadań zarządzania kosztami.

## <a name="prerequisites"></a>Wymagania wstępne

Azure Cost Management wymaga dostępu do odczytu do konta rozliczeń lub subskrypcji. Dostęp można udzielić na dowolnym poziomie powyżej zasobów, na koncie rozliczeń lub grupie zarządzania w dół do poszczególnych grup zasobów, w których są zarządzane aplikacje. Aby umożliwić użytkownikom subskrypcji wyświetlanie cen i kosztów, w przypadku konta rozliczeniowego należy włączyć dostęp do wyświetlania opłat. Aby uzyskać więcej informacji na temat włączania i przypisywania dostępu do Azure Cost Management, zobacz [Przypisywanie dostępu do danych](assign-access-acm-data.md). Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [opis Cost Management danych](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management używa zakresów

Zakresy są miejscem, w którym są zarządzane dane dotyczące rozliczeń, mają role specyficzne dla płatności, wyświetlają faktury i przeprowadzają ogólne zarządzanie kontami. Role rozliczeń i kont są zarządzane oddzielnie od zakresów używanych do zarządzania zasobami, które używają RBAC. Aby wyraźnie odróżnić intencje oddzielnych zakresów, w tym różnice kontroli dostępu, są one określane odpowiednio jako zakresy rozliczeń i zakresy RBAC.

Aby zrozumieć zakresy rozliczeń i zakresy RBAC oraz jak działa zarządzanie kosztami przy użyciu zakresów, zobacz [Opis i praca z zakresami](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Zarządzanie kosztami przy użyciu zakresów rozliczeń dzierżawy partnerów

Po dodaniu klientów do umowy klienta firmy Microsoft w dzierżawie są dostępne następujące _zakresy rozliczeń_ . Użyj zakresów, aby zarządzać kosztami w Cost Management.

### <a name="billing-account-scope"></a>Zakres konta rozliczeniowego

Zakres konta rozliczeniowego służy do wyświetlania kosztów przed opodatkowaniem wszystkich klientów i profilów rozliczeń. Możesz również wyświetlić koszty faktury dla produktów opartych na użyciu dla klientów w umowie klienta firmy Microsoft. Koszty faktury są również wyświetlane dla produktów na podstawie zakupów dla klientów z umową klienta firmy Microsoft i oferty dostawcy usług kryptograficznych. Obecnie domyślna waluta do wyświetlania kosztów w zakresie to dolary AMERYKAŃSKIe. Budżety ustawione dla zakresu są również w USD.

Niezależnie od waluty rozliczanej przez klienta, partnerzy używają zakresu do ustawiania budżetów i zarządzania kosztami w USD przez ich klientów, subskrypcje, zasoby i grupy zasobów.

Partnerzy filtrują także koszty w określonej walucie rozliczeniowej dla klientów w widoku analizy kosztów. Wybierz listę **rzeczywistych kosztów** , aby wyświetlić koszty w ramach obsługiwanych walut rozliczeniowych klientów.

![Przykład pokazujący rzeczywiste wybór kosztów dla walut](./media/get-started-partners/actual-cost-selector.png)

Za pomocą [widoku płatnego kosztu](quick-acm-cost-analysis.md#customize-cost-views) w zakresach rozliczeń można wyświetlić amortyzowane koszty wystąpienia zarezerwowanego w ramach okresu rezerwacji.

### <a name="billing-profile-scope"></a>Zakres profilu rozliczeń

Zakres profilu rozliczeń umożliwia wyświetlanie kosztów przed opodatkowaniem w walucie rozliczeń dla wszystkich klientów w przypadku wszystkich produktów i subskrypcji uwzględnionych w fakturze. Można filtrować koszty w profilu rozliczania dla określonej faktury przy użyciu filtru **InvoiceID** . Filtr pokazuje koszty zużycia i zakupu produktów dla określonej faktury. Możesz również filtrować koszty określonego klienta na fakturze, aby zobaczyć koszty wstępne.

Po dołączeniu klientów do umowy klienta firmy Microsoft otrzymujesz fakturę klienta, która przedstawia opłaty za prawo i nabyte produkty, takie jak SaaS, Azure Marketplace i rezerwacje. Gdy opłata jest naliczana w tej samej walucie rozliczeniowej, faktura również pokazuje opłaty klienta, które nie są uwzględnione w nowej umowie z firmą Microsoft.

Aby pomóc w uzgadnianiu opłat z fakturą klienta, zakres profilów rozliczeń umożliwia wyświetlenie wszystkich kosztów, które naliczane są za faktury dla klientów. Podobnie jak w przypadku faktury, zakres pokazuje koszty każdego klienta w nowej umowie klienta firmy Microsoft. Zakres zawiera również wszystkie opłaty za produkty związane z prawami klienta nadal dostępne w bieżącej ofercie dostawcy CSP.

Zakresy profilu rozliczeń i konta rozliczeniowego są jedynymi, które zawierają opłaty za Produkty uprawniające i zakupu.

Profile rozliczeń definiują subskrypcje dołączone do faktury. Profile rozliczeń to funkcjonalny odpowiednik rejestracji w ramach umowy Enterprise Agreement. Rejestracja to zakres, w którym są generowane faktury. Podobnie zakupy, które nie są oparte na użyciu, takie jak Azure Marketplace i rezerwacje, są dostępne tylko w zakresie profilu rozliczania.

Obecnie waluta rozliczeniowa klienta jest walutą domyślną podczas wyświetlania kosztów w zakresie profilu rozliczania. Budżety ustawione w zakresie profilu rozliczania są w walucie rozliczeniowej.

Partnerzy mogą używać zakresu, aby uzgodnić faktury. I, wykorzystują zakres do ustawiania budżetów w walucie rozliczeniowej dla:

- Konkretna filtrowana faktura
- Dział
- Subskrypcja
- Grupa zasobów
- Zasób
- Usługa platformy Azure
- Jednostka
- ResellerMPNID

### <a name="customer-scope"></a>Zakres klienta

Partnerzy korzystają z zakresu, aby zarządzać kosztami związanymi z klientami, które zostały dołączone do umowy klienta firmy Microsoft. Zakres umożliwia partnerom wyświetlanie kosztów przed opodatkowaniem określonego klienta. Możesz również filtrować koszty wstępne dla określonej subskrypcji, grupy zasobów lub zasobu.

Zakres klienta nie obejmuje klientów, którzy znajdują się w bieżącej ofercie dostawcy CSP. Koszty związane z uprawnieniem, nie użycie platformy Azure, w przypadku bieżącego dostawcy usług kryptograficznych klienci oferują dostęp do zakresów rozliczeń i profilów rozliczeń w przypadku zastosowania filtru klienta.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Dostęp partnerów do zakresów rozliczeń w Cost Management

Tylko użytkownicy z rolami **administrator globalny** i **administrator mogą** zarządzać kosztami kont rozliczeń, profilów rozliczeń i klientów bezpośrednio w dzierżawie platformy Azure partnera. Aby uzyskać więcej informacji na temat ról Centrum partnerskiego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

### <a name="enable-cost-management-in-the-customer-tenant"></a>Włącz zarządzanie kosztami w dzierżawie klienta

Partnerzy mogą zapewnić dostęp do Cost Management po dołączeniu klientów do umowy klienta firmy Microsoft. Następnie partnerzy mogą następnie włączyć zasady umożliwiające klientom wyświetlanie ich kosztów w ramach stawek za sprzedaż zgodnie z rzeczywistym użyciem. Koszty są pokazane w walucie rozliczeniowej klienta dla ich użycia w ramach subskrypcji RBAC i zakresów grup zasobów.

Po włączeniu przez partnera zasad dotyczących widoczności kosztów każdy użytkownik, który ma Azure Resource Manager dostęp do subskrypcji, może zarządzać kosztami i analizować je według stawek płatność zgodnie z rzeczywistym użyciem. Skutecznie odsprzedawcy i Klienci, którzy mają odpowiedni dostęp RBAC do subskrypcji platformy Azure, mogą wyświetlać koszt.

Bez względu na to, że partnerzy mogą również wyświetlać koszty, jeśli mają dostęp do subskrypcji i grupy zasobów.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Włącz zasady, aby wyświetlić opłaty za użycie platformy Azure

Partnerzy używają następujących informacji, aby włączyć zasady, aby wyświetlić opłaty za użycie platformy Azure dla swoich klientów.

W Azure Portal Zaloguj się do dzierżawy partnerskiej, a następnie kliknij pozycję **Cost Management + rozliczanie**. Wybierz konto rozliczeniowe, a następnie kliknij pozycję **klienci**. Lista klientów jest skojarzona z kontem rozliczeń.

Na liście klientów wybierz klienta, który ma być uprawniony do wyświetlania kosztów.

![Wybierz klientów w Cost Management](./media/get-started-partners/customer-list.png)

W obszarze **Ustawienia**kliknij pozycję **zasady**.

Bieżące zasady widoczności kosztów są wyświetlane dla opłat za **użycie platformy Azure** skojarzonych z subskrypcjami dla wybranego klienta.
![Policy, aby umożliwić klientom wyświetlanie opłat z płatnością zgodnie z rzeczywistym użyciem @ no__t-1

Jeśli zasady są ustawione na wartość **nie**, Azure Cost Management nie są dostępne dla użytkowników subskrypcji skojarzonych z klientem. Zasady widoczności kosztów są domyślnie wyłączone dla wszystkich użytkowników subskrypcji, chyba że jest on włączony przez partnera.

Gdy dla zasad kosztów ustawiono wartość **tak**, użytkownicy subskrypcji powiązani z dzierżawcą mogą zobaczyć opłaty za użycie w ramach stawek płatności zgodnie z rzeczywistym użyciem.

Po włączeniu zasad widoczności kosztów wszystkie usługi zawierające subskrypcję pokazują koszty według stawek płatność zgodnie z rzeczywistym użyciem. Użycie zastrzeżenia ma wartość zero opłaty za rzeczywiste i amortyzowany koszt. Zakupy i uprawnienia nie są skojarzone z określoną subskrypcją. W związku z tym zakupy nie są wyświetlane w zakresie subskrypcji.

Aby wyświetlić koszty dla dzierżawy klienta, Otwórz Cost Management + rozliczenia, a następnie kliknij pozycję konta rozliczeń. Na liście kont rozliczeń kliknij konto rozliczeniowe.

![Wybierz konto rozliczeniowe](./media/get-started-partners/select-billing-account.png)

W obszarze **rozliczenia**kliknij pozycję **subskrypcje platformy Azure**, a następnie kliknij klienta.

![Wybierz klienta subskrypcji platformy Azure](./media/get-started-partners/subscriptions-select-customer.png)

Kliknij pozycję **Analiza kosztów** i Rozpocznij przeglądanie kosztów.
Analizy kosztów, budżetów i alertów są teraz dostępne dla zakresów RBAC subskrypcji i grup zasobów w ramach stawek płatność zgodnie z rzeczywistym użyciem.

![Wyświetlanie analizy kosztów jako klienta ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Widoki amortyzowane i rzeczywiste koszty wystąpień zarezerwowanych w zakresach RBAC pokazują Zero opłat. Koszty wystąpień zarezerwowanych są wyświetlane tylko w zakresach rozliczeniowych, w których dokonano zakupów.

## <a name="analyze-costs-in-cost-analysis"></a>Analizowanie kosztów w analizie kosztów

Partnerzy mogą eksplorować i analizować koszty w analizie kosztów od klientów dla określonego klienta lub dla faktury. Funkcje Filter i Group by umożliwiają analizowanie kosztów według wielu pól, w tym:

| **Pole** | **Opis** | **Równoważna kolumna w centrum partnerskim** |
| --- | --- | --- |
| PartnerTenantID | Identyfikator dzierżawcy Azure Active Directory partnera | Partner Azure Active Directory TenantID jako identyfikator partnera. W formacie identyfikatora GUID. |
| PartnerName | Nazwa partnera Azure Active Directory dzierżawy | Nazwa partnera |
| CustomerTenantID | Identyfikator dzierżawy Azure Active Directory subskrypcji klienta | Identyfikator organizacji klienta. Na przykład klient Azure Active Directory TenantID. |
| CustomerName | Nazwa dzierżawy Azure Active Directoryej zawierającej subskrypcję klienta | Nazwa organizacji klienta zgłoszona w centrum partnerskim. Ważne w przypadku uzgadniania faktury z informacjami o systemie. |
| ResellerMPNID | MPNID dla odsprzedawcy skojarzonego z subskrypcją | IDENTYFIKATOR MPN odsprzedawcy rekordu dla subskrypcji. Niedostępne dla bieżącego działania. |
| subscription ID | Unikatowy identyfikator wygenerowany przez firmę Microsoft dla subskrypcji platformy Azure | ND |
| subscriptionName | Nazwa subskrypcji platformy Azure | ND |
| billingProfileID | Identyfikator dla profilu rozliczeń. Dział IT grupuje koszty w ramach jednej waluty rozliczeniowej dla klientów. | Identyfikator grupy rozliczeń partnera MCAPI. Używane w żądaniach interfejsu API, ale nie są uwzględniane w odpowiedziach. |
| invoiceID | Identyfikator faktury dla faktury, w której występuje konkretna transakcja | Numer faktury, w której zostanie wyświetlona określona transakcja. |
| resourceGroup | Nazwa grupy zasobów platformy Azure. Używany do zarządzania cyklem życia zasobów. | Nazwa grupy zasobów. |
| partnerEarnedCreditRate | Stosowana stawka rabatu w przypadku, gdy Partner uzyskał kredyt (PEC) w oparciu o dostęp do linku administratora partnera. | Stawka w wysokości (PEC) uzyskana przez partnera. Na przykład 0% lub 15%. |
| partnerEarnedCreditApplied | Wskazuje, czy zastosowana została kwota środków uzyskanych przez partnera. | ND |

W widoku [Analiza kosztów](quick-acm-cost-analysis.md) można także [zapisywać widoki](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) i eksportować dane do plików [CSV i PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis) .

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Wyświetlanie kosztów zasobów dla partnerów (PEC)

W Azure Cost Management partnerzy mogą korzystać z analizy kosztów, aby wyświetlać koszty, które otrzymały zalety PEC.

W Azure Portal Zaloguj się do dzierżawy partnerskiej i wybierz pozycję **Cost Management + rozliczanie**. W obszarze **Cost Management**kliknij pozycję **Analiza kosztów**.

W widoku Analiza kosztów są wyświetlane koszty konta rozliczeniowego dla partnera. Wybierz **zakres** wymagany dla partnera, określonego klienta lub profilu rozliczeń, aby uzgodnić faktury.

Na wykresie pierścieniowym kliknij listę rozwijaną, a następnie wybierz pozycję **PartnerEarnedCreditApplied** , aby przejść do szczegółów dotyczących kosztów Pec.

![Przykład przedstawiający sposób wyświetlania środków uzyskanych przez partnera](./media/get-started-partners/cost-analysis-pec1.png)

Gdy właściwość **PartnerEarnedCreditApplied** ma _wartość true_, skojarzony koszt ma korzyść dostęp administratora przez partnera.

Gdy właściwość **PartnerEarnedCreditApplied** ma _wartość false_, skojarzony koszt nie spełnia wymagań wymaganych do uznania. Lub zakupiona usługa nie kwalifikuje się do uzyskania kredytu dla partnerów.

Dane użycia usługi zwykle zajmują 8-24 godzin, aby były wyświetlane w Cost Management. Aby uzyskać więcej informacji, zobacz [częstotliwość aktualizacji danych użycia jest różna](understand-cost-mgt-data.md#usage-data-update-frequency-varies). Kredyty PEC są wyświetlane w ciągu 48 godzin od momentu uzyskania dostępu w Azure Cost Management.


Można również grupować i filtrować według właściwości **PartnerEarnedCreditApplied** przy użyciu opcji **Grupuj według** . Użyj opcji, aby przejrzeć koszty, które nie mają PEC.

![Grupuj lub Filtruj według środków w ramach partnera](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>Interfejsy API REST Cost Management

Partnerzy, dostawcy pośrednii i klienci mogą używać Cost Management interfejsów API opisanych w poniższych sekcjach dla typowych zadań.

### <a name="azure-cost-management-apis-for-partners"></a>Interfejsy API Azure Cost Management dla partnerów

Partnerzy i użytkownicy z dostępem do zakresów rozliczeń w dzierżawie partnerskim mogą używać następujących interfejsów API.

#### <a name="to-get-a-list-of-billing-accounts"></a>Aby uzyskać listę kont rozliczeniowych

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Aby uzyskać listę klientów

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Aby uzyskać listę subskrypcji

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>Aby utworzyć nową subskrypcję

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Aby pobrać lub pobrać użycie usług platformy Azure

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>Aby uzyskać listę profilów rozliczeń

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Aby pobrać lub pobrać arkusz cen dla zużytych usług platformy Azure

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Aby uzyskać koszty klienta z ostatnich dwóch miesięcy, posortowane według miesiąca.

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Aby uzyskać koszty subskrypcji platformy Azure z ostatnich dwóch miesięcy, posortowane według miesiąca.

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Aby uzyskać dzienne koszty bieżącego miesiąca

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Aby uzyskać zasady dotyczące wyświetlania kosztów przez klientów

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Aby ustawić zasady dla klientów do wyświetlania kosztów

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>Interfejsy API Azure Cost Management dla dostawców pośrednich

Dostawcy niebezpośrednii z dostępem do zakresów RBAC w dzierżawie klienta mogą używać następujących interfejsów API. Aby rozpocząć, zaloguj się jako użytkownik lub przy użyciu nazwy głównej usługi.

#### <a name="to-get-the-billing-account-information"></a>Aby uzyskać informacje o koncie rozliczeniowym

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Aby uzyskać listę klientów

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>Aby uzyskać listę odsprzedawcy skojarzonych z klientem

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>Aby uzyskać listę subskrypcji z informacjami o odsprzedawcy

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>Aby utworzyć subskrypcję

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>Interfejsy API Azure Cost Management dla klientów

Aby uzyskać dostęp do interfejsów API, klienci używają poniższych informacji. Aby rozpocząć, zaloguj się jako użytkownik.

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Aby uzyskać lub pobrać informacje o użyciu użycia platformy Azure za pomocą stawek detalicznych

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>Następne kroki
- [Rozpocznij analizowanie kosztów](quick-acm-cost-analysis.md) w Cost Management
- [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md) w Cost Management
