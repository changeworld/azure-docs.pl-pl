---
title: Wdrażanie, monitorowanie modułów dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zarządzanie modułów, które działają na urządzeniach brzegowych
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/25/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 28aa2904f63a9802305d24fec1650f84e38601ab
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258437"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Usługa Azure IoT Edge pozwala na przenoszenie analytics na urządzeniach brzegowych i zapewnia interfejs chmurowy dzięki czemu możesz zarządzać i monitorować urządzenia brzegowe IoT bez konieczności uzyskiwania fizycznego dostępu każdej z nich. Możliwość zdalnego zarządzania urządzeniami jest coraz ważniejsze rozwiązania Internetu rzeczy są coraz większej i bardziej złożonej. Usługa Azure IoT Edge jest przeznaczony do obsługi do celów biznesowych, niezależnie od tego, ile urządzeń, możesz dodać.

Możesz zarządzać poszczególnych urządzeń i wdrażać moduły do nich pojedynczo. Jednak jeśli chcesz wprowadzić zmiany do urządzeń na dużą skalę, możesz utworzyć **automatycznego wdrażania usługi IoT Edge**, który jest częścią automatyczne zarządzanie urządzeniami w usłudze IoT Hub. Wdrożenia są dynamiczne procesów, które umożliwiają jednocześnie wdrażać wielu modułów na wielu urządzeniach, śledzenia stanu i kondycji modułów i wprowadzić zmiany, gdy jest to konieczne. 

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń za pomocą tagów

Przed utworzeniem wdrożenia, musisz mieć możliwość określenia urządzeń, które mają wpływ na. Usługa Azure IoT Edge identyfikuje urządzenia przy użyciu **tagi** w bliźniaczej reprezentacji urządzenia. Każde urządzenie może mieć wiele tagów i można je zdefiniować sposób, który ma sens dla Twojego rozwiązania. Na przykład jeśli zarządzasz campus budynki, możesz dodać następujące znaczniki na urządzeniu:

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

Aby uzyskać więcej informacji na temat tagów i bliźniacze reprezentacje urządzeń, zobacz [poznawanie i używanie bliźniaczych reprezentacji urządzeń w usłudze IoT Hub][lnk-device-twin].

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

1. W [witryny Azure portal][lnk-portal], przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **Dodaj wdrożenie usługi IoT Edge**.

Istnieje pięć kroków, aby utworzyć wdrożenie. Poniższe sekcje przeprowadzą za pośrednictwem każdej z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Nazwa i etykieta

1. Nadaj wdrożenia unikatową nazwę, która jest maksymalnie 128 małe litery. Należy unikać miejsca do magazynowania i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.
1. Dodaj etykiety służące do śledzenia wdrożenia. Etykiety są **nazwa**, **wartość** pary, które opisują wdrożenia. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`.
1. Wybierz **dalej** aby przejść do kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2: Dodawanie modułów (opcjonalnie)

Istnieją dwa typy modułów, które można dodać do wdrożenia. Pierwsza to modułu na podstawie usługi platformy Azure, takich jak konto magazynu lub usługi Stream Analytics. Drugim jest modułu na podstawie własnego kodu. Do wdrożenia, można dodać wiele modułów dowolnego typu. 

Jeśli tworzysz wdrożenia bez modułów usuwa wszystkie bieżące moduły z urządzeń. 

>[!NOTE]
>Usługa Azure Machine Learning i Azure Functions nie obsługują jeszcze wdrożenie zautomatyzowane usługi platformy Azure. Aby ręcznie dodać tych usług dla danego wdrożenia, należy użyć wdrażania niestandardowego modułu. 

Aby dodać moduł z usługi Azure Stream Analytics, wykonaj następujące kroki:
1. W **moduły wdrożeń** części strony, kliknij przycisk **Dodaj**.
1. Wybierz **modułu usługi Azure Stream Analytics**.
1. Wybierz swoje **subskrypcji** z menu rozwijanego.
1. Wybierz swoje **zadanie Edge** z menu rozwijanego.
1. Wybierz **Zapisz** można dodać modułu do wdrożenia. 

Aby dodać niestandardowy kod jako moduł lub ręcznie Dodaj moduł usługi Azure, wykonaj następujące kroki:
1. W **ustawień rejestru** części strony, podaj nazwy i poświadczenia dla rejestrów dowolnego kontenera prywatnych, zawierających obrazy modułu dla tego wdrożenia. Agent usługi Edge będzie zgłaszać błąd 500, nie można znaleźć kontener usługi poświadczenia rejestru dla obrazu platformy docker.
1. W **moduły wdrożeń** części strony, kliknij przycisk **Dodaj**.
1. Wybierz **moduł usługi IoT Edge**.
1. Nadaj modułu **nazwa**.
1. Aby uzyskać **identyfikator URI obrazu** wprowadź obrazu kontenera dla modułu. 
1. Określ dowolne **opcje tworzenia kontenera** powinien zostać przekazany do kontenera. Aby uzyskać więcej informacji, zobacz [docker Utwórz][lnk-docker-create].
1. Użyj menu rozwijanego, aby wybrać **zasady ponownego uruchamiania**. Wybierz spośród następujących opcji: 
   * **Zawsze** — moduł ponowne uruchomienie zawsze, jeśli kończy pracę z dowolnego powodu.
   * **Nigdy nie** — moduł nigdy nie uruchamia ponownie, jeśli kończy pracę z dowolnego powodu.
   * **Nie powiodło się** — moduł spowoduje ponowne uruchomienie, jeśli awarii, ale nie Jeśli zamykania nie pozostawia żadnych śladów. 
   * **Na nieprawidłowości** — moduł spowoduje ponowne uruchomienie, jeśli ulega awarii, lub zwraca komunikat o złej kondycji. To Ty każdego modułu, aby zaimplementować funkcję stan kondycji. 
1. Użyj menu rozwijanego, aby wybrać **żądanego stanu** dla modułu. Wybierz spośród następujących opcji:
   * **Uruchamianie** — jest to opcja domyślna. Moduł zostanie uruchomione natychmiast po wdrożeniu.
   * **Zatrzymano** — po wdrożeniu moduł może pozostawać bezczynny, dopóki nie jest wymagane do uruchomienia przez Ciebie lub innego modułu.
1. Wybierz **Włącz** Jeśli chcesz dodać znaczniki lub żądane właściwości do bliźniaczej reprezentacji modułu. 
1. Wprowadź **zmienne środowiskowe** dla tego modułu. Zmienne środowiskowe zawierają informacje o dodatku do modułu, w ułatwienia procesu konfiguracji.
1. Wybierz **Zapisz** można dodać modułu do wdrożenia. 

Po utworzeniu wszystkich modułów dla wdrożenia skonfigurowane, wybierz **dalej** aby przejść do kroku 3.

### <a name="step-3-specify-routes-optional"></a>Krok 3: Określanie tras (opcjonalnie)

Trasy definiują, jak moduły komunikują się ze sobą w ramach danego wdrożenia. Domyślnie Kreator umożliwia trasy o nazwie **trasy** i zdefiniowane jako **FROM /* do $nadrzędne **, oznacza to, że wszystkie komunikaty generowane przez moduły są wysyłane do usługi IoT hub.  

Dodawanie lub aktualizowanie tras przy użyciu informacji z [zadeklarować trasy](module-composition.md#declare-routes), a następnie wybierz **dalej** można przejść do sekcji przeglądu.


### <a name="step-4-target-devices"></a>Krok 4: Urządzenia docelowe

Użyj właściwości tagi z urządzeniami pod kątem konkretnych urządzeń, które powinny otrzymać tego wdrożenia. 

Ponieważ wielu wdrożeń będących częścią mogą odnosić się do tego samego urządzenia, należy nadać kontu każdego wdrożenia numer priorytetu. Jeśli nigdy nie są zgodne, usługa wins wdrożenia o najwyższym priorytecie (wyższe wartości wskazują wyższy priorytet). Jeśli dwa wdrożenia mają ten sam numer priorytet, ten, który został utworzony w większości ostatnio wins. 

1. Wprowadź dodatnią liczbę całkowitą dla wdrożenia **priorytet**. W przypadku, gdy co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, będą stosowane wdrożenie o najwyższej wartości liczbowe dla priorytetu.
1. Wprowadź **warunek docelowy** do określenia urządzeń, które zostaną objęte tego wdrożenia. Warunek opiera się na tagów bliźniaczych reprezentacji urządzeń lub zgłoszonych właściwości bliźniaczej reprezentacji urządzenia, a powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.reported.devicemodel='4000x'`. 
1. Wybierz **dalej** można przenieść do ostatniego kroku.

### <a name="step-5-review-template"></a>Krok 5: Przegląd szablonu

Przejrzyj informacje o wdrożeniu, a następnie wybierz **przesyłania**.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Aby wyświetlić szczegóły wdrożenia i monitorowania urządzeń, w których jest on uruchomiony, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal] [ lnk-portal] i przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **wdrożenia usługi IoT Edge**. 

   ![Wyświetl wdrożenia usługi IoT Edge][1]

1. Sprawdź, czy lista wdrożenia. Dla każdego wdrożenia można wyświetlić następujące informacje:
   * **Identyfikator** — Nazwa wdrożenia.
   * **Warunek docelowy** -tag używane do definiowania urządzeń docelowych.
   * **Priorytet** — numer priorytetu, przypisanych do wdrożenia.
   * **Metryki systemu** - **docelowych** określa liczbę bliźniaczych reprezentacji urządzeń w usłudze IoT Hub, który odpowiada warunkowi określania wartości docelowej i **zastosowano** określa liczbę urządzeń, które mają Gdyby zawartości wdrożenia są stosowane do ich bliźniaczych reprezentacjach modułów usługi IoT Hub. 
   * **Metryki urządzenia** — liczba urządzeń brzegowych we wdrożeniu raportowania sukcesów lub błędów środowiska uruchomieniowego klienta usługi IoT Edge.
   * **Godzina utworzenia** -sygnaturę czasową od utworzenia wdrożenia. Sygnatura czasowa jest używany na przerwanie ties, gdy dwa wdrożenia mają ten sam priorytet. 
2. Wybierz wdrożenie, które chcesz monitorować.  
3. Sprawdź szczegóły wdrożenia. Można użyć karty, aby poznać szczegóły wdrożenia.

## <a name="modify-a-deployment"></a>Zmodyfikuj wdrożenie

Podczas modyfikowania wdrożenia zmiany są natychmiast replikowane do wszystkie objęte nimi urządzenia. 

Jeśli zaktualizujesz warunek docelowy, zachodzą następujące aktualizacje:
* Jeśli urządzenie nie spełnia warunek docelowy stare, ale nowy warunek docelowy spełnia, to wdrożenie ma najwyższy priorytet dla tego urządzenia to wdrożenie jest zastosowany na urządzeniu. 
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, odinstalowuje tego wdrożenia i Trwa dalej wdrożenia najwyższy priorytet. 
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy wszystkich innych wdrożeń, żadna zmiana występuje na urządzeniu. Urządzenie będzie nadal działać jego bieżący modułów w ich bieżący stan, ale nie jest zarządzany w ramach tego wdrożenia już. Gdy spełnia warunek docelowy wszystkich innych wdrożeń, odinstalowuje tego wdrożenia i przejście na nowy. 

Aby zmodyfikować wdrożenie, użyj następujących kroków: 

1. Zaloguj się do [witryny Azure portal] [ lnk-portal] i przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **wdrożenia usługi IoT Edge**. 

   ![Wyświetl wdrożenia usługi IoT Edge][1]

1. Wybierz wdrożenie, które chcesz zmodyfikować. 
1. Aktualizowanie następujące pola: 
   * Warunek docelowy 
   * Etykiety 
   * Priorytet 
1. Wybierz pozycję **Zapisz**.
1. Postępuj zgodnie z instrukcjami w [monitorowania wdrożeń] [ anchor-monitor] obejrzeć zmiany wprowadzane. 

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia żadnych urządzeń przyjmują ich dalej wdrożenia najwyższy priorytet. Jeśli urządzenia nie spełniają warunek docelowy wszystkich innych wdrożeń, następnie modułów nie są usuwane po usunięciu wdrożenia. 

1. Zaloguj się do [witryny Azure portal] [ lnk-portal] i przejdź do Centrum IoT hub. 
1. Wybierz **usługi IoT Edge**.
1. Wybierz **wdrożenia usługi IoT Edge**. 

   ![Wyświetl wdrożenia usługi IoT Edge][1]

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć. 
1. Wybierz pozycję **Usuń**.
1. Monit dowiesz się, że ta akcja spowoduje usunięcie tego wdrożenia i powrócić do poprzedniego stanu dla wszystkich urządzeń.  Oznacza to, że wdrożenie o niższym priorytecie zostaną zastosowane.  Jeśli jest przeznaczona żadnych innych wdrożeń, zostaną usunięte żadne moduły. Jeśli chcesz usunąć wszystkie moduły na urządzeniu, Utwórz wdrożenie z modułów, zerowego i wdrożyć ją na tych samych urządzeń. Wybierz **tak** aby kontynuować. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Wdrażanie modułów na urządzenia brzegowe][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
