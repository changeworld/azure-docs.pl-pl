---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 2138eed9975abe804442c476d19b5b7229685362
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146636"
---
# <a name="security-best-practices-for-internet-of-things-iot"></a>Najlepsze rozwiązania dotyczące Internetu rzeczy (IoT)

Zabezpieczanie infrastruktury Internetu rzeczy (IoT) wymaga rygorystyczne strategii zabezpieczeń w głębi. Ta strategia wymaga Zabezpieczanie danych w chmurze, ochrona integralności danych przesyłanych za pośrednictwem publicznej sieci internet i bezpiecznie aprowizuj urządzenia. Każda warstwa kompilacje większa kontrola zabezpieczeń w całej infrastruktury.

## <a name="secure-an-iot-infrastructure"></a>Zabezpieczanie infrastruktury IoT

Ta strategia zabezpieczeń zabezpieczeń można opracowane i wykonywane przy użyciu aktywnego udziału wiele odtwarzaczy, związane z produkcji, tworzenia i wdrażania infrastruktury i urządzeń IoT. Poniżej przedstawiono ogólny opis tych graczy.

* **IoT, producent sprzętu/integrator**: Zazwyczaj te odtwarzacze są producentów sprzętu IoT wdrażane integratorzy złożenia sprzętu od różnych producentów lub dostawców, zapewniając sprzętu do wdrożenia IoT wytwarzane lub zintegrowane za pośrednictwem innych dostawców.

* **Dla deweloperów rozwiązań IoT**: Tworzenie rozwiązania IoT jest zazwyczaj wykonywane przez dewelopera rozwiązania. Część może dewelopera wewnętrznego zespołu lub integrator systemów (SI) specjalizującym się w przypadku tego działania. Deweloper rozwiązania IoT można tworzenia różnych składników zestawu rozwiązań IoT od podstaw, zintegrować różne składniki gotowe lub typu open source lub przyjmuje akceleratorów rozwiązań za pomocą drobnych dostosowania.

* **Narzędzie wdrażania rozwiązania IoT**: Po opracowania rozwiązania IoT, należy wdrożyć w polu. Ten proces obejmuje wdrażanie sprzętu, wzajemne połączenie urządzeń i wdrażanie rozwiązań w urządzeń sprzętowych, czy w chmurze.

* **Operator rozwiązania IoT**: Po wdrożeniu rozwiązania IoT wymaga długoterminowej obsługi, monitorowaniem, zarządzaniem uaktualnienia i konserwacji. Te zadania może odbywać się przez wewnętrznego zespołu, który obejmuje operacje sprzętu i konserwacji zespołów, specjaliści ds. informacji o technologii i specjalistów domeny, które monitorują prawidłowe działanie całej infrastruktury IoT.

W kolejnych sekcjach najlepsze rozwiązania dla każdego z tych działania graczy, aby ułatwić opracowywanie, wdrażanie i obsługiwanie bezpiecznej infrastruktury IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT, producent sprzętu/integrator

Poniżej przedstawiono najlepsze rozwiązania dla producentów sprzętu IoT i integratorów sprzętu.

* **Zakres sprzętu minimalne wymagania**: Projekt sprzętu powinna zawierać minimalne funkcje wymagane przez operację sprzęt i nic więcej. Przykładem jest uwzględnienie portów USB, tylko wtedy, gdy jest to niezbędne do działania urządzenia. Te dodatkowe funkcje Otwórz urządzenie niechciane ataków, które należy unikać.

* **Uczynienia sprzętu naruszanie dowód**: Twórz w mechanizmów naruszeniem fizyczne, takie jak otwieranie pokrycia urządzenia lub usuwając część urządzenia. Te naruszanie sygnałów może być częścią strumienia danych przekazanych do chmury, co może ostrzegania operatorów tych zdarzeń.

* **Tworzenie wokół bezpieczny sprzęt**: Pozwala na KWS, tworzyć funkcje zabezpieczeń, takie jak magazyn bezpieczna i szyfrowana lub funkcji rozruchu oparte na Trusted Platform Module (TPM). Te funkcje sprawiają, urządzenia więcej zabezpieczenia i ochrona całej infrastruktury IoT.

* **Zwiększyć bezpieczeństwo uaktualnień**: Aktualizacje oprogramowania układowego w okresie istnienia urządzenia są nieuniknione. Tworzenie urządzenia ze ścieżkami bezpiecznego uaktualnień i kryptograficzne zapewnianie wersji oprogramowania układowego umożliwi urządzenie musi być bezpieczne, podczas i po uaktualnieniu.

## <a name="iot-solution-developer"></a>Dla deweloperów rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów rozwiązań IoT:

* **Postępuj zgodnie z metodologii rozwoju oprogramowania bezpiecznego**: Tworzenie bezpiecznego oprogramowania wymaga podstaw myśleć o zabezpieczeniach, od momentu rozpoczęcia projektu aż do jej implementacji, testowania i wdrażania. Wybór platform, języków i narzędzi mają wpływ przy użyciu tej metody. Cykl projektowania zabezpieczeń firmy Microsoft zawiera instrukcje krok po kroku podejście do tworzenia bezpiecznego oprogramowania.

* **Wybierz oprogramowanie typu open source z rozwagą**: Oprogramowanie typu open-source umożliwia szybkie opracowywanie rozwiązań. Wybrać oprogramowanie typu open source, należy wziąć pod uwagę poziom aktywności społeczności dla każdego składnika typu open source. Aktywna społeczność zapewnia, że oprogramowanie jest obsługiwane i czy problemy są odnajdywane i które zostały rozwiązane. Alternatywnie projektu oprogramowania typu open-source zasłoniętej i nieaktywnych może nie być obsługiwany i problemy są nie może zostać odnalezione.

* **Integracja z rozwagą**: Istnieje wiele luki w zabezpieczeniach oprogramowania na granicy bibliotek i interfejsów API. Funkcje, które nie mogą być wymagane dla bieżącego wdrożenia i nadal może być dostępna za pośrednictwem warstwę interfejsu API. Aby zapewnić ogólnego stanu zabezpieczeń, upewnij się sprawdzić wszystkie interfejsy składników integrowany dla luki w zabezpieczeniach.

## <a name="iot-solution-deployer"></a>Narzędzie wdrażania rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące procedur wdrażających rozwiązania IoT:

* **Bezpieczne wdrażanie sprzętu**: Wdrożenia usługi IoT, mogą wymagać sprzętu, który ma zostać wdrożony w niezabezpieczonych lokalizacjach, takich jak publicznego miejsca do magazynowania lub ustawień nienadzorowanych. W takich przypadkach upewnij się, że wdrożenia sprzętowego odporną na manipulacje w maksymalnym zakresie. Jeśli USB lub inne porty są dostępne na sprzęcie, upewnij się, że są one objęte bezpiecznie. Wiele rodzajów ataków można ich użyć jako punkty wejścia.

* **Zabezpieczanie klucze uwierzytelniania**: Podczas wdrażania każde urządzenie wymaga identyfikatory urządzeń i klucze uwierzytelniania skojarzone generowane przez usługę w chmurze. Zabezpieczenie tych kluczy fizycznie nawet po wdrożeniu. Dowolny klawisz, którego bezpieczeństwo zostało naruszone, może służyć przez złośliwych urządzenie poczynania jako istniejące urządzenie.

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dla operatorów rozwiązania IoT:

* **Aktualizowanie systemu**: Upewnij się, że uaktualniony do najnowszej wersji systemów operacyjnych urządzeń i wszystkie sterowniki urządzeń. Po włączeniu funkcji Aktualizacje automatyczne w systemie Windows 10 (IoT lub inne jednostki SKU) firma Microsoft zachowuje on aktualne, zapewniając bezpieczny system operacyjny dla urządzeń IoT. Utrzymywanie innych systemów operacyjnych (na przykład system Linux) aktualne pomaga zapewnić, również są chronione przed złośliwymi atakami.

* **Ochrona przed złośliwymi działaniami**: Pozwala na system operacyjny, należy zainstalować najnowsze możliwości przed wirusami i złośliwym na każdy system operacyjny urządzenia. To rozwiązanie może pomóc Eliminuj zagrożenia najbardziej zewnętrznego. Większość nowoczesnych systemów operacyjnych przed zagrożeniami można chronić, wykonując odpowiednie czynności.

* **Inspekcja często**: Inspekcja problemów związanych z zabezpieczeniami w infrastrukturze IoT jest klucz, w przypadku reagowania na zdarzenia związane z bezpieczeństwem. Większość systemów operacyjnych udostępnia rejestrowania zdarzeń wbudowanych, która powinna zostać przejrzana pod często do upewnij się, że wystąpił nie naruszenia zabezpieczeń. Informacje o inspekcji mogą być wysyłane jako strumień osobne dane telemetryczne do usługi w chmurze gdzie mogą być analizowane.

* **Fizycznie ochrona infrastruktury IoT**: Najgorszy ataków bezpieczeństwa infrastruktury IoT uruchamianych przy użyciu fizyczny dostęp do urządzeń. Jednym rozwiązaniem jest ważne dla bezpieczeństwa jest ochrona przed złośliwym korzystaniem z portów USB i innych fizyczny dostęp. Jednego klucza do po odkrywanie naruszeń, które mogły wystąpić jest rejestrowania fizycznego dostępu, takich jak użycie portu USB. Ponownie systemu Windows 10 (IoT i inne jednostki SKU) umożliwia szczegółowe rejestrowanie tych zdarzeń.

* **Ochrona poświadczeń w chmurze**: Poświadczenia uwierzytelniania chmury, używany do konfigurowania i obsługi wdrożenia usługi IoT są prawdopodobnie Najprostszym sposobem na uzyskanie dostępu i naruszenia bezpieczeństwa systemu IoT. Chronią poświadczenia często zmieniając hasło, a punktowanych przy użyciu tych poświadczeń na komputerach publicznych.

Funkcje z różnych urządzeń IoT różnią się od. Niektóre urządzenia mogą być komputerami z systemem typowe systemy operacyjne, a niektóre urządzenia mogą działać bardzo lekkie systemów operacyjnych. Najlepsze rozwiązania opisane wcześniej może się do tych urządzeń w różnym stopniu. Jeśli nie dostarczono, należy stosować dodatkowe zabezpieczenia i wdrażanie na podstawie najlepszych rozwiązań producentów tych urządzeń.

Niektóre starsze i ograniczone urządzenia może nie zostały opracowane specjalnie dla wdrożenia IoT. Te urządzenia mogą wykazywać Brak możliwość szyfrowania danych, połącz się z Internetem lub podaj zaawansowanych zasad inspekcji. W takich przypadkach bramy w terenie nowoczesnych i bezpieczne można agregować dane z starsze urządzenia i zapewnienia zabezpieczeń wymaganych do łączenia się te urządzenia za pośrednictwem Internetu. Bram działających w terenie może zapewnić bezpieczne uwierzytelnianie, negocjowanie zaszyfrowanych sesji, potwierdzenia poleceń z chmury i wiele innych funkcji zabezpieczeń.