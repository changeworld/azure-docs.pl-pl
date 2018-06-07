---
title: Pojęcia dotyczące zabezpieczeń X.509 Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Pojęcie — opis wartość X.509 certyfikatu urzędu certyfikatów w produkcji urządzenia IoT i uwierzytelniania.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 1f7a02f66a8d87f33d7bac9068628dbd29e5bd7c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635699"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Pojęć związanych z certyfikatami X.509 urzędu certyfikacji w branży IoT

W tym artykule opisano wartość przy użyciu certyfikatów urzędu certyfikacji certyfikatu X.509 w produkcji urządzenia IoT i uwierzytelniania z Centrum IoT.  Zawiera informacje o zasobach łańcucha Instalatora i zaznacz zalety.

W tym artykule opisano:

* Certyfikaty X.509 urzędu certyfikacji i sposobu ich
* Jak zarejestrować certyfikat X.509 urzędu certyfikacji z Centrum IoT
* Jak skonfigurować produkcyjnym podać łańcucha dla uwierzytelniania opartego na X.509 urzędu certyfikacji
* Jak urządzenia podpisany z urzędem certyfikacji X.509 łączyć się Centrum IoT

## <a name="overview"></a>Przegląd

Uwierzytelnianie certyfikatu urzędu certyfikacji X.509 jest podejścia do uwierzytelniania urządzeń przy użyciu metody, które znacząco upraszcza tożsamości tworzenie i cyklu życia zarządzania urządzeniami w łańcuch dostaw Centrum IoT.

Atrybut wyróżniającą uwierzytelniania X.509 urzędu certyfikacji jest relacji jeden do wielu, którą ma certyfikat urzędu certyfikacji z jego podrzędne urządzeń.  Ta relacja włącza rejestracji dowolną liczbę urządzeń do Centrum IoT rejestrując raz certyfikat X.509 urzędu, w przeciwnym razie urządzenia unikatowych certyfikatów musi być wstępnie zarejestrowanych dla każdego urządzenia przed urządzenie można połączyć. Tej relacji jeden do wielu upraszcza operacji zarządzania cyklem życia certyfikatów urządzeń.

Inny atrybut ważne uwierzytelniania X.509 urzędu certyfikacji jest uproszczenie logistyki łańcucha dostaw.  Bezpieczne uwierzytelnianie urządzenia wymaga, że każdego urządzenia jest przechowywany jako podstawę zaufania Unikatowy klucz tajny, takie jak klucz. W przypadku uwierzytelniania opartego na certyfikatach ten klucz tajny jest klucz prywatny. Typowe urządzenie produkcyjny przepływu obejmuje wiele kroków i nadzorców.  Bezpiecznie zarządzania kluczy prywatnych urządzeń w wielu nadzorców i utrzymywanie relacji zaufania jest trudne i kosztowne.  Przy użyciu urzędów certyfikacji rozwiązuje ten problem, podpisywania każdego niejawnego w kryptograficznych łańcuch zaufania, a nie powierzających im klucze prywatne urządzenia.  Każdy niejawnego podpisuje z kolei urządzenia na ich kroku procesu odpowiednich przepływu produkcji.  Wynik ogólny jest łańcuch dostaw optymalne z wbudowanych odpowiedzialności za pomocą usług kryptograficznych łańcuch zaufania.  Warto zauważyć, że ten proces daje najwyższy poziom zabezpieczeń po ich unikatowy kluczy prywatnych ochrony urządzeń.  W tym celu niniejsza korzystanie z sprzętu Secure modułów (HSM) może generować wewnętrznie kluczy prywatnych, które nigdy nie będą widzieć światła dnia.

Ten artykuł zawiera widok end-to-end przy użyciu uwierzytelniania X.509 urzędu certyfikacji, z Instalatora łańcucha dostaw w celu połączenia urządzeń podczas wprowadzania Użyj przykładu rzeczywistych, aby zwiększyć zrozumienie.

## <a name="introduction"></a>Wprowadzenie

Certyfikat X.509 urzędu certyfikacji jest certyfikat, którego właściciel może podpisywania innych certyfikatów.  Ten certyfikat cyfrowy jest X.509, ponieważ spełnia on certyfikat formatowania standardowego przewidzianych w IETF RFC 5280 standard, a jest urząd certyfikacji (CA), ponieważ jego posiadacz może podpisywania innych certyfikatów.

Korzystanie z urzędu certyfikacji X.509 najlepiej jest rozpoznawany w odniesieniu do konkretnego przykład.  Należy wziąć pod uwagę X firmy, maker inteligentnych-X-widżetów przeznaczony dla specjalistów instalacji. Firma X outsources zarówno produkcyjnej oraz instalacji.  Jej umów producenta Y fabryka do produkcji inteligentnych-X-elementów widget i dostawcy usług Z personelu do zainstalowania. Firma X życzy sobie, że inteligentnych-X-Widget bezpośrednio jest dostarczany z fabryki-Y do personelu Z instalacji i podawał bezpośrednio do firmy-x wystąpienia Centrum IoT po zakończeniu instalacji bez dalszej interwencji z X firmy. Aby to osiągnąć, X firmy, trzeba wykonać kilka operacji jednorazowej konfiguracji zapisują inteligentnych-X-Widget automatyczne połączenie.  Ze scenariuszem end-to-end pamiętać dalszej części tego artykułu jest zbudowany w następujący sposób:

* Uzyskanie certyfikatu X.509 urzędu certyfikacji

* Zarejestruj certyfikat X.509 urzędu certyfikacji z Centrum IoT

* Zaloguj się urządzeń w łańcuch zaufania certyfikatów

* Połączenie z urządzeniem

## <a name="acquire-the-x509-ca-certificate"></a>Uzyskanie certyfikatu X.509 urzędu certyfikacji

Firma X ma możliwość zakupu certyfikatów X.509 urzędu certyfikacji od głównego publicznego urzędu certyfikacji lub jednym z podpisem własnym procesie.  Jedną z opcji będą optymalne siebie w zależności od scenariusza aplikacji.  Niezależnie od opcji proces pociąga za sobą dwa podstawowe kroki, generowanie pary kluczy publiczny/prywatny i podpisywania do certyfikatu klucza publicznego.

![img csr przepływu](./media/csr-flow.png)

Szczegółowe informacje na temat sposobu wykonywania tych kroków różnią się z różnych dostawców usług.

### <a name="purchasing-an-x509-ca-certificate"></a>Zakup certyfikatu X.509 urzędu certyfikacji

Zakup certyfikatu urzędu certyfikacji ma o konieczności act dobrze znanego głównego urzędu certyfikacji jako zaufanego innych firm do ciesząca dla legalności urządzenia IoT podczas łączenia urządzeń. Firma X czy wybierz tę opcję, jeśli użytkownik chce inteligentnych-X-Widget wchodzić w interakcje z innej produktów lub usług po początkowego połączenia z Centrum IoT.

Aby kupić certyfikat X.509 urzędu certyfikacji, firma X wybrać dostawcę usług certyfikatów głównych. Internet wyszukaj frazę "Głównego urzędu certyfikacji" umożliwia uzyskanie dobrej potencjalnych klientów.  Główny urząd certyfikacji przeprowadzi X firmy na tworzenie pary kluczy publiczny/prywatny i sposób generowania certyfikatu podpisywania żądania (CSR) dla swoich usług.  Renderowanie po stronie klienta jest posiadanie proces stosowania certyfikatu od urzędu certyfikacji.  Wynikiem tego zakupu jest certyfikatu jako certyfikatu urzędu certyfikacji.  Biorąc pod uwagę powszechności certyfikatów X.509, certyfikat prawdopodobnie zostały prawidłowo sformatowane do jego IETF RFC 5280 standardowa.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Tworzenie certyfikatu z podpisem własnym X.509 z urzędu certyfikacji

Utwórz certyfikat z podpisem własnym X.509 certyfikacji proces jest podobny do zakupu z wyjątkiem obejmujące podpisujący innych firm, takich jak główny urząd certyfikacji. W tym przykładzie firma X podpisze swój certyfikat urzędu zamiast główny urząd certyfikacji.  Firma X może wybrać tę opcję, do testowania, aż gotowość zakupienie certyfikatu urzędu. Firma X może również użyć certyfikatu X.509 urzędu certyfikacji z podpisem własnym w środowisku produkcyjnym, jeśli inteligentnych-X-Widget nie jest przeznaczona do nawiązania połączenia wszelkich usług innych firm poza Centrum IoT.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Zarejestruj certyfikat X.509 do Centrum IoT

Firma X musi zarejestrować do Centrum IoT, w którym będzie służyć do uwierzytelnienia inteligentnych-X-elementy widget, ponieważ łączą X.509 urzędu certyfikacji.  Jest to jednorazowe proces, który daje możliwość uwierzytelniania i zarządzania dowolnej liczby urządzeń inteligentnych-X-elementu Widget.  Ten proces jest jednorazowe z powodu relacji jeden do wielu między certyfikatu urzędu certyfikacji i urządzenia i również stanowi jeden z głównych zalet przy użyciu metody uwierzytelniania X.509 urzędu certyfikacji.  Alternatywą jest przekazywanie odciski palców certyfikat dla każdego urządzenia inteligentnych-X-Widget co dodawanie do koszty operacyjne.

Rejestracja certyfikatu X.509 urzędu certyfikacji jest procesem dwuetapowym, przekazywanie certyfikatu i certyfikat dowód z posiadania.

![img pop przepływu](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Przekazywanie certyfikatu X.509 urzędu certyfikacji

Certyfikat X.509 urzędu certyfikacji Przekaż procesu jest po prostu, Przekaż certyfikat urzędu certyfikacji do Centrum IoT.  Centrum IoT oczekuje certyfikatu w pliku. Firma X po prostu przekazuje plik certyfikatu. Plik certyfikatu nie może w żadnym zawierać dowolnych kluczy prywatnych.  Najlepsze rozwiązania z standardów dotyczących infrastruktury kluczy publicznych (PKI) ma wiedzy firmy-x prywatny znajduje się w tym przypadku wyłącznie w obrębie firmy-X.

### <a name="proof-of-possession-of-the-certificate"></a>Potwierdzenie programu posiadania certyfikatu

Certyfikat X.509 urzędu certyfikacji, podobnie jak wszelkie certyfikatu cyfrowego jest publiczny informacje, które są podatne na podsłuchiwaniu.  W efekcie przechwytującym może przechwytywać certyfikat i spróbuj przekazać go jako własne.  W naszym przykładzie Centrum IoT chce upewnij się, że certyfikat urzędu certyfikacji, który przekazuje X firmy rzeczywiście należy do firmy-X. Tak więc trudne x firmy na dowód, że w rzeczywistości posiadają certyfikatu za pomocą [przepływu dowód z posiadania (PoP)](https://tools.ietf.org/html/rfc5280#section-3.1). Przepływ "dowodu posiadania" pociąga za sobą Centrum IoT Generowanie liczby losowej podpisem firmy-X przy użyciu jego klucz prywatny.  Jeśli firma X oraz najlepsze rozwiązania w zakresie infrastruktury kluczy publicznych i chronione własnego klucza prywatnego następnie tylko ich będzie w stanie poprawnie odpowiadanie na żądanie dowodu z posiadania.  Centrum IoT będzie kontynuowana, aby zarejestrować certyfikat X.509 urząd certyfikacji po pomyślnej odpowiedzi żądania "dowodu posiadania".

Odpowiedź na pytanie "dowodu posiadania" powiodło się z Centrum IoT zakończeniu rejestracji X.509 urzędu certyfikacji.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Zaloguj się urządzeń w łańcuch zaufania certyfikatów

IoT wymaga każde urządzenie, aby mieć unikatową tożsamość.  Są tymi tożsamościami w certyfikatach formularza dla schematów uwierzytelniania opartego na certyfikatach.  W naszym przykładzie oznacza to, że każdy inteligentnych-X-Widget musi mieć certyfikat unikatowych urządzeń.  Jak firma X Instalator w tym w jego łańcuch dostaw?

Jednym ze sposobów przejdź na ten temat jest wstępnie wygenerować certyfikaty dla inteligentnych-X-elementów widget i powierzenia znajomość odpowiadające im klucze prywatne unikatowych urządzeń z dostawcami.  X firmy oznacza to powierzenia Y fabryki i personelu —.  Jest to prawidłowe metody, pochodzi z wyzwania, które należy rozwiązać do zapewnienia zaufania w następujący sposób:

1. Konieczności udostępniania kluczy prywatnych urządzeń z dostawcami, oprócz ignorowanie infrastruktury kluczy publicznych najlepszych rozwiązań nigdy nie udostępniania kluczy prywatnych, umożliwia tworzenie relacji zaufania w łańcuch dostaw kosztowne.  Oznacza to kapitału systemów, takich jak bezpieczny pomieszczenia do kluczy prywatnych urządzeń dom i procesy, takie jak inspekcji zabezpieczeń muszą być zainstalowane.  Koszt obu dodać do łańcucha dostaw.

2. Bezpiecznie ewidencjonowanie aktywności dla urządzeń w łańcuch dostaw i zarządzania nimi później we wdrożeniu staje się jeden do jednego zadania dla każdej pary klucz do urządzenia z punktu generacji (klucz prywatny dlatego) unikatowy certyfikat urządzenia do wycofania urządzenia. Wyklucza grupy zarządzania urządzeniami, chyba że pojęcie grup jest jawnie wbudowana w procesie jakiś sposób. Bezpieczne ewidencjonowania i urządzeń — Zarządzanie cyklem życia, w związku z tym staje się operacje duże obciążenie.  W tym przykładzie firma X będzie zawierać to obciążenie.

Uwierzytelnianie certyfikatu X.509 urzędu certyfikacji zapewnia elegancki rozwiązania wyżej wymienione problemy przy użyciu łańcucha certyfikatów.  Powoduje łańcucha certyfikatów od urzędu certyfikacji podpisywania pośredniego urzędu certyfikacji, który z kolei podpisuje innego pośredniego urzędu certyfikacji i dlatego przejdzie w aż do końcowej pośredni urząd certyfikacji podpisuje urządzenia.  W tym przykładzie firma X podpisuje fabryki-Y, która z kolei podpisuje Z technika, koniec logujący inteligentnych-X-elementu Widget.

![img-cert — łańcuch hierarchii](./media/cert-chain-hierarchy.png)

Powyżej cascade certyfikatów w łańcuchu przedstawia logicznej zapasów — wyłączone urzędu.  Wiele łańcuchami wykonaj to logicznej zapasów — wyłączone zgodnie z którymi każdego pośredniego urzędu certyfikacji pobiera podpisany w łańcuchu podczas odbierania nadrzędnego certyfikaty urzędu certyfikacji, a ostatni pośredni urząd certyfikacji podpisuje koniec każdego urządzenia i wstrzyknąć wszystkie certyfikaty urzędu z łańcucha na urządzeniu. Jest to typowe, gdy firmy produkcyjnej umowy w ramach hierarchii fabryk zleca fabrykę konkretnego celu produkcji.  Hierarchia może być kilka poziomów głębokości (na przykład według lokalizacji geograficznej/produktu typu i produkcyjny wiersza), tylko fabryki na końcu pobiera do interakcji z urządzeniem, ale łańcucha jest obsługiwane na szczycie hierarchii.

Alternatywne łańcuchów może mieć interakcji z urządzeniem, w których przypadku urzędu certyfikacji interakcji z urządzeniem injects zawartość łańcucha certyfikatu w tym momencie różnych pośredniego urzędu certyfikacji.  Modele hybrydowe możliwe są również których tylko niektóre z urzędu certyfikacji jest fizyczny interakcji z urządzeniem.

W naszym przykładzie zarówno fabryki-Y, jak i Z technika współdziałają z widżetu X inteligentnych.  Gdy firma X jest właścicielem inteligentnych-X-Widget, faktycznie nie fizycznie współdziała z nim w łańcuch dostaw całego.  Łańcuch certyfikatów zaufania widżetu inteligentnych-X obejmować X firmy podpisywania Y fabryki, która z kolei zaloguje się Z produkcyjnym udostępniającą następnie końcowego podpisu do inteligentnych-X-elementu Widget. Producent i instalacji inteligentnych-X-Widget obejmują fabryki-Y i personelu — przy użyciu ich pośrednich urzędów certyfikacji do podpisywania każdego inteligentnych-X-elementy widget. W rezultacie całego procesu jest inteligentnych-X-elementy widget certyfikaty unikatowych urządzeń i łańcucha certyfikatów mają certyfikat urzędu certyfikacji firmy-X.

![img--mfr — łańcuch certyfikatów](./media/cert-mfr-chain.png)

Jest to dobry punkt, aby przejrzeć wartość metody X.509 urzędu certyfikacji.  Zamiast wstępnie Generowanie i przekazywanie certyfikatów co inteligentnych-X-elementu widget do łańcucha dostaw, firma X ręcznie tylko raz logowania fabryki-Y.  Zamiast do śledzenia wszystkich urządzeń w cyklu życia urządzenia X firmy nie może śledzić i zarządzanie urządzeniami za pomocą grup, które naturalnie żądano proces łańcucha dostaw, na przykład zainstalowanych przez technika Z po lipca roku niektórych urządzeń.

Ostatnio, ale nie najmniej urzędu certyfikacji metodę uwierzytelniania infuses bezpiecznego accountability na urządzeniu łańcucha dostaw produkcyjnego. Z powodu proces łańcucha certyfikatów działania każdego elementu w łańcuchu jest kryptograficznie zarejestrowane i możliwe do zweryfikowania.

Proces ten polega na pewne założenia, które muszą być udostępniane aby informacje były kompletne.  Wymaga to niezależnie od utworzenia urządzenia unikatowy publicznego i prywatnego pary kluczy i ochronę klucza prywatnego w urządzeniu.  Na szczęście istnieje mikroukłady bezpiecznego krzemu w formularzu z sprzętu bezpiecznych modułów (HSM) wewnętrznie generowania kluczy i ochrona kluczy prywatnych.  Firma X tylko należy dodać jedną z takich mikroukłady do inteligentnych-X-widżetu BOM składnika.

## <a name="device-connection"></a>Połączenie z urządzeniem

Poprzednich sekcjach powyżej ma zostały Tworzenie połączenia urządzenia.  Po prostu rejestrując X.509 urzędu certyfikacji, aby Centrum IoT jedną czasu, jak potencjalnie milionów urządzeń połączenia i pobrania uwierzytelniony z po raz pierwszy?  Proste; za pomocą tego samego przekazywanie certyfikatu i dowód z posiadania przepływu wcześniej wystąpił rejestrację certyfikatu X.509 urzędu certyfikacji.

Wytwarzane dla uwierzytelniania X.509 urzędu certyfikacji są wyposażone w unikatowych certyfikatów urządzenie i łańcuch certyfikatów z odpowiednich urządzeń produkcyjny łańcuch dostaw.  Połączenie z urządzeniem, nawet po raz pierwszy, odbywa się w dwóch etapach: łańcuch przekazywania i dowód z posiadania certyfikatu.

W trakcie przekazywania łańcucha certyfikatów urządzenie przekazuje jego urządzenia unikatowy certyfikat wraz z łańcucha certyfikatów instalowane w ramach go do Centrum IoT.  Za pomocą wstępnie zarejestrowanych certyfikatu X.509 urzędu certyfikacji, Centrum IoT kryptograficznie sprawdzić kilka rzeczy, zgodnego łańcucha certyfikatów przekazane wewnętrznie i zainicjowanej łańcucha przez właściciela prawidłowy certyfikat X.509 urzędu certyfikacji.  Właśnie została wykonana z użyciem proces rejestracji X.509 urzędu certyfikacji, Centrum IoT może zainicjować proces "dowodu posiadania" żądanie odpowiedź, aby ustalić, że łańcucha i dlatego faktycznie należy certyfikatu urządzenia na urządzeniu przekazać go.  Robi to przez generowania losowego żądanie podpisania przez urządzenie przy użyciu jego klucz prywatny do weryfikacji przez Centrum IoT.   Odpowiedź oznaczająca Powodzenie wyzwala Centrum IoT, aby zaakceptować urządzenia jako autentyczny i przyznać jej połączenia.

W naszym przykładzie każdy inteligentnych-X-widżet czy przekazać jego urządzenia unikatowy certyfikat wraz z fabryki Y i urzędu certyfikacji X.509 technika Z certyfikatów, następnie odpowiada na żądanie dowodu z posiadania z Centrum IoT.

![img urządzenia pop przepływu](./media/device-pop-flow.png)

Należy zauważyć, że podstawę zaufania opiera się w zakresie ochrony kluczy prywatnych, w tym do kluczy prywatnych urządzeń.  W związku z tym nie szczególną wystarczająco znaczenie bezpiecznego krzemu chips w formularzu z sprzętu bezpiecznych modułów (HSM) do ochrony kluczy prywatnych urządzeń i ogólne najlepsze rozwiązanie polegające na nigdy nie udostępnianie dowolnych kluczy prywatnych, takich jak fabrykę jeden inny powierzenia jego klucz prywatny.

