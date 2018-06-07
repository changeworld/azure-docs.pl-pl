---
title: Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę z Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przypisywanie konfiguracji do wielu urządzeń przy użyciu konfiguracji automatycznego urządzeń Centrum IoT Azure
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: fe5ce960663f39d4f2c87a7bbffa091d327e9559
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632452"
---
# <a name="configure-and-monitor-iot-devices-at-scale---preview"></a>Konfigurowanie i monitorować urządzenia IoT na dużą skalę - preview

Zarządzanie urządzeniami automatyczne w Centrum IoT Azure pozwala zautomatyzować wiele powtarzających się i złożonych zadań związanych z zarządzaniem floty dużych urządzenia za pośrednictwem całości ich cyklów. Z zarządzania urządzeniami automatyczne docelowa zbiór urządzeń, na podstawie ich właściwości, zdefiniuj wymaganą konfiguracją, a let Centrum IoT aktualizowania urządzeń, które pochodzą do zakresu.  To jest wykonywane przy użyciu konfiguracji automatycznego urządzenia, która będzie również można podsumować zakończenia i zgodności, scalanie dojścia i konflikty i wdrażanie konfiguracji etapami.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Pracy konfiguracji urządzenia automatycznego aktualizowania zbiór urządzeń twins odpowiednie właściwości i raportowania podsumowanie w oparciu o urządzenia dwie zgłosił właściwości.  Podaj nową klasą a dokumentu JSON o nazwie _konfiguracji_ którego ma trzy części:

* **Target warunku** określa zakres twins urządzenia do zaktualizowania. Warunek docelowy jest określony jako zapytania w tagach dwie urządzenia i/lub raportowania właściwości.

* **Kierować zawartość** definiuje żądanej właściwości mają być dodane lub zaktualizowane w twins urządzenia docelowego. Zawartość zawiera ścieżkę do sekcji żądanej właściwości zostanie zmieniony.

* **Metryki** zdefiniuj podsumowanie liczby różnych stanami konfiguracji, takich jak **Powodzenie**, **w toku**, i **błąd**. Metryki niestandardowe są określone jako zapytania na urządzeniu dwie zgłoszone właściwości.  Metryki systemu są domyślne metryki pomiaru dwie stan aktualizacji, takie jak liczba twins urządzenia, które są stosowane i liczba twins, które zostały pomyślnie zaktualizowane. 

> [!Note]
> Podczas udostępniania wersji zapoznawczej ta funkcja nie jest dostępna dla centra IoT w regionach wschodnie stany USA, zachodnie stany USA, Europa Północna, Europa i Europa Zachodnia.

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

## <a name="create-a-configuration"></a>Tworzenie konfiguracji

1. W [portalu Azure][lnk-portal], przejdź do Centrum IoT. 
1. Wybierz **konfiguracji urządzenia IoT (wersja zapoznawcza)**.
1. Wybierz **Dodaj konfigurację**.

Brak pięć kroki, aby utworzyć konfigurację. Poniższe sekcje przeprowadzenie każdej z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Nazwa i etykieta

1. Nadaj unikatową nazwę, która jest maksymalnie 128 małe litery konfiguracji. Unikaj spacje i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.
1. Dodawanie etykiet służące do śledzenia konfiguracji. Etykiety są **nazwa**, **wartość** pary, które opisują konfiguracji. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`.
1. Wybierz **dalej** aby przejść do kroku 2. 

### <a name="step-2-specify-settings"></a>Krok 2: Określ ustawienia

W tej sekcji określa kierowanie zawartości w twins urządzenia docelowego. Brak dwóch parametrów wejściowych dla każdej grupy ustawień. Pierwsza to ścieżki dwie urządzenia, która jest ścieżka do sekcji JSON w ramach właściwości dwie potrzeby, które zostaną ustawione.  Drugim jest zawartość JSON, który ma zostać wstawiony w tej sekcji. Na przykład ustawić ścieżkę dwie urządzenia i zawartości do następującego:

![Ustaw ścieżkę dwie urządzenia i zawartości](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Można również ustawić poszczególnych ustawień, określając na całej ścieżce dwie ścieżki urządzenia i wartość w zawartości nie nawiasami. Na przykład ustawić ścieżkę dwie urządzenia `properties.desired.chiller-water.temperature` ustawiono zawartości: `66`

Jeśli co najmniej dwie konfiguracje skierować je do tej samej ścieżki dwie urządzenia, zostaną zastosowane zawartości z konfiguracji najwyższy priorytet (priorytet jest zdefiniowany w kroku 4).

Jeśli chcesz usunąć właściwość, należy określić wartość właściwości `null`.

Dodatkowe ustawienia można dodawać przez zaznaczenie **Dodaj ustawienie dwie urządzenia**

### <a name="step-3-specify-metrics-optional"></a>Krok 3: Określ metryki (opcjonalnie)

Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie może wysyłać raporty w wyniku stosowania zawartość konfiguracji. Może na przykład utworzyć metrykę dla oczekujące zmiany ustawień, metryki dla błędów i metryki dla zmiany ustawień powiodło się.

1. Wprowadź nazwę **Nazwa metryki**
1. Wprowadź kwerendę dla **kryteria Metryka**.  Zapytanie jest oparty na urządzeniu dwie zgłoszone właściwości.  Metryka przedstawia liczbę wierszy zwróconych przez kwerendę.

Na przykład: `SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

Mogą zawierać klauzuli, że konfiguracja została zastosowana, na przykład: `SELECT deviceId FROM devices WHERE configurations.[[yourconfigname]].status='Applied'` tym nawias kwadratowy.


### <a name="step-4-target-devices"></a>Krok 4: Urządzeń docelowych

Użyj właściwości tagów z twins Twojego urządzenia pod kątem określonych urządzeń, które powinien zostać wyświetlony tej konfiguracji.  Możesz też określić urządzeń przez urządzenie dwie zgłoszone właściwości.

Ponieważ wiele konfiguracji mogą odnosić się do tego samego urządzenia, należy nadać każdej konfiguracji priorytety. Jeśli kiedykolwiek występuje konflikt, wins w konfiguracji o najwyższym priorytecie. 

1. Wprowadź dodatnią liczbą całkowitą w konfiguracji **priorytet**. Największa wartość numeryczna jest traktowany jako najwyższy priorytet. Jeśli dwie konfiguracje mają ten sam numer priorytet, który został utworzony w większości ostatnio wins. 
1. Wprowadź **Target warunku** do określ urządzeń, które będą stosowane w przypadku tej konfiguracji. Warunek jest oparta na tagi dwie urządzenia lub dwie urządzenia zgłoszone właściwości i powinny być zgodne z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.reported.chillerProperties.model='4000x'`. 
1. Wybierz **dalej** można przenieść do ostatniego kroku.

### <a name="step-5-review-configuration"></a>Krok 5: Przejrzyj konfigurację

Przejrzyj informacje o konfiguracji, a następnie wybierz **przesyłania**.

## <a name="monitor-a-configuration"></a>Monitor konfiguracji

Aby wyświetlić szczegóły konfiguracji i monitorowania urządzeń, uruchomienie jej, wykonaj następujące kroki:

1. W [portalu Azure][lnk-portal], przejdź do Centrum IoT. 
1. Wybierz **konfiguracji urządzenia IoT (wersja zapoznawcza)**.
1. Sprawdź, czy listy konfiguracji. Dla każdej konfiguracji można wyświetlić następujące informacje:
   * **Identyfikator** — Nazwa konfiguracji.
   * **Docelowa warunku** -Zapytanie używane do definiowania urządzeń docelowych.
   * **Priorytet** — numer priorytetu skojarzony z konfiguracją.
   * **Czas utworzenia** -znacznik czasu od utworzenia konfiguracji. Sygnatura czasowa służy do Przerwij ties, gdy dwie konfiguracje mają ten sam priorytet. 
   * **Metryki systemu** -metryki, które mają być obliczane przez Centrum IoT i nie można dostosowywać przez deweloperów. Celem określa liczbę twins urządzenia, spełniających warunek docelowy. Stosuje określona liczba twins urządzenia, które zostały zmodyfikowane przez tę konfigurację, która może obejmować częściowe zmiany w przypadku, gdy konfiguracja oddzielne, o wyższym priorytecie również wprowadzone zmiany. 
   * **Metryki niestandardowe** -metryki, które zostały określone przez dewelopera jako zapytań dotyczących dwie urządzenia zgłoszone właściwości.  Maksymalnie pięć metryki niestandardowe można zdefiniować na konfigurację. 
   
1. Wybierz konfigurację, którą chcesz monitorować.  
1. Sprawdź, czy szczegóły konfiguracji. Korzystania z kart, aby wyświetlić szczegółowe informacje o urządzeniach, które otrzymały konfiguracji: 
   * **Docelowa warunku** — urządzenia, które są zgodne z warunkiem docelowej. 
   * **Metryki** — lista metryki systemu i metryki niestandardowe.  Można wyświetlić listę urządzeń, które są uwzględniane dla poszczególnych metryki wybranie Metryka w listy rozwijanej, a następnie wybierając **Wyświetl urządzenia**.
   * **Ustawienia urządzenia dwie** — ustawienia dwie urządzenia, które są ustawiane przez tę konfigurację. 
   * **Etykiety konfiguracji** -pary klucz wartość używana do opisu konfiguracji.  Etykiety nie mają wpływu na funkcjonalność. 

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Podczas modyfikowania konfiguracji zmiany natychmiast replikowane do wszystkich urządzeń docelowych. 

Po zaktualizowaniu warunek docelowy są wykonywane następujące aktualizacje:
* Jeśli dwie urządzenie nie spełnia warunek docelowy stare, ale spełnia nowy warunek docelowy i ta konfiguracja jest najwyższy priorytet dwie tego urządzenia, ta konfiguracja jest stosowany do dwie urządzenia. 
* Jeśli dwie urządzenie nie spełnia warunek docelowy, ustawienia z konfiguracji zostaną usunięte i dwie urządzenia zostaną zmodyfikowane przez konfigurację dalej najwyższy priorytet. 
* Jeśli dwie urządzenia, w obecnie uruchomiona ta konfiguracja nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy innych konfiguracji, ustawienia z konfiguracji zostaną usunięte i nie zostaną wprowadzone nie inne zmiany na dwie. 

Aby zmodyfikować konfigurację, użyj następujących kroków: 

1. W [portalu Azure][lnk-portal], przejdź do Centrum IoT. 
1. Wybierz **konfiguracji urządzenia IoT (wersja zapoznawcza)**. 
1. Wybierz konfigurację, którą chcesz zmodyfikować. 
1. Aktualizowanie następujące pola: 
   * Warunek docelowy 
   * Etykiety 
   * Priorytet 
   * Metryki
1. Wybierz pozycję **Zapisz**.
1. Postępuj zgodnie z instrukcjami [Monitor konfiguracji] [zakotwiczenia monitorów] Aby obejrzeć zmiany wdrożyć. 

## <a name="delete-a-configuration"></a>Usuwanie konfiguracji

Podczas usuwania konfiguracji twins dowolnego urządzenia przełączyć na ich dalej konfiguracji najwyższy priorytet. Jeśli twins urządzenia nie spełniają warunek docelowy z dowolnej innej konfiguracji, nie inne ustawienia są stosowane. 

1. W [portalu Azure][lnk-portal], przejdź do Centrum IoT. 
1. Wybierz **konfiguracji urządzenia IoT (wersja zapoznawcza)**. 
1. Użyj pola wyboru, aby wybrać konfigurację, którą chcesz usunąć. 
1. Wybierz pozycję **Usuń**.
1. Monit zostanie wyświetlony monit o potwierdzenie.

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
