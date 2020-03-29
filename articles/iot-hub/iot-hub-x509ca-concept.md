---
title: Pojęcia zabezpieczeń usługi Azure IoT Hub X.509 | Dokumenty firmy Microsoft
description: Koncepcja — opis wartości certyfikatów urzędu certyfikacji X.509 w produkcji urządzeń IoT i uwierzytelniania.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61320511"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Koncepcyjne zrozumienie certyfikatów X.509 CA w branży IoT

W tym artykule opisano wartość używania certyfikatów urzędu certyfikacji X.509 (CA) w produkcji urządzeń IoT i uwierzytelniania w centrum IoT Hub. Zawiera informacje na temat konfiguracji łańcucha dostaw i wyróżnić zalety.

W tym artykule opisano:

* Czym są certyfikaty urzędu certyfikacji X.509 i jak je uzyskać

* Jak zarejestrować certyfikat urzędu certyfikacji X.509 w centrum IoT Hub

* Jak skonfigurować łańcuch dostaw produkcji dla uwierzytelniania opartego na ucho jako certyfikatu C.019

* Jak urządzenia podpisane za pomocą usługi X.509 CA łączą się z centrum IoT Hub

## <a name="overview"></a>Omówienie

Uwierzytelnianie urzędu certyfikacji (CA) X.509 to podejście do uwierzytelniania urządzeń w centrum IoT Hub przy użyciu metody, która znacznie upraszcza tworzenie tożsamości urządzenia i zarządzanie cyklem życia w łańcuchu dostaw.

Wyróżniającym atrybutem uwierzytelniania urzędu certyfikacji X.509 jest relacja jeden-do-wielu certyfikat urzędu certyfikacji z jego urządzeniami podrzędnymi. Ta relacja umożliwia rejestrację dowolnej liczby urządzeń w Centrum IoT przez zarejestrowanie certyfikatu urzędu certyfikacji X.509 raz, w przeciwnym razie unikatowe certyfikaty urządzenia muszą być wstępnie zarejestrowane dla każdego urządzenia, zanim urządzenie będzie można połączyć. Ta relacja jeden do wielu upraszcza również operacje zarządzania cyklem życia certyfikatów urządzeń.

Innym ważnym atrybutem uwierzytelniania X.509 CA jest uproszczenie logistyki łańcucha dostaw. Bezpieczne uwierzytelnianie urządzeń wymaga, aby każde urządzenie miało unikatowy klucz tajny, taki jak klucz jako podstawa zaufania. W uwierzytelnianiu opartym na certyfikatach ten klucz tajny jest kluczem prywatnym. Typowy przepływ produkcji urządzenia obejmuje wiele kroków i opiekunów. Bezpieczne zarządzanie kluczami prywatnymi urządzeń w wielu opiekunach i utrzymywanie zaufania jest trudne i kosztowne. Użycie urzędów certyfikacji rozwiązuje ten problem, podpisując każdego opiekuna w kryptograficznym łańcuchu zaufania, zamiast powierzać im klucze prywatne urządzenia. Każdy opiekun z kolei podpisuje urządzenia na odpowiednim etapie procesu przepływu produkcji. Ogólny wynik to optymalny łańcuch dostaw z wbudowaną odpowiedzialnością dzięki wykorzystaniu kryptograficznego łańcucha zaufania. Warto zauważyć, że proces ten zapewnia największe bezpieczeństwo, gdy urządzenia chronią swoje unikalne klucze prywatne. W tym celu wzywamy do korzystania z modułów sprzętowych bezpiecznych (HSM) zdolnych do wewnętrznego generowania kluczy prywatnych, które nigdy nie ujrzą światła dziennego.

Ten artykuł zawiera kompleksowy widok używania uwierzytelniania urzędu certyfikacji X.509, od konfiguracji łańcucha dostaw po połączenie z urządzeniem, przy jednoczesnym wykorzystaniu przykładu świata rzeczywistego w celu umocnienia zrozumienia.

## <a name="introduction"></a>Wprowadzenie

Certyfikat urzędu certyfikacji X.509 jest certyfikatem cyfrowym, którego posiadacz może podpisać inne certyfikaty. Ten certyfikat cyfrowy jest X.509, ponieważ jest zgodny ze standardem formatowania certyfikatów określonym przez standard RFC 5280 IETF i jest urzędem certyfikacji, ponieważ jego posiadacz może podpisywać inne certyfikaty.

Użycie X.509 CA jest najlepiej rozumiane w odniesieniu do konkretnego przykładu. Rozważmy Company-X, producent Smart-X-Widgets przeznaczone do profesjonalnej instalacji. Firma-X zleca zarówno produkcję, jak i instalację. To kontrakty producenta Factory-Y do produkcji Smart-X-Widgets i usługodawca Technician-Z zainstalować. Firma-X pragnie, aby Smart-X-Widget został dostarczony bezpośrednio z Factory-Y do Technician-Z w celu instalacji i że łączy się bezpośrednio z wystąpieniem Usługi IoT Hub firmy-X po instalacji bez dalszej interwencji firmy X. Aby tak się stało, firma-X musi wykonać kilka jednorazowych operacji konfiguracyjnych, aby zagruntować Smart-X-Widget dla automatycznego połączenia. Mając na uwadze scenariusz end-to-end, reszta tego artykułu jest skonstruowana w następujący sposób:

* Uzyskiwanie certyfikatu urzędu certyfikacji X.509

* Rejestrowanie certyfikatu urzędu certyfikacji X.509 w centrum IoT Hub

* Podpisywanie urządzeń w łańcuchu zaufania certyfikatów

* Połączenie urządzenia

## <a name="acquire-the-x509-ca-certificate"></a>Uzyskiwanie certyfikatu urzędu certyfikacji X.509

Firma-X ma możliwość zakupu certyfikatu urzędu certyfikacji X.509 od publicznego głównego urzędu certyfikacji lub utworzenia go za pośrednictwem procesu podpisu własnego. Jedna opcja będzie optymalna w stosunku do innych w zależności od scenariusza aplikacji. Niezależnie od opcji proces ten wymaga dwóch podstawowych kroków, generowania pary kluczy publicznych/prywatnych i podpisywania klucza publicznego w certyfikacie.

![Przepływ do generowania certyfikatów X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Szczegółowe informacje na temat wykonywania tych kroków różnią się w zależności od dostawcy usług.

### <a name="purchasing-an-x509-ca-certificate"></a>Zakup certyfikatu urzędu certyfikacji X.509

Zakup certyfikatu urzędu certyfikacji ma zaletę, że dobrze znany główny urząd certyfikacji działa jako zaufana strona trzecia, aby ręczyć za legalność urządzeń IoT, gdy urządzenia się łączą. Firma-X wybrałaby tę opcję, jeśli zamierza smart-X-Widget do interakcji z produktami lub usługami innych firm po początkowym połączeniu z Centrum IoT Hub.

Aby zakupić certyfikat urzędu certyfikacji X.509, firma-X wybrałaby dostawcę usług certyfikatów głównych. Wyszukiwanie w Internecie dla wyrażenia "Root CA" przyniesie dobre przewody. Główny urząd certyfikacji poprowadzi firmę X na temat tworzenia pary kluczy publicznych/prywatnych oraz sposobu generowania żądania podpisywania certyfikatów (CSR) dla ich usług. CSR to formalny proces ubiegania się o certyfikat od urzędu certyfikacji. Wynikiem tego zakupu jest certyfikat do użycia jako certyfikat urzędu. Biorąc pod uwagę wszechobecność certyfikatów X.509, certyfikat prawdopodobnie został prawidłowo sformatowany zgodnie ze standardem RFC 5280 IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Tworzenie certyfikatu urzędu certyfikacji typu self-signed X.509

Proces tworzenia certyfikatu urzędu certyfikacji z podpisem własnym X.509 jest podobny do zakupu, z wyjątkiem angażowania osoby podpisującej innej firmy, takiej jak główny urząd certyfikacji. W naszym przykładzie firma-X podpisze certyfikat urzędu zamiast głównego urzędu certyfikacji. Firma-X może wybrać tę opcję do testowania, dopóki nie będzie gotowa do zakupu certyfikatu urzędu. Firma-X może również używać samodzielnie podpisanego certyfikatu urzędu certyfikacji X.509 w produkcji, jeśli smart-X-Widget nie jest przeznaczony do łączenia się z usługami innych firm poza Centrum IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Zarejestruj certyfikat X.509 w Urzędzie IoT Hub

Firma-X musi zarejestrować urząd certyfikacji X.509 w centrum IoT Hub, gdzie będzie służyć do uwierzytelniania widżetów Smart-X podczas łączenia. Jest to jednorazowy proces, który umożliwia uwierzytelnienie i zarządzanie dowolną liczbą urządzeń Smart-X-Widget. Ten proces jest jednorazowy ze względu na relację jeden do wielu między certyfikatem urzędu a urządzeniami, a także stanowi jedną z głównych zalet korzystania z metody uwierzytelniania urzędu certyfikacji X.509. Alternatywą jest przesłanie pojedynczych odcisków palców certyfikatu dla każdego urządzenia Smart-X-Widget, zwiększając w ten sposób koszty operacyjne.

Rejestrowanie certyfikatu urzędu certyfikacji X.509 jest procesem dwuetapowym, przekazywaniem certyfikatu i dowodem posiadania certyfikatu.

![Rejestrowanie certyfikatu X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Przekazywanie certyfikatu urzędu certyfikacji X.509

Proces przekazywania certyfikatów urzędu certyfikacji X.509 jest właśnie taki, który przekazuje certyfikat urzędu certyfikacji do centrum IoT Hub. Usługa IoT Hub oczekuje certyfikatu w pliku. Firma-X po prostu przesyła plik certyfikatu. Plik certyfikatu NIE MOŻE w żadnym wypadku zawierać żadnych kluczy prywatnych. Najlepsze praktyki ze standardów dotyczących infrastruktury kluczy publicznych (PKI) nakazują, aby znajomość prywatnej firmy-X w tym przypadku mieściła się wyłącznie w ramach firmy X.

### <a name="proof-of-possession-of-the-certificate"></a>Dowód posiadania certyfikatu

Certyfikat urzędu certyfikacji X.509, podobnie jak każdy certyfikat cyfrowy, jest informacją publiczną, która jest podatna na podsłuchiwanie. W związku z tym podsłuch może przechwycić certyfikat i spróbować przesłać go jako swój własny. W naszym przykładzie Centrum IoT pragnie upewnić się, że certyfikat urzędu certyfikacji, który firma-X przesyła, naprawdę należy do firmy X. Czyni to poprzez wyzwanie Company-X do dowodu, że w rzeczywistości posiadają certyfikat poprzez [dowód posiadania (PoP) przepływu](https://tools.ietf.org/html/rfc5280#section-3.1). Przepływ weryfikacji posiadania pociąga za sobą, że centrum IoT Hub generuje losową liczbę podpisaną przez firmę-X przy użyciu klucza prywatnego. Jeśli firma-X postępowała zgodnie z najlepszymi praktykami w zakresie infrastruktury kluczy publicznych i chroniła ich klucz prywatny, tylko oni byliby w stanie poprawnie odpowiedzieć na wyzwanie dotyczące dowodu posiadania. Usługa IoT Hub rejestruje certyfikat urzędu certyfikacji X.509 po pomyślnej odpowiedzi na wyzwanie weryfikacji posiadania.

Skuteczna odpowiedź na wyzwanie weryfikacji posiadania z usługi IoT Hub kończy rejestrację urzędu certyfikacji X.509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Podpisywanie urządzeń w łańcuchu zaufania certyfikatów

IoT wymaga, aby każde urządzenie posiadało unikatową tożsamość. Te tożsamości znajdują się w certyfikatach formularza dla schematów uwierzytelniania opartych na certyfikatach. W naszym przykładzie oznacza to, że każdy Smart-X-Widget musi posiadać unikatowy certyfikat urządzenia. W jaki sposób firma-X przygotowuje się do tego w swoim łańcuchu dostaw?

Jednym ze sposobów, aby przejść o tym jest wstępnie wygenerować certyfikaty dla Smart-X-Widgets i powierzając wiedzę o odpowiednich unikalnych kluczy prywatnych urządzenia z partnerami łańcucha dostaw. Dla firmy X oznacza to powierzenie Factory-Y i Technician-Z. Chociaż jest to prawidłowa metoda, wiąże się z wyzwaniami, które należy przezwyciężyć, aby zapewnić zaufanie w następujący sposób:

1. Konieczność udostępniania kluczy prywatnych urządzeń partnerom łańcucha dostaw, oprócz ignorowania najlepszych praktyk infrastruktury kluczy publicznych, które uniemożliwia udostępnianie kluczy prywatnych, sprawia, że budowanie zaufania do łańcucha dostaw jest kosztowne. Oznacza to, że systemy kapitałowe, takie jak bezpieczne pomieszczenia do pomieszczeń prywatnych urządzeń, i procesy, takie jak okresowe audyty bezpieczeństwa, muszą być zainstalowane. Oba zwiększają koszty łańcucha dostaw.

2. Bezpieczne rozliczanie urządzeń w łańcuchu dostaw, a później zarządzanie nimi podczas wdrażania staje się zadaniem jeden do jednego dla każdej pary klucz-urządzenie od momentu generowania unikatowego certyfikatu urządzenia (a więc klucza prywatnego) do wycofania urządzenia. Wyklucza to zarządzanie grupami urządzeń, chyba że pojęcie grup jest w jakiś sposób wbudowane w proces. Bezpieczne zarządzanie księgowością i cyklem życia urządzeń staje się zatem dużym obciążeniem dla operacji. W naszym przykładzie Firma-X poniesie ten ciężar.

Uwierzytelnianie certyfikatów urzędu certyfikacji X.509 oferuje eleganckie rozwiązania dla wymienionych wyzwań dzięki wykorzystaniu łańcuchów certyfikatów. Łańcuch certyfikatów wynika z podpisania przez urząd certyfikacji pośredniego urzędu certyfikacji, który z kolei podpisuje inny pośredni urząd certyfikacji i tak trwa do momentu, gdy końcowy pośredni urząd certyfikacji podpisze urządzenie. W naszym przykładzie, Company-X znaki Factory-Y, co z kolei znaki Technician-Z, że w końcu podpisuje Smart-X-Widget.

![Hierarchia łańcucha certyfikatów](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Powyżej kaskady certyfikatów w łańcuchu przedstawia logiczne przekazanie władzy. Wiele łańcuchów dostaw stosuje się do tego logicznego przekazania, w którym każdy pośredni urząd certyfikacji jest logowany do łańcucha podczas odbierania wszystkich certyfikatów urzędu certyfikacji wyższego szczebla, a ostatni pośredni urząd certyfikacji ostatecznie podpisuje każde urządzenie i wstrzykuje wszystkie certyfikaty urzędu z łańcucha do urządzenia. Jest to powszechne, gdy firma produkcyjna kontraktowa z hierarchią fabryk zleca konkretnej fabryce produkcję. Podczas gdy hierarchia może być kilka poziomów głębokości (na przykład według geografii/typu produktu/linii produkcyjnej), tylko fabryka na końcu dostaje do interakcji z urządzeniem, ale łańcuch jest utrzymywany od góry hierarchii.

Alternatywne łańcuchy mogą mieć różne pośrednie urzędy certyfikacji interakcji z urządzeniem, w którym to przypadku urząd certyfikacji interakcji z urządzeniem wstrzykuje zawartość łańcucha certyfikatów w tym momencie. Modele hybrydowe są również możliwe, gdy tylko niektóre urzędy certyfikacji mają fizyczną interakcję z urządzeniem.

W naszym przykładzie zarówno Factory-Y, jak i Technician-Z wchodzą w interakcję z widżetem Smart-X.In our example, both Factory-Y and Technician-Z interact with the Smart-X-Widget. Chociaż Firma-X jest właścicielem Smart-X-Widget, w rzeczywistości nie wchodzi w interakcję fizycznie z nim w całym łańcuchu dostaw. Łańcuch zaufania certyfikatów dla Smart-X-Widget obejmuje zatem podpisanie Factory-Y przez firmę X, które z kolei podpisuje Technician-Z, które następnie zapewni ostateczny podpis Smart-X-Widget. Produkcja i instalacja Smart-X-Widget obejmują Factory-Y i Technician-Z przy użyciu odpowiednich certyfikatów pośredniego urzędu certyfikacji do podpisania każdego Smart-X-Widgets. Efektem końcowym całego procesu są widżety Smart-X z unikatowymi certyfikatami urządzeń i łańcuchem zaufania certyfikatów, które przechodzą do certyfikatu urzędu certyfikacji company-x.

![Łańcuch zaufania od certyfikatów jednej firmy do certyfikatów innej firmy](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Jest to dobry punkt do przeglądu wartości metody X.509 CA. Zamiast wstępnie generować i przekazywać certyfikaty dla każdego Smart-X-Widget do łańcucha dostaw, Firma-X musiała podpisać Factory-Y tylko raz. Zamiast śledzić każde urządzenie w całym cyklu życia urządzenia, Firma-X może teraz śledzić i zarządzać urządzeniami za pośrednictwem grup, które naturalnie wynikają z procesu łańcucha dostaw, na przykład urządzeń zainstalowanych przez Technician-Z po lipcu pewnego roku.

Wreszcie, metoda uwierzytelniania urzędu certyfikacji napełnia bezpieczną odpowiedzialność w łańcuchu dostaw produkcji urządzeń. Ze względu na proces łańcucha certyfikatów akcje każdego członka w łańcuchu są rejestrowane kryptograficznie i weryfikowalne.

Proces ten opiera się na pewnych założeniach, które muszą być powierzchniowe dla kompletności. Wymaga niezależnego utworzenia urządzenia unikatowa para klucza publicznego/ prywatnego i że klucz prywatny być chronione w urządzeniu. Na szczęście istnieją bezpieczne chipy krzemowe w postaci sprzętowych modułów bezpiecznych (HSM) zdolnych do wewnętrznego generowania kluczy i ochrony kluczy prywatnych. Firma-X wystarczy dodać jeden z takich chipów do komponentu Smart-X-Widget.

## <a name="device-connection"></a>Połączenie urządzenia

Poprzednie sekcje powyżej zostały budowania do połączenia urządzenia. Po prostu rejestrując certyfikat urzędu certyfikacji X.509 w Urzędzie IoT Hub jeden raz, w jaki sposób potencjalnie miliony urządzeń łączą się i są uwierzytelniane po raz pierwszy?  Proste; poprzez ten sam przesyłanie certyfikatów i przepływ dowodu posiadania, z jakim wcześniej napotkaliśmy rejestrację certyfikatu urzędu certyfikacji X.509.

Urządzenia wyprodukowane do uwierzytelniania X.509 CA są wyposażone w unikatowe certyfikaty urządzenia i łańcuch certyfikatów z ich odpowiedniego łańcucha dostaw produkcyjnych. Połączenie urządzenia, nawet po raz pierwszy, odbywa się w dwuetapowym procesie: przesyłanie łańcucha certyfikatów i dowód posiadania.

Podczas przekazywania łańcucha certyfikatów urządzenie przekazuje unikatowy certyfikat urządzenia wraz z łańcuchem certyfikatów zainstalowanym w nim do usługi IoT Hub. Korzystając z wstępnie zarejestrowanego certyfikatu urzędu certyfikacji X.509, usługa IoT Hub może za pomocą kryptograficznej weryfikacji kilku rzeczy, że łańcuch przekazanych certyfikatów jest wewnętrznie spójny i że łańcuch został zainicjowany przez prawidłowego właściciela certyfikatu urzędu certyfikacji X.509. Po prostu był z X.509 CA procesu rejestracji, IoT Hub zainicjuje proces dowodu posiadania wyzwanie odpowiedzi, aby upewnić się, że łańcuch, a tym samym certyfikat urządzenia rzeczywiście należy do urządzenia przesyłania go. Robi to, generując losowe wyzwanie, które ma być podpisane przez urządzenie przy użyciu klucza prywatnego do sprawdzania poprawności przez centrum IoT Hub. Pomyślna odpowiedź wyzwala Usługi IoT Hub, aby zaakceptować urządzenie jako autentyczne i udzielić mu połączenia.

W naszym przykładzie każdy widżet Smart-X-Widget będzie przesyłać swój unikatowy certyfikat urządzenia wraz z certyfikatami urzędu certyfikacji Factory-Y i Technician-Z X.509, a następnie odpowiadać na wyzwanie weryfikacji posiadania z usługi IoT Hub.

![Przepływ z jednego certyfikatu do drugiego, pop wyzwanie z piasty](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Należy zauważyć, że podstawą zaufania jest ochrona kluczy prywatnych, w tym kluczy prywatnych urządzenia. W związku z tym nie możemy wystarczająco podkreślić znaczenia bezpiecznych chipów krzemowych w postaci sprzętowych modułów bezpiecznych (HSM) do ochrony kluczy prywatnych urządzenia, a także ogólnej najlepszej praktyki, aby nigdy nie udostępniać żadnych kluczy prywatnych, takich jak jedna fabryka powierzająca drugą klucza prywatnego.