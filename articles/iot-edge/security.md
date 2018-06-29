---
title: Zabezpieczenia w programie Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zabezpieczeń, uwierzytelniania i autoryzacji urządzenia brzegowe IoT
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f198efe9ff5e4862a3bbe872ab50e5848c9dbb5c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030584"
---
# <a name="securing-azure-iot-edge"></a>Zabezpieczanie krawędzi IoT Azure

Zabezpieczanie inteligentnego krawędzi jest przyznanie zaufania w ramach operacji kompleksowe rozwiązania IoT. Usługa Azure IoT krawędzi jest przeznaczona dla zabezpieczeń jest rozszerzony do różnych ryzyka profilów, scenariusze wdrażania i oferuje takiej samej ochronie, która oczekuje od wszystkich usług platformy Azure.

Krawędź IoT Azure działa na inny komputer obsługuje zarówno systemu Linux i Windows i ma zastosowanie do różnych wdrożeń.  Oceny ryzyka zależy od wielu zagadnienia, w tym własność rozwiązania, geography wdrożenia, wrażliwości danych, ochrony prywatności, pionowe i przepisami wymagań aplikacji.  Zamiast oferty konkretnego rozwiązania konkretnych scenariuszy, warto projektowania framework extensible zabezpieczeń opartych na zasadach dobrze uziemionych przeznaczone do skalowania. 
 
Ten artykuł zawiera omówienie przez strukturę zabezpieczeń. Aby uzyskać więcej informacji, zobacz [zabezpieczanie inteligentnego krawędzi][lnk-edge-blog].

## <a name="standards"></a>Standardy

Standardy promować łatwość kontroli i łatwość wdrażania, które są cecha charakterystyczna zabezpieczeń.  Rozwiązanie dobrze zaprojektowanej architektury zabezpieczeń powinny nadają się do kontroli w wersji ewaluacyjnej do tworzenia relacji zaufania i nie powinny być próg wykluczający do wdrożenia.  Projekt platformy do zabezpieczania Azure IoT krawędzi stanowi sprawdzona i protokoły zabezpieczeń branży sprawdzone wykorzystać znajomości i ponowne użycie. 

## <a name="authentication"></a>Authentication

Znajomość bez wątpliwości, jakie złośliwych użytkowników, urządzenia i składniki uczestniczą w dostarczania wartości za pośrednictwem kompleksowe rozwiązania IoT jest podstawowym tworzenia relacji zaufania.  Takie wiedzy oferuje bezpiecznego accountability uczestników z włączaniem podstawę do wprowadzenia.  Usługa Azure IoT krawędzi osiągnie wiedzy za pomocą uwierzytelniania.  Podstawowego mechanizmu uwierzytelniania dla platformy Azure IoT krawędzi jest uwierzytelnianie oparte na certyfikatach.  Ten mechanizm pochodzi z zestawem standardów dotyczących infrastruktury kluczy publicznych (PKiX) przez Internet Engineering Task Force (IETF).     

Przez strukturę zabezpieczeń Azure IoT krawędzi wymaga unikatowego certyfikatu tożsamości dla wszystkich urządzeń, modułów (kontenerów, które hermetyzują logiki urządzenia) i uczestników interakcji z urządzenia Azure IoT krawędzi, można go fizycznie lub za pośrednictwem połączenia sieciowego.  Nie każdy scenariusz lub składnik może spowodować w przyszłości zmianę uwierzytelniania opartego na certyfikatach, dla którego extensibility Framework zabezpieczeń zapewnia bezpieczny możliwe do pomieszczenia. 

## <a name="authorization"></a>Autoryzacja

Aby delegować uprawnienia i kontroli dostępu jest kluczową rolę w osiąganiu zasada zabezpieczeń podstawowych — zasadą najniższych uprawnień.  Urządzenia, moduły i uczestników mogą uzyskać dostęp tylko do zasobów i dane w ich zakresie uprawnień i tylko wtedy, gdy jest dopuszczalna pod względem architektury.  Oznacza to, że można skonfigurować przy użyciu wystarczające uprawnienia, a inne pod względem architektury wymuszane są niektóre uprawnienia.  Na przykład gdy moduł może uprawnień za pomocą uprzywilejowanych konfiguracji do nawiązania połączenia z Centrum IoT Azure, istnieje ma powodu, dlaczego moduł jednego urządzenia Azure IoT krawędzi powinien uzyskiwać dostęp dwie modułu w innym Azure IoT urządzenia.  Z tego powodu drugie może być pod względem architektury wykluczone. 

Inne schematy autoryzacji to prawa podpisywania certyfikatów i kontroli dostępu opartej na rolach (RBAC).  Możliwość rozszerzania przez strukturę zabezpieczeń zezwala na przyjęcie innych systemów dojrzałe autoryzacji. 

## <a name="attestation"></a>Zaświadczanie

Poświadczenie zapewnia integralność bitów oprogramowania.  Jest ważne w przypadku wykrywania i zapobiegania złośliwego oprogramowania.  Przez strukturę zabezpieczeń Azure IoT krawędzi klasyfikuje zaświadczania w obszarze trzy główne kategorie:

* Poświadczenie statyczne
* Poświadczenie środowiska wykonawczego
* Poświadczenie oprogramowania

### <a name="static-attestation"></a>Poświadczenie statyczne

Statyczne zaświadczania jest Weryfikacja integralności wszystkie bity oprogramowania, w tym z systemami operacyjnymi wszystkich środowisk uruchomieniowych, i włączania zasilania informacje o konfiguracji na urządzeniu.  Jest ona często określana jako bezpieczny rozruch.  Przez strukturę zabezpieczeń dla urządzenia Azure IoT brzegowe rozszerza dostawcom krzemu i dołącza możliwości bezpiecznych składników sprzętowych ingrained zapewnienie statycznych zaświadczania procesów. Te procesy obejmują bezpiecznego rozruchu i oprogramowanie układowe bezpiecznego uaktualnienia procesów.  W bliskiej współpracy z dostawcami krzemu eliminuje warstwy zbędny oprogramowania układowego pozwalają zminimalizować liczbę powierzchni zagrożeń. 

### <a name="runtime-attestation"></a>Poświadczenie środowiska wykonawczego

Po systemu zakończył proces rozruchu zweryfikowane i jest uruchomiony i uruchomione, przemyślany systemów zabezpieczeń może wykryć próby wstrzyknięcia złośliwego oprogramowania za pomocą systemów porty i interfejsy i podjąć odpowiednie środki zaradcze.  Dla urządzeń inteligentnych krawędzi w fizycznych nadzoru nad złośliwych osób istnieje możliwość wprowadzenia malcontent za pośrednictwem środków innych niż urządzenia interfejsów, takich jak naruszeniu i ataków kanału po stronie.   Takie malcontent, która może być w formie złośliwe oprogramowanie lub zmiany konfiguracji nieautoryzowanego, zwykle nie można wykryć przez proces bezpieczny rozruch ponieważ one odbywa się po zakończeniu rozruchu.  Środki zaradcze oferowane lub wymuszane przez urządzeń sprzętowych znacznie przyczynia się do niepożądanymi tych zagrożeń.  Struktura zabezpieczeń Azure IoT Edge jawnie uwidacznia dla rozszerzeń dla combatting zagrożenia środowiska wykonawczego.     

### <a name="software-attestation"></a>Poświadczenie oprogramowania

Wszystkie systemy dobrej kondycji, w tym systemy inteligentnego krawędzi musi być nadającymi się do poprawki i aktualizacje.  Zabezpieczenia są ważne dla procesów aktualizacji w przeciwnym razie się, że ich potencjalne zagrożenie wektory.  Struktura zabezpieczeń dla wywołań Azure IoT krawędzi aktualizacje za pośrednictwem mierzony i podpisanych pakietów do zapewnienia integralności i uwierzytelniania źródła pakietów.  Ma to zastosowanie do wszystkich systemów operacyjnych i aplikacji oprogramowania z usługi bits. 

## <a name="hardware-root-of-trust"></a>Sprzęt głównym zaufania

We wdrożeniach urządzenia brzegowe inteligentnego, zwłaszcza tych, które wdrożone w miejscach, w którym potencjalne złośliwych osób mieć fizyczny dostęp do urządzenia, zabezpieczeń oferowanych przez urządzeń sprzętowych jest ostatnim narzędziem do ochrony.  Z tego powodu kotwiące zaufanie odporne na próby naruszenia sprzętu jest najbardziej niezwykle istotne w przypadku tych wdrożeń.  Przez strukturę zabezpieczeń Azure IoT Edge wymaga współpracy dostawców sprzętu bezpiecznego krzemu oferowanie różnymi odmianami sprzętu głównym zaufania, aby zmieścił się w różnych wdrożeń i profilów ryzyka. Obejmują one użycie bezpiecznego krzemu przestrzegać wspólne standardy protokołu zabezpieczeń, takie jak Trusted Platform Module (ISO/IEC 11889) i aparat kompozycji identyfikator Trusted Computing Group na urządzenia (GRUPOWANE).  Obejmują one bezpieczne enklawę technologii, takich jak TrustZones i oprogramowania rozszerzenia Guard (SGX). 

## <a name="certification"></a>Certyfikacja

Aby ułatwić klientom podejmowanie świadomych decyzji podczas nabywanie urządzenia Azure IoT brzegowe dla wdrożenia, framework Azure IoT krawędzi zawiera wymagania dotyczące certyfikacji.  Podstawowych, aby te wymagania są certyfikaty dotyczące bezpieczeństwa i certyfikaty odnoszące się do weryfikacji implementacji zabezpieczeń.  Na przykład certyfikacji oświadczeń zabezpieczeń będzie informuje, że urządzenie brzegowe IoT używa bezpiecznych składników sprzętowych znane na ataki rozruchu. Certyfikacji weryfikacji będzie informuje, że bezpiecznych składników sprzętowych prawidłowo został wdrożony do zaoferowania tej wartości w urządzeniu.  Zgodnie z zasadą prostotę platformę oferuje wizji pozostawienie minimalnego obciążenia certyfikacji.   

## <a name="extensibility"></a>Rozszerzalność

Rozszerzalność jest pierwszą klasą obywateli w ramach zabezpieczeń Azure IoT krawędzi.  Wraz z technologią IoT zwiększają różnego rodzaju przekształcenia biznesowych jego oznacza przyczyny, że zabezpieczeń powinny bezproblemowo rozwijać w lockstep adres nowych scenariuszy.  Przez strukturę zabezpieczeń Azure IoT krawędzi, rozpoczyna się od solidną podstawę, na którym kompilacje w rozszerzalności w różnych wymiarach, aby uwzględnić: 

* Pierwszy usług zabezpieczeń firmy, takich jak usługa inicjowania obsługi urządzeń dla Centrum IoT Azure.
* Usług innych firm, takich jak usługi zarządzanej zabezpieczeń dla przypadkach innej aplikacji (takich jak przemysłowych lub opieki zdrowotnej) lub technologii fokus (np. monitorowanie zabezpieczeń siatki sieci lub usługi zaświadczania sprzętu krzemu) za pośrednictwem sieci sformatowanego partnerów.
* Starsze systemy, aby uwzględnić modernizacji o strategii zabezpieczeń alternatywnych, takich jak uwierzytelnianie i tożsamość zarządzania przy użyciu technologii zabezpieczeń innych niż certyfikaty.
* Zabezpiecz sprzętu w celu łatwego przyjęcie nowych technologii bezpiecznych składników sprzętowych i krzemu wkładów partnera.

Przykłady kilka wymiarów dla rozszerzeń i strukturę zabezpieczeń Azure IoT krawędzi jest przeznaczony do zabezpieczenia od podstaw do obsługi rozszerzania. 

W końcu najwyższy Powodzenie zabezpieczania inteligentnego krawędzi wyniki z udziału współpracy społeczności Otwórz regulowane przez wspólne zainteresowanie zabezpieczanie IoT.  Te udziały mogą być w formie bezpiecznego technologii i usług.  Przez strukturę zabezpieczeń krawędzi IoT Azure zapewnia solidną podstawę dla zabezpieczeń, który jest rozszerzalny maksymalnego pokrycia do zaoferowania poziomu zaufania i integralności w inteligentnego krawędź jako z chmury Azure.  

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat sposobu Azure IoT krawędzi jest [zabezpieczanie inteligentnego krawędzi][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 