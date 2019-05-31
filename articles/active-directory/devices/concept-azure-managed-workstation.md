---
title: Dlaczego są ważne — bezpieczne stacje robocze usługi Azure Active Directory
description: Dowiedz się, dlaczego organizacje powinny tworzyć bezpieczne stacje robocze, które są zarządzane w usłudze Azure
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
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357048"
---
# <a name="building-secure-workstations"></a>Tworzenie bezpiecznej stacji roboczych

Zabezpieczone izolowane stacje robocze są niezwykle ważne dla bezpieczeństwa poufnych ról, takich jak Administratorzy, deweloperów i operatorów krytycznych usług. Wiele innych opcji zabezpieczeń i zapewnienia będą się nie powieść lub nie obowiązują w przypadku naruszenia zabezpieczeń podstawowych zabezpieczeń stacji roboczej klienta.

W tym dokumencie opisano, co jest potrzebne do tworzenia bezpiecznego stacji roboczej użytkownika, wraz ze szczegółowymi instrukcjami krok po kroku, w tym sposób konfigurowania od kontroli zabezpieczeń. Ten typ z stacje robocze w czasie nosi nazwę stacji roboczej dostępu uprzywilejowanego (PAW), to odwołanie jest używany, która utworzonych na podstawie. Jednak wskazówek, odwołując się do technologii opartych na chmurze do zarządzania usługą i wprowadza możliwości zabezpieczeń wprowadzonych od Windows 10RS5, Microsoft Defender ATP, usługi Azure Active Directory i Intune.

## <a name="why-securing-workstation-access-is-important"></a>Zabezpieczanie dostępu do stacji roboczej jest ważna

Szybkie wdrażanie usługi w chmurze oraz możliwość pracy z dowolnego miejsca utworzył nową metodę do wykorzystania. Osoby atakujące wykorzystywały kontroli słabe zabezpieczeń na urządzeniach, w której administratorzy działać i będą mogli uzyskać dostęp do zasobów uprzywilejowanych.

Zgodnie z opisem w [raport zagrożeń Verizon](https://enterprise.verizon.com/resources/reports/dbir/), i [Security Intelligence Report](https://aka.ms/sir) niewłaściwym użyciem uprzywilejowanego i ataki łańcucha dostaw należą mechanizmy pięć, używane do naruszenia zabezpieczeń organizacji, a Po drugie najczęściej wykryto taktyką zdarzenia zgłaszane w 2018 roku.

Większość osoby atakujące postępuj zgodnie z poniższym katalogu:

* Rozpoczynać Rekonesans, często specyficzne dla branży, aby znaleźć sposób w
* Analizowanie zebranych informacji do identyfikowania najlepsze oznacza, że uzyskanie dostępu (przeniknięciu) stacji roboczej postrzegany niskiej wartości
* Trwałość i spójrz na oznacza, że można przenieść [bok](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Wyprowadzać poufnych i wrażliwych danych.

Osoby atakujące często miejscach urządzeń, które wydają się niskie ryzyko lub niedoceniane rekonesansu. Te urządzenia na ataki są następnie używane do zlokalizowania szansy sprzedaży dla ruchu poprzecznego, Znajdź administracyjne użytkownicy i urządzenia i identyfikacji wysokiej cennych danych o pomyślnie wyprowadzać po uzyskają tych ról użytkownika uprzywilejowanego.

![Wzorzec typowego naruszenia zabezpieczeń.](./media/concept-azure-managed-workstation/typical-timeline.png)

Ten dokument stanowi rozwiązanie do ochrony urządzeń obliczeniowych przez izolowanie management i usług, aby ułatwić ochronę przed penetracji sieci lub ataków od mniej wartościowych produktywności urządzeń. Projekt pomaga ograniczyć możliwość pomyślnie wykonać w przypadku naruszenia zabezpieczeń dzięki pozbyciu się w łańcuchu przed przeniknięciu urządzenia służące do zarządzania i dostęp do zasobów w chmurze poufnych. Rozwiązanie przedstawione zostanie korzystanie z natywnymi usługami platformy Azure, które są częścią rozwiązania Microsoft 365 Enterprise tym stosu:

* Usługi Intune do zarządzania urządzeniami, w tym aplikacji i adres URL umieszczania na białej liście
* Rozwiązania autopilot dla konfiguracji urządzenia, wdrażania i odświeżanie 
* Usługi Azure AD na potrzeby zarządzania użytkownikami, dostępu warunkowego i uwierzytelniania wieloskładnikowego
* System Windows 10 (bieżąca wersja) dla urządzeń kondycji zaświadczania i środowisko użytkownika
* Microsoft Defender Advanced Threat Protection (ATP) dla programu endpoint protection, wykrywania i reagowania, za pomocą funkcji zarządzania w chmurze
* Usługa Azure AD PIM do autoryzacji, w tym właśnie w czas (JIT) do zarządzania uprzywilejowany dostęp do zasobów

## <a name="who-benefit-from-using-a-secure-workstation"></a>Kto korzystać z usługi bezpiecznego stacji roboczej

Wszyscy użytkownicy i Operatorzy korzystać z bezpiecznych stacji roboczej. Osoba atakująca, która naruszeń komputer lub urządzenie można zrobić kilka rzeczy, w tym personifikacji konta w pamięci podręcznej i Użyj poświadczeń i tokeny używane na tym urządzeniu, gdy są zalogowani. To zagrożenie sprawia, że zabezpieczanie urządzenia używane dla dowolnej roli uprzywilejowanej, łącznie z prawami administracyjnymi tak ważne, jak urządzenia użycia uprzywilejowanego konta są cele ruchu poprzecznego i ataki polegające na eskalacji uprawnień. Te konta może służyć do różnych zasobów, takich jak:

* Administratorzy w środowisku lokalnym i systemów opartych na chmurze
* Stanowisko pracy dewelopera dla systemów krytycznych
* Administrator kont mediów społecznościowych z wysokiej zagrożeń
* Wysoce poufne stacjach roboczych, takich jak terminale płatności SWIFT
* Obsługa tajemnice handlowe stacji roboczych

Firma Microsoft zaleca implementowania kontroli zabezpieczeń z podwyższonym poziomem uprawnień dla uprzywilejowanych stacji roboczych użycia tych kont pozwala zredukować ryzyko. Dodatkowe wytyczne można znaleźć w [przewodnik wdrażania funkcji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [plan usługi Office 365](https://aka.ms/o365secroadmap), i [plan zabezpieczania uprzywilejowanego dostępu](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>Dlaczego dedykowane stacje robocze

Choć jest możliwe zwiększyć bezpieczeństwo do istniejącego urządzenia, zaleca się rozpoczynać od bezpiecznej podstawy. Począwszy od znanych urządzeń dobre i zestaw umieszcza kontrolek znanych zabezpieczeń organizacji najlepsze możliwości do obsługi, który zwiększyć poziom zabezpieczeń. Za pomocą kiedykolwiek coraz większej liczbie ataków dozwolone przez zwykłych poczty e-mail i przeglądania sieci web trudno jest coraz bardziej upewnij się, że urządzenie może być zaufane. Ta działa Przewodnik przy założeniu dedykowana stacja robocza oddzielona od standardowego wydajności, przeglądanie, i wykonywane są zadania poczty e-mail. Usunięcie produktywność, przeglądanie sieci web i wiadomości e-mail z urządzenia może mieć negatywny wpływ na wydajność, ale to zabezpieczenie jest zazwyczaj dopuszczalny dla scenariuszy, w którym zadania jawnie nie wymagają i ryzyko w przypadku wystąpienia zdarzenia zabezpieczeń jest wysoka.

> [!NOTE]
> Przeglądanie sieci Web, w tym miejscu odwołuje się do ogólnego dostępu do dowolnego witryn sieci Web, czyli wysokiego ryzyka, znacznie się różnią od otwieranie niewielką liczbę dobrze znanych administracyjnych witryn sieci Web dla usług takich jak Azure, Office 365, innych dostawców rozwiązań w chmurze i SaaS za pomocą przeglądarki sieci web aplikacje.

Strategie zawierania zapewniają zwiększone zabezpieczenia, zabezpieczenia, zwiększając liczbę i rodzaj kontrolki, które osoba atakująca ma rozwiązywania w celu uzyskania dostępu do poufnych zasobów. Model opracowany tutaj zapewnia zawierania uprawnień administracyjnych do konkretnych urządzeń za pomocą modelu warstwowego uprawnień.

## <a name="supply-chain-management"></a>Zarządzanie łańcuchem zaopatrzenia

Niezbędne do bezpiecznej stacji roboczej jest rozwiązaniem łańcucha dostaw, gdzie jest zaufany, stacji roboczej, należy użyć "elementu głównego zaufania". To rozwiązanie pozwala sprostać głównym zaufania za pomocą [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technologii. W przypadku zabezpieczonej stacji roboczej Autopilot firmy Microsoft zapewnia możliwość wykorzystać urządzenia Microsoft OEM są zoptymalizowane pod kątem systemu Windows 10, która zapewnia znanego, dobrego stanu od producenta. Zamiast odtwarzanie z obrazu do urządzenia, które nie jest zaufany, Microsoft Autopilot można przekształcać urządzeń Windows w stanie "gotowe", zastosowanie ustawienia i zasady, instalowanie aplikacji, i nawet zmianę wersji systemu Windows 10 używane (np. z Windows 10 Pro do Windows 10 Enterprise, aby obsługiwać zaawansowane funkcje).

![Zabezpieczanie poziomy stacji roboczej](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role urządzenia i profile

W ciągu uzyskać wskazówki wiele profilów zabezpieczeń i role problem zostanie rozwiązany, aby osiągnąć bardziej bezpieczne rozwiązanie dla użytkowników, deweloperom i zespołom IT. Te profile mają został wyrównane do obsługi wspólnych użytkowników w organizacji, które mogą korzystać z rozszerzonych lub bezpieczny stacji roboczej, jednocześnie zapewniając równoważenie dzięki użyteczność i ryzyka. Wskazówki zapewnią konfiguracji ustawień w oparciu o standardy branżowe zaakceptowane. Niniejsze wskazówki jest używana do zilustrowania metoda ograniczania funkcjonalności systemu Windows 10 i zmniejszając ryzyko związane z naruszeniem urządzenia lub użytkownika, za pomocą zasad i technologii, aby ułatwić zarządzanie funkcjami zabezpieczeń i zagrożeń.
![Zabezpieczanie poziomy stacji roboczej](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Niska bezpieczeństwo** — zarządzanych standardowa stacji roboczej, która udostępnia dobry punkt wyjścia do większości użycia domu i w małych firmach. Te urządzenia są zarejestrowane z usługi Azure AD i zarządzane przez usługę Intune. Profil, który pozwala użytkownikom na uruchamianie wszystkich aplikacji, a następnie przejdź do dowolnej witryny sieci Web. To rozwiązanie chroniące przed złośliwym kodem, takie jak [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) powinno być włączone.
* **Zwiększone zabezpieczenia** — dobra jest rozwiązanie wpis poziomu chronionego dla użytkowników domowych, małe firmy, a także ogólne deweloperów.
   * Stacji roboczej rozszerzony zawiera zasady na podstawie oznacza, że zwiększyć bezpieczeństwo profil zabezpieczeń niski. Ten profil umożliwia bezpieczny sposób pracy z danymi klienta i mieć możliwość użycia narzędzi zwiększających produktywność, takich jak sprawdzanie poczty e-mail i przeglądania sieci web. Na stacji roboczej rozszerzony może służyć do inspekcji zachowania użytkowników i Użyj profilu nad stacją roboczą przez włączenie zasady inspekcji i logowania do usługi Intune. W tym profilu stacji roboczej spowoduje włączenie opcji zabezpieczeń i zasad opisane w zawartości i wdrożone w ulepszone stacji roboczej - Windows10 skryptu (1809). Wdrożenie wykorzystuje także ochronę za pomocą zaawansowanego złośliwego oprogramowania [zaawansowanej ochrony przed zagrożeniami (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **Wysoki poziom zabezpieczeń** — jest najbardziej skutecznym sposobem, aby zmniejszyć obszar narażony na stacji roboczej jest usunięcie możliwość samodzielne administrowanie stacji roboczej. Usuwanie lokalnych praw administracyjnych jest krokiem, który zwiększa bezpieczeństwo i może wpłynąć na wydajność, jeśli niepoprawnie zaimplementowana. Profil o wysokim poziomie zabezpieczeń jest oparta na profilu zwiększone zabezpieczenia przy użyciu jednej znaczne zmiany, usunięcie uprawnieniami lokalnego administratora. Ten profil jest przeznaczony do pomagają użytkownikom, które może być profil o wysokiej takich jak wykonawczego lub użytkowników, którzy mogą mieć skontaktuj się z danymi poufnymi, takie jak listy płac akceptacji lub usług i procesów.
   * Profil użytkownika o wysokim poziomie zabezpieczeń wymaga wyższej kontrolowanym środowisku, przy zachowaniu możliwości do wykonania działania ich wydajności, takich jak wiadomości e-mail i przeglądania przy zachowaniu prostego w użyciu środowisko sieci web. Użytkownicy oczekują, funkcje, takie jak pliki cookie, ulubionych i innych skróty, które są dostępne do działania. Jednak tacy użytkownicy mogą nie wymagać możliwość modyfikowania lub debugować swoje urządzenie, a nie trzeba instalować sterowniki. Profil o wysokim poziomie zabezpieczeń zostanie wdrożony, przy użyciu wysokiego poziomu zabezpieczeń — skrypt Windows10 (1809).
* **Wyspecjalizowane** — deweloperzy i Administratorzy IT są atrakcyjny cel dla osób atakujących, jak te role można zmienić systemów zainteresowania atakujących. Wyspecjalizowanych stacji roboczej zajmuje wdrożone w stacji roboczej o wysokim poziomie zabezpieczeń i dalsze emphases jego zabezpieczenia, zarządzanie aplikacje lokalne, ograniczenie witryny internetowej i ograniczając możliwości wydajności, które są wysokie ryzyko, np. ActiveX, nakład pracy Java, wtyczka przeglądarki i kilka innych kontrolek znanych wysokiego ryzyka na urządzeniu z systemem Windows. W tym profilu stacji roboczej spowoduje włączenie opcji zabezpieczeń i zasad opisane w zawartości i wdrożone w DeviceConfiguration_NCSC - Windows10 skryptu SecurityBaseline (1803).
* **Zabezpieczone** — osoba atakująca, która może naruszyć bezpieczeństwo konta administracyjnego zazwyczaj może spowodować szkody firma przez kradzieżą danych, zmiany danych lub przerw w działaniu usługi. W tym stanie ze wzmocnionymi zabezpieczeniami spowoduje włączenie stacji roboczej, wszystkie środki kontroli bezpieczeństwa i zasady, które ograniczają bezpośrednią kontrolę zarządzania lokalnych aplikacji i narzędzi zwiększających produktywność, są usuwane. W wyniku naruszania urządzenia składa się coraz trudniejszy jako wiadomości e-mail i mediów społecznościowych są blokowane, które odzwierciedlają najbardziej popularny sposób wyłudzanie mogła zakończyć się pomyślnie.  Zabezpieczanie stacji roboczej - SecurityBaseline Windows10 (1809) skryptu można wdrażać zabezpieczonej stacji roboczej.

   ![Zabezpieczone stacji roboczej](./media/concept-azure-managed-workstation/secure-workstation.png)

   Bezpieczne stacji roboczej, która zapewnia administratorowi stacji roboczej ze wzmocnionymi zabezpieczeniami, kontrola wyczyść aplikacji i funkcji application guard. Stacja robocza użyje poświadczeń, urządzeń i funkcji exploit guard do ochrony hosta przed złośliwego zachowania. Ponadto wszystkie lokalne dyski są szyfrowane przy użyciu szyfrowania funkcją Bitlocker.

* **Izolowane** — ten scenariusz w trybie offline niestandardowe reprezentuje extreme końcu spektrum (Brak skryptów instalacji znajdują się w tym przypadku). Organizacje mogą okazać się zarządzanie izolowane biznesowe krytyczne funkcji takich jak linia produkcyjna o wysokiej wartości lub życia pomocy technicznej systemów wymagającej nieobsługiwany/nie zastosowano poprawki starszych systemów operacyjnych. Ponieważ zabezpieczeń ma krytyczne znaczenie i usług w chmurze są niedostępne, organizacje mogą ręcznie Zarządzanie/aktualizowanie tych komputerów lub izolowane architektury lasu usługi Active Directory (takich jak zwiększonych zabezpieczeń administratora środowiska (ESAE)) umożliwia zarządzanie nimi. W tych okolicznościach usunięcie wszystkich dostęp poza podstawowe usługi Intune i zaawansowanej ochrony przed zagrożeniami sprawdzanie kondycji należy rozważyć.
   * [Wymagania łączności sieci usługi Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Wymagania łączności sieci zaawansowanej ochrony przed zagrożeniami](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie bezpiecznej usługi Azure managed stacji roboczej](howto-azure-managed-workstation.md)
