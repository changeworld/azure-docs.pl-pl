---
title: Portal Marketplace transakcji handlowych możliwości i zagadnienia | Azure
description: W tym artykule opisano zagadnienia cen, rozliczeń, fakturowania i informacje dotyczące wypłat Transact dla typu oferty.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: d266b314f19979578b7e7b8de4e7a7090200c9d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445451"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Możliwości transakcji handlowych w portalu marketplace i zagadnienia

W tym artykule omówiono następujące tematy dotyczące handlu komercyjnych portalu Marketplace

* Opcje publikowania w portalu Marketplace
* Ogólne omówienie Transact
* Przeprowadzaj transakcje modelami rozliczeń
* Wymagania dotyczące transakcji

## <a name="marketplace-publishing-options"></a>Opcje publikowania w portalu Marketplace

Następujące opcje publikowania są dostępne dla marketplace komercyjnych wydawców.

### <a name="list--trial-publishing-options"></a>Lista & wersji próbnej opcje publikowania

Wydawcy mogą korzystać z listy, wersja próbna i publikowania opcje BYOL promocyjnych i celów nabycia użytkownika. Za pomocą tych opcji Microsoft nie uczestniczy bezpośrednio w transakcji licencji na oprogramowanie wydawcy, a nie ma skojarzonej transakcji opłat. Wydawcy są odpowiedzialni za obsługę wszystkich aspektów transakcji licencji oprogramowania, w tym między innymi: kolejność, realizacji, pomiaru użytkowania, rozliczeniami, Fakturowanie, płatności i kolekcji. Z listy i w wersji próbnej opcje publikowania wydawcy Zachowaj 100% publisher opłat licencyjnych zebrane z klienta. 

### <a name="transact-publishing-option"></a>Przeprowadzaj transakcje opcja publikowania

Oprócz listy i w wersji próbnej opcje publikowania transact publikowania opcja jest dostępna dla wydawców. Korzysta z możliwości dostępnie handlowych firmy Microsoft i pozwala firmie Microsoft na hosta cloud marketplace transakcji w imieniu wydawcy.

## <a name="transact-general-overview"></a>Ogólne omówienie Transact

Korzystając z instrukcji języka transact opcji publikowania, firma Microsoft zapewnia sprzedaż oprogramowania innych firm oraz wdrażania pewnych typów ofert do subskrypcji platformy Azure przez klienta. Wydawca należy wziąć pod uwagę Naliczanie opłat za infrastrukturę, a internetowa wydawcy opłat licencyjnych, podczas wybierania rozliczeń model i typ oferty.

Opcja publikowania Transact jest obecnie obsługiwana dla następujących typów oferty: Maszyny wirtualne, aplikacje platformy Azure i aplikacji SaaS.


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

* • Ceny subskrypcji: Opłaty za licencję oprogramowania są uporządkowane jako miesięczna lub roczna, cykliczne opłaty rozliczane jako stosowana jest stała stawka lub na stanowisko. Dotyczy to tylko aplikacji SaaS i aplikacje platformy Azure — aplikacje zarządzane przez.

* Bezpłatna wersja próbna oprogramowania: Brak opłat za licencje na oprogramowanie do 30 dni lub 90 dni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Bezpłatne i bring-your-own-license (BYOL) ceny

Podczas publikowania oferty bezpłatnej lub bring-your-own-license transakcji firmy Microsoft nie uczestniczy w ułatwienia transakcji sprzedaży dla Twojego opłat za licencje na oprogramowanie. Podobnie jak listy i wersji próbnej opcje publikowania wydawca zapewnia 100% opłat za licencje oprogramowania. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Płatność za rzeczywiste użycie i (oparty na lokacji) cennik subskrypcji

WPay co rzeczywistym i cennik subskrypcji, gdy publikowanie oferty transakcji płatności lub subskrypcji, firma Microsoft udostępnia technologii i usług do przetworzenia licencji na oprogramowanie zakupów, zwraca i obciążeń zwrotnych. W tym scenariuszu wydawcy upoważnia firmę Microsoft, aby pełnić rolę agenta do tych celów. Wydawcy umożliwia firmie Microsoft w celu ułatwienia licencjonowania transakcji przy zachowaniu ich oznaczenie jako sprzedawcy, dostawca, dystrybutora i licencjodawcy oprogramowania.

Microsoft umożliwia klientom zamówienia, licencji i korzystanie z oprogramowania wydawcy, poddanie warunków i postanowień, zarówno w portalu Marketplace handlowych firmy Microsoft, jak i Umowa licencyjna wydawcy. Wydawca musi zapewnić ich umowa licencyjna lub wybierz [standardowej umowy](https://docs.microsoft.com/azure/marketplace/standard-contract) podczas tworzenia oferty.


### <a name="free-software-trials"></a>Bezpłatne oprogramowanie próbne

Dla języka transact scenariuszy publikowania, wprowadzić wydawcy oprogramowania licencji dostępnych bezpłatnie przez 30 dni lub 90 dni. Ta funkcja licząc nie obejmuje koszt użycia infrastruktury platformy Azure, które są sterowane przy użyciu rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz używania oferuje typy i model do rozliczeń wprowadzanie do obiegu oferty, wydawcy mogą korzystać z oferty prywatne, wraz z wynegocjowanym, specyficzne dla transakcji ceny i konfiguracje niestandardowe. Oferty prywatne są obsługiwane przez wszystkie 3 transact opcje publikowania.

Ta opcja umożliwia wyższej lub niższej ceny niż publicznie dostępne oferty. Oferty prywatne może służyć do rabatu, lub Dodaj premium dla oferty. Oferty prywatne mogą dostępne jednego lub więcej klientów przez biały listę swoich subskrypcji platformy Azure na poziomie oferty.


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

**Subskrypcję aplikacji SaaS**

Ta opcja musi być skonfigurowany do sprzedaży firmy Microsoft i mogą są wyceniane na podstawie stałej stawki lub dla poszczególnych użytkowników na podstawie miesięcznej lub rocznej.
• Jeśli włączysz sprzedaży przy użyciu opcji Microsoft skorzystania z oferty SaaS, a następnie mają następującą strukturę kosztów.

|Koszt licencji       | 100,00 USD na miesiąc  |
|--------------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    | Rozliczane bezpośrednio z wydawcą zostało nawiązane nie klienta |
|*Rozliczenie przez firmę Microsoft*    |  *100,00 USD miesięcznie (Uwaga: wydawcy muszą uwzględniać żadnych kosztów infrastruktury poniesionych lub przekazywania w opłacie licencyjnej)*  |

* W tym scenariuszu firma Microsoft rozlicza 100,00 USD dla licencji na oprogramowanie i płaci się $80,00 z wydawcą zostało nawiązane.
* Partnerzy, którzy mają kwalifikowanych dla zmniejszona za usługę portalu Marketplace, zobaczą opłaty mniejsze transakcji na SaaS oferuje od maja 2019 aż do 2020 czerwca. W tym scenariuszu firma Microsoft rozlicza 100,00 USD dla licencji na oprogramowanie i płaci się $90.00 z wydawcą zostało nawiązane.

|W ramach firmy Microsoft  | 100,00 USD na miesiąc  |
|---------|---------|
|Microsoft płaci 80% kosztów licencji <br> \* Microsoft płaci możesz 90% firm z kosztami licencji dla aplikacji SaaS kwalifikowana   |   $80,00 na miesiąc <br> \* $90.00 na miesiąc    |
|Firma Microsoft zachowuje 20% przychodów kosztami licencji <br> \* Firma Microsoft zachowuje 10% koszt licencji dla aplikacji SaaS kwalifikowaną.  |  20,00 USD za na miesiąc <br> \* $10.00     |

* **Opłata za usługę Marketplace zmniejszenie:** Dla niektórych produktów SaaS publikowanej na rynku komercyjnych, Microsoft zmniejsza jego opłata za usługę portalu Marketplace z 20% (zgodnie z opisem w umowie wydawcy portalu Microsoft) 10%.  Aby produktu, aby zakwalifikować się, co najmniej jeden z Twoimi produktami musi zostać wyznaczona przez firmę Microsoft jako adresu IP gotowość do współpracy sprzedażowej lub IP sprzedażową uszeregowane według priorytetów. Aby otrzymać to zmniejszenie opłata za usługę portalu Marketplace na miesiąc, uprawnienia muszą być spełnione co najmniej pięciu (5) dni roboczych przed zakończeniem w danym miesiącu kalendarzowym. Zmniejszona usługę portalu Marketplace, opłata nie dotyczą maszyn wirtualnych, aplikacji zarządzanych przez lub innych produktów, udostępniane za pośrednictwem rynku komercyjnych.  To zmniejszyć Marketplace opłata za usługę będzie można kwalifikowaną oferty z opłaty za licencje na zbieranych przez firmę Microsoft, między 1 maja 2019 a 30 czerwca 2020 r.  Po upływie tego czasu opłaty za usługę portalu Marketplace powróci do jego normalnej kwoty.

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
>Raportowanie i szczegółowe informacje transact opcji publikowania są dostępne za pośrednictwem sekcji szczegółowych informacji w portalu Cloud Partner lub analizy części Centrum partnerskiego.

#### <a name="billing-questions-and-support"></a>Pytania dotyczące rozliczeń i pomocy technicznej

Aby uzyskać więcej informacji i prawne zasad, zobacz [umowie wydawcy portalu](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (dostępne w portalu Cloud Partner).

Aby uzyskać pomoc dotyczącą rozliczeń pytania, skontaktuj się z [pomocy technicznej wydawcy komercyjnych marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Wymagania dotyczące transakcji

W tej sekcji znajdują się wymagania transact dla typów inną ofertę.

### <a name="requirements-for-all-offer-types"></a>Wymagania dotyczące wszystkich oferuje typy

- Konta Microsoft i informacji finansowych wymagane przez transact publikowania opcji, niezależnie od tego, oferty w modelu cenowym.
- Obowiązkowe informacji finansowych zawiera informacje dotyczące wypłat profilu konta i podatków.

Aby uzyskać więcej informacji na temat konfigurowania tych kont, zobacz [zarządzania partnera Centrum konta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Wymagania dotyczące określonych typów ofert

Opcja publikowania transact jest dostępne tylko do użytku z następujących typów ofert portalu marketplace: 

**Maszyna wirtualna** 

Wybierz z bezpłatnej, bring-your-own-license lub płatności-zgodnie-you-rzeczywistym-pricing modeli, a następnie prezentować w postaci zdefiniowane na poziomie oferty jednostki SKU. Na rachunku za platformę Azure przez klienta firmy Microsoft przedstawia informacje o wydawcy opłat za licencje na oprogramowanie niezależnie od opłaty podstawowej infrastruktury platformy Azure. Opłaty za infrastrukturę platformy Azure są sterowane za korzystanie z oprogramowania wydawcy.

**Aplikacje platformy Azure: Szablon rozwiązania usługi lub aplikacji zarządzanej** 

Należy zarezerwować maszyny wirtualne lub ściąga za pośrednictwem sumę cen maszyn wirtualnych. Dla zarządzanych aplikacji w ramach jednego planu jednolitą stawkę miesięcznej subskrypcji można wybrać jako ceny modelu w zamian cen maszyn wirtualnych. W niektórych przypadkach opłaty za użycie infrastruktury platformy Azure są przekazywane do klienta oddzielnie od opłat licencyjnych oprogramowania, ale w tym samym zestawienie rozliczeniowe. Jeśli jednak skonfigurujesz aplikację Managed przeznaczone dla niezależnych dostawców oprogramowania opłaty związane z infrastrukturą, zasobów platformy Azure są rozliczane z wydawcą zostało nawiązane, i klient otrzymuje obowiązuje stała opłata obejmuje koszt infrastruktury, licencji na oprogramowanie i usługi zarządzania.

## <a name="next-steps"></a>Kolejne kroki

* Należy przejrzeć wymagania kwalifikacyjne w opcje publikowania przez sekcję typ oferty w celu sfinalizowania wyborem i konfiguracją, oferty.
* Przejrzyj wzorców publikowania w sklepie przykłady w sposób swoje rozwiązanie mapowania typu oferty i konfiguracji.
