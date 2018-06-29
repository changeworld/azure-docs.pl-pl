---
title: Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę z Centrum IoT Azure (CLI) | Dokumentacja firmy Microsoft
description: Przypisywanie konfiguracji do wielu urządzeń przy użyciu konfiguracji automatycznego urządzeń Centrum IoT Azure
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: c12a07aabdecb070cfa99f8851f907499599a1fc
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036726"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę, przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Zarządzanie urządzeniami automatyczne w Centrum IoT Azure pozwala zautomatyzować wiele powtarzających się i złożonych zadań związanych z zarządzaniem floty dużych urządzenia za pośrednictwem całości ich cyklów. Z zarządzania urządzeniami automatyczne docelowa zbiór urządzeń, na podstawie ich właściwości, zdefiniuj wymaganą konfiguracją, a let Centrum IoT aktualizowania urządzeń, które pochodzą do zakresu.  To jest wykonywane przy użyciu konfiguracji automatycznego urządzenia, która będzie również można podsumować zakończenia i zgodności, scalanie dojścia i konflikty i wdrażanie konfiguracji etapami.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Pracy konfiguracji urządzenia automatycznego aktualizowania zbiór urządzeń twins odpowiednie właściwości i raportowania podsumowanie w oparciu o urządzenia dwie zgłosił właściwości.  Podaj nową klasą a dokumentu JSON o nazwie _konfiguracji_ którego ma trzy części:

* **Target warunku** określa zakres twins urządzenia do zaktualizowania. Warunek docelowy jest określony jako zapytania w tagach dwie urządzenia i/lub raportowania właściwości.

* **Kierować zawartość** definiuje żądanej właściwości mają być dodane lub zaktualizowane w twins urządzenia docelowego. Zawartość zawiera ścieżkę do sekcji żądanej właściwości zostanie zmieniony.

* **Metryki** zdefiniuj podsumowanie liczby różnych stanami konfiguracji, takich jak **Powodzenie**, **w toku**, i **błąd**. Metryki niestandardowe są określone jako zapytania na urządzeniu dwie zgłoszone właściwości.  Metryki systemu są domyślne metryki pomiaru dwie stan aktualizacji, takie jak liczba twins urządzenia, które są stosowane i liczba twins, które zostały pomyślnie zaktualizowane. 

## <a name="cli-prerequisites"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w Twojej subskrypcji platformy Azure. 
* Zainstalowany w środowisku [interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 
* [Rozszerzenia IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementowanie twins urządzenia w celu skonfigurowania urządzeń

Automatyczne urządzenie konfiguracje wymagają użycia twins urządzenia do synchronizacji stanu między chmurą i urządzeniami.  Zapoznaj się [opis i użyj twins urządzenie w Centrum IoT] [ lnk-device-twin] wskazówki na temat używania twins urządzenia.

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem konfiguracji należy określić urządzeń, które mają zostać zmodyfikowane. Centrum IoT Azure identyfikuje urządzenia przy użyciu tagów w dwie urządzenia. Każde urządzenie może mieć wiele tagów i można je zdefiniować sposób, który ma sens dla rozwiązania. Na przykład możesz zarządzać urządzeniami w różnych lokalizacjach, może dodać następujących tagów do dwie urządzenia:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```
## <a name="define-the-target-content-and-metrics"></a>Zdefiniuj zawartość docelowych i metryki

Zawartość docelowej i metryka zapytania są określone jako dokumenty JSON, które opisują urządzenia dwie żądaną jako zestaw i zgłoszone właściwości mierzone.  Aby utworzyć konfigurację urządzenia automatycznego używa interfejsu wiersza polecenia platformy Azure w wersji 2.0, Zapisz kierowanie zawartości i metryki lokalnie jako plików txt. Ścieżki plików użyje w dalszej części dalej po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia. 

Oto przykład zawartości podstawowe docelowych:

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

Poniżej przedstawiono przykłady kwerend metryki:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Tworzenie konfiguracji

Tworząc konfigurację, która składa się z zawartości docelowych i metryki konfigurowania urządzeń docelowych. 

Aby utworzyć konfigurację, użyj następującego polecenia:

   ```cli
   az iot hub configuration create --config-id [configuration id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int] --metrics [metric queries]
   ```

* **Identyfikator--config** — Nazwa konfiguracji, które zostaną utworzone w Centrum IoT. Nadaj unikatową nazwę, która jest maksymalnie 128 małe litery konfiguracji. Unikaj spacje i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.
* **--etykiety** — Dodawanie etykiet służące do śledzenia konfiguracji. Etykiety są nazwę i pary wartości, które opisują wdrożenia. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`
* **--zawartości** -tekście JSON lub ścieżka pliku docelowego zawartości ma być ustawiona jako dwie żądanego właściwości. 
* **--nazwy centrum** — nazwa Centrum IoT, w którym zostanie utworzona konfiguracja. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`
* **--warunek docelowy** — wprowadź warunek docelowy określ urządzeń, które będą stosowane w przypadku tej konfiguracji. Warunek jest oparta na tagi dwie urządzenia lub dwie urządzenia żądanego właściwości i powinny być zgodne z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.desired.devicemodel='4000x'`. 
* **--priorytet** -dodatnią liczbą całkowitą. W przypadku, gdy co najmniej dwie konfiguracje są przeznaczone dla tego samego urządzenia, będą miały zastosowania konfiguracji z najwyższą wartością liczbową priorytetu.
* **--metryki** -Filepath do metryki zapytań. Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie może wysyłać raporty w wyniku stosowania zawartość konfiguracji. Może na przykład utworzyć metrykę dla oczekujące zmiany ustawień, metryki dla błędów i metryki dla zmiany ustawień powiodło się. 

## <a name="monitor-a-configuration"></a>Monitor konfiguracji

Aby wyświetlić zawartość konfiguracji, użyj następującego polecenia:

   ```cli
az iot hub configuration show --config-id [configuration id] --hub-name [hub name]
   ```
* **Identyfikator--config** — Nazwa konfiguracji, który istnieje w Centrum IoT.
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje konfiguracji. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`

Sprawdź konfigurację w oknie wiersza polecenia. **Metryki** liczba dla każdej metryki, które jest oceniane w każdej Centrum list właściwości:
* **targetedCount** -metrykę systemu, która określa liczbę twins urządzenie w Centrum IoT, spełniających warunek określania wartości docelowej.
* **appliedCount** -Metryka systemu określa liczbę urządzeń, które miały zawartości docelowych zastosowane.
* **Twoje metryki niestandardowe** — wszystkie metryki zdefiniowane będą uznawane za metryki użytkownika.

Można wyświetlić listę identyfikatory urządzeń lub obiektów dla każdego metryki za pomocą następującego polecenia:

   ```cli
az iot hub configuration show-metric --config-id [configuration id] --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
   ```

* **Identyfikator--config** — Nazwa wdrożenia, który istnieje w Centrum IoT.
* **— Identyfikator Metryka** — Nazwa metryki, dla którego chcesz wyświetlić listę urządzeń identyfikatorów, na przykład `appliedCount`
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje wdrożenie. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`
* **--typu Metryka** — może być typem metryki `system` lub `user`.  Metryki systemu są `targetedCount` i `appliedCount`. Wszystkie inne metryki są metryki użytkownika.

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Podczas modyfikowania konfiguracji zmiany natychmiast replikowane do wszystkich urządzeń docelowych. 

Po zaktualizowaniu warunek docelowy są wykonywane następujące aktualizacje:
* Jeśli dwie urządzenie nie spełnia warunek docelowy stare, ale spełnia nowy warunek docelowy i ta konfiguracja jest najwyższy priorytet dwie tego urządzenia, ta konfiguracja jest stosowany do dwie urządzenia. 
* Jeśli dwie urządzenie nie spełnia warunek docelowy, ustawienia z konfiguracji zostaną usunięte i dwie urządzenia zostaną zmodyfikowane przez konfigurację dalej najwyższy priorytet. 
* Jeśli dwie urządzenia, w obecnie uruchomiona ta konfiguracja nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy innych konfiguracji, ustawienia z konfiguracji zostaną usunięte i nie zostaną wprowadzone nie inne zmiany na dwie. 

Aby zaktualizować konfigurację, użyj następującego polecenia:

   ```cli
az iot hub configuration update --config-id [configuration id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **Identyfikator--config** — Nazwa konfiguracji, który istnieje w Centrum IoT.
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje konfiguracji. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`
* **--ustawić** — aktualizowanie właściwości w konfiguracji. Można aktualizować następujące właściwości:
    * targetCondition — na przykład `targetCondition=tags.location.state='Oregon'`
    * etykiety 
    * priorytet

## <a name="delete-a-configuration"></a>Usuwanie konfiguracji

Podczas usuwania konfiguracji twins dowolnego urządzenia przełączyć na ich dalej konfiguracji najwyższy priorytet. Jeśli twins urządzenia nie spełniają warunek docelowy z dowolnej innej konfiguracji, nie inne ustawienia są stosowane. 

Aby usunąć konfigurację, użyj następującego polecenia:

   ```cli
az iot hub configuration delete --config-id [configuration id] --hub-name [hub name] 
   ```
* **Identyfikator--config** — Nazwa konfiguracji, który istnieje w Centrum IoT.
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje konfiguracji. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania i monitorowania urządzeń IoT na dużą skalę. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu Centrum IoT Azure:

* [Zarządzanie tożsamościami urządzenia IoT Hub zbiorcze][lnk-bulkIDs]
* [Metryki Centrum IoT][lnk-metrics]
* [Operacje monitorowania][lnk-monitor]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przewodnik dewelopera Centrum IoT][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

Aby zapoznać się z usługi IoT Centrum urządzeń inicjowania obsługi administracyjnej Włącz bezobsługową, w czasie inicjowania obsługi, zobacz: 

* [Usługi inicjowania obsługi administracyjnej urządzeniu Centrum IoT Azure][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
