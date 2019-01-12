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
ms.openlocfilehash: b174d7f9b4b8438687512a90dc7a65b5649f758a
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229894"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpieczeń usługi Azure IoT Edge

Przenoszenie danych i analizy na inteligentnych urządzeniach brzegowych tworzy ryzyka scenariusze, które usługi Azure IoT Edge jest przeznaczony do adresów. Standardy zabezpieczeń usługi IoT Edge są przeznaczone do zapewniają elastyczność dla wdrożeń i profile ryzyka różnych, jednocześnie zapewniając taką samą ochronę, który można oczekiwać od wszystkich usług platformy Azure. 

Usługa Azure IoT Edge działa na różnych sprawia, że sprzęt i modeli, obsługuje kilka systemów operacyjnych i ma zastosowanie do różnorodnych wdrożeń. Oceny ryzyka scenariusz wdrażania, zależy od wielu zagadnienia, w tym własności rozwiązania obszaru geograficznego wdrożenia, wrażliwości danych, ochrony prywatności, aplikacji pionowy, wymaganiami prawnymi w zakresie. Zamiast oferty konkretnych rozwiązań dla konkretnych scenariuszy, IoT Edge to struktura extensible zabezpieczeń opartych na zasadach dobrze jest ona przeznaczona dla skalowania. 
 
Ten artykuł zawiera omówienie struktury zabezpieczeń usługi IoT Edge. Aby uzyskać więcej informacji, zobacz [zabezpieczanie inteligentnych rozwiązań brzegowych](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy wspieranie łatwość kontroli i łatwość implementacji, które są hallmarks zabezpieczeń. Rozwiązanie w zakresie zabezpieczeń należy przystosowany do kontroli w ramach oceny do tworzenia relacji zaufania i nie powinien być próg wykluczający do wdrożenia. Projekt Framework, aby zabezpieczyć usługi Azure IoT Edge zależy od sprawdzona i zabezpieczeń sprawdzonej przez branżę protokoły znajomość i ponowne używanie. 

## <a name="authentication"></a>Authentication

Podczas wdrażania rozwiązania IoT, musisz wiedzieć, że tylko zaufane aktorów, urządzenia i składniki mają dostęp do rozwiązania. Takie wiedzy oferuje bezpieczne odpowiedzialność uczestników z włączaniem podstawę do przyjęcia.  Usługa Azure IoT Edge zapewnia tej wiedzy za pomocą uwierzytelniania.  Podstawowy mechanizm uwierzytelniania dla platformy Azure IoT Edge jest uwierzytelnianie oparte na certyfikatach.  Ten mechanizm jest pochodną zestaw standardów dotyczących infrastruktury klucza publicznego (PKiX) przez Internet Engineering Task Force (IETF).     

Wszystkie urządzenia, moduły i aktorów interakcji z urządzeniem Azure IoT Edge, czy fizycznie lub za pośrednictwem połączenia sieciowego, powinny mieć unikatowy certyfikat tożsamości. Nie każdy scenariusz lub składnika może przystosowany do uwierzytelniania opartego na certyfikatach. W tych scenariuszach extensibility Framework, zabezpieczenia oferuje bezpieczne alternatyw. 

## <a name="authorization"></a>Autoryzacja

Zasadę najmniejszych uprawnień mówi, że użytkownicy i składników systemu powinny mieć dostęp tylko minimalny zestaw zasobów i dane wymagane do wykonania ich ról. Urządzenia, moduły i aktorów powinien uzyskiwać dostęp do zasobów i danych w obrębie swojego zakresu uprawnień i tylko wtedy, gdy jest pod względem architektury dopuszczalny rozmiar. Niektóre uprawnienia są konfigurowalne o odpowiednich uprawnieniach i innych pod względem architektury wymuszane.  Na przykład moduł może dokonywać za pośrednictwem konfiguracji uprzywilejowanych inicjować połączenie z usługą Azure IoT Hub. Jednak nie ma powodu Dlaczego modułu w jedno urządzenie usługi Azure IoT Edge należy uzyskać dostępu do bliźniaczej reprezentacji modułu na innym urządzeniu usługi Azure IoT Edge.

Inne schematy autoryzacji to certyfikat podpisywania prawa, kontrola dostępu oparta na rolach (RBAC) i innych systemów dojrzała autoryzacji. 

## <a name="attestation"></a>Zaświadczanie

Zaświadczania gwarantuje integralność oprogramowania bitów.  Jest ważne w przypadku wykrywania i zapobiegania złośliwym oprogramowaniem.  Struktura zabezpieczeń usługi Azure IoT Edge klasyfikuje zaświadczania w obszarze trzy główne kategorie:

* Statyczne zaświadczania
* Środowisko uruchomieniowe zaświadczania
* Oprogramowanie zaświadczania

### <a name="static-attestation"></a>Statyczne zaświadczania

Statyczne zaświadczania weryfikuje integralność wszystkich składników oprogramowania na urządzeniu, w tym systemu operacyjnego, wszystkie środowiska uruchomieniowe i informacji o konfiguracji w zasilanie urządzenia. Jest ona często nazywa Bezpieczny rozruch. Struktura zabezpieczeń dla urządzeń z usługi Azure IoT Edge rozszerza producentów i dołącza możliwości bezpiecznego sprzętu, które zapewnią procesy statyczne zaświadczania. Procesy te obejmują bezpiecznego rozruchu i oprogramowanie układowe bezpieczne uaktualnienia.  W bliskiej współpracy z dostawcami dolina eliminuje zbędne oprogramowanie układowe warstwy, dlatego minimalizuje powierzchni zagrożeń. 

### <a name="runtime-attestation"></a>Środowisko uruchomieniowe zaświadczania

Gdy system ukończeniu procesu bezpiecznego rozruchu i jest uruchomiona, dobrze zaprojektowanych systemów będzie wykryć próbuje wstrzyknąć złośliwe oprogramowanie i podejmij odpowiednie środki zaradcze. Atakami złośliwego oprogramowania mogą odnosić się do systemu porty i interfejsy w celu uzyskania dostępu do systemu. Lub, jeśli uczestników złośliwych działań ma fizyczny dostęp do urządzenia mogą manipulować samego urządzenia lub przy użyciu kanału po stronie ataków uzyskują dostęp. Takie malcontent, która może być w formie złośliwe oprogramowanie lub zmiany konfiguracji nieautoryzowany, są wstrzykiwane po zakończeniu procesu rozruchu, więc statyczne zaświadczania nie wykryje. Środki zaradcze oferowanych lub wymuszane przez urządzenia sprzętowe pomocy odwrócenia takiego zagrożenia.  Struktura zabezpieczeń dla usługi Azure IoT Edge jawnie wywołuje rozszerzeń, które walczyć z zagrożeniami w czasie wykonywania.  

### <a name="software-attestation"></a>Oprogramowanie zaświadczania

Wszystkie systemy dobrej kondycji, w tym systemów inteligentnych rozwiązań brzegowych musi zaakceptować, poprawek i uaktualnień.  Zabezpieczenia jest ważne dla procesów aktualizacji w przeciwnym razie mogą być potencjalne wektorami zagrożenia.  Struktura zabezpieczeń dla wywołań usługi Azure IoT Edge aktualizacje za pośrednictwem mierzona i podpisanych pakietów do zapewnienia integralności i uwierzytelniania źródło pakietów.  Ten standard ma zastosowanie do wszystkich systemów operacyjnych i aplikacji oprogramowania z usługi bits. 

## <a name="hardware-root-of-trust"></a>Sprzęt elementu głównego zaufania

Dla wielu urządzeń inteligentnych rozwiązań brzegowych, szczególnie urządzeń w miejscach, w których potencjalnych uczestników złośliwych działań mają fizyczny dostęp do urządzenia, zasady zabezpieczeń urządzeń sprzętowych jest ostatnim podstawowym narzędziem do ochrony. Manipulacje sprzęt jest niezwykle istotne w przypadku takich wdrożeń. Usługa Azure IoT Edge zachęca dolina bezpiecznego dostawcami sprzętu, aby oferować różnymi odmianami sprzętu elementu głównego zaufania dla różnych profilów ryzyka i scenariuszy wdrażania. Sprzęt zaufania mogą pochodzić z typowych standardów protokół zabezpieczeń, takich jak Trusted Platform Module (ISO/IEC 11889) i Trusted Computing Group firmy identyfikator kompozycji aparatem DICE (Device). Bezpieczne enklawy technologii, takich jak TrustZones i oprogramowania rozszerzenia Guard (SGX) oferują również zaufania sprzętu. 

## <a name="certification"></a>Certyfikacja

Aby pomóc klientom w podejmowaniu świadomych wyborów podczas nabywanie urządzeń usługi Azure IoT Edge do ich wdrożenia, w ramach usługi Azure IoT Edge zawiera wymagania dotyczące certyfikacji.  Podstawowe te wymogi są certyfikaty dotyczące bezpieczeństwa i certyfikaty dotyczące weryfikacji wdrożenia zabezpieczeń.  Na przykład certyfikacji oświadczeń zabezpieczeń będzie poinformować, że urządzenie usługi IoT Edge korzysta z bezpiecznego sprzętu znane oprzeć się atakom rozruchu. Certyfikacji sprawdzania poprawności będzie poinformować, że bezpieczny sprzęt prawidłowo została zaimplementowana do zaoferowania tej wartości na urządzeniu.  Zgodnie z zasadą prostotę struktura próbuje zachować minimalne obciążenie certyfikacji.   

## <a name="extensibility"></a>Rozszerzalność

Możliwość rozbudowy jest teraz najwyższej jakości dla obywateli w ramach zabezpieczeń usługi Azure IoT Edge.  Dzięki technologii IoT zapewniają różne rodzaje przekształcenia firmy go oznacza przyczyny ciągły zabezpieczeń powinny rozwój w sposób równoległy adres pojawiających się scenariuszy.  Struktura zabezpieczeń usługi Azure IoT Edge rozpoczyna się od solidną podstawę, na którym opiera się on w rozszerzalności w różnych wymiarach do uwzględnienia: 

* Pierwsze usługi zabezpieczeń innych firm, takich jak usługi Device Provisioning Service dla usługi Azure IoT Hub.
* Usługi innych firm, takich jak usługi zarządzanej zabezpieczeń dla różnych aplikacji branżowych (np. przemysłowych lub opieki zdrowotnej) lub zespół technologii (np. monitorowanie zabezpieczeń siatki sieci lub dolina sprzętu zaświadczania usługi) za pośrednictwem sieci sformatowanego partnerów.
* Starsze systemy obejmujący modernizacji za pomocą strategii zabezpieczeń alternatywne, np. przy użyciu technologii zabezpieczeń innych niż certyfikatów do uwierzytelniania i zarządzania tożsamościami.
* Bezpieczny sprzęt dla przyjęcie nowych technologii bezpieczny sprzęt i dolina wkładów partnera.

Na koniec najwyższy sukces w zabezpieczaniu inteligentnych rozwiązań brzegowych wynika z współpracy wkład w społeczność open prowadzona przez wspólne zainteresowanie zabezpieczanie IoT.  Tych wkładów może być w formie bezpiecznego technologii lub usług.  Struktura zabezpieczeń usługi Azure IoT Edge zapewnia solidną podstawę dla zabezpieczeń, którą można rozszerzać, aby zapewnić maksymalne pokrycie do zaoferowania w tym samym poziomie zaufania i integralności w inteligentnych węzłach brzegowych jako za pomocą usługi Azure cloud.  

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat sposobu usługi Azure IoT Edge jest [zabezpieczanie inteligentnych rozwiązań brzegowych](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
