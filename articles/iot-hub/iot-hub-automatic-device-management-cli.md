---
title: Zarządzanie urządzeniami automatyczne na dużą skalę za pomocą usługi Azure IoT Hub (CLI) | Dokumentacja firmy Microsoft
description: Przypisz konfigurację do wielu urządzeń IoT za pomocą zarządzania urządzeniami automatyczne usługi Azure IoT Hub
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 0302146634904ccf1d87220d3a24553149e10372
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012968"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-cli"></a>Automatyczne zarządzanie urządzeniami IoT na dużą skalę przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Zarządzanie urządzeniami automatyczne w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań zarządzania flot duże urządzenia. Za pomocą funkcji zarządzania urządzenia automatycznego docelowe zbiór urządzeń, na podstawie ich właściwości, zdefiniuj wymaganą konfiguracją, a następnie pozwól usłudze IoT Hub aktualizowania urządzeń po znalezieniu w zakresie. Ta aktualizacja jest wykonywane przy użyciu _konfiguracji urządzenia automatycznego_, co umożliwi Podsumuj zakończenia i zgodności, scalanie dojścia i konfliktów i wdrażanie konfiguracji etapami.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Przebieg zarządzania urządzeniami automatyczne aktualizowanie zbiór bliźniacze reprezentacje urządzeń za pomocą odpowiednich właściwości i raportowania podsumowanie, który jest oparty na bliźniaczej reprezentacji urządzenia zgłoszonych właściwości.  Wprowadza nową klasę i dokument JSON o nazwie *konfiguracji* ma trzy części:

* **Warunek docelowy** definiuje zakres bliźniaczych reprezentacji urządzeń do aktualizacji. Warunek docelowy jest określony jako zapytanie na tagów bliźniaczych reprezentacji urządzeń i/lub zgłoszonych właściwości.

* **Dopasowywanie zawartości** definiuje żądane właściwości, które mają być dodane lub zaktualizowane w docelowym bliźniaczych reprezentacji urządzeń. Zawartość zawiera ścieżkę do sekcji żądane właściwości, które mają być zmienione.

* **Metryki** zdefiniować podsumowanie liczby różnych stanów konfiguracji, takie jak **Powodzenie**, **w toku**, i **błąd**. Metryki niestandardowe są określane jako zapytania na urządzeniu zgłoszonych właściwości bliźniaka.  Metryki systemu są domyślnych metryk, które mierzą stan aktualizacji bliźniaczej reprezentacji, takie jak liczba bliźniacze reprezentacje urządzeń, które są stosowane i liczba reprezentacje urządzeń, które zostały pomyślnie zaktualizowane. 

## <a name="cli-prerequisites"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

* [Usługi IoT hub](../iot-hub/iot-hub-create-using-cli.md) w subskrypcji platformy Azure. 
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w danym środowisku. Co najmniej z wiersza polecenia platformy Azure musi być w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 
* [Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementowanie bliźniaczych reprezentacji urządzeń w celu skonfigurowania urządzeń

Urządzenia automatycznego konfiguracje wymagają użycia bliźniacze reprezentacje urządzeń, aby zsynchronizować stan między chmurą i urządzeniami.  Zapoznaj się [poznawanie i używanie bliźniaczych reprezentacji urządzeń w usłudze IoT Hub](iot-hub-devguide-device-twins.md) wskazówki na temat korzystania z bliźniaczych reprezentacji urządzeń.

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń za pomocą tagów

Przed przystąpieniem do tworzenia konfiguracji należy określić urządzeń, które mają zostać zmodyfikowane. Usługa Azure IoT Hub identyfikuje urządzenia przy użyciu tagów w bliźniaczej reprezentacji urządzenia. Każde urządzenie może mieć wiele tagów i można je zdefiniować sposób, który ma sens dla Twojego rozwiązania. Na przykład jeśli zarządzasz urządzeniami w różnych lokalizacjach, należy dodać następujące znaczniki do bliźniaczej reprezentacji urządzenia:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Zdefiniuj Adresuj zawartość i metryki

Adresuj zawartość i metryk zapytań są zdefiniowane w bliźniaczej reprezentacji dokumentów JSON, które opisują urządzenia, żądanych właściwości do zestawu i zgłaszanych właściwości do mierzenia.  Aby utworzyć konfigurację urządzenia automatycznego przy użyciu wiersza polecenia platformy Azure, Zapisz Adresuj zawartość i metryki lokalnie jako pliki txt. Używasz ścieżki plików w dalszej części tego tematu, po uruchomieniu polecenia, aby zastosować konfigurację do Twojego urządzenia. 

Oto przykład zawartości podstawowe docelowej:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Poniżej przedstawiono przykładowe zapytania dotyczące metryk:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Utwórz konfigurację

Możesz skonfigurować urządzenia docelowe, tworząc konfigurację, która składa się z Adresuj zawartość i metryki. 

Aby utworzyć konfigurację, użyj następującego polecenia:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**Identyfikator konfiguracji** -Nazwa konfiguracji, który zostanie utworzony w usłudze IoT hub. Nadaj konfigurację unikatową nazwę, która jest maksymalnie 128 małe litery. Należy unikać miejsca do magazynowania i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.

* --**etykiety** — Dodawanie etykiety, aby ułatwić śledzenie konfiguracji. Etykiety są nazwę i pary wartości, które opisują wdrożenia. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`

* --**zawartość** -tekście JSON lub ścieżki pliku do Adresuj zawartość ma być ustawiona jako bliźniaczej reprezentacji żądane właściwości. 

* --**Nazwa koncentratora** — nazwy Centrum IoT, w którym zostanie utworzona konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

* --**warunek docelowy** -Podaj warunek docelowy, aby ustalić, urządzeń, które będą objęte przy użyciu tej konfiguracji. Warunek opiera się na tagów bliźniaczych reprezentacji urządzeń lub żądane właściwości bliźniaczej reprezentacji urządzenia, a powinien być zgodny z formatem wyrażenia. Na przykład: `tags.environment='test'` lub `properties.desired.devicemodel='4000x'`. 

* --**priorytet** -dodatnią liczbą całkowitą. W przypadku, gdy co najmniej dwóch konfiguracji są przeznaczone dla tego samego urządzenia, zostaną zastosowane konfiguracji o najwyższej wartości liczbowe dla priorytetu.

* --**metryki** -Filepath do zapytania dotyczące metryk. Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie może raportować po zastosowaniu konfiguracji zawartości. Może na przykład utworzyć metrykę dla oczekujących zmian ustawień spowoduje zmianę, metryki dla błędów i metryki dla zmiany w ustawieniach pomyślnie. 

## <a name="monitor-a-configuration"></a>Monitor konfiguracji

Użyj następującego polecenia, aby wyświetlić zawartość konfiguracji:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**Identyfikator konfiguracji** -Nazwa konfiguracji, który znajduje się w Centrum IoT hub.

* --**Nazwa koncentratora** — nazwy Centrum IoT, w której istnieje konfiguracji. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

Sprawdź konfigurację w oknie wiersza polecenia. **Metryki** listy właściwości — liczba, dla każdego metryki, które jest obliczane przez każdego Centrum:

* **targetedCount** — metryki systemu, która określa liczbę bliźniaczych reprezentacji urządzeń w usłudze IoT Hub odpowiada warunkowi określania wartości docelowej.

* **appliedCount** — metryki systemu określa liczbę urządzeń, które miały zawartości docelowych zastosowane.

* **Twoje Metryka niestandardowa** — wszystkie metryki, zdefiniowany przez użytkownika są metryki użytkowników.

Możesz wyświetlić listę identyfikatorów urządzeń lub obiektów dla każdego z metryk, za pomocą następującego polecenia:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**Identyfikator konfiguracji** — Nazwa wdrożenia, który istnieje w usłudze IoT hub.

* --**identyfikator metryki** — Nazwa metryki, dla którego chcesz wyświetlić listę urządzeń identyfikatorów, na przykład `appliedCount`.

* --**Nazwa koncentratora** — nazwy Centrum IoT, w której istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji przy użyciu polecenia `az account set -s [subscription name]`.

* --**Typ metryki** — może być typem metryki `system` lub `user`.  Metryki systemu są `targetedCount` i `appliedCount`. Wszystkie pozostałe metryki są metryki użytkowników.

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Podczas modyfikowania konfiguracji, zmiany są natychmiast replikowane do wszystkie objęte nimi urządzenia. 

Jeśli zaktualizujesz warunek docelowy, zachodzą następujące aktualizacje:

* Jeśli w bliźniaczej reprezentacji urządzenia nie spełniają warunek docelowy stare, ale nowy warunek docelowy spełnia i ta konfiguracja ma najwyższy priorytet dla tej bliźniaczej reprezentacji urządzenia, ta konfiguracja jest stosowany do bliźniaczej reprezentacji urządzenia. 

* Jeśli w bliźniaczej reprezentacji urządzenia nie jest już spełnia warunek docelowy, ustawienia z konfiguracji zostaną usunięte i bliźniaczej reprezentacji urządzenia zostaną zmodyfikowane przez dalej konfigurację najwyższy priorytet. 

* Jeśli w bliźniaczej reprezentacji urządzenia obecnie uruchomiona ta konfiguracja nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy inne konfiguracje, ustawienia z konfiguracji zostanie usunięta i żadne inne zmiany zostaną wprowadzone w bliźniaczej reprezentacji. 

Aby zaktualizować konfigurację, użyj następującego polecenia:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**Identyfikator konfiguracji** -Nazwa konfiguracji, który znajduje się w Centrum IoT hub.

* --**Nazwa koncentratora** — nazwy Centrum IoT, w której istnieje konfiguracji. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji przy użyciu polecenia `az account set -s [subscription name]`.

* --**Ustaw** — aktualizowanie właściwości w konfiguracji. Można aktualizować następujące właściwości:

    * targetCondition — przykład `targetCondition=tags.location.state='Oregon'`

    * etykiety 

    * priority

## <a name="delete-a-configuration"></a>Usuwanie konfiguracji

Możesz usunąć konfigurację, wszelkie bliźniaczych reprezentacji urządzeń przyjmą ich dalej konfiguracji najwyższy priorytet. Jeśli bliźniaczych reprezentacji urządzeń nie spełniają warunek docelowy innych konfiguracji, żadne inne ustawienia są stosowane. 

Aby usunąć konfigurację, użyj następującego polecenia:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**Identyfikator konfiguracji** -Nazwa konfiguracji, który znajduje się w Centrum IoT hub.

* --**Nazwa koncentratora** — nazwy Centrum IoT, w której istnieje konfiguracji. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji przy użyciu polecenia `az account set -s [subscription name]`.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób konfigurowania i monitorowania urządzeń IoT na dużą skalę. Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Zarządzanie tożsamościami urządzeń usługi IoT Hub w zbiorcze](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Aby zapoznać się z pomocą IoT Hub Device Provisioning Service do włączenia aprowizacji bezobsługowe, just-in-time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](/azure/iot-dps)
