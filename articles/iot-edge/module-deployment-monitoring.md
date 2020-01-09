---
title: Automatyczne wdrażanie do grupy urządzeń — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Umożliwia automatyczne wdrożenia w usłudze Azure IoT Edge Zarządzanie grupami urządzeń na podstawie udostępnionych tagów
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13390de8d3008907a0b55bf3a61c931dfdcd84e6
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552359"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Omówienie automatycznego wdrożenia usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali

Wdrożenia automatyczne i wdrożenie warstwowe ułatwiają zarządzanie i Konfigurowanie modułów na dużej liczbie IoT Edge urządzeń. 

Azure IoT Edge oferuje dwa sposoby konfigurowania modułów do uruchamiania na urządzeniach IoT Edge. Pierwsza metoda polega na wdrożeniu modułów na poszczególnych urządzeniach. Należy utworzyć manifest wdrożenia, a następnie zastosować go do określonego urządzenia według nazwy. Druga metoda polega na automatycznym wdrożeniu modułów na dowolnym zarejestrowanym urządzeniu, które spełnia określone warunki. Należy utworzyć manifest wdrożenia, a następnie określić, które urządzenia odnoszą się do nich w oparciu o [Tagi](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) w bliźniaczych urządzeniach. 

Ten artykuł koncentruje się na konfiguracji i monitorowania etapów do floty urządzeń, zbiorczo określany jako automatycznego wdrożenia usługi IoT Edge. Poniżej przedstawiono ogólne kroki wdrażania: 

1. Operator definiuje wdrożenia, które opisuje zestaw modułów, a także urządzeń docelowych. Każde wdrożenie zawiera manifest wdrożenia, który odzwierciedla te informacje. 
2. Usługa IoT Hub komunikuje się z wszystkie objęte nimi urządzenia, aby je skonfigurować przy użyciu żądanych modułów. 
3. Usługa IoT Hub pobiera stan z urządzenia usługi IoT Edge i udostępnienie ich dla operatora.  Na przykład operator może zobaczyć, kiedy urządzenie brzegowe nie zostało prawidłowo skonfigurowane lub jeśli wystąpi błąd modułu w czasie wykonywania. 
4. W dowolnym momencie nowego urządzenia usługi IoT Edge, które spełniają warunki określania wartości docelowej są skonfigurowane dla wdrożenia. 
 
W tym artykule opisano poszczególne składniki zaangażowane w konfigurowania i monitorowania wdrożenia. Aby uzyskać wskazówki dotyczące tworzenia i aktualizowania wdrożenia, zobacz [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

## <a name="deployment"></a>Wdrażanie

Automatyczne wdrożenie usługi IoT Edge przypisuje usługi IoT Edge obrazy modułu do uruchamiania jako wystąpień w zestawie docelowym urządzenia usługi IoT Edge. To działa, konfigurując manifest wdrożenia usługi IoT Edge zawiera listę modułów za pomocą odpowiednich parametrów inicjowania. Wdrożenie może być przypisane do jednego urządzenia (w oparciu o identyfikator urządzenia) lub do grupy urządzeń (w oparciu o Tagi). Gdy urządzenie IoT Edge odbiera manifest wdrożenia, pobiera i instaluje obrazy kontenerów z odpowiednich repozytoriów kontenerów i odpowiednio konfiguruje je. Po utworzeniu wdrożenia operator może monitorować stan wdrożenia, aby sprawdzić, czy urządzenia są poprawnie skonfigurowane.

Można skonfigurować tylko na urządzeniach usługi IoT Edge z wdrożeniem. Następujące wymagania wstępne muszą być na urządzeniu, zanim może ona odbierać wdrożenia:

* Podstawowego systemu operacyjnego
* System zarządzania kontenera, takich jak Moby lub rozwiązania Docker
* Inicjowanie obsługi środowiska uruchomieniowego usługi IoT Edge 

### <a name="deployment-manifest"></a>Manifest wdrożenia

Manifest wdrożenia jest to dokument JSON, który opisuje modułów, które mają być skonfigurowane na docelowe urządzenia usługi IoT Edge. Zawiera ona metadanych konfiguracji dla wszystkich modułów, w tym moduły wymagany system (w szczególności agenta usługi IoT Edge i Centrum usługi IoT Edge).  

Metadane konfiguracji dla każdego modułu, obejmują: 

* Wersja 
* Typ 
* Stan (na przykład uruchomiona lub zatrzymana) 
* Zasady ponownego uruchamiania 
* Rejestr obrazów oraz kontenerów
* Trasy dla danych wejściowych i wyjściowych 

Jeśli obraz modułu jest przechowywany w prywatnym rejestrze kontenerów, agent usługi IoT Edge przechowuje poświadczenia rejestru. 

### <a name="target-condition"></a>Warunek docelowy

Warunek docelowy jest stale oceniany przez cały okres istnienia wdrożenia. Uwzględniono nowych urządzeń, które spełniają wymagania, a wszystkie istniejące urządzenia, które nie są usuwane. Wdrażanie jest możliwe, jeśli usługa wykrywa wszelkie zmiany stanu docelowego. 

Na przykład masz wdrożenie obejmuje A tags.environment warunek docelowy = "prod". Gdy wdrożenie jest rozpoczynane, istnieją 10 urządzeń w środowisku produkcyjnym. Moduły zostali pomyślnie zainstalowani na tych urządzeniach 10. Stan agenta IoT Edge jest wyświetlany jako 10 łącznej liczby urządzeń, 10 pomyślne odpowiedzi, odpowiedzi 0 i 0 oczekujące odpowiedzi. Teraz możesz dodać pięć więcej urządzeń za pomocą tags.environment = "prod". Usługa wykrywa zmianę i stan agenta IoT Edge staje się 15 łącznej liczby urządzeń, 10 pomyślne odpowiedzi, 0 odpowiedzi i 5 oczekujące odpowiedzi podczas próby wdrożenia do pięciu nowych urządzeń.

Użyj dowolnego warunku typu Boolean na tagów bliźniaczych reprezentacji urządzenia lub identyfikator urządzenia, aby wybrać urządzenia docelowe. Jeśli chcesz użyć warunku przy użyciu tagów, musisz dodać "tags":{} sekcji w bliźniaczej reprezentacji urządzenia, w tym samym poziomie jako właściwości. [Dowiedz się więcej na temat tagów w bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md)

Przykłady warunków docelowej:

* deviceId = "linuxprod1"
* Tags.Environment = "prod"
* Tags.Environment = 'prod' AND tags.location = 'westus'
* Tags.Environment = 'prod' OR tags.location = 'westus'
* Tags.operator = "Jan" i tags.environment = 'prod' nie deviceId = "linuxprod1"

Poniżej przedstawiono niektóre ogranicza podczas konstruowania warunek docelowy:

* W bliźniaczej reprezentacji urządzenia można utworzyć tylko za pomocą znaczników lub deviceId warunek docelowy.
* Podwójny cudzysłów nie jest dozwolone w dowolnej części warunek docelowy. Należy używać cudzysłowów.
* Apostrofy reprezentują wartości warunek docelowy. W związku z tym jeśli jest ona częścią nazwy urządzenia musi znak ucieczki pojedynczy cudzysłów za pomocą innego pojedynczy cudzysłów. Na przykład, aby urządzenie docelowe o nazwie `operator'sDevice`, zapis `deviceId='operator''sDevice'`.
* Cyfry, litery i następujące znaki są dozwolone w wartości warunku docelowego: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorytet

Priorytet określa, czy wdrożenie powinny być stosowane do urządzenia docelowego względem innych wdrożeń. Priorytet wdrożenia jest dodatnią liczbą całkowitą z większą liczbą oznaczający wyższy priorytet. Jeśli urządzenia usługi IoT Edge jest objęty przez więcej niż jedno wdrożenie, dotyczą wdrożenia o najwyższym priorytecie.  Wdrożenia z niższymi priorytetami nie są stosowane ani nie są scalane.  Jeśli urządzenie jest przeznaczone dla co najmniej dwóch wdrożeń o równym priorytecie, stosuje się ostatnio utworzone wdrożenie (określone przez sygnaturę czasową tworzenia).

### <a name="labels"></a>Etykiety 

Etykiety to pary klucz/wartość, których można użyć do filtrowania i grupowania wdrożeń. Wdrożenie może mieć wiele etykiet. Etykiety są opcjonalne i nie mają wpływu na rzeczywistą konfigurację IoT Edge urządzeń. 

### <a name="metrics"></a>Metryki

Domyślnie wszystkie wdrożenia są raportowane według czterech metryk:

* Element **docelowy** przedstawia IoT Edge urządzeń, które pasują do warunku określania wartości docelowej wdrożenia.
* **Dotyczy** IoT Edge to urządzeń, które nie są objęte innymi wdrożeniami o wyższym priorytecie.
* **Raportowanie sukcesu** przedstawia urządzenia IoT Edge, które zostały zgłoszone z powrotem do usługi, że moduły zostały pomyślnie wdrożone. 
* **Raportowanie błędów** przedstawia IoT Edge urządzeń, które zostały zgłoszone z powrotem do usługi, która nie została pomyślnie wdrożona co najmniej jeden moduł. Aby dokładniej zbadać błąd, łączyć się zdalnie z tych urządzeń i wyświetlić pliki dziennika.

Ponadto można definiować własne metryki niestandardowe w celu ułatwienia monitorowania wdrożenia i zarządzania nim. 

Metryki zawierają podsumowanie liczb różnych stanów, które urządzenia mogą raportować z powrotem w wyniku zastosowania konfiguracji wdrożenia. Metryki mogą wykonywać zapytania o [raportowane właściwości w module edgeHub](module-edgeagent-edgehub.md#edgehub-reported-properties), takie jak ostatni żądany stan lub czas ostatniego połączenia. Przykład: 

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Dodawanie własnych metryk jest opcjonalne i nie ma wpływu na rzeczywistą konfigurację IoT Edge urządzeń. 

## <a name="layered-deployment"></a>Wdrożenie warstwowe

Wdrożenia warstwowe to automatyczne wdrożenia, które można łączyć ze sobą, aby zmniejszyć liczbę unikatowych wdrożeń, które należy utworzyć. Wdrożenia warstwowe są przydatne w scenariuszach, w których te same moduły są ponownie używane w różnych kombinacjach w wielu wdrożeniach automatycznych. 

Wdrożenia warstwowe mają takie same składniki podstawowe jak dowolne wdrożenie automatyczne. Są one urządzeniami docelowymi w oparciu o Tagi w urządzeniu bliźniaczych reprezentacji i oferują te same funkcje wokół etykiet, metryk i raportów o stanie. Wdrożenia warstwowe mają przypisane priorytety, ale zamiast korzystać z priorytetu w celu ustalenia, które wdrożenie jest stosowane do urządzenia, priorytet określa, jak wiele wdrożeń jest uporządkowanych na urządzeniu. Na przykład jeśli dwa wdrożenia warstwowe mają moduł lub trasę o tej samej nazwie, wdrożenie warstwowe o wyższym priorytecie zostanie zastosowane, gdy dolny priorytet zostanie zastąpiony. 

Moduły uruchomieniowe systemu, edgeAgent i edgeHub nie są skonfigurowane jako część wdrożenia warstwowego. Każde urządzenie IoT Edge objęte wdrożeniem warstwowym wymaga standardowego wdrożenia automatycznego stosowanego do niego w pierwszej kolejności, aby zapewnić bazę, na podstawie której można dodać wdrożenia warstwowe. 

Urządzenie IoT Edge może zastosować jedno i tylko jedno standardowe wdrożenie automatyczne, ale może stosować wiele wdrożeń automatycznych z warstwami. Wszystkie wdrożenia warstwowe ukierunkowane na urządzenie muszą mieć wyższy priorytet niż wdrożenie automatyczne dla tego urządzenia. 

Rozważmy na przykład następujący scenariusz firmy, która zarządza budynkami. Opracowano IoT Edge modułów do zbierania danych z kamer zabezpieczeń, czujników ruchu i wind. Jednak nie wszystkie budynki mogą korzystać z wszystkich trzech modułów. W przypadku standardowych wdrożeń automatycznych firma musi utworzyć poszczególne wdrożenia dla wszystkich kombinacji modułów, których potrzebują. 

![Standardowe wdrożenia automatyczne muszą obsługiwać każdą kombinację modułów](./media/module-deployment-monitoring/standard-deployment.png)

Jednak po przełączeniu do warstwowych wdrożeń automatycznych można utworzyć te same kombinacje modułów dla budynków z mniejszą liczbą wdrożeń do zarządzania. Każdy moduł ma własne wdrożenie warstwowe, a Tagi urządzeń określają, które moduły zostaną dodane do każdego budynku. 

![Automatyczne wdrażanie warstwowe upraszcza scenariusze, w których te same moduły są łączone na różne sposoby](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfiguracja sznurka modułu

Podczas pracy z wdrożeniami warstwowymi można celowo lub w inny sposób mieć dwa wdrożenia z tym samym modułem przeznaczonym dla urządzenia. W takich przypadkach można zdecydować, czy wdrożenie o wyższym priorytecie powinno zastąpić dwuosiowy moduł, czy dołączyć do niego. Na przykład może istnieć wdrożenie, które stosuje ten sam moduł do 100 różnych urządzeń. Jednak 10 z tych urządzeń jest w bezpiecznym zakresie i potrzebna jest dodatkowa konfiguracja w celu komunikowania się przez serwery proxy. Przy użyciu wdrożenia warstwowego można dodać właściwości wieloosiowe modułu, które umożliwią tym 10 urządzeniom bezpieczne komunikowanie się bez zastępowania istniejących informacji o postawce modułu z podstawowego wdrożenia. 

W manifeście wdrożenia można dołączyć odpowiednie właściwości sznurka modułu. W przypadku wdrożenia standardowego należy dodać właściwości w sekcji **właściwości. wymagana** sekcja sznurka modułu, w przypadku wdrożenia warstwowego można zadeklarować nowy podzbiór żądanych właściwości. 

Na przykład w standardowym wdrożeniu można dodać moduł symulowanego czujnika temperatury z następującymi pożądanymi właściwościami, które informują go o konieczności wysyłania danych w 5-sekundowych odstępach czasu:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

W przypadku wdrożenia warstwowego przeznaczonego na te same urządzenia lub podzestawu tych samych urządzeń warto dodać dodatkową właściwość, która informuje symulowany czujnik o wysłanie komunikatów 1000, a następnie zatrzymanie. Nie chcesz zastąpić istniejących właściwości, więc Utwórz nową sekcję w ramach żądanych właściwości o nazwie `layeredProperties`, która zawiera nową właściwość:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Urządzenie, które ma zastosowane oba wdrożenia, będzie odzwierciedlać następujące dane w postaci sznurka modułowego dla czujnika symulowanej temperatury: 

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Jeśli ustawisz pole `properties.desired`u w ramach wielowarstwowego wdrożenia, spowoduje to zastąpienie żądanych właściwości tego modułu w ramach wdrożeń o niższym priorytecie. 

## <a name="phased-rollout"></a>Etapowego wdrażania 

Etapowego wdrażania jest cały proces, według której operator wdraża zmiany rozszerzanie zbiór urządzeń usługi IoT Edge. Celem jest, aby wprowadzić zmiany, stopniowo w celu zmniejszenia ryzyka dokonywania szerokiej skali istotne zmiany w. Automatyczne wdrożenia pomagają zarządzać stopniowanymi wdrożeniami w ramach floty IoT Edge urządzeń. 

Etapowe wdrażanie jest wykonywane w następujących faz i kroki: 

1. Ustanowienie środowiska testowego z urządzenia usługi IoT Edge inicjowanie ich obsługi administracyjnej i ustawiając tagu bliźniaczej reprezentacji urządzenia, takie jak `tag.environment='test'`. Środowisko testowe powinno dublować środowisko produkcyjne, w którym wdrożenie będzie ostatecznie docelowe. 
2. Utwórz wdrożenie w tym żądanych modułów i konfiguracji. Warunek docelowy powinien dotyczyć testu środowisko urządzenia usługi IoT Edge.   
3. Sprawdź poprawność nowej konfiguracji modułu w środowisku testowym.
4. Aktualizowanie wdrożenia obejmujący podzbiór urządzenia usługi IoT Edge w środowisku produkcyjnym, dodając nowy tag do stanu, określania wartości docelowej. Upewnij się również, że priorytet dla wdrożenia jest wyższy niż inne wdrożenia mają obecnie zastosowania żadne do tych urządzeń 
5. Sprawdź, czy wdrożenie zakończyło się pomyślnie na docelowych urządzeniach IoT, wyświetlając stan wdrożenia.
6. Aktualizowanie wdrożenia i nakieruj wszystkie pozostałe urządzenia usługi IoT Edge w środowisku produkcyjnym.

## <a name="rollback"></a>Wycofuj

Wdrożeń można wycofać, jeśli otrzymujesz błędy lub błędy konfiguracji. Ze względu na to, że wdrożenie definiuje bezwzględną konfigurację modułu dla urządzenia IoT Edge, dodatkowe wdrożenie musi być również celem tego samego urządzenia o niższym priorytecie, nawet jeśli celem jest usunięcie wszystkich modułów.  

Usunięcie wdrożenia nie powoduje usunięcia modułów z urządzeń kierowanych. Musi istnieć inne wdrożenie, które definiuje nową konfigurację dla urządzeń, nawet jeśli jest to puste wdrożenie. 

Wycofywanie zmian należy wykonać w następującej kolejności: 

1. Upewnij się, że drugie wdrożenie jest wskazywane na tym samym zestawie urządzeń. Jeśli celem wycofywanie jest aby usunąć wszystkie moduły, drugie wdrożenie nie może zawierać żadnych modułów. 
2. Modyfikowanie lub usuwanie wyrażenie warunku docelowego wdrożenia, którą chcesz wdrożyć tak, aby urządzenia nie spełniają warunek określania wartości docelowej.
3. Sprawdź, czy wycofywanie zakończyło się pomyślnie, wyświetlając stan wdrożenia.
   * Wdrożenie zwrotnego wycofana już nie powinien być wyświetlony stan urządzeń, które zostały wycofane.
   * Drugie wdrożenie powinny znajdować się teraz stan wdrożenia dla urządzeń, które zostały wycofane.


## <a name="next-steps"></a>Następne kroki

* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).
* Dowiedz się więcej o innych pojęć usługi IoT Edge, takich jak [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i [moduły usługi IoT Edge](iot-edge-modules.md).

