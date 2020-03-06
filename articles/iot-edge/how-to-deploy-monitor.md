---
title: Wdrażanie modułów na dużą skalę Azure Portal — Azure IoT Edge
description: Tworzenie automatycznego wdrażania dla grup usługi IoT Edge urządzenia za pomocą witryny Azure portal
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396749"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal

Utwórz **IoT Edge Automatyczne wdrażanie** w Azure Portal, aby zarządzać trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń za pomocą tagów

Przed utworzeniem wdrożenia, musisz mieć możliwość określenia urządzeń, które mają wpływ na. Azure IoT Edge identyfikuje urządzenia przy użyciu **tagów** z sznurka urządzenia. Każde urządzenie może mieć wiele tagów zdefiniowanych w dowolny sposób, który ma sens dla danego rozwiązania.

Na przykład, Jeśli zarządzasz kampusami z inteligentnymi budynkami, możesz dodać do urządzenia następujące znaczniki lokalizacji, typu pokoju i środowiska:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji i tagów urządzeń, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

IoT Edge oferuje dwa różne typy wdrożeń automatycznych, których można użyć do dostosowania scenariusza. Można utworzyć standardowe *wdrożenie*, które obejmuje moduły środowiska uruchomieniowego systemu oraz dodatkowe moduły i trasy. Każde urządzenie może zastosować tylko jedno wdrożenie. Można też utworzyć *wdrożenie warstwowe*, które obejmuje tylko niestandardowe moduły i trasy, a nie środowisko uruchomieniowe systemu. Wiele wdrożeń warstwowych można łączyć na urządzeniu na podstawie standardowego wdrożenia. Aby uzyskać więcej informacji o tym, jak dwa typy wdrożeń automatycznych współpracują ze sobą, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

Kroki związane z tworzeniem wdrożenia i wdrożeniem warstwowym są bardzo podobne. Wszelkie różnice są wywoływane w poniższych krokach.

1. W [Azure Portal](https://portal.azure.com)przejdź do IoT Hub.
1. W menu w lewym okienku wybierz pozycję **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami**.
1. Na górnym pasku wybierz pozycję **Utwórz wdrożenie** lub **Utwórz wdrożenie warstwowe**.

Istnieje pięć kroków, aby utworzyć wdrożenie. Poniższe sekcje przeprowadzą za pośrednictwem każdej z nich.

### <a name="step-1-name-and-label"></a>Krok 1. nazwa i etykieta

1. Nadaj wdrożenia unikatową nazwę, która jest maksymalnie 128 małe litery. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /`.
1. Etykiety można dodawać jako pary klucz-wartość, aby ułatwić śledzenie wdrożeń. Na przykład **HostPlatform** i **Linux**, lub **wersja** i **3.0.1**.
1. Wybierz kolejno pozycje **Następny: moduły** , aby przejść do kroku 2.

### <a name="step-2-modules"></a>Krok 2. moduły

Do wdrożenia można dodać maksymalnie 20 modułów. Jeśli utworzysz wdrożenie bez modułów, spowoduje to usunięcie wszystkich bieżących modułów z urządzeń docelowych.

W obszarze wdrożenia można zarządzać ustawieniami agenta IoT Edge i IoT Edge modułów centrów. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** , aby skonfigurować dwa moduły środowiska uruchomieniowego. W przypadku wdrażania warstwowego moduły środowiska uruchomieniowego nie są uwzględniane, więc nie można ich skonfigurować.

Można dodać trzy typy modułów:

* Moduł IoT Edge
* Moduł portalu Marketplace
* Moduł Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Dodawanie modułu IoT Edge

Aby dodać niestandardowy kod jako moduł lub ręcznie Dodaj moduł usługi Azure, wykonaj następujące kroki:

1. W sekcji **poświadczenia Container Registry** na stronie Podaj nazwy i poświadczenia dla prywatnych rejestrów kontenerów, które zawierają obrazy modułów dla tego wdrożenia. Agent IoT Edge zgłosi błąd 500, jeśli nie można znaleźć poświadczenia rejestru kontenera dla obrazu platformy Docker.
1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł IoT Edge** z menu rozwijanego.
1. Nadaj modułowi **IoT Edge nazwę modułu**.
1. W polu **Identyfikator URI obrazu** wprowadź obraz kontenera dla modułu.
1. Użyj menu rozwijanego, aby wybrać **zasady ponownego uruchamiania**. Wybierz spośród następujących opcji:
   * **zawsze** — moduł zawsze jest uruchamiany ponownie, jeśli z jakiegoś powodu zostanie zamknięty.
   * **nigdy** — moduł nigdy nie jest ponownie uruchamiany, jeśli z jakiegoś powodu zostanie zamknięty.
   * w przypadku niepowodzenia — moduł jest uruchamiany ponownie w przypadku awarii, ale nie w przypadku jego **nieprawidłowego** zamknięcia.
   * **w złej kondycji** — moduł jest uruchamiany ponownie, jeśli ulegnie awarii lub zwróci stan złej kondycji. To Ty każdego modułu, aby zaimplementować funkcję stan kondycji.
1. Użyj menu rozwijanego, aby wybrać **żądany stan** modułu. Wybierz spośród następujących opcji:
   * **uruchomiona** jest opcja domyślna. Moduł zostanie uruchomione natychmiast po wdrożeniu.
   * **zatrzymano** — po wdrożeniu moduł pozostanie bezczynny do momentu wywołania przez użytkownika lub innego modułu.
1. Określ wszelkie **Opcje tworzenia kontenera** , które powinny być przesyłane do kontenera. Aby uzyskać więcej informacji, zobacz [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Wybierz pozycję **Ustawienia sznurka modułu** , jeśli chcesz dodać Tagi lub inne właściwości do sznurka modułu.
1. Wprowadź **zmienne środowiskowe** dla tego modułu. Zmienne środowiskowe udostępniają informacje o konfiguracji do modułu.
1. Wybierz pozycję **Dodaj** , aby dodać moduł do wdrożenia.

#### <a name="add-a-module-from-the-marketplace"></a>Dodawanie modułu z portalu Marketplace

Aby dodać moduł z portalu Azure Marketplace, wykonaj następujące kroki:

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł Marketplace** z menu rozwijanego.
1. Wybierz moduł na stronie **witryny Marketplace modułu IoT Edge** . Wybrany moduł jest automatycznie konfigurowany dla Twojej subskrypcji, grupy zasobów i urządzenia. Zostanie ona wyświetlona na liście modułów IoT Edge. Niektóre moduły mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz [wdrażanie modułów z witryny Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Dodaj moduł Stream Analytics

Aby dodać moduł z usługi Azure Stream Analytics, wykonaj następujące kroki:

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł Azure Stream Analytics** z menu rozwijanego.
1. W prawym okienku wybierz swoją **subskrypcję**.
1. Wybierz zadanie IoT **Edge**.
1. Wybierz pozycję **Zapisz** , aby dodać moduł do wdrożenia.

#### <a name="configure-module-settings"></a>Konfigurowanie ustawień modułu

Po dodaniu modułu do wdrożenia można wybrać jego nazwę, aby otworzyć stronę **modułu aktualizacji IoT Edge** . Na tej stronie można edytować ustawienia modułu, zmienne środowiskowe, opcje tworzenia i sznurki modułowe. Jeśli dodano moduł z portalu Marketplace, może on mieć już wypełnione niektóre parametry.

W przypadku tworzenia wdrożenia warstwowego można skonfigurować moduł, który istnieje w innych wdrożeniach przeznaczonych dla tych samych urządzeń. Aby zaktualizować splot modułu bez zastępowania innych wersji, Otwórz kartę **Ustawienia sznurka modułu** . Utwórz nową **Właściwość sznurka modułu** o unikatowej nazwie dla podsekcji w odpowiednich właściwościach sznurka modułu, na przykład `properties.desired.settings`. Jeśli zdefiniujesz właściwości w tylko `properties.desired` polu, spowoduje to zastąpienie żądanych właściwości modułu zdefiniowanego we wdrożeniach o niższym priorytecie.

![Ustaw właściwość sznurka modułu dla wdrożenia warstwowego](./media/how-to-deploy-monitor/module-twin-property.png)

Aby uzyskać więcej informacji na temat konfiguracji sznurka modułu w przypadku wdrożeń warstwowych, zobacz [wdrażanie warstwowe](module-deployment-monitoring.md#layered-deployment).

Po skonfigurowaniu wszystkich modułów dla wdrożenia wybierz pozycję **Dalej: trasy** , które mają zostać przeniesione do kroku 3.

### <a name="step-3-routes"></a>Krok 3. trasy

Trasy definiują, jak moduły komunikują się ze sobą w ramach danego wdrożenia. Domyślnie Kreator udostępnia trasę o nazwie **nadrzędny** i zdefiniowany jako **od/messages/\* do $upstream**, co oznacza, że wszystkie komunikaty przesyłane przez wszystkie moduły są wysyłane do centrum IoT Hub.  

Dodaj lub zaktualizuj trasy z informacjami z [deklaracji trasy](module-composition.md#declare-routes), a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd.

Wybierz pozycję **Dalej: metryki**.

### <a name="step-4-metrics"></a>Krok 4. metryki

Metryki zawierają podsumowanie liczb różnych stanów, które urządzenie może zgłosić z powrotem w wyniku zastosowania zawartości konfiguracji.

1. Wprowadź nazwę dla **nazwy metryki**.

1. Wprowadź zapytanie dla **kryteriów metryki**. Zapytanie jest oparte na IoT Edge [raportowanych właściwościach](module-edgeagent-edgehub.md#edgehub-reported-properties)modułu centrum. Metryka reprezentuje liczbę wierszy zwracanych przez zapytanie.

   Na przykład:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Wybierz pozycję **Dalej: urządzenia docelowe**.

### <a name="step-5-target-devices"></a>Krok 5. urządzenia docelowe

Użyj właściwości tagi z urządzeniami pod kątem konkretnych urządzeń, które powinny otrzymać tego wdrożenia.

Ponieważ wielu wdrożeń będących częścią mogą odnosić się do tego samego urządzenia, należy nadać kontu każdego wdrożenia numer priorytetu. Jeśli kiedykolwiek wystąpi konflikt, wdrożenie o najwyższym priorytecie (większe wartości wskazują wyższy priorytet) WINS. Jeśli dwa wdrożenia mają ten sam numer priorytet, ten, który został utworzony w większości ostatnio wins.

Jeśli wiele wdrożeń jest przeznaczonych dla tego samego urządzenia, zostanie zastosowany tylko ten z wyższym priorytetem. Jeśli wiele wdrożeń warstwowych jest przeznaczonych dla tego samego urządzenia, zostaną one zastosowane. Jednakże jeśli wszystkie właściwości są zduplikowane, na przykład jeśli istnieją dwie trasy o tej samej nazwie, to jeden z wyższych priorytetów wdrożenia warstwowego zastępuje resztę.

Każde wdrożenie warstwowe ukierunkowane na urządzenie musi mieć wyższy priorytet niż wdrożenie podstawowe, aby można je było zastosować.

1. Wprowadź dodatnią liczbę całkowitą dla **priorytetu**wdrożenia.
1. Wprowadź **warunek docelowy** , aby określić, które urządzenia będą ukierunkowane na to wdrożenie. Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.reported.devicemodel='4000x'`.

Wybierz pozycję **Dalej: Przejrzyj i Utwórz** , aby przejść do ostatniego kroku.

### <a name="step-6-review-and-create"></a>Krok 6. przegląd i tworzenie

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Aby wyświetlić szczegóły wdrożenia i monitorowania urządzeń, w których jest on uruchomiony, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.
1. Wybierz **IoT Edge**.
1. Wybierz kartę **wdrożenia IoT Edge** .

   ![Wyświetl wdrożenia usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Sprawdź, czy lista wdrożenia. Dla każdego wdrożenia można wyświetlić następujące informacje:
   * **Identyfikator** — nazwa wdrożenia.
   * **Typ** — **typ wdrożenia, wdrożenie lub** **wdrożenie warstwowe**.
   * **Warunek docelowy** — tag używany do definiowania urządzeń docelowych.
   * **Priorytet** — numer priorytetu przypisany do wdrożenia.
   * **Metryki systemu** - określania wartości **docelowej** określa liczbę urządzeń w IoT Hub, które pasują do warunku określania wartości docelowej, i **zastosowano** określa liczbę urządzeń, na których zastosowano zawartość wdrożenia do ich modułu bliźniaczych reprezentacji w IoT Hub.
   * **Metryki urządzeń** — liczba urządzeń IoT Edge w przypadku powodzenia lub błędów raportowania wdrożenia w środowisku uruchomieniowym klienta IoT Edge.
   * **Metryki niestandardowe** — liczba IoT Edge urządzeń w danych raportowania wdrożenia dla wszystkich metryk zdefiniowanych dla wdrożenia.
   * **Czas utworzenia** — sygnatura czasowa od momentu utworzenia wdrożenia. Sygnatura czasowa jest używany na przerwanie ties, gdy dwa wdrożenia mają ten sam priorytet.
1. Wybierz wdrożenie, które chcesz monitorować.  
1. Sprawdź szczegóły wdrożenia. Można użyć karty, aby poznać szczegóły wdrożenia.

## <a name="modify-a-deployment"></a>Zmodyfikuj wdrożenie

Podczas modyfikowania wdrożenia zmiany są natychmiast replikowane do wszystkie objęte nimi urządzenia.

Jeśli zaktualizujesz warunek docelowy, zachodzą następujące aktualizacje:

* Jeśli urządzenie nie spełnia warunek docelowy stare, ale nowy warunek docelowy spełnia, to wdrożenie ma najwyższy priorytet dla tego urządzenia to wdrożenie jest zastosowany na urządzeniu.
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, odinstalowuje tego wdrożenia i Trwa dalej wdrożenia najwyższy priorytet.
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy wszystkich innych wdrożeń, żadna zmiana występuje na urządzeniu. Urządzenie będzie nadal działać jego bieżący modułów w ich bieżący stan, ale nie jest zarządzany w ramach tego wdrożenia już. Gdy spełnia warunek docelowy wszystkich innych wdrożeń, odinstalowuje tego wdrożenia i przejście na nowy.

Aby zmodyfikować wdrożenie, użyj następujących kroków:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.
1. Wybierz **IoT Edge**.
1. Wybierz kartę **wdrożenia IoT Edge** .

   ![Wyświetl wdrożenia usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Wybierz wdrożenie, które chcesz zmodyfikować.
1. Wykonaj aktualizacje na następujących kartach:
   * **Warunek docelowy**
   * **Metryki** — można modyfikować lub usuwać zdefiniowane metryki lub dodawać nowe.
   * **Etykiety**
   * **Moduły**
   * **Rozsyłan**
   * **Wdrożenie**

1. Wybierz pozycję **Zapisz**.
1. Wykonaj kroki opisane w sekcji [monitorowanie wdrożenia](#monitor-a-deployment) , aby obejrzeć zmiany.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie wdrożone urządzenia przyjmą kolejne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunek docelowy wszystkich innych wdrożeń, następnie modułów nie są usuwane po usunięciu wdrożenia.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.
1. Wybierz **IoT Edge**.
1. Wybierz kartę **wdrożenia IoT Edge** .

   ![Wyświetl wdrożenia usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć.
1. Wybierz pozycję **Usuń**.
1. Monit dowiesz się, że ta akcja spowoduje usunięcie tego wdrożenia i powrócić do poprzedniego stanu dla wszystkich urządzeń.  Oznacza to, że zostanie zastosowane wdrożenie o niższym priorytecie. Jeśli żadne inne wdrożenie nie jest wskazywane, moduły nie zostaną usunięte. Jeśli chcesz usunąć wszystkie moduły na urządzeniu, Utwórz wdrożenie z modułów, zerowego i wdrożyć ją na tych samych urządzeń. Wybierz pozycję **tak** , aby kontynuować.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).
