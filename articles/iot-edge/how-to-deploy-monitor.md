---
title: Wdrażanie modułów na dużą skalę w witrynie Azure portal — usługa Azure IoT Edge
description: Tworzenie automatycznych wdrożeń dla grup urządzeń usługi IoT Edge za pomocą witryny Azure Portal
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271436"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażanie i monitorowanie modułów usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal

Utwórz **automatyczne wdrożenie usługi IoT Edge** w witrynie Azure portal, aby zarządzać bieżącymi wdrożeniami na wielu urządzeniach jednocześnie. Automatyczne wdrożenia usługi IoT Edge są częścią funkcji [automatycznego zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) usługi IoT Hub. Wdrożenia są procesami dynamicznymi, które umożliwiają wdrażanie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [Opis automatycznych wdrożeń usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę.](module-deployment-monitoring.md)

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu znaczników

Przed utworzeniem wdrożenia należy określić, na które urządzenia mają mieć wpływ. Usługa Azure IoT Edge identyfikuje urządzenia przy użyciu tagów w bliźniaczej **reprezentacji** urządzenia. Każde urządzenie może mieć wiele tagów, które można zdefiniować w dowolny sposób, który ma sens dla rozwiązania.

Jeśli na przykład zarządzasz kampusem inteligentnych budynków, możesz dodać do urządzenia znaczniki lokalizacji, typu pomieszczenia i środowiska:

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

Aby uzyskać więcej informacji na temat bliźniąt bliźniąt urządzeń i tagów, zobacz [Opis bliźniaczych urządzeń i używanie ich w U.](../iot-hub/iot-hub-devguide-device-twins.md)

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Usługi IoT Edge udostępnia dwa różne typy wdrożeń automatycznych, których można użyć do dostosowania scenariusza. Można utworzyć standardowe *wdrożenie,* które obejmuje moduły środowiska wykonawczego systemu oraz wszelkie dodatkowe moduły i trasy. Każde urządzenie może zastosować tylko jedno wdrożenie. Można też utworzyć *wdrożenie warstwowe,* które obejmuje tylko niestandardowe moduły i trasy, a nie środowisko wykonawcze systemu. Wiele wielowarstwowych wdrożeń można łączyć na urządzeniu, oprócz wdrożenia standardowego. Aby uzyskać więcej informacji o tym, jak dwa typy wdrożeń automatycznych współpracują ze sobą, zobacz [Opis automatycznych wdrożeń usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę.](module-deployment-monitoring.md)

Kroki tworzenia wdrożenia i wdrożenia warstwowego są bardzo podobne. Wszelkie różnice są wywoływane w następujących krokach.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. W menu w lewym okienku wybierz pozycję **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami**.
1. Na górnym pasku wybierz pozycję **Utwórz wdrożenie** lub **Utwórz wdrożenie warstwowe**.

Istnieje pięć kroków, aby utworzyć wdrożenie. W poniższych sekcjach przechodzi każdy z nich.

### <a name="step-1-name-and-label"></a>Krok 1: Nazwa i etykieta

1. Nadaj wdrożeniu unikatową nazwę, która wynosi do 128 małych liter. Unikaj spacji i następujących `& ^ [ ] { } \ | " < > /`nieprawidłowych znaków: .
1. Etykiety można dodawać jako pary klucz-wartość, aby ułatwić śledzenie wdrożeń. Na przykład **HostPlatform** i **Linux**, lub **wersja** i **3.0.1**.
1. Wybierz **dalej: Moduły,** aby przejść do kroku drugiego.

### <a name="step-2-modules"></a>Krok 2: Moduły

Do wdrożenia można dodać maksymalnie 20 modułów. Jeśli utworzysz wdrożenie bez modułów, usuwa wszystkie bieżące moduły z urządzeń docelowych.

We wdrożeniach można zarządzać ustawieniami dla agenta usługi IoT Edge i modułów centrum usługi IoT Edge. Wybierz **ustawienia środowiska uruchomieniowego,** aby skonfigurować dwa moduły środowiska wykonawczego. W umieszczeniu warstwowym moduły środowiska wykonawczego nie są uwzględniane, więc nie można ich skonfigurować.

Można dodać trzy typy modułów:

* Moduł brzegowy IoT
* Moduł Marketplace
* Moduł usługi Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Dodawanie modułu IoT Edge

Aby dodać kod niestandardowy jako moduł lub ręcznie dodać moduł usługi platformy Azure, wykonaj następujące kroki:

1. W sekcji **Poświadczenia rejestru kontenerów** na stronie podaj nazwy i poświadczenia dla wszystkich rejestrów kontenerów prywatnych, które zawierają obrazy modułów dla tego wdrożenia. Agent usługi IoT Edge zgłosi błąd 500, jeśli nie może znaleźć poświadczeń rejestru kontenerów dla obrazu platformy Docker.
1. W sekcji **Moduły krawędzi IoT** na stronie kliknij przycisk **Dodaj**.
1. Z menu rozwijanego wybierz **opcję Moduł krawędzi IoT.**
1. Nadaj modułowi **nazwę modułu IoT Edge.**
1. W polu **Identyfikator URI obrazu** wprowadź obraz kontenera dla modułu.
1. Użyj menu rozwijanego, aby wybrać **zasadę ponownego uruchomienia**. Wybierz jedną z następujących opcji:
   * **zawsze** — moduł jest zawsze uruchamiany ponownie, jeśli zostanie wyłączony z jakiegokolwiek powodu.
   * **nigdy** — moduł nigdy nie uruchamia się ponownie, jeśli zostanie zamknięty z jakiegokolwiek powodu.
   * **on-failure** — moduł uruchamia się ponownie, jeśli ulega awarii, ale nie, jeśli zostanie całkowicie zamknięty.
   * **on-złej kondycji** — moduł uruchamia się ponownie, jeśli ulega awarii lub zwraca stan w złej kondycji. To do każdego modułu do zaimplementowania funkcji stanu kondycji.
1. Użyj menu rozwijanego, aby wybrać **żądany stan** modułu. Wybierz jedną z następujących opcji:
   * **running** - Running jest opcją domyślną. Moduł rozpocznie się natychmiast po wdrożeniu.
   * **zatrzymano** — po wdrożeniu moduł pozostanie bezczynny, dopóki nie zostanie wywołany przez Ciebie lub inny moduł.
1. Określ wszystkie **opcje tworzenia kontenera,** które powinny być przekazywane do kontenera. Aby uzyskać więcej informacji, zobacz [tworzenie platformy docker](https://docs.docker.com/engine/reference/commandline/create/).
1. Wybierz **ustawienia bliźniaczej reprezentacji modułu,** jeśli chcesz dodać tagi lub inne właściwości do bliźniaczej reprezentacji modułu.
1. Wprowadź **zmienne środowiskowe** dla tego modułu. Zmienne środowiskowe dostarczają informacji o konfiguracji do modułu.
1. Wybierz **dodaj,** aby dodać moduł do wdrożenia.

#### <a name="add-a-module-from-the-marketplace"></a>Dodawanie modułu z marketplace

Aby dodać moduł z portalu Azure Marketplace, wykonaj następujące kroki:

1. W sekcji **Moduły krawędzi IoT** na stronie kliknij przycisk **Dodaj**.
1. Z menu rozwijanego wybierz **pozycję Moduł marketplace.**
1. Wybierz moduł ze strony **portalu IoT Edge Module Marketplace.** Wybrany moduł jest automatycznie konfigurowany dla subskrypcji, grupy zasobów i urządzenia. Następnie pojawia się na liście modułów IoT Edge. Niektóre moduły mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz [Wdrażanie modułów z witryny Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Dodawanie modułu usługi Stream Analytics

Aby dodać moduł z usługi Azure Stream Analytics, wykonaj następujące kroki:

1. W sekcji **Moduły krawędzi IoT** na stronie kliknij przycisk **Dodaj**.
1. Wybierz **moduł usługi Azure Stream Analytics** z menu rozwijanego.
1. W prawym okienku wybierz **subskrypcję**.
1. Wybierz zadanie IoT **Edge**.
1. Wybierz **pozycję Zapisz,** aby dodać moduł do wdrożenia.

#### <a name="configure-module-settings"></a>Konfigurowanie ustawień modułu

Po dodaniu modułu do wdrożenia można wybrać jego nazwę, aby otworzyć stronę **Aktualizuj moduł usługi IoT Edge.** Na tej stronie można edytować ustawienia modułu, zmienne środowiskowe, tworzenie opcji i bliźniaczej reprezentacji modułu. Jeśli dodano moduł z rynku, może już niektóre z tych parametrów wypełnione.

Jeśli tworzysz wdrożenie warstwowe, może być konfigurowanie modułu, który istnieje w innych wdrożeniach kierowania tych samych urządzeń. Aby zaktualizować bliźniaczątkę modułu bez zastępowania innych wersji, otwórz kartę **Ustawienia bliźniaczej reprezentacji** **modułu.** `properties.desired.settings` Jeśli zdefiniujesz właściwości `properties.desired` w obrębie tylko w polu, zastąpi żądane właściwości modułu zdefiniowanego w dowolnych wdrożeniach o niższym priorytecie.

![Ustawianie właściwości bliźniaczej modułu dla wdrożenia warstwowego](./media/how-to-deploy-monitor/module-twin-property.png)

Aby uzyskać więcej informacji na temat konfiguracji bliźniaczej reprezentacji modułu we wdrożeniach warstwowych, zobacz [Wdrażanie warstwowe](module-deployment-monitoring.md#layered-deployment).

Po skonfigurowaniu wszystkich modułów wdrożenia wybierz pozycję **Dalej: Trasy,** aby przejść do kroku trzeciego.

### <a name="step-3-routes"></a>Krok 3: Trasy

Trasy określają sposób komunikowania się modułów ze sobą w ramach wdrożenia. Domyślnie kreator daje trasę wywoływaną **w górę** i zdefiniowaną jako **FROM /messages/\* INTO $upstream**, co oznacza, że wszelkie komunikaty wysyłane przez wszystkie moduły są wysyłane do centrum IoT hub.  

Dodaj lub zaktualizuj trasy informacjami z [opcji Deklarowanie tras](module-composition.md#declare-routes), a następnie wybierz pozycję **Dalej,** aby przejść do sekcji recenzji.

Wybierz **dalej: Metryki**.

### <a name="step-4-metrics"></a>Krok 4: Metryki

Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie może raportować z powrotem w wyniku zastosowania zawartości konfiguracji.

1. Wprowadź nazwę **nazwy metryki**.

1. Wprowadź kwerendę dla **kryteriów metryk**. Kwerenda jest oparta na [bliźniaczej zgłoszonej właściwości](module-edgeagent-edgehub.md#edgehub-reported-properties)modułu koncentratora usługi IoT. Metryka reprezentuje liczbę wierszy zwracanych przez kwerendę.

   Przykład:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Wybierz **dalej: Urządzenia docelowe**.

### <a name="step-5-target-devices"></a>Krok 5: Urządzenia docelowe

Użyj właściwości tags z urządzeń, aby kierować reklamy na określone urządzenia, które powinny otrzymać to wdrożenie.

Ponieważ wiele wdrożeń może być przeznaczone dla tego samego urządzenia, należy nadać każdemu wdrożeniu numer priorytetu. Jeśli kiedykolwiek wystąpi konflikt, zostanie wygrane wdrożenie o najwyższym priorytecie (większe wartości oznaczają wyższy priorytet). Jeśli dwa wdrożenia mają ten sam numer priorytetu, ten, który został utworzony ostatnio wygrywa.

Jeśli wiele wdrożeń jest przeznaczone dla tego samego urządzenia, zostanie zastosowane tylko to, które ma wyższy priorytet. Jeśli wiele wielowarstwowych wdrożeń jest przeznaczone dla tego samego urządzenia, wszystkie są stosowane. Jeśli jednak wszystkie właściwości są duplikowane, na przykład jeśli istnieją dwie trasy o tej samej nazwie, to jedna z wyższego priorytetu wdrożenia warstwowego zastępuje resztę.

Każdy obiekt docelowy wdrożenia warstwowego urządzenia musi mieć wyższy priorytet niż wdrożenie podstawowe, aby można było je zastosować.

1. Wprowadź dodatnią ćdźwę całkowitą dla **priorytetu**wdrożenia .
1. Wprowadź **warunek docelowy,** aby określić, które urządzenia będą kierowane za pomocą tego wdrożenia.Warunek jest oparty na tagach bliźniaczej reprezentacji urządzenia lub bliźniaczej reprezentacji urządzenia zgłoszonych właściwości i powinien być zgodny z formatem wyrażenia.Na przykład: `tags.environment='test'` lub `properties.reported.devicemodel='4000x'`.

Wybierz **dalej: Przejrzyj + Utwórz,** aby przejść do ostatniego kroku.

### <a name="step-6-review-and-create"></a>Krok 6: Przegląd i tworzenie

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Aby wyświetlić szczegóły wdrożenia i monitorować uruchomione urządzenia, należy wykonać następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. Wybierz **opcję IoT Edge**.
1. Wybierz kartę **Wdrożenia usługi IoT Edge.**

   ![Wyświetlanie wdrożeń usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Sprawdź listę wdrożeń.Dla każdego wdrożenia można wyświetlić następujące szczegóły:
   * **Identyfikator** — nazwa wdrożenia.
   * **Typ** — typ wdrożenia, **wdrożenia** lub **wdrożenia warstwowego**.
   * **Warunek docelowy** — znacznik używany do definiowania urządzeń docelowych.
   * **Priorytet** — numer priorytetu przypisany do wdrożenia.
   * **Metryki** - systemu**Targeted** określa liczbę bliźniaczych urządzeń w Centrum IoT, które odpowiadają warunek kierowania i **Applied** określa liczbę urządzeń, które miały zawartość wdrożenia stosowane do ich bliźniaczy moduł bliźniaczych w Centrum IoT Hub.
   * **Metryki urządzeń** — liczba urządzeń usługi IoT Edge w raportowaniu wdrożenia powodzenie lub błędy ze środowiska wykonawczego klienta usługi IoT Edge.
   * **Metryki niestandardowe** — liczba urządzeń usługi IoT Edge w danych raportowania wdrożenia dla wszystkich metryk zdefiniowanych dla wdrożenia.
   * **Czas tworzenia** — sygnatura czasowa z momentu utworzenia wdrożenia. Ten sygnatura czasowa jest używana do zerwania więzi, gdy dwa wdrożenia mają ten sam priorytet.
1. Wybierz wdrożenie, które chcesz monitorować.  
1. Sprawdź szczegóły wdrożenia. Za pomocą kart można przejrzeć szczegóły wdrożenia.

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Podczas modyfikowania wdrożenia zmiany natychmiast replikują się na wszystkie urządzenia docelowe.

W przypadku aktualizacji warunku docelowego wystąpią następujące aktualizacje:

* Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia nowy warunek docelowy i to wdrożenie jest najwyższy priorytet dla tego urządzenia, to wdrożenie jest stosowane do urządzenia.
* Jeśli urządzenie aktualnie uruchomione to wdrożenie nie spełnia już warunku docelowego, odinstalowuje to wdrożenie i przyjmuje następne wdrożenie o najwyższym priorytecie.
* Jeśli urządzenie aktualnie uruchomione to wdrożenie nie spełnia już warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, na urządzeniu nie nastąpi żadna zmiana. Urządzenie kontynuuje uruchamianie swoich bieżących modułów w ich bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Gdy spełnia warunek docelowy innego wdrożenia, odinstalowuje to wdrożenie i przyjmuje nowe.

Aby zmodyfikować wdrożenie, należy wykonać następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. Wybierz **opcję IoT Edge**.
1. Wybierz kartę **Wdrożenia usługi IoT Edge.**

   ![Wyświetlanie wdrożeń usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Wybierz wdrożenie, które chcesz zmodyfikować.
1. Aktualizacje na następujących kartach:
   * **Warunek docelowy**
   * **Metryki** — możesz modyfikować lub usuwać zdefiniowane dane lub dodawać nowe.
   * **Etykiety**
   * **Moduły**
   * **Trasy**
   * **wdrażania**

1. Wybierz **pozycję Zapisz**.
1. Wykonaj kroki opisane w [monitorowaniu wdrożenia,](#monitor-a-deployment) aby obserwować wdrażanie zmian.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie wdrożone urządzenia przyjmują następne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego innego wdrożenia, moduły nie są usuwane po usunięciu wdrożenia.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. Wybierz **opcję IoT Edge**.
1. Wybierz kartę **Wdrożenia usługi IoT Edge.**

   ![Wyświetlanie wdrożeń usługi IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć.
1. Wybierz pozycję **Usuń**.
1. Monit informuje, że ta akcja spowoduje usunięcie tego wdrożenia i przywrócenie poprzedniego stanu dla wszystkich urządzeń.Oznacza to, że zastosowanie będzie miało wdrożenie o niższym priorytecie.Jeśli żadne inne wdrożenie nie jest ukierunkowane, żadne moduły nie zostaną usunięte. Jeśli chcesz usunąć wszystkie moduły z urządzenia, utwórz wdrożenie z zerowym modułem i wdrożyć je na tych samych urządzeniach.Wybierz **pozycję Tak,** aby kontynuować.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na urządzeniach IoT Edge](module-deployment-monitoring.md).
