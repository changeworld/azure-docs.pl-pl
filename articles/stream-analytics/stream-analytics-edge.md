---
title: Usługa Azure Stream Analytics na krawędzi IoT (wersja zapoznawcza)
description: Tworzenie zadań krawędzi w Azure Stream Analytics i wdrażać je dla urządzenia uruchomiona Azure IoT krawędzi.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: 5ce0420dde5bf232fe8067a3b14814f14380602e
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802531"
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Usługa Azure Stream Analytics na krawędzi IoT (wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest w wersji zapoznawczej i nie jest zalecane używanie w środowisku produkcyjnym.
 
Azure Stream Analytics (ASA) na krawędzi IoT umożliwia deweloperom wdrażanie niemal czasie rzeczywistym bliżej analitycznych analizy urządzenia IoT, tak aby ich odblokowania pełną wartość dane generowane przez urządzenie. Usługa Azure Stream Analytics jest przeznaczona dla małych opóźnieniach, odporności, efektywne wykorzystanie przepustowości i zgodności. Przedsiębiorstwa teraz można wdrożyć logikę kontroli bliski operacji przemysłowych i uzupełniają analizy danych Big Data w chmurze.  

Usługa Azure Stream Analytics na krawędzi IoT jest uruchamiany w ramach [Azure IoT krawędzi](https://azure.microsoft.com/campaigns/iot-edge/) framework. Po utworzeniu zadania w ASA, można wdrażać i zarządzać nimi ASA zadań przy użyciu Centrum IoT. Ta funkcja jest dostępna w wersji zapoznawczej. Jeśli masz pytania lub opinie, możesz użyć [badanie](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) do kontaktowania się z zespołu produktu. 

## <a name="scenarios"></a>Scenariusze
![Diagram ogólny](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Polecenie małe opóźnienia i kontroli**: na przykład produkcyjnym bezpieczeństwa systemów musi odpowiadać na danych operacyjnych z bardzo niskim opóźnieniem. Z ASA na krawędzi IoT można przeanalizować czujnik dane prawie w czasie rzeczywistym i wydania poleceń podczas wykrywania anomalii, aby zatrzymać maszynę lub wyzwoliły alertów.
*   **Ograniczona łączność z chmurą**: misji ważnych systemów, takich jak urządzenia zdalnego wyszukiwania, połączonych zbiorników lub platformy przechodzenia na wyższy poziom, należy do analizowania i reagowania na danych, nawet wtedy, gdy łączność chmury jest przerywana. ASA logika przesyłania strumieniowego działa niezależnie od łączność sieciową i są dostępne, co możesz wysłać do chmury dla dalszego przetwarzania lub magazynu.
* **Ograniczona przepustowość**: ilość danych utworzone przez aparaty jet lub połączonych samochodów może być taki duży, że dane muszą być filtrowane lub wstępnie przetworzyć przed jej wysłaniem do chmury. Przy użyciu ASA, możesz filtrować i agregowanie danych, który ma być wysyłane do chmury.
* **Zgodność**: zgodność z przepisami może wymagać określonych danych lokalnie anonimowe lub agregowana przed wysłaniem do chmury.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Krawędzi zadania usługi analiza strumienia Azure
### <a name="what-is-an-edge-job"></a>Co to jest zadanie "krawędzi"?

Uruchamianie zadań krawędzi ASA modułów w [środowiska uruchomieniowego Azure IoT krawędzi](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Składają się z dwóch części:
1.  Części chmury, która jest odpowiedzialna za definicji zadania: użytkownicy definiować danych wejściowych, wyjściowych zapytania i innych ustawień (zdarzenia poza kolejnością itp.) w chmurze.
2.  ASA na krawędzi IoT moduł, który działa lokalnie. Zawiera aparat złożonych przetwarzania zdarzeń ASA i odbiera definicji zadania z chmury. 

ASA używa Centrum IoT, aby przeprowadzić wdrożenie zadania krawędzi urządzenia. Więcej informacji na temat [krawędzi IoT wdrożenia są widoczne w tym miejscu](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Zadanie Edge](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Instrukcje dotyczące instalacji
Ogólne kroki opisane w poniższej tabeli. W poniższych sekcjach podano więcej szczegółów.
|      |Krok   | Miejsce     | Uwagi   |
| ---   | ---   | ---       |  ---      |
| 1   | **Tworzenie kontenera magazynu**   | Azure Portal       | Kontenery magazynu są używane do zapisać definicję zadania, w którym są one dostępne przez urządzenia IoT. <br>  Można ponownie użyć dowolnego istniejącego kontenera magazynu.     |
| 2   | **Utwórz zadanie krawędzi ASA**   | Azure Portal      |  Utwórz nowe zadanie, wybierz opcję **krawędzi** jako **środowisko macierzyste**. <br> Te zadania są tworzone zarządzane z chmury, a następnie uruchom na urządzenia IoT krawędzi.     |
| 3   | **Konfiguracji środowiska krawędzi IoT na Twoje urządzenia**   | Urządzenia      | Instrukcje dotyczące [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Wdrażanie ASA na Twoje urządzenia IoT krawędzi**   | Azure Portal      |  ASA definicji zadania są eksportowane do kontenera magazynu utworzony wcześniej.       |
Możesz wykonać [tego samouczka krok po kroku](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) do wdrożenia Twoja pierwsza Praca ASA na krawędzi IoT. Poniższe wideo należy pomaga w zrozumieniu procesu Uruchom zadanie usługi Stream Analytics urządzenia IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
Kontener magazynu jest wymagana w celu eksportowania zapytań ASA skompilowany i konfiguracji zadań. Służy do konfigurowania obrazów ASA Docker z określonego zapytania. 
1. Postępuj zgodnie z [tych instrukcji](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) można utworzyć konta magazynu z portalu Azure. Można zachować wszystkie domyślne opcje do tego konta za pomocą ASA.
2. W nowo utworzone konto magazynu należy utworzyć kontener magazynu obiektów blob:
    1. Polecenie **obiekty BLOB**, następnie **+ kontener**. 
    2. Wprowadź nazwę i zachować kontenera jako **prywatnej**.

#### <a name="create-an-asa-edge-job"></a>Utwórz zadanie krawędzi ASA
> [!Note]
> Ten samouczek koncentruje się na utworzenie zadania ASA przy użyciu portalu Azure. Możesz również [umożliwiają utworzenie zadania krawędzi ASA dodatku Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. W portalu Azure należy utworzyć nowe "Zadanie usługi analiza strumienia". [Bezpośredniego łącza do tworzenia nowego zadania ASA tutaj](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na ekranie tworzenia wybierz **krawędzi** jako **środowisko macierzyste** (patrz poniżej) ![Tworzenie zadania](media/stream-analytics-edge/ASAEdge_create.png)
3. Definicji zadania
    1. **Zdefiniuj strumieni wejściowych**. Zdefiniuj jednego lub kilku strumienie wejściowe dla zadania.
    2. Definiowanie danych referencyjnych (opcjonalnie).
    3. **Zdefiniuj strumieni wyjściowych**. Zdefiniuj jeden lub kilka strumieni danych wyjściowych dla zadania. 
    4. **Zdefiniuj zapytanie o**. Zdefiniuj kwerendę ASA w chmurze za pomocą wbudowanego edytora. Kompilator automatycznie sprawdza, czy składnia włączone ASA Edge. Można przetestować zapytanie to przekazywania przykładowych danych. 
4. Ustaw informacje o kontenerze magazynu **ustawienia IoT Edge** menu.
5. Ustawienia opcjonalne
    1. **Kolejność zdarzeń**. Poza kolejnością zasadę można skonfigurować w portalu. Dokumentacja jest dostępna [tutaj](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Ustawienia regionalne**. Określanie formatu internalization.



> [!Note]
> Po utworzeniu wdrożenia ASA eksportuje definicji zadania do kontenera magazynu. Tej definicji zadania nie zmieniają się w okresie wdrożenia. W rezultacie jeśli chcesz zaktualizować zadania uruchomione na krawędzi, należy edytować zadania w ASA, a następnie utwórz nowe wdrożenie w Centrum IoT.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurowanie środowiska krawędzi IoT na Twoje urządzenia
Krawędź zadań można wdrożyć na urządzeniach z systemem Azure IoT krawędzi.
W tym celu należy wykonać następujące kroki:
- Tworzenie Centrum Iot.
- Na urządzeniach krawędzi, należy zainstalować środowisko uruchomieniowe Docker i krawędzi IoT.
- Skonfiguruj urządzenia jako **urządzenia IoT brzegowe** w Centrum IoT.

Te kroki opisano w dokumentacji krawędzi IoT [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Wdrażanie ASA Twojego urządzenia IoT krawędzi
##### <a name="add-asa-to-your-deployment"></a>Dodaj ASA do wdrożenia
- W portalu Azure Otwórz Centrum IoT, przejdź do **krawędzi IoT** i kliknij na urządzeniu ma być docelowa dla tego wdrożenia.
- Wybierz **ustawić modułów**, a następnie wybierz pozycję **+ Dodaj** i wybierz polecenie **Azure Stream Analytics modułu**.
- Wybierz subskrypcję i zadania krawędzi ASA, utworzony. Kliknij przycisk Zapisz.
![Dodaj moduł ASA we wdrożeniu](media/stream-analytics-edge/set_module.png)


> [!Note]
> W tym kroku ASA tworzy folder o nazwie "EdgeJobs" w kontenerze magazynu (jeśli go jeszcze nie istnieje). Dla każdego wdrożenia nowego podfolderu jest tworzony w folderze "EdgeJobs".
> Aby można było wdrożyć swoją pracę na urządzeniach krawędzi, ASA tworzy sygnatury dostępu współdzielonego (SAS) dla pliku definicji zadania. Klucz sygnatury dostępu Współdzielonego bezpiecznie są przesyłane do urządzenia IoT krawędzi przy użyciu podwójnego urządzenia. Po upływie tego klucza jest trzech lat od dnia jej tworzenia.


Aby uzyskać więcej informacji o wdrożeniach krawędzi IoT, zobacz Aby [tej strony](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurowanie tras
Krawędź IoT zapewnia sposób deklaratywnego tras wiadomości między modułami i między modułami i Centrum IoT. Pełna składnia jest opisany [tutaj](https://docs.microsoft.com/azure/iot-edge/module-composition).
Nazwy danych wejściowych i wyjściowych utworzonej przez zadanie ASA może służyć jako punkty końcowe dla routingu.  

###### <a name="example"></a>Przykład
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Ten przykład przedstawia trasy w scenariuszu opisano na poniższej ilustracji. Zawiera zadania krawędź o nazwie "**ASA**", przy użyciu danych wejściowych o nazwie "**temperatury**"i danych wyjściowych o nazwie"**alert**".
![Przykład routingu](media/stream-analytics-edge/RoutingExample.png)

W tym przykładzie zdefiniowano następujących tras:
- Każdy komunikat z **tempSensor** są wysyłane do modułu o nazwie **ASA** w danych wejściowych o nazwie **temperatury**,
- Wszystkie elementy wyjściowe **ASA** modułu są wysyłane do Centrum IoT połączone z tym urządzeniem (od$),
- Wszystkie elementy wyjściowe **ASA** modułu są wysyłane do **kontroli** punkt końcowy **tempSensor**.


## <a name="technical-information"></a>Informacje techniczne
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Bieżących ograniczeń dotyczących zadań krawędzi w porównaniu do zadań chmury
Celem jest parzystość platformy krawędzi zadań i zadań w chmurze. Większość funkcji języka zapytań SQL są już obsługiwane.
Jednak następujące funkcje nie są jeszcze obsługiwane krawędzi zadań:
* Funkcje zdefiniowane przez użytkownika (UDF) i agregacje zdefiniowane przez użytkownika (UDA).
* Funkcje uczenia Maszynowego Azure.
* Przy użyciu więcej niż 14 wartości zagregowanych w jednym kroku.
* Format AVRO wejścia/wyjścia. W tej chwili obsługiwane są tylko CSV i JSON.
* Następujące operatory SQL:
    * AnomalyDetection
    * Operatory dane geograficzne:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTYCJA PRZEZ
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Wymagania dotyczące sprzętu i środowiska wykonawczego
Aby uruchomić ASA na krawędzi IoT, potrzebujesz urządzeń, które można uruchomić [Azure IoT krawędzi](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA i Azure IoT krawędzi użyj **Docker** kontenery zapewnienie przenośnych rozwiązania działającego w wielu systemach operacyjnych hosta (z systemem Windows, Linux).

ASA na krawędzi IoT jest udostępniany jako obrazów systemu Windows i Linux w systemie zarówno x86 64 albo architektury usługi Azure Resource Manager. 


### <a name="input-and-output"></a>Dane wejściowe i wyjściowe
#### <a name="input-and-output-streams"></a>Dane wejściowe i strumienie wyjściowe
Krawędzi ASA zadań może pobrać wejścia i wyjścia z innych modułów uruchomione na urządzeniu IoT krawędzi. Nawiązywanie połączenia z i do określonych modułów, można ustawić konfiguracji routingu w czasie wdrażania. Więcej informacji można znaleźć na [dokumentacji kompozycji moduł krawędzi IoT](https://docs.microsoft.com/azure/iot-edge/module-composition).

Dla wejścia i wyjścia są obsługiwane formaty CSV i JSON.

Dla każdego wejściowe i strumień wyjściowy, możesz utworzyć w zadania ASA, odpowiedniego punktu końcowego jest tworzony w module wdrożone. Te punkty końcowe można w przypadku tras, wdrożenia.

W obecnych, jedynym obsługiwanym strumienia danych wejściowych i strumienia wyjściowego typy Centrum krawędzi. Odwołanie do danych wejściowych obsługuje odwoływać się do typu pliku. Inne dane wyjściowe można połączyć się przy użyciu zadania chmury poniżej. Na przykład zadanie usługi Stream Analytics hostowanej w programie Edge wysyła dane wyjściowe do koncentratora krawędzi, który można następnie Wyślij dane wyjściowe do Centrum IoT. Można użyć drugiego zadania usługi analiza strumienia Azure hostowane w chmurze przy użyciu danych wejściowych z Centrum IoT i dane wyjściowe do usługi Power BI lub inny typ danych wyjściowych.



##### <a name="reference-data"></a>Dane referencyjne
Dane referencyjne (znanej także jako tabela odnośnika) jest ograniczone zestawu danych, który jest statyczny lub powolne, zmiana charakter. Służy do wyszukiwania lub mieć związek z strumienia danych. Aby użyć danych odwołanie do zadania usługi analiza strumienia Azure, zwykle użyje [dołączenia danych odwołania](https://msdn.microsoft.com/library/azure/dn949258.aspx) w zapytaniu. Aby uzyskać więcej informacji, zobacz [ASA dokumentację dotyczącą danych referencyjnych](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Aby użyć danych referencyjnych ASA na krawędzi Iot, wykonaj następujące kroki: 
1. Utwórz nowe dane wejściowe dla zadania
2. Wybierz **danych referencyjnych** jako **typ źródła**.
3. Ustaw ścieżkę pliku. Ścieżka do pliku powinna być **bezwzględną** ścieżki pliku na urządzeniu ![odwołania Tworzenie danych](media/stream-analytics-edge/ReferenceData.png)
4. Włącz **udostępnione dyski** w konfiguracji Docker i upewnij się, że dysk jest włączone, przed rozpoczęciem wdrażania.

Aby uzyskać więcej informacji, zobacz [Docker w dokumentacji systemu Windows w tym miejscu](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> W tej chwili tylko dane lokalnego odwołania są obsługiwane.




## <a name="license-and-third-party-notices"></a>Licencja i powiadomienia dotyczące osób trzecich
* [Usługa Azure Stream Analytics na krawędzi IoT Podgląd licencji](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Powiadomienie innych firm dla usługi Azure Stream Analytics na krawędzi IoT Podgląd](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat krawędzi Iot Azure](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA na krawędzi IoT — samouczek](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Wyślij opinię do zespołu za pomocą tej ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
* [Tworzenie zadania Stream Analytics krawędzi za pomocą narzędzi Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
