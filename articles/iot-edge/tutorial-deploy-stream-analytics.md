---
title: Samouczek — analiza strumienia na krawędzi przy użyciu usługi Azure IoT Edge
description: W tym samouczku wdrażasz usługę Azure Stream Analytics jako moduł na urządzeniu usługi IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d8c3bde0f32c1df6c98f6a71f6ab830c21256903
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76906294"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Samouczek: Wdrażanie usługi Azure Stream Analytics jako modułu usługi IoT Edge

Wiele rozwiązań IoT korzysta z usług analitycznych w celu uzyskania informacji o danych pochodzących z chmury z urządzeń IoT. Dzięki usłudze Azure IoT Edge możesz zastosować logikę usługi [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), wdrażając ją bezpośrednio na urządzeniu. Dzięki przetwarzaniu potoków danych telemetrycznych na urządzeniu brzegowym możesz zmniejszyć ilość przekazywanych danych i skrócić czas reakcji na szczegółowe informacje umożliwiające podejmowanie działań.

Usługa Azure IoT Edge i usługa Azure Stream Analytics są zintegrowane, aby uprościć tworzenie obciążeń. Możesz utworzyć zadanie usługi Azure Stream Analytics w witrynie Azure portal, a następnie wdrożyć je jako moduł usługi IoT Edge bez dodatkowego kodu.  

Usługa Azure Stream Analytics zapewnia bogatą strukturę składni zapytań do analizy danych, zarówno w chmurze, jak i na urządzeniach usługi IoT Edge. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Moduł usługi Stream Analytics w tym samouczku oblicza średnią temperaturę w ruchomym przedziale 30 sekund. Jeśli średnia wyniesie 70 stopni, moduł wyśle dla urządzenia alert o konieczności podjęcia działania. W tym przypadku tym działaniem jest zresetowanie symulowanego czujnika temperatury. W środowisku produkcyjnym możesz użyć tej funkcji na przykład do wyłączenia maszyny lub podjęcia działań zapobiegawczych, gdy temperatura osiągnie niebezpieczną wartość.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Tworzenie zadania usługi Azure Stream Analytics w celu przetwarzania danych na urządzeniu brzegowym.
> * Łączenie nowego zadania usługi Azure Stream Analytics z innymi modułami usługi IoT Edge.
> * Wdrażanie zadania usługi Azure Stream Analytics na urządzeniu usługi IoT Edge z poziomu witryny Azure Portal.

<center>

![Diagram - Architektura samouczka: etap i wdrażanie zadania ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Maszyny wirtualnej platformy Azure można używać jako urządzenia usługi IoT Edge, wykonując kroki opisane w przewodniku Szybki start dla urządzeń [z systemem Linux](quickstart-linux.md) lub [Windows.](quickstart.md)

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Tworzenie zadania usługi Azure Stream Analytics

W tej sekcji utworzysz zadanie usługi Azure Stream Analytics, które wykona następujące kroki:

* Odbieraj dane z urządzenia Usługi IoT Edge.
* Kwerenda danych telemetrycznych dla wartości spoza zestawu zakres.
* Podejmij działania na urządzeniu IoT Edge na podstawie wyników kwerendy.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W przypadku tworzenia zadania usługi Azure Stream Analytics do uruchamiania na urządzeniu usługi IoT Edge musi być ono przechowywane w sposób umożliwiający wywoływanie go z urządzenia. Możesz użyć istniejącego konta usługi Azure Storage lub utworzyć nowe teraz.

1. W witrynie Azure portal przejdź do **witryny Tworzenie** > **konta magazynu****magazynu** > zasobów .

1. Podaj następujące wartości, aby utworzyć konto magazynu:

   | Pole | Wartość |
   | ----- | ----- |
   | Subskrypcja | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub. |
   | Grupa zasobów | Zaleca się używanie tej samej grupy zasobów dla wszystkich zasobów testowych dla przewodników szybkiej obsługi i samouczków dla aplikacji IoT Edge. Na przykład **IoTEdgeResources**. |
   | Nazwa | Wprowadź unikatową nazwę konta magazynu. |
   | Lokalizacja | Wybierz bliską lokalizację. |

1. Zachowaj wartości domyślne dla innych pól i wybierz **pozycję Recenzja + Utwórz**.

1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-new-job"></a>Tworzenie nowego zadania

1. W witrynie Azure portal przejdź do **zadania Tworzenie zasobu** > Usługi**Stream Analytics**w zakresie**internetu rzeczy** > .

1. Podaj następujące wartości, aby utworzyć zadanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Nazwa zadania | Podaj nazwę zadania. Na przykład **IoTEdgeJob** |
   | Subskrypcja | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Lokalizacja | Wybierz bliską lokalizację. |
   | Środowisko hostingu | Wybierz pozycję **Edge**. |

1. Wybierz **pozycję Utwórz**.

### <a name="configure-your-job"></a>Konfigurowanie zadania

Po utworzeniu zadania usługi Stream Analytics w witrynie Azure Portal można je skonfigurować za pomocą danych wejściowych, danych wyjściowych i zapytania w celu uruchamiania na danych, które przez nie przechodzą.

Przy użyciu trzech elementów — danych wejściowych, danych wyjściowych i zapytania — w tej sekcji zostanie utworzone zadanie, które odbiera dane dotyczące temperatury z urządzenia usługi IoT Edge. Analizuje ono te dane w kroczącym 30-sekundowym przedziale czasu. Jeśli średnia temperatura w tym przedziale czasu przekracza 70 stopni, wysyłany jest alert do urządzenia usługi IoT Edge. W następnej sekcji, podczas wdrażania zadania, określisz dokładnie, skąd dane pochodzą i dokąd są przesyłane.  

1. Przejdź do zadania usługi Stream Analytics w witrynie Azure Portal.

1. W obszarze **Topologia zadania** wybierz pozycję **Dane wejściowe**, a następnie pozycję **Dodaj wejście strumienia**.

   ![Usługa Azure Stream Analytics — dodawanie danych wejściowych](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Wybierz pozycję **Edge Hub** z listy rozwijanej.

1. W okienku **Nowe dane wejściowe** wpisz **temperature** jako alias danych wejściowych.

1. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Zapisz**.

1. W obszarze **Topologia zadania** otwórz pozycję **Dane wyjściowe**, a następnie wybierz pozycję **Dodaj**.

   ![Usługa Azure Stream Analytics — dodawanie danych wyjściowych](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Wybierz pozycję **Edge Hub** z listy rozwijanej.

1. W okienku **Nowe dane wyjściowe** wpisz **alert** jako alias danych wyjściowych.

1. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Zapisz**.

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

1. Zastąp tekst domyślny poniższym zapytaniem. Kod SQL wysyła polecenie reset do danych wyjściowych alertu, jeśli średnia temperatura maszyny w 30-sekundowym przedziale czasu osiągnie 70 stopni. Polecenie reset zostało wstępnie zaprogramowane w czujniku jako akcja, która może zostać wykonana.

    ```sql
    SELECT  
        'reset' AS command
    INTO
       alert
    FROM
       temperature TIMESTAMP BY timeCreated
    GROUP BY TumblingWindow(second,30)
    HAVING Avg(machine.temperature) > 70
    ```

1. Wybierz **pozycję Zapisz**.

### <a name="configure-iot-edge-settings"></a>Konfigurowanie ustawień usługi IoT Edge

Aby przygotować zadanie sługi Stream Analytics do wdrożenia na urządzeniu usługi IoT Edge, musisz skojarzyć zadanie z kontenerem na koncie magazynu. Po przejściu do wdrażania zadania definicja zadania zostanie wyeksportowana do kontenera magazynu.

1. W obszarze **Konfiguruj**wybierz pozycję **Ustawienia konta magazynu,** a następnie wybierz pozycję **Dodaj konto magazynu**.

   ![Usługa Azure Stream Analytics — dodawanie konta magazynu](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Z menu rozwijanego wybierz **konto magazynu** utworzone na początku tego samouczka.

1. W polu **Kontener** wybierz pozycję **Utwórz nowy** i wpisz nazwę kontenera magazynu.

1. Wybierz **pozycję Zapisz**.

## <a name="deploy-the-job"></a>Wdrażanie zadania

Możesz teraz wdrożyć zadanie usługi Azure Stream Analytics na urządzeniu usługi IoT Edge.

W tej sekcji użyjesz kreatora **Utwórz moduły** w witrynie Azure Portal, aby utworzyć *manifest wdrożenia*. Manifest wdrożenia to plik JSON opisujący wszystkie moduły, które zostaną wdrożone na urządzeniu, rejestry kontenerów, w których są przechowywane obrazy modułów, sposób, w jaki powinny być zarządzane moduły oraz sposób, w jaki moduły mogą komunikować się między sobą. Urządzenie usługi IoT Edge pobiera manifest wdrożenia z usługi IoT Hub, a następnie używa zawartych w nim informacji, aby wdrożyć i skonfigurować wszystkie przypisane moduły.

W tym samouczku wdrażane są dwa moduły. Pierwszym z nich jest **SimulatedTemperatureSensor**, który jest modułem, który symuluje czujnik temperatury i wilgotności. Drugi to zadanie usługi Stream Analytics. Moduł czujnika dostarcza strumień danych, który będzie analizowany przez zapytanie zadania.

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

1. Przejdź do aplikacji **IoT Edge**, a następnie otwórz stronę szczegółów urządzenia IoT Edge.

1. Wybierz **opcję Ustaw moduły**.  

1. Jeśli wcześniej wdrożono moduł SimulatedTemperatureSensor na tym urządzeniu, może on automatycznie wypełnić. W przeciwnym razie dodaj moduł, wykonując następujące kroki:

   1. Kliknij pozycję **Dodaj** i wybierz pozycję **Moduł usługi IoT Edge**.
   1. Dla nazwy wpisz **SymulowanyTemperatureSensor**.
   1. W polu Identyfikator URI obrazu wpisz **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**.
   1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.

1. Dodaj zadanie usługi Azure Stream Analytics dla usługi IoT Edge, wykonując następujące kroki:

   1. Kliknij przycisk **Dodaj** i wybierz pozycję **Moduł usługi Azure Stream Analytics**.
   1. Wybierz subskrypcję i utworzone wcześniej zadanie usługi Azure Stream Analytics dla usługi IoT Edge.
   1. Wybierz **pozycję Zapisz**.

   Po zapisaniu zmian szczegóły zadania usługi Stream Analytics są publikowane w utworzonym kontenerze magazynu.

1. Po dodaniu modułu usługi Stream Analytics do listy modułów wybierz jego nazwę, aby zobaczyć, jak jest skonstruowany i zaktualizować jego ustawienia na stronie **Aktualizacja modułu usługi IoT Edge.**

   Karta **Ustawienia modułu** zawiera **identyfikator URI obrazu,** który wskazuje standardowy obraz usługi Azure Stream Analytics. Ten jeden obraz jest używany dla każdego modułu usługi Stream Analytics, który zostanie wdrożony na urządzeniu usługi IoT Edge.

   Karta **Ustawienia bliźniaczych reprezentacji modułu** zawiera usługę JSON definiuj właściwość usługi Azure Stream Analytics (ASA) o nazwie **ASAJobInfo**. Wartość tej właściwości wskazuje definicję zadania w kontenerze magazynu. Ta właściwość jest jak obraz usługi Stream Analytics jest skonfigurowany z określonymi szczegółami zadania.

   Domyślnie moduł Usługi Stream Analytics ma taką samą nazwę jak zadanie, na podstawie czego jest on oparty. Możesz zmienić nazwę modułu na tej stronie, jeśli chcesz, ale nie jest to konieczne.

1. Wybierz **pozycję Anuluj** lub **Zapisz**.

1. Zanotuj nazwę modułu Usługi Stream Analytics, ponieważ jest ona potrzebna w następnym kroku, a następnie wybierz **pozycję Dalej: Trasy,** aby kontynuować.

1. Na **trasy** kartę można zdefiniować, jak wiadomości są przekazywane między modułami i Centrum IoT Hub. Wiadomości są tworzone przy użyciu par nazwy/wartości. Zastąp domyślne `route` i `upstream` nazwy i wartości parami pokazanymi w poniższej tabeli, następujące pary nazw/wartości, zastępując wystąpienia _{moduleName}_ nazwą modułu usługi Azure Stream Analytics.

    | Nazwa | Wartość |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    Trasy zadeklarowane w tym miejscu definiują przepływ danych przez urządzenie usługi IoT Edge. Dane telemetryczne z SimulatedTemperatureSensor są wysyłane do usługi IoT Hub i do danych wejściowych **temperatury,** który został skonfigurowany w zadaniu usługi Stream Analytics. Komunikaty wyjściowe **alertu** są wysyłane do centrum IoT Hub i modułu SimulatedTemperatureSensor w celu wyzwolenia polecenia resetowania.

1. Wybierz **dalej: Recenzja + Utwórz**.

1. Na karcie **Recenzja + Utwórz** możesz zobaczyć, jak informacje podane w kreatorze są konwertowane na manifest wdrożenia JSON. Po zakończeniu przeglądania manifestu wybierz pozycję **Utwórz**.

1. Zostaniesz przywrócony do strony szczegółów urządzenia. Wybierz **pozycję Odśwież**.  

    Powinien zostać wyświetlony nowy moduł usługi Stream Analytics uruchomiony wraz z agentem usługi IoT Edge i modułami centrum usługi IoT Edge. Może upłynąć kilka minut, aby informacje dotarły do urządzenia Usługi IoT Edge, a następnie uruchomienie nowych modułów. Jeśli nie widzisz modułów uruchomionych od razu, kontynuuj odświeżanie strony.

    ![Symulowany ModułTemperatureSensor i ASA zgłoszony przez urządzenie](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Wyświetlanie danych

Teraz możesz przejść do urządzenia usługi IoT Edge, aby sprawdzić interakcję między modułem usługi Azure Stream Analytics a modułem SimulatedTemperatureSensor.

1. Sprawdź, czy wszystkie moduły działają w aplikacji Docker:

   ```cmd/sh
   iotedge list  
   ```

1. Wyświetl wszystkie dzienniki systemu i dane metryk. Użyj nazwy modułu usługi Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Wyświetl polecenie resetowania wpływa na resetowanytemperaturaSensor, wyświetlając dzienniki czujników:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Możesz obserwować, jak temperatura maszyny stopniowo wzrasta, aż osiągnie 70 stopni przez 30 sekund. Następnie moduł usługi Stream Analytics wywoła reset, a temperatura maszyny spadnie do 21 stopni.

   ![Resetowanie danych wyjściowych polecenia do dzienników modułu](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie można usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano zadanie usługi Azure Stream Analytics służące do analizy danych z urządzenia usługi IoT Edge. Następnie załadowano moduł usługi Azure Stream Analytics na urządzeniu usługi IoT Edge w celu lokalnego przetwarzania danych dotyczących temperatury i reagowania na wzrost temperatury, a także przesyłania zagregowanego strumienia danych do chmury. Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"]
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
