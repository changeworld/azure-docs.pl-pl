---
title: Informacje z stacje robocze bezpieczne, zarządzane usługi Azure — usłudze Azure Active Directory
description: Zapoznaj się z stacje robocze bezpieczne, zarządzane usługi Azure pozwolą Ci zrozumieć, dlaczego jest ważna.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491592"
---
# <a name="understand-secure-azure-managed-workstations"></a>Informacje z stacje robocze bezpieczne, zarządzane usługi Azure

Zabezpieczonej i odizolowanej stacje robocze są niezwykle ważne dla bezpieczeństwa poufnych ról, takich jak Administratorzy, deweloperów i operatorów krytycznych usług. Jeśli zabezpieczeń stacji roboczej klienta zostanie naruszony, wielu kontroli zabezpieczeń i zapewnienia może zakończyć się niepowodzeniem lub nieskuteczne.

W tym dokumencie wyjaśniono, co jest potrzebne do tworzenia bezpiecznego stacji roboczej, często nazywane stacji roboczej z dostępem uprzywilejowanym (PAW). Ten artykuł zawiera również szczegółowe instrukcje dotyczące konfigurowania kontroli zabezpieczeń początkowych. W tym poradniku opisano, jak oparte na chmurze technologii można zarządzać usługą. Opiera się na funkcje zabezpieczeń, które zostały wprowadzone w 10RS5 Windows, Microsoft Defender Advanced Threat Protection (ATP), usługi Azure Active Directory i Intune.

> [!NOTE]
> W tym artykule wyjaśniono pojęcie bezpiecznego stacji roboczych i ich znaczenie. Jeśli znasz już pojęcia i chcesz od razu przejść do wdrażania, odwiedź stronę [wdrażanie stacji roboczej Secure](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Dostęp do bezpiecznej stacji roboczej jest ważna

Szybkie wdrażanie usługi w chmurze oraz możliwość pracy z dowolnego miejsca została utworzona nowa metoda wykorzystania. Dzięki wykorzystaniu kontroli słabe zabezpieczeń na urządzeniach, w którym administratorzy pracują, osoby atakujące mogą uzyskać dostęp do zasobów uprzywilejowanych.

Niewłaściwym użyciem uprzywilejowanego i ataki łańcucha dostaw należą najważniejsze pięć metod, które osoby atakujące używają do naruszenia zabezpieczeń organizacji. Są one też druga najczęściej wykryto taktyka zdarzenia zgłaszane w 2018 roku, zgodnie z opisem w [raport zagrożeń Verizon](https://enterprise.verizon.com/resources/reports/dbir/)i [Security Intelligence Report](https://aka.ms/sir).

Większość osoby atakujące, wykonaj następujące kroki:

1. Rekonesans, aby znaleźć sposób, często specyficzne dla branży.
1. Analiza do zbierania informacji i identyfikowania najlepszym sposobem na stacji roboczej, która jest traktowana jako niska wartość miejscach.
1. Stan trwały, aby wyszukać oznacza, że można przenieść [bok](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Wykradanie danych poufnych i wrażliwych.

Podczas czynności rozpoznawczych osoby atakujące często miejscach urządzeń, które wydają się niskie ryzyko lub niedoceniane. Będą oni używać tych urządzeń narażony, do lokalizowania szansy sprzedaży dla ruchu poprzecznego i Znajdź urządzeń i użytkowników administracyjnych. Po uzyskają dostęp do ról uprzywilejowanych użytkowników, osoby atakujące zidentyfikować dane o wysokiej wartości i pomyślnie wyprowadzić dane.

![Wzorzec typowego naruszenia zabezpieczeń.](./media/concept-azure-managed-workstation/typical-timeline.png)

W tym dokumencie opisano rozwiązania, które pomaga chronić Twoje urządzeń komputerowych z takich ataków poprzecznego. Rozwiązanie izoluje management i usług pochodzących od mniej wartościowych urządzeń produktywność, przerwanie łańcucha, zanim może być infiltrated urządzenia, które ma dostęp do zasobów w chmurze poufnych. Rozwiązanie korzysta z natywnymi usługami platformy Azure, które są częścią rozwiązania Microsoft 365 Enterprise stosu:

* Usługi Intune do zarządzania urządzeniami i listy bezpiecznych aplikacji i adresów URL
* Rozwiązania autopilot dla konfiguracji urządzenia, wdrażania i odświeżanie
* Usługi Azure AD na potrzeby zarządzania użytkownikami, dostępu warunkowego i uwierzytelniania wieloskładnikowego
* System Windows 10 (bieżąca wersja) dla urządzeń kondycji zaświadczania i środowisko użytkownika
* Usługa Defender ATP zarządzane w chmurze programu endpoint protection, wykrywania i odpowiedzi
* Usługa Azure AD PIM związane z zarządzaniem autoryzacji i just-in-time (JIT) uprzywilejowany dostęp do zasobów

## <a name="who-benefits-from-a-secure-workstation"></a>Kto korzysta z bezpiecznego stacji roboczej?

Wszyscy użytkownicy i Operatorzy korzyści, używając bezpiecznej stacji roboczej. Osoba atakująca, która obniża komputerze lub urządzeniu mogą personifikować wszystkie buforowane konta. Po zalogowaniu się na urządzeniu, mogą również użyć poświadczeń i tokenów. To zagrożenie ułatwia ważne do bezpiecznych urządzeń, które są używane do ról uprzywilejowanych, łącznie z prawami administracyjnymi. Urządzenia z wykorzystaniem kont uprzywilejowanych są elementy docelowe penetracji sieci i ataki polegające na eskalacji uprawnień. Te konta może służyć do różnych zasobów, takich jak:

* Administrator środowiska lokalnego lub systemów opartych na chmurze
* Stacji roboczej dewelopera dla systemów krytycznych
* Administrator konta mediów społecznościowych z wysoką zagrożeń
* Wysoce poufne stacji roboczej, takich jak płatności SWIFT terminalu
* Stacja robocza obsługi tajemnice handlowe

Aby ograniczyć ryzyko, należy zaimplementować formantów z podwyższonym poziomem uprawnień zabezpieczeń dla uprzywilejowanych stacje robocze, które należy użyć tych kont. Aby uzyskać więcej informacji, zobacz [przewodnik wdrażania funkcji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [plan usługi Office 365](https://aka.ms/o365secroadmap), i [plan zabezpieczania uprzywilejowanego dostępu](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Dlaczego warto używać dedykowane stacje robocze?

Choć jest możliwe zwiększyć bezpieczeństwo do istniejącego urządzenia, zaleca się rozpoczynać od bezpiecznej podstawy. Aby przełączyć organizacji najlepsze możliwości, aby zachować wysoki poziom zabezpieczeń, rozpoczyna się od urządzenia, które znasz są bezpieczne i wdrożyć zestaw kontrolek znanych zabezpieczeń.

Coraz większej liczbie ataków za pośrednictwem poczty e-mail i przeglądania sieci web sprawia, że coraz bardziej twarde należy upewnić się, czy urządzenie jest zaufany. W tym przewodniku założono, że dedykowana stacja robocza jest odizolowana od standardowego produktywność, przeglądanie i wiadomości e-mail. Usunięcie produktywność, przeglądanie sieci web i wiadomości e-mail z urządzenia może mieć negatywny wpływ na wydajność. Jednak to zabezpieczenie jest zazwyczaj dopuszczalny dla scenariuszy, w którym zadania jawnie nie wymagają i ryzyko w przypadku wystąpienia zdarzenia zabezpieczeń jest wysoka.

> [!NOTE]
> Przeglądanie sieci Web, w tym miejscu odnosi się do ogólnego dostępu do dowolnego witryn sieci Web, która może być czynnością o wysokim ryzyku. Takie przeglądania znacznie różni się od otwieranie niewielką liczbę dobrze znanych administracyjnych witryn sieci Web dla usług takich jak Azure, Office 365, innych dostawców rozwiązań w chmurze i aplikacji SaaS za pomocą przeglądarki sieci web.

Strategie zawierania zwiększyć bezpieczeństwo, zwiększając liczbę i typ elementów sterujących, które sądowym osobie atakującej uzyskanie dostępu do poufnych zasobów. Model opisanych w tym artykule używa projektu warstwowego uprawnień i ogranicza uprawnienia administracyjne do określonych urządzeń.

## <a name="supply-chain-management"></a>Zarządzanie łańcuchem zaopatrzenia

Niezbędne do bezpiecznej stacji roboczej jest rozwiązaniem łańcucha dostaw, w którym używasz zaufanych stacji roboczej o nazwie "elementu głównego zaufania". W przypadku tego rozwiązania wykorzystuje elementu głównego zaufania [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technologii. Aby zabezpieczyć stacji roboczej, rozwiązania Autopilot umożliwia korzystanie z urządzeń Microsoft OEM są zoptymalizowane pod kątem systemu Windows 10. Urządzenia te są dostępne w znanego, dobrego stanu od producenta. Zamiast odtwarzanie z obrazu urządzenia potencjalnie niebezpieczne, rozwiązania Autopilot można przekształcać urządzeń Windows w stanie "gotowe". Jego mają zastosowanie ustawienia i zasady, instaluje aplikacje i nawet zmiany wersji systemu Windows 10. Na przykład rozwiązania Autopilot może zmienić instalacja Windows urządzenia z Windows 10 Pro do systemu Windows 10 Enterprise, aby go mogą korzystać z zaawansowanych funkcji.

![Zabezpieczanie poziomy stacji roboczej](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role urządzenia i profile

Niniejsze wskazówki odwołuje się do kilku profile zabezpieczeń i role, które mogą pomóc tworzyć bardziej bezpieczne rozwiązania dla użytkowników, deweloperzy i personel działu informatycznego. Te profile równoważyć użyteczność i zagrożenia związane z typowych użytkowników, którzy mogą korzystać z rozszerzonych lub bezpieczny stacji roboczej. Ustawienia konfiguracji przedstawione tutaj są oparte na standardy branżowe zaakceptowane. Wskazówki te przedstawiają sposób ograniczenia funkcjonalności systemu Windows 10 i zmniejszyć ryzyko związane z naruszeniem urządzenia lub użytkownika. Robi to za pomocą zasad i technologii ułatwiających zarządzanie funkcjami zabezpieczeń i zagrożeń.
![Zabezpieczanie poziomy stacji roboczej](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Niska bezpieczeństwo** — zarządzanych, standardowa stacji roboczej, która udostępnia dobry punkt wyjścia do większości użycia domu i w małych firmach. Te urządzenia są zarejestrowane w usłudze Azure AD i zarządzane przy użyciu usługi Intune. Ten profil zezwala użytkownikom na uruchamianie wszystkich aplikacji, a następnie przejdź do dowolnej witryny sieci Web. To rozwiązanie chroniące przed złośliwym kodem, takie jak [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) powinno być włączone.

* **Zwiększone zabezpieczenia** — to rozwiązanie klasy podstawowej, chronione to rozwiązanie dobre dla użytkowników domowych, małych firm użytkowników i deweloperów ogólne.

   Rozszerzone stacja robocza jest oparta na zasadach sposób na zwiększenie bezpieczeństwa profil o niskim poziomie zabezpieczeń. Zapewnia bezpieczny sposób pracy z danymi klienta podczas również korzystania z narzędzi zwiększających produktywność, takich jak wiadomości e-mail i przeglądania sieci web. Zasady inspekcji i usługi Intune służy do monitorowania rozszerzone stacji roboczej użycie zachowania i profilu użytkownika. Wdrażanie profilu rozszerzone stacji roboczej ze skryptem Windows10 (1809) i korzysta z ochrony za pomocą zaawansowanego złośliwego oprogramowania [zaawansowanej ochrony przed zagrożeniami (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Wysoki poziom zabezpieczeń** — jest najbardziej skutecznym sposobem, aby zmniejszyć obszar narażony na stacji roboczej jest usunięcie możliwość samodzielne administrowanie stacji roboczej. Usuwanie lokalnych praw administracyjnych jest krokiem, który zwiększa bezpieczeństwo, ale może wpłynąć na wydajność, jeśli niepoprawnie zaimplementowana. Profil o wysokim poziomie zabezpieczeń jest oparta na profilu zwiększone zabezpieczenia przy użyciu jednej znaczące zmiany: usunięcie uprawnieniami lokalnego administratora. Ten profil jest przeznaczony dla użytkowników profilu wysokiej: kierownicy, Lista płac i poufne dane użytkowników, osoby zatwierdzające dla usług i procesów.

   Użytkowników o wysokim poziomie zabezpieczeń wymaga bardziej kontrolowanym środowisku, przy zachowaniu możliwości w celu działania, takie jak wiadomości e-mail i przeglądania w ramach prostego w użyciu środowiska sieci web. Użytkownicy oczekują, funkcje, takie jak pliki cookie, ulubionych i inne skróty do pracy. Jednak ci użytkownicy nie mogą być potrzebne możliwość modyfikowania lub debugować swoje urządzenie. Nie należy instalować sterowniki. Profil o wysokim poziomie zabezpieczeń zostanie wdrożony, przy użyciu wysokiego poziomu zabezpieczeń — skrypt Windows10 (1809).

* **Wyspecjalizowane** — osoby atakujące docelowe deweloperów i administratorów IT, ponieważ zmieniają systemy zainteresowania atakujących. Wyspecjalizowanych stacji roboczej rozszerza zasady wysokiego poziomu zabezpieczeń stacji roboczej zarządzania aplikacje lokalne i ograniczając witryn sieci Web. Ogranicza możliwości wydajności o wysokim ryzyku, takich jak ActiveX, Java, wtyczki przeglądarki i innych kontrolek Windows. Możesz wdrożyć ten profil z DeviceConfiguration_NCSC — skrypt SecurityBaseline Windows10 (1803).

* **Zabezpieczone** — osoba atakująca, która obniża konta administracyjnego może zaszkodzić firma przez kradzieżą danych, zmiany danych lub przerw w działaniu usługi. W tym stanie ze wzmocnionymi zabezpieczeniami stacji roboczej włącza wszystkie środki kontroli bezpieczeństwa i zasady, które ograniczają bezpośrednią kontrolę zarządzania aplikacji lokalnej. Zabezpieczone stacji roboczej ma zatem nie narzędzi zwiększających produktywność trudniejsze do naruszenia bezpieczeństwa urządzenia. Blokuje najbardziej typowe wektor dla wyłudzanie: poczty e-mail i mediów społecznościowych.  Zabezpieczanie stacji roboczej - SecurityBaseline Windows10 (1809) skryptu można wdrażać zabezpieczonej stacji roboczej.

   ![Zabezpieczone stacji roboczej](./media/concept-azure-managed-workstation/secure-workstation.png)

   Bezpieczne stacji roboczej, która zapewnia administratorowi stacji roboczej ze wzmocnionymi zabezpieczeniami, kontrola wyczyść aplikacji i funkcji application guard. Stacja robocza używa funkcji credential guard, funkcja device guard i funkcji exploit guard do ochrony hosta przed złośliwego zachowania. Wszystkie dyski lokalne, również są szyfrowane za pomocą funkcji BitLocker.

* **Izolowane** — w tym scenariuszu niestandardowych, w trybie offline reprezentuje extreme końcu spektrum. Brak skryptów instalacji znajdują się w tym przypadku. Może być konieczne zarządzanie krytyczne dla prowadzonej działalności funkcji, która wymaga nieobsługiwana lub które starszej wersji systemu operacyjnego. Na przykład linii produkcyjnej o wysokiej wartości lub system pomocy technicznej — życia. Ponieważ zabezpieczeń ma krytyczne znaczenie i usług w chmurze są niedostępne, można zarządzać i zaktualizować te komputery ręcznie lub za pomocą izolowane architektury lasu usługi Active Directory takich jak zwiększonych zabezpieczeń administratora środowiska (ESAE). W takiej sytuacji należy rozważyć usunięcie wszystkich dostęp poza podstawowe kontrole kondycji usługi Intune i zaawansowanej ochrony przed zagrożeniami.

  * [Wymagania łączności sieci usługi Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Wymagania łączności sieci zaawansowanej ochrony przed zagrożeniami](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie bezpiecznej stacji roboczej usługi Azure managed](howto-azure-managed-workstation.md).
