---
title: Plan Azure przetwarzania płatności — wymagania systemowe bezpieczne
description: Wymaganie PCI DSS 6
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2f6f1bfc853f261eecf5357cef5d3e3d972781b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895425"
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Wymagania systemowe dla środowisk standardem PCI DSS Secure 
## <a name="pci-dss-requirement-6"></a>Wymaganie PCI DSS 6

**Tworzenie i obsługa systemów zabezpieczeń i aplikacji**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Osoby nieuczciwe umożliwia uzyskanie uprzywilejowanego dostępu do systemów luk w zabezpieczeniach. Wiele z tych luk w zabezpieczeniach są rozwiązywane przez poprawki dostarczonym zabezpieczeń, które mogą być instalowane przez obiekty, które zarządzania systemami. Wszystkie systemy musi mieć wszystkie poprawki odpowiednie oprogramowanie do ochrony przed wykorzystania i naruszeniu bezpieczeństwa danych posiadacza przez złośliwych użytkowników i złośliwego oprogramowania.

> [!NOTE]
> Poprawki odpowiednie oprogramowanie są te poprawki, które zostały ocenione i przetestowane wystarczająco, aby określić, że poprawki nie są sprzeczne z istniejącymi konfiguracjami zabezpieczeń. Wiele luk w zabezpieczeniach mogą być wewnętrznie opracowanych aplikacji, zaoszczędzone przy użyciu procesów tworzenia oprogramowania w standardowym systemie i bezpieczne techniki tworzenia kodu.

## <a name="pci-dss-requirement-61"></a>Wymaganie PCI DSS 6.1

**6.1** ustanowić procesu, aby zidentyfikować luki w zabezpieczeniach, przy użyciu zaufanych poza źródeł informacji o zabezpieczeniach luki w zabezpieczeniach i przypisz zagrożenie klasyfikacji (na przykład jako "high", "medium," lub "low") do nowo odnalezionych zabezpieczeń luki w zabezpieczeniach.

> [!NOTE]
> Klasyfikacje ryzyka powinny być oparte na najlepsze rozwiązania w branży, jak również uwagę potencjalny wpływ. Na przykład kryteria klasyfikacji luk w zabezpieczeniach mogą uwzględniać wynik podstawowy CVSS i/lub klasyfikację przez dostawców i/lub typ systemy, których to dotyczy. 
> 
> Metody oceny luk w zabezpieczeniach i przypisywanie oceny ryzyka będzie różnić w zależności od środowiska i strategii oceny ryzyka organizacji. Klasyfikacje ryzyka co najmniej zidentyfikować wszystkie luki w zabezpieczeniach uważany za "wysokiego ryzyka" do środowiska. Oprócz klasyfikacji ryzyka luk w zabezpieczeniach można uznać za "krytyczne" Jeśli one stanowić bezpośrednie zagrożenie dla środowiska krytyczne systemy wpływ i/lub spowodowałoby potencjalne naruszenie zabezpieczeń, jeśli nie problemu. Przykłady ważnych systemów może systemów zabezpieczeń, publicznych urządzenia i systemy, baz danych i innych systemów, które przechowują procesu, lub przesyłać danych imię.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Procedury zostały ustalone i wdrożone w celu skanowania w poszukiwaniu luk w zabezpieczeniach na hostach funkcji hypervisor w granicy zakresu. Luki w zabezpieczeniach skanowanie jest wykonywane na systemy operacyjne serwera baz danych i urządzeń sieciowych z luki w zabezpieczeniach odpowiednich narzędzi do skanowania. Luki w zabezpieczeniach skanowania są wykonywane co kwartał, co najmniej. Umowy Microsoft Azure z niezależnych ekspertów do testowania penetracji granic Microsoft Azure. Ćwiczenia zespołu czerwony również regularnie są wykonywane i wyników użyć w celu dokonania poprawek zabezpieczeń. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji z zapory aplikacji sieci Web i zestaw reguł OWASP włączone. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - zmniejsza ryzyko związane z luki w zabezpieczeniach](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-62"></a>Wymaganie PCI DSS 6.2

**6.2** upewnij się, że wszystkie składniki systemu i oprogramowania są chronione przed znanych luk w zabezpieczeniach, instalując poprawki stosowane zabezpieczeń dostarczonego przez dostawcę. Instalacja krytycznych poprawek w ciągu miesiąca od wersji.

> [!NOTE]
> Zgodnie z ryzykiem Klasyfikacja proces określony w 6.1 wymaganie należy zidentyfikować krytycznych poprawek.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure jest odpowiedzialny za egzekwowanie wszystkich urządzeń sieciowych i funkcji hypervisor oprogramowania systemu operacyjnego jest chroniony przed znanych luk w zabezpieczeniach, instalując poprawki stosowane zabezpieczeń dostarczonego przez dostawcę. Chyba, że klient żąda nie korzystać z usługi, proces zarządzania poprawki istnieje zapewnić uniemożliwił i skorygować w odpowiednim czasie poziomu luk w zabezpieczeniach systemu operacyjnego. Serwerów produkcyjnych są skanowane w celu zweryfikowania zgodności poprawki co miesiąc. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso to rozwiązanie typu PaaS usługi. Platforma Azure udostępnia obsługi wszystkich poprawek.|



## <a name="pci-dss-requirement-63"></a>Wymaganie PCI DSS 6.3

**6.3** opracowywania aplikacji wewnętrznych i zewnętrznych (w tym dostęp administracyjny do aplikacji opartych na sieci web) bezpiecznie w następujący sposób:
- Zgodnie z PCI DSS (na przykład bezpiecznego uwierzytelniania i logowania)
- Na podstawie standardów branżowych i najlepszych rozwiązań
- Dołączanie ochrony informacji w całym cyklu tworzenia oprogramowania 

> [!NOTE]
> Dotyczy to wszystkich opracowane wewnętrznie, jak również tworzony na zamówienie lub niestandardowe oprogramowanie opracowane przez innych firm.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Aplikacje Microsoft Azure i punkty końcowe są tworzone zgodnie z metodologii Microsoft Security Development Lifecycle (SDL), która jest zgodna z wymaganiami DSS. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web została zaprojektowana w celu postępuj zgodnie z branży najlepsze rozwiązania w zakresie ochrony CHD. Wskazówki dotyczące wdrażania zawiera szczegółowe informacje o meassures zabezpieczeń i rejestrowanie jest włączone.|



### <a name="pci-dss-requirement-631"></a>Wymaganie PCI DSS 6.3.1

**6.3.1** przed aplikacje staną się aktywne lub są zwalniane klientom usunąć programowanie, testu i/lub kont niestandardowych aplikacji, nazwy użytkowników i hasła.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Przegląd zabezpieczeń końcowego (FRS) jest przeprowadzane dla wersjach głównych przed ich wdrożeniem w środowisku produkcyjnym przez wyznaczonych Advisor zabezpieczeń poza zespół deweloperów platformy Azure, aby upewnić się, że są wydawane tylko aplikacje gotowe do produkcji. W ramach przeglądu ostatecznego zapewnia się, że wszystkie konta testowe i dane testowe zostały usunięte. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso udostępnia usługę tymczasowej, która jest rejestrowane i samodzielnie. Każdy z warstw sieci ma dedykowany sieciowej grupy zabezpieczeń [NSG]. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - grupy zabezpieczeń sieci](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>Wymaganie PCI DSS 6.3.2

**6.3.2** przeglądu kodu niestandardowego przed wersji produkcyjnej oraz klientów, aby zidentyfikować wszelkie potencjalne kodowania luki w zabezpieczeniach (przy użyciu procesów ręczne lub automatyczne) zawierać co najmniej następujące:
- Zmiany kodu są kontrolowane przez osoby inne niż autorem kodu źródłowego i osoby odpowiednią wiedzę na temat przeglądu kodu technik i bezpieczne praktyk kodowania.
- Przeglądy kodu upewnij się, że kod został utworzony zgodnie z wytycznymi kodowania bezpieczne
- Odpowiednie korekty są implementowane przed wersji
- Wyników przeglądu kodu zostały sprawdzone i zatwierdzone przez zarządzania przed publikacją 

> [!NOTE]
> To wymaganie dla przeglądy kodu ma zastosowanie do całego kodu niestandardowego (wewnętrzny i publicznych), jako część systemu cyklu. 
>
> Przeglądy kodu mogą zostać przeprowadzone przez pracowników wewnętrznych wiedzę lub osób trzecich. Aplikacje sieci web publicznych obowiązują również dodatkowe funkcje kontroli, adres bieżących zagrożeń i luk w zabezpieczeniach po implementacji, zgodnie z definicją w PCI DSS wymaganie 6.6.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Aplikacje Microsoft Azure i punkty końcowe są tworzone zgodnie z metodologii Microsoft Security Development Lifecycle (SDL). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso udostępnia usługę tymczasowej, która jest rejestrowane i samodzielnie. Każdy z warstw sieci ma dedykowany sieciowej grupy zabezpieczeń [NSG]. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - grupy zabezpieczeń sieci](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>Wymaganie PCI DSS 6.4

**6.4** wykonaj zmiana kontroli procesy i procedury dla wszystkich zmian do składników systemu. Procesy musi zawierać następujące (zobacz wymagania 6.4.1 do 6.4.6).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft następuje NIST wskazówki dotyczące zagadnienia dotyczące zabezpieczeń w rozwoju oprogramowania, w tym bezpieczeństwa informacji muszą zostać włączone do SDLC od rozpoczęcia systemu. Umożliwia ciągłej integracji praktyk związanych z zabezpieczeniami w SDL firmy Microsoft:<ul><li>Wczesne identyfikacji i środki zaradcze luk w zabezpieczeniach i błędy konfiguracji</li><li>Świadomość potencjalnych oprogramowania kodowania wyzwanie spowodowane kontroli zabezpieczeń</li><li>Identyfikacja usług udostępnionych zabezpieczeń i ponowne użycie narzędzia najlepsze rozwiązania w zakresie zabezpieczeń, co zwiększa stan zabezpieczeń za pomocą sprawdzonych metod i technik</li><li>Wymuszanie firmy Microsoft już ryzyka zarządzania programu</li></ul>Microsoft Azure została ustanowiona zmiany i zwolnij procesów zarządzania do kontroli wykonania istotne zmiany w tym:<ul><li>Identyfikowanie i dokumentacji planowane zmiany</li><li>Określenie celów biznesowych, priorytetów i scenariuszy podczas planowania produktu</li><li>Specyfikacja funkcji/składnika projektu</li><li>Sprawdź gotowość do działania na podstawie wstępnie zdefiniowane kryteria/wyboru listy do oceny ryzyka ogólnego/wpływu</li><li>Testowanie, autoryzacji i zarządzanie zmianami oparte na kryteriach wejścia/wyjścia dla deweloperów (Programowanie), INT (Testowanie integracji), etap (przedprodukcyjnej) i produkcyjną (środowisko produkcyjne) środowisk zależnie od potrzeb. Klienci są zobowiązani do własnych aplikacji hostowanej na platformie Microsoft Azure.</li></ul> |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Pokaz magazynu sieci Web firmy Contoso udostępnia usługę tymczasowej, która jest rejestrowane i samodzielnie. <br /><br />Każdy z warstw sieci ma dedykowany sieciowej grupy zabezpieczeń [NSG]. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - grupy zabezpieczeń sieci](payment-processing-blueprint.md#network-security-groups).<br /><br />Zmiany są rejestrowane za pomocą usługi Operations Management Suite i elementy Runbook są używane do zbierania dzienników. Analiza dzienników zapewnia szczegółowe rejestrowanie zmian. Zmiany można przejrzeć i sprawdzić dokładność. Aby uzyskać bardziej szczegółowe wskazówki, zobacz [wskazówki PCI - usługi Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-641"></a>Wymaganie PCI DSS 6.4.1

**6.4.1** oddzielne środowiska programowania i testowania w środowisku produkcyjnym, a wymuszania separacji z kontroli dostępu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [6.4 wymaganie](#pci-dss-requirement-6-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso udostępnia usługę tymczasowej, która jest rejestrowane i samodzielnie. Każdy z warstw sieci ma dedykowany sieciowej grupy zabezpieczeń [NSG]. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - grupy zabezpieczeń sieci](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>Wymaganie PCI DSS 6.4.2

**6.4.2** podział obowiązków między środowiskami programowanie i testowanie i produkcji

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [6.4 wymaganie](#pci-dss-requirement-6-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso udostępnia usługę tymczasowej, która jest rejestrowane i samodzielnie. Każdy z warstw sieci ma dedykowany sieciowej grupy zabezpieczeń [NSG]. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - grupy zabezpieczeń sieci](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>Wymaganie PCI DSS 6.4.3

**6.4.3** danych produkcyjnych (Miednice na żywo) nie są wykorzystywane do testowania i tworzenia.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [6.4 wymaganie](#pci-dss-requirement-6-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie kontem podstawowym numer (PRZESUWANIE) danych dynamicznych.|



### <a name="pci-dss-requirement-644"></a>Wymaganie PCI DSS 6.4.4

**6.4.4** usunięcie danych testowych i kont z składników systemu, zanim system staje się aktywny lub w środowisku produkcyjnym.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [6.4 wymaganie](#pci-dss-requirement-6-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie ma żadnych testowe konta.|



### <a name="pci-dss-requirement-645"></a>Wymaganie PCI DSS 6.4.5

**6.4.5** procedury sterowania zmianami do stosowania poprawek zabezpieczeń i modyfikacji oprogramowania musi zawierać następujące:
- **6.5.4.1** dokumentacji wpływu.
- **6.5.4.2** udokumentowane zmiana zatwierdzenia przez autoryzowanego strony.
- **6.5.4.3** funkcji test, aby sprawdzić, czy zmiany nie ma negatywnego wpływu zabezpieczeń systemu.
- **6.5.4.4** wycofujący procedury.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [6.4 wymaganie](#pci-dss-requirement-6-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso to rozwiązanie typu PaaS usługi. I platforma Azure udostępnia obsługi wszystkich poprawek.|



### <a name="pci-dss-requirement-646"></a>Wymaganie PCI DSS 6.4.6

**6.4.6** po zakończeniu znaczących zmian, wszystkie odpowiednie wymagania PCI DSS musi zostać wdrożone na wszystkich nowych lub zmienionych systemów i sieci oraz dokumentacji zaktualizowane zgodnie z wymaganiami.

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [6.4 wymaganie](#pci-dss-requirement-6-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso to rozwiązanie typu PaaS usługi. I platforma Azure udostępnia obsługi wszystkich poprawek.|



## <a name="pci-dss-requirement-65"></a>Wymaganie PCI DSS 6.5

**6.5** adres wspólnej kodowania luk w zabezpieczeniach w procesach tworzenia oprogramowania w następujący sposób:
- Deweloperzy Train co najmniej raz w aktualne zabezpieczenia technik programowania, łącznie ze sposobem uniknięcia znanych luk kodowania.
- Tworzenie aplikacji na podstawie bezpiecznego dotyczących kodowania.

> [!NOTE]
> Podczas tej wersji PCI DSS został opublikowany bieżącego z najlepszymi rozwiązaniami w branży zostały wymienione na 6.5.1 za pośrednictwem 6.5.10 luk w zabezpieczeniach. Jednak zgodnie z branży najlepsze rozwiązania dotyczące zarządzania luki w zabezpieczeniach zostaną zaktualizowane (na przykład w przewodniku OWASP sieci SAN, do góry CWE 25 certyfikatu Secure kodowania, itp.), bieżący najlepsze rozwiązania należy użyć tych wymagań. 
> 
> [!NOTE]
> Wymagania dotyczące 6.5.1 za pośrednictwem 6.5.6, poniżej dotyczą wszystkich aplikacji (wewnętrznych lub zewnętrznych). Wymagania dotyczące 6.5.7 za pośrednictwem 6.5.10, poniżej, mają zastosowanie do aplikacji sieci web i interfejsów aplikacji (wewnętrznych lub zewnętrznych). 

- **6.5.1** iniekcji błędów, szczególnie iniekcja kodu SQL. Należy również rozważyć wady iniekcji iniekcji polecenia systemu operacyjnego, LDAP i wyrażenie XPath, a także innych wady iniekcji.
- **6.5.2** przepełnienia buforu
- **Ppkt 6.5.3** niezabezpieczonych magazynu kryptograficznego
- **6.5.4** niezabezpieczonych komunikacji
- **Ppkt 6.5.5** obsługi błędów nieprawidłowej
- **6.5.6** wszystkich "wysokiego ryzyka" luk w zabezpieczeniach w proces identyfikacji luki w zabezpieczeniach (zgodnie z definicją w PCI DSS wymaganie 6.1)
- **6.5.7** skryptów między witrynami (XSS)
- **6.5.8** kontroli dostępu niewłaściwy (takie jak niebezpieczne obiektu bezpośrednio odwołania, awarii do ograniczenia dostępu adresu URL, przechodzenie katalogu i niepowodzenia, aby ograniczyć dostęp użytkownika do funkcji)
- **6.5.9** sfałszowaniem żądań Cross-site (CSRF)
- **6.5.10** uszkodzony uwierzytelniania i sesji zarządzania

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [6.4 wymaganie](#pci-dss-requirement-6-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Pokaz magazynu sieci Web firmy Contoso zawiera wskazówki dotyczące bezpiecznego programowania, DPD i zagrożeń modelu ilustrujący bezpiecznego programowania rozwiązań.|



## <a name="pci-dss-requirement-66"></a>Wymaganie PCI DSS 6.6

**6.6** publicznych aplikacji sieci web, adres nowych zagrożeń i luk w zabezpieczeniach w sposób ciągły i upewnij się, te aplikacje są chronione przed znane ataki przy użyciu jednej z następujących metod:

- Przeglądanie publicznych aplikacji sieci web za pomocą narzędzia oceny zabezpieczeń luki w zabezpieczeniach aplikacji ręczne lub automatyczne lub metod, co najmniej raz i po zmiany 

   > [!NOTE]
   > Ocena nie jest taki sam, jak skanowanie luki w zabezpieczeniach wykonywane dla 11.2 wymaganie. 

- Instalowanie zautomatyzowane rozwiązanie techniczne, która wykrywa i uniemożliwia ataków opartych na sieci web (na przykład zapora aplikacji sieci web) przed publicznych aplikacji sieci web, aby sprawdzić stale cały ruch.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure testów aplikacji sieci web publicznych w ramach procesu tworzenia oprogramowania przed wdrożeniem aplikacji do środowiska produkcyjnego. Ponadto Microsoft skanowanie wszystkich publicznych aplikacji sieci web w środowisku produkcyjnym regularnie wykrywać wszelkie możliwe luki w zabezpieczeniach. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Odwołanie rozwiązanie zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji z zapory aplikacji sieci Web i zestaw reguł OWASP włączone. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - zmniejsza ryzyko związane z luki w zabezpieczeniach](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-67"></a>Wymaganie PCI DSS 6.7

**6.7** Sprawdź, czy zasady zabezpieczeń i procedur operacyjnych tworzenie i obsługa zabezpieczenia i aplikacje są udokumentowane w użyciu i znanych wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Odwołanie rozwiązanie zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji z zapory aplikacji sieci Web i zestaw reguł OWASP włączone. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - zmniejsza ryzyko związane z luki w zabezpieczeniach](payment-processing-blueprint.md#application-gateway).|




