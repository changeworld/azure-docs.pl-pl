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
ms.custom: seodec18
ms.openlocfilehash: 376ee74732daf526b31129fa8c93cbaa32350eae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318210"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Omówienie automatycznego wdrożenia usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali

Postępuj zgodnie z urządzenia w usłudze Azure IoT Edge [cykl życia urządzenia](../iot-hub/iot-hub-device-management-overview.md) przypomina na inne typy urządzeń IoT:

1. Aprowizacja nowych urządzeń usługi IoT Edge imaging urządzenia z systemem operacyjnym i instalując [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md).
2. Konfigurowanie urządzeń, aby uruchamiać [moduły usługi IoT Edge](iot-edge-modules.md), a następnie monitorować ich kondycję. 
3. Ponadto wycofywanie urządzeń, gdy są one zastąpione lub stają się przestarzałe.  

Usługa Azure IoT Edge zapewnia dwa sposoby konfigurowania modułów do uruchamiania na urządzeniach usługi IoT Edge: jeden dla rozwoju i szybkie iteracje na jednym urządzeniu (Ta metoda jest używana w usłudze Azure IoT Edge [samouczki](tutorial-deploy-function.md)), i jedną do zarządzania dużą flot programu Urządzenia usługi IoT Edge. Obie te metody są dostępne, w witrynie Azure portal lub programowo. Dla docelowych grup lub dużą liczbę urządzeń, należy określić urządzeń, które chcesz wdrożyć moduły przy użyciu [tagi](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) w bliźniaczej reprezentacji urządzenia. Poniższe kroki porozmawiać na temat wdrożenia w grupie urządzeń stanu Waszyngton identyfikowane za pomocą właściwości tagów. 

Ten artykuł koncentruje się na konfiguracji i monitorowania etapów do floty urządzeń, zbiorczo określany jako automatycznego wdrożenia usługi IoT Edge. Ogólne kroki wdrożenia są następujące: 

1. Operator definiuje wdrożenia, które opisuje zestaw modułów, a także urządzeń docelowych. Każde wdrożenie ma manifestu wdrażania, który odzwierciedla te informacje. 
2. Usługa IoT Hub komunikuje się z wszystkie objęte nimi urządzenia, aby je skonfigurować przy użyciu żądanych modułów. 
3. Usługa IoT Hub pobiera stan z urządzenia usługi IoT Edge i udostępnienie ich dla operatora.  Na przykład operator widoczne, gdy urządzenia usługi Edge nie została skonfigurowana pomyślnie lub jeśli moduł zakończy się niepowodzeniem podczas wykonywania. 
4. W dowolnym momencie nowego urządzenia usługi IoT Edge, które spełniają warunki określania wartości docelowej są skonfigurowane dla wdrożenia. Na przykład wdrożenia, który jest przeznaczony dla wszystkich urządzeń usługi IoT Edge w stanie Waszyngton, automatycznie konfiguruje nowe urządzenie usługi IoT Edge po jego są aprowizowane i dodane do grupy urządzeń w stanie Waszyngton. 
 
W tym artykule opisano poszczególne składniki zaangażowane w konfigurowania i monitorowania wdrożenia. Aby uzyskać wskazówki dotyczące tworzenia i aktualizowania wdrożenia, zobacz [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

## <a name="deployment"></a>Wdrożenie

Automatyczne wdrożenie usługi IoT Edge przypisuje usługi IoT Edge obrazy modułu do uruchamiania jako wystąpień w zestawie docelowym urządzenia usługi IoT Edge. To działa, konfigurując manifest wdrożenia usługi IoT Edge zawiera listę modułów za pomocą odpowiednich parametrów inicjowania. Wdrożenia można przypisać do jednego urządzenia (oparte na identyfikator urządzenia) lub do grupy urządzeń (na podstawie tagów). Po urządzenia usługi IoT Edge otrzymuje manifestu wdrażania, pobiera i instaluje obrazy kontenerów z repozytoriów odpowiednich kontenerów i konfiguruje je odpowiednio. Po utworzeniu wdrożenia operator można monitorować stan wdrożenia, aby zobaczyć, czy urządzenia docelowe są poprawnie skonfigurowane.

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

Warunek docelowy jest stale oceniany w okresie istnienia wdrożenia. Uwzględniono nowych urządzeń, które spełniają wymagania, a wszystkie istniejące urządzenia, które nie są usuwane. Wdrażanie jest możliwe, jeśli usługa wykrywa wszelkie zmiany stanu docelowego. 

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

Priorytet określa, czy wdrożenie powinny być stosowane do urządzenia docelowego względem innych wdrożeń. Priorytet wdrożenia jest dodatnią liczbą całkowitą z większą liczbą oznaczający wyższy priorytet. Jeśli urządzenia usługi IoT Edge jest objęty przez więcej niż jedno wdrożenie, dotyczą wdrożenia o najwyższym priorytecie.  Wdrożenia o niższych priorytetach nie są stosowane, nie są one scalane.  Jeśli urządzenie zostanie wybrane co najmniej dwóch wdrożeń o taki sam priorytet, stosuje się ostatnio utworzonego wdrożenia (określany przez sygnaturę czasową utworzenia).

### <a name="labels"></a>Etykiety 

Etykiety są pary klucz/wartość ciągu, które można użyć do filtrowania i grupy wdrożenia. Wdrożenie może mieć wielu etykiet. Etykiety są opcjonalne i wykonaj bez wpływu na rzeczywistą konfigurację urządzenia usługi IoT Edge. 

### <a name="deployment-status"></a>Stan wdrożenia

Wdrożenia można monitorować w taki sposób, aby określić, czy pomyślnie zastosowane dla wszystkich docelowych urządzeń usługi IoT Edge.  Docelowe urządzenia Edge pojawi się w co najmniej jednej z następujących kategorii stanu: 

* **Docelowy** pokazuje usługi IoT Edge urządzenia pasujące wdrożenia przeznaczone dla warunku.
* **Rzeczywiste** wyświetlane docelowej usługi IoT Edge urządzenia, które nie są objęci inne wdrożenie o wyższym priorytecie.
* **Dobra** pokazuje usługi IoT Edge urządzenia, które zgłosiły powrót do usługi, moduły zostały pomyślnie wdrożone. 
* **Zła** pokazuje usługi IoT Edge urządzenia mają odsyłane do usługi, że jedna lub moduły nie zostały pomyślnie wdrożone. Aby dokładniej zbadać błąd, łączyć się zdalnie z tych urządzeń i wyświetlić pliki dziennika.
* **Nieznany** wyświetlane usługi IoT Edge urządzenia, które nie zgłosiły żadnych stanu dotyczące tego wdrożenia. Aby badać, Wyświetl usługi informacji i plików dziennika.

## <a name="phased-rollout"></a>Etapowego wdrażania 

Etapowego wdrażania jest cały proces, według której operator wdraża zmiany rozszerzanie zbiór urządzeń usługi IoT Edge. Celem jest, aby wprowadzić zmiany, stopniowo w celu zmniejszenia ryzyka dokonywania szerokiej skali istotne zmiany w.  

Etapowe wdrażanie jest wykonywane w następujących faz i kroki: 

1. Ustanowienie środowiska testowego z urządzenia usługi IoT Edge inicjowanie ich obsługi administracyjnej i ustawiając tagu bliźniaczej reprezentacji urządzenia, takie jak `tag.environment='test'`. Środowisko testowe powinny odzwierciedlać środowisko produkcyjne, po pewnym czasie przeznaczony dla wdrożenia. 
2. Utwórz wdrożenie w tym żądanych modułów i konfiguracji. Warunek docelowy powinien dotyczyć testu środowisko urządzenia usługi IoT Edge.   
3. Sprawdź poprawność nowej konfiguracji modułu w środowisku testowym.
4. Aktualizowanie wdrożenia obejmujący podzbiór urządzenia usługi IoT Edge w środowisku produkcyjnym, dodając nowy tag do stanu, określania wartości docelowej. Upewnij się również, że priorytet dla wdrożenia jest wyższy niż inne wdrożenia mają obecnie zastosowania żadne do tych urządzeń 
5. Sprawdź, czy wdrożenie zakończyło się pomyślnie na docelowych urządzeniach IoT, wyświetlając stan wdrożenia.
6. Aktualizowanie wdrożenia i nakieruj wszystkie pozostałe urządzenia usługi IoT Edge w środowisku produkcyjnym.

## <a name="rollback"></a>Wycofywanie

Wdrożeń można wycofać, jeśli otrzymujesz błędy lub błędy konfiguracji.  Ponieważ wdrożenie definiuje konfigurację modułu bezwzględne dla urządzenia usługi IoT Edge, wdrożenia dodatkowych muszą również zostać objęci tym samym urządzeniu z niższym priorytetem, nawet jeśli celem jest, aby usunąć wszystkie moduły.  

Wycofywanie zmian należy wykonać w następującej kolejności: 

1. Upewnij się, że drugie wdrożenie jest wskazywane na tym samym zestawie urządzeń. Jeśli celem wycofywanie jest aby usunąć wszystkie moduły, drugie wdrożenie nie może zawierać żadnych modułów. 
2. Modyfikowanie lub usuwanie wyrażenie warunku docelowego wdrożenia, którą chcesz wdrożyć tak, aby urządzenia nie spełniają warunek określania wartości docelowej.
3. Sprawdź, czy wycofywanie zakończyło się pomyślnie, wyświetlając stan wdrożenia.
   * Wdrożenie zwrotnego wycofana już nie powinien być wyświetlony stan urządzeń, które zostały wycofane.
   * Drugie wdrożenie powinny znajdować się teraz stan wdrożenia dla urządzeń, które zostały wycofane.


## <a name="next-steps"></a>Kolejne kroki

* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).
* Dowiedz się więcej o innych pojęć usługi IoT Edge, takich jak [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i [moduły usługi IoT Edge](iot-edge-modules.md).

