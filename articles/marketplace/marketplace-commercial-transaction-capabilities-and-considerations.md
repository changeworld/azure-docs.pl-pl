---
title: Możliwości i uwagi dotyczące transakcji komercyjnych w marketplace | Azure
description: W tym artykule opisano informacje o uwzględnianych cenach transakcji, rozliczeniach, fakturach i wypłatach dla typu oferty.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: dsindona
ms.openlocfilehash: de93a3c48e0b0aceb447e54e11190f487d1aa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279831"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Możliwości w zakresie transakcji komercyjnej platformy handlowej i kwestie do wzięcia pod uwagę

Ten artykuł obejmuje następujące tematy związane z handlem dla rynku komercyjnego

* Opcje publikowania w portalu Marketplace
* Przegląd ogólny transakcji
* Transact modele rozliczeń
* Wymogi dotyczące transakcji

## <a name="marketplace-publishing-options"></a>Opcje publikowania w portalu Marketplace

Następujące opcje publikowania są dostępne dla wydawców komercyjnych w portalu marketplace.

### <a name="list--trial-publishing-options"></a>Lista opcji publikowania & wersji próbnej

Wydawcy mogą korzystać z opcji publikowania listy, wersji próbnej i BYOL w celach promocyjnych i pozyskiwania użytkowników. Dzięki tym opcjom firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie wydawcy i nie ma skojarzonej opłaty transakcyjnej. Wydawcy są odpowiedzialni za obsługę wszystkich aspektów transakcji licencji na oprogramowanie, w tym między innymi: zamówienia, realizacji, pomiaru, rozliczeń, fakturowania, płatności i zbierania. Dzięki opcjom publikowania na liście i wersji próbnej wydawcy zachowują 100% opłat licencyjnych za oprogramowanie wydawcy pobranych od klienta. 

### <a name="transact-publishing-option"></a>Opcja publikowania transakcji

Oprócz opcji publikowania listy i wersji próbnej opcja publikowania transakcji jest dostępna dla wydawców. Korzysta to z dostępnych na całym świecie możliwości handlowych firmy Microsoft i umożliwia firmie Microsoft hostowania transakcji w portalu cloud marketplace w imieniu wydawcy.

## <a name="transact-general-overview"></a>Przegląd ogólny transakcji

Korzystając z opcji publikowania transakcji, firma Microsoft umożliwia sprzedaż oprogramowania innych firm i wdrażanie niektórych typów ofert w ramach subskrypcji platformy Azure klienta. Przy wyborze modelu rozliczeniowego i typu oferty wydawca musi wziąć pod uwagę rozliczanie opłat za infrastrukturę oraz opłaty licencyjne wydawcy za własne oprogramowanie.

Opcja publikowania transakcji jest obecnie obsługiwana dla następujących typów ofert: Maszyny wirtualne, aplikacje platformy Azure i aplikacje SaaS.


![[Zawieranie transakcji dla przedsiębiorstw w portalu Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Koszty infrastruktury rozliczeniowej

**Dla maszyn wirtualnych i aplikacji platformy Azure**

W przypadku maszyn wirtualnych i aplikacji platformy Azure opłaty za użycie infrastruktury platformy Azure są rozliczane w ramach subskrypcji platformy Azure klienta.  Opłaty za korzystanie z infrastruktury są wyceniane i prezentowane oddzielnie od opłat licencyjnych dostawcy oprogramowania na fakturze klienta.

**Dla aplikacji SaaS**

W przypadku aplikacji SaaS wydawca musi uwzględnić opłaty za korzystanie z infrastruktury platformy Azure i opłaty licencyjne za oprogramowanie jako element pojedynczego kosztu.  Jest on reprezentowany jako opłata ryczałtowa dla klienta. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio do partnera.  Rzeczywiste opłaty za korzystanie z infrastruktury nie są widoczne dla klienta.  Wydawcy zazwyczaj decydują się na powiązanie opłat za korzystanie z infrastruktury platformy Azure do cen licencji na oprogramowanie.  Opłaty licencyjne za oprogramowanie nie są mierzone ani oparte na zużyciu.

## <a name="transact-billing-models"></a>Transact modele rozliczeń

W zależności od użytej opcji transakcji opłaty licencyjne wydawcy mogą być przedstawione w następujący sposób:  

* Bezpłatnie: Bezpłatnie za licencje na oprogramowanie. 

* Przynieś własną licencję (BYOL): Wszelkie opłaty za licencje na oprogramowanie są zarządzane bezpośrednio między wydawcą a klientem. Firma Microsoft przechodzi tylko przez opłaty za korzystanie z infrastruktury platformy Azure. (Tylko maszyny wirtualne i aplikacje platformy Azure).

* Płatność zgodnie z rzeczywistym użyciem: opłaty licencyjne za oprogramowanie są przedstawiane jako stawka cenowa za godzinę (vCPU) na podstawie używanej infrastruktury platformy Azure. Dotyczy to tylko maszyn wirtualnych i aplikacji platformy Azure.

* Ceny subskrypcji: opłaty licencyjne za oprogramowanie są przedstawiane jako opłata miesięczna lub roczna, opłata cykliczna rozliczana jako stawka zryczałtowana lub za miejsce. Dotyczy to tylko aplikacji SaaS i aplikacji platformy Azure — aplikacje zarządzane.

* Bezpłatna wersja próbna oprogramowania: Bezpłatnie za licencje na oprogramowanie przez 30 lub 90 dni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Darmowe ceny i bring-your-own-license (BYOL)

Podczas publikowania oferty transakcji bezpłatnej lub bezpłatnej licencji firma Microsoft nie odgrywa roli w ułatwianiu transakcji sprzedaży opłat licencyjnych za oprogramowanie. Podobnie jak na liście i wersjach próbnych, wydawca zachowuje 100% opłat licencyjnych za oprogramowanie. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Ceny płatności zgodnie z rzeczywistym użyciem i subskrypcje (oparte na witrynie)

Podczas publikowania oferty transakcji płatności zgodnie z rzeczywistym ratami lub subskrypcji firma Microsoft udostępnia technologię i usługi przetwarzania zakupów licencji na oprogramowanie, zwrotów i naliczeń zwrotnych. W tym scenariuszu wydawca upoważnia firmę Microsoft do działania jako agent w tych celach. Wydawca zezwala firmie Microsoft na ułatwienie transakcji licencjonowania oprogramowania, zachowując jednocześnie ich wyznaczenie jako sprzedawcy, dostawcy, dystrybutora i licencjodawcy.

Firma Microsoft umożliwia klientom zamawianie, licencjonowanie i korzystanie z oprogramowania wydawcy, zgodnie z warunkami zarówno komercyjnego portalu Marketplace firmy Microsoft, jak i umowy licencyjnej wydawcy. Wydawcy muszą przedstawić umowę licencyjną użytkownika końcowego lub wybrać [umowę standardową](https://docs.microsoft.com/azure/marketplace/standard-contract) podczas tworzenia oferty.


### <a name="free-software-trials"></a>Wersje próbne wolnego oprogramowania

W przypadku scenariuszy publikowania transakcji wydawca może udostępnić licencję na oprogramowanie bezpłatnie przez 30 dni lub 90 dni. Ta funkcja rabatowania nie obejmuje koszt użycia infrastruktury platformy Azure, który jest napędzany przez korzystanie z rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz używania typów ofert i modeli rozliczeń do zarabiania na ofercie, wydawcy mogą dokonywać transakcji z ofertą prywatną, zawierającą wynegocjowane ceny specyficzne dla transakcji lub konfiguracje niestandardowe. Oferty prywatne są obsługiwane przez wszystkie 3 opcje publikowania transakcji.

Ta opcja umożliwia wyższe lub niższe ceny niż publicznie dostępna oferta. Oferty prywatne mogą być wykorzystane do rabatu lub dodania premii za ofertę. Oferty prywatne mogą być udostępniane jednemu lub większej liczbie klientów, wystawiając na biało ich subskrypcję platformy Azure na poziomie oferty.


### <a name="examples"></a>Przykłady

**Płatność zgodnie z rzeczywistym użyciem** 

* Jeśli włączysz opcję Płatność zgodnie z rzeczywistymi przyuśmiań, masz następującą strukturę kosztów.

|Koszt licencji  | $1.00 za godzinę  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-Core)    |   $0.14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$1.14 za godzinę*       |

* W tym scenariuszu microsoft rachunki $1.14 za godzinę za korzystanie z opublikowanego obrazu maszyny Wirtualnej.

|Rachunki Firmy Microsoft  | $1.14 za godzinę  |
|---------|---------|
|Microsoft płaci 80% kosztów licencji|   $0.80 za godzinę     |
|Microsoft utrzymuje 20% kosztów licencji  |  $0.20 za godzinę       |
|Firma Microsoft utrzymuje 100% kosztów użycia platformy Azure | $0.14 za godzinę |

**Przynieś własną licencję (BYOL)**

* Jeśli włączysz opcję BYOL, masz następującą strukturę kosztów.

|Koszt licencji  | Opłata licencyjna wynegocjowana i naliczona przez Ciebie  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-Core)    |   $0.14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$0.14 za godzinę*       |

* W tym scenariuszu microsoft rachunki $0.14 za godzinę za korzystanie z opublikowanego obrazu maszyny Wirtualnej.

|Rachunki Firmy Microsoft  | $0.14 za godzinę  |
|---------|---------|
|Firma Microsoft zachowuje koszt użycia platformy Azure    |   $0.14 za godzinę     |
|Firma Microsoft utrzymuje 0% kosztów licencji   |  $0.00 za godzinę       |

**Subskrypcja aplikacji SaaS**

Ta opcja musi być skonfigurowana do sprzedaży za pośrednictwem firmy Microsoft i może być wyceniana według stawki ryczałtowej lub na użytkownika w ujęciu miesięcznym lub rocznym.
*    Jeśli włączysz opcję Sprzedaj za pośrednictwem firmy Microsoft dla oferty SaaS, masz następującą strukturę kosztów.

|Koszt licencji       | $100.00 miesięcznie  |
|--------------|---------|
|Koszt użycia platformy Azure (D1/1-Core)    | Rozliczane bezpośrednio wydawcy, a nie klientowi |
|*Klient jest rozliczany przez firmę Microsoft*    |  *100,00 USD miesięcznie (uwaga: wydawca musi uwzględnić wszelkie poniesione lub przenoszone koszty infrastruktury w opłacie licencyjnej)*  |

* W tym scenariuszu Microsoft rozlicza $100.00 za licencję oprogramowania i wypłaca wydawcy $80.00.
* Od maja 2019 r. do czerwca 2020 r. partnerzy, którzy zakwalifikowali się do obniżonej opłaty za usługę w marketplace, otrzymają obniżoną opłatę transakcyjną w ofertach SaaS. W tym scenariuszu Microsoft rozlicza $100.00 za licencję oprogramowania i wypłaca wydawcy $90.00.

|Rachunki Firmy Microsoft  | $100.00 miesięcznie  |
|---------|---------|
|Microsoft płaci 80% kosztów licencji <br> \*Microsoft płaci 90% kosztów licencji za wszystkie kwalifikowane aplikacje SaaS   |   $80.00 miesięcznie <br> \*$90.00 miesięcznie    |
|Microsoft utrzymuje 20% kosztów licencji <br> \*Firma Microsoft przechowuje 10% kosztów licencji dla wszystkich kwalifikowanych aplikacji SaaS.  |  $20.00 miesięcznie <br> \*zł.     |

* **Obniżona opłata za usługę Marketplace:** W przypadku niektórych Produktów SaaS publikowanych w naszym portalu Marketplace Commercial Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w Umowie wydawcy firmy Microsoft) do 10%.  Aby produkt został zakwalifikowany, co najmniej jeden z produktów musi zostać oznaczony przez firmę Microsoft jako gotowy do współsprzedania ip lub współsprzedaży IP. Aby otrzymać obniżoną opłatę za usługę w marketplace za miesiąc, uprawnienia muszą zostać spełnione co najmniej pięć (5) dni roboczych przed końcem poprzedniego miesiąca kalendarzowego. Obniżona opłata za usługę Marketplace nie będzie miała zastosowania do maszyn wirtualnych, aplikacji zarządzanych ani żadnych innych produktów udostępnianych za pośrednictwem naszego komercyjnego portalu Marketplace.  Ta obniżona opłata za usługę Marketplace będzie dostępna dla kwalifikowanych ofert, a opłaty licencyjne pobierane są przez firmę Microsoft między 1 maja 2019 r. a 30 czerwca 2020 r.  Po tym czasie opłata za usługę marketplace powróci do normalnej kwoty.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fakturowanie, płatność, rozliczenia i windykacja klientów

**Fakturowanie i płatność**

Program Publisher może korzystać z preferowanej przez klienta metody fakturowania w celu zapewnienia opłat abonamentowych lub licencyjnych za oprogramowanie PAYGO.

**Umowa enterprise** 

Jeśli preferowaną metodą fakturowania klienta jest umowa Microsoft Enterprise Agreement, opłaty licencyjne za oprogramowanie będą naliczane przy użyciu tej metody fakturowania jako wyszczególniony koszt, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Karty kredytowe i faktura miesięczna** 

Klienci mogą również płacić kartą kredytową i fakturą miesięczną. W takim przypadku opłaty licencyjne za oprogramowanie będą naliczane tak samo, jak w scenariuszu umowy Enterprise Agreement, jako koszt wyszczególniony, niezależnie od kosztów użycia specyficznych dla platformy Azure.

Na przykład, jeśli klient kupuje przy użyciu karty kredytowej:

|Opis    |    Data  |
|----------|----------|
|Okres zamówienia   | 15 sierpnia 2018 - 30 sierpnia 2018 |
|Zakończenie okresu (miesiąc)   | 30 sierpnia 2018 r. |
|Data rozliczenia | 1 września 2018 r. |
|Data płatności odbiorcy | 1 września 2018 r. |
|Okres escrow (tylko karty kredytowe, 30 dni) | Wrzesień 1, 2018 - Wrzesień 30, 2018 |
|Początek okresu zbiórki | 1 września 2018 r. |
|Koniec okresu zbierania (maksymalnie 30 dni) | 30 września 2018 r. |
|Data obliczania wypłaty (co miesiąc 15) | 1 października 2018 r. |
|Data wypłaty | 15 października 2018 r. |

Jeśli klient kupuje przy użyciu umowy Enterprise Agreement:

| Opis |    Data  |
|----------|----------|
|Okres zamówienia | 15 sierpnia 2018 - 30 sierpnia 2018 |
|Zakończenie kadencji (kwartał) | 30 września 2018 r. |
|Data rozliczenia | 15 października 2018 r. |
|Okres escrow (tylko karty kredytowe, 30 dni) | Nie dotyczy |
|Początek okresu zbiórki | 15 października 2018 r. |
|Koniec okresu zbierania (maksymalnie 90 dni) | 15 stycznia 2019 r. |
|Data płatności odbiorcy | 30 grudnia 2018 r. |
|Data obliczania wypłaty (co miesiąc 15) | 15 stycznia 2019 r. |
|Data wypłaty | 15 lutego 2019 r. |

**Darmowe kredyty i zobowiązania pieniężne** 

Niektórzy klienci decydują się na przedpłatę platformy Azure z zobowiązaniem pieniężnym w umowie Enterprise Agreement lub otrzymali bezpłatne kredyty do użytku z platformą Azure. Mimo że te kredyty mogą być używane do płacenia za użycie platformy Azure, nie można ich używać do płacenia za opłaty licencyjne za oprogramowanie wydawcy.

**Rozliczenia i kolekcje** 

Rozliczanie licencji na oprogramowanie programu Publisher jest prezentowane przy użyciu wybranej przez klienta metody fakturowania i jest zgodne z osią czasu fakturowania. Klienci bez umowy Enterprise Agreement są co miesiąc rozliczani za licencje na oprogramowanie w marketplace. Klienci z umową Enterprise Agreement są rozliczani co miesiąc za pomocą faktury prezentowanej kwartalnie.

Po wybraniu modeli cen subskrypcji lub płatności zgodnie z rzeczywistym użyciem firma Microsoft działa jako agent wydawcy i jest odpowiedzialna za wszystkie aspekty rozliczeń, płatności i zbierania.

### <a name="publisher-payout-and-reporting"></a>Wypłaty i raportowanie przez wydawcę

* Wszelkie opłaty licencyjne za oprogramowanie pobierane przez firmę Microsoft jako agent podlegają opłacie transakcyjnej w wysokości 20%, chyba że określono inaczej i zostaną odjęte w momencie wypłaty przez wydawcę.

* Klienci zazwyczaj kupują za pomocą umowy Enterprise Agreement lub umowy płatności zgodnie z rzeczywistym użyciem karty kredytowej. Typ umowy określa rozliczenia, fakturowanie, zbieranie i terminy wypłat.

>[!NOTE] 
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania transakcji są dostępne za pośrednictwem sekcji Insights w witrynie Cloud Partner Portal lub Analytics w Centrum partnerów.

#### <a name="billing-questions-and-support"></a>Pytania dotyczące rozliczeń i pomoc techniczna

Aby uzyskać więcej informacji i zasad prawnych, zobacz [Umowy wydawcy](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (dostępne w portalu cloud partner).

Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu marketplace.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Wymogi dotyczące transakcji

Wymagania dotyczące transakcji dla różnych typów ofert są omówione w tej sekcji.

### <a name="requirements-for-all-offer-types"></a>Wymagania dla wszystkich typów ofert

- Konto Microsoft i informacje finansowe są wymagane dla opcji publikowania transakcji, niezależnie od modelu cenowego oferty.
- Obowiązkowe informacje finansowe obejmują konto wypłat i profil podatkowy.

Aby uzyskać więcej informacji na temat konfigurowania tych kont, zobacz [Zarządzanie kontem Centrum partnerów](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)).


### <a name="requirements-for-specific-offer-types"></a>Wymagania dotyczące określonych typów ofert

Opcja publikowania transakcji jest dostępna tylko do użycia z następującymi typami oferty portalu Marketplace: 

**Maszyna wirtualna** 

Wybierz z bezpłatnych, bring-your-own-license lub pay-as-you-go-pricing modeli i przedstawić jako jednostki SKU zdefiniowane na poziomie oferty. Na rachunku za platformę Azure klienta firma Microsoft przedstawia opłaty licencyjne za oprogramowanie wydawcy oddzielnie od podstawowych opłat za infrastrukturę platformy Azure. Opłaty za infrastrukturę platformy Azure są oparte na korzystaniu z oprogramowania wydawcy.

**Aplikacje platformy Azure: szablon rozwiązania lub aplikacja zarządzana** 

Należy aprowizować jedną lub więcej maszyn wirtualnych i pobiera przez sumę ceny maszyny wirtualnej. W przypadku aplikacji zarządzanych w ramach jednego planu można wybrać zryczałtowaną subskrypcję miesięczną jako model cenowy zamiast cen maszyny wirtualnej. W niektórych przypadkach opłaty za użycie infrastruktury platformy Azure są przekazywane klientowi oddzielnie od opłat licencyjnych za oprogramowanie, ale na tym samym zestawieniu rozliczeniowym. Jeśli jednak skonfigurujesz ofertę aplikacji zarządzanej dla opłat za infrastrukturę isv, zasoby platformy Azure są naliczane wydawcy, a klient otrzymuje zryczałtowaną opłatę, która obejmuje koszt infrastruktury, licencji na oprogramowanie i usług zarządzania.

## <a name="next-steps"></a>Następne kroki

* Przejrzyj wymagania dotyczące uprawnień w sekcji Opcje publikowania według typu oferty, aby sfinalizować wybór i konfigurację oferty.
* Przejrzyj wzorce publikowania według witryny sklepowej, aby zapoznać się z przykładami, w jaki sposób rozwiązanie mapuje typ oferty i konfigurację.
