---
title: Automatyczne wdrażanie dla grup urządzeń — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Automatyczne wdrożenia w usłudze Azure IoT Edge do zarządzania grupami urządzeń na podstawie tagów udostępnionych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aaac6100ba980301ff3e85a3ac3959bfee89b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76895968"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Opis automatycznych wdrożeń usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę

Automatyczne wdrożenia i wdrażanie warstwowe ułatwiają zarządzanie i konfigurowanie modułów na dużej liczbie urządzeń usługi IoT Edge.

Usługa Azure IoT Edge udostępnia dwa sposoby konfigurowania modułów do uruchamiania na urządzeniach usługi IoT Edge. Pierwszą metodą jest wdrażanie modułów na podstawie na urządzenie. Utwórz manifest wdrożenia, a następnie zastosuj go do określonego urządzenia według nazwy. Drugą metodą jest automatyczne wdrażanie modułów na dowolnym zarejestrowanym urządzeniu, które spełnia zestaw zdefiniowanych warunków. Utwórz manifest wdrożenia, a następnie zdefiniuj urządzenia, których dotyczy na podstawie znaczników w bliźniaczej [reprezentacji](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) urządzenia.

W tym artykule skupiono się na konfigurowaniu i monitorowaniu flot urządzeń, zwanych łącznie *automatycznymi wdrożeniami usługi IoT Edge.*Podstawowe kroki wdrażania są następujące:

1. Operator definiuje wdrożenie, które opisuje zestaw modułów i urządzeń docelowych.Każde wdrożenie ma manifest wdrożenia, który odzwierciedla te informacje.
2. Usługa Centrum IoT komunikuje się ze wszystkimi urządzeniami docelowymi, aby skonfigurować je z zadeklarowanych modułów.
3. Usługa Usługi IoT Hub pobiera stan z urządzeń usługi IoT Edge i udostępnia je operatorowi.Na przykład operator może zobaczyć, gdy urządzenie Edge nie jest pomyślnie skonfigurowane lub jeśli moduł ulegnie awarii w czasie wykonywania.
4. W dowolnym momencie nowe urządzenia usługi IoT Edge, które spełniają warunki kierowania są konfigurowane dla wdrożenia.

W tym artykule opisano każdy składnik zaangażowany w konfigurowanie i monitorowanie wdrożenia. Aby zapoznać się z instruktażem tworzenia i aktualizowania wdrożenia, zobacz [Wdrażanie i monitorowanie modułów usługi IoT Edge na dużą skalę.](how-to-deploy-monitor.md)

## <a name="deployment"></a>wdrażania

Automatyczne wdrożenie usługi IoT Edge przypisuje obrazy modułów usługi IoT Edge do uruchamiania jako wystąpienia na docelowym zestawie urządzeń usługi IoT Edge. Działa przez skonfigurowanie manifestu wdrażania usługi IoT Edge w celu uwzględnienia listy modułów z odpowiednimi parametrami inicjowania.Wdrożenie można przypisać do jednego urządzenia (na podstawie identyfikatora urządzenia) lub do grupy urządzeń (na podstawie tagów).Gdy urządzenie usługi IoT Edge odbiera manifest wdrożenia, pobiera i instaluje obrazy kontenerów z odpowiednich repozytoriów kontenerów i odpowiednio je konfiguruje.Po utworzeniu wdrożenia operator może monitorować stan wdrożenia, aby sprawdzić, czy urządzenia docelowe są poprawnie skonfigurowane.

Tylko urządzenia usługi IoT Edge można skonfigurować za pomocą wdrożenia. Przed odebraniem wdrożenia należy na urządzeniu znajdować się następujące wymagania wstępne:

* Podstawowy system operacyjny
* System zarządzania kontenerami, taki jak Moby lub Docker
* Inicjowanie obsługi administracyjnej środowiska wykonawczego IoT Edge

### <a name="deployment-manifest"></a>Manifest wdrożenia

Manifest wdrażania to dokument JSON, który opisuje moduły, które mają być skonfigurowane na docelowych urządzeniach usługi IoT Edge. Zawiera metadane konfiguracji dla wszystkich modułów, w tym wymaganych modułów systemowych (w szczególności agenta usługi IoT Edge i centrum usługi IoT Edge).  

Metadane konfiguracji dla każdego modułu obejmują:

* Wersja
* Typ
* Stan (na przykład uruchamianie lub zatrzymywane)
* Zasady ponownego uruchamiania
* Rejestr obrazów i kontenerów
* Trasy wprowadzania i wyprowadzania danych

Jeśli obraz modułu jest przechowywany w rejestrze kontenerów prywatnych, agent usługi IoT Edge przechowuje poświadczenia rejestru.

### <a name="target-condition"></a>Warunek docelowy

Warunek docelowy jest stale oceniane przez cały okres istnienia wdrożenia. Wszystkie nowe urządzenia, które spełniają wymagania są uwzględniane, a wszystkie istniejące urządzenia, które już nie są usuwane. Wdrożenie jest ponownie uaktywniane, jeśli usługa wykryje zmiany warunku docelowego.

Na przykład masz wdrożenie z docelowym tags.environment warunku = "prod". Po rozpoczęciu wdrażania istnieje 10 urządzeń produkcyjnych. Moduły są pomyślnie zainstalowane w tych 10 urządzeniach. Stan agenta usługi IoT Edge zawiera 10 urządzeń, 10 pomyślnych odpowiedzi, 0 odpowiedzi błędów i 0 oczekujących odpowiedzi. Teraz dodajesz jeszcze pięć urządzeń z tags.environment = 'prod'. Usługa wykrywa zmiany i stan agenta usługi IoT Edge staje się 15 urządzeń ogółem, 10 pomyślnych odpowiedzi, 0 odpowiedzi błędów i 5 oczekujących odpowiedzi podczas wdrażania na pięciu nowych urządzeniach.

Użyj dowolnego warunku logicznego na bliźniaczych tagach urządzenia, właściwości zgłoszonych bliźniaczej reprezentacji urządzenia lub identyfikatora urządzenia, aby wybrać urządzenia docelowe. Jeśli chcesz użyć warunku z tagami, musisz dodać{} sekcję "tagi": w bliźniaczej reprezentacji urządzenia na tym samym poziomie co właściwości. [Dowiedz się więcej o tagach w bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md)

Przykłady warunków docelowych:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' i tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'
* properties.reported.devicemodel = '4000x'

Należy wziąć pod uwagę te ograniczenia podczas konstruowania warunku docelowego:

* W bliźniaczej reprezentacji urządzenia można utworzyć warunek docelowy tylko przy użyciu tagów, zgłoszonych właściwości lub identyfikatora urządzenia.
* Cudzysłowy nie są dozwolone w żadnej części warunku docelowego. Użyj pojedynczych cudzysłowów.
* Pojedyncze cudzysłowy reprezentują wartości warunku docelowego. W związku z tym należy uciec z pojedynczego cudzysłowu z innym pojedynczym cytatem, jeśli jest to część nazwy urządzenia. Na przykład, aby kierować na urządzenie o nazwie `operator'sDevice`, napisz `deviceId='operator''sDevice'`.
* Liczby, litery i następujące znaki są dozwolone w `-:.+%_#*?!(),=@;$`wartościach warunku docelowego: .

### <a name="priority"></a>Priorytet

Priorytet określa, czy wdrożenie powinno być stosowane do urządzenia docelowego w stosunku do innych wdrożeń. Priorytet wdrożenia jest dodatnią liczbą całkowitą, z większą liczbą oznaczającą wyższy priorytet. Jeśli urządzenie usługi IoT Edge jest kierowane przez więcej niż jedno wdrożenie, stosuje się wdrożenie o najwyższym priorytecie.Wdrożenia o niższych priorytetach nie są stosowane ani nie są scalane.Jeśli urządzenie jest przeznaczone dla dwóch lub więcej wdrożeń o równym priorytecie, stosuje się ostatnio utworzone wdrożenie (określone przez sygnaturę czasową tworzenia).

### <a name="labels"></a>Etykiety

Etykiety są parami klucza/wartości ciągu, których można używać do filtrowania i grupowania wdrożeń.Wdrożenie może mieć wiele etykiet. Etykiety są opcjonalne i nie mają wpływu na rzeczywistą konfigurację urządzeń usługi IoT Edge.

### <a name="metrics"></a>Metryki

Domyślnie wszystkie wdrożenia raportują cztery metryki:

* **Obiekt docelowy** pokazuje urządzenia usługi IoT Edge, które są zgodne z warunkiem kierowania wdrożenia.
* **Zastosowane** pokazuje ukierunkowane urządzenia usługi IoT Edge, które nie są kierowane przez inne wdrożenie o wyższym priorytecie.
* **Raportowanie powodzenie** pokazuje urządzenia IoT Edge, które zgłosiły, że moduły zostały pomyślnie wdrożone.
* **Błąd raportowania** pokazuje urządzenia IoT Edge, które zgłosiły, że jeden lub więcej modułów nie zostało pomyślnie wdrożonych. Aby dokładniej zbadać błąd, należy połączyć się zdalnie z tymi urządzeniami i wyświetlić pliki dziennika.

Ponadto można zdefiniować własne metryki niestandardowe, aby ułatwić monitorowanie i zarządzanie wdrożeniem.

Metryki zawierają podsumowanie liczby różnych stanów, które urządzenia mogą raportować z powrotem w wyniku zastosowania konfiguracji wdrożenia. Metryki mogą wysyłać zapytania [do bliźniaczej reprezentacji modułu EdgeHub](module-edgeagent-edgehub.md#edgehub-reported-properties), takich jak *lastDesiredStatus* lub *lastConnectTime*. Przykład:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Dodawanie własnych metryk jest opcjonalne i nie ma wpływu na rzeczywistą konfigurację urządzeń Usługi IoT Edge.

## <a name="layered-deployment"></a>Wdrażanie warstwowe

Wdrożenia warstwowe to wdrożenia automatyczne, które można łączyć w celu zmniejszenia liczby unikatowych wdrożeń, które należy utworzyć. Wdrożenia warstwowe są przydatne w scenariuszach, w których te same moduły są ponownie używane w różnych kombinacjach w wielu wdrożeniach automatycznych.

Wdrożenia warstwowe mają te same podstawowe składniki, co każde wdrożenie automatyczne. Są one kierowane na urządzenia oparte na tagach w bliźniacze urządzenia i zapewniają te same funkcje wokół etykiet, metryk i raportowania stanu. Wdrożenia warstwowe mają również przypisane priorytety, ale zamiast używać priorytetu do określenia, które wdrożenie jest stosowane do urządzenia, priorytet określa, jak wiele wdrożeń są klasyfikowane na urządzeniu. Na przykład jeśli dwa wielowarstwowe wdrożenia mają moduł lub trasę o tej samej nazwie, wdrożenie warstwowe o wyższym priorytecie zostanie zastosowane podczas zastępowania niższego priorytetu.

Moduły środowiska wykonawczego systemu, edgeAgent i edgeHub, nie są skonfigurowane jako część wdrożenia warstwowego. Każde urządzenie usługi IoT Edge, do którego skierowane jest wdrożenie warstwowe, wymaga standardowego wdrożenia automatycznego zastosowanego do niego w pierwszej kolejności. Automatyczne wdrożenie zapewnia podstawę, po której można dodawać wielowarstwowe wdrożenia.

Urządzenie usługi IoT Edge może zastosować jedno i tylko jedno standardowe wdrożenie automatyczne, ale może zastosować wiele wielowarstwowych wdrożeń automatycznych. Wszystkie wdrożenia warstwowe przeznaczone dla urządzenia muszą mieć wyższy priorytet niż automatyczne wdrożenie dla tego urządzenia.

Rozważmy na przykład następujący scenariusz firmy, która zarządza budynkami. Opracowali moduły IoT Edge do zbierania danych z kamer bezpieczeństwa, czujników ruchu i wind. Jednak nie wszystkie ich budynki mogą korzystać ze wszystkich trzech modułów. Dzięki standardowym wdrożeniom automatycznym firma musi utworzyć indywidualne wdrożenia dla wszystkich kombinacji modułów, których potrzebują ich budynki.

![Standardowe wdrożenia automatyczne muszą pomieścić każdą kombinację modułów](./media/module-deployment-monitoring/standard-deployment.png)

Jednak gdy firma przełączy się na warstwowe wdrożenia automatyczne, stwierdzą, że mogą tworzyć te same kombinacje modułów dla swoich budynków z mniejszą liczbą wdrożeń do zarządzania. Każdy moduł ma własne wdrożenie warstwowe, a znaczniki urządzeń identyfikują, które moduły są dodawane do każdego budynku.

![Wielowarstwowe wdrożenia automatyczne upraszczają scenariusze, w których te same moduły są łączone na różne sposoby](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfiguracja bliźniaczej reprezentacji modułu

Podczas pracy z wdrożeniami warstwowymi, może, celowo lub w inny sposób, mieć dwa wdrożenia z tego samego modułu kierowania na urządzenie. W takich przypadkach można zdecydować, czy wdrożenie o wyższym priorytecie należy zastąpić bliźniaczej reprezentacji modułu lub dołączyć do niego. Na przykład może mieć wdrożenie, które stosuje ten sam moduł do 100 różnych urządzeń. Jednak 10 z tych urządzeń znajduje się w bezpiecznych obiektach i wymaga dodatkowej konfiguracji, aby komunikować się za pośrednictwem serwerów proxy. Można użyć wdrożenia warstwowego, aby dodać właściwości bliźniaczej reprezentacji modułu, które umożliwiają tym 10 urządzeniom bezpieczną komunikację bez zastępowania istniejących informacji bliźniaczej reprezentacji modułu z wdrożenia podstawowego.

W manifeście wdrażania można dołączyć bliźniaczej reprezentacji modułu żądane właściwości. Gdzie w standardowym wdrożeniu można dodać właściwości w **sekcji properties.desired** bliźniaczej reprezentacji modułu, w wdrożeniu warstwowym można zadeklarować nowy podzbiór żądanych właściwości.

Na przykład w standardowym wdrożeniu można dodać symulowany moduł czujnika temperatury o następujących żądanych właściwościach, które informują go o wysyłaniu danych w odstępach 5-sekundowych:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

We wdrożeniu warstwowym, który jest przeznaczony dla niektórych lub wszystkich tych samych urządzeń, można dodać właściwość, która informuje symulowanego czujnika, aby wysłać 1000 komunikatów, a następnie zatrzymać. Nie chcesz zastępować istniejących właściwości, więc należy utworzyć nową sekcję w obrębie `layeredProperties`żądanych właściwości o nazwie , która zawiera nową właściwość:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Urządzenie, które ma oba wdrożenia zastosowane, będzie odzwierciedlać następujące właściwości w bliźniaczej reprezentacji modułu dla symulowanego czujnika temperatury:

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

Jeśli ustawisz `properties.desired` pole bliźniaczej reprezentacji modułu we wdrożeniu warstwowym, zastąpi żądane właściwości dla tego modułu w dowolnych wdrożeniach o niższym priorytecie.

## <a name="phased-rollout"></a>Stopniowe wdrażanie

Stopniowe wdrażanie jest ogólnym procesem, w którym operator wdraża zmiany w rozszerzonym zestawie urządzeń IoT Edge. Celem jest stopniowe wprowadzanie zmian w celu zmniejszenia ryzyka wprowadzania zmian na szeroką skalę. Automatyczne wdrożenia ułatwiają zarządzanie stopniowymi wdrażaniami we flocie urządzeń Usługi IoT Edge.

Stopniowe wdrażanie jest wykonywane w następujących fazach i krokach:

1. Ustanowienie środowiska testowego urządzeń Z usługą IoT Edge przez `tag.environment='test'`ich inicjowanie obsługi administracyjnej i ustawianie podwójnego tagu urządzenia, takiego jak .Środowisko testowe należy dublować środowiska produkcyjnego, że wdrożenie ostatecznie będzie kierować.
2. Utwórz wdrożenie obejmujące żądane moduły i konfiguracje. Warunek kierowania powinien być ukierunkowany na testowe środowisko urządzenia IoT Edge.
3. Sprawdź poprawność nowej konfiguracji modułu w środowisku testowym.
4. Zaktualizuj wdrożenie, aby uwzględnić podzbiór produkcyjnych urządzeń usługi IoT Edge, dodając nowy tag do warunku kierowania. Upewnij się również, że priorytet wdrożenia jest wyższy niż w przypadku innych wdrożeń obecnie kierowanych na te urządzenia
5. Sprawdź, czy wdrożenie powiodło się na wybranych urządzeniach IoT, wyświetlając stan wdrożenia.
6. Zaktualizuj wdrożenie, aby kierować reklamy na wszystkie pozostałe produkcyjne urządzenia usługi IoT Edge.

## <a name="rollback"></a>Wycofywania

Wdrożenia można wycofać, jeśli pojawią się błędy lub błędy konfiguracji.Ponieważ wdrożenie definiuje konfigurację modułu bezwzględnego dla urządzenia usługi IoT Edge, dodatkowe wdrożenie musi być również ukierunkowane na to samo urządzenie o niższym priorytecie, nawet jeśli celem jest usunięcie wszystkich modułów.  

Usunięcie wdrożenia nie powoduje usunięcia modułów z urządzeń docelowych. Musi istnieć inne wdrożenie, które definiuje nową konfigurację dla urządzeń, nawet jeśli jest to puste wdrożenie.

Wykonaj wycofywanie w następującej kolejności:

1. Upewnij się, że drugie wdrożenie jest również ukierunkowane na ten sam zestaw urządzeń. Jeśli celem wycofywania jest usunięcie wszystkich modułów, drugie wdrożenie nie powinno zawierać żadnych modułów.
2. Zmodyfikuj lub usuń wyrażenie warunku docelowego wdrożenia, które chcesz wycofać, aby urządzenia przestały spełniać warunek kierowania.
3. Sprawdź, czy wycofywanie powiodło się, wyświetlając stan wdrożenia.
   * Wycofane wdrożenie nie powinno już wyświetlać stanu urządzeń, które zostały wycofane.
   * Drugie wdrożenie powinno teraz zawierać stan wdrożenia dla urządzeń, które zostały wycofane.

## <a name="next-steps"></a>Następne kroki

* Przejdź przez kroki, aby utworzyć, zaktualizować lub usunąć wdrożenie w [deploy i monitorować moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).
* Dowiedz się więcej o innych pojęciach dotyczących ioT Edge, takich jak [środowisko uruchomieniowe IoT Edge](iot-edge-runtime.md) i [moduły IoT Edge.](iot-edge-modules.md)
