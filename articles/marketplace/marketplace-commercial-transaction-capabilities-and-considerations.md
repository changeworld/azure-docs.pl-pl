---
title: Portal Marketplace transakcji handlowych możliwości i zagadnienia | Azure
description: W tym artykule opisano zagadnienia cen, rozliczeń, fakturowania i informacje dotyczące wypłat Transact dla typu oferty.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter: ''
author: yijenj
manager: nuno costa
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: 3c07f7ac2b6407a3edf53fd5d3a4b402714fefbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065014"
---
# <a name="azure-marketplace-commercial-transaction-capabilities-and-considerations"></a>Azure Marketplace transakcji handlowych możliwości i zagadnienia

Opcje publikowania z portalu Marketplace platformy Azure oferują unikatowe dostawców oprogramowania i usług w chmurze w kontakcie z klientami. W tym artykule omówiono następujące tematy dotyczące handlu w portalu Azure Marketplace:

* Opcje publikowania w portalu Marketplace
* Ogólne omówienie Transact
* Przeprowadzaj transakcje modelami rozliczeń
* Wymagania dotyczące transakcji

## <a name="marketplace-publishing-options"></a>Opcje publikowania w portalu Marketplace

Następujące opcje publikowania są dostępne dla wydawców w portalu Azure Marketplace.

### <a name="list--trial-publishing-options"></a>Lista & wersji próbnej opcje publikowania

W witrynie Azure Marketplace wydawcy mogą korzystać z listy i wersji próbnej opcje publikowania promocyjnych i celów nabycia użytkownika. Z listy lub wersji próbnej opcje publikowania Microsoft nie uczestniczy bezpośrednio w transakcji licencji na oprogramowanie wydawcy, a nie ma skojarzonej transakcji opłat. Wydawcy są odpowiedzialni za obsługę wszystkich aspektów transakcji licencji oprogramowania, w tym między innymi: kolejność, realizacji, pomiaru użytkowania, rozliczeniami, Fakturowanie, płatności i kolekcji. Z listy i w wersji próbnej opcje publikowania wydawcy Zachowaj 100% publisher opłat licencyjnych zebrane z klienta. 

### <a name="transact-publishing-option"></a>Przeprowadzaj transakcje opcja publikowania

Oprócz wersji próbnej opcje publikowania i listy transact publikowania opcja jest dostępna dla wydawców w portalu Azure Marketplace.   Wykorzystuje ona możliwości dostępnie handlowych firmy Microsoft. Ta opcja umożliwia firmie Microsoft do hosta cloud marketplace transakcji w imieniu wydawcy.

## <a name="transact-general-overview"></a>Ogólne omówienie Transact

Korzystając z instrukcji języka transact opcji publikowania, firma Microsoft zapewnia sprzedaż oprogramowania innych firm oraz wdrażania pewnych typów ofert do subskrypcji platformy Azure przez klienta. Wydawcy należy wziąć pod uwagę Naliczanie opłat za infrastrukturę platformy Azure, a internetowa wydawcy opłat licencyjnych, podczas wybierania rozliczeń modelu i oferują typ w witrynie Azure Marketplace.

Opcja publikowania Transact w witrynie Azure Marketplace jest obecnie obsługiwana dla następujących typów oferty: Maszyny wirtualne, aplikacje platformy Azure lub aplikacje SaaS.

![[Programu Enterprise oferty w witrynie Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Rozliczenia kosztów infrastruktury

**Dla maszyn wirtualnych i aplikacji platformy Azure**

Dla maszyn wirtualnych i aplikacji platformy Azure opłaty za użycie infrastruktury platformy Azure są rozliczane z subskrypcją platformy Azure przez klienta.  Opłaty za użycie infrastruktury są naliczane i prezentowana oddzielnie od opłat licencyjnych dostawcy oprogramowania na fakturze klienta.

**Dla aplikacji SaaS**

W przypadku aplikacji SaaS wydawcy muszą uwzględniać opłaty za użycie infrastruktury platformy Azure i opłat licencyjnych jako element pojedynczy koszt.  Jest przedstawiana jako obowiązuje stała opłata do klienta. Użycie infrastruktury platformy Azure zarządzane i rozliczane partnera bezpośrednio.  Opłaty za użycie rzeczywistej infrastruktury nie są widoczne dla klienta.  Wydawcy zazwyczaj zdecydować się na pakietu opłaty za użycie infrastruktury platformy Azure do ich ceny licencji oprogramowania.  Oprogramowanie, które nie są naliczane opłaty licencyjne lub oparty na zużyciu.

## <a name="transact-billing-models"></a>Przeprowadzaj transakcje modelami rozliczeń

W zależności od opcji transakcji używany opłat za licencje na oprogramowanie wydawcy jest widoczne w następujący sposób:  

* Bezpłatne: Brak opłat za licencje na oprogramowanie. 

* Model dostarczania własnej licencji (BYOL): Wszelkie odpowiednie opłaty za licencje na oprogramowanie odbywa się bezpośrednio od wydawcy i klientów. Microsoft tylko przechodzą przez opłaty za użycie infrastruktury platformy Azure. (Maszyny wirtualne i tylko w przypadku aplikacji platformy Azure).

* Płatność za rzeczywiste użycie: Opłaty za licencję oprogramowania są uporządkowane jako za godzinę, rdzeń (vCPU) ceny stawka od w infrastrukturze platformy Azure używane. Dotyczy to tylko maszyny wirtualne i aplikacje platformy Azure.

* Subskrypcja ceny (oparty na lokacji): Opłaty za licencję oprogramowania są uporządkowane jako opłata miesięczna, cyklicznych.  Dotyczy to tylko aplikacji SaaS i aplikacje platformy Azure — aplikacje zarządzane przez.

* Bezpłatna wersja próbna oprogramowania: Brak opłat za licencje na oprogramowanie do 30 dni lub 90 dni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Bezpłatne i bring-your-own-license (BYOL) ceny

Podczas publikowania oferty bezpłatnej lub bring-your-own-license transakcji firmy Microsoft nie uczestniczy w ułatwienia transakcji sprzedaży dla Twojego opłat za licencje na oprogramowanie. Podobnie jak listy i wersji próbnej opcje publikowania wydawca zapewnia 100% opłat za licencje oprogramowania. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Płatność za rzeczywiste użycie i (oparty na lokacji) cennik subskrypcji

Podczas publikowania oferty transakcji płatności lub subskrypcji, firma Microsoft udostępnia technologii i usług do zakupów licencji oprogramowania procesu, zwrotów i obciążeń zwrotnych. W tym scenariuszu wydawcy upoważnia firmę Microsoft, aby pełnić rolę agenta do tych celów. Wydawcy umożliwia firmie Microsoft w celu ułatwienia licencjonowania transakcji przy zachowaniu ich oznaczenie jako sprzedawcy, dostawca, dystrybutora i licencjodawcy oprogramowania.

Firma Microsoft zapewnia klientom zamówienia, licencji i korzystać z oprogramowania wydawcy poddanie warunków i postanowień w portalu Azure Marketplace i Umowa licencyjna wydawcy (patrz portalu Cloud Partner). Wydawca musi zapewnić ich umowa licencyjna w ramach oferty z portalu marketplace.

Zamówienia przetwarzane za pośrednictwem portalu marketplace są doliczane do subskrypcji platformy Azure przez klienta w jednym rachunku tej samej metody rozliczeń koszty infrastruktury platformy Azure przez klienta. Klienci mogą używać preferowanych fakturowania metody i płatności instrument umożliwiający ich rozliczeniami subskrypcji systemu Azure.

### <a name="free-software-trials"></a>Bezpłatne oprogramowanie próbne

Dla języka transact scenariuszy publikowania, wprowadzić wydawcy oprogramowania licencji dostępnych bezpłatnie przez 30 dni lub 90 dni. Ta funkcja licząc nie obejmuje koszt użycia infrastruktury platformy Azure, które są sterowane przy użyciu rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz używania oferuje typy i model do rozliczeń wprowadzanie do obiegu oferty, wydawcy przeprowadzać transakcje w prywatnej wersji oferty rozwiązań w modelu cen wynegocjowanym, specyficzne dla transakcji, a konfiguracje niestandardowe przy użyciu niestandardowego obrazu. Oferty prywatne są obsługiwane przez wszystkie 3 transact opcje publikowania.

Ta opcja cennika może być wyższe lub niższe niż publicznie wyświetlane ceny.  Oferty prywatne może służyć do rabatu, lub Dodaj premium dla oferty. Oferty prywatne mogą dostępne jednego lub więcej klientów przez biały listę swoich subskrypcji platformy Azure na poziomie oferty.

### <a name="examples"></a>Przykłady

**Pay-As-You-Go** 

* Jeśli włączona jest opcja płatności, masz następujące elementy struktury kosztów.

|Koszt licencji  | $1.00 za godzinę  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   0,14 USD za godzinę     |
|*Rozliczenie przez firmę Microsoft*    |  *$1.14 za godzinę*       |

* W tym scenariuszu firma Microsoft rozlicza 1.14 $ na godzinę w przypadku użycia opublikowanego obrazu maszyny Wirtualnej.

|W ramach firmy Microsoft  | $1.14 za godzinę  |
|---------|---------|
|Microsoft płaci 80% kosztów licencji|   $0.80 za godzinę     |
|Firma Microsoft zachowuje 20% przychodów kosztami licencji  |  0,20 USD za godzinę       |
|Firma Microsoft zachowuje 100% kosztów użycia platformy Azure | 0,14 USD za godzinę |

**Model dostarczania własnej licencji (BYOL)**

* Po włączeniu opcji BYOL, masz następujące elementy struktury kosztów.

|Koszt licencji  | Opłata za licencję negocjowane i rozliczane przez użytkownika  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   0,14 USD za godzinę     |
|*Rozliczenie przez firmę Microsoft*    |  *0,14 USD za godzinę*       |

* W tym scenariuszu firma Microsoft rozlicza 0,14 USD za godzinę do użycia opublikowanego obrazu maszyny Wirtualnej.

|W ramach firmy Microsoft  | 0,14 USD za godzinę  |
|---------|---------|
|Firma Microsoft zachowuje koszty użycia platformy Azure    |   0,14 USD za godzinę     |
|Firma Microsoft zachowuje 0% kosztów licencji   |  0,00 USD za godzinę       |

**Subskrypcję aplikacji SaaS (sprzedaż za pośrednictwem platformy Azure)**

Ta opcja musi być skonfigurowany do sprzedaży firmy Microsoft i rozliczana przy użyciu jednej lub więcej planów miesięcznych stosowana jest stała stawka zdefiniowane na poziomie oferty.

* Jeśli włączysz sprzedaży przy użyciu opcji platformy Azure, masz następujące elementy struktury kosztów.

|Koszt licencji       | 100,00 USD na miesiąc  |
|--------------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    | Rozliczane bezpośrednio z wydawcą zostało nawiązane nie klienta |
|*Rozliczenie przez firmę Microsoft*    |  *100,00 USD miesięcznie (Uwaga: wydawcy muszą uwzględniać żadnych kosztów infrastruktury poniesionych lub przekazywania w opłacie licencyjnej)*  |

* W tym scenariuszu firma Microsoft rozlicza 100,00 USD dla licencji na oprogramowanie i płaci się $80,00 z wydawcą zostało nawiązane.

|W ramach firmy Microsoft  | 100,00 USD na miesiąc  |
|---------|---------|
|Microsoft płaci 80% kosztów licencji    |   $80,00 na miesiąc     |
|Firma Microsoft zachowuje 20% przychodów kosztami licencji   |  20,00 USD za na miesiąc       |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faktury płatności, rozliczeń i kolekcje dla klientów

**Faktury i płatności**

Wydawca umożliwia odbiorcy preferowana metoda fakturowania dostarczanie subskrypcji lub opłat za licencje na oprogramowanie PŁATNICZEGO.

**Umowy Enterprise agreement** 

Preferowaną metodą fakturowania klienta w przypadku umowy Microsoft Enterprise Agreement, będą naliczane opłaty licencyjne oprogramowania za pomocą tego, Metoda fakturowania koszt dla pozycji, niezależnie od wszelkie koszty użytkowania specyficzne dla platformy Azure.

**Karty kredytowe i miesięcznej fakturze** 

Klienci mogą także płacić za pomocą karty kredytowej i miesięcznej fakturze. W takim przypadku opłaty licencyjne oprogramowania będą rozliczane podobnie jak w scenariuszu umowy Enterprise Agreement, jako wyszczególnione koszt, niezależnie od wszelkie koszty użytkowania specyficzne dla platformy Azure.

Na przykład, jeśli klient zakupi, korzystając z karty kredytowej:

|Opis    |    Date  |
|----------|----------|
|Okres zamówienia   | 15 sierpnia 2018 r. — 30 sierpnia 2018 r. |
|Kończenie termin (miesiąc)   | 30 sierpnia 2018 r. |
|Data rozliczenia | 1 września 2018 r. |
|Data płatności odbiorcy | 1 września 2018 r. |
|Zdeponowania okres (kart kredytowych, 30 dni) | Do 1 września 2018 – 30 września 2018 r. |
|Rozpoczęcia okresu kolekcji | 1 września 2018 r. |
|Koniec okresu kolekcji (maksymalna, 30 dni) | 30 września 2018 r. |
|Data obliczenia informacje dotyczące wypłat (co miesiąc na 15) | 1 października 2018 r. |
|Informacje dotyczące wypłat daty | 15 października 2018 r. |

Jeśli klient zakupi przy użyciu umowy Enterprise Agreement:

| Opis |    Date  |
|----------|----------|
|Okres zamówienia | 15 sierpnia 2018 r. — 30 sierpnia 2018 r. |
|Kończenie termin (kwartał) | 30 września 2018 r. |
|Data rozliczenia | 15 października 2018 r. |
|Zdeponowania okres (kart kredytowych, 30 dni) | Nie dotyczy |
|Rozpoczęcia okresu kolekcji | 15 października 2018 r. |
|Koniec okresu kolekcji (maksymalna, 90 dni) | 15 stycznia 2019 r. |
|Data płatności odbiorcy | 30 grudnia 2018 r. |
|Data obliczenia informacje dotyczące wypłat (co miesiąc na 15) | 15 stycznia 2019 r. |
|Informacje dotyczące wypłat daty | 15 lutego 2019 r. |

**Bezpłatne kredyty i zobowiązania pieniężnego** 

Niektórzy klienci zdecydować się na przedpłaty platformy Azure przy użyciu zobowiązań pieniężnych w umowie Enterprise Agreement lub dostarczono bezpłatnych środków do użytku z platformą Azure. Mimo że te środki na korzystanie z może służyć do zapłaty za użycie platformy Azure, nie mogą one używane do zapłacenia za opłat za licencje na oprogramowanie wydawcy.

**Pomoc dotycząca rozliczeń i kolekcji** 

Rozliczenia licencji na oprogramowanie wydawcy są prezentowane za pomocą metody klienta wybranego fakturowania i następuje fakturowania osi czasu. Klienci bez umowy Enterprise Agreement w miejscu, są rozliczane co miesiąc dla licencji na oprogramowanie portalu marketplace. Klienci z umową Enterprise Agreement, są rozliczane co miesiąc za pomocą faktury, zostanie wyświetlony co kwartał.

Po wybraniu subskrypcji lub zgodnie z rzeczywistym użyciem modele cen, firma Microsoft działa jako agent wydawcy i odpowiada za wszystkie aspekty rozliczeń, płatności i kolekcji.

### <a name="publisher-payout-and-reporting"></a>Informacje dotyczące wypłat wydawcy i raportowanie

* Żadnych opłat licencyjnych zbieranych przez firmę Microsoft jako funkcja agentów jest zależna od opłaty za transakcje 20%, chyba że określono inaczej i są odejmowane w czasie informacje dotyczące wypłat wydawcy.

* Klienci zwykle kupować, korzystając z umowy Enterprise Agreement lub karty kredytowej włączone Umowę zgodnie z rzeczywistym użyciem. Określa typ umowy, rozliczeń, fakturowania, kolekcji i informacje dotyczące wypłat chronometrażu.

>[!NOTE] 
>Raportowanie i szczegółowe informacje transact opcji publikowania są dostępne za pośrednictwem sekcji szczegółowe informacje o portalu Cloud Partner.

#### <a name="billing-questions-and-support"></a>Pytania dotyczące rozliczeń i pomocy technicznej

Aby uzyskać więcej informacji i prawne zasad, zobacz [umowie wydawcy portalu](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (dostępne w portalu Cloud Partner).

Aby uzyskać pomoc dotyczącą pytań związanych z rozliczeniami [utworzyć zdarzenie pomocy technicznej](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&pesid=16230&forceorigin=esmc&ccsid=636764613233453423) i wybierz maszyny wirtualne lub aplikacji sieci Web (zwane również aplikacje SaaS) w zależności od typu oferty używane.

## <a name="transact-requirements"></a>Wymagania dotyczące transakcji

W tej sekcji znajdują się wymagania transact dla typów inną ofertę.

### <a name="requirements-for-all-offer-types"></a>Wymagania dotyczące wszystkich oferuje typy

**Konto Centrum deweloperów i firmy Microsoft** 

* W przypadku transact publikowania opcji, bez względu na to oferta w modelu cen zarówno w Centrum deweloperów, jak i konto Microsoft jest wymagane.
* Konto Centrum deweloperów zawiera wszystkich odpowiednich szczegółów finansowych wymaganych do firmy Microsoft, aby opłaty od klienta w imieniu wydawcy i wpłacenia się wydawcy.
* Mimo że na obu kontach może używać tej samej organizacji lub szczegóły logowania firmy Microsoft, Centrum deweloperów jest osobnego konta z konta w portalu Cloud Partner. Aby użyć opcji publikowania transact, wydawca, należy wykonać Centrum deweloperów konto procesu rejestracji, poza rejestracją na dostęp do portalu Cloud Partner.

*Aby uzyskać więcej informacji na temat konfigurowania tych kont, zobacz [stają się wydawcy w witrynie Marketplace chmury](https://docs.microsoft.com/azure/marketplace/become-publisher).*

### <a name="requirements-for-specific-offer-types"></a>Wymagania dotyczące określonych typów ofert

Opcja publikowania transact jest dostępne tylko do użytku z następujących typów ofert portalu marketplace: 

**Maszyna wirtualna** 

Wybierz z bezpłatnej, bring-your-own-license lub płatności-zgodnie-you-rzeczywistym-pricing modeli, a następnie prezentować w postaci zdefiniowane na poziomie oferty jednostki SKU. Na rachunku za platformę Azure przez klienta firmy Microsoft przedstawia informacje o wydawcy opłat za licencje na oprogramowanie niezależnie od opłaty podstawowej infrastruktury platformy Azure. Opłaty za infrastrukturę platformy Azure są sterowane za korzystanie z oprogramowania wydawcy.

**Aplikacje platformy Azure: Szablon rozwiązania usługi lub aplikacji zarządzanej** 

Należy zarezerwować maszyny wirtualne lub ściąga za pośrednictwem sumę cen maszyn wirtualnych. Dla zarządzanych aplikacji w ramach jednego planu jednolitą stawkę miesięcznej subskrypcji można wybrać jako ceny modelu w zamian cen maszyn wirtualnych. W obu przypadkach opłaty za użycie infrastruktury platformy Azure są przekazywane do klienta oddzielnie od opłat licencyjnych oprogramowania, ale w tym samym zestawienie rozliczeniowe.

## <a name="next-steps"></a>Kolejne kroki

* Należy przejrzeć wymagania kwalifikacyjne w opcje publikowania przez sekcję typ oferty w celu sfinalizowania wyborem i konfiguracją, oferty.
* Przejrzyj wzorców publikowania w sklepie przykłady w sposób swoje rozwiązanie mapowania typu oferty i konfiguracji.
* Stają się wydawcy w witrynie Marketplace, a następnie zaloguj się do [portalu Cloud Partner](https://cloudpartner.azure.com) tworzenie i konfigurowanie swojej oferty.
