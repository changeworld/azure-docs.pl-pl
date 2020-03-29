---
title: Struktura zabezpieczeń — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Dowiedz się więcej o standardach zabezpieczeń, uwierzytelniania i autoryzacji, które zostały użyte do opracowania usługi Azure IoT Edge i powinny być brane pod uwagę podczas projektowania rozwiązania
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760117"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpieczeń usługi Azure IoT Edge

Usługa Azure IoT Edge rozwiązuje problemy związane z przenoszeniem danych i analiz do inteligentnej krawędzi. Standardy zabezpieczeń usługi IoT Edge równoważą elastyczność dla różnych scenariuszy wdrażania z ochroną, której oczekujesz od wszystkich usług platformy Azure.

Usługi IoT Edge działają na różnych formatach i modelach sprzętu, obsługują kilka systemów operacyjnych i stosuje się do różnych scenariuszy wdrażania. Zamiast oferować konkretne rozwiązania dla określonych scenariuszy, usługa IoT Edge jest rozszerzalną strukturą zabezpieczeń opartą na dobrze uziemionych zasadach, które są przeznaczone do skalowania. Ryzyko wdrożenia zależy od wielu czynników, w tym:

* Własność rozwiązania
* Geografia wdrażania
* Czułość danych
* Ochrona prywatności
* Zastosowanie w pionie
* Wymogi regulacyjne

Ten artykuł zawiera omówienie struktury zabezpieczeń usługi IoT Edge. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie inteligentnej krawędzi](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy promują łatwość kontroli i łatwość wdrażania, które są cechami bezpieczeństwa. Rozwiązanie zabezpieczeń powinno być przeznaczone do kontroli w ramach oceny w celu budowania zaufania i nie powinno być przeszkodą do wdrożenia. Projekt struktury zabezpieczającej usługę Azure IoT Edge opiera się na testowanych w czasie i sprawdzonych w branży protokołach zabezpieczeń do znajomości i ponownego użycia.

## <a name="authentication"></a>Uwierzytelnianie

Podczas wdrażania rozwiązania IoT, należy wiedzieć, że tylko zaufane podmioty, urządzenia i moduły mają dostęp do rozwiązania. Uwierzytelnianie oparte na certyfikatach jest podstawowym mechanizmem uwierzytelniania dla platformy Usługi Azure IoT Edge. Mechanizm ten jest pochodną zestawu standardów regulujących infrastrukturę kluczy publicznych (PKiX) przez Internet Engineering Task Force (IETF).

Wszystkie urządzenia, moduły i podmioty, które współdziałają z urządzeniem usługi Azure IoT Edge powinny mieć unikatowe tożsamości certyfikatów. Niniejsza wskazówka dotyczy tego, czy interakcje są fizyczne, czy za pośrednictwem połączenia sieciowego. Nie każdy scenariusz lub składnik może nadawać się do uwierzytelniania opartego na certyfikatach, więc rozszerzalność struktury zabezpieczeń oferuje bezpieczne alternatywy.

Aby uzyskać więcej informacji, zobacz [Użycie certyfikatu usługi Azure IoT Edge](iot-edge-certs.md).

## <a name="authorization"></a>Autoryzacja

Zasada najmniejszych uprawnień mówi, że użytkownicy i składniki systemu powinny mieć dostęp tylko do minimalnego zestawu zasobów i danych potrzebnych do wykonywania ich ról. Urządzenia, moduły i podmioty powinny uzyskiwać dostęp tylko do zasobów i danych w zakresie ich uprawnień i tylko wtedy, gdy jest to architektonicznie dozwolone. Niektóre uprawnienia są konfigurowane z wystarczającymi uprawnieniami, a inne są wymuszane architektonicznie. Na przykład niektóre moduły mogą być autoryzowane do łączenia się z usługą Azure IoT Hub. Jednak nie ma powodu, dla którego moduł w jednym urządzeniu IoT Edge powinien uzyskać dostęp do bliźniaczej reprezentacji modułu w innym urządzeniu IoT Edge.

Inne schematy autoryzacji obejmują prawa do podpisywania certyfikatów i kontrolę dostępu opartą na rolach (RBAC).

## <a name="attestation"></a>Poświadczenie

Zaświadczenie zapewnia integralność bitów oprogramowania, co jest ważne dla wykrywania złośliwego oprogramowania i zapobiegania temu oprogramowaniu. Struktura zabezpieczeń usługi Azure IoT Edge klasyfikuje zaświadczanie w trzech głównych kategoriach:

* Zaświadczenie statyczne
* Zaświadczenie o czasie wykonywania
* Zaświadczenie o oprogramowaniu

### <a name="static-attestation"></a>Zaświadczenie statyczne

Statyczne zaświadczenie weryfikuje integralność całego oprogramowania na urządzeniu podczas włączania zasilania, w tym systemu operacyjnego, wszystkich czasów pracy i informacji o konfiguracji. Ponieważ statyczne zaświadczanie występuje podczas włączania, jest często określane jako bezpieczny rozruch. Struktura zabezpieczeń dla urządzeń IoT Edge obejmuje producentów i zawiera bezpieczne funkcje sprzętowe, które zapewniają statyczne procesy zaświadczania. Procesy te obejmują bezpieczny rozruch i bezpieczne uaktualnienie oprogramowania układowego. Ścisła współpraca z dostawcami krzemu eliminuje zbędne warstwy oprogramowania układowego, minimalizuje to powierzchnię zagrożenia.

### <a name="runtime-attestation"></a>Zaświadczenie o czasie wykonywania

Po zakończeniu procesu bezpiecznego rozruchu, dobrze zaprojektowane systemy powinny wykryć próby wstrzyknięcia złośliwego oprogramowania i podjąć odpowiednie środki zaradcze. Ataki złośliwego oprogramowania mogą być kierowane na porty i interfejsy systemu. Jeśli złośliwi aktorzy mają fizyczny dostęp do urządzenia, mogą manipulować samym urządzeniem lub użyć ataków kanału bocznego, aby uzyskać dostęp. Takie malcontent, czy złośliwe oprogramowanie lub nieautoryzowane zmiany konfiguracji, nie mogą być wykryte przez statyczne zaświadczenie, ponieważ jest wstrzykiwany po procesie rozruchu. Środki zaradcze oferowane lub wymuszane przez sprzęt urządzenia pomagają odeprzeć takie zagrożenia. Struktura zabezpieczeń dla aplikacji IoT Edge jawnie wymaga rozszerzeń, które zwalczają zagrożenia środowiska uruchomieniowego.  

### <a name="software-attestation"></a>Zaświadczenie o oprogramowaniu

Wszystkie zdrowe systemy, w tym inteligentne systemy brzegowe, wymagają poprawek i ulepszeń. Zabezpieczenia są ważne dla procesów aktualizacji, w przeciwnym razie mogą być potencjalnymi wektorami zagrożeń. Struktura zabezpieczeń dla usługi IoT Edge wymaga aktualizacji za pośrednictwem zmierzonych i podpisanych pakietów w celu zapewnienia integralności i uwierzytelnienia źródła pakietów. Niniejszy standard dotyczy wszystkich systemów operacyjnych i bitów oprogramowania aplikacji.

## <a name="hardware-root-of-trust"></a>Sprzętowy katalog główny zaufania

W przypadku wielu inteligentnych urządzeń brzegowych, zwłaszcza urządzeń, do których mogą być fizycznie dostępni przez potencjalnych złośliwych aktorów, bezpieczeństwo sprzętu jest ostatnią obroną dla ochrony. Sprzęt odporny na manipulacje ma kluczowe znaczenie dla takich wdrożeń. Usługa Azure IoT Edge zachęca dostawców bezpiecznego sprzętu krzemowego do oferowania różnych smaków sprzętu głównego zaufania, aby dostosować się do różnych profili ryzyka i scenariuszy wdrażania. Zaufanie sprzętowe może pochodzić ze wspólnych standardów protokołu zabezpieczeń, takich jak moduł trusted platform (ISO/IEC 11889) i aparat kompozycji identyfikatorów urządzeń (DICE) grupy trusted computing group. Bezpieczne technologie enklawy, takie jak TrustZones i Software Guard Extensions (SGX), również zapewniają zaufanie do sprzętu.

## <a name="certification"></a>Certyfikacja

Aby ułatwić klientom podejmowanie świadomych decyzji podczas zamawiania urządzeń usługi Azure IoT Edge do ich wdrożenia, struktura usługi IoT Edge zawiera wymagania dotyczące certyfikacji. Podstawą tych wymagań są certyfikaty odnoszące się do oświadczeń o bezpieczeństwie i certyfikatów odnoszących się do walidacji implementacji zabezpieczeń. Na przykład certyfikacja oświadczeń zabezpieczeń oznacza, że urządzenie Usługi IoT Edge używa bezpiecznego sprzętu, o których wiadomo, że jest odporny na ataki rozruchowe. Certyfikacja sprawdzania poprawności oznacza, że bezpieczny sprzęt został prawidłowo zaimplementowany, aby zaoferować tę wartość w urządzeniu. Zgodnie z zasadą prostoty, ramy starają się utrzymać minimalny ciężar certyfikacji.

## <a name="extensibility"></a>Rozszerzalność

Dzięki technologii IoT, która napędza różne rodzaje transformacji biznesowej, zabezpieczenia powinny ewoluować równolegle, aby sprostać pojawiającym się scenariuszom. Struktura zabezpieczeń usługi Azure IoT Edge rozpoczyna się od solidnego fundamentu, na którym tworzy rozszerzalność na różne wymiary, aby uwzględnić:

* Usługi zabezpieczeń innych firm, takie jak usługa inicjowania obsługi administracyjnej urządzeń dla usługi Azure IoT Hub.
* Usługi innych firm, takie jak zarządzane usługi zabezpieczeń dla różnych branż aplikacji (takich jak przemysł lub służba zdrowia) lub skoncentrowanie się na technologii (np. monitorowanie bezpieczeństwa w sieciach mesh lub usługi zaświadczania sprzętu krzemowego) za pośrednictwem bogatej sieci Partnerów.
* Starsze systemy obejmują doposażenie w alternatywne strategie zabezpieczeń, takie jak używanie bezpiecznej technologii innej niż certyfikaty do uwierzytelniania i zarządzania tożsamościami.
* Bezpieczny sprzęt do wdrażania nowych technologii bezpiecznych sprzętu i wkładu partnerów krzemowych.

W końcu zabezpieczenie inteligentnej przewagi wymaga wspólnego wkładu otwartej społeczności, kierującego się wspólnym interesem w zabezpieczeniu IoT. Wkład ten może mieć formę bezpiecznych technologii lub usług. Struktura zabezpieczeń usługi Azure IoT Edge oferuje solidne podstawy zabezpieczeń, które są rozszerzalne dla maksymalnego pokrycia, aby zapewnić taki sam poziom zaufania i integralności w inteligentnej krawędzi, jak w przypadku chmury platformy Azure.  

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak usługa Azure IoT Edge [zabezpiecza inteligentną krawędź](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
