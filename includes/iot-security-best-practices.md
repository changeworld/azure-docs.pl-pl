---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2c6f5cf2d89da0c2418ac58ca5d47a8aa05e732f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33814768"
---
# <a name="internet-of-things-security-best-practices"></a>Najlepsze rozwiązania Internetu rzeczy

Zabezpieczanie infrastruktury Internetu rzeczy (IoT) wymaga rygorystyczne strategii zabezpieczeń zabezpieczeń. Ta strategia wymaga do zabezpieczania danych w chmurze, ochronę integralności danych przesyłanych za pośrednictwem publicznej sieci internet i bezpiecznie udostępnić urządzeń. Każda warstwa kompilacje gwarancję zabezpieczeń w całej infrastruktury.

## <a name="secure-an-iot-infrastructure"></a>Zabezpieczanie infrastruktury IoT

Ta strategia zabezpieczeń zabezpieczeń można opracowany i wykonywane przy aktywnego udziału różnych odtwarzaczy niezbędnych do produkcji, opracowywania i wdrażania urządzenia IoT i infrastruktury. Poniżej znajduje się ogólny opis te odtwarzacze.

* **Producenta sprzętu IoT/integrator**: zazwyczaj te odtwarzacze są producentów sprzętu IoT wdrażany integratory zebrania sprzętu od różnych producentów lub dostawców, zapewniając sprzętu wdrożenia IoT wyprodukowanych lub zintegrować przez innych dostawców.
* **Deweloper rozwiązania IoT**: Projektowanie rozwiązania IoT jest zazwyczaj wykonywane przez dewelopera rozwiązania. Część może tego projektanta wewnętrznych zespołu lub integrator systemu (SI) zajmujących się tego działania. Developer rozwiązania IoT można opracowanie różne składniki rozwiązania IoT od początku, integracji różnych składników gotowe lub open source lub przyjąć akceleratorów rozwiązania z pomocniczą dostosowania.
* **Narzędzie wdrażania rozwiązania IoT**: rozwiązanie po IoT jest rozwinięte, musi on zostać wdrożony w polu. Ten proces obejmuje wdrożenia sprzętu, połączenia urządzeń i wdrażanie rozwiązań w urządzeniach sprzętowych lub chmury.
* **Operator rozwiązania IoT**: rozwiązania IoT — po wdrożeniu, wymaga długoterminowej obsługi, monitorowaniem, uaktualnienia i konserwacji. Te zadania można wewnętrznych zespół, który obejmuje informacje specjalistów technologii, operacje sprzętu i zespoły konserwacji i specjalistów domeny, które monitorują poprawne zachowanie całej infrastruktury IoT.

W kolejnych sekcjach udostępnienie najlepszych rozwiązań dla każdego z tych odtwarzacze ułatwiające opracowanie, wdrażania i obsługi bezpiecznej infrastruktury IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Producenta sprzętu IoT/integrator

Poniżej przedstawiono najlepsze rozwiązania dotyczące IoT producentów sprzętu i integratory sprzętu.

* **Zakres sprzętu do minimalnych wymagań**: projekt sprzętu powinna zawierać minimalne funkcje wymagane dla operacji sprzętu i nic więcej. Przykładem jest uwzględnienie portów USB tylko wtedy, gdy jest to niezbędne do działania urządzenia. Te dodatkowe funkcje Otwórz urządzenie pod kątem niechciane ataków, które należy unikać.
* **Należy sprzętu manipulację dowód**: kompilacji w mechanizmów fizycznej przed naruszeniem, takie jak otwieranie pokrycia urządzenia lub usunięcie części urządzenia. Te manipulację sygnały może być częścią strumienia danych przekazane do chmury, co może alertów operatory te zdarzenia.
* **Tworzenie wokół bezpiecznych składników sprzętowych**: Jeśli kst pozwala, tworzenia funkcji zabezpieczeń, takie jak magazyn bezpieczna i szyfrowana lub rozruchu funkcje oparte na modułu (TPM). Te funkcje, że urządzenia więcej zabezpieczenia i ochrona całej infrastruktury IoT.
* **Zabezpieczyć uaktualnień**: aktualizacje oprogramowania w okresie istnienia urządzenia są nieuniknione. Tworzenie urządzeń ze ścieżkami bezpiecznego uaktualnień i zapewnienia kryptograficznych wersji oprogramowania układowego umożliwi urządzenia do zabezpieczenia, podczas i po uaktualnieniu.

## <a name="iot-solution-developer"></a>Deweloper rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów rozwiązania IoT:

* **Postępuj zgodnie z metodologii rozwoju oprogramowania bezpiecznego**: Programowanie bezpiecznego oprogramowania wymaga planowania podstaw dotyczących zabezpieczeń, od momentu rozpoczęcia projektu aż do jej wdrożenia, testowania i wdrażania. Opcje platform, języki i narzędzi są wpływ z tej metody. Cykl życia tworzenia zabezpieczeń firmy Microsoft zapewnia podejście krok po kroku dotyczący tworzenia bezpiecznego oprogramowania.
* **Wybierz oprogramowania typu open source z rozwagą**: oprogramowania typu Open source zapewnia możliwość szybkiego opracowywania rozwiązań. Wybierając oprogramowania typu open source, należy wziąć pod uwagę poziom aktywności społeczności dla każdego składnika open source. Aktywną społeczność zapewnia obsługę oprogramowania i że problemów są odnajdywane i problemu. Alternatywnie projektu oprogramowania typu open source zasłoniętej i nieaktywnych może nie być obsługiwany i problemy nie są prawdopodobnie odnalezione.
* **Integracja z rozwagą**: istnieje wiele luk w zabezpieczeniach oprogramowania na granicy bibliotek i interfejsów API. Funkcje, które nie mogą być wymagane dla bieżącego wdrożenia może nadal być dostępny za pośrednictwem interfejsu API warstwy. Aby zapewnić ogólne bezpieczeństwo, upewnij się, że sprawdzanie wszystkich interfejsów składników integrowana dla luk w zabezpieczeniach.

## <a name="iot-solution-deployer"></a>Narzędzie wdrażania rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące procedur wdrażających rozwiązania IoT:

* **Wdrażanie sprzętu bezpiecznie**: IoT wdrożenia może wymagać sprzętu, który ma zostać wdrożony w lokalizacjach niezabezpieczone, taki jak publiczny spacji lub ustawień nienadzorowanych. W takich sytuacjach, sprawdź, czy wdrożenie sprzętu odporne w maksymalnym zakresie. Jeśli są dostępne na sprzęcie USB lub inne porty, upewnij się, że są one objęte bezpiecznie. Wiele metod ataków można użyć je jako punkty wejścia.
* **Bezpieczeństwa kluczy uwierzytelniania**: podczas wdrażania, każde urządzenie wymaga identyfikatory urządzeń i skojarzonych kluczy uwierzytelniania generowane przez usługę w chmurze. Zachowaj te klucze fizycznie bezpieczne nawet po wdrożeniu. Dowolny klawisz, którego bezpieczeństwo zostało naruszone można za pomocą złośliwych urządzenia udają istniejące urządzenie.

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące operatorów rozwiązania IoT:

* **System aktualizowanie**: Upewnij się, że uaktualniony do najnowszej wersji systemów operacyjnych urządzeń i wszystkie sterowniki urządzeń. Po włączeniu automatycznej aktualizacji w systemie Windows 10 (IoT lub inne wersje) firmy Microsoft utrzymuje je aktualne, podając bezpieczny system operacyjny urządzenia IoT. Przechowywanie innych systemów operacyjnych (na przykład Linux) pomaga aktualne upewnij się również są chronione przed złośliwymi atakami.
* **Ochrona przed złośliwych działań**: pozwala na system operacyjny, należy zainstalować najnowszych funkcji ochrony przed wirusami i ochrony przed złośliwym oprogramowaniem w każdym systemie operacyjnym urządzenia. Takie rozwiązanie może pomóc ograniczyć zagrożenia najbardziej zewnętrznego. Większość nowoczesnych systemów operacyjnych przed zagrożeniami można chronić, wykonując odpowiednie czynności.
* **Inspekcji, często**: inspekcja IoT infrastruktury dla problemów związanych z zabezpieczeniami jest kluczem podczas reagowania na przypadki naruszenia zabezpieczeń. Większość systemów operacyjnych Podaj rejestrowanie zdarzeń wbudowanych, który należy ją sprawdzić często, aby upewnić się, że nie nastąpiło żadne naruszenia zabezpieczeń. Informacje o inspekcji mogą być wysyłane jako strumień oddzielnych telemetrii do usługi w chmurze gdzie można analizować.
* **Fizycznie ochrona infrastruktury IoT**: najgorszy ataki zabezpieczeń infrastruktury IoT jest uruchomiony przy użyciu fizyczny dostęp do urządzeń. Jeden rozwiązaniem bezpieczeństwa jest do ochrony przed złośliwym korzystaniem z portów USB i innych fizycznego dostępu. Jednego klucza do uncovering naruszeń, które mogły wystąpić jest rejestrowanie fizycznego dostępu, takich jak użycie portu USB. Ponownie Windows 10 (IoT i inne wersje) umożliwia szczegółowe rejestrowanie tych zdarzeń.
* **Ochrona poświadczeń w chmurze**: chmury uwierzytelniania poświadczenia używane do konfigurowania i obsługi wdrażania IoT są prawdopodobnie najłatwiejszym sposobem na uzyskanie dostępu i złamanie IoT. Ochronę często zmiany hasła poświadczenia, a zaniechania przy użyciu tych poświadczeń na komputerach publicznych.

Możliwości różnych urządzeń IoT różnią się. Niektóre urządzenia mogą być komputerami z systemem typowe systemy operacyjne, a niektóre urządzenia mogą działać bardzo lekki systemów operacyjnych. Najlepsze rozwiązania z zakresu bezpieczeństwa opisane wcześniej można mające zastosowanie do tych urządzeń w różnym stopniu. Jeśli zostanie podana, należy stosować dodatkowe wdrożenia najlepsze praktyki dotyczące zabezpieczeń i producentów tych urządzeń.

Niektóre starsze i ograniczonego urządzenia mogą nie został zaprojektowany specjalnie z myślą IoT wdrożenia. Te urządzenia mogą nie możliwość szyfrowania danych, nawiązania połączenia z Internetem lub podaj zaawansowanych zasad inspekcji. W takich przypadkach brama pola nowoczesnych i bezpieczne może agregować dane ze starszych urządzeń i podaj zabezpieczeń wymagane do połączenia tych urządzeń w Internecie. Pole bramy może zapewnić bezpieczne uwierzytelnianie, negocjacji zaszyfrowanych sesji, otrzymania poleceń z chmury i wiele innych funkcji zabezpieczeń.