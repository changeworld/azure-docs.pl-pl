---
title: Wdrażanie modułów na dużą skalę Azure Portal — Azure IoT Edge
description: Użyj Azure Portal, aby utworzyć automatyczne wdrożenia dla grup urządzeń IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 068845bf8cda7ce6abf11eefad0ed176688b34c5
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665852"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażaj i monitoruj moduły IoT Edge na dużą skalę przy użyciu Azure Portal

Utwórz **IoT Edge Automatyczne wdrażanie** w Azure Portal, aby zarządzać trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby. 

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia należy mieć możliwość określania, które urządzenia mają mieć wpływ. Azure IoT Edge identyfikuje urządzenia przy użyciu **tagów** z sznurka urządzenia. Każde urządzenie może mieć wiele tagów zdefiniowanych w dowolny sposób, który ma sens dla danego rozwiązania. Na przykład w przypadku zarządzania najwyższego budynku można dodać następujące znaczniki do urządzenia:

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

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub. 
1. Wybierz **IoT Edge**.
1. Wybierz pozycję **Dodaj wdrożenie IoT Edge**.

Istnieje pięć kroków, które należy wykonać, aby utworzyć wdrożenie. W poniższych sekcjach omówiono każdy z nich. 

### <a name="step-1-name-and-label"></a>Krok 1. nazwa i etykieta

1. Nadaj wdrożenie unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /`.
1. Etykiety można dodawać jako pary klucz-wartość, aby ułatwić śledzenie wdrożeń. Na przykład **HostPlatform** i **Linux**, lub **wersja** i **3.0.1**.
1. Wybierz przycisk **dalej** , aby przejść do kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2. Dodawanie modułów (opcjonalnie)

Do wdrożenia można dodać maksymalnie 20 modułów. 

Jeśli utworzysz wdrożenie bez modułów, spowoduje to usunięcie wszystkich bieżących modułów z urządzeń docelowych. 

Aby dodać moduł z Azure Stream Analytics, wykonaj następujące kroki:

1. W sekcji **moduły wdrażania** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł Azure Stream Analytics**.
1. Wybierz swoją **subskrypcję** z menu rozwijanego.
1. Wybierz zadanie usługi IoT **Edge** z menu rozwijanego.
1. Wybierz pozycję **Zapisz** , aby dodać moduł do wdrożenia. 

Aby dodać niestandardowy kod jako moduł lub ręcznie dodać moduł usługi platformy Azure, wykonaj następujące kroki:

1. W sekcji **ustawienia Container Registry** strony Podaj nazwy i poświadczenia dla prywatnych rejestrów kontenerów, które zawierają obrazy modułów dla tego wdrożenia. Agent IoT Edge zgłosi błąd 500, jeśli nie można znaleźć poświadczenia rejestru kontenera dla obrazu platformy Docker.
1. W sekcji **moduły wdrażania** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł IoT Edge**.
1. Nadaj modułowi **nazwę**.
1. W polu **Identyfikator URI obrazu** wprowadź obraz kontenera dla modułu. 
1. Określ wszelkie **Opcje tworzenia kontenera** , które powinny być przesyłane do kontenera. Aby uzyskać więcej informacji, zobacz [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Użyj menu rozwijanego, aby wybrać **zasady ponownego uruchamiania**. Wybierz jedną z następujących opcji: 
   * **Zawsze** — moduł zawsze jest uruchamiany ponownie, jeśli z jakiegoś powodu zostanie zamknięty.
   * **nigdy** — moduł nigdy nie jest ponownie uruchamiany, jeśli z jakiegoś powodu zostanie zamknięty.
   * w przypadku niepowodzenia — moduł jest uruchamiany ponownie w przypadku awarii, ale nie w przypadku jego **nieprawidłowego** zamknięcia. 
   * **w złej kondycji** — moduł jest uruchamiany ponownie, jeśli ulegnie awarii lub zwróci stan złej kondycji. Do każdego modułu jest zaimplementowana funkcja stanu kondycji. 
1. Użyj menu rozwijanego, aby wybrać **żądany stan** modułu. Wybierz jedną z następujących opcji:
   * **uruchomiona** jest opcja domyślna. Moduł zacznie działać natychmiast po wdrożeniu.
   * **zatrzymano** — po wdrożeniu moduł pozostanie bezczynny do momentu wywołania przez użytkownika lub innego modułu.
1. Wybierz pozycję **Ustaw odpowiednie właściwości w module** , jeśli chcesz dodać Tagi lub inne właściwości do sznurka modułu.
1. Wprowadź **zmienne środowiskowe** dla tego modułu. Zmienne środowiskowe udostępniają informacje o konfiguracji do modułu.
1. Wybierz pozycję **Zapisz** , aby dodać moduł do wdrożenia. 

Po skonfigurowaniu wszystkich modułów dla wdrożenia wybierz pozycję **dalej** , aby przejść do kroku 3.

### <a name="step-3-specify-routes-optional"></a>Krok 3. Określanie tras (opcjonalnie)

Trasy definiują, jak moduły komunikują się ze sobą w ramach wdrożenia. Domyślnie Kreator udostępnia trasę o nazwie **trasa** i zdefiniowana jako **z/* do $Upstream * *, co oznacza, że wszystkie komunikaty przesyłane przez wszystkie moduły są wysyłane do centrum IoT Hub.  

Dodaj lub zaktualizuj trasy z informacjami z [deklaracji trasy](module-composition.md#declare-routes), a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd.

### <a name="step-4-specify-metrics-optional"></a>Krok 4. Określanie metryk (opcjonalnie)

Metryki zawierają podsumowanie liczb różnych stanów, które urządzenie może zgłosić z powrotem w wyniku zastosowania zawartości konfiguracji.

1. Wprowadź nazwę dla **nazwy metryki**.

1. Wprowadź zapytanie dla **kryteriów metryki**. Zapytanie jest oparte na IoT Edge [raportowanych właściwościach](module-edgeagent-edgehub.md#edgehub-reported-properties)modułu centrum. Metryka reprezentuje liczbę wierszy zwracanych przez zapytanie.

   Na przykład:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Krok 5. urządzenia docelowe

Użyj właściwości Tags z urządzeń, aby określić urządzenia docelowe, które powinny otrzymać to wdrożenie. 

Ponieważ wiele wdrożeń może wskazywać na to samo urządzenie, należy nadać każdemu wdrożeniu numer priorytetu. Jeśli kiedykolwiek wystąpi konflikt, wdrożenie o najwyższym priorytecie (większe wartości wskazują wyższy priorytet) WINS. Jeśli dwa wdrożenia mają taki sam numer priorytetu, ten, który został utworzony ostatnio jako usługa WINS. 

1. Wprowadź dodatnią liczbę całkowitą dla **priorytetu**wdrożenia.
1. Wprowadź **warunek docelowy** , aby określić, które urządzenia będą ukierunkowane na to wdrożenie. Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.reported.devicemodel='4000x'`. 
1. Wybierz przycisk **dalej** , aby przejść do ostatniego kroku.

### <a name="step-6-review-deployment"></a>Krok 6. przegląd wdrożenia

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**.

## <a name="deploy-modules-from-azure-marketplace"></a>Wdrażanie modułów z witryny Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług online, w którym można przeglądać szeroką gamę aplikacji i rozwiązań dla przedsiębiorstw, które są certyfikowane i zoptymalizowane pod kątem działania na platformie Azure, w tym [modułów IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Dostęp do portalu Azure Marketplace można także uzyskać za pomocą Azure Portal w obszarze **Tworzenie zasobu**.

Moduł IoT Edge można wdrożyć w witrynie Azure Marketplace lub Azure Portal:

1. Znajdź moduł i Rozpocznij proces wdrażania.

   * Azure Portal: Znajdź moduł i wybierz pozycję **Utwórz**.

   * Portal Azure Marketplace:

     1. Znajdź moduł i wybierz pozycję **Pobierz teraz**.
     1. Potwierdź warunki użytkowania dostawcy i zasady ochrony prywatności, wybierając pozycję **Kontynuuj**.

1. Wybierz swoją subskrypcję i IoT Hub, do której jest dołączone urządzenie docelowe.

1. Wybierz pozycję **Wdróż na dużą skalę**.

1. Zdecyduj, czy dodać moduł do nowego wdrożenia, czy do klonowania istniejącego wdrożenia; w przypadku klonowania Wybierz istniejące wdrożenie z listy.

1. Wybierz pozycję **Utwórz** , aby kontynuować proces tworzenia wdrożenia w odpowiedniej skali. Będzie można określić takie same szczegóły, jak w przypadku każdego wdrożenia.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Aby wyświetlić szczegóły wdrożenia i monitorować urządzenia, na których działa, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub. 
1. Wybierz **IoT Edge**.
1. Wybierz **IoT Edge wdrożenia**. 

   ![Wyświetl IoT Edge wdrożenia](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Sprawdź listę wdrożenia. Dla każdego wdrożenia można wyświetlić następujące informacje:
   * **Identyfikator** — nazwa wdrożenia.
   * **Warunek docelowy** — tag używany do definiowania urządzeń docelowych.
   * **Priorytet** — numer priorytetu przypisany do wdrożenia.
   * **Metryki systemu** - określania wartości **docelowej** określa liczbę urządzeń w IoT Hub, które pasują do warunku określania wartości docelowej, i **zastosowano** określa liczbę urządzeń, na których zastosowano zawartość wdrożenia do ich modułu bliźniaczych reprezentacji w IoT Hub. 
   * **Metryki urządzeń** — liczba urządzeń IoT Edge w przypadku powodzenia lub błędów raportowania wdrożenia w środowisku uruchomieniowym klienta IoT Edge.
   * **Metryki niestandardowe** — liczba IoT Edge urządzeń w danych raportowania wdrożenia dla wszystkich metryk zdefiniowanych dla wdrożenia.
   * **Czas utworzenia** — sygnatura czasowa od momentu utworzenia wdrożenia. Ta sygnatura czasowa jest używana do przerwania powiązań, gdy dwa wdrożenia mają ten sam priorytet. 
1. Wybierz wdrożenie, które chcesz monitorować.  
1. Sprawdź szczegóły wdrożenia. Możesz użyć kart, aby przejrzeć szczegóły wdrożenia.

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany są natychmiast replikowane na wszystkie urządzenia objęte usługą. 

W przypadku zaktualizowania warunku docelowego następujące aktualizacje są wykonywane:

* Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia warunki nowego elementu docelowego, a to wdrożenie ma najwyższy priorytet dla tego urządzenia, to wdrożenie jest stosowane na urządzeniu. 
* Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego, Odinstalowuje to wdrożenie i podejmuje kolejne wdrożenie o najwyższym priorytecie. 
* Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, nie nastąpi żadne zmiany na urządzeniu. Urządzenie kontynuuje uruchamianie bieżących modułów w bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Po spełnieniu warunku docelowego dowolnego innego wdrożenia Odinstalowuje to wdrożenie i przyjmuje nowe. 

Aby zmodyfikować wdrożenie, wykonaj następujące czynności: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub. 
1. Wybierz **IoT Edge**.
1. Wybierz **IoT Edge wdrożenia**. 

   ![Wyświetl IoT Edge wdrożenia](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Wybierz wdrożenie, które chcesz zmodyfikować. 
1. Wprowadź aktualizacje następujących pól: 
   * Warunek docelowy
   * Metryki — można modyfikować lub usuwać zdefiniowane metryki lub dodawać nowe.
   * Etykiety
   * Priorytet
1. Wybierz pozycję **Zapisz**.
1. Wykonaj kroki opisane w sekcji [monitorowanie wdrożenia](#monitor-a-deployment) , aby obejrzeć zmiany. 

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie urządzenia przyjmą kolejne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego innego wdrożenia, moduły nie zostaną usunięte po usunięciu wdrożenia. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub. 
1. Wybierz **IoT Edge**.
1. Wybierz **IoT Edge wdrożenia**. 

   ![Wyświetl IoT Edge wdrożenia](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć. 
1. Wybierz pozycję **Usuń**.
1. Zostanie wyświetlony monit z informacją o tym, że ta akcja spowoduje usunięcie tego wdrożenia i przywrócenie poprzedniego stanu dla wszystkich urządzeń.  Oznacza to, że zostanie zastosowane wdrożenie o niższym priorytecie.  Jeśli żadne inne wdrożenie nie jest wskazywane, moduły nie zostaną usunięte. Jeśli chcesz usunąć wszystkie moduły z urządzenia, Utwórz wdrożenie z zerowymi modułami i wdróż je na tych samych urządzeniach. Wybierz pozycję **tak** , aby kontynuować. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).
