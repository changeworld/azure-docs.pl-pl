---
title: Plan Azure przetwarzania płatności — wymagania dotyczące fizycznego dostępu
description: Wymaganie PCI DSS 9
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 2dbf4d9e2f4ff7b6cdf8e48c24eeee4d036fe195
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące fizycznego dostępu dla środowisk standardem PCI DSS 
## <a name="pci-dss-requirement-9"></a>Wymaganie PCI DSS 9

**Ogranicz dostęp fizyczny do danych posiadacza**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Wszelkie fizyczna dostęp do danych lub systemów tego posiadacza DOM danych zapewnia możliwość dla użytkowników indywidualnych, dostęp do urządzeń lub danych i usunąć systemów lub wydruki i należy odpowiednio ograniczyć. Na potrzeby wymaganie 9 "u klienta personel" odwołuje się do pełnym i niepełnym pracowników tymczasowych, wykonawców oraz doradców IT, którzy są fizycznie obecne lokalnie jednostki. "Odwiedzającego" odwołuje się do dostawcy, Gość dowolną liczbę pracowników siedzibie organizacji, pracowników usługi lub dowolny użytkownik musi podać funkcji krótki czas trwania, zwykle nie więcej niż jeden dzień. "Nośnika" odnosi się do wszystkich papieru elektronicznych nośnika zawierającego dane imię.

## <a name="pci-dss-requirement-91"></a>Wymaganie PCI DSS 9.1

**9.1** używać odpowiednie funkcje kontroli wpisu limit i monitor fizyczny dostęp do systemów w środowisku danych imię.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure jest odpowiedzialny za wdrażanie, wymuszanie i monitorowanie zabezpieczeń fizyczny dostęp do centrów danych. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-911"></a>Wymaganie PCI DSS 9.1.1

**9.1.1** użyć wideo kamery lub dostępu kontroli mechanizmów (lub obu) do monitorowania pojedynczych fizycznego dostępu do obszarów poufnych. Przejrzyj zebrane dane i mieć związek z innych pozycji. Przechowywania dla co najmniej trzy miesiące, chyba że w przeciwnym razie ograniczony przez prawo.

> [!NOTE]
> "Obszarów wrażliwych" odwołuje się do dowolnego centrum danych, pomieszczenia serwera lub dowolnego obszar, który zawiera wszystkie systemy, które przechowywania, przetwarzania lub przesyłania danych imię. Obejmuje to publicznych obszarów, gdzie tylko szacunkowe terminale są obecne, takie jak obszary kasjer w sklepie.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure jest odpowiedzialny za wdrażanie, wymuszanie i monitorowanie systemu telewizji przemysłowej i mechanizmy kontroli dostępu biometryczne centrach danych. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-912"></a>Wymaganie PCI DSS 9.1.2

**9.1.2** wdrożenie środków fizycznych i/lub logicznych, aby ograniczyć dostęp do gniazda publicznie dostępnej sieci. 

Na przykład gniazda sieci znajduje się w obszarach publiczny i obszarów dostępny dla osób odwiedzających można wyłączyć i włączyć tylko wtedy, gdy uzyskano dostęp do sieci. Aby upewnić się, że osoby odwiedzające są eskortowanych na cały czas w obszarach z gniazda aktywna sieć można alternatywnie realizowane procesów.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie ma żadnych gniazda publicznie dostępnej sieci w ramach platformy Microsoft Azure. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-913"></a>Wymaganie PCI DSS 9.1.3

**9.1.3** ograniczyć fizyczny dostęp do punktów dostępu bezprzewodowego, bram, urządzeń przenośnych, sprzęt sieci/komunikacji i telekomunikacyjnych wierszy.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Fizyczny dostęp do systemu Microsoft Azure sprzęt sieciowy jest ściśle kontrolowane przez listy dostępu do wielu metod uwierzytelniania, bariery fizyczne wejścia i wymagania dla firm należy zatwierdzić do uzyskiwania dostępu do urządzenia. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



## <a name="pci-dss-requirement-92"></a>Wymaganie PCI DSS 9.2

**9.2** Tworzenie procedury, aby łatwo rozróżnianie między pracownikami w siedzibie organizacji i gości, obejmują:
- Identyfikowania pracowników w siedzibie organizacji i osoby odwiedzające (na przykład przypisywanie identyfikatory)
- Zmiany uzyskanie dostępu do wymagań
- Odwoływanie lub przerywanie personelu w siedzibie organizacji i identyfikacji wygasłe odwiedzający (np. identyfikatory).

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure jest odpowiedzialny za wdrażanie, wymuszanie i monitorowania identyfikator zabezpieczeń i pracowników lub wykonawcy fizycznego dostępu podczas odwiedzania centrach danych. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



## <a name="pci-dss-requirement-93"></a>Wymaganie PCI DSS 9.3

**9.3** kontrolować fizyczny dostęp dla personelu w siedzibie organizacji do obszarów wrażliwych, w następujący sposób:
- Dostęp musi być autoryzowany i oparte na funkcji poszczególnych zadań.
- Dostęp został odwołany natychmiast po zakończeniu i wszystkich mechanizmów fizycznego dostępu, takie jak klucze, kart dostępu itd., są zwracane lub wyłączone.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Autoryzacji dostępu w centrach danych firmy Microsoft jest kontrolowany przy użyciu listy autoryzowanych dostępu zatwierdzone przez zespół centrum danych w oparciu o zasadę najniższych uprawnień. Listy kontroli dostępu jest sprawdzone, weryfikacji i aktualizowane co kwartał.<br /><br />Centrów danych Microsoft Azure wykorzystywać urządzenia fizycznego dostępu, takich jak obwodowej bramy, czytniki dostępu elektronicznego czytników biometrycznych, man pułapek/portali i uruchomiony przebieg urządzenia kopii. Urządzenia wskaźnika dostępu są stale monitorowane. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



## <a name="pci-dss-requirement-94"></a>Wymaganie PCI DSS 9.4

**9.4** wykonania procedur, aby zidentyfikować i autoryzować odwiedzający. Procedury należy obejmują następujące czynności.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure jest odpowiedzialny dla wymuszenie dostaw wstępnie zatwierdzonych są odbierane w bay bezpieczne ładowania, fizycznie izolowane od urządzeń przetwarzania informacji i są monitorowane przez autoryzowanego personelu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-941"></a>Wymaganie PCI DSS 9.4.1

**9.4.1** gości są autoryzowane przed wprowadzeniem i eskortowanych na cały czas, w ramach obszarów, gdzie przetworzenia lub utrzymywane posiadacza danych.


**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure jest odpowiedzialny dla wymuszenie dostaw wstępnie zatwierdzonych są odbierane w bay bezpieczne ładowania, fizycznie izolowane od urządzeń przetwarzania informacji i są monitorowane przez autoryzowanego personelu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-942"></a>Wymaganie PCI DSS 9.4.2

**9.4.2** gości są zidentyfikowane i daną wskaźnika lub inne określenie, które wygasa i który odróżnia widoczny gości z pracownikami w siedzibie organizacji.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Dostęp do centrum danych firmy Microsoft musi być wstępnie zatwierdzone i osoby upoważnione są wymagane do zaewidencjonowania przy zabezpieczeń fizycznych w punkcie odbioru i podaj prawidłowy dowód identyfikator przed wejściem. Identyfikatory wyraźnie wskazuje pracowników. Wykonawców i gości otrzymywać tymczasowego identyfikatory, które muszą zostać przekazana po wyjściu z funkcji. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-943"></a>Wymaganie PCI DSS 9.4.3 otrzymuje brzmienie

**9.4.3 otrzymuje brzmienie** odwiedzających monit o przekazanie wskaźnika lub identyfikacji przed opuszczeniem funkcji lub po dacie wygaśnięcia.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Osoby odwiedzające są wymagane do przekazania identyfikatory po wyjściu z dowolnego obiektu Microsoft. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-944"></a>Wymaganie PCI DSS 9.4.4

**9.4.4** dziennika dla gości jest używany do przechowywania dziennika inspekcji fizycznych działania odwiedzający do, a także pomieszczenia komputera i centrów danych, gdzie przechowywane lub przesyłane dane posiadacza.
Nazwa odwiedzającego, przedsiębiorstwo reprezentowane i personelu siedzibie autoryzacji fizycznego dostępu w Dzienniku dokumentów.
Zachować ten dziennik na co najmniej trzy miesiące, chyba że w przeciwnym razie ograniczony przez prawo.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure jest odpowiedzialny za konserwację dziennika dla gości, jako dziennik inspekcji fizycznych działania odwiedzający do, a także pomieszczenia komputera i centrów danych, gdzie przechowywane lub przekazywanych danych imię. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



## <a name="pci-dss-requirement-95"></a>Wymaganie PCI DSS 9.5

**9.5** fizycznie Zabezpiecz wszystkich nośników.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>Wymaganie PCI DSS 9.5.1

**9.5.1** przechowywania kopii zapasowych nośnika w bezpiecznej lokalizacji, najlepiej poza nim funkcje, takie jak witryny alternatywnego lub tworzenia kopii zapasowej lub komercyjnych magazynu. Przejrzyj zabezpieczenia lokalizacji co najmniej raz.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>Wymaganie PCI DSS 9.6

**9.6** ścisłą kontrolę nad dystrybucji wewnętrzne lub zewnętrzne każdego typu nośnika, takie jak obsługa.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>Wymaganie PCI DSS 9.6.1

**9.6.1** klasyfikowania nośnika, można ustalić poufność danych.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>Wymaganie PCI DSS 9.6.2

**9.6.2** Prześlij nośnik zabezpieczonych courier lub innej metody dostarczania, które może być śledzone dokładnie.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>Wymaganie PCI DSS 9.6.3

**9.6.3** zarządzania upewnij się, że zatwierdza wszystkie nośnika, który jest przenoszony z obszaru zabezpieczonych (w tym gdy nośnika jest dystrybuowana do osób).

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>Wymaganie PCI DSS 9.7

**9.7** Obsługa ścisłą kontrolę nad magazynu i dostępności nośnika.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>Wymaganie PCI DSS 9.7.1

**9.7.1** prawidłowo Obsługa dzienników spis wszystkich nośników i przeprowadzanie spisów nośnika co najmniej raz.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>Wymaganie PCI DSS 9,8

**9,8** zniszczyć nośnika, gdy nie jest już potrzebne dla firm lub z przyczyn prawnych w następujący sposób.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>Wymaganie PCI DSS 9.8.1

**9.8.1** "Podrasowane", spalania lub miąższu materiałów wydruku, aby nie można odtworzyć posiadacza danych. Zabezpiecz kontenery magazynu używane do materiałów, które mają zostać zniszczone.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web przechowuje wszystkie dane w bazie danych SQL Azure. Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>Wymaganie PCI DSS 9.8.2

**9.8.2** renderowania posiadacza dane na nośniku elektronicznych nieodwracalny, aby nie można odtworzyć posiadacza danych.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Techniki zniszczenie danych różnią się w zależności od typu obiektu danych niszczone, czy będzie subskrypcji, magazynu, maszyn wirtualnych i baz danych. W środowisku wielodostępnym Microsoft Azure dokładne, że uwagi jest podjąć w celu zapewnienia danych jednego klienta nie może albo "przeciek" na dane z innego klienta lub gdy klient usuwa dane, inne klienta (w tym, w większości przypadków klienta kto raz własność danych) można uzyskać dostęp do usuniętych danych.<br /><br />Microsoft Azure wynika NIST wytyczne 800 88 na nośniku ich oczyszczania, które rozwiązania główna problemu zapewniania, że dane nie zostaje zwolniony przypadkowo. Niniejsze wytyczne obejmują ich oczyszczania zarówno elektronicznych, jak i fizycznych. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Całkowicie przez usunięcie grupy zasobów, używane podczas wdrażania można usunąć magazynu sieci Web firmy Contoso.|



## <a name="pci-dss-requirement-99"></a>Wymaganie PCI DSS 9.9

**9.9** ochrona urządzeń, które przechwytują dane kart płatniczych za pośrednictwem bezpośredniego fizycznych interakcji z kartą przed naruszeniem i podstawienia.

> [!NOTE]
> Wymagania te dotyczą odczytu karty urządzenia używane w transakcji zaprezentowanie karty (to znaczy, przejdź do karty lub dip) w punkcie sprzedaży. To wymaganie nie jest przeznaczona do zastosowania do ręcznego wprowadzania klucza elementów takich jak klawiatury komputera i klawiatury POS. 

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso korzysta z platformy Azure do rejestrowania wszystkich zmian w systemie.<br /><br />Analiza dzienników zapewnia szczegółowe rejestrowanie zmian. Zmiany można przejrzeć i sprawdzić dokładność. Aby uzyskać bardziej szczegółowe wskazówki, zobacz [wskazówki PCI - rejestrowanie i inspekcja](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>Wymaganie PCI DSS 9.9.1

**9.9.1** Obsługa aktualna lista urządzeń. Lista powinna zawierać następujące czynności:
- Marka i model urządzenia
- Lokalizacja urządzenia (na przykład adres lub urządzenie, na którym znajduje się urządzenie)
- Numer seryjny urządzenia lub innej metody unikatowych identyfikatorów

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso zawiera architektura referencyjna oraz listę wszystkich usług używanych w dokumentacji wdrożenia.|



### <a name="pci-dss-requirement-992"></a>Wymaganie PCI DSS 9.9.2

**9.9.2** okresowo sprawdzać powierzchni urządzenia można wykryć naruszeniu (na przykład oprócz karty skimmers na urządzeniach) lub podstawienia (na przykład, sprawdzając numer seryjny lub inne urządzenie właściwości w celu weryfikacji ma nie została zamieniona na fałszywych urządzenia).

> [!NOTE]
> Znaki, czy urządzenie może zostały zmodyfikowane lub zastąpione przykłady załączniki nieoczekiwany lub kable podłączone do urządzenia, brakujące lub zmienione etykiety zabezpieczeń, uszkodzenie lub inaczej kolorowe wielkości liter lub zmiany numeru seryjnego lub innych Oznakowanie zewnętrzne.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-993"></a>Wymaganie PCI DSS 9.9.3

**9.9.3** Zapewnij szkolenie odpowiednie do pracowników, należy pamiętać o naruszeniu próba lub zastąpienie urządzeń. Szkolenie powinno są następujące:
- Sprawdź, czy tożsamość osób trzecich się personelu naprawy lub konserwacji, przed przyznaniem im dostępu do modyfikacji lub rozwiązywać problemy dotyczące urządzeń.
- Nie zainstalować, Zamień lub zwraca urządzenia bez weryfikacji.
- Należy pamiętać o podejrzane zachowanie wokół urządzeń (na przykład prób przez osoby nieznany odłączyć lub otworzyć urządzenia).
- Raport podejrzane zachowanie i oznaczeń naruszeniu urządzenia lub podstawienia do odpowiedniego personelu (na przykład, aby specjalistę menedżera lub zabezpieczeń).

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



## <a name="pci-dss-requirement-910"></a>Wymaganie PCI DSS 9.10

**9.10** upewnij się, że zasady zabezpieczeń i procedur operacyjnych ograniczania fizyczny dostęp do danych posiadacza są udokumentowane w użyciu i znane wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|




