---
title: Tworzenie automatycznego wdrożenia w witrynie Azure portal — usługa Azure IoT Edge | Dokumentacja firmy Microsoft
description: Tworzenie automatycznego wdrażania dla grup usługi IoT Edge urządzenia za pomocą witryny Azure portal
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f012d3e228a2730423c0d5a6f2cea7a8f2f9eab4
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190426"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal

Tworzenie **automatycznego wdrażania usługi IoT Edge** w witrynie Azure portal do zarządzania bieżących wdrożeń dla wielu urządzeń na raz. Automatyczne wdrażanie dla usługi IoT Edge są częścią [urządzenia automatycznego zarządzania](/iot-hub/iot-hub-automatic-device-management.md) funkcji usługi IoT Hub. Wdrożenia są dynamiczne procesów, które pozwalają na wdrażanie wiele modułów na wielu urządzeniach, śledzenia stanu i kondycji modułów i wprowadzić zmiany, gdy jest to konieczne. 

Aby uzyskać więcej informacji, zobacz [automatycznego wdrożenia zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń za pomocą tagów

Przed utworzeniem wdrożenia, musisz mieć możliwość określenia urządzeń, które mają wpływ na. Usługa Azure IoT Edge identyfikuje urządzenia przy użyciu **tagi** w bliźniaczej reprezentacji urządzenia. Każde urządzenie może mieć wiele tagów, które definiujesz w jakikolwiek sposób, który ma sens dla Twojego rozwiązania. Na przykład jeśli zarządzasz campus budynki, możesz dodać następujące znaczniki na urządzeniu:

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

Aby uzyskać więcej informacji na temat tagów i bliźniacze reprezentacje urządzeń, zobacz [poznawanie i używanie bliźniaczych reprezentacji urządzeń w usłudze IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

1. W [witryny Azure portal](https://portal.azure.com), przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **Dodaj wdrożenie usługi IoT Edge**.

Istnieje pięć kroków, aby utworzyć wdrożenie. Poniższe sekcje przeprowadzą za pośrednictwem każdej z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Nazwa i etykieta

1. Nadaj wdrożenia unikatową nazwę, która jest maksymalnie 128 małe litery. Należy unikać miejsca do magazynowania i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.
1. Można dodawać etykiety jako pary klucz wartość, aby śledzić wdrożenia. Na przykład **HostPlatform** i **Linux**, lub **wersji** i **3.0.1**.
1. Wybierz **dalej** aby przejść do kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2: Dodaj moduły (opcjonalnie)

Możesz dodać maksymalnie 20 modułów do wdrożenia. 

Jeśli tworzysz wdrożenia bez modułów usuwa wszystkie bieżące moduły z urządzeń docelowych. 

Aby dodać moduł z usługi Azure Stream Analytics, wykonaj następujące kroki:

1. W **moduły wdrożeń** części strony, kliknij przycisk **Dodaj**.
1. Wybierz **modułu usługi Azure Stream Analytics**.
1. Wybierz swoje **subskrypcji** z menu rozwijanego.
1. Wybierz IoT **zadanie Edge** z menu rozwijanego.
1. Wybierz **Zapisz** można dodać modułu do wdrożenia. 

Aby dodać niestandardowy kod jako moduł lub ręcznie Dodaj moduł usługi Azure, wykonaj następujące kroki:

1. W **ustawienia usługi Container Registry** części strony, podaj nazwy i poświadczenia dla rejestrów dowolnego kontenera prywatnych, zawierających obrazy modułu dla tego wdrożenia. Agent usługi IoT Edge będzie zgłaszać błąd 500, nie można znaleźć poświadczenia rejestru kontenera obrazu platformy Docker.
1. W **moduły wdrożeń** części strony, kliknij przycisk **Dodaj**.
1. Wybierz **moduł usługi IoT Edge**.
1. Nadaj modułu **nazwa**.
1. Aby uzyskać **identyfikator URI obrazu** wprowadź obrazu kontenera dla modułu. 
1. Określ dowolne **opcje tworzenia kontenera** powinien zostać przekazany do kontenera. Aby uzyskać więcej informacji, zobacz [docker Utwórz](https://docs.docker.com/engine/reference/commandline/create/).
1. Użyj menu rozwijanego, aby wybrać **zasady ponownego uruchamiania**. Wybierz spośród następujących opcji: 
   * **Zawsze** — moduł ponowne uruchomienie zawsze, jeśli kończy pracę z dowolnego powodu.
   * **Nigdy nie** — moduł nigdy nie uruchamia ponownie, jeśli kończy pracę z dowolnego powodu.
   * **w przypadku niepowodzenia** — moduł spowoduje ponowne uruchomienie, jeśli awarii, ale nie Jeśli zamykania nie pozostawia żadnych śladów. 
   * **Na nieprawidłowości** — moduł spowoduje ponowne uruchomienie, jeśli ulega awarii, lub zwraca komunikat o złej kondycji. To Ty każdego modułu, aby zaimplementować funkcję stan kondycji. 
1. Użyj menu rozwijanego, aby wybrać **żądanego stanu** dla modułu. Wybierz spośród następujących opcji:
   * **uruchamianie** — uruchomiona jest opcją domyślną. Moduł zostanie uruchomione natychmiast po wdrożeniu.
   * **Zatrzymano** — po wdrożeniu moduł może pozostawać bezczynny, dopóki nie jest wymagane do uruchomienia przez Ciebie lub innego modułu.
1. Wybierz **żądane właściwości zestawu modułu bliźniaczej reprezentacji** Jeśli chcesz dodać do bliźniaczej reprezentacji modułu tagi lub inne właściwości.
1. Wprowadź **zmienne środowiskowe** dla tego modułu. Zmienne środowiskowe zawierają informacje o konfiguracji do modułu.
1. Wybierz **Zapisz** można dodać modułu do wdrożenia. 

Po utworzeniu wszystkich modułów dla wdrożenia skonfigurowane, wybierz **dalej** aby przejść do kroku 3.

### <a name="step-3-specify-routes-optional"></a>Krok 3: Określanie tras (opcjonalnie)

Trasy definiują, jak moduły komunikują się ze sobą w ramach danego wdrożenia. Domyślnie Kreator umożliwia trasy o nazwie **trasy** i zdefiniowane jako **FROM /\* do $nadrzędne**, oznacza to, że wszystkie komunikaty generowane przez moduły są wysyłane do usługi IoT hub.  

Dodawanie lub aktualizowanie tras przy użyciu informacji z [zadeklarować trasy](module-composition.md#declare-routes), a następnie wybierz **dalej** można przejść do sekcji przeglądu.

### <a name="step-4-specify-metrics-optional"></a>Krok 4: Określ metryki (opcjonalnie)

Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie może zgłosić wyniku stosowania zawartość konfiguracji.

1. Wprowadź nazwę dla **Nazwa metryki**.

1. Wprowadź zapytanie dotyczące **kryteria metryki**. Zapytanie jest oparty na bliźniaczą reprezentację modułu Centrum IoT Edge [zgłaszanych właściwości](module-edgeagent-edgehub.md#edgehub-reported-properties). Metryka reprezentuje liczbę wierszy zwróconych przez zapytanie.

   Na przykład:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Krok 5. Urządzenia docelowe

Użyj właściwości tagi z urządzeniami pod kątem konkretnych urządzeń, które powinny otrzymać tego wdrożenia. 

Ponieważ wielu wdrożeń będących częścią mogą odnosić się do tego samego urządzenia, należy nadać kontu każdego wdrożenia numer priorytetu. Jeśli nigdy nie są zgodne, usługa wins wdrożenia o najwyższym priorytecie (wyższe wartości wskazać wyższy priorytet). Jeśli dwa wdrożenia mają ten sam numer priorytet, ten, który został utworzony w większości ostatnio wins. 

1. Wprowadź dodatnią liczbę całkowitą dla wdrożenia **priorytet**.
1. Wprowadź **warunek docelowy** do określenia urządzeń, które zostaną objęte tego wdrożenia. Warunek opiera się na tagów bliźniaczych reprezentacji urządzeń lub zgłoszonych właściwości bliźniaczej reprezentacji urządzenia, a powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.reported.devicemodel='4000x'`. 
1. Wybierz **dalej** można przenieść do ostatniego kroku.

### <a name="step-6-review-deployment"></a>Krok 6: Przegląd wdrożenia

Przejrzyj informacje o wdrożeniu, a następnie wybierz **przesyłania**.

## <a name="deploy-modules-from-azure-marketplace"></a>Wdrażać moduły z witryny Azure Marketplace

Witryna Azure Marketplace to rynek online aplikacji i usług, którego można przeglądać przy użyciu szerokiej gamy aplikacji i rozwiązań, które są certyfikowane i optymalizowane pod kątem działania na platformie Azure, w tym [moduły usługi IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Portal Azure Marketplace można także uzyskać dostęp za pośrednictwem witryny Azure portal w obszarze **Utwórz zasób**.

Aby wdrożyć moduł usługi IoT Edge z portalu Azure Marketplace lub w portalu Azure:

1. Znajdź moduł i rozpocząć proces wdrażania.

   * Witryna Azure Portal: Znajdź moduł i zaznacz **Utwórz**.

   * Azure Marketplace:

     1. Znajdź moduł i zaznacz **Pobierz teraz**.
     1. Potwierdzenie dostawcy warunki użycia i zasady zachowania poufności, wybierając **Kontynuuj**.

1. Wybierz subskrypcję i usługi IoT Hub, do której jest dołączona na urządzeniu docelowym.

1. Wybierz **wdrażanie w skali**.

1. Wybierz, czy dodanie modułu do nowe wdrożenie lub Sklonowanie istniejącego wdrożenia; Jeśli klonowanie, wybierz istniejące wdrożenie z listy.

1. Wybierz **Utwórz** aby kontynuować proces tworzenia wdrożenia na dużą skalę. Będzie można określić szczegóły tego samego, podobnie jak w przypadku każdego wdrożenia.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Aby wyświetlić szczegóły wdrożenia i monitorowania urządzeń, w których jest on uruchomiony, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **wdrożenia usługi IoT Edge**. 

   ![Wyświetl wdrożenia usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Sprawdź, czy lista wdrożenia. Dla każdego wdrożenia można wyświetlić następujące informacje:
   * **Identyfikator** — Nazwa wdrożenia.
   * **Warunek docelowy** -tag używane do definiowania urządzeń docelowych.
   * **Priorytet** — numer priorytetu, przypisanych do wdrożenia.
   * **Metryki systemu** - **docelowych** określa liczbę bliźniaczych reprezentacji urządzeń w usłudze IoT Hub, który odpowiada warunkowi określania wartości docelowej i **zastosowano** określa liczbę urządzeń, które mają Gdyby zawartości wdrożenia są stosowane do ich bliźniaczych reprezentacjach modułów usługi IoT Hub. 
   * **Metryki urządzenia** — liczba urządzeń usługi IoT Edge we wdrożeniu raportowania sukcesów lub błędów środowiska uruchomieniowego klienta usługi IoT Edge.
   * **Metryki niestandardowe** — liczba urządzeń usługi IoT Edge we wdrożeniu, zgłoszenie danych dla dowolnego metryk, która jest zdefiniowana dla tego wdrożenia.
   * **Godzina utworzenia** -sygnaturę czasową od utworzenia wdrożenia. Sygnatura czasowa jest używany na przerwanie ties, gdy dwa wdrożenia mają ten sam priorytet. 
1. Wybierz wdrożenie, które chcesz monitorować.  
1. Sprawdź szczegóły wdrożenia. Można użyć karty, aby poznać szczegóły wdrożenia.

## <a name="modify-a-deployment"></a>Zmodyfikuj wdrożenie

Podczas modyfikowania wdrożenia zmiany są natychmiast replikowane do wszystkie objęte nimi urządzenia. 

Jeśli zaktualizujesz warunek docelowy, zachodzą następujące aktualizacje:

* Jeśli urządzenie nie spełnia warunek docelowy stare, ale nowy warunek docelowy spełnia, to wdrożenie ma najwyższy priorytet dla tego urządzenia to wdrożenie jest zastosowany na urządzeniu. 
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, odinstalowuje tego wdrożenia i Trwa dalej wdrożenia najwyższy priorytet. 
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy wszystkich innych wdrożeń, żadna zmiana występuje na urządzeniu. Urządzenie będzie nadal działać jego bieżący modułów w ich bieżący stan, ale nie jest zarządzany w ramach tego wdrożenia już. Gdy spełnia warunek docelowy wszystkich innych wdrożeń, odinstalowuje tego wdrożenia i przejście na nowy. 

Aby zmodyfikować wdrożenie, użyj następujących kroków: 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **wdrożenia usługi IoT Edge**. 

   ![Wyświetl wdrożenia usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Wybierz wdrożenie, które chcesz zmodyfikować. 
1. Aktualizowanie następujące pola: 
   * Warunek docelowy
   * Metryki — można modyfikować ani usuwać metryki został zdefiniowany, lub dodać nowe.
   * Etykiety
   * Priorytet
1. Wybierz pozycję **Zapisz**.
1. Postępuj zgodnie z instrukcjami w [monitorowania wdrożeń](#monitor-a-deployment) obejrzeć zmiany wprowadzane. 

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia żadnych urządzeń przyjmują ich dalej wdrożenia najwyższy priorytet. Jeśli urządzenia nie spełniają warunek docelowy wszystkich innych wdrożeń, następnie modułów nie są usuwane po usunięciu wdrożenia. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **wdrożenia usługi IoT Edge**. 

   ![Wyświetl wdrożenia usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć. 
1. Wybierz pozycję **Usuń**.
1. Monit dowiesz się, że ta akcja spowoduje usunięcie tego wdrożenia i powrócić do poprzedniego stanu dla wszystkich urządzeń.  Oznacza to, że wdrożenie o niższym priorytecie zostaną zastosowane.  Jeśli jest przeznaczona żadnych innych wdrożeń, zostaną usunięte żadne moduły. Jeśli chcesz usunąć wszystkie moduły na urządzeniu, Utwórz wdrożenie z modułów, zerowego i wdrożyć ją na tych samych urządzeń. Wybierz **tak** aby kontynuować. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Wdrażanie modułów na urządzeniach usługi IoT Edge](module-deployment-monitoring.md).
