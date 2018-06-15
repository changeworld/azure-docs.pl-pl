---
title: UK oficjalne trójwarstwowa automatyzacji aplikacji sieci Web — Omówienie
description: UK oficjalne trójwarstwowa automatyzacji aplikacji sieci Web — Omówienie
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 787f504dea6e98911d6ba4716e88aff322c70c5b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151382"
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Omówienie zasad zabezpieczeń chmury Centrum zabezpieczeń przez National


> [!NOTE]
> Te zasady zabezpieczeń są definiowane przez UK National przez zabezpieczeń Centrum (NCSC). Zapoznaj się [dokumentacji NCSC](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) informacji na temat testowania procedury i wskazówki dotyczące każdej zasady zabezpieczeń.



## <a name="ncsc-cloud-security-principle-1"></a>NCSC Cloud Security Principle 1
### <a name="data-in-transit-protection"></a>Dane w przypadku przesyłania ochrony
Dane użytkownika przejeżdżających przez sieci należy zabezpieczone przed naruszeniem i podsłuchiwaniu.

Należy to osiągnąć przy użyciu kombinacji:

- Ochrona sieci - odmawianie użytkownika osoba atakująca możliwości przechwycenia danych
- szyfrowanie - odmawianie atakująca sieci pozwala na odczyt danych


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Ten plan konfiguruje zasoby do komunikowania się przy użyciu protokołów tylko na zabezpieczone. Składnik zapory aplikacji sieci Web, bramy aplikacji został skonfigurowany do akceptowania communicators z zastosowań zewnętrznych za pośrednictwem protokołu HTTPS/TLS i komunikować się z puli zaplecza tylko za pośrednictwem protokołu HTTPS/TLS. Usługi pulpitu zdalnego są skonfigurowane do używania bezpiecznych połączeń. Sieć VPN jest używany do zabezpieczania ruchu w sieci web między AppGateway i platformą Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure używa standardowych protokołów zabezpieczeń TLS (Transport Layer) 1.2 z kluczami szyfrowania RSA/SHA256 2048-bitowego, zgodnie z zaleceniami CESG/NCSC do szyfrowania komunikacji zarówno między chmurą a klienta, jak i wewnętrznie między systemami Azure i centrów danych. Na przykład gdy Administratorzy korzystanie z portalu Microsoft Azure do zarządzania usługą dla organizacji, dane przesyłane między portalem i administratora urządzenia są wysyłane za pośrednictwem kanału szyfrowanego protokołu TLS. W przypadku wiadomości e-mail łączenia się użytkownika z usługi Outlook.com za pomocą przeglądarki sieci web standard, połączenia HTTPS zapewnia bezpieczny kanał odbieranie i wysyłanie wiadomości e-mail.<br /> <br /> System Azure oferuje klientom gamę opcji zabezpieczania własnych danych i ruchu. Certyfikat funkcji zarządzania wbudowanych w platformy Azure pozwala administratorom elastyczność konfigurowania certyfikatów i kluczy szyfrowania dla systemów zarządzania, poszczególnych usług, sesje bezpiecznej powłoki (SSH), połączenia wirtualnej sieci prywatnej (VPN) Podłączanie pulpitu zdalnego (RDP), a inne funkcje. <br /><br /> Deweloperzy mogą używać dostawcy usług kryptograficznych (CSP), wbudowane w system Microsoft .NET Framework do dostępu algorytmy szyfrowania AES (Advanced Standard), wraz z Secure Hash Algorithm (SHA-2) funkcje do obsługi zadań takich jak sprawdzanie poprawności cyfrowych podpisy. Usługa Azure Key Vault ułatwia klientom ochrony kluczy kryptograficznych i kluczy tajnych przez zapisanie ich w sprzętowych modułów zabezpieczeń (HSM). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Zasady zabezpieczeń chmury NCSC 2
### <a name="asset-protection-and-resilience"></a>Ochrona zasobów i odporności
Dane użytkownika i zasoby, przechowywania i przetwarzania, powinny być chronione przed dostępem fizycznym, utrata lub uszkodzenie zajęcia.

Aspektów, które należy wziąć pod uwagę są:

1. Lokalizacji fizycznej, a właściwość prawnych
2. Bezpieczeństwo centrów danych
3. Dane na ochronę Rest
4. Ich oczyszczania danych
5. Usuwanie urządzenia
6. Odporność na fizyczne i dostępności


 ## <a name="ncsc-cloud-security-principle-21"></a>NCSC Cloud Security Principle 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>Lokalizacji fizycznej, a właściwość prawnych
Aby zrozumieć prawne okoliczności, w których dane mogą być używane bez zgody użytkownika należy zidentyfikować lokalizacje, w których jest przechowywana, przetwarzane i zarządzany.
Należy również należy zrozumieć kontroli sposobu obsługi danych w ramach usługi są wymuszane, względem ustawodawstwa Wielka Brytania. Niewłaściwe ochrony danych użytkownika może skutkować oficjalnie akceptować prawne i przepisów prawnych lub reputational uszkodzenia.


**Obowiązki:**`Customer`

> [!NOTE]
> Usług platformy Azure są wdrażane regionalnie i klienci mogą skonfigurować niektórych usług platformy Azure do przechowywania danych klienta tylko w pojedynczym regionie. Microsoft Azure zawiera listę dostępnego globalnie centrach danych, aby zapewnić dostępność i niezawodność w skali globalnej. Potwierdzone wszystkie centrach danych platformy Azure z normą ISO/IEC 27001: 2013. UK Geo składa się z 2 regionów: Wielka Brytania Południowa i Zjednoczone Królestwo zachodnie.

|||
|---|---|
| **Klienta** | Ten plan monituje o administratora dla regionu, którego wdrażania zasobów platformy Azure. Wielka Brytania Południowa lub Wielka Brytania Zachodnia są zalecane regiony dla wdrożenia. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Nie dotyczy |


 ## <a name="ncsc-cloud-security-principle-22"></a>NCSC Cloud Security Principle 2.2
### <a name="datacenter-security"></a>Bezpieczeństwo centrów danych
Lokalizacje używane w celu dostarczania usługi w chmurze muszą fizycznej ochrony przed nieautoryzowanym dostępem, naruszeniu, kradzieży lub ponownej konfiguracji systemów. Nieodpowiednie ustawienia zabezpieczeń może spowodować ujawnienie, zmiany lub utraty danych.


**Obowiązki:**`Microsoft Azure`


|||
|---|---|
| **Klienta** | Klienci nie mają fizyczny dostęp do zasobów systemu w centrach danych platformy Azure; środków ochronnych zabezpieczeń w centrum danych są wdrożone i zarządzane przez Microsoft Azure. Ta zasada jest odziedziczone Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje tę zasadę w imieniu klientów. Microsoft Azure działa w obiektach Microsoft geograficznie rozproszonych, miejsca i narzędzia do udostępniania z innych usług online firmy Microsoft. Każde urządzenie jest przeznaczony do uruchamiania 24 x 7 x 365, używa różnych mechanizmów branżowymi w celu ochrony operacji przed awarii zasilania, fizycznych nieautoryzowanego dostępu i awarie sieci. Tych centrach danych są zgodne ze standardami branżowymi (na przykład ISO 27001) dla zabezpieczeń fizycznych i dostępności. Są zarządzane, monitorowany i zarządzany przez personel firmy Microsoft. <br /> <br /> Klientów platformy Azure można mieć pewność, że kontroli zabezpieczeń fizycznych zostały spełnione przez cały Azure centrów danych z powodu Azure zawierający certyfikatów na wszystkich centrów danych dla normy ISO/IEC 27001: 2013. Wielka Brytania Geo składa się z dwóch regionach: Wielka Brytania Południowa i Zjednoczone Królestwo zachodnie. |


 ## <a name="ncsc-cloud-security-principle-23"></a>NCSC Cloud Security Principle 2.3
### <a name="data-at-rest-protection"></a>Dane na ochronę Rest
Aby upewnić się, że dane nie są dostępne dla osób niepowołanych mający fizyczny dostęp do infrastruktury, dane użytkownika przechowywane w ramach usługi powinny być chronione, bez względu na nośnikach magazynowania, na którym jest przechowywany. Bez odpowiednie środki w miejscu dane mogą zostać przypadkowo ujawnione na nośniku odrzuconych, utraty lub kradzieży.


**Obowiązki:**`Shared`

> [!NOTE]
> Wszystkie rozwiązania szyfrowania, które umożliwia klientom łatwe Microsoft Azure zintegrować z usługą Azure Key Vault, co umożliwia łatwe zarządzanie kluczy szyfrowania.

|||
|---|---|
| **Klienta** | Poufność i integralność wszystkich magazynu obiektów blob wdrażane przez to rozwiązanie plan jest chroniony przy użyciu szyfrowania usługi Azure magazynu (SSE). SSE zabezpiecza dane przechowywane w ramach konta magazynu platformy Azure przy użyciu szyfrowania AES 256-bitowego. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | System Azure oferuje szeroką gamę możliwości szyfrowania, zapewniając elastyczność wyboru rozwiązania, która najlepiej spełnia ich wymagania dotyczące klientów. Usługa Azure Key Vault pomaga klientom łatwe i kosztów skutecznie zachować kontrolę nad kluczy używanych przez usługi i aplikacje w chmurze do szyfrowania danych. Szyfrowanie dysków Azure umożliwia klientom zaszyfrować maszyny wirtualne. Szyfrowanie usługi Magazyn Azure umożliwia szyfrowanie wszystkich danych umieszczane konta magazynu klienta. |


 ## <a name="ncsc-cloud-security-principle-24"></a>NCSC Cloud Security Principle 2.4
### <a name="data-sanitization"></a>Ich oczyszczania danych
Proces inicjowania obsługi administracyjnej, migracji i usuwania inicjowania obsługi administracyjnej zasobów powinien spowoduje nieautoryzowany dostęp do danych użytkownika.

Nieodpowiednie ich oczyszczania danych może spowodować:

- Dane użytkownika przechowywane przez nieograniczony czas przez dostawcę usług
- Dane użytkownika są dostępne dla innych użytkowników usługi, ponieważ zasoby są ponownie
- Dane użytkownika zostanie utracone lub ujawnione na nośniku odrzuconych, utraty lub kradzieży.


**Obowiązki:**`Microsoft Azure`


|||
|---|---|
| **Klienta** | Microsoft Azure udostępnia umownymi zapewnienia dotyczące usunięcia trwałych danych na platformie Azure. W efekcie tej zasady jest odziedziczone Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure wykonuje proces usuwania NIST SP800-88r1 z klasyfikacją danych do umiarkowane FIPS 199. NIST zapewnia bezpieczny wymazać podejście (za pomocą oprogramowania układowego dysku twardego) dla dysków, które obsługują tę. W przypadku dysków twardych, które nie można wyczyścić Microsoft niszczy je i renderuje odzyskiwania informacji niemożliwe (np. rozpaść "Podrasowane", rozpylać albo spalania). Odpowiednie środki usuwania jest określana przez typ zasobu. Rejestruje zniszczenie zostaną zachowane. Wszystkich usług Microsoft Azure wykorzystują zatwierdzone nośnika przechowywania i usuwania usługi zarządzania. <br />  <br /> Wygaśnięcia lub zakończenia subskrypcji usługi, klient może kontakt z firmą Microsoft i poinformuj go, czy: <br /><br /> (1) Wyłącz konta klienta, a następnie usuń dane klienta; lub <br /> (2) zachowane dane przechowywane w dokumentacji online usługi na koncie funkcji ograniczonej przez co najmniej 90 dni, po wygaśnięciu lub zakończenia subskrypcji klienta ("okres przechowywania"), więc klienta może wyodrębnić dane. Po upływie okresu przechowywania firma Microsoft wyłączenie konta klienta i usunąć wszystkie dane. Buforowane lub kopie zapasowe zostaną usunięte w ciągu 30 dni od zakończenia okresu przechowywania. |


 ## <a name="ncsc-cloud-security-principle-25"></a>NCSC Cloud Security Principle 2.5
### <a name="equipment-disposal"></a>Usuwanie urządzenia
Gdy urządzenia używane w celu dostarczenia usługi dociera do końca okresu eksploatacji, powinny zostać usunięte z w taki sposób, aby nie naruszyć bezpieczeństwo usługi lub dane użytkownika przechowywane w usłudze.


**Obowiązki:**`Microsoft Azure`


|||
|---|---|
| **Klienta** | Klienci nie mają fizyczny dostęp do zasobów systemu w centrach danych platformy Azure; procedury usuwania urządzenia są wdrożone i zarządzane przez Microsoft Azure. Ta zasada jest odziedziczone Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje tę zasadę w imieniu klientów. Od systemu z wycofanych Microsoft personel operacyjny wykonaj danych rygorystycznych procedur i procesy usuwania sprzętu, aby zapewnić, że sprzęt nie może zawierać danych klienta jest udostępnione niezaufane. Microsoft Azure wykonuje proces usuwania NIST SP800-88r1 z klasyfikacją danych do umiarkowane FIPS 199. NIST zapewnia bezpieczny wymazać podejście (za pomocą oprogramowania układowego dysku twardego) dla dysków, które obsługują tę. W przypadku dysków twardych, które nie można wyczyścić Microsoft niszczy je i renderuje odzyskiwania informacji niemożliwe (np. rozpaść "Podrasowane", rozpylać albo spalania). Odpowiednie środki usuwania jest określana przez typ zasobu. Rejestruje zniszczenie zostaną zachowane. Wszystkich usług Microsoft Azure wykorzystują zatwierdzone nośnika przechowywania i usuwania usługi zarządzania. |


 ## <a name="ncsc-cloud-security-principle-26"></a>NCSC Cloud Security Principle 2.6
### <a name="physical-resilience-and-availability"></a>Odporność na fizyczne i dostępności
Usługi mają różne poziomy odporności, co wpływa na możliwość działa normalnie, w przypadku awarii lub zdarzeń ataków. Usługa bez gwarancji dostępności mogą stać się niedostępne, potencjalnie przez dłuższe okresy, niezależnie od wpływu na firmę.


**Obowiązki:**`Shared`

> [!NOTE]
> Jeśli klient skonfiguruje Microsoft Azure odpowiednio przez włączenie usługi Azure Site Recovery i alternatywne magazynu danych na inny geograficznie graficznie zlokalizować centrum danych, Microsoft Azure może obsługiwać operacji ciągłego zasobów wdrożonych przez klienta.

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za ustanawianie lokalizacji alternatywnej. Instrukcja implementacji kontroli klienta należy spełnić klienta możliwość pracy w przypadku zdarzenia. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure ma UK National przez zabezpieczeń Centrum (NCSC) zatwierdzonych centrów danych w różnych lokalizacjach geograficznych (Wielka Brytania Południowa i Zjednoczone Królestwo Zachodnie) w celu zapewnienia dostępności i odporności. Będzie ona odpowiedzialność klienta do zarezerwowania pojemności w regionie alternatywnego przy użyciu usługi Site Recovery platformy Azure. Po ich skonfigurowaniu usługi Azure Site Recovery Azure będzie uruchamianie i zatrzymywanie usług klienta w płynne przejście do lokalizacji alternatywnej przetwarzania. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Zasady zabezpieczeń chmury NCSC 3
### <a name="separation-between-users"></a>Rozdzielenie użytkowników
Złośliwe lub ze złamanymi zabezpieczeniami użytkownika usługi nie będą już mogli wpływ na usługi lub danych innego.

Czynniki wpływające na separacji użytkownika obejmują:

- gdzie są implementowane formanty separacji — silnie ma to wpływ modelu usług (np. IaaS, PaaS, SaaS)
- które współużytkują usługę za pomocą — ustawieniem modelu wdrażania (np. w chmurze publicznej, prywatnej lub społeczności)
- poziom pewności dostępne w implementacji separacji formantów

> [!NOTE]
> W usłudze IaaS należy rozważyć separacji podał obliczeniowych, magazynu i składników sieciowych. Ponadto usługi SaaS i PaaS, które są wbudowane w system IaaS mogą dziedziczyć niektórych właściwości separacji podstawowej infrastruktury IaaS.

**Obowiązki:**`Microsoft Azure`


|||
|---|---|
| **Klienta** | Microsoft Azure zapewnia izolację dla każdego użytkownika zapobiec wpływu na usługi lub danych innego jeden użytkownik złośliwego lub ze złamanymi zabezpieczeniami. W efekcie tej zasady jest odziedziczone Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Z powodu serwerów klienta w chmurze jest wirtualny modelu fizyczne rozdzielenie nie ma już zastosowania. Microsoft Azure został zaprojektowany do identyfikacji i licznika ryzyka związanego z używaniem w środowisku wielodostępnym. Magazyn danych i przetwarzania jest logicznie rozdzielone między użytkownikami platformy Azure przy użyciu usługi Active Directory i funkcję opracowane specjalnie dla wielodostępnej usług, która ma na celu zagwarantowania, że dane użytkownika przechowywane w udostępnionym centrach danych platformy Azure nie jest dostępny przez inną organizacja. <br /> <br /> Podstawowe do dowolnego architektura udostępnionej chmury jest izolacji podane dla każdego użytkownika zapobiec wpływu na usługi lub danych innego jeden użytkownik złośliwego lub ze złamanymi zabezpieczeniami. <br /> <br /> Aby uzyskać więcej informacji dotyczących separacji dzierżawy firmy Microsoft, zobacz pełny opis w [zabezpieczeń platformy Azure i macierzy obowiązki klienta planu zgodności - zasad zabezpieczeń chmury NCSC -](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Zasady zabezpieczeń chmury NCSC 4
### <a name="governance-framework"></a>Framework ładu
Dostawca usług ma strukturę zarządu zabezpieczeń, który koordynuje i kieruje jego zarządzania usługi i informacje w nim. Wszelkie kontroli technicznej wdrożona poza ta struktura będzie całkowicie naruszony.
Posiadanie framework efektywne zarządzanie zapewnia tej procedury, pracowników, mechanizmów kontroli fizycznej i technicznych nadal działać przez okres istnienia usługi. Powinien on także odpowiadanie na zmiany w usługa, opracowania i wyglądu nowych zagrożeń.


**Obowiązki:**`Microsoft Azure`


|||
|---|---|
| **Klienta** | Microsoft Azure obsługuje RAM ładu udokumentowane zabezpieczeń dla usług Azure. W efekcie tej zasady jest odziedziczone Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Struktura zgodności firmy Microsoft obejmuje standardowych metodologia Definiowanie domen zgodności, określanie celów, które dotyczą danego zespołu lub zasobów i przechwytywanie jak cele sterowania domeny są opisane szczegółowo, ponieważ mają one zastosowanie do Podany zestaw standardy branżowe, przepisami lub wymaganiami biznesowymi. Platformę mapy formantów wielu normami, co pozwala firmie Microsoft na projektowanie i tworzenie usług za pomocą wspólnego zestawu formantów, co optymalizuje zgodności w zakresie przepisów dzisiaj i ich ewolucji w przyszłości. <br /> <br /> Procesy zgodności Microsoft również ułatwić do zapewnienia zgodności w wielu usługach i ich potrzeb zmieniania wydajnie. Razem zwiększających zabezpieczenia technologii i procesy zgodności skuteczne umożliwiają firmie Microsoft utrzymanie i rozszerzenie bogaty zestaw certyfikaty innych firm. Te certyfikaty pomóc klientom pokazują zgodności gotowości do swoich klientów, audytorzy i organów nadzoru. <br /> <br />  Azure jest zgodna z szeroką gamę międzynarodowych oraz standardów zgodności regionalnego i branżowe, takie jak ISO 27001, FedRAMP SOC 1 i SOC 2. Zgodność z ograniczeniami zabezpieczeń formanty zawarte w tych standardów jest weryfikowany przez rygorystyczne inspekcji innych firm, które prezentacja usług Azure współpracować i spełniają standardy branżowe światowej klasy, certyfikaty, poświadczenia i zezwolenia. <br /> <br /> Azure zaprojektowano z strategii zgodności, która pomaga celów biznesowych adresów klientów oraz standardy branżowe i przepisami. Struktura zgodności zabezpieczeń obejmuje testu i inspekcji fazy, analizy zabezpieczeń najlepsze rozwiązania dotyczące zarządzania ryzykiem i analizy testu porównawczego zabezpieczeń w celu uzyskania certyfikatów i poświadczenia. <br /> <br /> Aby uzyskać więcej informacji dotyczących przestrzegania zgodności platform firmy Microsoft, zobacz pełny opis w [zabezpieczeń platformy Azure i macierzy obowiązki klienta planu zgodności - zasad zabezpieczeń chmury NCSC -](https://aka.ms/blueprintuk-gcrm).|


 ## <a name="ncsc-cloud-security-principle-5"></a>NCSC Cloud Security Principle 5
### <a name="operational-security"></a>Bezpieczeństwa operacyjnego
Usługa musi być obsługiwany i bezpiecznie zarządzane w celu utrudniać i zapobieganie atakom. Dobrym bezpieczeństwa operacyjnego nie powinny wymagać procesów do złożonych, zbiurokratyzowanym, czasochłonne i kosztowne.

Istnieją cztery elementy wziąć pod uwagę:

- Konfiguracja i zarządzanie zmianami — należy upewnić się czy zmiany w systemie zostały prawidłowo przetestowane i autoryzacji. Zmiany nie należy zmieniać nieoczekiwanie właściwości zabezpieczeń
- Zarządzanie luki w zabezpieczeniach - powinien identyfikowanie i usuwanie problemów z zabezpieczeniami w składnikach składowych
- Monitorowanie ochronnych - środków należy umieścić w celu wykrywania ataków i nieautoryzowane działanie usługi
- Zarządzanie zdarzeniami — upewnij się, można odpowiadanie na zdarzenia i odzyskiwać bezpieczne i dostępne usługi




 ## <a name="ncsc-cloud-security-principle-51"></a>NCSC Cloud Security Principle 5.1
### <a name="configuration-and-change-management"></a>Konfiguracja i zarządzanie zmianami
Powinien mieć dokładny obraz zasoby, które tworzą usługi, oraz ich konfiguracje i zależności.
Zmiany, które mogą mieć wpływ na zabezpieczenia usługi należy zidentyfikować i zarządzane. Nieautoryzowane zmiany powinny zostać wykryte.
W przypadku, gdy zmiana nie jest zarządzana skutecznie, luk w zabezpieczeniach mogą nieświadomie wprowadzone do usługi. A nawet w przypadku świadomości luki w zabezpieczeniach, jego może nie być całkowicie skorygowane.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Szablony usługi Azure Resource Manager i towarzyszące zasobów, które obejmuje ten plan reprezentują linii bazowej "Konfiguracja jako kod" dla architektury wdrożone. Rozwiązanie znajduje się jednak GitHub, która może służyć do konfiguracji kontroli. <br /> <br /> Azure Active Directory, konto, które uprawnienia są implementowane przy użyciu kontroli dostępu opartej na rolach, przypisując użytkowników do ról, zapewniając strict sterowania przez użytkowników, którzy mogą wyświetlać i kontroli wdrożonych zasobów. Uprawnienia konta w usłudze Active Directory są implementowane za pomocą kontroli dostępu opartej na rolach, przypisując użytkownikom dla grup zabezpieczeń. Te grupy zabezpieczeń kontrolować akcje wykonywane przez użytkowników względem konfiguracji systemu operacyjnego. Te systemy oparte na rolach można rozszerzyć przez klienta w celu spełnienia potrzeb misji. <br /> <br /> Aby było zgodne z tą zasadą, dalsze konfiguracja jest wymagana przez klienta do użycia w środowisku produkcyjnym. Tak te konfiguracje musi być częścią procesu zarządzania zmiany przez klienta. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure przegląda i aktualizuje ustawienia konfiguracji i linii bazowej konfiguracji urządzeń sprzętu, oprogramowania i sieci co roku. Zmiany są rozwinięte, przetestowane i zatwierdzone przed wprowadzeniem w środowisku produkcyjnym ze środowiska programowania i/lub testu. <br /> <br />Microsoft Azure stosuje linii bazowej konfiguracji za pomocą procesu zmiany i wersji dla składników oprogramowania Microsoft Azure (np. system operacyjny, sieci szkieletowej, frontonu REDDOG, XStore itp.) i proces ładowania początkowego konfigurowania sieci i składniki wprowadzania Microsoft Azure środowiska produkcyjnego w sposób opisany poniżej. <br /> <br /> Konfiguracje podstawowe wymagane dla usług opartych na platformie Azure są kontrolowane przez zespół Azure zabezpieczeń i zgodności i zespoły obsługi w ramach testowania przed wdrożeniem usługi ich produkcji. |


 ## <a name="ncsc-cloud-security-principle-52"></a>NCSC Cloud Security Principle 5.2
### <a name="vulnerability-management"></a>Zarządzanie luki w zabezpieczeniach
Dostawcy usług powinien mieć procesów zarządzania w celu identyfikowania, klasyfikowanie i usuwanie luk w zabezpieczeniach. Usługi, które nie, szybko będzie narażony na ataki przy użyciu metody publicznej i narzędzia.


**Obowiązki:**`Customer`


|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za luki w zabezpieczeniach skanowanie wdrożonych przez klienta zasobów (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). Instrukcja implementację klienta należy spełnić narzędzia służące do skanowania luki w zabezpieczeniach. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Zarządzanie aktualizacjami zabezpieczeń pomaga chronić systemy ze znanych luk w zabezpieczeniach. Platforma Azure zintegrowane wdrożenia systemy do zarządzania dystrybucji i instalowanie aktualizacji zabezpieczeń oprogramowania firmy Microsoft. Azure może także korzystać z zasobów z MSRC Microsoft Security Response Center (), który identyfikuje, monitoruje odpowiada i jest rozpoznawany jako przypadki naruszenia zabezpieczeń i w chmurze luk w zabezpieczeniach wokół zegara, każdego dnia roku. |


 ## <a name="ncsc-cloud-security-principle-53"></a>NCSC Cloud Security Principle 5.3
### <a name="protective-monitoring"></a>Monitorowanie ochrony
Usługi, które skutecznie nie monitoruje ataków, nieprawidłowego użycia i nieprawidłowego działania będzie prawdopodobnie nie wykrywania ataków (zarówno zakończone powodzeniem i niepowodzeniem). W związku z tym będzie on mógł szybkie odpowiadanie na potencjalne dokonywania środowisk i danych użytkownika.


**Obowiązki:**`Customer`


|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za monitorowanie zasobów wdrożonych przez klienta (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). Instrukcja implementacji kontroli klienta należy spełnić mechanizmy do monitorowania, wykrywania i reagowania na ataki, nieprawidłowego użycia i nieprawidłowego działania. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure Security zostały zdefiniowane wymagania aktywnego monitorowania. Zespoły obsługi skonfiguruj active narzędzi monitorowania zgodnie z tych wymagań. Narzędzia do aktywnego monitorowania obejmują agenta monitorowania (MA) i System Center Operations Manager (SCOM), które są skonfigurowane do alertów Podaj czas do personelu zabezpieczeń firmy Microsoft Azure w sytuacjach, które wymagają natychmiastowego działania. |


 ## <a name="ncsc-cloud-security-principle-54"></a>NCSC Cloud Security Principle 5.4
### <a name="incident-management"></a>Zarządzanie zdarzeniami
Procesy dokładnie wstępnie zaplanować Zarządzanie zdarzeniami znajdują się w miejscu, które mogą stać się po wystąpieniu zdarzenia potencjalnie powiększając istniejące ogólny wpływ na użytkowników są niską decyzje.
Te procesy nie musi być złożony lub wymagają dużej ilości opis, ale dobrym zarządzania zdarzeniami będzie zminimalizować wpływ użytkownikom bezpieczeństwa, niezawodności i środowiska problemy z usługą.


**Obowiązki:**`Shared`

> [!NOTE]
> W przypadkach, gdy przypadki naruszenia zabezpieczeń klienta może mieć wpływ na stan zabezpieczeń systemu Microsoft Azure klient jest odpowiedzialna za powiadomienie Microsoft Azure.

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za ustanawianie procesu zarządzania zdarzeniami dla wdrożonych przez klienta zasobów (dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). Instrukcja implementację klienta należy spełnić raportowania zdarzenia i alerty, obsługa czas odpowiedzi na zdarzenia i przekazywania informacji do PGA i innych organizacji HMG zależnie od potrzeb. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft zaimplementowała procesu zarządzania zdarzeniami zabezpieczeń w celu ułatwienia skoordynowany sposób reagowania na zdarzenia powinien wystąpić jeden. <br /> <br /> Jeśli Microsoft uzyska informacje o wszelkich nieautoryzowanego dostępu do danych klienta przechowywanych na jego urządzenia lub w jego urządzenia lub nieautoryzowany dostęp do takich urządzeń lub urządzeń powodujące utratę, ujawnienia lub zmiany danych klienta, stwierdził firmy Microsoft, który będą wykonywane następujące czynności: <br /> <br />   -Natychmiast powiadomić klienta zdarzenia zabezpieczeń; <br /> -Natychmiast Zbadaj zdarzenia zabezpieczeń i podaj klientowi szczegółowe informacje dotyczące zdarzenia zabezpieczeń; i <br /> -Podjąć uzasadnione i Szybkie kroki w celu ograniczenia efektów i zminimalizować szkody wynikające z naruszeniem bezpieczeństwa.  <br />  <br /> Określone role i obowiązki przydzielone została ustanowiona framework zarządzania zdarzeniami. Zespół zarządzania zdarzeniami zabezpieczeń systemu Windows Azure (WASIM) jest odpowiedzialny za zarządzanie zdarzeniami zabezpieczeń, w tym eskalacją i zapewnienie zaangażowania zespoły specjalistyczne, gdy jest to konieczne. Personel Azure jest odpowiedzialny za nadzór nad badanie i rozwiązywanie zdarzeń zabezpieczeń i prywatności z obsługą z innych funkcji. <br /> <br /> Aby uzyskać więcej informacji na temat procesów reagowania na zdarzenia firmy Microsoft, zobacz pełny opis w [zabezpieczeń platformy Azure i macierzy obowiązki klienta planu zgodności - zasad zabezpieczeń chmury NCSC -](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Zasady zabezpieczeń chmury NCSC 6
### <a name="personnel-security"></a>Zabezpieczenia dotyczące personelu
W przypadku, gdy personel dostawcy usług mają dostęp do danych i systemy należy wysoki stopień zaufania do ich wiarygodności. Obsługiwane szczegółowej kontroli przez odpowiedniego szkolenia zmniejsza prawdopodobieństwo przypadkowego lub złośliwymi naruszenia przez personel dostawcy usługi.
Dostawcy usług należy poddać personel kontroli zabezpieczeń i szkolenia w zakresie zabezpieczeń regularne. Personel w ramach tych ról poznać obowiązków. Dostawców należy wyjaśnić sposób ekranu i zarządzanie personelu w ramach ról uprzywilejowanych.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za osłonami osób i zapewnia dostęp do zasobów wdrożonych przez klienta szkolenia w zakresie zabezpieczeń regularnych dla użytkowników indywidualnych. Instrukcja implementację klienta należy spełnić kryteria kontroli ról i częstotliwość szkolenia w zakresie zabezpieczeń. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure pracowników, którzy usług platformy Azure i świadczenia obsługi klienta (lub podwykonawców firmy Microsoft, którzy pomagają operacje platformy, rozwiązywanie problemów i pomoc techniczna) przechodzą Microsoft standardowe tła (lub równoważnego) sprawdź, Ocena edukacji pracownika, zatrudnienia i karnej historii. Sprawdzanie w tle są szeroko zgodnie z wymogami Rząd UK BPSS/BS7858. Nie w szczególności obejmują one wyboru posiadanie tożsamości.  <br /> <br /> Firma Microsoft udostępnia przepisy o nieujawnianiu do swoich pracowników i podwykonawców umów. Wszystkie odpowiednie pracowników firmy Microsoft i podwykonawców brać udział w programie Microsoft Azure sponsorowany szkolenia w zakresie zabezpieczeń informujący personelu ich odpowiedzialności za bezpieczeństwo informacji. <br /> <br /> Zespół usługi Microsoft Azure lub podwykonawców firmy Microsoft podejrzewa zatwierdzania naruszeń zabezpieczeń i/lub naruszenie zasad zabezpieczeń informacji podlegają procesie dochodzenia i odpowiednie dyscyplinarnemu do i zakończenie włącznie. Jeśli wymagają tego okoliczności firmy Microsoft może zwrócić się do postępowania przez ścigania. <br /> <br /> Uzupełnienie tego systemu kontroli tła i education zabezpieczeń Microsoft wdraża kombinacji zapobiegawczych obrony i reaktywne formantów w celu ochrony przed nieautoryzowanym deweloperów i/lub działania administracyjne, takie jak mechanizmy: <br />  <br /> -Kontroli ścisłej dostępu do poufnych danych, w tym wymagania dotyczące uwierzytelniania dwuskładnikowego na podstawie karty inteligentnej do wykonywania ważnych operacji. <br /> -Kombinacje formantów, które zwiększają niezależne wykrywania złośliwych działań. <br /> -Wiele poziomów monitorowania, rejestrowania i raportowania. |


 ## <a name="ncsc-cloud-security-principle-7"></a>Zasady zabezpieczeń chmury NCSC 7
### <a name="secure-development"></a>Bezpiecznego programowania
Usługi powinny zaprojektowane i opracowany, aby zidentyfikować i uniknięcie zagrożeń do ich zabezpieczeń. Te, które nie mogą występować problemy z zabezpieczeniami, które może naruszyć bezpieczeństwo danych, spowodować utratę pracy lub włączyć innych złośliwych działań.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System Windows udostępnia weryfikacji integralności plików w czasie rzeczywistym, ochrony i odzyskiwania podstawowej systemu plików, które są instalowane w ramach systemu Windows lub autoryzowanych aktualizacje systemu Windows za pomocą funkcji ochrony zasobów systemu Windows (WRP), dzięki czemu w czasie rzeczywistym Sprawdzanie integralności. <br /> <br /> System Windows ma ochrony w celu zapobiegania wykonywanie kodu w lokalizacji pamięci ograniczone: nie wykonać (NX), losowe generowanie układu przestrzeni adresów (ASLR) i funkcja Zapobieganie wykonywaniu danych (DEP). <br /> <br /> Ten plan wdraża zabezpieczenia przed złośliwym kodem oparta na hoście dla wszystkich wdrożonych maszyn wirtualnych systemu Windows implementowane za pomocą programu Microsoft Windows Defender. Usługa Windows Defender jest skonfigurowany do automatycznego aktualizowania obu ochrony przed złośliwym kodem aparatu i ochrony podpisy jako wersji staną się dostępne. <br /> <br /> Aby było zgodne z tą zasadą, dalsze konfiguracja jest wymagana przez klienta do użycia w środowisku produkcyjnym. Tak te konfiguracje musi być częścią procesu bezpiecznego programowania klienta. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Security Development Lifecycle (SDL) udostępnia efektywny proces modelowanie zagrożeń do identyfikowania zagrożeń i luk w zabezpieczeniach oprogramowania i usług. Modelowanie zagrożeń jest ćwiczeniu zespołu obejmujące programu operations manager, program/menedżerów, deweloperów i testerów i reprezentuje zadanie analizy zabezpieczeń klucza wykonana w przypadku projektowania rozwiązania. Członkowie zespołu służy narzędzie modelowania zagrożeń SDL do modelowania wszystkie usługi i projekty, zarówno podczas zostały one utworzone i podczas zostaną zaktualizowane o nowe funkcje i możliwości. Modele zagrożeniem obejmować cały kod narażony na ataki i całego kodu napisane przez lub z innych firm i należy wziąć pod uwagę wszystkie granice zaufania. STRIDE system (Spoofing, Tampering, Repudiation, ujawnienie informacji, odmowę usługi i podniesienie uprawnień) służy do identyfikowania i rozwiązywania zagrożenia bezpieczeństwa na początku procesu projektowania, przed może mieć wpływ na klientów. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Zasady zabezpieczeń chmury NCSC 8
### <a name="supply-chain-security"></a>Zabezpieczenia łańcucha dostaw
Dostawcy usług należy upewnić się, że jego łańcuch dostaw prawidłowo obsługuje wszystkie zasady zabezpieczeń, które usługa oświadczeń do zaimplementowania.
Usługi w chmurze często zależny od innych firm, produktów i usług. W związku z tym Jeśli zasada ta nie jest zaimplementowana, naruszenia łańcucha dostaw może naruszać zabezpieczenia usługi i wpływ na stosowanie innych zasad zabezpieczeń.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zapewnienie dokumentacji łańcucha dostaw bezpieczne dla żadnych innych firm nabyte oprogramowania i systemów operacyjnych używanych w swoich subskrypcji platformy Azure. Instrukcja implementację klienta należy spełnić wyjątku, aby wykonać procesy identyfikowana na podstawie tej dokumentacji łańcucha dostaw. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Grupy łańcucha dostaw chmury firmy Microsoft (MCSC) składa się z sześciu zespoły unikatowy każdego przyczyniając się do ochrony przed zagrożeniami łańcucha dostaw Azure.  <br />  <br /> -Zamówień <br /> — Operacje klient <br /> -Jakość wdrożenia <br /> -Zarządzanie relacjami dostawcy <br /> -Dysków zapasowych <br />  <br /> Aby uzyskać więcej informacji dotyczących grupy MCSC firmy Microsoft, zobacz pełny opis w [zabezpieczeń platformy Azure i macierzy obowiązki klienta planu zgodności - zasad zabezpieczeń chmury NCSC -](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Zasady zabezpieczeń chmury NCSC 9
### <a name="secure-user-management"></a>Zabezpieczanie Zarządzanie użytkownikami
Dostawcy powinny udostępniać narzędzia do zarządzania bezpiecznego korzystania z usługi. Interfejsy zarządzania i procedury są to ważna część barierę bezpieczeństwa, co uniemożliwia nieautoryzowanym dostępem i zmian zasobów, aplikacji i danych.

Aspektów, które należy wziąć pod uwagę są:

- Uwierzytelnianie użytkowników do interfejsów zarządzania i kanały pomocy technicznej
- Rozdzielenie i kontroli dostępu w ramach interfejsy zarządzania



 ## <a name="ncsc-cloud-security-principle-91"></a>Zasady zabezpieczeń chmury NCSC 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Uwierzytelnianie użytkowników do interfejsów zarządzania i w obrębie kanały pomocy technicznej
Aby zachować Usługa bezpiecznego, użytkownicy muszą zostać prawidłowo uwierzytelnione przed uzyskaniem dostępu do wykonywania działań zarządzania raportu błędów żądania zmiany lub usługi.
Te działania mogą być przeprowadzane za pośrednictwem portalu sieci web zarządzania lub za pośrednictwem innych kanałów, takich jak telefonu lub poczty e-mail. Są one może zawierać takie funkcje jak inicjowanie obsługi administracyjnej nowych elementów usługi, zarządzanie kontami użytkowników i zarządzanie danymi użytkownika.
Upewnij się, że wszystkie żądania zarządzania, które może mieć negatywny wpływ na zabezpieczenia są wykonywane przez kanały bezpieczna i uwierzytelniona muszą usługodawców. Jeśli użytkownicy nie są uwierzytelniane silnie następnie domniemanego możliwość pomyślnego wykonania uprzywilejowanych akcji, obchodząc zabezpieczeń danych lub usługi.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Gdy administratorzy uzyskują dostęp do portalu Microsoft Azure do zarządzania zasobami Azure dla organizacji, dane przesyłane między portalem i administratora urządzenia był wysyłany za pośrednictwem kanału szyfrowanego zabezpieczeń TLS (Transport Layer) za pomocą 2048-bitowego RSA / Klucze szyfrowania SHA256, zgodnie z zaleceniami CESG/NCSC.  <br /> <br /> Ten plan zostają uwierzytelniania systemu Windows, pulpitu zdalnego i funkcji BitLocker. Te składniki można skonfigurować polegać na FIPS 140 sprawdzania poprawności modułów kryptograficznych. <br /> <br /> Ten plan wymusza autoryzacje logicznego dostępu za pomocą kontroli dostępu opartej na rolach, wymuszane przez usługę Azure Active Directory przez przypisywania użytkowników do ról, usługi Active Directory, przypisując użytkownikom dla grup zabezpieczeń i formanty poziomu systemu operacyjnego Windows. Azure Active Directory role przypisane do użytkowników lub grup sterować logicznego dostępu do zasobów w obrębie platformy Azure na poziomie zasobu, grupy lub subskrypcji. Grup zabezpieczeń usługi Active Directory kontrolować logicznego dostępu do zasobów na poziomie systemu operacyjnego i funkcji. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Klienci administrować zasobami Azure za pośrednictwem portalu Azure, która zapewnia dostęp do wszystkich maszyn wirtualnych, baz danych i usługi w chmurze, oraz innych zasobów skonfigurowanych dla konta klienta. Dostęp do portalu Azure w sieci Web, jest chroniona przez branżowych standardów zabezpieczeń TLS (Transport Layer) 1.2 połączenia przy użyciu kluczy szyfrowania RSA/SHA256 2048-bitowego, jak zalecane przez CESG/NCSC. Kontroli dostępu opartej na rolach są udostępniane umożliwiające klientom udzielany ograniczony dostęp do zasobów zarządzania platformy Azure dla konkretnych użytkowników i grup. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Zasady zabezpieczeń chmury NCSC 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>Rozdzielenie i kontroli dostępu w ramach interfejsy zarządzania
Wiele usług w chmurze są zarządzane za pośrednictwem aplikacji sieci web lub interfejsów API. Te interfejsy są kluczowym elementem zabezpieczeń usługi. Jeśli użytkownicy nie są odpowiednio oddzielone w ramach zarządzania interfejsów, może być może mieć wpływ na usługi lub modyfikować danych innego jednego użytkownika.
Uprzywilejowane konta administracyjne prawdopodobnie mają dostęp do dużych ilości danych. Ograniczający uprawnień użytkownikom do tych bezwzględnie konieczne może pomóc ograniczyć szkód spowodowanych przez złośliwych użytkowników, przejęcie poświadczeń lub urządzeń ze złamanymi zabezpieczeniami.
Kontrola dostępu oparta na rolach zapewnia mechanizm można to osiągnąć i może być szczególnie ważne możliwości użytkownikom zarządzanie wdrożeń na dużą.
Udostępnianie interfejsy zarządzania sieciami mniej dostępny (np. społeczności zamiast sieci publicznych) ułatwia utrudnia osobom atakującym do nawiązywania połączeń i ataku, zgodnie z ich musiałaby najpierw uzyskać dostęp do jednej z tych sieci. Wskazówki dotyczące ocenę ryzyka udostępnianie interfejsy do różnych typów sieci znajduje się w obszarze zasady 11.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Ten plan wdraża bramy aplikacji, usługi równoważenia obciążenia i konfiguruje reguły grupy zabezpieczeń sieci w celu kontrolowania commutations na granicach zewnętrznych i między podsieciami wewnętrznego. Funkcja użytkownika jest oddzielony od funkcji zarządzania systemu za pośrednictwem wymuszanie kontroli dostępu logicznych i architektura systemu. Interfejsy do obsługi funkcji zarządzania systemu są niezależne od interfejsów użytkownika. Wszystkie łączność w procesie zarządzania odbywa się za pośrednictwem bezpiecznego bastionu hosta (jumpbox) znajduje się w podsieci zarządzania z reguły grupy zabezpieczeń sieci można ograniczyć dostęp do zasobów w środowisku produkcyjnym zgodnie z potrzebami. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Zgodnie z opisem w rozdzielenie separacji użytkownika jest oparty na platformie Azure podstawowym składnikiem. Usługa Azure Active Directory (Azure AD lub AAD) może służyć do zapewnienia każdy użytkownik, który uwierzytelnia się do portalu Azure z dostępem tylko do zasobów, które mają prawo do wyświetlanie i zarządzanie nimi. W związku z tym kont różnych klientów są ściśle rozdzielone od siebie, gdy zarządzane za pośrednictwem portalu Azure wspólnej. |


 ## <a name="ncsc-cloud-security-principle-10"></a>NCSC Cloud Security Principle 10
### <a name="identity-and-authentication"></a>Uwierzytelnianie i tożsamość
Dostęp do interfejsy usługi powinny ograniczone do osób, uwierzytelnionym i autoryzowanym.
Słabe uwierzytelniania tych interfejsów mogą umożliwić nieautoryzowany dostęp do systemów, kradzieży lub modyfikacji danych, zmiany z usługą lub typu "odmowa usługi".
Ważne Jeśli wystąpią uwierzytelniania za pośrednictwem bezpiecznych kanałów. Wiadomości e-mail, HTTP lub telefonu są narażone na ataki zatrzymania oraz socjotechnicznych.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Ten plan wykorzystuje usługi Active Directory dla konta administracyjnego. Dostęp do zasobów wykorzystywanych przez ten plan jest chroniony przed atakami powtarzania wbudowanej funkcji protokołu Kerberos w usłudze Azure Active Directory, usługi Active Directory i systemu operacyjnego Windows. W uwierzytelnianiu Kerberos uwierzytelniania wysyłane przez klienta zawiera dodatkowe dane, takie jak listy IP zaszyfrowane, klienta sygnatury czasowe i okres istnienia biletu. Jeśli pakiet jest odtwarzany, zostanie sprawdzony sygnatury czasowej. Jeśli znacznik czasu jest starsza niż, lub taka sama jak poprzednie uwierzytelniania, pakiet zostanie odrzucony. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Platforma Azure udostępnia usługi służące do śledzenia tożsamości również zintegrować ją z magazyny tożsamości, które są już w użyciu. Usługi Azure AD jest kompleksowe usługi zarządzania tożsamościami i dostępem w chmurze, która pomaga zabezpieczyć dostęp do danych w lokalnych i aplikacji w chmurze. Usługi Azure AD również upraszcza zarządzanie użytkownikami i grupami łącząc podstawowych usług katalogowych, Zaawansowane zarządzanie tożsamościami, zabezpieczeń i zarządzania dostępem do aplikacji. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Zasady zabezpieczeń chmury NCSC 11
### <a name="external-interface-protection"></a>Ochrona interfejsu zewnętrznego
Wszystkie zewnętrzne lub mniej zaufanego interfejsy usługi powinny być określone i odpowiednio bronione.
Jeśli niektóre interfejsy widoczne są prywatne (na przykład interfejsy zarządzania) wpływu naruszenia zabezpieczeń może być bardziej znaczące.
Różne modele służy do nawiązania połączenia usługi w chmurze, które ujawnia z systemami przedsiębiorstwa do różnych poziomów ryzyka.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Ten plan wdrażania zasobów w architekturze podsieci WWW, podsieci bazy danych, podsieci usługi Active Directory i zarządzania podsieci. Podsieci są logicznie oddzielone reguły grupy zabezpieczeń sieci zastosować do poszczególnych podsieci, aby ograniczyć ruch między podsieciami, aby tylko to niezbędne do obsługi funkcji zarządzania i systemu (np. ruch zewnętrzny nie dostęp do bazy danych, zarządzanie, lub podsieci usługi Active Directory).  <br /> <br /> Bramy aplikacji jest wdrażany do zarządzania połączeniami zewnętrzne do aplikacji sieci web wdrożonych przez klienta. Połączenia zewnętrzne dla zarządzania dostępu są ograniczone do jumpbox (host bastionu) wdrożone w podsieci zarządzania z reguły zabezpieczeń sieciowych stosowane do ograniczenia połączeń zewnętrznych do autoryzowanych adresów IP. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft wykorzystuje metoda wywołuje metodę "Red zespołu kart interfejsu sieciowego" usprawniających kontrole zabezpieczeń platformy Azure i procesy regularne penetracji testy. Zespół czerwony jest grupą pełnoetatowych pracowników w ramach firmy Microsoft, który koncentruje się na wykonywanie docelowe i trwałe ataków Microsoft infrastruktury, platform i aplikacji, ale nie zakończenia klientów aplikacji lub danych. <br /> <br /> Aby uzyskać więcej informacji na temat tworzenia zespołu czerwony firmy Microsoft, jak również opis działań niebieski zespołu kart interfejsu sieciowego, zobacz pełny opis w [zabezpieczeń platformy Azure i plan zgodności - zasad zabezpieczeń chmury NCSC - klienta obowiązki macierzy ](https://aka.ms/blueprintuk-gcrm).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Zasady zabezpieczeń chmury NCSC 12
### <a name="secure-service-administration"></a>Usługa bezpiecznego administracji
Systemy używane do administrowania usługą w chmurze będzie mieć wysokiej uprzywilejowany dostęp do tej usługi. Ich naruszenia musi znaczący wpływ, w tym sposób do obejścia zabezpieczeń i wykradać lub manipulowania dużych ilości danych.
Projektowania, wdrażania i zarządzania systemami administracji powinien być zgodny enterprise dobrym rozwiązaniem, podczas ich wysokiej wartości na ataki.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zapewnienie bezpiecznego stacji roboczej do administrowania ich subskrypcji platformy Azure i wdrożone klienta zasobów (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). Instrukcja implementację klienta należy spełnić mechanizmy służące do ograniczenia ryzyka wykorzystania zasobów wdrożonych przez klienta. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Personel firmy Microsoft Azure muszą korzystać z bezpiecznej administracyjnej stacji roboczych (piły; tzw. stacje robocze uprzywilejowanego dostępu lub łapy). Podejście PIŁA jest rozszerzeniem ustalonym zalecaną praktyką użyć oddzielnych administratora i konta użytkowników dla personelu administracyjnego. Takie rozwiązanie wykorzystuje konto administratora indywidualnie przypisane, który jest całkowicie niezależna od konta użytkowników standardowych. Zapewniając zaufanego stacji roboczej dla tych kont poufnych był WYŚWIETLANY kompilacji w praktyce separacji tego konta. |


 ## <a name="ncsc-cloud-security-principle-13"></a>NCSC Cloud Security Principle 13
### <a name="audit-information-for-users"></a>Informacje o inspekcji dla użytkowników
Powinno być dostarczane z inspekcji rekordy niezbędne do monitorowania dostępu do usługi i dane przechowywane w nim. Typ informacji inspekcji dostępne będą ma bezpośredni wpływ na możliwość wykrywanie i reagowanie na nieodpowiednie lub złośliwe działania w rozsądnym przedziałach czasowych.


**Obowiązki:**`Shared`


|||
|---|---|
| **Klienta** | Zdarzenia inspekcji przez ten plan zawiera sprawdzana przez działanie Azure dzienniki dla wdrożonych zasobów, dzienniki na poziomie systemu operacyjnego i dzienników usługi Active Directory. Te dzienniki zdarzeń obejmują informacje wystarczające do określenia, w przypadku wystąpienia zdarzenia, źródło zdarzenia, wynik zdarzenia oraz inne szczegółowe informacje, które obsługuje zbadanie zdarzeń zabezpieczeń. Klienci mogą wybrać dodatkowe zdarzenia do inspekcji należy do potrzeb misji. Wszystkie zasoby platformy Azure ma dzienników inspekcji dostępnych w portalu Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Analiza dzienników Azure zbierają rekordy zdarzeń występujących w ramach systemów i sieci w organizacji, jak ich występowania, aby każdy użytkownik może manipulować je i umożliwia różnego rodzaju analizy przez skorelowanie danych na wielu komputerach. Azure umożliwia klientom umożliwia generowanie zdarzeń zabezpieczeń i kolekcji ze Azure IaaS i PaaS ról do centralnego magazynu w subskrypcji. W przypadku zdarzeń zbieranych mogą być eksportowane do lokalnych informacji o zabezpieczeniach i systemów zarządzania (SIEM) zdarzenia do ciągłego monitorowania. Po przeniesieniu do magazynu danych, istnieje wiele opcji, aby wyświetlić dane diagnostyczne. <br /> <br /> Wbudowana diagnostyka Azure może pomóc z debugowaniem. Dla aplikacji, które są wdrażane na platformie Azure zestaw zdarzeń zabezpieczeń systemu operacyjnego są domyślnie włączone. Klientów można dodać, usunąć ani zmodyfikować zdarzenia, które mają być sprawdzona dostosowując zasady inspekcji systemu operacyjnego. <br /> <br /> Na wysokim poziomie jest bardzo proste i prostego rozpoczęcia zbierania dzienników przy użyciu funkcji przekazywania zdarzeń systemu Windows (WEF) lub bardziej zaawansowanych diagnostyki Azure podczas rozmieszczania maszyn wirtualnych z systemem Windows przy użyciu IaaS na platformie Azure. Ponadto można skonfigurować do zbierania dzienników i zdarzeń z wystąpień roli PaaS diagnostyki Azure. Podczas korzystania z maszyn wirtualnych IaaS, klient po prostu konfiguruje i umożliwia zdarzenia wymaganymi tak samo umożliwiają one serwery z systemem Windows do dziennika inspekcji w swoim centrum danych lokalnych. Dla aplikacji sieci web jest również można włączyć rejestrowanie programu IIS, jeśli to głównej aplikacji i wdrażania na platformie Azure. Klienci zawsze można przechowywać dane zabezpieczeń na kontach magazynu w obsługiwane lokalizacje geograficzne według ich wyboru, aby spełnić wymagania suwerenności danych. |


 ## <a name="ncsc-cloud-security-principle-14"></a>NCSC Cloud Security Principle 14
### <a name="secure-use-of-the-service"></a>Bezpieczne korzystanie z usługi
Zabezpieczenia usługi w chmurze i przechowywanych w tych danych może być obniżona, jeśli korzystasz z usługi nieprawidłowo. W związku z tym konieczne będzie pewne obowiązki podczas korzystania z usługi w kolejności danych ma być odpowiednio chroniona.
Zakres odpowiedzialności użytkownika różni się zależnie od modele wdrażania usługi w chmurze oraz scenariusz, w którym zamierzasz korzystać z usługi. Specyficzne funkcje usługi mogą także mieć znaczenie. Na przykład jak sieci dostarczania zawartości chroni klucz prywatny lub jak dostawcy usług w chmurze płatności wykrywa fałszywe transakcje są ważne zagadnienia dotyczące zabezpieczeń tylko Ogólne zagadnienia, które są objęte zasadami zabezpieczeń chmury.
Z oferty IaaS i PaaS jest odpowiedzialny za istotne aspekty zabezpieczeń danych i obciążeń. Na przykład jeśli użytkownik nabywania IaaS obliczeniowe wystąpienia, zazwyczaj będzie odpowiedzialny za instalowanie nowoczesnych systemów operacyjnych, bezpiecznie Konfigurowanie tego systemu operacyjnego, bezpieczne wdrażanie aplikacji i również obsługa tego wystąpienia przez stosowanie poprawki lub konserwacja wymagane.


**Obowiązki:**`Customer`

> [!NOTE]
> Klienta można użyć Centrum zabezpieczeń Azure można zapobiec, wykrywania i reagowania na zagrożenia lepszy wgląd i kontrolę zabezpieczeń zasobów platformy Azure. Centrum zabezpieczeń Azure zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami różnych subskrypcji Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

|||
|---|---|
| **Klienta** | Szablonów usługi Azure Resource Manager i towarzyszące zasobów, które obejmuje ten plan wykonać podejście obrony zabezpieczeń dla zabezpieczeń. W celu zapewnienia zgodności z zasadą dalsza konfiguracja jest wymagana przez klienta do użycia w środowisku produkcyjnym (np. oprogramowanie do zarządzania bazą danych, wdrażanie aplikacji sieci web). |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Nie dotyczy |

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.
 - Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.
 - Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
 - Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.