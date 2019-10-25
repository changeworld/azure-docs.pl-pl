---
title: Wprowadzenie do Azure Cost Management dla partnerów
description: W tym artykule wyjaśniono, w jaki sposób partnerzy używają Azure Cost Management funkcji i jak umożliwiają Cost Management dostęp dla swoich klientów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 6d59964013a2631430ecd7e46d1ce0f6be60a05f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802026"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Wprowadzenie do Azure Cost Management dla partnerów

Azure Cost Management jest natywnie dostępna dla partnerów, którzy zostali dołączeni do umowy klienta firmy Microsoft. W tym artykule wyjaśniono, w jaki sposób partnerzy używają funkcji [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) . Opisano w nim również, jak partnerzy zapewniają klientom Cost Management dostęp do nich. Klienci mogą korzystać z funkcji Cost Management, jeśli są włączone przez partnera CSP.

Partnerzy programu CSP używają Cost Management do:

- Poznanie kosztów zafakturowanych i kojarzenie kosztów z klientami, subskrypcjami, grupami zasobów i usługami.
- Uzyskaj intuicyjny wgląd w koszty platformy Azure w [analizie kosztów](quick-acm-cost-analysis.md) dzięki możliwościom analizowania kosztów według klientów, subskrypcji, grupy zasobów, zasobu, licznika, usługi i wielu innych wymiarów.
- Wyświetlanie kosztów zasobów, w przypadku których uzyskano środki na rzecz partnerów (PEC) stosowane w analizie kosztów.
- Skonfiguruj powiadomienia i automatyzację przy użyciu [budżetów](tutorial-acm-create-budgets.md) programowych i alertów, gdy koszty przekraczają budżety.
- Włącz zasady Azure Resource Manager, które zapewniają klientom dostęp do Cost Management danych. Klienci mogą następnie wyświetlać dane dotyczące kosztów zużycia dla swoich subskrypcji [, używając stawek płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/calculator/)użyciem.

Oto przykład przedstawiający koszty dla wszystkich klientów.
![przykład pokazujący koszty dla wszystkich klientów](./media/get-started-partners/customer-costs1.png)

Oto przykład przedstawiający koszty jednego klienta.
![przykład pokazujący koszty jednego klienta](./media/get-started-partners/customer-costs2.png)

Wszystkie funkcje dostępne w Azure Cost Management są również dostępne w interfejsach API REST. Użyj interfejsów API do automatyzowania zadań zarządzania kosztami.

## <a name="prerequisites"></a>Wymagania wstępne

Azure Cost Management wymaga dostępu do odczytu do konta rozliczeń lub subskrypcji. Dostęp można udzielić na dowolnym poziomie powyżej zasobów, na koncie rozliczeń lub grupie zarządzania w dół do poszczególnych grup zasobów, w których są zarządzane aplikacje. Aby uzyskać więcej informacji na temat włączania i przypisywania dostępu do Azure Cost Management dla konta rozliczeniowego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview). Role **administratora globalnego** i **agenta administracyjnego** mogą zarządzać kosztami konta rozliczeniowego.

Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [opis Cost Management danych](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management używa zakresów

Zakresy są miejscem, w którym są zarządzane dane dotyczące rozliczeń, mają role specyficzne dla płatności, wyświetlają faktury i przeprowadzają ogólne zarządzanie kontami. Role rozliczeń i kont są zarządzane oddzielnie od zakresów używanych do zarządzania zasobami, które używają RBAC. Aby wyraźnie odróżnić intencje oddzielnych zakresów, w tym różnice kontroli dostępu, są one określane odpowiednio jako zakresy rozliczeń i zakresy RBAC.

Aby zrozumieć zakresy rozliczeń i zakresy RBAC oraz jak działa zarządzanie kosztami przy użyciu zakresów, zobacz [Opis i praca z zakresami](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Zarządzanie kosztami przy użyciu zakresów rozliczeń dzierżawy partnerów

Po dodaniu klientów do umowy klienta firmy Microsoft w dzierżawie są dostępne następujące _zakresy rozliczeń_ . Użyj zakresów, aby zarządzać kosztami w Cost Management.

### <a name="billing-account-scope"></a>Zakres konta rozliczeniowego

Zakres konta rozliczeniowego służy do wyświetlania kosztów przed opodatkowaniem wszystkich klientów i profilów rozliczeń. Koszty faktury są wyświetlane tylko dla produktów na podstawie zużycia klienta w umowie klienta firmy Microsoft. Koszty faktury są jednak wyświetlane dla produktów na podstawie zakupów dla klientów w ramach umowy klienta firmy Microsoft i oferty dostawcy usług kryptograficznych. Obecnie domyślna waluta do wyświetlania kosztów w zakresie to dolary AMERYKAŃSKIe. Budżety ustawione dla zakresu są również w USD.

Niezależnie od różnych walut naliczanych przez klientów, partnerzy używają zakresu konta rozliczeniowego do ustawiania budżetów i zarządzania kosztami w USD w przypadku klientów, subskrypcji, zasobów i grup zasobów.

Partnerzy filtrują także koszty w określonej walucie rozliczeniowej dla klientów w widoku analizy kosztów. Wybierz listę **rzeczywistych kosztów** , aby wyświetlić koszty w ramach obsługiwanych walut rozliczeniowych klientów.

![Przykład pokazujący rzeczywiste wybór kosztów dla walut](./media/get-started-partners/actual-cost-selector.png)

Za pomocą [widoku płatnego kosztu](quick-acm-cost-analysis.md#customize-cost-views) w zakresach rozliczeń można wyświetlić amortyzowane koszty wystąpienia zarezerwowanego w ramach okresu rezerwacji.

### <a name="billing-profile-scope"></a>Zakres profilu rozliczeń

Zakres profilu rozliczeń umożliwia wyświetlanie kosztów przed opodatkowaniem w walucie rozliczeń dla wszystkich klientów w przypadku wszystkich produktów i subskrypcji uwzględnionych w fakturze. Można filtrować koszty w profilu rozliczania dla określonej faktury przy użyciu filtru **InvoiceID** . Filtr pokazuje koszty zużycia i zakupu produktów dla określonej faktury. Możesz również filtrować koszty określonego klienta na fakturze, aby zobaczyć koszty wstępne.

Po dołączeniu klientów do umowy klienta firmy Microsoft otrzymasz fakturę zawierającą wszystkie opłaty za wszystkie produkty (zużycie, zakupy i uprawnienia) dla tych klientów w umowie klienta firmy Microsoft. W przypadku rozliczania w tej samej walucie faktury obejmują również opłaty za uprawnienia i zakupione produkty, takie jak SaaS, Azure Marketplace i rezerwacje dla klientów, którzy nadal znajdują się w ofercie dostawcy usług kryptograficznych.

Aby pomóc w uzgadnianiu opłat z fakturą klienta, zakres profilów rozliczeń umożliwia wyświetlenie wszystkich kosztów, które naliczane są za faktury dla klientów. Podobnie jak w przypadku faktury, zakres pokazuje koszty każdego klienta w nowej umowie klienta firmy Microsoft. Zakres zawiera również wszystkie opłaty za produkty związane z prawami klienta nadal dostępne w bieżącej ofercie dostawcy CSP.

Zakresy profilu rozliczeń i konta rozliczeniowego są jedynymi odpowiednimi zakresami, które pokazują opłaty za Produkty uprawniające i zakupu, takie jak Azure Marketplace i rezerwacja rezerwacji.

Profile rozliczeń definiują subskrypcje dołączone do faktury. Profile rozliczeń to funkcjonalny odpowiednik rejestracji w ramach umowy Enterprise Agreement. Profil rozliczeniowy jest zakresem, w którym są generowane faktury.

Obecnie waluta rozliczeniowa klienta jest walutą domyślną podczas wyświetlania kosztów w zakresie profilu rozliczania. Budżety ustawione w zakresie profilu rozliczania są wyrażone w walucie rozliczeń.

Partnerzy mogą używać zakresu, aby uzgodnić faktury. I, wykorzystują zakres do ustawiania budżetów w walucie rozliczeniowej dla następujących elementów:

- Konkretna filtrowana faktura
- Klient
- Subskrypcja
- Grupa zasobów
- Zasób
- Usługa platformy Azure
- Jednostka
- ResellerMPNID

### <a name="customer-scope"></a>Zakres klienta

Partnerzy korzystają z zakresu, aby zarządzać kosztami związanymi z klientami, które zostały dołączone do umowy klienta firmy Microsoft. Zakres umożliwia partnerom wyświetlanie kosztów przed opodatkowaniem określonego klienta. Możesz również filtrować koszty wstępne dla określonej subskrypcji, grupy zasobów lub zasobu.

Zakres klienta nie obejmuje klientów, którzy znajdują się w bieżącej ofercie dostawcy CSP. Zakres zawiera tylko klientów, którzy mają umowę klienta firmy Microsoft. Koszty związane z uprawnieniem, nie użycie platformy Azure, w przypadku bieżącego dostawcy usług kryptograficznych klienci oferują dostęp do zakresów rozliczeń i profilów rozliczeń w przypadku zastosowania filtru klienta.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Dostęp partnerów do zakresów rozliczeń w Cost Management

Tylko użytkownicy z rolami **administrator globalny** i **administrator mogą** zarządzać kosztami kont rozliczeń, profilów rozliczeń i klientów bezpośrednio w dzierżawie platformy Azure partnera. Aby uzyskać więcej informacji na temat ról Centrum partnerskiego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>Włącz zarządzanie kosztami w dzierżawie klienta

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
Zasady ![, aby umożliwić klientom wyświetlanie opłat z płatnością zgodnie z rzeczywistym użyciem](./media/get-started-partners/cost-management-billing-policies.png)

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

| **Pole** | **Opis** |
| --- | --- |
| PartnerTenantID | Identyfikator dzierżawcy Azure Active Directory partnera |
| PartnerName | Nazwa partnera Azure Active Directory dzierżawy |
| CustomerTenantID | Identyfikator dzierżawy Azure Active Directory subskrypcji klienta |
| CustomerName | Nazwa dzierżawy Azure Active Directoryej zawierającej subskrypcję klienta |
| ResellerMPNID | MPNID dla odsprzedawcy skojarzonego z subskrypcją |
| subscription ID | Unikatowy identyfikator wygenerowany przez firmę Microsoft dla subskrypcji platformy Azure |
| subscriptionName | Nazwa subskrypcji platformy Azure |
| billingProfileID | Identyfikator dla profilu rozliczeń. Dział IT grupuje koszty w ramach jednej waluty rozliczeniowej dla klientów.
| invoiceID | Identyfikator faktury dla faktury, w której występuje konkretna transakcja |
| resourceGroup | Nazwa grupy zasobów platformy Azure. Używany do zarządzania cyklem życia zasobów. |
| partnerEarnedCreditRate | Stosowana stawka rabatu w przypadku, gdy Partner uzyskał kredyt (PEC) w oparciu o dostęp do linku administratora partnera. |
| partnerEarnedCreditApplied | Wskazuje, czy zastosowana została kwota środków uzyskanych przez partnera. |

W widoku [Analiza kosztów](quick-acm-cost-analysis.md) można także [zapisywać widoki](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) i eksportować dane do [plików CSV i PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis).

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

Partnerzy i klienci mogą używać Cost Management interfejsów API opisanych w poniższych sekcjach dla typowych zadań.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Interfejsy API Azure Cost Management — bezpośredni i pośredni dostawca

Partnerzy z dostępem do zakresów rozliczeń w dzierżawie partnerskim mogą używać następujących interfejsów API do wyświetlania zafakturowanych kosztów.

Interfejsy API w zakresie subskrypcji mogą być wywoływane przez partnera niezależnie od zasad kosztów, jeśli mają dostęp do subskrypcji. Inni użytkownicy z dostępem do subskrypcji, takie jak klient lub odsprzedawca, mogą wywoływać interfejsy API tylko po włączeniu przez partnera zasad kosztów dla dzierżawy klienta.


#### <a name="to-get-a-list-of-billing-accounts"></a>Aby uzyskać listę kont rozliczeniowych

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Aby uzyskać listę klientów

```
armclient get "providers/Microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Aby uzyskać listę subskrypcji

```
armclient get "/providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Aby uzyskać zasady dotyczące wyświetlania kosztów przez klientów

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Aby ustawić zasady dla klientów do wyświetlania kosztów

```
armclient put "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview" @policy.json
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Aby uzyskać użycie usługi platformy Azure dla konta rozliczeniowego

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Aby pobrać użycie usługi platformy Azure przez klienta

Następujące wywołanie get jest operacją asynchroniczną.

```
armclient get providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Wywołaj identyfikator URI `Location` zwrócony w odpowiedzi, aby sprawdzić stan operacji. Po *zakończeniu*stanu Właściwość `downloadUrl` zawiera link, którego można użyć do pobrania wygenerowanego raportu.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Aby pobrać lub pobrać arkusz cen dla zużytych usług platformy Azure

Najpierw użyj poniższego wpisu.

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Następnie Wywołaj wartość właściwości operacji asynchronicznej. Na przykład:

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheetDownloadOperations/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX?sessiontoken=0:11186&api-version=2019-10-01-preview"
```
Poprzednie wywołanie Get zwraca łącze pobierania zawierające arkusz cen.

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Aby uzyskać koszty klienta z ostatnich dwóch miesięcy, posortowane według miesiąca.

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Aby uzyskać koszty subskrypcji platformy Azure z ostatnich dwóch miesięcy, posortowane według miesiąca.

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Aby uzyskać dzienne koszty bieżącego miesiąca

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="create-a-budget-for-a-partner"></a>Tworzenie budżetu dla partnera

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01 @budgetCreate.json
```


#### <a name="create-a-budget-for-a-customer"></a>Tworzenie budżetu dla klienta

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/providers/Microsoft.Consumption/budgets/test-partner-demo?api-version=2019-10-01 @budgetCreate.json
```
#### <a name="delete-a-budget"></a>Usuń budżet

```
armclient delete providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```


## <a name="next-steps"></a>Następne kroki
- [Rozpocznij analizowanie kosztów](quick-acm-cost-analysis.md) w Cost Management
- [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md) w Cost Management
