---
title: Struktury zabezpieczeń — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zabezpieczeń, uwierzytelniania i standardami autoryzacji, które były używane do tworzenia usługi Azure IoT Edge i powinny być uznane za projektowanie rozwiązania
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1042f53147122a7370b464f6bfc892dcee70fe70
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100144"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpieczeń usługi Azure IoT Edge

Zabezpieczanie inteligentnych rozwiązań brzegowych jest przyznanie zaufania w operacji kompleksowe rozwiązania IoT. Usługa Azure IoT Edge jest przeznaczona dla zabezpieczeń, które jest rozszerzyć, aby profile ryzyka inną, wdrożeń i oferuje taką samą ochronę, który można oczekiwać od wszystkich usług platformy Azure.

Usługa Azure IoT Edge działa na różnym sprzęcie, obsługuje zarówno systemu Linux i Windows i ma zastosowanie do różnorodnych wdrożeń.  Ryzyko oceniane zależy od wielu zagadnienia, w tym własności rozwiązania obszaru geograficznego wdrożenia, wrażliwości danych, ochrony prywatności, aplikacji pionowy, wymaganiami prawnymi w zakresie.  Zamiast oferty konkretnego rozwiązania do konkretnych scenariuszy, dobrym pomysłem będzie projektowania platforma extensible zabezpieczenia oparty na zasadach dobrze jest ona przeznaczona dla skalowania. 
 
Ten artykuł zawiera omówienie struktury zabezpieczeń. Aby uzyskać więcej informacji, zobacz [zabezpieczanie inteligentnych rozwiązań brzegowych](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy promować łatwość kontroli i łatwość implementacji, które nie są kluczowa zabezpieczeń.  Rozwiązanie dobrze zaprojektowanej architektury zabezpieczeń powinny przystosowany do kontroli w ramach oceny do tworzenia relacji zaufania i nie powinny być próg wykluczający do wdrożenia.  Projekt Framework, aby zabezpieczyć usługi Azure IoT Edge stanowi sprawdzona i zabezpieczeń sprawdzonej przez branżę protokoły wykorzystać znajomość i ponowne użycie. 

## <a name="authentication"></a>Authentication

Znajomość bez wątpliwości, w jakich podmiotów, urządzenia i składniki uczestniczą w dostarczaniu wartości za pomocą kompleksowego rozwiązania IoT jest najważniejszą kwestią w tworzeniu relacji zaufania.  Takie wiedzy oferuje bezpieczne odpowiedzialność uczestników z włączaniem podstawę do przyjęcia.  Usługa Azure IoT Edge zapewnia tej wiedzy za pomocą uwierzytelniania.  Podstawowy mechanizm uwierzytelniania dla platformy Azure IoT Edge jest uwierzytelnianie oparte na certyfikatach.  Ten mechanizm jest pochodną zestaw standardów dotyczących infrastruktury klucza publicznego (PKiX) przez Internet Engineering Task Force (IETF).     

Struktura zabezpieczeń usługi Azure IoT Edge wywołuje tożsamości unikatowy certyfikat dla wszystkich urządzeń, moduły (kontenery, które hermetyzują logiki urządzenia) i aktorów interakcji z urządzeniem Azure IoT Edge fizycznie lub za pośrednictwem połączenia sieciowego.  Nie każdy scenariusz lub składnika może przystosowany do uwierzytelniania opartego na certyfikatach, dla którego extensibility Framework, zabezpieczenia oferuje bezpieczne drogi prowadzące do pomieszczenia. 

## <a name="authorization"></a>Autoryzacja

Możliwość delegowania urzędu i kontroli dostępu jest niezwykle istotne w osiąganiu zasada zabezpieczeń podstawowych — zasadę najmniejszych uprawnień.  Urządzenia, moduły i aktorów mogą uzyskać dostęp tylko do zasobów i danych w obrębie swojego zakresu uprawnień, i tylko wtedy, gdy jest dopuszczalne pod względem architektury.  Oznacza to, że niektóre uprawnienia są konfigurowalne wystarczające uprawnienia, i innym osobom pod względem architektury wymuszane.  Na przykład gdy moduł może dokonywać za pośrednictwem konfiguracji uprzywilejowanych inicjować połączenie z usługą Azure IoT Hub, istnieje bez powodu, dlaczego modułu w jedno urządzenie usługi Azure IoT Edge należy uzyskać dostępu do bliźniaczej reprezentacji modułu na innym urządzeniu usługi Azure IoT Edge.  Z tego powodu ten ostatni będzie się pod względem architektury wykluczone. 

Inne schematy autoryzacji to prawa podpisywania certyfikatów i kontroli dostępu opartej na rolach (RBAC).  Extensibility Framework zabezpieczeń pozwala na wdrożenie innych schematów dojrzała autoryzacji. 

## <a name="attestation"></a>Zaświadczanie

Zaświadczania gwarantuje integralność oprogramowania bitów.  Jest ważne w przypadku wykrywania i zapobiegania złośliwym oprogramowaniem.  Struktura zabezpieczeń usługi Azure IoT Edge klasyfikuje zaświadczania w obszarze trzy główne kategorie:

* Statyczne zaświadczania
* Środowisko uruchomieniowe zaświadczania
* Oprogramowanie zaświadczania

### <a name="static-attestation"></a>Statyczne zaświadczania

Statyczne zaświadczania jest Weryfikacja integralności wszystkie bity oprogramowania, w tym z systemami operacyjnymi wszystkie środowiska wykonawcze, i zasilanie informacje o konfiguracji na urządzeniu.  Jest ona często nazywa Bezpieczny rozruch.  Struktura zabezpieczeń dla urządzeń z usługi Azure IoT Edge rozszerza dolina dostawcom i dołącza możliwości bezpiecznego sprzętu utrwalone zapewnienie procesy statyczne zaświadczania. Procesy te obejmują bezpiecznego rozruchu i oprogramowanie układowe bezpieczne uaktualnienia procesów.  W bliskiej współpracy z dostawcami dolina eliminuje warstwy zbędne oprogramowanie układowe, minimalizując tym samym powierzchni zagrożeń. 

### <a name="runtime-attestation"></a>Środowisko uruchomieniowe zaświadczania

Po system została zakończona do procesu rozruchu zweryfikowane i jest uruchomiona, i uruchomione, dobrze zaprojektowanych systemów zabezpieczeń może wykryć próbuje wstrzyknąć złośliwe oprogramowanie za pośrednictwem systemów porty i interfejsy i podjąć odpowiednie środki zaradcze.  W przypadku urządzeń inteligentnych rozwiązań brzegowych w fizycznych nadzoru nad uczestników złośliwych działań można wstawić malcontent za pośrednictwem oznacza, że inne niż interfejsy urządzenia, takie jak manipulowanie i ataki kanału po stronie.   Takie malcontent, która może być w formie złośliwe oprogramowanie lub zmiany konfiguracji nieautoryzowany, zwykle nie będzie wykryte przez proces bezpieczny rozruch, ponieważ są wykonywane podczas procesu rozruchu.  Środki zaradcze oferowane lub wymuszane przez urządzenia sprzętowe znacznie przyczynia się do takiego zagrożenia przed niepożądanymi.  Struktura zabezpieczeń dla usługi Azure IoT Edge jawnie wywołuje rozszerzeń combatting zagrożeń dla środowiska uruchomieniowego dla.     

### <a name="software-attestation"></a>Oprogramowanie zaświadczania

Wszystkie systemy dobrej kondycji, w tym systemów inteligentnych rozwiązań brzegowych musi być nadającymi się do poprawek i uaktualnień.  Zabezpieczenia jest ważne dla procesów aktualizacji w przeciwnym razie mogą być potencjalne wektorami zagrożenia.  Struktura zabezpieczeń dla wywołań usługi Azure IoT Edge aktualizacje za pośrednictwem mierzona i podpisanych pakietów do zapewnienia integralności i uwierzytelniania źródło pakietów.  To ma zastosowanie do wszystkich systemów operacyjnych i aplikacji oprogramowania z usługi bits. 

## <a name="hardware-root-of-trust"></a>Sprzęt elementu głównego zaufania

W przypadku wdrożeń wiele urządzeń inteligentnych rozwiązań brzegowych, zwłaszcza tych, które wdrożone w miejscach, w których potencjalnych uczestników złośliwych działań mają fizyczny dostęp do urządzenia zabezpieczeń oferowanych przez urządzeń sprzętowych jest ostatnim podstawowym narzędziem do ochrony.  Z tego powodu kotwiące zaufania manipulacje sprzętu jest najbardziej krytycznym w przypadku takich wdrożeń.  Struktura zabezpieczeń dla usługi Azure IoT Edge pociąga za sobą współpracę dolina bezpiecznego dostawcami sprzętu, aby oferować różnymi odmianami sprzętu elementu głównego zaufania dla różnych profilów ryzyka i scenariuszy wdrażania. Obejmują one użycie bezpiecznego dolina przestrzega wspólnej standardów protokół zabezpieczeń, takich jak Trusted Platform Module (ISO/IEC 11889) i Trusted Computing Group firmy identyfikator kompozycji aparatem DICE (Device).  Obejmują one bezpieczne enklawy technologii, takich jak TrustZones i oprogramowania rozszerzenia Guard (SGX). 

## <a name="certification"></a>Certyfikacja

Aby pomóc klientom w podejmowaniu świadomych wyborów podczas nabywanie urządzeń usługi Azure IoT Edge do ich wdrożenia, w ramach usługi Azure IoT Edge zawiera wymagania dotyczące certyfikacji.  Podstawowe te wymogi są certyfikaty dotyczące bezpieczeństwa i certyfikaty dotyczące weryfikacji wdrożenia zabezpieczeń.  Na przykład certyfikacji oświadczeń zabezpieczeń będzie poinformować, że urządzenie usługi IoT Edge korzysta z bezpiecznego sprzętu znane oprzeć się atakom rozruchu. Certyfikacji sprawdzania poprawności będzie poinformować, że bezpieczny sprzęt prawidłowo została zaimplementowana do zaoferowania tej wartości na urządzeniu.  Zgodnie z zasadą prostotę struktura oferuje wizji zachowaniu minimalnego obciążenia certyfikacji.   

## <a name="extensibility"></a>Rozszerzalność

Możliwość rozbudowy jest teraz najwyższej jakości dla obywateli w ramach zabezpieczeń usługi Azure IoT Edge.  Przy użyciu technologii IoT zapewniają różne rodzaje przekształcenia firmy jego brzmieniu przyczyny, że zabezpieczeń powinny bezproblemowo ewoluuj po lockstep adres pojawiających się scenariuszy.  Struktura zabezpieczeń usługi Azure IoT Edge rozpoczyna się od solidną podstawę, na którym opiera się on w rozszerzalności w różnych wymiarach do uwzględnienia: 

* Pierwsze usługi zabezpieczeń innych firm, takich jak usługi Device Provisioning Service dla usługi Azure IoT Hub.
* Usługi innych firm, takich jak usługi zarządzanej zabezpieczeń dla różnych aplikacji branżowych (np. przemysłowych lub opieki zdrowotnej) lub zespół technologii (np. monitorowanie zabezpieczeń siatki sieci lub dolina sprzętu zaświadczania usługi) za pośrednictwem sieci sformatowanego partnerów.
* Starsze systemy obejmujący modernizacji za pomocą strategii zabezpieczeń alternatywne, np. przy użyciu technologii zabezpieczeń innych niż certyfikatów do uwierzytelniania i zarządzania tożsamościami.
* Bezpieczny sprzęt do łatwego przyjęcie nowych technologii bezpieczny sprzęt i dolina wkładów partnera.

Oto kilka przykładów wymiary rozszerzalności i struktury zabezpieczeń usługi Azure IoT Edge jest przeznaczona do zabezpieczenia od podstaw do obsługi tego rozszerzalności. 

Na koniec najwyższy sukces w zabezpieczaniu inteligentnych rozwiązań brzegowych wynika z współpracy wkład w społeczność open prowadzona przez wspólne zainteresowanie zabezpieczanie IoT.  Tych wkładów może być w formie bezpiecznego technologii lub usług.  Struktura zabezpieczeń usługi Azure IoT Edge zapewnia solidną podstawę dla zabezpieczeń, którą można rozszerzać, aby zapewnić maksymalne pokrycie do zaoferowania w tym samym poziomie zaufania i integralności w inteligentnych węzłach brzegowych jako za pomocą usługi Azure cloud.  

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat sposobu usługi Azure IoT Edge jest [zabezpieczanie inteligentnych rozwiązań brzegowych](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
