---
title: Omówienie usługi inicjowania obsługi administracyjnej urządzeniu Centrum IoT Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, inicjowanie obsługi administracyjnej urządzeń na platformie Azure z usługą inicjowania obsługi urządzeń i Centrum IoT
author: nberdy
ms.author: nberdy
ms.date: 12/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 45f47a553f94da2759c4db2b79c8ef5a1b42b8e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630247"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Inicjowania obsługi urządzeń przy użyciu usługi inicjowania obsługi urządzeń Centrum IoT Azure
Microsoft Azure oferuje bogaty zestaw usługi w chmurze publicznej zintegrowane wszystkie potrzeby rozwiązania IoT. Usługa udostępniania urządzeń Centrum IoT to usługa pomocnika do Centrum IoT, umożliwiającą bezobsługową, w czasie inicjowania obsługi administracyjnej Centrum IoT w prawo bez udziału człowieka, umożliwiając klientom udostępniania milionów urządzeń bezpieczne i skalowalne sposób.

## <a name="when-to-use-device-provisioning-service"></a>Kiedy należy używać usługi inicjowania obsługi urządzeń
Istnieje wiele scenariuszy inicjowania obsługi administracyjnej, w których usługa inicjowania obsługi urządzeń jest doskonałym rozwiązaniem dla pobierania urządzenia podłączone i skonfigurowane do Centrum IoT, takich jak:

* Bezobsługową udostępniania jednego rozwiązania IoT bez hardcoding Centrum IoT informacji o połączeniu w fabryce (początkowej konfiguracji)
* Równoważenie obciążenia urządzeń w wielu centrów
* Łączenie urządzenia z rozwiązania IoT ich właściciela na podstawie danych sprzedaży transakcji (wielodostępności)
* Podłączanie urządzeń do danego rozwiązania IoT, w zależności od przypadek użycia (rozwiązania izolacji)
* Łączenie urządzenia z Centrum IoT z najniższym opóźnieniu (geograficznie-dzielenia na fragmenty)
* Ponowne inicjowanie obsługi administracyjnej oparte na zmiany w urządzeniu
* Wprowadzanie kluczy używanych przez urządzenia do łączenia się Centrum IoT (podczas nawiązywania połączenia, nie za pomocą certyfikatów X.509)

## <a name="behind-the-scenes"></a>Za kulisami
Wszystkie scenariusze, które są wymienione w poprzedniej sekcji można wykonać przy użyciu usługi inicjowania obsługi administracyjnej dla bezobsługową inicjowania obsługi administracyjnej z takim samym przepływie. Wiele ręcznych czynności tradycyjnie objętego inicjowania obsługi administracyjnej są automatyzowane przy użyciu usługi inicjowania obsługi urządzeń, aby skrócić czas wdrażania urządzenia IoT i zmniejszyć ryzyko błędu ręcznego. Poniżej znajduje się opis co dzieje się w tle, aby uzyskać urządzenia udostępnionego. Pierwszym krokiem jest ręczne, automatyczne są wszystkie poniższe kroki.

![Podstawowy przepływ inicjowania obsługi administracyjnej](./media/about-iot-dps/dps-provisioning-flow.png)

1. Producent urządzenia dodaje informacje o rejestracji urządzeń do listy rejestracji w portalu Azure.
2. Urządzenie kontaktuje się ustawić w fabryce inicjowania obsługi administracyjnej punktu końcowego usługi. Urządzenie przekazuje inicjowania obsługi usługi jego informacje identyfikacyjne, aby potwierdzić swoją tożsamość.
3. Inicjowania obsługi usługi weryfikuje tożsamość urządzenia weryfikując rejestracji identyfikator i klucz przed wpisu listy rejestracji za pomocą identyfikatora jednorazowego żądanie ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) lub standardowy (weryfikacji X.509 X.509).
4. Usługa dostarczania rejestruje urządzenie z Centrum IoT i wypełnienie urządzenia [żądany stan dwie](../iot-hub/iot-hub-devguide-device-twins.md).
5. Centrum IoT zwraca informacji o identyfikatorze urządzenia do inicjowania obsługi usługi.
6. Usługa inicjowania obsługi administracyjnej zwraca informacje o połączeniu Centrum IoT na urządzeniu. Urządzenie można teraz rozpocząć wysyłanie danych bezpośrednio do Centrum IoT.
7. Urządzenie łączy się z Centrum IoT.
8. Urządzenie pobiera żądanego stanu z jego dwie urządzenie w Centrum IoT.

## <a name="provisioning-process"></a>Proces inicjowania obsługi administracyjnej
Istnieją dwa różne czynności w procesie wdrażania urządzenia, w którym odbywa się części, może odbywać się niezależnie usługi inicjowania obsługi urządzeń:

* **Krok produkcyjnym** w którym urządzenie nie zostanie utworzona i przygotowane w fabryce, i
* **Kroku konfiguracji chmury** w usługi inicjowania obsługi urządzeń jest skonfigurowany do automatycznego inicjowania obsługi administracyjnej.

Oba te kroki mieści się w bezproblemowo z istniejących procesów wytwarzania i wdrożenia. Usługa udostępniania urządzeń nawet upraszcza niektórych procesów wdrażania, obejmujących wiele ręcznych, aby uzyskać informacje dotyczące połączenia na urządzeniu.

### <a name="manufacturing-step"></a>Krok produkcyjnym
Ten krok jest wszystko co się dzieje z wiersza produkcyjnym. Role związane z tego kroku obejmują krzemu projektanta, krzemu producenta, integrator i/lub zakończenia producenta urządzenia. Ten krok dotyczy tworzenia samego sprzętu.

Usługa inicjowania obsługi urządzeń nie wprowadza nowym krokiem w procesie wytwarzania; zamiast wiąże do istniejących krok, który instaluje oprogramowanie początkowa i (najlepiej) modułu HSM na urządzeniu. Zamiast tworzenia identyfikator urządzenia, w tym kroku, urządzenie jest po prostu zaprogramowane z danych inicjowania obsługi usługi, wywołuje metodę inicjowania obsługi administracyjnej usługi, aby uzyskać jego przypisania rozwiązania IoT/informacji połączenia, gdy jest włączone.

W tym kroku producent dostarcza również urządzenia wdrażającego/operator identyfikowania informacji o kluczu. Może to być najprostszą potwierdzenie, że wszystkie urządzenia mają certyfikat X.509 wygenerowane z certyfikatu podpisywania dostarczanych przez narzędzie wdrażania urządzenia/operatora, wyodrębnianie publicznej części klucza poręczenia modułu TPM z każdego urządzenia modułu TPM. Te usługi są oferowane przez wielu producentów krzemu dzisiaj.

### <a name="cloud-setup-step"></a>Krok ustawienia chmury
Ten krok dotyczy Konfigurowanie chmury na potrzeby sprawnego automatycznego inicjowania obsługi administracyjnej. Zazwyczaj istnieją dwa typy użytkowników, związane z kroku konfiguracji chmury: kogoś, kto wie, jak urządzenia muszą być wstępnie skonfigurowane (operator urządzenia), a ktoś inny, który zna, jak urządzenia mają być dzielone między centra IoT (operator rozwiązania).

Brak jednorazowej konfiguracji początkowej konfiguracji, który musi występować i to zadanie jest ogólnie obsługiwane przez operator rozwiązania. Po skonfigurowaniu usługi inicjowania obsługi administracyjnej, nie trzeba można modyfikować, jeśli przypadek użycia zmiany.

Po usługa została skonfigurowana do automatycznego inicjowania obsługi, muszą być przygotowane do rejestracji urządzeń. Ten krok jest realizowane przez operatora urządzenia, który zna odpowiednią konfigurację urządzenia i jest odpowiedzialny za zapewnienie zgodności inicjowania obsługi usługi można prawidłowo potwierdzają tożsamości urządzenia po przejściu do wyszukiwania dla swojego Centrum IoT. Operator urządzenia przyjmuje dane identyfikacyjne klucza od producenta i dodaje go do listy rejestracji. Może istnieć kolejnych aktualizacji do listy rejestracji, nowe wpisy są dodawane lub istniejące wpisy są aktualizowane przy użyciu najnowszych informacji o urządzeniach.

## <a name="registration-and-provisioning"></a>Rejestracji i inicjowania obsługi administracyjnej
*Inicjowanie obsługi administracyjnej* oznacza różnych rzeczy, w zależności od branży, w którym używany jest termin. W kontekście inicjowania obsługi urządzeń IoT do rozwiązania w chmurze Inicjowanie obsługi to proces dwie części:

1. Pierwsza część jest ustanowienie początkowego połączenia między urządzeniem a rozwiązaniem IoT, rejestrując urządzenia.
2. Druga część jest stosowanie właściwej konfiguracji do urządzenia na podstawie określonych wymagań rozwiązania, który został zarejestrowany.

Tylko w przypadku, gdy oba te dwa kroki zostały ukończone możemy powiedzieć pełni zaaprowizowanym urządzenia. Niektóre usługi w chmurze tylko zapewnić pierwszym krokiem procesu zastrzegania, rejestrowanie urządzeń do punktu końcowego rozwiązania IoT, ale nie zapewniają konfiguracji początkowej. Usługa udostępniania urządzeń automatyzuje obu czynności, aby zapewnić bezproblemowe inicjowania obsługi administracyjnej dla urządzenia.

## <a name="features-of-the-device-provisioning-service"></a>Funkcje inicjowania obsługi usługi urządzeń
Usługa inicjowania obsługi urządzeń ma wiele funkcji, które idealne rozwiązanie w przypadku inicjowania obsługi urządzeń.

* **Zabezpieczanie zaświadczania** obsługę zarówno X.509, jak i opartych na modułach TPM tożsamości.
* **Lista rejestracji** zawierającego pełny rekord urządzenia/grup urządzeń, które mogą dla niektórych wskazać rejestru. Lista rejestracji zawiera informacje o żądanej konfiguracji urządzenia, gdy rejestruje i może być aktualizowana w dowolnym momencie.
* **Wiele zasad alokacji** do kontrolowania, jak usługa inicjowania obsługi urządzeń przypisuje urządzeń centra IoT wesprzeć scenariuszy.
* **Dzienniki monitorowania i diagnostyki** się upewnić, że wszystko działa poprawnie.
* **Obsługa wielu Centrum** dzięki czemu usługa inicjowania obsługi urządzeń, przypisywanie urządzeń do więcej niż jednego centrum IoT. Usługi udostępniania urządzenie może komunikować się koncentratorów przez wiele subskrypcji Azure.
* **Obsługa region między** dzięki czemu usługa inicjowania obsługi urządzeń, przypisywanie urządzeń do centra IoT w różnych regionach.

Dowiedz się więcej o pojęciach i funkcje związane z Inicjowanie obsługi administracyjnej urządzeń w [pojęcia urządzenia](concepts-device.md), [usługi pojęcia](concepts-service.md), i [pojęcia dotyczące zabezpieczeń](concepts-security.md).

## <a name="cross-platform-support"></a>Obsługa platform
Urządzenie usługi udostępniania, takich jak wszystkich usług Azure IoT, działa i platform z różnymi systemami operacyjnymi. Oferty Azure Otwórz zestawów SDK źródła w różnych [języków](https://github.com/Azure/azure-iot-sdks) ułatwiające podłączania urządzeń i zarządzanie usługą. Usługa udostępniania urządzeń obsługuje następujące protokoły czy za połączenie urządzeń:

* HTTPS
* AMQP
* Protokół AMQP przez protokół websockets
* MQTT
* MQTT przez protokół websockets

Usługa inicjowania obsługi urządzeń obsługuje tylko połączenia HTTPS dla operacji usługi.

## <a name="regions"></a>Regiony
Usługa inicjowania obsługi urządzeń jest dostępne w wielu regionach. Firma Microsoft zachowuje zaktualizowaną listę istniejących i nowo ogłoszenia regiony dla wszystkich usług w [regiony platformy Azure](https://azure.microsoft.com/regions/). Gdy usługa inicjowania obsługi urządzeń jest dostępne na [stan Azure](https://azure.microsoft.com/status/) strony.

> [!NOTE]
> Usługa udostępniania urządzeń jest globalna i niepowiązana do lokalizacji. Należy jednak określić regionu, w której będą znajdować się metadane skojarzone z profilem inicjowania obsługi usługi urządzeń.

## <a name="availability"></a>Dostępność
Firma Microsoft zachowuje 99,9% umową dotyczącą poziomu usług dla usługi udostępniania urządzenia, a można [odczytu umowy SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

## <a name="quotas"></a>Przydziały
Każda subskrypcja platformy Azure ma domyślne limity przydziału w miejscu, które może mieć wpływ na zakres rozwiązania IoT. Bieżący limit oparte na subskrypcji jest 10 urządzenia udostępnianie usług dla subskrypcji.

Więcej informacji na temat limitów przydziałów znajduje się w artykułach:

* [Azure Subscription Service Limits](../azure-subscription-service-limits.md) (Limity usług subskrypcji platformy Azure)

## <a name="related-azure-components"></a>Powiązane składniki platformy Azure
Usługa udostępniania urządzeń automatyzuje Inicjowanie obsługi administracyjnej urządzeń z Centrum IoT Azure. Dowiedz się więcej o [Centrum IoT](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Kolejne kroki
Masz teraz omówienie inicjowania obsługi urządzeń IoT na platformie Azure. Następnym krokiem jest wypróbowanie scenariusz IoT end-to-end.
> [!div class="nextstepaction"]
> [Konfigurowanie inicjowania obsługi usługi IoT Hub urządzeń przy użyciu portalu Azure](quick-setup-auto-provision.md)
> [tworzenia i udostępniania symulowane urządzenie](quick-create-simulated-device.md)
> [skonfigurować urządzenie do inicjowania obsługi](tutorial-set-up-device.md)
