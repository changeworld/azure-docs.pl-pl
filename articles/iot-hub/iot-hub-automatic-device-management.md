---
title: Do zarządzania urządzeniami automatyczne skalowanie przy użyciu usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przypisz konfigurację do wielu urządzeń IoT za pomocą zarządzania urządzeniami automatyczne usługi Azure IoT Hub
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 598bf82e375f472b2f723c3462ba7ba7b4d25fbe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012953"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-portal"></a>Automatyczne zarządzanie urządzeniami IoT na dużą skalę przy użyciu witryny Azure portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Zarządzanie urządzeniami automatyczne w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań zarządzania flot duże urządzenia. Za pomocą funkcji zarządzania urządzenia automatycznego docelowe zbiór urządzeń, na podstawie ich właściwości, zdefiniuj wymaganą konfiguracją, a następnie pozwól usłudze IoT Hub aktualizowania urządzeń po znalezieniu w zakresie. Ta aktualizacja jest wykonywane przy użyciu _konfiguracji urządzenia automatycznego_, co umożliwi Podsumuj zakończenia i zgodności, scalanie dojścia i konfliktów i wdrażanie konfiguracji etapami.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Przebieg zarządzania urządzeniami automatyczne aktualizowanie zbiór bliźniacze reprezentacje urządzeń za pomocą odpowiednich właściwości i raportowania podsumowanie, który jest oparty na bliźniaczej reprezentacji urządzenia zgłoszonych właściwości.  Wprowadza nową klasę i dokument JSON o nazwie *konfiguracji* ma trzy części:

* **Warunek docelowy** definiuje zakres bliźniaczych reprezentacji urządzeń do aktualizacji. Warunek docelowy jest określony jako zapytanie na tagów bliźniaczych reprezentacji urządzeń i/lub zgłoszonych właściwości.

* **Dopasowywanie zawartości** definiuje żądane właściwości, które mają być dodane lub zaktualizowane w docelowym bliźniaczych reprezentacji urządzeń. Zawartość zawiera ścieżkę do sekcji żądane właściwości, które mają być zmienione.

* **Metryki** zdefiniować podsumowanie liczby różnych stanów konfiguracji, takie jak **Powodzenie**, **w toku**, i **błąd**. Metryki niestandardowe są określane jako zapytania na urządzeniu zgłoszonych właściwości bliźniaka.  Metryki systemu są domyślnych metryk, które mierzą stan aktualizacji bliźniaczej reprezentacji, takie jak liczba bliźniacze reprezentacje urządzeń, które są stosowane i liczba reprezentacje urządzeń, które zostały pomyślnie zaktualizowane. 

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

## <a name="create-a-configuration"></a>Utwórz konfigurację

1. W [witryny Azure portal](https://portal.azure.com), przejdź do Centrum IoT hub. 

2. Wybierz **konfiguracji urządzenia IoT**.

3. Wybierz **Dodaj konfigurację**.

Istnieje pięć kroków, aby utworzyć konfigurację. Poniższe sekcje przeprowadzą za pośrednictwem każdej z nich. 

### <a name="name-and-label"></a>Nazwa i etykieta

1. Nadaj konfigurację unikatową nazwę, która jest maksymalnie 128 małe litery. Należy unikać miejsca do magazynowania i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.

2. Dodaj etykiety służące do śledzenia Twojej konfiguracji. Etykiety są **nazwa**, **wartość** pary, które opisują konfigurację. Na przykład: `HostPlatform, Linux` lub `Version, 3.0.1`.

3. Wybierz **dalej** można przenieść do następnego kroku. 

### <a name="specify-settings"></a>Określ ustawienia

W tej sekcji określa Adresuj zawartość w docelowym bliźniaczych reprezentacji urządzeń. Istnieją dwa obiekty wejściowe dla każdego zestawu ustawień. Pierwsza to ścieżki bliźniaczej reprezentacji urządzenia, który jest ścieżką do sekcji JSON w odpowiednich właściwości bliźniaka, które zostaną ustawione.  Drugim jest zawartość JSON, który ma zostać wstawiony w tej sekcji. Na przykład Ustaw ścieżki bliźniacza reprezentacja urządzenia i zawartość do następującego:

![Ustaw ścieżkę bliźniacza reprezentacja urządzenia i zawartości](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Można również ustawić poszczególnych ustawień, podając ścieżkę do całej ścieżki bliźniacza reprezentacja urządzenia i wartość w zawartości przy użyciu nie nawiasów. Na przykład ustawić ścieżki bliźniaczej reprezentacji urządzenia `properties.desired.chiller-water.temperature` i ustaw zawartość `66`.

Jeśli co najmniej dwóch konfiguracjach skierować je do tej samej ścieżce bliźniaczej reprezentacji urządzenia, zostaną zastosowane zawartości z najwyższy priorytet konfiguracji (priorytet jest zdefiniowany w kroku 4).

Jeśli chcesz usunąć właściwość, należy określić wartość właściwości `null`.

Można dodać dodatkowe ustawienia, wybierając **Dodaj ustawienie bliźniaczej reprezentacji urządzenia**.

### <a name="specify-metrics-optional"></a>Określ metryki (opcjonalnie)

Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie może raportować po zastosowaniu konfiguracji zawartości. Może na przykład utworzyć metrykę dla oczekujących zmian ustawień spowoduje zmianę, metryki dla błędów i metryki dla zmiany w ustawieniach pomyślnie.

1. Wprowadź nazwę dla **Nazwa metryki**.

2. Wprowadź zapytanie dotyczące **kryteria metryki**.  Zapytanie opiera się na urządzeniu zgłoszonych właściwości bliźniaka.  Metryka reprezentuje liczbę wierszy zwróconych przez zapytanie.

Na przykład:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Może zawierać klauzuli, że została zastosowana konfiguracja, na przykład: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

### <a name="target-devices"></a>Urządzenia docelowe

Użyj właściwości tagi z bliźniaczych reprezentacji urządzeń pod kątem konkretnych urządzeń, które powinny otrzymać tę konfigurację.  Można również przeznaczać urządzeń według urządzeń zgłoszonych właściwości bliźniaka.

Ponieważ wiele konfiguracji mogą odnosić się do tego samego urządzenia, należy nadać kontu konfiguracjami numer priorytetu. Jeśli nigdy nie są zgodne, usługa wins konfiguracji o najwyższym priorytecie. 

1. Wprowadź dodatnią liczbę całkowitą dla konfiguracji **priorytet**. Najwyższą wartość liczbową, jest uznawana za najwyższy priorytet. Jeśli dwie konfiguracje mają ten sam numer priorytet, ten, który został utworzony w większości ostatnio wins. 

2. Wprowadź **warunek docelowy** do określenia urządzeń, które będą objęte przy użyciu tej konfiguracji. Warunek opiera się na tagów bliźniaczych reprezentacji urządzeń lub zgłoszonych właściwości bliźniaczej reprezentacji urządzenia, a powinien być zgodny z formatem wyrażenia. Na przykład: `tags.environment='test'` lub `properties.reported.chillerProperties.model='4000x'`. Można określić `*` pod kątem wszystkich urządzeń.

3. Wybierz **dalej** można przenieść do ostatniego kroku.

### <a name="review-configuration"></a>Przejrzyj konfigurację

Przejrzyj informacje o konfiguracji, a następnie wybierz **przesyłania**.

## <a name="monitor-a-configuration"></a>Monitor konfiguracji

Aby wyświetlić szczegółowe informacje o konfiguracji i monitorowania urządzeń, w których jest on uruchomiony, wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do Centrum IoT hub. 

2. Wybierz **konfiguracji urządzenia IoT**.

3. Sprawdź listę konfiguracji. Dla każdej konfiguracji można wyświetlić następujące informacje:

   * **Identyfikator** -Nazwa konfiguracji.

   * **Warunek docelowy** -zapytania używane do definiowania urządzeń docelowych.

   * **Priorytet** — numer priorytetu, przypisanych do konfiguracji.

   * **Godzina utworzenia** -sygnaturę czasową od utworzenia konfiguracji. Sygnatura czasowa jest używany na przerwanie ties, gdy dwie konfiguracje mają ten sam priorytet. 

   * **Metryki systemu** — metryki, które są obliczane przez usługę IoT Hub i nie można dostosować przez deweloperów. Docelowe Określa liczbę bliźniaczych reprezentacji urządzeń spełniających warunek docelowy. Stosuje się określona liczba bliźniacze reprezentacje urządzeń, które zostały zmodyfikowane przez tę konfigurację, który może zawierać zmiany częściowe, w przypadku, gdy konfiguracja oddzielne, o wyższym priorytecie również wprowadzone zmiany. 

   * **Metryki niestandardowe** — metryki, które zostały określone przez dewelopera jako zapytania dotyczące bliźniaczych reprezentacji urządzeń zgłoszonych właściwości.  Maksymalnie pięć metryki niestandardowe mogą być definiowane dla poszczególnych konfiguracji. 
   
4. Wybierz konfigurację, którą chcesz monitorować.  

5. Sprawdź szczegóły konfiguracji. Aby wyświetlić szczegółowe informacje o urządzeniach, które otrzymały konfiguracji, można użyć karty.

   * **Warunek docelowy** — urządzenia, które są zgodne z warunkiem docelowym. 

   * **Metryki** -listę metryk systemu i metryki niestandardowe.  Można wyświetlić listę urządzeń, które są uwzględniane dla poszczególnych metryki, wybierając metryki z listy rozwijanej, a następnie wybierając **wyświetlać urządzenia**.

   * **Ustawienia bliźniaczej reprezentacji urządzenia** — ustawienia bliźniaczej reprezentacji urządzenia, które są ustawione przez tą konfigurację. 

   * **Etykiety konfiguracji** -pary klucz wartość, używane do opisywania konfiguracji.  Etykiety nie mają wpływu na funkcjonalność. 

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Podczas modyfikowania konfiguracji, zmiany są natychmiast replikowane do wszystkie objęte nimi urządzenia. 

Jeśli zaktualizujesz warunek docelowy, zachodzą następujące aktualizacje:

* Jeśli w bliźniaczej reprezentacji urządzenia nie spełniają warunek docelowy stare, ale nowy warunek docelowy spełnia i ta konfiguracja ma najwyższy priorytet dla tej bliźniaczej reprezentacji urządzenia, ta konfiguracja jest stosowany do bliźniaczej reprezentacji urządzenia. 

* Jeśli w bliźniaczej reprezentacji urządzenia nie jest już spełnia warunek docelowy, ustawienia z konfiguracji zostaną usunięte i bliźniaczej reprezentacji urządzenia zostaną zmodyfikowane przez dalej konfigurację najwyższy priorytet. 

* Jeśli w bliźniaczej reprezentacji urządzenia obecnie uruchomiona ta konfiguracja nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy inne konfiguracje, ustawienia z konfiguracji zostanie usunięta i żadne inne zmiany zostaną wprowadzone w bliźniaczej reprezentacji. 

Aby zmodyfikować konfigurację, użyj następujących kroków: 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do Centrum IoT hub. 

2. Wybierz **konfiguracji urządzenia IoT**. 

3. Wybierz konfigurację, którą chcesz zmodyfikować. 

4. Aktualizowanie następujące pola: 

   * Warunek docelowy 
   * Etykiety 
   * Priorytet 
   * Metryki

4. Wybierz pozycję **Zapisz**.

5. Postępuj zgodnie z instrukcjami w [monitorowanie konfiguracji](#monitor-a-configuration) obejrzeć zmiany wprowadzane. 

## <a name="delete-a-configuration"></a>Usuwanie konfiguracji

Możesz usunąć konfigurację, wszelkie bliźniaczych reprezentacji urządzeń przyjmą ich dalej konfiguracji najwyższy priorytet. Jeśli bliźniaczych reprezentacji urządzeń nie spełniają warunek docelowy innych konfiguracji, żadne inne ustawienia są stosowane. 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do Centrum IoT hub. 

2. Wybierz **konfiguracji urządzenia IoT**. 

3. Użyj pola wyboru, aby wybrać konfigurację, która ma zostać usunięty. 

4. Wybierz pozycję **Usuń**.

5. Monit zostanie wyświetlony monit o potwierdzenie.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób konfigurowania i monitorowania urządzeń IoT na dużą skalę. Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Zbiorcze zarządzanie tożsamościami urządzeń usługi IoT Hub](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Aby zapoznać się z pomocą IoT Hub Device Provisioning Service do włączenia aprowizacji bezobsługowe, just-in-time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](/azure/iot-dps)
