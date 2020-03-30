---
title: Rozliczenia na rynku komercyjnym | Azure Marketplace
description: Ten artykuł obejmuje tematy związane z handlem na rynku komercyjnym.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279746"
---
# <a name="commercial-marketplace-billing"></a>Rozliczenia na rynku komercyjnym

Ten artykuł obejmuje tematy związane z handlem na rynku komercyjnym:

- [Opcje publikowania w portalu Marketplace](#marketplace-publishing-options)
- [Przegląd ogólny transakcji](#transact-general-overview)
- [Transact modele rozliczeń](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opcje publikowania w portalu Marketplace

Rynek komercyjny oferuje wydawcom kilka opcji publikowania.

### <a name="list-and-trial-publishing-options"></a>Opcje publikowania listy i wersji próbnej

Wydawcy mogą korzystać z listy, wersji próbnej i przynieść własne opcje publikowania licencji (BYOL) w celach promocyjnych i pozyskiwania użytkowników. Dzięki tym opcjom firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie wydawcy i nie ma skojarzonej opłaty transakcyjnej. Wydawcy są odpowiedzialni za obsługę wszystkich aspektów transakcji licencji na oprogramowanie, w tym między innymi: zamówienia, realizacji, pomiaru, rozliczeń, fakturowania, płatności i zbierania. Dzięki opcjom publikowania na liście i wersji próbnej wydawcy zachowują 100% opłat licencyjnych za oprogramowanie wydawcy pobranych od klienta.

### <a name="transact-publishing-option"></a>Opcja publikowania transakcji

Oprócz opcji publikowania listy i wersji próbnej opcja publikowania transakcji jest dostępna dla wydawców. Ta opcja korzysta z dostępnych na całym świecie możliwości handlowych firmy Microsoft i umożliwia firmie Microsoft hostowania transakcji w portalu cloud marketplace w imieniu wydawcy.

## <a name="transact-general-overview"></a>Przegląd ogólny transakcji

Korzystając z opcji publikowania transakcji, firma Microsoft umożliwia sprzedaż oprogramowania innych firm i wdrażanie niektórych typów ofert w ramach subskrypcji platformy Azure klienta. Przy wyborze modelu rozliczeniowego i typu oferty wydawca musi wziąć pod uwagę rozliczanie opłat za infrastrukturę oraz opłaty licencyjne wydawcy za własne oprogramowanie.

Opcja publikowania transakcji jest obecnie obsługiwana dla następujących typów ofert: maszyny wirtualne, aplikacje platformy Azure i aplikacje SaaS.

![Transakcje w portalu Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Koszty infrastruktury rozliczeniowej

#### <a name="for-virtual-machines-and-azure-applications"></a>Dla maszyn wirtualnych i aplikacji platformy Azure

W przypadku maszyn wirtualnych i aplikacji platformy Azure opłaty za użycie infrastruktury platformy Azure są rozliczane w ramach subskrypcji platformy Azure klienta. Opłaty za korzystanie z infrastruktury są wyceniane i prezentowane oddzielnie od opłat licencyjnych dostawcy oprogramowania na fakturze klienta.

#### <a name="for-saas-apps"></a>Dla aplikacji SaaS

W przypadku aplikacji SaaS wydawca musi uwzględnić opłaty za korzystanie z infrastruktury platformy Azure i opłaty licencyjne za oprogramowanie jako element pojedynczego kosztu. Jest on reprezentowany jako opłata ryczałtowa dla klienta. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio do partnera. Rzeczywiste opłaty za korzystanie z infrastruktury nie są widoczne dla klienta. Wydawcy zazwyczaj decydują się na powiązanie opłat za korzystanie z infrastruktury platformy Azure do cen licencji na oprogramowanie. Opłaty licencyjne za oprogramowanie nie są mierzone ani oparte na zużyciu.

## <a name="transact-billing-models"></a>Transact modele rozliczeń

W zależności od użytej opcji transakcji opłaty licencyjne wydawcy mogą być przedstawione w następujący sposób:

- *Bezpłatnie*: Bezpłatne licencje na oprogramowanie.
- *Przynieś własną licencję (BYOL)*: Wszelkie opłaty za licencje na oprogramowanie są zarządzane bezpośrednio między wydawcą a klientem. Firma Microsoft przechodzi tylko przez opłaty za korzystanie z infrastruktury platformy Azure. (Tylko maszyny wirtualne i aplikacje platformy Azure).
- *Płatność zgodnie z rzeczywistym użyciem:* Opłaty licencyjne za oprogramowanie są przedstawiane jako stawka cenowa za godzinę (vCPU) na podstawie używanej infrastruktury platformy Azure. Ten model dotyczy tylko maszyn wirtualnych i aplikacji platformy Azure.
- *Ceny subskrypcji:* Opłaty licencyjne za oprogramowanie są przedstawiane jako opłata miesięczna lub roczna, opłata cykliczna rozliczana jako stawka zryczałtowana lub za miejsce. Ten model dotyczy tylko aplikacji SaaS i aplikacji platformy Azure — aplikacje zarządzane.
- *Bezpłatna wersja próbna oprogramowania*: Bezpłatnie za licencje na oprogramowanie przez 30 dni lub 90 dni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Darmowe ceny i bring-your-own-license (BYOL)

Podczas publikowania oferty transakcji bezpłatnej lub bezpłatnej licencji firma Microsoft nie odgrywa roli w ułatwianiu transakcji sprzedaży opłat licencyjnych za oprogramowanie. Podobnie jak na liście i wersjach próbnych, wydawca zachowuje 100% opłat licencyjnych za oprogramowanie.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Ceny płatności zgodnie z rzeczywistym użyciem i subskrypcje (oparte na witrynie)

Publikując ofertę transakcji płatności zgodnie z rzeczywistym ratą lub subskrypcją, firma Microsoft udostępnia technologię i usługi do przetwarzania zakupów licencji na oprogramowanie, zwrotów i naliczać zwroty. W tym scenariuszu wydawca upoważnia firmę Microsoft do działania jako agent w tych celach. Wydawca zezwala firmie Microsoft na ułatwienie transakcji licencjonowania oprogramowania, zachowując jednocześnie ich wyznaczenie jako sprzedawcy, dostawcy, dystrybutora i licencjodawcy.

Firma Microsoft umożliwia klientom zamawianie, licencjonowanie i korzystanie z oprogramowania wydawcy, zgodnie z warunkami zarówno komercyjnego portalu Marketplace firmy Microsoft, jak i umowy licencyjnej wydawcy. Wydawcy muszą przedstawić umowę licencyjną użytkownika końcowego lub wybrać [umowę standardową](https://docs.microsoft.com/azure/marketplace/standard-contract) podczas tworzenia oferty.

### <a name="free-software-trials"></a>Wersje próbne wolnego oprogramowania

W przypadku scenariuszy publikowania transakcji wydawca może udostępnić licencję na oprogramowanie bezpłatnie przez 30 dni lub 90 dni. Ta funkcja rabatowania nie obejmuje koszt użycia infrastruktury platformy Azure, który jest napędzany przez korzystanie z rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz używania typów ofert i modeli rozliczeń do zarabiania na ofercie, wydawcy mogą dokonywać transakcji z ofertą prywatną, zawierającą wynegocjowane ceny i specyficzne dla transakcji lub konfiguracje niestandardowe. Oferty prywatne są obsługiwane przez wszystkie 3 opcje publikowania transakcji.

Ta opcja umożliwia wyższe lub niższe ceny niż publicznie dostępna oferta. Oferty prywatne mogą być wykorzystane do zniżki lub dodać premię za ofertę. Oferty prywatne mogą być udostępniane jednemu lub większej liczbie klientów, wystawiając na biało ich subskrypcję platformy Azure na poziomie oferty.

### <a name="examples"></a>Przykłady

#### <a name="pay-as-you-go"></a>Płatność zgodnie z rzeczywistym użyciem

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

### <a name="bring-your-own-license-byol"></a>Przynieś własną licencję (BYOL)

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

### <a name="saas-app-subscription"></a>Subskrypcja aplikacji SaaS

Ta opcja musi być skonfigurowana do sprzedaży za pośrednictwem firmy Microsoft i może być wyceniana według stawki ryczałtowej lub na użytkownika w ujęciu miesięcznym lub rocznym.

• Jeśli włączysz opcję Sprzedaj za pośrednictwem firmy Microsoft dla oferty SaaS, masz następującą strukturę kosztów.

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

**Obniżona opłata za usługę Marketplace:** W przypadku niektórych Produktów SaaS publikowanych w naszym portalu Marketplace Commercial Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w Umowie wydawcy firmy Microsoft) do 10%.  Aby produkt został zakwalifikowany, co najmniej jeden z produktów musi zostać oznaczony przez firmę Microsoft jako gotowy do współsprzedania ip lub współsprzedaży IP. Aby otrzymać obniżoną opłatę za usługę w marketplace za miesiąc, uprawnienia muszą zostać spełnione co najmniej pięć (5) dni roboczych przed końcem poprzedniego miesiąca kalendarzowego. Obniżona opłata za usługę Marketplace nie będzie miała zastosowania do maszyn wirtualnych, aplikacji zarządzanych ani żadnych innych produktów udostępnianych za pośrednictwem naszego komercyjnego portalu Marketplace.  Ta obniżona opłata za usługę Marketplace będzie dostępna dla kwalifikowanych ofert, a opłaty licencyjne pobierane są przez firmę Microsoft między 1 maja 2019 r. a 30 czerwca 2020 r.  Po tym czasie opłata za usługę marketplace powróci do normalnej kwoty.
