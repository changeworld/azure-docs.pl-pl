---
title: "Plan obsługi - automatyzacji aplikacji sieci Web FedRAMP - zabezpieczeń platformy Azure i zgodność"
description: "Automatyzacja aplikacji sieci Web FedRAMP — konserwacji"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2018
---
# <a name="maintenance-ma"></a>Konserwacji (MA)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-ma-1"></a>Formant MA NIST 800 53-1

#### <a name="system-maintenance-policy-and-procedures"></a>Zasady konserwacji systemu i procedur

**MA 1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról], którego dotyczy cel, zakres, ról, obowiązki zobowiązań zarządzania, koordynacji zasad konserwacji systemu wśród jednostki organizacyjnej i zgodności; i procedury w celu ułatwienia implementacji zasad konserwacji systemu i skojarzonego z nim systemu obsługi kontroli; przegląda i aktualizuje bieżące zasady konserwacji systemu [przypisania: zdefiniowane przez organizację częstotliwość]; i procedury obsługi systemu [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Zasady konserwacji systemu na poziomie przedsiębiorstwa i procedury klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-2a"></a>Formant MA NIST 800 53-2.a

#### <a name="controlled-maintenance"></a>Kontrolowane konserwacji

**MA 2.a** organizacja planuje, wykonuje dokumentów i monitoruje rekordów konserwacji i naprawy składników systemu informacji zgodnie z specyfikacji producenta lub dostawcy i/lub wymagań organizacyjnych.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za kontrolowane konserwacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-2b"></a>NIST 800-53 Control MA-2.b

#### <a name="controlled-maintenance"></a>Kontrolowane konserwacji

**MA 2.b** organizacji zatwierdza i monitoruje wszystkie działania konserwacji, czy wykonać w witrynie lub zdalnie oraz tego, czy urządzenie jest obsługiwane w lokacji lub usunięty w innej lokalizacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za kontrolowane konserwacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-2c"></a>Formant MA NIST 800 53-2.c

#### <a name="controlled-maintenance"></a>Kontrolowane konserwacji

**MA 2.c** organizacja wymaga, aby [przypisania: techniczną zdefiniowany przez organizację lub ról] jawnie potwierdzenie usunięcia systemu informacje lub składników systemu z urządzeń do organizacji lub poza nim konserwacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Platforma Microsoft Azure wymaga tej właściwości zasobów (np. urządzenia sieciowego lub serwera) konieczności transferu poza siedzibą firmy mają zatwierdzenia przez właściciela zasobów jawnego. |


 ## <a name="nist-800-53-control-ma-2d"></a>NIST 800-53 Control MA-2.d

#### <a name="controlled-maintenance"></a>Kontrolowane konserwacji

**MA 2.d** organizacji oczyszcza urządzenia do usuwania wszystkich informacji z nośnika skojarzony przed usunięciem z urządzeń do organizacji lub poza nim konserwacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure zasobów ochrony standardowe definiuje zasobów obsługi kroków wymaganych do przeniesienia poza siedzibą firmy zasobów. Standardowa ochrony zasobów wymaga zasobów magazynu danych wyczyszczone/usunięte w sposób zgodny z dodatkiem SP NIST 800-88, wskazówki dotyczące ich oczyszczania nośnika, przed opuszczeniem centrum danych. |


 ## <a name="nist-800-53-control-ma-2e"></a>Formant MA NIST 800 53-2.e

#### <a name="controlled-maintenance"></a>Kontrolowane konserwacji

**MA-2.e** Organizacja sprawdza wszystkie potencjalnie wpływ na zabezpieczenia, aby sprawdzić, czy formanty nadal działają poprawnie następujące akcje konserwacji lub naprawy.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za kontrolowane konserwacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-2f"></a>Formant MA NIST 800 53-2.f

#### <a name="controlled-maintenance"></a>Kontrolowane konserwacji

**MA 2.f** obejmuje organizacji [przypisania: zdefiniowane przez organizację informacje dotyczące konserwacji] w rekordach organizacji konserwacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za kontrolowane konserwacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ma-2-2a"></a>Tj NIST 800 53 sterowania Zasilaniem-2 (2).

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Kontrolowane konserwacji | Działania automatyczne konserwacji

**.A MA-2 (2)** organizacja stosuje automatycznych mechanizmy harmonogramu, postępowania i konserwacji dokumentu i naprawy.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za automatyzację obsługi działań. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ma-2-2b"></a>NIST 800 53 b sterowania Zasilaniem-2 (2)

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Kontrolowane konserwacji | Działania automatyczne konserwacji

**(2) MA-2, b** organizacji tworzy o daty, dokładne, i rekordów ukończenia wszystkich działań konserwacji i napraw zażądał zaplanowane w procesie oraz zostać zakończona.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za automatyzację obsługi działań. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-3"></a>Formant MA NIST 800 53-3

#### <a name="maintenance-tools"></a>Narzędzia do konserwacji

**MA 3** organizacji zatwierdza kontrolek i monitoruje informacje narzędzia do konserwacji systemu.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure korzysta kilka narzędzi obsługi. Konserwacja oprogramowania zatwierdzone, kontrolowane i obsługiwane za pomocą Microsoft Azure tools zmienić i zwolnij procesu. <br /> Zespół usługi lokacji przechowuje spis narzędzi konserwacji zatwierdzone do użycia w centrum danych (patrz MA 3). Personelu obsługi są przekierowywane do korzystania z narzędzi konserwacji podana. Zatwierdzenie zarządzania Centrum danych jest wymagane do używania narzędzia nie został dostarczony przez centrum danych. Narzędzia ręczne fizycznych (śrubokręty, ręczne itp.) są wykluczone z tego formantu. <br /> Każde urządzenie zawiera pole ograniczone blokady fizycznego lub pod kontrolą dostępu do miejsca do przechowywania narzędzi specjalne konserwacji, takich jak zakresy ether firmy fluke firmy fluke fiber channel testerów, Ethernet tonery itp. Zespół usługi lokacji sprawdza rutynowych spisu Aby sprawdzić stan wszystkich narzędzi. Dostęp do blokowania pomieszczenia pola lub konserwacji są śledzone w dziennikach czytnika wskaźnika dostępu, które są dostępne w przypadku postępowania. |


 ### <a name="nist-800-53-control-ma-3-1"></a>Formant MA NIST 800 53-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Narzędzia do konserwacji | Narzędzia inspekcji

**(1) MA-3** organizacja kontroluje narzędzi konserwacji odbywa się w zakładzie personelowi obsługi do nieprawidłowej lub nieautoryzowane zmiany.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Zespołu usług witryny Microsoft Azure obsługuje spis narzędzi konserwacji zatwierdzone do użycia w centrum danych (zobacz MA 3 szczegółowe). Personelu obsługi są przekierowywane do korzystania z narzędzi konserwacji podana. DCM zatwierdzenie jest wymagane do używania narzędzia nie został dostarczony przez centrum danych. |


 ### <a name="nist-800-53-control-ma-3-2"></a>Formant MA NIST 800 53-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Narzędzia do konserwacji | Sprawdź nośnika

**(2) MA-3** organizacji nośników zawierających diagnostycznych i programy badania złośliwego kodu przed sprawdza nośniki są używane w systemie informacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie pozwalają na korzystanie z komputerów przenośnych lub nośników magazynowania w środowisku produkcyjnym centrów danych Microsoft Azure bez zatwierdzenia zarządzania Centrum danych. Korzystanie z prywatnych nośnika jest zabronione użycia w środowisku produkcyjnym centrów danych Microsoft Azure. <br /> Microsoft Azure została zaimplementowana procesu do zbadania laptopów przed ich użyciem w środowisku produkcyjnym centrów danych Microsoft Azure. Funkcjonariusze są uczone zażąda pracowników, aby sprawdzić, czy komputery przenośne poddane i przekazany kontroli przy użyciu komputerów przenośnych w środowisku produkcyjnym. |


 ### <a name="nist-800-53-control-ma-3-3"></a>Formant MA NIST 800 53-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Narzędzia do konserwacji | Zapobiec usunięciu nieautoryzowanego

**(3) MA-3** organizacji uniemożliwia nieautoryzowanym usuwania urządzeń do konserwacji zawierający informacje organizacyjne, upewniając się, że nie istnieje organizacji informacji znajdujących się na sprzęt; oczyszczania lub niszczenie urządzenia; zachowywanie urządzeń w obrębie obiektu; lub uzyskiwanie zwolnienia z [przypisania: techniczną zdefiniowany przez organizację lub ról] jawnie autoryzowanie usunięcie urządzenia z funkcji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure wykorzystuje narzędzia obsługi określonego centrum danych, które są zachowywane w obrębie obiektu i nie zostaną usunięte. Każde urządzenie zawiera pomieszczenie ograniczeniami blokady fizycznej pola lub magazynu, zawierający narzędzia do konserwacji, takich jak zakresy ether firmy fluke firmy fluke fiber channel testerów, Ethernet tonery itp. Dostęp jest kontrolowany do pomieszczenia pola lub magazynu blokady w DCAT zakazać nieautoryzowanego dostępu do narzędzi konserwacji. <br /> Informacje organizacyjne jest chroniony podczas konserwacji formantów w MA 4. Aby uzyskać dostęp do informacji organizacyjnych, użytkownik ma uprzywilejowany konta i wystawcy uwierzytelnienia. |


 ## <a name="nist-800-53-control-ma-4a"></a>Formant MA NIST 800 53-4.a

#### <a name="nonlocal-maintenance"></a>Nielokalne konserwacji

**MA 4.a** organizacji zatwierdza i monitoruje nielokalne obsługi i działania diagnostyczne.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za konserwacja nie lokalnego w systemach operacyjnych wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-4b"></a>Formant MA NIST 800 53-4.b

#### <a name="nonlocal-maintenance"></a>Nielokalne konserwacji

**MA 4.b** organizacji zezwala na korzystanie z obsługi nielokalne i diagnostycznych narzędzi tylko jako zgodne z zasadami organizacji i opisane w sekcji Planowanie zabezpieczeń systemu informacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za konserwacja nie lokalnego w systemach operacyjnych wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-4c"></a>NIST 800-53 Control MA-4.c

#### <a name="nonlocal-maintenance"></a>Nielokalne konserwacji

**MA 4.c** silne wystawców uwierzytelnienia w zakładzie nielokalne konserwacji i diagnostycznych sesji jest stosowana w organizacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za konserwacja nie lokalnego w systemach operacyjnych wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-4d"></a>Formant MA NIST 800 53-4.d

#### <a name="nonlocal-maintenance"></a>Nielokalne konserwacji

**MA 4.d** organizacji przechowuje rekordy nielokalne konserwacji i diagnostycznych działań.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za konserwacja nie lokalnego w systemach operacyjnych wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-4e"></a>Formant MA NIST 800 53-4.e

#### <a name="nonlocal-maintenance"></a>Nielokalne konserwacji

**MA-4.e** Organizacja kończy sesji i połączenia sieciowe, po zakończeniu obsługi nielokalnych.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za konserwacja nie lokalnego w systemach operacyjnych wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ma-4-2"></a>Formant MA NIST 800 53-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Nielokalne konserwacji | Konserwacja nielokalne dokumentu

**(2) MA-4** dokumentów organizacji w sekcji Planowanie zabezpieczeń systemu informacji zasady i procedury dotyczące tworzenia i wykorzystania nielokalne konserwacji i diagnostycznych połączenia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za dokumentację konserwacji innego niż lokalne w planie zabezpieczeń dla systemów operacyjnych wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ma-4-3"></a>Formant MA NIST 800 53-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Nielokalne konserwacji | Można porównywać pod względem zabezpieczeń / ich oczyszczania

**(3) MA-4** organizacja wymaga nielokalne konserwacji i usługi diagnostyczne wykonanie z systemu informacji, który implementuje porównywalna możliwości zaimplementowana w systemie są obsługiwane; możliwości zabezpieczeń lub usuwa składnik, który będzie obsługiwany w systemie informacji przed nielokalne konserwacji lub usługi diagnostyczne oczyszcza składnika (w odniesieniu do informacji organizacyjnych) przed usunięcie z urządzeń w organizacji, a po usługi wykonywane, bada i oczyszcza składnika (względem potencjalnie złośliwych programów) przed ponownym połączeniem składnika do systemu informacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za konserwacja wszystkich nielokalnych wdrożonych przez klienta systemów operacyjnych z systemu informacje można porównywać pod względem zabezpieczeń. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ma-4-6"></a>Formant MA NIST 800 53-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Nielokalne konserwacji | Ochrona usług kryptograficznych

**MA-4 (6)** system informacji implementuje mechanizmów kryptograficznych do ochrony integralności i poufności nielokalne konserwacji i diagnostycznych komunikacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wdrażanie mechanizmów kryptograficznych podczas przeprowadzania konserwacji innego niż lokalne i informacji diagnostycznych wdrożonych przez klienta systemów operacyjnych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-5a"></a>Formant MA NIST 800 53-5.a

#### <a name="maintenance-personnel"></a>Personel konserwacji

**MA 5.a** ustanawia proces autoryzacji personelu obsługi i przechowuje listę autoryzowanych konserwacji organizacji lub personel organizacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klienta przedsiębiorstw konserwacji personelu autoryzacji i konwojowanie procesy systemowe mogą być wystarczająca do adresów tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-5b"></a>Formant MA NIST 800 53-5.b

#### <a name="maintenance-personnel"></a>Personel konserwacji

**MA 5.b** organizacji gwarantuje, że personel eskortowanych konserwacja systemu informacje mają odpowiednie zezwolenia na dostęp do.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klienta przedsiębiorstw konserwacji personelu autoryzacji i konwojowanie procesy systemowe mogą być wystarczająca do adresów tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-5c"></a>Formant MA NIST 800 53-5.c

#### <a name="maintenance-personnel"></a>Personel konserwacji

**MA 5.c** organizacji wyznacza organizacji pracowników z zezwoleń wymagane prawa dostępu i kwalifikacji technicznych nadzorowanie działania obsługi technicznej, którzy nie mają zezwolenia wymagany dostęp.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klienta przedsiębiorstw konserwacji personelu autoryzacji i konwojowanie procesy systemowe mogą być wystarczająca do adresów tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ma-5-1a"></a>Tj NIST 800 53 sterowania Zasilaniem-5 (1).

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personel konserwacji | Osoby bez odpowiedniego dostępu

**(1) MA-5 .a** organizacji implementuje procedury wykorzystania personelu obsługi, które nie mają odpowiednich niedopuszczających lub obywateli Stanów Zjednoczonych, które obejmują następujące personel obsługi wymagań, który nie ma potrzeby nie są zezwolenia na dostęp, prześwity lub posiadanie dostępu zatwierdzeń są eskortowanych i nadzorowane podczas wykonywania konserwacji i diagnostycznych działania w systemie informacji przez zatwierdzonych organizacji pracowników, którzy są w pełni wyczyszczone, mają odpowiednie autoryzacji dostępu i są technicznie kwalifikowana; przed inicjujący konserwacji lub czynności diagnostycznych przez personel kto nie mieć wymaganych autoryzacji dostępu, zezwoleń lub zatwierdzenia posiadanie dostępu, zmienne informacje się, że składniki magazynu w ramach systemu informacje są oczyszczony i wszystkie nieulotnej nośniki są usuwane lub fizycznie odłączone od systemu i zabezpieczone.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klienta przedsiębiorstw konserwacji personelu autoryzacji i konwojowanie procesy systemowe mogą być wystarczająca do adresów tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ma-5-1b"></a>NIST 800 53 b sterowania Zasilaniem-5 (1)

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personel konserwacji | Osoby bez odpowiedniego dostępu

**(1) MA-5, b** rozwoju organizacji i implementuje alternatywne zabezpieczeń, w przypadku informacji składnik systemu nie może być oczyszczony, usunięte lub odłączone od systemu.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klienta przedsiębiorstw konserwacji personelu autoryzacji i konwojowanie procesy systemowe mogą być wystarczająca do adresów tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ma-6"></a>Formant MA NIST 800 53-6

#### <a name="timely-maintenance"></a>Czas konserwacji

**MA 6** organizacji uzyskuje obsługi pomocy technicznej i/lub zapasowego części dla [przypisania: składniki systemowe zdefiniowane przez organizację informacje] w [przypisania: okres zdefiniowany organizacji] awarii.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure w centrach danych Obsługa rezydentna pracowników do obsługi datacenter krytyczne systemy infrastruktury, a także operacji centrum danych. Zespoły zidentyfikowano krytycznych składników systemu zabezpieczeń i technologii dysków zapasowych, które zachowują dla siedzibie organizacji. Krytyczne systemy są zaprojektowane w N + 1 konfiguracje i usługi mają na celu zapewnienie odporności. Dzięki temu spełnia cele odzyskiwania w przypadku przerw w obsłudze lub sytuacji plan gotowości zespołu zarządzania Centrum danych. Usługi systemowe kluczowych informacji obsługi administracyjnej z więcej niż jednego centrum danych, aby uniknąć przerwy w działaniu usługi z powodu zdarzenia w jednym centrach danych. Aplikacje odbiorcy są zobowiązani do wdrażania w wielu centrach danych, aby zapewnić nadmiarowość i odporność. |
