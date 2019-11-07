---
title: Informacje o danych Azure Cost Management | Microsoft Docs
description: Ten artykuł pomaga lepiej zrozumieć dane zawarte w Azure Cost Management oraz częstotliwość przetwarzania, zbierania, wyświetlania i zamykania.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721369"
---
# <a name="understand-cost-management-data"></a>Omówienie danych usługi Cost Management

Ten artykuł pomaga lepiej zrozumieć dane dotyczące kosztów i użycia platformy Azure, które znajdują się w Azure Cost Management. Wyjaśniono, jak często dane są przetwarzane, zbierane, wyświetlane i zamykane. Opłaty są naliczane miesięcznie za użycie platformy Azure. Mimo że cykle rozliczeń to okresy miesięczne, daty rozpoczęcia i zakończenia cyklu różnią się w zależności od typu subskrypcji. Częstotliwość, z jaką Cost Management odbiera dane użycia, różnią się w zależności od różnych czynników. Takie czynniki obejmują czas potrzebny na przetworzenie danych oraz częstotliwość, z jaką usługi platformy Azure emitują użycie do systemu rozliczeń.

Cost Management obejmuje użycie i zakupy, w tym rezerwacje i oferty innych firm dla kont Umowa Enterprise (EA). Konta umów dla klientów firmy Microsoft i poszczególnych subskrypcji z użyciem stawek płatność zgodnie z rzeczywistym użyciem obejmują wyłącznie użycie z usług Azure i Marketplace. Pomoc techniczna i inne koszty nie są uwzględniane. Koszty są szacowane do momentu wygenerowania faktury i nie są one uwzględniane w kredytach.

## <a name="supported-microsoft-azure-offers"></a>Obsługiwane Microsoft Azure oferty

Poniższe informacje przedstawiają obecnie obsługiwane [oferty Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) w Azure Cost Management. Oferta platformy Azure to typ posiadanej subskrypcji platformy Azure. Dane są dostępne w Cost Management rozpoczynające się od **danych dostępnych od** daty. Jeśli subskrypcja zmieni oferty, koszty przed zmianą oferty nie będą dostępne.

| **Kategoria**  | **Nazwa oferty** | **Identyfikator limitu przydziału** | **Numer oferty** | **Dane dostępne z** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 maja<sup>1</sup> |
| **Umowa Enterprise (EA)** | Enterprise — tworzenie i testowanie                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 maja<sup>1</sup> |
| **Umowa Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 maja<sup>1</sup> |
| **Umowa klienta firmy Microsoft** | [Plan Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Nie dotyczy | Marzec 2019<sup>3</sup> |
| **Umowa klienta firmy Microsoft** | [Plan Microsoft Azure dla tworzenia i testowania](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Nie dotyczy | Marzec 2019<sup>3</sup> |
| **Umowa klienta firmy Microsoft obsługiwana przez partnerów** | Plan platformy Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 i CSPDEVTEST_2018-05-01<br><br>Identyfikator przydziału jest ponownie używany dla umowy klienta firmy Microsoft i starszych subskrypcji CSP. Obecnie obsługiwane są tylko subskrypcje umów dla klientów firmy Microsoft. | Nie dotyczy | Październik 2019 r. |
| **Microsoft Developer Network (MSDN)** | [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 października, 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 października, 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 października, 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 października, 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 października, 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Platforma Azure w ramach programu Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 października, 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | Azure — dostęp próbny<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P-MS-AZR-0125P, MS-AZR-0128P-MS-AZR-0130P | 2 października, 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 października, 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 października, 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 października, 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 października, 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 października, 2018<sup>2</sup> |

_<sup>**1**</sup> w przypadku danych przed 2014 maja odwiedź witrynę [Azure Enterprise Portal](https://ea.azure.com)._

_<sup>**2**</sup> w przypadku danych przed 2 października 2018, odwiedź [centrum konta platformy Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> umowy klienta firmy Microsoft rozpoczęły się w marcu 2019 i nie mają żadnych danych historycznych przed tym punktem._

_<sup>**4**</sup> dane historyczne dla subskrypcji płatnych za pośrednictwem środków i płacy mogą nie być zgodne z fakturą. Zobacz, jak [dane historyczne mogą nie być zgodne](#historical-data-might-not-match-invoice) z poniższą fakturą._

Następujące oferty nie są jeszcze obsługiwane:

| Kategoria  | **Nazwa oferty** | **Identyfikator limitu przydziału** | **Numer oferty** |
| --- | --- | --- | --- |
| **Azure (Niemcy)** | [Azure (Niemcy) — płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Dostawca rozwiązań w chmurze (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Azure Government dostawcę usług kryptograficznych                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Platforma Azure (Niemcy) w programie CSP dla usługi Microsoft Cloud (Niemcy)   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Płatność zgodnie z rzeczywistym użyciem**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Azure dla studentów](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Płatność zgodnie z rzeczywistym użyciem**                 | [Dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plany pomocy technicznej** | Pomoc techniczna Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plany pomocy technicznej** | Wsparcie dla deweloperów                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plany pomocy technicznej** | Plan pomocy technicznej (Niemcy)                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plany pomocy technicznej** | Azure Government pomoc techniczna Standard   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plany pomocy technicznej** | Azure Government pomoc techniczna Pro-Direct | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plany pomocy technicznej** | Azure Government pomoc techniczna Developer  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Określanie typu oferty
Jeśli nie widzisz danych dla subskrypcji i chcesz określić, czy subskrypcja jest objęta obsługiwanymi ofertami, możesz sprawdzić, czy subskrypcja jest obsługiwana. Aby sprawdzić, czy subskrypcja platformy Azure jest obsługiwana, zaloguj się do [Azure Portal](https://portal.azure.com). Następnie w okienku menu po lewej stronie wybierz pozycję **wszystkie usługi** . Na liście usług wybierz pozycję **subskrypcje**. W menu listy subskrypcji kliknij subskrypcję, którą chcesz zweryfikować. Twoja subskrypcja zostanie wyświetlona na karcie Przegląd i zostanie wyświetlona **Oferta** i **Identyfikator oferty**. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład karty Przegląd subskrypcji z ofertą i IDENTYFIKATORem oferty](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Koszty zawarte w Cost Management

W poniższych tabelach przedstawiono dane, które są uwzględnione lub nie znajdują się w Cost Management. Wszystkie koszty są szacowane do momentu wygenerowania faktury. Pokazane koszty nie obejmują kredytów bezpłatnych i przedpłaconych.

**Dane dotyczące kosztów i użycia**

| **Uwzględnione** | **Nie uwzględniono** |
| --- | --- |
| Użycie usługi platformy Azure<sup>5</sup>        | Opłaty za pomoc techniczną — Aby uzyskać więcej informacji, zobacz [postanowienia dotyczące faktury](../billing/billing-understand-your-invoice.md). |
| Użycie Oferty Marketplace<sup>6</sup> | Podatki — Aby uzyskać więcej informacji, zobacz [wyjaśnienia dotyczące faktur](../billing/billing-understand-your-invoice.md). |
| Zakupy w portalu Marketplace<sup>6</sup>      | Kredyty — Aby uzyskać więcej informacji, zobacz [wyjaśnienia dotyczące faktur](../billing/billing-understand-your-invoice.md). |
| Rezerwacja zakupów<sup>7</sup>      |  |
| Umorzenie zakupów rezerwacji<sup>7</sup>      |  |

_<sup>**5**</sup> użycie usługi Azure jest oparte na rezerwacji i cenach negocjowanych._

_<sup>**6**</sup> zakupów w portalu Marketplace nie jest dostępnych do oferty płatność zgodnie z rzeczywistym użyciem, MSDN i Visual Studio._

_<sup>**7**</sup> zakupy rezerwacji są dostępne tylko dla kont usługi Umowa Enterprise (EA)._

**Metadane**

| **Uwzględnione** | **Nie uwzględniono** |
| --- | --- |
| Tagi zasobów<sup>8</sup> | Tagi grupy zasobów |

_<sup>**8**</sup> tagów zasobów są stosowane, ponieważ użycie jest emitowane z każdej usługi i nie jest dostępne z mocą wsteczną do historycznego użycia._

## <a name="rated-usage-data-refresh-schedule"></a>Harmonogram odświeżania danych użycia

Dane dotyczące kosztów i użycia są dostępne w Cost Management + rozliczanie w Azure Portal i [Obsługa interfejsów API](index.yml). Podczas przeglądania kosztów należy pamiętać o następujących kwestiach:

- Szacowane opłaty za bieżący okres rozliczeniowy są aktualizowane sześć razy dziennie.
- Szacowane opłaty za bieżący okres rozliczeniowy mogą ulec zmianie w miarę ponoszenia większej ilości użycia.
- Każda aktualizacja jest zbiorcza i zawiera wszystkie elementy wiersza i informacje z poprzedniej aktualizacji.
- Platforma Azure kończy lub _zamyka_ bieżący okres rozliczeniowy do 72 godzin (trzy dni kalendarzowe) po zakończeniu okresu rozliczeniowego.

W poniższych przykładach pokazano, jak można zakończyć okresy rozliczeniowe.

Subskrypcje Umowa Enterprise (EA) — Jeśli miesiąc rozliczeniowy upływa 31 marca, szacowane opłaty są aktualizowane do 72 godzin później. W tym przykładzie o północy (UTC) 4 kwietnia.

Subskrypcje z płatnością zgodnie z rzeczywistym użyciem — Jeśli miesiąc rozliczeniowy upływa 15 maja, szacowane opłaty mogą zostać zaktualizowane do 72 godzin później. W tym przykładzie o północy (UTC) 19 maja.

### <a name="rerated-data"></a>Dane o obniżonej wartości

Niezależnie od tego, czy używasz [Cost Management interfejsów API](index.yml), Power BI czy Azure Portal do pobierania danych, należy oczekiwać, że opłaty za bieżący okres rozliczeniowy zostaną naliczone proporcjonalnie do momentu zamknięcia faktury.

## <a name="cost-management-data-fields"></a>Cost Management pól danych

Następujące pola danych znajdują się w plikach szczegółów użycia i interfejsów API Cost Management. W przypadku następujących pogrubionych pól partnerzy mogą używać funkcji filtrowania i grupowania przez funkcje w analizie kosztów, aby analizować koszty według wielu pól. Pola pogrubione dotyczą tylko umów klienta firmy Microsoft obsługiwanych przez partnerów.

| **Nazwa pola** | **Opis** |
| --- | --- |
| invoiceId | Identyfikator faktury wyświetlany na fakturze dla określonej transakcji. |
| previousInvoiceID | Odwołanie do oryginalnej faktury ma zwrot (koszt ujemny). Wypełniane tylko wtedy, gdy istnieje zwrot. |
| billingAccountName | Nazwa konta rozliczeniowego reprezentującego partnera. Naliczane są wszystkie koszty dla klientów, którzy zostali dołączeni do umowy klienta firmy Microsoft i klientów dostawcy usług kryptograficznych, którzy mieli uprawnienia do zakupów takich jak SaaS, Azure Marketplace i rezerwacje. |
| billingAccountID | Identyfikator konta rozliczeniowego reprezentujący partnera. |
| billingProfileID | Identyfikator profilu rozliczeń, który grupuje koszty między faktury w ramach pojedynczej waluty rozliczeniowej dla klientów, którzy zostali dołączeni do umowy klienta firmy Microsoft i klientów z dostawcami usług kryptograficznych, którzy dokonywali zakupów, takich jak SaaS, Azure Marketplace i dokonując. |
| billingProfileName | Nazwa profilu rozliczeń, który grupuje koszty między faktury w ramach pojedynczej waluty rozliczeniowej dla klientów, którzy zostali dołączeni do umowy klienta firmy Microsoft i klientów korzystających z usług dostawcy CSP, którzy dokonywali zakupów, takich jak SaaS, Azure Marketplace i dokonując. |
| invoiceSectionName | Nazwa projektu, który jest rozliczany na podstawie faktury. Nie dotyczy umów klienta firmy Microsoft dołączonych przez partnerów. |
| invoiceSectionID | Identyfikator projektu, który jest rozliczany na podstawie faktury. Nie dotyczy umów klienta firmy Microsoft dołączonych przez partnerów. |
| **CustomerTenantID** | Identyfikator dzierżawy Azure Active Directory subskrypcji klienta&#39;. |
| **CustomerName** | Nazwa dzierżawy usługi Azure Active Directory dla subskrypcji klienta&#39;s. |
| **CustomerTenantDomainName** | Nazwa domeny dla dzierżawy Azure Active Directory subskrypcji klienta&#39;. |
| **PartnerTenantID** | Identyfikator dzierżawy Azure Active Directory&#39;partnera. |
| **PartnerName** | Nazwa partnera Azure Active Directory dzierżawy. |
| **ResellerMPNID** | MPNID dla odsprzedawcy skojarzonego z subskrypcją. |
| costCenter | Centrum kosztów skojarzone z subskrypcją. |
| billingPeriodStartDate | Data rozpoczęcia okresu rozliczeniowego, jak pokazano na fakturze. |
| billingPeriodEndDate | Data końcowa okresu rozliczeniowego, jak pokazano na fakturze. |
| servicePeriodStartDate | Data rozpoczęcia okresu oceny, gdy użycie usługi zostało ocenione pod kątem opłat. Ceny usług platformy Azure są określane na okres oceniania. |
| servicePeriodEndDate | Data zakończenia okresu, w którym oceniono użycie usługi pod kątem opłat. Ceny usług platformy Azure są ustalane na podstawie okresu rankingu. |
| date | W przypadku danych dotyczących zużycia na platformie Azure pokazuje datę użycia jako sklasyfikowaną. W przypadku wystąpienia zarezerwowanego jest wyświetlana data zakupu. W przypadku naliczania opłat cyklicznych i jednorazowych opłat, takich jak Marketplace i pomoc techniczna, jest wyświetlana data zakupu. |
| Produktu | Identyfikator produktu, który ma naliczone opłaty według zużycia lub zakupu. Jest to połączenie klucza productID i identyfikatora skuId, jak pokazano w centrum partnerskim. |
| product | Nazwa produktu, który ma naliczone opłaty według zużycia lub zakupu, jak pokazano na fakturze. |
| serviceFamily | Pokazuje rodzinę usług zakupionych lub naliczanych produktów. Na przykład Storage lub COMPUTE. |
| productOrderID | Identyfikator elementu zawartości lub nazwy planu platformy Azure, do którego należy subskrypcja. Na przykład usługa Azure plan. |
| productOrderName | Nazwa planu platformy Azure, do którego należy subskrypcja. Na przykład usługa Azure plan. |
| consumedService | Użycie usługi (starsza taksonomia) w ramach szczegółowych informacji o użyciu starszych wersji EA. |
| meterID | Mierzony identyfikator dla zmierzonego zużycia. |
| meterName | Określa nazwę miernika do zmierzonego zużycia. |
| meterCategory | Określa usługę najwyższego poziomu do użycia. |
| meterSubCategory | Definiuje typ lub podkategorię usługi platformy Azure, która może wpływać na stawkę. |
| meterRegion | Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych. |
| subscription ID | Unikatowy identyfikator wygenerowany przez firmę Microsoft dla subskrypcji platformy Azure. |
| subscriptionName | Nazwa subskrypcji platformy Azure. |
| Termin | Przedstawia okres ważności oferty. Na przykład wystąpienia zarezerwowane pokazują 12 miesięcy rocznego warunku wystąpienia zarezerwowanego. W przypadku jednorazowych zakupów lub cyklicznych zakupów termin wyświetla jeden miesiąc dla SaaS, Azure Marketplace i pomocy technicznej. Nie dotyczy użycia platformy Azure. |
| PublisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ wydawcy, który identyfikuje wydawcę jako pierwszej strony, odsprzedawcę lub inną osobę. |
| PartNumber | Numer części nieużywanych wystąpień zarezerwowanych i usług Azure Marketplace. |
| publisherName | Nazwa wydawcy usługi, w tym wydawcy firmy Microsoft lub innych firm. |
| reservationId | Identyfikator zakupu wystąpienia zarezerwowanego. |
| reservationName | Nazwa wystąpienia zarezerwowanego. |
| reservationOrderId | Identyfikator IDZamówienia dla wystąpienia zarezerwowanego. |
| frequency | Częstotliwość płatności dla wystąpienia zarezerwowanego. |
| resourceGroup | Nazwa grupy zasobów platformy Azure używanej do zarządzania zasobami cyklu życia. |
| Identyfikator instanceID (lub) ResourceID | Identyfikator wystąpienia zasobu. |
| resourceLocation | Nazwa lokalizacji zasobu. |
| Lokalizacja | Znormalizowana lokalizacja zasobu. |
| effectivePrice | Obowiązująca cena jednostkowa usługi w walucie cenowej. Unikatowy dla produktu, rodziny usług, miernika i oferty. Używany z cenami w arkuszu cen dla konta rozliczeniowego. W przypadku cen warstwowych lub uwzględnionych ilości zostanie wyświetlona połączona cena zużycia. |
| Liczba | Zmierzona ilość zakupione lub zużyte. Ilość licznika użyta w okresie rozliczeniowym. |
| unitOfMeasure | Identyfikuje jednostkę, w której jest naliczana opłata za usługę. Na przykład GB i godziny. |
| pricingCurrency | Waluta określająca cenę jednostkową. |
| billingCurrency | Waluta definiująca koszt naliczany |
| chargeType | Określa typ opłaty reprezentowanej przez koszt Azure Cost Management, na przykład zakup i zwrot. |
| costinBillingCurrency | Koszt rozszerzony lub zmieszany koszt przed opodatkowaniem w walucie rozliczeniowej. |
| costinPricingCurrency | Koszt rozszerzony lub zmieszany koszt przed opodatkowaniem w walucie cenowej w celu skorelowania cen. |
| **costinUSD** | Szacowany koszt koszt rozszerzony lub mieszany przed opodatkowaniem w USD. |
| **paygCostInBillingCurrency** | Pokazuje koszty w przypadku cen detalicznych. W walucie rozliczeniowej są wyświetlane ceny płatności zgodnie z rzeczywistym użyciem. Dostępne tylko w zakresach RBAC. |
| **paygCostInUSD** | Pokazuje koszty w przypadku cen detalicznych. W USD są wyświetlane ceny z opcją płatność zgodnie z rzeczywistym użyciem. Dostępne tylko w zakresach RBAC. |
| exchangeRate | Kurs wymiany używany do konwersji z waluty cenowej na walutę rozliczeń. |
| exchangeRateDate | Data dla kursu&#39;wymiany używanego do konwersji z waluty cenowej na walutę rozliczeń. |
| isAzureCreditEligible | Wskazuje, czy koszt jest uprawniony do płatności za środki na korzystanie z platformy Azure. |
| serviceInfo1 | Starsze pole, które przechwytuje opcjonalne metadane specyficzne dla usługi. |
| serviceInfo2 | Starsze pole, które przechwytuje opcjonalne metadane specyficzne dla usługi. |
| additionalInfo | Metadane dotyczące konkretnej usługi. Na przykład typ obrazu dla maszyny wirtualnej. |
| tagów | Tag przypisany do miernika. Grupowanie rekordów rozliczeń przy użyciu tagów. Można na przykład użyć tagów do dystrybucji kosztów według działu, który używa miernika. |
| **partnerEarnedCreditRate** | Kwota rabatu stosowana w przypadku, gdy Partner uzyskał kredyt (PEC) w oparciu o dostęp do linku administratora partnera. |
| **partnerEarnedCreditApplied** | Wskazuje, czy jest stosowany kredyt uzyskany przez partnera. |


## <a name="usage-data-update-frequency-varies"></a>Częstotliwość aktualizacji danych użycia jest różna

Dostępność poniesionych danych użycia w Cost Management zależy od kilku czynników, w tym:

- Jak często usługi platformy Azure (takie jak Storage, COMPUTE, CDN i SQL) emitują użycie.
- Czas potrzebny na przetworzenie danych użycia za pomocą potoku aparatu klasyfikacji i zarządzania kosztami.

Niektóre usługi emitują użycie częściej niż inne. W związku z tym dane w Cost Management niektórych usług mogą być widoczne wcześniej niż w przypadku innych usług, które emitują dane rzadziej. Zazwyczaj użycie usług trwa 8-24 godzin, aby były wyświetlane w Cost Management. Należy pamiętać, że dane dla otwartego miesiąca są odświeżane, ponieważ nastąpi dalsze użycie, ponieważ aktualizacje są naliczane zbiorczo.

## <a name="historical-data-might-not-match-invoice"></a>Dane historyczne mogą nie być zgodne z fakturą

Dane historyczne dla ofert opartych na kredytach i płatnych z góry mogą nie być zgodne z fakturą. Niektóre oferty usługi Azure — płatność zgodnie z rzeczywistym użyciem, MSDN i Visual Studio mogą mieć środki na korzystanie z platformy Azure oraz zaawansowane płatności do faktury. Jednak dane historyczne podane w Cost Management opierają się wyłącznie na szacowanych kosztach zużycia. Cost Management dane historyczne nie obejmują płatności i kredytów. W związku z tym dane historyczne wyświetlane dla następujących ofert mogą nie być zgodne z fakturą.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure — dostęp próbny (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Bezpłatna wersja próbna (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Zobacz też

- Jeśli nie zostało to jeszcze zrobione z pierwszego przewodnika Szybki Start dla Cost Management, zapoznaj się z tematem [Rozpocznij analizowanie kosztów](quick-acm-cost-analysis.md).
