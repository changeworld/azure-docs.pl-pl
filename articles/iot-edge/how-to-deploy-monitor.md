---
title: Wdrażanie, monitorowanie modułów Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zarządzanie modułów, które działają na urządzeniach krawędzi w
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: be52a57f10f286bded9a31d84b36a49717b94006
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029761"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę przy użyciu portalu Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Krawędź IoT Azure umożliwia przenoszenie analytics krawędzi i udostępnia interfejs chmury, dzięki czemu można zarządzać i monitorować urządzenia IoT krawędzi bez konieczności fizycznie dostęp do każdej z nich. Możliwość zdalnego zarządzania urządzeniami jest coraz bardziej istotne, ponieważ Internetu rzeczy rozwiązań rośnie większej i bardziej złożonej. Azure IoT krawędzi służy do celów biznesowych, niezależnie od tego, ile urządzeń, możesz dodać.

Można zarządzać poszczególnych urządzeń i wdrożyć moduły pojedynczo. Jednak jeśli chcesz wprowadzić zmiany na urządzeniach na dużą skalę, możesz utworzyć **wdrażania automatycznego krawędzi IoT**, który jest częścią automatyczne zarządzanie urządzeniami w Centrum IoT. Wdrożenia są dynamiczne procesów, które można wdrożyć wiele modułów jednocześnie na wielu urządzeniach, śledzić stan i kondycję moduły i wprowadzić zmiany, gdy jest to konieczne. 

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia, należy być w stanie określić, które mają wpływ na urządzenia. Azure IoT krawędzi identyfikuje urządzeń przy użyciu **tagi** w dwie urządzenia. Każde urządzenie może mieć wiele tagów i można je zdefiniować sposób, który ma sens dla rozwiązania. Na przykład jeśli zarządzasz firmy inteligentne budynków, można dodać następujących tagów do urządzenia:

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

Aby uzyskać więcej informacji na temat twins urządzenia i tagów, zobacz [opis i użyj twins urządzenie w Centrum IoT][lnk-device-twin].

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

1. W [portalu Azure][lnk-portal], przejdź do Centrum IoT. 
1. Wybierz **krawędzi IoT**.
1. Wybierz **Dodaj wdrożenie krawędzi IoT**.

Brak pięć kroków, aby utworzyć wdrożenie. Poniższe sekcje przeprowadzenie każdej z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Nazwa i etykieta

1. Nadaj unikatową nazwę, która jest maksymalnie 128 małe litery wdrożenia. Unikaj spacje i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.
1. Dodawanie etykiet, aby ułatwić śledzenie wdrożeń. Etykiety są **nazwa**, **wartość** pary, które opisują wdrożenia. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`.
1. Wybierz **dalej** aby przejść do kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2: Dodawanie modułów (opcjonalnie)

Istnieją dwa typy modułów, które można dodać do wdrożenia. Pierwsza to modułu na podstawie usługi platformy Azure, takich jak konta magazynu lub Stream Analytics. Drugim jest modułu na podstawie swoim własnym kodem. Wiele modułów obu typów można dodać do wdrożenia. 

Po utworzeniu wdrożenia z modułów nie usuwa żadnych modułów bieżącego z urządzeń. 

>[!NOTE]
>Uczenie maszynowe Azure i usługi Azure Functions nie obsługuje jeszcze wdrożenie zautomatyzowane usługi Azure. Aby ręcznie dodać tych usług do wdrożenia przy użyciu wdrażania niestandardowego modułu. 

Aby dodać moduł z usługi Azure Stream Analytics, wykonaj następujące kroki:
1. W **moduły wdrożeń** części strony, kliknij przycisk **Dodaj**.
1. Wybierz **modułu Azure Stream Analytics**.
1. Wybierz użytkownika **subskrypcji** z menu rozwijanego.
1. Wybierz użytkownika **zadania krawędzi** z menu rozwijanego.
1. Wybierz **zapisać** można dodać modułu do wdrożenia. 

Aby dodać kod niestandardowy jako moduł lub ręcznie Dodaj moduł usługi Azure, wykonaj następujące kroki:
1. W **ustawień rejestru** części strony, podaj nazwy i poświadczeń dla dowolnego rejestrów Kontener prywatny zawierających obrazy modułu dla tego wdrożenia. Agent krawędzi zgłosi błąd 500 contrainer poświadczeń rejestru nie można znaleźć obrazu docker.
1. W **moduły wdrożeń** części strony, kliknij przycisk **Dodaj**.
1. Wybierz **krawędzi IoT modułu**.
1. Nadaj modułu **nazwa**.
1. Aby uzyskać **identyfikatora URI obrazu** wprowadź obrazu kontener dla modułu. 
1. Określ dowolne **opcje tworzenia kontenera** powinien zostać przekazany do kontenera. Aby uzyskać więcej informacji, zobacz [docker Utwórz][lnk-docker-create].
1. Użyj menu rozwijanego, aby wybrać **ponowne uruchomienie zasad**. Wybierz spośród następujących opcji: 
   * **Zawsze** — moduł zawsze zostanie ponownie uruchomiony Jeśli zamykania różnych przyczyn.
   * **Nigdy nie** — moduł nigdy nie uruchamia ponownie Jeśli zamykania różnych przyczyn.
   * **Nie powiodło się** — moduł ponowne uruchomienie, jeśli awarii, ale nie Jeśli zostanie zamknięty prawidłowo. 
   * **Na zła** — moduł ponowne uruchomienie, jeśli jest on awarii lub zwraca stan złej kondycji. To poszczególnych modułów do implementowania funkcji stan kondycji. 
1. Użyj menu rozwijanego, aby wybrać **żądanego stanu** dla modułu. Wybierz spośród następujących opcji:
   * **Uruchomiona** — jest to opcja domyślna. Moduł zostanie uruchomione natychmiast po wdrożeniu.
   * **Zatrzymano** — po wdrożeniu, moduł może pozostawać bezczynny, aż do uruchomienia przez użytkownika lub innego modułu.
1. Wybierz **włączyć** Jeśli chcesz dodać do modułu dwie znaczniki lub odpowiednie właściwości. 
1. Wprowadź **zmiennych środowiskowych** dla tego modułu. Zmienne środowiskowe zawierają informacje o dodatku do ułatwienia procesu konfiguracji modułu.
1. Wybierz **zapisać** można dodać modułu do wdrożenia. 

Po utworzeniu wszystkich modułów wdrożenia skonfigurowane, wybierz **dalej** aby przejść do kroku 3.

### <a name="step-3-specify-routes-optional"></a>Krok 3: Określ trasy (opcjonalnie)

Trasy zdefiniuj, jak moduły komunikują się ze sobą w ramach danego wdrożenia. Domyślnie Kreator pozwala trasa o nazwie **trasy** i zdefiniowane jako **FROM /* do górnej $**, co oznacza, że wszystkie komunikaty wyjściowe przez wszystkie moduły są wysyłane do Centrum IoT.  

Dodaj lub zaktualizuj trasy z informacjami z [zadeklarować tras](module-composition.md#declare-routes), a następnie wybierz pozycję **dalej** w dalszym ciągu w sekcji Przegląd.


### <a name="step-4-target-devices"></a>Krok 4: Urządzeń docelowych

Użyj właściwości tagi z urządzeniami pod kątem określonych urządzeń, które powinien zostać wyświetlony tego wdrożenia. 

Ponieważ wielu wdrożeń mogą odnosić się do tego samego urządzenia, należy nadać każdego wdrożenia priorytety. Jeśli kiedykolwiek występuje konflikt, wins wdrożenia z najwyższym priorytetem (wyższy priorytet wskazać wyższe wartości). Jeśli dwa wdrożenia mają ten sam numer priorytet, który został utworzony w większości ostatnio wins. 

1. Wprowadź dodatnią liczbę całkowitą dla wdrożenia **priorytet**. W przypadku, gdy co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, będą miały zastosowania wdrożenia z najwyższą wartością liczbową priorytetu.
1. Wprowadź **Target warunku** do określenia urządzeń, które będą objęte z tego wdrożenia. Warunek jest oparta na tagi dwie urządzenia lub dwie urządzenia żądanego właściwości i powinny być zgodne z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.desired.devicemodel='4000x'`. 
1. Wybierz **dalej** można przenieść do ostatniego kroku.

### <a name="step-5-review-template"></a>Krok 5: Szablon przeglądu

Przejrzyj informacje dotyczące wdrażania, a następnie wybierz **przesyłania**.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Aby wyświetlić szczegóły wdrożenia i monitorowania urządzeń, uruchomienie jej, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure] [ lnk-portal] i przejdź do Centrum IoT. 
1. Wybierz **krawędzi IoT**.
1. Wybierz **krawędzi IoT wdrożeń**. 

   ![Widok krawędzi IoT wdrożenia][1]

1. Sprawdź, czy na liście wdrożenia. Dla każdego wdrożenia można wyświetlić następujące informacje:
   * **Identyfikator** — Nazwa wdrożenia.
   * **Docelowa warunku** -tag używane do definiowania urządzeń docelowych.
   * **Priorytet** — numer priorytet przypisany do wdrożenia.
   * **Metryki systemu** - **docelowych** określa liczbę twins urządzenie w Centrum IoT, spełniających warunek określania wartości docelowej i **zastosowano** określa liczbę urządzeń, które mają zawartości wdrożenia były stosowane do ich twins modułu w Centrum IoT. 
   * **Metryki urządzenia** -liczba urządzenia brzegowe we wdrożeniu raportowania powodzenie lub błędy środowiska uruchomieniowego klienta IoT krawędzi.
   * **Czas utworzenia** -znacznik czasu od utworzenia wdrożenia. Sygnatura czasowa służy do Przerwij ties, gdy dwa wdrożenia mają ten sam priorytet. 
2. Wybierz wdrożenie, które chcesz monitorować.  
3. Sprawdź, czy szczegóły wdrożenia. Korzystania z kart, aby wyświetlić szczegóły wdrożenia.

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany natychmiast replikowane do wszystkich urządzeń docelowych. 

Po zaktualizowaniu warunek docelowy są wykonywane następujące aktualizacje:
* Jeśli urządzenie nie spełnia warunek docelowy stare, ale spełnia nowy warunek docelowy i to wdrożenie jest najwyższy priorytet dla tego urządzenia, to wdrożenie jest zastosowany na urządzeniu. 
* Jeśli urządzenie aktualnie uruchomione tego wdrożenia nie spełnia warunek docelowy, odinstalowuje tego wdrożenia i przejmuje dalej wdrożenia najwyższy priorytet. 
* Jeśli urządzenie aktualnie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy innych wdrożeń, zmiany nie występuje na urządzeniu. Urządzenia wciąż trwa jego bieżący modułów w jego bieżącym stanie, ale nie jest zarządzana w ramach już tego wdrożenia. Po spełnia warunek docelowy inne wdrożenia, odinstalowuje tego wdrożenia i przejście na nowy. 

Aby zmodyfikować wdrożenia, użyj następujących kroków: 

1. Zaloguj się do [portalu Azure] [ lnk-portal] i przejdź do Centrum IoT. 
1. Wybierz **krawędzi IoT**.
1. Wybierz **krawędzi IoT wdrożeń**. 

   ![Widok krawędzi IoT wdrożenia][1]

1. Wybierz wdrożenie, które chcesz zmodyfikować. 
1. Aktualizowanie następujące pola: 
   * Warunek docelowy 
   * Etykiety 
   * Priorytet 
1. Wybierz pozycję **Zapisz**.
1. Postępuj zgodnie z instrukcjami [monitorowania wdrożeń] [ anchor-monitor] Aby obejrzeć zmiany wdrożyć. 

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia żadnych urządzeń przełączyć na ich dalej wdrożenia najwyższy priorytet. Jeśli urządzenia nie spełniają warunek docelowy inne wdrożenia, następnie moduły nie zostaną usunięte po usunięciu wdrożenia. 

1. Zaloguj się do [portalu Azure] [ lnk-portal] i przejdź do Centrum IoT. 
1. Wybierz **krawędzi IoT**.
1. Wybierz **krawędzi IoT wdrożeń**. 

   ![Widok krawędzi IoT wdrożenia][1]

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć. 
1. Wybierz pozycję **Usuń**.
1. Monit informuje, że ta akcja spowoduje to usunięcie tego wdrożenia i powrócić do poprzedniego stanu dla wszystkich urządzeń.  Oznacza to, że wdrożenie o niższym priorytecie zostaną zastosowane.  Jeśli jest docelowym dla żadnych innych wdrożeń, moduły nie zostaną usunięte. Jeśli chcesz usunąć wszystkie moduły z urządzenia, wdrożenie z modułów zero i wdróż je do tego samego urządzenia. Wybierz **tak** aby kontynuować. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wdrożenie modułów urządzenia brzegowe][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
