---
title: Wprowadzenie do Azure Cost Management dla partnerów
description: W tym artykule wyjaśniono, w jaki sposób partnerzy używają Azure Cost Management funkcji i jak umożliwiają Cost Management dostęp dla swoich klientów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 958335892b62c17e7e8bc3129796e2906cff2070
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441032"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Wprowadzenie do Azure Cost Management dla partnerów

Azure Cost Management jest natywnie dostępna dla partnerów, którzy zostali dołączeni do umowy klienta firmy Microsoft i [kupili plan platformy Azure](/partner-center/purchase-azure-plan). W tym artykule wyjaśniono, w jaki sposób partnerzy używają [Azure Cost Management](index.yml) funkcji do wyświetlania kosztów subskrypcji w planie platformy Azure. Opisano w nim również, jak partnerzy zapewniają klientom Cost Management dostęp do nich. Klienci mogą korzystać z funkcji Cost Management, jeśli są włączone przez partnera CSP.

Partnerzy programu CSP używają Cost Management do:

- Poznanie kosztów zafakturowanych i kojarzenie kosztów z klientami, subskrypcjami, grupami zasobów i usługami.
- Uzyskaj intuicyjny wgląd w koszty platformy Azure w [analizie kosztów](quick-acm-cost-analysis.md) dzięki możliwościom analizowania kosztów według klientów, subskrypcji, grupy zasobów, zasobu, licznika, usługi i wielu innych wymiarów.
- Wyświetlanie kosztów zasobów, w przypadku których uzyskano środki na rzecz partnerów (PEC) stosowane w analizie kosztów.
- Skonfiguruj powiadomienia i automatyzację przy użyciu [budżetów](tutorial-acm-create-budgets.md) programowych i alertów, gdy koszty przekraczają budżety.
- Włącz zasady Azure Resource Manager, które zapewniają klientom dostęp do Cost Management danych. Klienci mogą następnie wyświetlać dane dotyczące kosztów zużycia dla swoich subskrypcji [, używając stawek płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/calculator/)użyciem.
- Wyeksportuj dane dotyczące kosztów i użycia do obiektu blob magazynu z subskrypcją płatną zgodnie z rzeczywistym użyciem.

Oto przykład przedstawiający koszty dla wszystkich klientów.
![przykład pokazujący koszty dla wszystkich klientów](./media/get-started-partners/customer-costs1.png)

Oto przykład przedstawiający koszty jednego klienta.
![przykład pokazujący koszty jednego klienta](./media/get-started-partners/customer-costs2.png)

Wszystkie funkcje dostępne w Azure Cost Management są również dostępne w interfejsach API REST. Użyj interfejsów API do automatyzowania zadań zarządzania kosztami.

## <a name="prerequisites"></a>Wymagania wstępne

Jako partner Azure Cost Management jest natywnie dostępny tylko dla subskrypcji, które znajdują się w planie platformy Azure.

Aby włączyć Azure Cost Management w Azure Portal, należy potwierdzić zatwierdzenie przez klienta umowy klienta firmy Microsoft (w imieniu klienta) i przejściu klienta do planu platformy Azure. W Azure Cost Management są dostępne tylko koszty subskrypcji, które są przenoszone do planu platformy Azure.

Azure Cost Management wymaga dostępu do odczytu do konta rozliczeń lub subskrypcji.

Aby uzyskać więcej informacji na temat włączania i przypisywania dostępu do Azure Cost Management dla konta rozliczeniowego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview). Role **administratora globalnego** i **agenta administracyjnego** mogą zarządzać kosztami konta rozliczeniowego.

Aby uzyskać dostęp do Azure Cost Management w zakresie subskrypcji, każdy użytkownik z dostępem RBAC do subskrypcji może wyświetlać koszty według stawek detalicznych (płatność zgodnie z rzeczywistym użyciem). Jednak zasady widoczności kosztów dla dzierżawy klienta muszą być włączone. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [opis Cost Management danych](understand-cost-mgt-data.md).


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

W Azure Portal Zaloguj się do dzierżawy partnerskiej i wybierz pozycję **Cost Management + rozliczanie**. Wybierz konto rozliczeniowe, a następnie wybierz pozycję **Customers**. Lista klientów jest skojarzona z kontem rozliczeń.

Na liście klientów wybierz klienta, który ma być uprawniony do wyświetlania kosztów.

![Wybierz klientów w Cost Management](./media/get-started-partners/customer-list.png)

W obszarze **Ustawienia**wybierz pozycję **zasady**.

Bieżące zasady widoczności kosztów są wyświetlane dla opłat za **użycie platformy Azure** skojarzonych z subskrypcjami dla wybranego klienta.
Zasady ![, aby umożliwić klientom wyświetlanie opłat z płatnością zgodnie z rzeczywistym użyciem](./media/get-started-partners/cost-management-billing-policies.png)

Jeśli zasady są ustawione na wartość **nie**, Azure Cost Management nie są dostępne dla użytkowników subskrypcji skojarzonych z klientem. Zasady widoczności kosztów są domyślnie wyłączone dla wszystkich użytkowników subskrypcji, chyba że jest on włączony przez partnera.

Gdy dla zasad kosztów ustawiono wartość **tak**, użytkownicy subskrypcji powiązani z dzierżawcą mogą zobaczyć opłaty za użycie w ramach stawek płatności zgodnie z rzeczywistym użyciem.

Po włączeniu zasad widoczności kosztów wszystkie usługi zawierające subskrypcję pokazują koszty według stawek płatność zgodnie z rzeczywistym użyciem. Użycie zastrzeżenia ma wartość zero opłaty za rzeczywiste i amortyzowany koszt. Zakupy i uprawnienia nie są skojarzone z określoną subskrypcją. W związku z tym zakupy nie są wyświetlane w zakresie subskrypcji.

Aby wyświetlić koszty dla dzierżawy klienta, Otwórz Cost Management + rozliczenia, a następnie wybierz pozycję konta rozliczeń. Na liście kont rozliczeń wybierz konto rozliczeniowe.

![Wybierz konto rozliczeniowe](./media/get-started-partners/select-billing-account.png)

W obszarze **rozliczenia**wybierz pozycję **subskrypcje platformy Azure**, a następnie wybierz klienta.

![Wybierz klienta subskrypcji platformy Azure](./media/get-started-partners/subscriptions-select-customer.png)

Wybierz pozycję **Analiza kosztów** i Rozpocznij przeglądanie kosztów.
Analiza kosztów, budżety i alerty są dostępne dla zakresów RBAC subskrypcji i grupy zasobów w ramach kosztów według stawki płatność zgodnie z rzeczywistym użyciem.

![Wyświetlanie analizy kosztów jako klienta ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Widoki amortyzowane i rzeczywiste koszty wystąpień zarezerwowanych w zakresach RBAC pokazują Zero opłat. Koszty wystąpień zarezerwowanych są wyświetlane tylko w zakresach rozliczeniowych, w których dokonano zakupów.

## <a name="analyze-costs-in-cost-analysis"></a>Analizowanie kosztów w analizie kosztów

Partnerzy z dostępem do zakresów rozliczeń w dzierżawie partnera mogą eksplorować i analizować koszty zafakturowane w analizie kosztów od klientów dla określonego klienta lub dla faktury. W widoku [Analiza kosztów](quick-acm-cost-analysis.md) można także [zapisywać widoki](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) i eksportować dane do [plików CSV i PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis).

Użytkownicy RBAC z dostępem do subskrypcji w dzierżawie klienta mogą również analizować koszty sprzedaży detalicznej dla subskrypcji w dzierżawie klienta, zapisywać widoki i eksportować dane do plików CSV i PNG.

W celu przeanalizowania kosztów według wielu pól można użyć funkcji filtrowania i grupowania przez funkcje w analizie kosztów. Pola specyficzne dla partnera są wyświetlane w następnej sekcji.

## <a name="data-fields"></a>Pola danych

Następujące pola danych znajdują się w plikach szczegółów użycia i interfejsów API Cost Management. Jeśli jest dostępna, są wyświetlane równoważne informacje Centrum partnerskiego. W przypadku następujących pogrubionych pól partnerzy mogą używać funkcji filtrowania i grupowania przez funkcje w analizie kosztów, aby analizować koszty według wielu pól. Pola pogrubione dotyczą tylko umów klienta firmy Microsoft obsługiwanych przez partnerów.

| **Nazwa pola** | **Opis** | **Odpowiednik Centrum partnerskiego** |
| --- | --- | --- |
| invoiceId | Identyfikator faktury wyświetlany na fakturze dla określonej transakcji. | Numer faktury, w której jest pokazywana transakcja. |
| previousInvoiceID | Odwołanie do oryginalnej faktury ma zwrot (koszt ujemny). Wypełniane tylko wtedy, gdy istnieje zwrot. | ND |
| billingAccountName | Nazwa konta rozliczeniowego reprezentującego partnera. Naliczane są wszystkie koszty dla klientów, którzy zostali dołączeni do umowy klienta firmy Microsoft i klientów dostawcy usług kryptograficznych, którzy mieli uprawnienia do zakupów takich jak SaaS, Azure Marketplace i rezerwacje. | ND |
| billingAccountID | Identyfikator konta rozliczeniowego reprezentujący partnera. | Identyfikator głównego elementu Commerce partnera MCAPI. Używane w żądaniu, ale nie zawarte w odpowiedzi.|
| billingProfileID | Identyfikator profilu rozliczeń, który grupuje koszty między faktury w ramach pojedynczej waluty rozliczeniowej dla klientów, którzy zostali dołączeni do umowy klienta firmy Microsoft i klientów z dostawcami usług kryptograficznych, którzy dokonywali zakupów, takich jak SaaS, Azure Marketplace i dokonując. | Identyfikator grupy rozliczeń partnera MCAPI. Używane w żądaniu, ale nie zawarte w odpowiedzi. |
| billingProfileName | Nazwa profilu rozliczeń, który grupuje koszty między faktury w ramach pojedynczej waluty rozliczeniowej dla klientów, którzy zostali dołączeni do umowy klienta firmy Microsoft i klientów korzystających z usług dostawcy CSP, którzy dokonywali zakupów, takich jak SaaS, Azure Marketplace i dokonując. | ND |
| invoiceSectionName | Nazwa projektu, który jest rozliczany na podstawie faktury. Nie dotyczy umów klienta firmy Microsoft dołączonych przez partnerów. | ND |
| invoiceSectionID | Identyfikator projektu, który jest rozliczany na podstawie faktury. Nie dotyczy umów klienta firmy Microsoft dołączonych przez partnerów. | ND |
| **CustomerTenantID** | Identyfikator dzierżawy Azure Active Directory subskrypcji klienta. | Identyfikator organizacyjny klienta — Azure Active Directory klienta TenantID. |
| **CustomerName** | Nazwa dzierżawy usługi Azure Active Directory dla subskrypcji klienta. | Nazwa organizacji klienta, jak pokazano w centrum partnerskim. Ważne w przypadku uzgadniania faktury z informacjami o systemie. |
| **CustomerTenantDomainName** | Nazwa domeny dla dzierżawy Azure Active Directory subskrypcji klienta. | Klient Azure Active Directory domeny dzierżawcy. |
| **PartnerTenantID** | Identyfikator dzierżawcy Azure Active Directory partnera. | Partner Azure Active Directory identyfikator dzierżawcy, który został wywołany jako identyfikator partnera w formacie identyfikatora GUID. |
| **PartnerName** | Nazwa partnera Azure Active Directory dzierżawy. | Nazwa partnera. |
| **ResellerMPNID** | MPNID dla odsprzedawcy skojarzonego z subskrypcją. | IDENTYFIKATOR MPN odsprzedawcy w ramach rekordu dla subskrypcji. Niedostępne dla bieżącego działania. |
| costCenter | Centrum kosztów skojarzone z subskrypcją. | ND |
| billingPeriodStartDate | Data rozpoczęcia okresu rozliczeniowego, jak pokazano na fakturze. | ND |
| billingPeriodEndDate | Data końcowa okresu rozliczeniowego, jak pokazano na fakturze. | ND |
| servicePeriodStartDate | Data rozpoczęcia okresu oceny, gdy użycie usługi zostało ocenione pod kątem opłat. Ceny usług platformy Azure są określane na okres oceniania. | ChargeStartDate w centrum partnerskim. Data rozpoczęcia cyklu rozliczania, z wyjątkiem przypadków, gdy daty są przedstawiane wcześniej nieodpłatne ukryte dane użycia z poprzedniego okresu rozliczeniowego. Czas jest zawsze początku dnia, 0:00. |
| servicePeriodEndDate | Data zakończenia okresu, w którym oceniono użycie usługi pod kątem opłat. Ceny usług platformy Azure są ustalane na podstawie okresu rankingu. | ND |
| date | W przypadku danych dotyczących zużycia na platformie Azure pokazuje datę użycia jako sklasyfikowaną. W przypadku wystąpienia zarezerwowanego jest wyświetlana data zakupu. W przypadku naliczania opłat cyklicznych i jednorazowych opłat, takich jak Marketplace i pomoc techniczna, jest wyświetlana data zakupu. | ND |
| Produktu | Identyfikator produktu, który ma naliczone opłaty według zużycia lub zakupu. Jest to połączenie klucza productID i identyfikatora skuId, jak pokazano w centrum partnerskim. | Identyfikator produktu. |
| product | Nazwa produktu, który ma naliczone opłaty według zużycia lub zakupu, jak pokazano na fakturze. | Nazwa produktu w wykazie. |
| serviceFamily | Pokazuje rodzinę usług zakupionych lub naliczanych produktów. Na przykład Storage lub COMPUTE. | ND |
| productOrderID | Identyfikator elementu zawartości lub nazwy planu platformy Azure, do którego należy subskrypcja. Na przykład usługa Azure plan. | ND |
| productOrderName | Nazwa planu platformy Azure, do którego należy subskrypcja. Na przykład usługa Azure plan. | ND|
| consumedService | Użycie usługi (starsza taksonomia) w ramach szczegółowych informacji o użyciu starszych wersji EA. | Usługa wyświetlana w centrum partnerskim. Na przykład Microsoft. Storage, Microsoft. COMPUTE i Microsoft. operationalinsights. |
| meterID | Mierzony identyfikator dla zmierzonego zużycia. | Identyfikator używanego licznika. |
| meterName | Określa nazwę miernika do zmierzonego zużycia. | Nazwa zużytego miernika. |
| meterCategory | Określa usługę najwyższego poziomu do użycia. | Usługa najwyższego poziomu do użycia. |
| meterSubCategory | Definiuje typ lub podkategorię usługi platformy Azure, która może wpływać na stawkę. | Typ usługi platformy Azure, który może mieć wpływ na stawkę.|
| meterRegion | Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych. | Regionalna Lokalizacja centrum danych dla usług, jeśli ma to zastosowanie i wypełnione. |
| subscription ID | Unikatowy identyfikator wygenerowany przez firmę Microsoft dla subskrypcji platformy Azure. | ND |
| subscriptionName | Nazwa subskrypcji platformy Azure. | ND |
| Okres obowiązywania Umowy | Przedstawia okres ważności oferty. Na przykład wystąpienia zarezerwowane pokazują 12 miesięcy rocznego warunku wystąpienia zarezerwowanego. W przypadku jednorazowych zakupów lub cyklicznych zakupów termin wyświetla jeden miesiąc dla SaaS, Azure Marketplace i pomocy technicznej. Nie dotyczy użycia platformy Azure. | ND |
| PublisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ wydawcy, który identyfikuje wydawcę jako pierwszej strony, odsprzedawcę lub inną osobę. | ND |
| partNumber | Numer części nieużywanych wystąpień zarezerwowanych i usług Azure Marketplace. | ND |
| publisherName | Nazwa wydawcy usługi, w tym wydawcy firmy Microsoft lub innych firm. | Nazwa wydawcy produktu.|
| reservationId | Identyfikator zakupu wystąpienia zarezerwowanego. | ND |
| reservationName | Nazwa wystąpienia zarezerwowanego. | ND |
| reservationOrderId | Identyfikator IDZamówienia dla wystąpienia zarezerwowanego. | ND |
| frequency | Częstotliwość płatności dla wystąpienia zarezerwowanego. | ND |
| resourceGroup | Nazwa grupy zasobów platformy Azure używanej do zarządzania zasobami cyklu życia. | Nazwa grupy zasobów. |
| Identyfikator instanceID (lub) ResourceID | Identyfikator wystąpienia zasobu. | Przedstawiane jako ResourceURI, które obejmują pełne właściwości zasobów. |
| resourceLocation | Nazwa lokalizacji zasobu. | Lokalizacja zasobu. |
| Lokalizacja | Znormalizowana lokalizacja zasobu. | ND |
| effectivePrice | Obowiązująca cena jednostkowa usługi w walucie cenowej. Unikatowy dla produktu, rodziny usług, miernika i oferty. Używany z cenami w arkuszu cen dla konta rozliczeniowego. W przypadku cen warstwowych lub uwzględnionych ilości zostanie wyświetlona połączona cena zużycia. | Cena jednostkowa po dokonaniu korekt. |
| Ilość | Zmierzona ilość zakupione lub zużyte. Ilość licznika użyta w okresie rozliczeniowym. | Liczba jednostek. Upewnij się, że jest ona zgodna z informacjami w systemie rozliczeniowym podczas uzgadniania. |
| unitOfMeasure | Identyfikuje jednostkę, w której jest naliczana opłata za usługę. Na przykład GB i godziny. | Identyfikuje jednostkę, w której jest naliczana opłata za usługę. Na przykład GB, godzin i 10, 000. |
| pricingCurrency | Waluta określająca cenę jednostkową. | Waluta na liście cen.|
| billingCurrency | Waluta definiująca koszt naliczany. | Waluta regionu geograficznego klienta. |
| chargeType | Określa typ opłaty reprezentowanej przez koszt Azure Cost Management, na przykład zakup i zwrot. | Typ opłaty lub korekty. Niedostępne dla bieżącego działania. |
| costinBillingCurrency | Koszt rozszerzony lub zmieszany koszt przed opodatkowaniem w walucie rozliczeniowej. | ND |
| costinPricingCurrency | Koszt rozszerzony lub zmieszany koszt przed opodatkowaniem w walucie cenowej w celu skorelowania cen. | ND |
| **costinUSD** | Szacowany koszt koszt rozszerzony lub mieszany przed opodatkowaniem w USD. | ND |
| **paygCostInBillingCurrency** | Pokazuje koszty w przypadku cen detalicznych. W walucie rozliczeniowej są wyświetlane ceny płatności zgodnie z rzeczywistym użyciem. Dostępne tylko w zakresach RBAC. | ND |
| **paygCostInUSD** | Pokazuje koszty w przypadku cen detalicznych. W USD są wyświetlane ceny z opcją płatność zgodnie z rzeczywistym użyciem. Dostępne tylko w zakresach RBAC. | ND |
| exchangeRate | Kurs wymiany używany do konwersji z waluty cenowej na walutę rozliczeń. | Określany jako PCToBCExchangeRate w centrum partnerskim. Waluta cennika dla kursu wymiany waluty.|
| exchangeRateDate | Data kursu wymiany używanego do konwersji z waluty cenowej na walutę rozliczeń. | Określany jako PCToBCExchangeRateDat w centrum partnerskim. Waluta cennika na datę kursu wymiany waluty.|
| isAzureCreditEligible | Wskazuje, czy koszt jest uprawniony do płatności za środki na korzystanie z platformy Azure. | ND |
| serviceInfo1 | Starsze pole, które przechwytuje opcjonalne metadane specyficzne dla usługi. | Wewnętrzne metadane usługi platformy Azure. |
| serviceInfo2 | Starsze pole, które przechwytuje opcjonalne metadane specyficzne dla usługi. | Informacje o usłudze. Na przykład typ obrazu dla maszyny wirtualnej i nazwy usługodawcy internetowego dla ExpressRoute.|
| additionalInfo | Metadane dotyczące konkretnej usługi. Na przykład typ obrazu dla maszyny wirtualnej. | Wszelkie dodatkowe informacje, które nie są objęte innymi kolumnami. Metadane specyficzne dla usługi. Na przykład typ obrazu dla maszyny wirtualnej.|
| tags | Tag przypisany do miernika. Grupowanie rekordów rozliczeń przy użyciu tagów. Można na przykład użyć tagów do dystrybucji kosztów według działu, który używa miernika. | Tagi dodane przez klienta.|
| **partnerEarnedCreditRate** | Kwota rabatu stosowana w przypadku, gdy Partner uzyskał kredyt (PEC) w oparciu o dostęp do linku administratora partnera. | Stawka w wysokości (PEC) uzyskana przez partnera. Na przykład 0% lub 15%. |
| **partnerEarnedCreditApplied** | Wskazuje, czy jest stosowany kredyt uzyskany przez partnera. | ND |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Wyświetlanie kosztów zasobów dla partnerów (PEC)

W Azure Cost Management partnerzy mogą korzystać z analizy kosztów, aby wyświetlać koszty, które otrzymały zalety PEC.

W Azure Portal Zaloguj się do dzierżawy partnerskiej i wybierz pozycję **Cost Management + rozliczanie**. W obszarze **Cost Management**wybierz pozycję **Analiza kosztów**.

W widoku Analiza kosztów są wyświetlane koszty konta rozliczeniowego dla partnera. Wybierz **zakres** wymagany dla partnera, określonego klienta lub profilu rozliczeń, aby uzgodnić faktury.

Na wykresie pierścieniowym wybierz listę rozwijaną i wybierz pozycję **PartnerEarnedCreditApplied** , aby przejść do szczegółów dotyczących kosztów Pec.

![Przykład przedstawiający sposób wyświetlania środków uzyskanych przez partnera](./media/get-started-partners/cost-analysis-pec1.png)

Gdy właściwość **PartnerEarnedCreditApplied** ma _wartość true_, skojarzony koszt ma korzyść dostęp administratora przez partnera.

Gdy właściwość **PartnerEarnedCreditApplied** ma _wartość false_, skojarzony koszt nie spełnia wymagań wymaganych do uznania. Lub zakupiona usługa nie kwalifikuje się do uzyskania kredytu dla partnerów.

Dane użycia usługi zwykle zajmują 8-24 godzin, aby były wyświetlane w Cost Management. Aby uzyskać więcej informacji, zobacz [częstotliwość aktualizacji danych użycia jest różna](understand-cost-mgt-data.md#usage-data-update-frequency-varies). Kredyty PEC są wyświetlane w ciągu 48 godzin od momentu uzyskania dostępu w Azure Cost Management.


Można również grupować i filtrować według właściwości **PartnerEarnedCreditApplied** przy użyciu opcji **Grupuj według** . Użyj opcji, aby przejrzeć koszty, które nie mają PEC.

![Grupuj lub Filtruj według środków w ramach partnera](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Eksportowanie danych kosztów do usługi Azure Storage

Partnerzy z dostępem do zakresów rozliczeń w dzierżawie partnerskim mogą eksportować dane dotyczące kosztów i użycia do obiektu BLOB usługi Azure Storage. Obiekt BLOB musi znajdować się w subskrypcji dzierżawcy partnera, która nie jest subskrypcją usługi udostępnionej ani subskrypcją klienta. Aby włączyć eksportowanie danych kosztów, zalecamy skonfigurowanie niezależnej subskrypcji płatnej zgodnie z rzeczywistym użyciem w dzierżawie partnera w celu hostowania wyeksportowanych danych kosztów. Konto eksportu magazynu jest tworzone w usłudze Azure Storage BLOB hostowanej w ramach subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem. Na podstawie zakresu, w którym partner tworzy eksport, skojarzone dane są automatycznie eksportowane do konta magazynu cyklicznie.

Użytkownicy z dostępem RBAC do subskrypcji mogą również eksportować dane kosztów do obiektu BLOB usługi Azure Storage hostowanego w dowolnej subskrypcji w dzierżawie klienta.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Tworzenie eksportu w dzierżawie partnerskiej lub dzierżawie klienta

W Azure Portal Zaloguj się do dzierżawcy partnera lub dzierżawy klienta i wybierz pozycję **Cost Management + rozliczanie**. Wybierz odpowiedni zakres, na przykład konto rozliczeniowe, a następnie wybierz pozycję **Analiza kosztów**. Po załadowaniu strony wybierz pozycję **Eksportuj**. Wybierz pozycję **Wyświetl wszystkie eksporty** w obszarze Zaplanuj eksport.

Następnie wybierz pozycję **Dodaj** i wpisz nazwę i wybierz typ eksportu. Wybierz kartę **Magazyn** i wprowadź wymagane informacje.

Po utworzeniu eksportu w dzierżawie partnera wybierz subskrypcję z płatność zgodnie z rzeczywistym użyciem w dzierżawie partnera. Utwórz konto usługi Azure Storage przy użyciu tej subskrypcji.

W przypadku użytkowników RBAC w dzierżawie klienta wybierz subskrypcję w dzierżawie klienta. Utwórz konto usługi Azure Storage przy użyciu subskrypcji.

Przejrzyj zawartość, a następnie wybierz pozycję **Utwórz** , aby zaplanować eksport.

Aby sprawdzić dane na liście eksportu, wybierz nazwę konta magazynu. Na stronie konto magazynu wybierz pozycję **kontenery** , a następnie wybierz kontener. Przejdź do odpowiedniego folderu i wybierz plik CSV. Wybierz pozycję **Pobierz** , aby pobrać plik CSV i otworzyć go. Eksportowane dane eksportowane przypominają dane dotyczące kosztów podobne do szczegółów użycia z Azure Portal.

## <a name="cost-management-rest-apis"></a>Interfejsy API REST Cost Management

Partnerzy i klienci mogą używać Cost Management interfejsów API opisanych w poniższych sekcjach dla typowych zadań.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Interfejsy API Azure Cost Management — bezpośredni i pośredni dostawca

Partnerzy z dostępem do zakresów rozliczeń w dzierżawie partnerskim mogą używać następujących interfejsów API do wyświetlania zafakturowanych kosztów.

Interfejsy API w zakresie subskrypcji mogą być wywoływane przez partnera niezależnie od zasad kosztów, jeśli mają dostęp do subskrypcji. Inni użytkownicy z dostępem do subskrypcji, takie jak klient lub odsprzedawca, mogą wywoływać interfejsy API tylko po włączeniu przez partnera zasad kosztów dla dzierżawy klienta.


#### <a name="to-get-a-list-of-billing-accounts"></a>Aby uzyskać listę kont rozliczeniowych

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Aby uzyskać listę klientów

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Aby uzyskać listę subskrypcji

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Aby uzyskać listę faktur w danym okresie czasu

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

Wywołanie interfejsu API zwraca tablicę faktur, które mają elementy podobne do poniższego kodu JSON.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Użyj poprzedniej zwróconej wartości pola ID i Zastąp ją w poniższym przykładzie jako zakres zapytania o szczegóły użycia.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

Przykład zwraca rekordy użycia skojarzone z określoną fakturą.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Aby uzyskać zasady dotyczące wyświetlania kosztów przez klientów

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Aby ustawić zasady dla klientów do wyświetlania kosztów

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Aby uzyskać użycie usługi platformy Azure dla konta rozliczeniowego

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Aby pobrać użycie usługi platformy Azure przez klienta

Następujące wywołanie get jest operacją asynchroniczną.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Wywołaj identyfikator URI `Location` zwrócony w odpowiedzi, aby sprawdzić stan operacji. Po *zakończeniu*stanu Właściwość `downloadUrl` zawiera link, którego można użyć do pobrania wygenerowanego raportu.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Aby pobrać lub pobrać arkusz cen dla zużytych usług platformy Azure

Najpierw użyj poniższego wpisu.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Następnie Wywołaj wartość właściwości operacji asynchronicznej. Przykład:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Poprzednie wywołanie Get zwraca łącze pobierania zawierające arkusz cen.


#### <a name="to-get-aggregated-costs"></a>Aby uzyskać zagregowane koszty

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Tworzenie budżetu dla partnera

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Tworzenie budżetu dla klienta

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Usuń budżet

```
PUT
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Następne kroki
- [Rozpocznij analizowanie kosztów](quick-acm-cost-analysis.md) w Cost Management
- [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md) w Cost Management
