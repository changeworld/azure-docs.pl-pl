---
title: Azure Stream Analytics na urządzeniach brzegowych IoT Edge
description: Tworzenie zadań krawędzi w usłudze Azure Stream Analytics i wdrażać je na urządzeniach z systemem Azure IoT Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 4/2/2019
ms.custom: seodec18
ms.openlocfilehash: 4ecea8864a565997b8df119d870e7efee8448143
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803977"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na urządzeniach brzegowych IoT Edge
 
Usługa Azure Stream Analytics (ASA) na usługi IoT Edge umożliwia deweloperom wdrażanie niemal w czasie rzeczywistym bliżej analitycznych analizy na urządzeniach IoT, dzięki czemu mogą odblokować pełną wartość danych generowanych przez urządzenie. Usługa Azure Stream Analytics jest przeznaczona dla małych opóźnień, odporności, efektywne wykorzystanie przepustowości i zgodności. Przedsiębiorstwom teraz wdrożyć logiki formantu blisko operacji przemysłowych i uzupełniają analizy danych Big Data w chmurze.  

Azure Stream Analytics w usłudze IoT Edge jest uruchamiany w ramach [usługi Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) framework. Po utworzeniu zadania w ASA można wdrażać i zarządzać nią za pomocą usługi IoT Hub.

## <a name="scenarios"></a>Scenariusze
![Diagram wysokiego poziomu usługi IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Małe opóźnienia poleceń i kontroli**: Na przykład produkcyjny systemy bezpieczeństwa musi odpowiedzieć danych operacyjnych z bardzo niskimi opóźnieniami. Za pomocą ASA na urządzeniach brzegowych IoT można analizować czujnika danych w czasie zbliżonym do rzeczywistego i wydawać polecenia, gdy wykrywa anomalie, aby zatrzymać maszynę lub wyzwolenia alertów.
*   **Ograniczone łączności z chmurą**: Misja krytycznych systemów, takich jak urządzenia zdalnego wyszukiwania, statków połączonych lub przechodzenie do platformy konieczne do analizowania i reagować na dane, nawet wtedy, gdy łączność z chmurą jest przerywana. Dzięki ASA logika przesyłania strumieniowego jest uruchamiana niezależnie od łączności sieciowej, i można wybrać, co możesz wysłać do chmury do dalszego przetwarzania lub magazynowania.
* **Ograniczona przepustowość**: Ilości danych wytworzonych przez aparaty jet lub podłączone samochody może być tak duża, że dane muszą być filtrowana lub wstępnie przetworzone przed wysłaniem ich do chmury. Za pomocą ASA, możesz filtrować lub agregacji danych, który ma być wysyłane do chmury.
* **Zgodność**: Zgodność z przepisami może wymagać niektóre dane lokalnie anonimowe lub agregowane przed wysłaniem do chmury.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Zadania Edge w usłudze Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co to jest zadanie usługi "brzegu"?

Krawędź ASA zadania są uruchamiane w kontenerach wdrożone [urządzenia usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Składają się z dwóch części:
1.  Część chmury, który jest odpowiedzialny za definicji zadania: użytkownikom Definiowanie danych wejściowych, danych wyjściowych, zapytań i innych ustawień (zdarzeń poza kolejnością, itp.) w chmurze.
2.  Moduł uruchamiania na urządzeniach IoT. Zawiera aparat ASA i odbiera definicji zadania w chmurze. 

ASA używa usługi IoT Hub do wdrożenia zadań edge do urządzeń. Więcej informacji na temat [wdrożenia usługi IoT Edge, zobacz](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Zadania usługi Azure Stream Analytics krawędzi](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instrukcje dotyczące instalacji
Ogólne kroki są opisane w poniższej tabeli. W poniższych sekcjach podano więcej szczegółów.

|      |Krok   | Uwagi   |
| ---   | ---   |  ---      |
| 1   | **Utwórz kontener magazynu**   | Kontenery magazynu są używane do Zapisz swoją definicję zadania, w których są one dostępne przez urządzenia IoT. <br>  Można ponownie użyć dowolnego istniejącego kontenera magazynu.     |
| 2   | **Tworzenie zadania usługi ASA edge**   |  Utwórz nowe zadanie, wybierz opcję **krawędzi** jako **Środowisko hostingu**. <br> Te zadania są tworzone lub zarządzane w chmurze i uruchomić na urządzeniach usługi IoT Edge.     |
| 3   | **Konfigurowanie środowiska usługi IoT Edge na Twoich urządzeń**   | Instrukcje dotyczące [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Wdrażanie ASA na Twoich urządzeń usługi IoT Edge**   |  Definicja zadania ASA są eksportowane do kontenera magazynu utworzoną wcześniej.       |

Możesz wykonać [ten samouczek krok po kroku](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) wdrożyć pierwsze zadanie ASA na urządzeniach brzegowych IoT. Poniższy klip wideo powinny pomóc w zrozumieniu procesu do uruchomienia zadania usługi Stream Analytics na urządzeniu usługi IoT edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Utwórz kontener magazynu
Kontener magazynu jest wymagane w celu eksportowania zapytania ASA skompilowane i konfiguracji zadania. Służy do skonfigurowania obrazu platformy Docker ASA przy użyciu określonego zapytania. 
1. Postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) do utworzenia konta magazynu w witrynie Azure portal. Możesz zachować wszystkie domyślne opcje tego konta za pomocą ASA.
2. W nowo utworzone konto magazynu należy utworzyć kontenera magazynu obiektów blob:
    1. Kliknij pozycję **obiektów blob**, następnie **+ kontener**. 
    2. Wprowadź nazwę i Zachowaj kontener jako **prywatnej**.

#### <a name="create-an-asa-edge-job"></a>Tworzenie zadania usługi ASA Edge
> [!Note]
> Ten samouczek koncentruje się na tworzenie zadania ASA przy użyciu witryny Azure portal. Możesz również [Użyj wtyczki programu Visual Studio do utworzenia zadania usługi ASA Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. W witrynie Azure portal należy utworzyć nowe "Zadanie usługi Stream Analytics". [Bezpośredni link, aby utworzyć nowe zadanie ASA w tym miejscu](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na ekranie tworzenia wybierz **krawędzi** jako **Środowisko hostingu** (zobacz poniższy obraz)

   ![Tworzenie zadania usługi Stream Analytics na urządzeniach brzegowych](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definicja zadania
    1. **Zdefiniuj strumieni danych wejściowych**. Umożliwia zdefiniowanie jednego lub kilku strumienie wejściowe dla zadania.
    2. Zdefiniuj dane referencyjne (opcjonalnie).
    3. **Definiowanie danych wyjściowych strumieni**. Umożliwia zdefiniowanie jednego lub wielu strumieni danych wyjściowych dla zadania. 
    4. **Zdefiniuj zapytanie o**. Zdefiniuj zapytanie o ASA, w chmurze za pomocą wbudowanego edytora. Kompilator automatycznie sprawdza, czy składnia włączone dla przeglądarki edge ASA. Możesz również przetestować zapytanie, przekazując przykładowych danych. 

4. Ustaw informacje o kontenerze magazynu w **ustawienia usługi IoT Edge** menu.

5. Ustawienia opcjonalne
    1. **Określanie kolejności zdarzeń**. W portalu, można skonfigurować zasady poza kolejnością. Dokumentacja jest dostępna [tutaj](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Ustawienia regionalne**. Ustaw internalization format.



> [!Note]
> Po utworzeniu wdrożenia ASA eksportuje definicję zadania do kontenera magazynu. Ta definicja zadania pozostają bez zmian w czasie trwania wdrożenia. W rezultacie jeśli chcesz zaktualizować zadanie uruchomione na urządzeniach brzegowych, należy edytować zadania w ASA, a następnie utwórz nowe wdrożenie w usłudze IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurowanie środowiska usługi IoT Edge na Twoich urządzeń
Zadania Edge można wdrożyć na urządzeniach z systemem Azure IoT Edge.
W tym celu należy wykonać następujące kroki:
- Tworzenie Centrum Iot.
- Zainstaluj środowisko uruchomieniowe platformy Docker i usługi IoT Edge na urządzeniach brzegowych.
- Skonfiguruj urządzenia jako **urządzenia usługi IoT Edge** w usłudze IoT Hub.

Te kroki opisano w dokumentacji usługi IoT Edge [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Wdrożenia ASA na Twoich urządzeń usługi IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Dodawanie ASA do wdrożenia
- W witrynie Azure portal. Otwórz Centrum IoT, przejdź do **usługi IoT Edge** i kliknij przycisk na urządzeniu ma być docelowa dla tego wdrożenia.
- Wybierz **Ustaw moduły**, a następnie wybierz **+ Dodaj** i wybierz polecenie **modułu usługi Azure Stream Analytics**.
- Wybierz subskrypcję i zadania ASA Edge, który został utworzony. Kliknij pozycję Zapisz.
![Dodaj moduł ASA w danym wdrożeniu](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> W tym kroku ASA tworzy folder o nazwie "EdgeJobs" w kontenerze magazynu (jeśli go jeszcze nie istnieje). Dla każdego wdrożenia nowego podfolderu jest tworzony w folderze "EdgeJobs".
> Aby wdrożyć zadanie na urządzeniach brzegowych, ASA tworzy sygnatury dostępu współdzielonego (SAS) dla pliku definicji zadania. Klucza sygnatury dostępu Współdzielonego jest bezpiecznie przesyłany do urządzenia usługi IoT Edge, za pomocą bliźniaczej reprezentacji urządzenia. Po upływie tego klucza jest trzech lat od dnia jego utworzenia.


Aby uzyskać więcej informacji na temat wdrożenia usługi IoT Edge, zobacz do [tę stronę](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurowanie tras
Usługi IoT Edge umożliwia deklaratywne kierowanie komunikatów między modułami i między modułami i IoT Hub. Pełna składnia jest opisany [tutaj](https://docs.microsoft.com/azure/iot-edge/module-composition).
Nazwy wejść i wyjść utworzonej przez zadanie ASA może służyć jako punkty końcowe dla routingu.  

###### <a name="example"></a>Przykład

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Ten przykład pokazuje trasy dla scenariusza opisanego na poniższej ilustracji. Zawiera ona zadania usługi edge o nazwie "**ASA**", przy użyciu danych wejściowych o nazwie "**temperatury**"i dane wyjściowe o nazwie"**alert**".
![Przykład diagramu routing wiadomości](media/stream-analytics-edge/edge-message-routing-example.png)

W tym przykładzie definiuje następujące trasy:
- Każdy komunikat z **tempSensor** są wysyłane do modułu o nazwie **ASA** w danych wejściowych o nazwie **temperatury**,
- Wszystkie elementy wyjściowe **ASA** moduł są wysyłane do Centrum IoT połączonego z tym urządzeniem ($nadrzędne),
- Wszystkie elementy wyjściowe **ASA** moduł są wysyłane do **kontroli** punktu końcowego **tempSensor**.


## <a name="technical-information"></a>Informacje techniczne
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Bieżące ograniczenia dla zadań usługi IoT Edge w porównaniu do zadania w chmurze
Celem jest zapewnienie równoważności między zadaniami usługi IoT Edge i zadania w chmurze. Większość funkcji języka zapytania SQL są obsługiwane, włączanie, aby uruchomić tę samą logikę, zarówno w chmurze, jak i usługi IoT Edge.
Jednak następujące funkcje nie są jeszcze obsługiwane w przypadku zadań edge:
* Funkcje zdefiniowane przez użytkownika (UDF) w języku JavaScript. Funkcji zdefiniowanej przez użytkownika są dostępne w [ C# dla zadań usługi IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (wersja zapoznawcza).
* Agregacje zdefiniowane przez użytkownika (UDA).
* Funkcje usługi Azure ML
* Przy użyciu więcej niż 14 agregatów w jednym kroku.
* Format AVRO dla wejścia/wyjścia. W tej chwili obsługiwane są tylko CSV i JSON.
* Następujące operatory SQL:
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Wymagania dotyczące sprzętu i środowiska uruchomieniowego
Aby uruchomić ASA na urządzeniach brzegowych IoT, potrzebujesz urządzeń z systemem [usługi Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA i używać usługi Azure IoT Edge **Docker** kontenerów, przenośne rozwiązanie, który jest uruchamiany w wielu systemach operacyjnych hosta (Windows, Linux).

ASA na urządzeniach brzegowych IoT jest udostępniany jako obrazy Windows i Linux, systemem zarówno x86-64 lub architektur ARM (Advanced RISC maszyn). 


### <a name="input-and-output"></a>Dane wejściowe i wyjściowe
#### <a name="input-and-output-streams"></a>Dane wejściowe i strumienie wyjściowe
Zadania ASA Edge można uzyskać dane wejściowe i wyjściowe z innych modułów, działające na urządzeniach usługi IoT Edge. Aby połączyć z i do określonych modułów, można ustawić konfiguracji routingu w czasie wdrażania. Więcej informacji można znaleźć na [dokumentacja kompozycji moduł IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Dane wejściowe i wyjściowe CSV i JSON formaty są obsługiwane.

Dla każdego dane wejściowe i strumień wyjściowy utworzysz w ramach zadania ASA, odpowiedni punkt końcowy jest tworzony na wdrożonym module. Te punkty końcowe może służyć w tras w danym wdrożeniu.

Co jest obecny, obsługiwana jest tylko wejście strumienia i typy danych wyjściowych strumieni są Centrum usługi Edge. Obsługuje dane wejściowe odwołania odwołań typu pliku. Inne dane wyjściowe można osiągnąć za pomocą zadania w chmurze. Na przykład zadanie usługi Stream Analytics, hostowana w przeglądarce Edge wysyła dane wyjściowe do Centrum usługi Microsoft Edge, który można wysłać dane wyjściowe do Centrum IoT Hub. Można użyć drugie zadanie usługi Azure Stream Analytics hostowane w chmurze przy użyciu danych wejściowych z Centrum IoT i danych wyjściowych do usługi Power BI lub innego typu danych wyjściowych.



##### <a name="reference-data"></a>Dane referencyjne
Dane referencyjne (znany także jako tabela odnośnika) jest ograniczone zestaw danych, który jest statyczny lub powolne, zmieniając charakter. Służy do wyszukiwania lub skorelowane ze strumienia danych. Aby korzystać z danych referencyjnych w ramach zadania usługi Azure Stream Analytics, będzie na ogół służy [Dołącz dane odwołanie](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) w zapytaniu. Aby uzyskać więcej informacji, zobacz [użycie danych referencyjnych dla wyszukiwania w usłudze Stream Analytics](stream-analytics-use-reference-data.md).

Tylko dane referencyjne lokalnych jest obsługiwane. Gdy zadanie jest wdrożone na urządzeniu usługi IoT Edge, ładuje danych referencyjnych ze ścieżki plików zdefiniowanych przez użytkownika.

Aby utworzyć zadanie z danymi referencyjnymi w programie Edge:

1. Utwórz nowe dane wejściowe dla zadania.

2. Wybierz **dane referencyjne** jako **typ źródła**.

3. Ma odwołanie do pliku danych gotowe na urządzeniu. Dla kontenera Windows umieścić do referencyjnego pliku danych na dysku lokalnym, a następnie udostępniać dysku lokalnego kontenera platformy Docker. Dla kontenera systemu Linux należy utworzyć wolumin platformy Docker i wypełnić plik danych do woluminu.

4. Ustaw ścieżkę do pliku. Dla systemu operacyjnego hosta Windows i Windows container, użyj ścieżki bezwzględnej: `E:\<PathToFile>\v1.csv`. Kontener systemu operacyjnego hosta Windows i Linux lub systemu operacyjnego Linux i kontenerów systemu Linux, użyj ścieżki na woluminie: `<VolumeName>/file1.txt`.

![Nowe wejściowych danych referencyjnych dla zadania usługi Azure Stream Analytics w usłudze IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Dane referencyjne w aktualizacji usługi IoT Edge jest wyzwalana przez wdrożenie. Po wyzwoleniu moduł ASA wybiera zaktualizowane dane bez konieczności zatrzymywania uruchomionego zadania.

Istnieją dwa sposoby, aby zaktualizować dane referencyjne:
* Aktualizuj odwołanie do ścieżki danych w ramach zadania ASA w witrynie Azure portal.
* Aktualizuj wdrożenie usługi IoT Edge.

## <a name="license-and-third-party-notices"></a>Bieżąca licencja i uwagi dotyczące innych firm
* [Licencja Azure Stream Analytics w usłudze IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Uwagi dotyczące innych firm — dla usługi Azure Stream Analytics w usłudze IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, spróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat usługi Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Usługa ASA na samouczek dotyczący usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Tworzenie zadań usługi Stream Analytics Edge przy użyciu narzędzi programu Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementowanie ciągłej integracji/ciągłego wdrażania dla usługi Stream Analytics przy użyciu interfejsów API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
