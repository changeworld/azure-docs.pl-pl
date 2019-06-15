---
title: Pojęcia dotyczące zabezpieczeń usługi Azure IoT Hub X.509 | Dokumentacja firmy Microsoft
description: Pojęcia — informacje o wartość X.509 certyfikatu certyfikaty w produkcji urządzeń IoT i uwierzytelniania.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61320511"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Pojęć związanych z certyfikatów X.509 urzędu certyfikacji w branży IoT

W tym artykule opisano wartość przy użyciu certyfikatów urzędu certyfikacji certyfikat X.509 w produkcji urządzeń IoT i uwierzytelniania do usługi IoT Hub. Zawiera informacje o dostaw łańcucha Instalator i wyróżnić korzyści.

W tym artykule opisano:

* Certyfikaty X.509 urzędu certyfikacji i jak je uzyskać

* Jak zarejestrować certyfikat X.509 urzędu certyfikacji do usługi IoT Hub

* Jak skonfigurować produkcji podać łańcuchu w przypadku uwierzytelniania opartego na X.509 urzędu certyfikacji

* Sposób łączenia urządzeń podpisany przy użyciu X.509 urzędu certyfikacji do usługi IoT Hub

## <a name="overview"></a>Omówienie

Uwierzytelnianie certyfikatu urzędu certyfikacji X.509 to podejście do uwierzytelniania urządzeń do usługi IoT Hub przy użyciu metody, która znacząco upraszcza identity tworzeniem i cyklu życia zarządzania urządzeniami za pomocą łańcucha dostaw.

Wyróżniającą atrybut uwierzytelniania X.509 urzędu certyfikacji jest relacji jeden do wielu, którą ma certyfikat urzędu certyfikacji, z jego podrzędne urządzeń. Ta relacja umożliwia rejestracji dowolna liczba urządzeń do Centrum IoT, rejestrując certyfikat X.509 urzędu certyfikacji, jeden raz, w przeciwnym razie urządzenie unikatowych certyfikatów musi być wstępnie zarejestrowanych dla każdego urządzenia urządzenie może nawiązać połączenie. Ta relacja jeden do wielu upraszcza również operacje zarządzania cyklem życia certyfikatów urządzeń.

Inny atrybut ważne uwierzytelniania X.509 urzędu certyfikacji jest uproszczenie Logistyka łańcucha dostaw. Bezpieczne uwierzytelnianie urządzenia wymaga, że każde urządzenie przechowuje unikatowy klucz tajny, takie jak klucz jako podstawa dla zaufania. W przypadku uwierzytelniania opartego na certyfikatach ten wpis tajny jest klucz prywatny. Typowe urządzenie produkcyjny przepływu obejmuje wiele kroków i nadzorców. Bezpieczne zarządzanie kluczami prywatnymi urządzenia w kilku nadzorców i utrzymywania zaufania jest trudne i kosztowna. Za pomocą urzędów certyfikacji rozwiązuje ten problem przez podpisywania każdy użytkownik, do usług kryptograficznych łańcuch zaufania, a nie powierzających im kluczy prywatnych urządzeń. Każdy użytkownik z kolei podpisuje urządzeń na etapie ich odpowiednich procesu przepływu produkcji. Ogólny wynik jest optymalne dostaw przy użyciu wbudowanych odpowiedzialność za pomocą usług kryptograficznych łańcuch zaufania. Warto zauważyć, że ten proces daje najwyższy poziom zabezpieczeń urządzenia ochrony swój unikatowy klucz prywatny. W tym celu ponaglamy korzystanie z sprzętu Secure modułów (HSM) może wewnętrznie generować klucze prywatne, które nigdy nie zobaczą światła dnia.

W tym artykule udostępnia widok end-to-end przy użyciu uwierzytelniania X.509 urzędu certyfikacji, z Instalatora łańcucha dostaw w celu połączenia urządzenia podczas wprowadzania umożliwia przykładu rzeczywistych zestalenia opis.

## <a name="introduction"></a>Wprowadzenie

Certyfikat X.509 urzędu certyfikacji jest certyfikatu cyfrowego, którego właściciel może podpisywania innych certyfikatów. Ten certyfikat cyfrowy jest X.509, ponieważ ona jest zgodny z certyfikatem formatowanie standard ustalonym przez standard RFC 5280 IETF firmy i jest urząd certyfikacji (CA), ponieważ jego symbol zastępczy może podpisywania innych certyfikatów.

W odniesieniu do konkretny przykład najlepiej rozumie użycia X.509 urzędu certyfikacji. Należy wziąć pod uwagę firmy-X, twórca inteligentnych-X-widżetów przeznaczone dla profesjonalnych instalacji. Firma X outsources produkcji i instalacji. Producent fabryki-Y, do produkcji inteligentnych-X-elementów widget i dostawcy usług Z technik do zainstalowania go umów. Firma X życzy sobie, że inteligentnych-X-widżet bezpośrednio jest dostarczany z fabryki-Y od a do Z technik do instalacji i że łączy się bezpośrednio do firmy-x wystąpienie usługi IoT Hub po zakończeniu instalacji bez dalszej interwencji z firmą X. Aby to osiągnąć, firmą X, trzeba wykonać kilka operacji jednorazowej konfiguracji zainicjowanie inteligentnych-X-widżetu automatycznego połączenia. W scenariuszu end-to-end, należy pamiętać pozostałej części tego artykułu jest zbudowany w następujący sposób:

* Uzyskiwanie certyfikatu X.509 urzędu certyfikacji

* Zarejestruj certyfikat X.509 urzędu certyfikacji do usługi IoT Hub

* Rejestrowania urządzenia w usłudze łańcuch zaufania certyfikatów

* Połączenie z urządzeniem

## <a name="acquire-the-x509-ca-certificate"></a>Uzyskiwanie certyfikatu X.509 urzędu certyfikacji

Firma X ma możliwość zakupu certyfikat X.509 urzędu certyfikacji od głównego publicznego urzędu certyfikacji lub tworzeniu proces podpisem. Jedną z opcji będą optymalne zamiast drugiego, w zależności od scenariusza aplikacji. Niezależnie od tego, w przypadku opcji proces pociąga za sobą dwa podstawowe kroki generowania pary kluczy publiczny/prywatny i zalogowanie klucz publiczny certyfikatu.

![Przepływ do generowania certyfikatów X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Szczegółowe informacje na temat sposobu wykonywania tych kroków różnią się za pomocą różnych dostawców usług.

### <a name="purchasing-an-x509-ca-certificate"></a>Zakup certyfikatu X.509 urzędu certyfikacji

Zakup certyfikatu urzędu certyfikacji ma tę zaletę mający czynność głównego znanego urzędu certyfikacji jako zaufanego innej firmy do ciesząca legalności urządzenia IoT, łącząc urządzenia. X firmy będzie wybierz tę opcję, jeśli zamierzają inteligentnych X widżetu do interakcji z innych firm, produktów lub usług po zainicjowaniu połączenia z usługą IoT Hub.

Aby kupić certyfikat X.509 urzędu certyfikacji, firmą X wybrać dostawcę usług certyfikatów głównych. Wyszukiwania w Internecie dla frazy "Głównego urzędu certyfikacji" umożliwia uzyskanie dobre potencjalnych klientów. Główny urząd certyfikacji przeprowadzi firmą X na sposób tworzenia pary kluczy publiczny/prywatny i jak można wygenerować żądania podpisania certyfikatu (CSR) dla usług. Renderowanie po stronie klienta jest posiadanie procesu stosowania certyfikatu od urzędu certyfikacji. Wynik ten zakup jest certyfikat do użycia jako certyfikatu urzędu. Biorąc pod uwagę wszechobecności certyfikatów X.509, certyfikat prawdopodobnie zostały prawidłowo sformatowane do firmy IETF RFC 5280 standardowych.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Tworzenia certyfikatu z podpisem własnym X.509 urzędu certyfikacji

Proces, aby utworzyć certyfikat z podpisem własnym X.509 urzędu certyfikacji jest podobne do zakupu, z wyjątkiem obejmujące podpisujący innych firm, takich jak główny urząd certyfikacji. W tym przykładzie firma X podpisze swój certyfikat urzędu zamiast główny urząd certyfikacji. Firma X może tę opcję do testowania, dopóki nie będą one gotowe do zakupu certyfikatu urzędu. Jeśli inteligentnych-X-element Widget nie ma połączyć się z dowolnej usługi innych firm poza usługi IoT Hub X firmy mogą użyć certyfikatu X.509 urzędu certyfikacji z podpisem własnym w środowisku produkcyjnym.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Zarejestruj certyfikat X.509 do usługi IoT Hub

Firma X musi zarejestrować X.509 urzędu certyfikacji do usługi IoT Hub, gdy będzie służyć do uwierzytelniania inteligentnych-X-elementów widget, jak łączą. Jest to jednorazowy proces, który włącza możliwość uwierzytelniania i zarządzania dowolnej liczby urządzeń inteligentnych-X-elementu Widget. Ten proces jest jednorazowe ze względu na relacji jeden do wielu między urzędu certyfikacji i urządzenia i również stanowi jedną z głównych zalet przy użyciu metody uwierzytelniania X.509 urzędu certyfikacji. Alternatywą jest przekazać odciski palców certyfikatu poszczególnych dla każdego urządzenia inteligentne-X-widżet ten sposób dodawania do koszty operacyjne.

Rejestrowanie certyfikatu X.509 urzędu certyfikacji jest procesem dwuetapowym, przekazywanie certyfikatu i dowodu posiadania certyfikatów.

![Zarejestrowanie certyfikatu X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Przekazywanie certyfikatu urzędu certyfikacji X.509

Certyfikat X.509 urzędu certyfikacji przekazywanie procesu jest po prostu, a następnie przekaż certyfikat urzędu certyfikacji do usługi IoT Hub. Usługa IoT Hub oczekuje, że certyfikat w pliku. Firma X po prostu przekazuje plik certyfikatu. Plik certyfikatu nie może w żadnym zawiera żadnych kluczy prywatnych. Najlepsze rozwiązania z standardów dotyczących infrastruktury kluczy publicznych (PKI) określającemu tej wiedzy firmy-x prywatne znajduje się w tym przypadku wyłącznie w obrębie firmą X.

### <a name="proof-of-possession-of-the-certificate"></a>Dowód posiadania certyfikatu

Certyfikat X.509 urzędu certyfikacji, podobnie jak wszelkie certyfikatu cyfrowego jest publiczny informacje, które są podatne na podsłuchiwaniu. W efekcie przechwytującym może przechwytywać certyfikat i spróbuj przekazać go jako swoje własne. W naszym przykładzie Usługa IoT Hub chce upewnij się, że certyfikat urzędu certyfikacji, który jest przekazanie firmą X naprawdę należy firmą X. Robi tak trudne x firmy na dowód, że w rzeczywistości posiadają certyfikatu za pośrednictwem [przepływ dowodu posiadania (PoP)](https://tools.ietf.org/html/rfc5280#section-3.1). Przepływ dowodu posiadania pociąga za sobą IoT Hub i generowania liczbę losową podpisany przez firmę-X za pomocą jego klucza prywatnego. Jeśli firmą X i najlepszych rozwiązań infrastruktury kluczy publicznych i chronione własnego klucza prywatnego tylko ich będzie w stanie prawidłowo odpowie na wezwanie dowodu posiadania. Usługa IoT Hub rozpoczynające się zarejestrować certyfikat X.509 urzędu certyfikacji, po pomyślnej odpowiedzi żądania dowodu posiadania.

Pomyślnej odpowiedzi na wezwanie dowodu posiadania w usłudze IoT Hub kończy rejestracji X.509 urzędu certyfikacji.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Rejestrowania urządzenia w usłudze łańcuch zaufania certyfikatów

IoT wymaga każdego urządzenia będą mieć przypisaną unikatową tożsamość. Te tożsamości są w certyfikatach formularza dla schematów uwierzytelniania opartego na certyfikatach. W tym przykładzie oznacza to, że każdy inteligentnych-X-widżet musi mieć certyfikat unikatowych urządzeń. Jak firmą X Instalator w tym w łańcuchu dostaw?

Jednym ze sposobów, aby przejść na ten temat jest wstępnie generowania certyfikatów dla inteligentnych-X-elementów widget i powierzenia znajomości odpowiadających im kluczy prywatnych unikatowe urządzenia z partnerami łańcucha dostaw. Firmy-X oznacza to powierzenia fabryki Y i Z technik. Gdy ta operacja jest prawidłową metodą, pochodzi z wyzwania, które muszą przezwyciężyć zapewnienie zaufania w następujący sposób:

1. Konieczności udostępniania kluczy prywatnych urządzeń z partnerami łańcucha dostaw, oprócz ignorowanie infrastruktury kluczy publicznych — najlepsze praktyki nigdy nie udostępniania kluczy prywatnych, sprawia, że tworzenie relacji zaufania w łańcuchu dostaw kosztowne. Oznacza to, kapitałowych systemów, takich jak bezpieczny pokoje, do kluczy prywatnych urządzeń dom i procesy, takie jak inspekcje zabezpieczeń muszą być zainstalowane. Koszt zarówno Dodaj do łańcucha dostaw.

2. Bezpiecznie ewidencjonowania aktywności na urządzeniach w łańcuch dostaw i później zarządzać nimi w wdrożenia staje się jeden do jednego zadania dla każdej pary klucza do urządzeń z punktu urządzenia unikatowy certyfikat (klucz prywatny w związku z tym) generacji na wycofanie urządzenia. Wyklucza grupy zarządzania urządzeniami, chyba że pojęcie grup jest jawnie zbudowany z procesem jakiś sposób. Bezpieczne księgowością i urządzeń — Zarządzanie cyklem życia, w związku z tym, staje się obciążeń dużymi operacji. W naszym przykładzie X firmy będzie zawierać tego obciążenia.

Uwierzytelnianie certyfikatu X.509 urzędu certyfikacji oferuje rozwiązania elegancki wyżej wymienione problemy za pomocą łańcuchów certyfikatów. Łańcuch certyfikatów jest wynikiem urząd certyfikacji podpisywania pośrednich urzędów certyfikacji, który z kolei podpisuje innego pośredniego urzędu certyfikacji i dlatego przechodzi aż końcowego pośredni urząd certyfikacji podpisuje urządzenia. W tym przykładzie firma X podpisuje fabryki-Y, która z kolei podpisuje Z technik na koniec użytym do inteligentnej-X-elementu Widget.

![Hierarchii łańcucha certyfikatów](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Powyżej cascade certyfikatów w łańcuchu przedstawia logicznej ręcznie off urzędu. Wiele łańcuchami dostaw postępuj zgodnie z tym logicznej ręcznie off zgodnie z którą każdego pośrednich urzędów certyfikacji pobiera podpisany do łańcucha podczas odbierania wszystkich certyfikatów urzędu certyfikacji nadrzędnego i ostatniego pośredni urząd certyfikacji podpisuje na koniec każdego urządzenia i wstrzyknąć certyfikaty urzędu z łańcucha na urządzeniu. Jest to typowe w przypadku, gdy firmy produkcyjnej związanych umowy z hierarchią fabryk prowizje fabrykę konkretnego celu do produkcji. Przy hierarchii może być kilka poziomów szczegółowe (na przykład według lokalizacji geograficznej/produktu typu/produkcji wiersza), tylko fabrykę na końcu pobiera do interakcji z urządzeniem, ale łańcuch jest obsługiwany z góry hierarchii.

Alternatywne łańcuchów mogą mieć różne interakcji z urządzeniem, w którym przypadku urzędu certyfikacji interakcji z urządzeniem wprowadza zawartość łańcucha certyfikatu w tym momencie pośrednich urzędów certyfikacji. Modele hybrydowe są również możliwe których tylko niektóre z urzędu certyfikacji jest fizyczny interakcji z urządzeniem.

W naszym przykładzie fabryki Y i Z technik wchodzić w interakcje z widżetem X inteligentnych. A firmą X jest właścicielem inteligentnych-X-widżet, faktycznie nie fizycznie współdziała z nim w łańcuch dostaw całego. Łańcuch certyfikatów zaufania widżetu inteligentnych-X obejmować firmą X Y fabryki, które z kolei podpisuje technik-Z, które będą następnie końcowego podpisu widgecie inteligentnych-X podpisywania. Produkcji i instalacji inteligentnych-X-widżet obejmuje fabryki-Y i technik — przy użyciu ich odpowiednich certyfikatów pośrednich urzędów certyfikacji do podpisania każdego inteligentnych-X-elementów widget. Wynik końcowy całego procesu jest inteligentnych-X-widżety przy użyciu certyfikatów urządzeń unikatowy i łańcucha certyfikatów zamierza certyfikatu urzędu certyfikacji z firmą X.

![Łańcuch zaufania certyfikatów jednego firmy certyfikatów innej firmy](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Jest to dobry punkt, aby sprawdzić wartość metody X.509 urzędu certyfikacji. Zamiast wstępnie Generowanie i przekazywanie certyfikatów widżetu co inteligentnych-X-w do łańcucha dostaw, firmą X miał tylko raz się fabryki-Y. Zamiast do śledzenia każdego urządzenia w całym cyklu życia urządzenia, firmą X może teraz śledzenie i zarządzanie urządzeniami za pomocą grup, które naturalnie żądano procesu łańcucha dostaw, na przykład urządzeń instalowane przez Z technik po lipca niektóre roku.

Ostatnie, ale nie najmniej urzędu certyfikacji metodę uwierzytelniania infuses bezpiecznego odpowiedzialność na urządzeniu, na produkcyjny łańcucha dostaw. Ze względu na proces łańcucha certyfikatów akcji każdego elementu w łańcuchu jest kryptograficznie zarejestrowane i możliwe do zweryfikowania.

Ten proces zależy od pewne założenia, które muszą być udostępniane aby informacje były kompletne. Wymaga niezależnego tworzenia urządzenia unikatowy publiczny/prywatny pary kluczy i ochronę klucza prywatnego urządzenia. Na szczęście istnieje mikroukładami dolina bezpieczne w formularzu z sprzętu bezpiecznych modułów (HSM) może wewnętrznie Generowanie kluczy i ochrony kluczy prywatnych. Firma X wystarczy dodanie jednego z takich mikroukładami do inteligentnej-X-widżetu składnika BOM.

## <a name="device-connection"></a>Połączenie z urządzeniem

Przedstawione w poprzednich sekcjach powyżej ma zostały budowanie na połączenie z urządzeniem. Po prostu rejestrując certyfikat X.509 urzędu certyfikacji do usługi IoT Hub raz, jak potencjalnie milionów urządzeń łączenie i Uzyskaj uwierzytelnili po raz pierwszy?  Proste; za pomocą tego samego przekazywanie certyfikatu i przepływ dowodu posiadania wcześniej napotkaliśmy rejestrację certyfikatu X.509 urzędu certyfikacji.

Urządzenia wyprodukowane dla uwierzytelniania X.509 urzędu certyfikacji są wyposażone w urządzeniu unikatowych certyfikatów i łańcuchów certyfikatów, z ich odpowiednimi produkcyjny łańcucha dostaw. Połączenie z urządzeniem, nawet w przypadku pierwszego uruchomienia odbywa się w dwóch etapach: certyfikat łańcucha przekazywanie i dowodu posiadania.

Podczas przekazywania łańcucha certyfikatów urządzenie przekazuje jej urządzenia unikatowy certyfikat wraz z łańcucha certyfikatów, zainstalowano w niej do usługi IoT Hub. Za pomocą wstępnie zarejestrowanych certyfikatu X.509 urzędu certyfikacji, usługa IoT Hub kryptograficznie może sprawdzić kilka rzeczy, że łańcuch przekazany certyfikat jest wewnętrznie spójnej i łańcucha został zainicjowany przez właściciela prawidłowy certyfikat X.509 urzędu certyfikacji. Po prostu odbyło się przy użyciu procesu rejestracji X.509 urzędu certyfikacji, usługi IoT Hub może zainicjować proces dowodu posiadania żądanie odpowiedź, aby ustalić, że łańcucha i dlatego certyfikatu urządzenia faktycznie należy do urządzenia, na przekazanie go. Robi to przez Generowanie losowe żądania były podpisane przez urządzenia przy użyciu jego klucz prywatny do weryfikacji przez usługę IoT Hub. Odpowiedź oznaczająca Powodzenie wyzwala usługi IoT Hub, aby zaakceptować urządzenia jako autentyczny i przyznać jej połączenia.

W tym przykładzie każdy inteligentnych-X-element Widget będzie przekazywanie jej urządzenia unikatowy certyfikat wraz z fabryki Y i Z technik X.509 urzędu certyfikacji certyfikatów i odpowiedzenie na żądania dowodu posiadania w usłudze IoT Hub.

![Przepływ z jednego certyfikatu do innego, pop żądanie z Centrum](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Należy zauważyć, że podstawę zaufania opiera się na celu zapewnienie ochrony kluczy prywatnych, w tym kluczy prywatnych urządzeń. W związku z tym nie szczególną wystarczająco znaczenie bezpiecznego dolina chips w formularzu elementu sprzętu bezpiecznych modułów (HSM) do ochrony kluczy prywatnych urządzeń i ogólne najlepszym rozwiązaniem jest nigdy nie udostępniania kluczy prywatnych, co fabryki powierzenia innego za pomocą, takie jak jego klucz prywatny.