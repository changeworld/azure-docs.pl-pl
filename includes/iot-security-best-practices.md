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
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793127"
---
Zabezpieczanie infrastruktury Internetu rzeczy (IoT) wymaga rygorystycznej strategii bezpieczeństwa. Ta strategia wymaga zabezpieczania danych w chmurze, ochrony integralności danych podczas przesyłania przez publiczny Internet i bezpiecznego udostępniania urządzeń. Każda warstwa tworzy większą pewność bezpieczeństwa w ogólnej infrastruktury.

## <a name="secure-an-iot-infrastructure"></a>Zabezpieczanie infrastruktury IoT

Ta dogłębna strategia zabezpieczeń może być opracowywana i realizowana z aktywnym udziałem różnych podmiotów zaangażowanych w produkcję, rozwój i wdrażanie urządzeń i infrastruktury IoT. Poniżej znajduje się ogólny opis tych graczy.

* **Producent/integrator sprzętu IoT**: Zazwyczaj są to producenci wdrażanego sprzętu IoT, integratorzy montujący sprzęt różnych producentów lub dostawcy dostarczający sprzęt do wdrożenia IoT produkowanego lub zintegrowanego przez innych dostawców.

* **Deweloper rozwiązania IoT:** Rozwój rozwiązania IoT jest zazwyczaj wykonywane przez dewelopera rozwiązania. Ten deweloper może być częścią wazyńskiego zespołu lub integratora systemu (SI) specjalizującego się w tym działaniu. Deweloper rozwiązania IoT może od podstaw tworzyć różne składniki rozwiązania IoT, integrować różne komponenty gotowe lub typu open source lub przyjmować akceleratory rozwiązań z niewielkimi dostosowaniami.

* **Wdrażający rozwiązania IoT:** Po opracowaniu rozwiązania IoT należy go wdrożyć w terenie. Proces ten obejmuje wdrażanie sprzętu, wzajemne połączenia urządzeń i wdrażanie rozwiązań w urządzeniach sprzętowych lub w chmurze.

* **Operator rozwiązania IoT:** Po wdrożeniu rozwiązania IoT wymaga ono długoterminowych operacji, monitorowania, uaktualnień i konserwacji. Zadania te mogą być wykonywane przez wewnętrzny zespół, który składa się ze specjalistów w dziedzinie technologii informatycznych, zespołów operacyjnych i konserwacyjnych oraz specjalistów od domeny, którzy monitorują prawidłowe zachowanie ogólnej infrastruktury IoT.

Sekcje, które należy wykonać, zawierają najlepsze rozwiązania dla każdego z tych graczy, aby pomóc w opracowywaniu, wdrażaniu i obsłudze bezpiecznej infrastruktury IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Producent/integrator sprzętu IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące producentów sprzętu IoT i integratorów sprzętu.

* **Zakres sprzętu do minimalnych wymagań:** Projekt sprzętu powinien zawierać minimalne funkcje wymagane do działania sprzętu i nic więcej. Przykładem jest uwzględnienie portów USB tylko wtedy, gdy jest to konieczne do działania urządzenia. Te dodatkowe funkcje otwierają urządzenie dla niechcianych wektorów ataku, których należy unikać.

* **Spraw, aby sprzęt był odporny na manipulacje:** Zbuduj mechanizmy wykrywania fizycznych manipulacji, takie jak otwarcie pokrywy urządzenia lub usunięcie części urządzenia. Te sygnały sabotażu mogą być częścią strumienia danych przesłanego do chmury, co może ostrzegać operatorów o tych zdarzeniach.

* **Twórz wokół bezpiecznego sprzętu:** Jeśli pozwala na to COGS, twórz funkcje zabezpieczeń, takie jak bezpieczne i szyfrowane magazynowanie, lub funkcjonalność rozruchu opartą na module TPM (Trusted Platform Module). Te funkcje sprawiają, że urządzenia są bezpieczniejsze i pomagają chronić ogólną infrastrukturę IoT.

* **Zabezpiecz aktualizacje:** aktualizacje oprogramowania układowego w okresie użytkowania urządzenia są nieuniknione. Tworzenie urządzeń z bezpiecznymi ścieżkami uaktualnień i kryptograficznym zapewnieniem wersji oprogramowania układowego pozwoli na bezpieczeństwo urządzenia podczas aktualizacji i po jego zakończeniu.

## <a name="iot-solution-developer"></a>Deweloper rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów rozwiązań IoT:

* **Postępuj zgodnie z bezpieczną metodologią tworzenia oprogramowania**: Rozwój bezpiecznego oprogramowania wymaga odmiecznienia myślenia o bezpieczeństwie, od momentu powstania projektu aż do jego wdrożenia, testowania i wdrażania. Ta metodologia ma wpływ na wybór platform, języków i narzędzi. Cykl rozwoju zabezpieczeń firmy Microsoft zapewnia krok po kroku podejście do tworzenia bezpiecznego oprogramowania.

* **Ostrożnie wybieraj oprogramowanie typu open source:** Oprogramowanie open-source daje możliwość szybkiego opracowywania rozwiązań. Wybierając oprogramowanie typu open source, należy wziąć pod uwagę poziom aktywności społeczności dla każdego składnika open source. Aktywna społeczność zapewnia, że oprogramowanie jest obsługiwane i że problemy są wykryte i rozwiązane. Alternatywnie nie można otworzyć niejasnego i nieaktywnego projektu oprogramowania typu open source, a problemy prawdopodobnie nie zostaną wykryte.

* **Integracja z ostrożnością:** wiele luk w zabezpieczeniach oprogramowania istnieje na granicy bibliotek i interfejsów API. Funkcje, które mogą nie być wymagane dla bieżącego wdrożenia, mogą być nadal dostępne za pośrednictwem warstwy interfejsu API. Aby zapewnić ogólne bezpieczeństwo, należy sprawdzić wszystkie interfejsy składników zintegrowanych pod kątem wad zabezpieczeń.

## <a name="iot-solution-deployer"></a>Wdrażacz rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dla wdrażaczy rozwiązań IoT:

* **Bezpieczne wdrażanie sprzętu:** wdrożenia IoT mogą wymagać wdrożenia sprzętu w niezabezpieczonych lokalizacjach, takich jak przestrzenie publiczne lub nienadzorowane ustawienia regionalne. W takich sytuacjach upewnij się, że wdrożenie sprzętu jest odporne na manipulacje w maksymalnym stopniu. Jeśli porty USB lub inne porty są dostępne na sprzęcie, upewnij się, że są one bezpiecznie objęte. Wiele wektorów ataku może używać ich jako punktów wejścia.

* **Bezpieczeństwo kluczy uwierzytelniania:** podczas wdrażania każde urządzenie wymaga identyfikatorów urządzeń i skojarzonych kluczy uwierzytelniania generowanych przez usługę w chmurze. Zachowaj te klucze fizycznie bezpieczne, nawet po wdrożeniu. Każdy klucz, którego bezpieczeństwo zostało naruszone, może zostać użyty przez złośliwe urządzenie do maskaradania jako istniejącego urządzenia.

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dla operatorów rozwiązań IoT:

* Aktualizuj system : Upewnij **się,** że systemy operacyjne urządzeń i wszystkie sterowniki urządzeń są uaktualniane do najnowszych wersji. Jeśli włączysz automatyczne aktualizacje w systemie Windows 10 (IoT lub inne jednostki SKU), firma Microsoft będzie aktualizować ją, zapewniając bezpieczny system operacyjny dla urządzeń IoT. Aktualizowanie innych systemów operacyjnych (takich jak Linux) pomaga zapewnić ochronę przed złośliwymi atakami.

* **Ochrona przed złośliwą aktywnością:** Jeśli system operacyjny na to pozwala, zainstaluj najnowsze funkcje antywirusowe i chroniące przed złośliwym oprogramowaniem w każdym systemie operacyjnym urządzenia. Ta praktyka może pomóc w ograniczeniu większości zagrożeń zewnętrznych. Większość nowoczesnych systemów operacyjnych można chronić przed zagrożeniami, podejmując odpowiednie kroki.

* **Często przeprowadzaj inspekcję:** Inspekcja infrastruktury IoT w przypadku problemów związanych z zabezpieczeniami ma kluczowe znaczenie podczas reagowania na zdarzenia związane z zabezpieczeniami. Większość systemów operacyjnych zapewnia wbudowane rejestrowanie zdarzeń, które należy często przeglądać, aby upewnić się, że nie wystąpiło żadne naruszenie zabezpieczeń. Informacje inspekcji mogą być wysyłane jako oddzielny strumień telemetrii do usługi w chmurze, gdzie mogą być analizowane.

* **Fizycznie chronić infrastrukturę IoT:** Najgorsze ataki zabezpieczeń przeciwko infrastrukturze IoT są uruchamiane przy użyciu fizycznego dostępu do urządzeń. Jedną z ważnych praktyk bezpieczeństwa jest ochrona przed złośliwym użyciem portów USB i innego fizycznego dostępu. Jednym z kluczy do wykrywania naruszeń, które mogły wystąpić, jest rejestrowanie dostępu fizycznego, takiego jak użycie portu USB. Ponownie system Windows 10 (IoT i inne jednostki SKU) umożliwia szczegółowe rejestrowanie tych zdarzeń.

* **Ochrona poświadczeń w chmurze:** poświadczenia uwierzytelniania w chmurze używane do konfigurowania i obsługi wdrożenia IoT są prawdopodobnie najprostszym sposobem uzyskania dostępu i naruszenia zabezpieczeń systemu IoT. Chroń poświadczenia, często zmieniając hasło i powstrzymaj się od używania tych poświadczeń na komputerach publicznych.

Możliwości różnych urządzeń IoT różnią się. Niektóre urządzenia mogą być komputerami z typowymi systemami operacyjnymi dla komputerów stacjonarnych, a niektóre urządzenia mogą być z bardzo lekkimi systemami operacyjnymi. Opisane wcześniej najlepsze rozwiązania w zakresie zabezpieczeń mogą mieć zastosowanie do tych urządzeń w różnym stopniu. Jeśli zostanie to przewidziane, należy przestrzegać dodatkowych zabezpieczeń i najlepszych rozwiązań w zakresie wdrażania od producentów tych urządzeń.

Niektóre starsze i ograniczone urządzenia mogły nie zostać zaprojektowane specjalnie do wdrożenia IoT. Na tych urządzeniach może brakować możliwości szyfrowania danych, nawiązywania połączenia z Internetem lub zaawansowanej inspekcji. W takich przypadkach nowoczesna i bezpieczna brama polowa może agregować dane ze starszych urządzeń i zapewniać bezpieczeństwo wymagane do łączenia tych urządzeń przez Internet. Bramy pól mogą zapewniać bezpieczne uwierzytelnianie, negocjowanie zaszyfrowanych sesji, przyjmowanie poleceń z chmury i wiele innych funkcji zabezpieczeń.