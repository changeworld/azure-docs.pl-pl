---
title: Omówienie usługi Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: W tym artykule opisano aprowizację urządzeń na platformie Azure za pomocą usług Device Provisioning Service i IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 8331161cde9e7717834dabd2228848de40f7be23
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360303"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Aprowizowanie urządzeń za pomocą usługi Azure IoT Hub Device Provisioning Service
Platforma Microsoft Azure zapewnia bogaty zestaw zintegrowanych usług w chmurze publicznej spełniający wszelkie potrzeby rozwiązania IoT. Usługa IoT Hub Device Provisioning Service to usługa pomocnika usługi IoT Hub, która umożliwia bezobsługową aprowizację w miarę potrzeb w odpowiednim centrum IoT, nie wymagając przy tym interwencji człowieka, co umożliwia klientom aprowizację milionów urządzeń w sposób bezpieczny i skalowalny.

## <a name="when-to-use-device-provisioning-service"></a>Kiedy stosować usługę Device Provisioning Service
Istnieje wiele scenariuszy aprowizowania, w których usługa Device Provisioning Service jest doskonałym wyborem na potrzeby łączenia urządzeń z usługą IoT Hub oraz ich konfigurowania, np.:

* Bezobsługowe aprowizowanie w jednym rozwiązaniu IoT bez trwałego kodowania informacji o połączeniu z usługą IoT Hub w fabryce (konfiguracja początkowa)
* Równoważenie obciążenia urządzeń w wielu centrach
* Łączenie urządzeń z rozwiązaniem IoT ich właściciela na podstawie danych o transakcji sprzedaży (wielodostępność)
* Łączenie urządzeń z określonym rozwiązaniem IoT w zależności od przypadku użycia (izolacja rozwiązania)
* Łączenie urządzenia z centrum IoT o najmniejszym opóźnieniu (geograficzne dzielenie na fragmenty)
* Ponowne aprowizowanie na podstawie zmiany w urządzeniu
* Przerzucanie kluczy używanych przez urządzenie do nawiązywania połączenia z usługą IoT Hub (gdy na potrzeby połączenia nie są używane certyfikaty X.509)

## <a name="behind-the-scenes"></a>Za kulisami
Wszystkie scenariusze wymienione w poprzedniej sekcji można zrealizować przy użyciu usługi aprowizowania, aby uzyskać bezobsługową aprowizację w tym samym przepływie. Wiele czynności tradycyjnie wykonywanych ręcznie w ramach aprowizowania zostało zautomatyzowanych przez usługę Device Provisioning Service w celu skrócenia czasu wdrażania urządzeń IoT i zmniejszenia ryzyka błędu. W poniższej sekcji opisano, co dzieje się za kulisami, aby aprowizować urządzenie. Pierwszy krok jest ręczny, a wszystkie następne kroki są zautomatyzowane.

![Podstawowy przepływ aprowizowania](./media/about-iot-dps/dps-provisioning-flow.png)

1. Producent urządzenia dodaje jego informacje o rejestracji do listy rejestracji w witrynie Azure Portal.
2. Urządzenie kontaktuje się z punktem końcowym usługi aprowizowania ustawionym w fabryce. Urządzenie przekazuje informacje identyfikacyjne do usługi aprowizowania, aby potwierdzić swoją tożsamość.
3. Usługa aprowizowania sprawdza tożsamość urządzenia przez walidację identyfikatora rejestracji oraz klucza względem wpisu listy rejestracji, korzystając z weryfikacji za pomocą testu identyfikatora jednorazowego ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) lub standardowej weryfikacji X.509 (X.509).
4. Usługa aprowizowania rejestruje urządzenie w centrum IoT i wypełnia [żądany stan bliźniaczej reprezentacji](../iot-hub/iot-hub-devguide-device-twins.md) urządzenia.
5. Centrum IoT zwraca informacje o identyfikatorze urządzenia do usługi aprowizowania.
6. Usługa aprowizowania zwraca informacje o połączeniu z centrum IoT do urządzenia. Urządzenie może teraz rozpocząć przesyłanie danych bezpośrednio do centrum IoT.
7. Urządzenie łączy się z centrum IoT.
8. Urządzenie pobiera żądany stan ze swojej bliźniaczej reprezentacji urządzenia w centrum IoT.

## <a name="provisioning-process"></a>Proces aprowizacji
Proces wdrażania urządzenia obejmujący usługę Device Provisioning Service składa się z dwóch odrębnych kroków, które można wykonać niezależnie:

* **Krok produkcji**, w ramach którego urządzenie jest tworzone i przygotowywane w fabryce.
* **Krok konfiguracji chmury**, w ramach którego usługa Device Provisioning Service jest konfigurowana na potrzeby automatycznego aprowizowania.

Oba te kroki bezproblemowo wpisują się w istniejące procesy produkcji i wdrażania. Usługa Device Provisioning Service upraszcza nawet niektóre procesy wdrażania obejmujące wiele ręcznej pracy w celu umieszczenia informacji o połączeniu na urządzeniu.

### <a name="manufacturing-step"></a>Krok produkcji
Ten krok obejmuje wszystko to, co dzieje się na linii produkcyjnej. Role biorące udział w tym kroku to projektant układów scalonych, producent układów scalonych, integrator i/lub końcowy producent urządzenia. Ten krok dotyczy tworzenia samego sprzętu.

Usługa Device Provisioning Service nie wprowadza nowego kroku do procesu produkcji, ale wiąże się z istniejącym krokiem, w którym na urządzeniu jest instalowane oprogramowanie początkowe i (w idealnym przypadku) moduł HSM. W tym kroku, zamiast tworzyć identyfikator urządzenia, urządzenie jest programowane tak, aby zawierało informacje o usłudze aprowizowania, co umożliwia mu wywołanie usługi aprowizowania w celu uzyskania informacji dotyczących połączenia/przypisania do rozwiązania IoT po jego włączeniu.

Ponadto w tym kroku producent dostarcza osobie wdrażającej urządzenia/operatorowi urządzeń informacje o kluczu identyfikacyjnym. Dostarczenie tych informacji może być proste (np. potwierdzenie, że wszystkie urządzenia mają certyfikat X.509 wygenerowany na podstawie certyfikatu podpisywania udostępnionego przez osobę wdrażającą urządzenia/operatora urządzeń) lub skomplikowane (np. wyodrębnienie publicznej części klucza poręczenia modułu TPM z każdego urządzenia TPM). Te usługi są obecnie oferowane przez wielu producentów układów scalonych.

### <a name="cloud-setup-step"></a>Krok konfiguracji chmury
Ten krok dotyczy konfigurowania chmury na potrzeby odpowiedniego aprowizowania automatycznego. Zasadniczo w krok konfiguracji chmury są zaangażowane dwa typy użytkowników: ktoś, kto wie, jak urządzenia mają być wstępnie skonfigurowane (operator urządzeń) oraz ktoś inny, kto wie, jak urządzenia mają być podzielone między centrami IoT (operator rozwiązania).

Należy wykonać jednorazową początkową konfigurację aprowizowania. Zwykle zajmuje się tym operator rozwiązania. Gdy usługa aprowizowania zostanie skonfigurowana, nie trzeba jej modyfikować, chyba że przypadek użycia ulegnie zmianie.

Po skonfigurowaniu usługi na potrzeby automatycznego aprowizowania musi ona zostać przygotowana do rejestrowania urządzeń. Ten krok jest wykonywany przez operatora urządzeń, który zna żądaną konfigurację urządzeń i odpowiada za zapewnienie, że usługa aprowizowania może prawidłowo potwierdzić tożsamość urządzenia, gdy będzie ono wyszukiwać swoje centrum IoT. Operator urządzeń dodaje informacje o kluczu identyfikacyjnym, uzyskane od producenta, do listy rejestracji. Lista rejestracji może być później aktualizowana przez dodawanie nowych wpisów lub aktualizowanie istniejących przy użyciu najnowszych informacji o urządzeniach.

## <a name="registration-and-provisioning"></a>Rejestracja i aprowizowanie
*Aprowizowanie* w zależności od branży, w której ten termin jest używany, może oznaczać coś innego. W kontekście aprowizowania urządzeń IoT w ich rozwiązaniu w chmurze aprowizowanie to proces składający się z dwóch części:

1. Pierwsza część to ustanowienie połączenia początkowego między urządzeniem a rozwiązaniem IoT przez rejestrację urządzenia.
2. Druga część to zastosowanie odpowiedniej konfiguracji do urządzenia na podstawie określonych wymagań rozwiązania, w którym zostało ono zarejestrowane.

Po wykonaniu tych dwóch kroków można powiedzieć, że urządzenie zostało w pełni aprowizowane. Niektóre usługi w chmurze umożliwiają wykonanie tylko pierwszego kroku tego procesu aprowizowania przez zarejestrowanie urządzeń w punkcie końcowym rozwiązania IoT, ale nie zapewniają konfiguracji początkowej. Usługa Device Provisioning Service automatyzuje oba kroki, aby zapewnić bezproblemowe środowisko aprowizowania dla urządzenia.

## <a name="features-of-the-device-provisioning-service"></a>Funkcje usługi Device Provisioning Service
Usługa Device Provisioning Service ma wiele funkcji, dzięki czemu jest idealnym rozwiązaniem w przypadku aprowizowania urządzeń.

* Obsługa **bezpiecznego zaświadczania** dla tożsamości opartych zarówno na standardzie X.509, jak i module TPM.
* **Lista rejestracji** zawierająca pełny rejestr urządzeń/grup urządzeń, które w pewnym momencie mogą zostać zarejestrowane. Lista rejestracji zawiera informacje dotyczące żądanej konfiguracji urządzenia po jego zarejestrowaniu i może być aktualizowana w dowolnym momencie.
* **Wiele zasad alokacji** w celu kontroli sposobu, w jaki usługa Device Provisioning Service przypisuje urządzenia do centrów IoT w celu obsługi scenariuszy.
* **Monitorowanie i rejestrowanie diagnostyczne** w celu zapewnienia poprawnego działania.
* **Obsługa wielu centrów** umożliwia usłudze Device Provisioning Service przypisywanie urządzeń do więcej niż jednego centrum IoT. Usługa Device Provisioning Service może komunikować się z centrami w wielu subskrypcjach platformy Azure.
* **Obsługa wielu regionów** umożliwia usłudze Device Provisioning Service przypisywanie urządzeń do centrów IoT w innych regionach.

Więcej informacji o pojęciach i funkcjach z zakresu aprowizowania urządzeń możesz znaleźć w artykułach na temat [pojęć dotyczących urządzeń](concepts-device.md), [pojęć dotyczących usługi](concepts-service.md) oraz [pojęć dotyczących zabezpieczeń](concepts-security.md).

## <a name="cross-platform-support"></a>Obsługa wielu platform
Usługa Device Provisioning Service, tak jak wszystkie usługi IoT platformy Azure, działa na wielu platformach z różnymi systemami operacyjnymi. Platforma Azure oferuje zestawy SDK typu open source w wielu różnych [językach](https://github.com/Azure/azure-iot-sdks), aby usprawnić łączenie urządzeń i zarządzanie usługą. Usługa Device Provisioning Service obsługuje następujące protokoły łączenia urządzeń:

* HTTPS
* AMQP
* AMQP za pośrednictwem obiektów Web Socket
* MQTT
* MQTT za pośrednictwem obiektów Web Socket

Usługa Device Provisioning Service obsługuje połączenia HTTPS tylko na potrzeby operacji usługi.

## <a name="regions"></a>Regions
Usługa Device Provisioning Service jest dostępna w wielu regionach. Aktualna lista obecnych i nowo ogłoszonych regionów dla wszystkich usług jest dostępna na stronie [Regiony świadczenia usługi Azure](https://azure.microsoft.com/regions/). Dostępność usługi Device Provisioning Service można sprawdzić na stronie [Stan platformy Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> Usługa Device Provisioning Service jest globalna i nie jest powiązana z konkretną lokalizacją. Należy jednak określić region, w którym będą znajdować się metadane skojarzone z Twoim profilem usługi Device Provisioning Service.

## <a name="availability"></a>Dostępność
Obowiązuje umowa dotycząca poziomu usług (SLA, Service Level Agreement) gwarantująca sprawne działanie usługi Device Provisioning Service przez 99,9% czasu. Tutaj możesz [przeczytać umowę SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

## <a name="quotas"></a>Przydziały
W każdej subskrypcji platformy Azure obowiązują domyślne limity przydziału, które mogą mieć wpływ na zakres rozwiązania IoT. Aktualny limit dla każdej subskrypcji wynosi 10 usług Device Provisioning Service na subskrypcję.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Więcej informacji na temat limitów przydziałów znajduje się w artykułach:
* [Azure Subscription Service Limits](../azure-subscription-service-limits.md) (Limity usług subskrypcji platformy Azure)

## <a name="related-azure-components"></a>Powiązane składniki platformy Azure
Usługa Device Provisioning Service automatyzuje aprowizowanie urządzeń w usłudze Azure IoT Hub. Dowiedz się więcej na temat usługi [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Następne kroki
Teraz ogólnie wiesz, na czym polega aprowizowanie urządzeń IoT na platformie Azure. Następnym krokiem jest wypróbowanie kompletnego scenariusza IoT.
> [!div class="nextstepaction"]
> [Konfigurowanie usługi IoT Hub Device Provisioning Service przy użyciu witryny Azure Portal](quick-setup-auto-provision.md)
> [Tworzenie i aprowizowanie urządzenia symulowanego](quick-create-simulated-device.md)
> [Konfigurowanie urządzenia pod kątem aprowizacji](tutorial-set-up-device.md)
