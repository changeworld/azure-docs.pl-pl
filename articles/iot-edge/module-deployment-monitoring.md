---
title: Automatyczne wdrażanie do grupy urządzeń — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Umożliwia automatyczne wdrożenia w usłudze Azure IoT Edge Zarządzanie grupami urządzeń na podstawie udostępnionych tagów
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb45f2b929c08ce77c83af450726a00dd6af458e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456729"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Omówienie automatycznego wdrożenia usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali

Urządzenia Azure IoT Edge są zgodne z [cyklem życia urządzenia](../iot-hub/iot-hub-device-management-overview.md) podobnym do innych typów urządzeń IoT:

1. Zainicjuj obsługę nowych urządzeń IoT Edge, tworząc obraz urządzenia w systemie operacyjnym i instalując [środowisko uruchomieniowe IoT Edge](iot-edge-runtime.md).
2. Skonfiguruj urządzenia do uruchamiania [modułów IoT Edge](iot-edge-modules.md), a następnie Monitoruj ich kondycję. 
3. Ponadto wycofywanie urządzeń, gdy są one zastąpione lub stają się przestarzałe.  

Azure IoT Edge oferuje dwa sposoby konfigurowania modułów do uruchamiania na urządzeniach IoT Edge: jeden do projektowania i szybkiej iteracji na jednym urządzeniu (użyto tej metody w [samouczkach](tutorial-deploy-function.md)Azure IoT Edge), a drugi do zarządzania dużymi flotami IoT Edge urządzeń. Obie te metody są dostępne, w witrynie Azure portal lub programowo. W przypadku grup docelowych lub dużej liczby urządzeń można określić, które urządzenia mają być wdrażane przy użyciu [tagów](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) w bliźniaczych urządzeniach. Poniższe kroki porozmawiać na temat wdrożenia w grupie urządzeń stanu Waszyngton identyfikowane za pomocą właściwości tagów. 

Ten artykuł koncentruje się na konfiguracji i monitorowania etapów do floty urządzeń, zbiorczo określany jako automatycznego wdrożenia usługi IoT Edge. Poniżej przedstawiono ogólne kroki wdrażania: 

1. Operator definiuje wdrożenia, które opisuje zestaw modułów, a także urządzeń docelowych. Każde wdrożenie zawiera manifest wdrożenia, który odzwierciedla te informacje. 
2. Usługa IoT Hub komunikuje się z wszystkie objęte nimi urządzenia, aby je skonfigurować przy użyciu żądanych modułów. 
3. Usługa IoT Hub pobiera stan z urządzenia usługi IoT Edge i udostępnienie ich dla operatora.  Na przykład operator może zobaczyć, kiedy urządzenie brzegowe nie zostało prawidłowo skonfigurowane lub jeśli wystąpi błąd modułu w czasie wykonywania. 
4. W dowolnym momencie nowego urządzenia usługi IoT Edge, które spełniają warunki określania wartości docelowej są skonfigurowane dla wdrożenia. Na przykład wdrożenie, które jest przeznaczone dla wszystkich urządzeń IoT Edge w stanie Waszyngton, automatycznie konfiguruje nowe urządzenie IoT Edge, gdy zostanie ono wdrożone i dodane do grupy urządzeń stanu Waszyngton. 
 
W tym artykule opisano poszczególne składniki zaangażowane w konfigurowania i monitorowania wdrożenia. Aby zapoznać się z przewodnikiem dotyczącym tworzenia i aktualizowania wdrożenia, zobacz [wdrażanie i monitorowanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-monitor.md).

## <a name="deployment"></a>Wdrożenie

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

Użyj dowolnego warunku typu Boolean na tagów bliźniaczych reprezentacji urządzenia lub identyfikator urządzenia, aby wybrać urządzenia docelowe. Jeśli chcesz użyć warunku ze znacznikami, musisz dodać "Tagi":{} sekcji na sznurze urządzenia pod tym samym poziomie co właściwości. [Dowiedz się więcej o tagach w bliźniaczych urządzeniach](../iot-hub/iot-hub-devguide-device-twins.md)

Przykłady warunków docelowej:

* deviceId = "linuxprod1"
* Tags.Environment = "prod"
* Tags.Environment = 'prod' AND tags.location = 'westus'
* Tags.Environment = 'prod' OR tags.location = 'westus'
* Tags.operator = "Jan" i tags.environment = 'prod' nie deviceId = "linuxprod1"

Poniżej przedstawiono niektóre ogranicza podczas konstruowania warunek docelowy:

* W bliźniaczej reprezentacji urządzenia można utworzyć tylko za pomocą znaczników lub deviceId warunek docelowy.
* Podwójny cudzysłów nie jest dozwolone w dowolnej części warunek docelowy. Należy używać cudzysłowów.
* Apostrofy reprezentują wartości warunek docelowy. W związku z tym jeśli jest ona częścią nazwy urządzenia musi znak ucieczki pojedynczy cudzysłów za pomocą innego pojedynczy cudzysłów. Na przykład aby określić miejsce docelowe urządzenia o nazwie `operator'sDevice`, `deviceId='operator''sDevice'`zapisywania.
* W wartościach warunku docelowego są dozwolone liczby, litery i następujące znaki: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorytet

Priorytet określa, czy wdrożenie powinny być stosowane do urządzenia docelowego względem innych wdrożeń. Priorytet wdrożenia jest dodatnią liczbą całkowitą z większą liczbą oznaczający wyższy priorytet. Jeśli urządzenia usługi IoT Edge jest objęty przez więcej niż jedno wdrożenie, dotyczą wdrożenia o najwyższym priorytecie.  Wdrożenia z niższymi priorytetami nie są stosowane ani nie są scalane.  Jeśli urządzenie jest przeznaczone dla co najmniej dwóch wdrożeń o równym priorytecie, stosuje się ostatnio utworzone wdrożenie (określone przez sygnaturę czasową tworzenia).

### <a name="labels"></a>Etykiety 

Etykiety są pary klucz/wartość ciągu, które można użyć do filtrowania i grupy wdrożenia. Wdrożenie może mieć wiele etykiet. Etykiety są opcjonalne i wykonaj bez wpływu na rzeczywistą konfigurację urządzenia usługi IoT Edge. 

### <a name="deployment-status"></a>Stan wdrożenia

Wdrożenia można monitorować w taki sposób, aby określić, czy pomyślnie zastosowane dla wszystkich docelowych urządzeń usługi IoT Edge.  Odpowiednie urządzenie brzegowe zostanie wyświetlone w co najmniej jednej z następujących kategorii stanu: 

* **Element docelowy** przedstawia urządzenia IoT Edge, które pasują do warunku określania wartości docelowej wdrożenia.
* Wartość **rzeczywiste** przedstawia IoT Edge urządzeń, które nie są objęte innym wdrożeniem o wyższym priorytecie.
* W **dobrej kondycji** przedstawiono IoT Edge urządzeń, które zostały zgłoszone z powrotem do usługi, pomyślnie wdrożono moduły. 
* W **złej kondycji** przedstawiono, IoT Edge urządzenia zostały zgłoszone z powrotem do usługi, która nie została pomyślnie wdrożona. Aby dokładniej zbadać błąd, łączyć się zdalnie z tych urządzeń i wyświetlić pliki dziennika.
* **Nieznany** pokazuje IoT Edge urządzeń, które nie zgłosiły żadnych Stanów związanych z tym wdrożeniem. Aby badać, Wyświetl usługi informacji i plików dziennika.

## <a name="phased-rollout"></a>Etapowego wdrażania 

Etapowego wdrażania jest cały proces, według której operator wdraża zmiany rozszerzanie zbiór urządzeń usługi IoT Edge. Celem jest, aby wprowadzić zmiany, stopniowo w celu zmniejszenia ryzyka dokonywania szerokiej skali istotne zmiany w.  

Etapowe wdrażanie jest wykonywane w następujących faz i kroki: 

1. Ustanów środowisko testowe IoT Edge urządzeń przez zainicjowanie ich i ustawienie znacznika sznurka urządzenia, takiego jak `tag.environment='test'`. Środowisko testowe powinno dublować środowisko produkcyjne, w którym wdrożenie będzie ostatecznie docelowe. 
2. Utwórz wdrożenie w tym żądanych modułów i konfiguracji. Warunek docelowy powinien dotyczyć testu środowisko urządzenia usługi IoT Edge.   
3. Sprawdź poprawność nowej konfiguracji modułu w środowisku testowym.
4. Aktualizowanie wdrożenia obejmujący podzbiór urządzenia usługi IoT Edge w środowisku produkcyjnym, dodając nowy tag do stanu, określania wartości docelowej. Upewnij się również, że priorytet dla wdrożenia jest wyższy niż inne wdrożenia mają obecnie zastosowania żadne do tych urządzeń 
5. Sprawdź, czy wdrożenie zakończyło się pomyślnie na docelowych urządzeniach IoT, wyświetlając stan wdrożenia.
6. Aktualizowanie wdrożenia i nakieruj wszystkie pozostałe urządzenia usługi IoT Edge w środowisku produkcyjnym.

## <a name="rollback"></a>Wycofywanie

Wdrożeń można wycofać, jeśli otrzymujesz błędy lub błędy konfiguracji.  Ze względu na to, że wdrożenie definiuje bezwzględną konfigurację modułu dla urządzenia IoT Edge, dodatkowe wdrożenie musi być również celem tego samego urządzenia o niższym priorytecie, nawet jeśli celem jest usunięcie wszystkich modułów.  

Wycofywanie zmian należy wykonać w następującej kolejności: 

1. Upewnij się, że drugie wdrożenie jest wskazywane na tym samym zestawie urządzeń. Jeśli celem wycofywanie jest aby usunąć wszystkie moduły, drugie wdrożenie nie może zawierać żadnych modułów. 
2. Modyfikowanie lub usuwanie wyrażenie warunku docelowego wdrożenia, którą chcesz wdrożyć tak, aby urządzenia nie spełniają warunek określania wartości docelowej.
3. Sprawdź, czy wycofywanie zakończyło się pomyślnie, wyświetlając stan wdrożenia.
   * Wdrożenie zwrotnego wycofana już nie powinien być wyświetlony stan urządzeń, które zostały wycofane.
   * Drugie wdrożenie powinny znajdować się teraz stan wdrożenia dla urządzeń, które zostały wycofane.


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z instrukcjami tworzenia, aktualizowania lub usuwania wdrożenia w sekcji [wdrażanie i monitorowanie modułów IoT Edge](how-to-deploy-monitor.md)w odpowiedniej skali.
* Dowiedz się więcej na temat innych IoT Edge pojęć, takich jak [środowisko uruchomieniowe IoT Edge](iot-edge-runtime.md) i [moduły IoT Edge](iot-edge-modules.md).

