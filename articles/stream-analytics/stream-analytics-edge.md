---
title: Azure Stream Analytics na urządzeniach IoT Edge
description: Tworzenie zadań krawędzi w usłudze Azure Stream Analytics i wdrażać je na urządzeniach z systemem Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 7e4a1cf43931b56cfdb3c31ffe870a07dbaa75af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201759"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na urządzeniach IoT Edge
 
Usługa Azure Stream Analytics (ASA) w usłudze IoT Edge umożliwia deweloperom wdrażanie analizy bliskiej czasowi rzeczywistemu bliżej urządzeń Internetu rzeczy, aby w pełni wykorzystać dane wygenerowane na urządzeniach. Usługę Azure Stream Analytics zaprojektowano pod kątem małych opóźnień, odporności, wydajnego wykorzystywania przepustowości i zgodności. Przedsiębiorstwa mogą teraz wdrażać logikę sterowania blisko operacji przemysłowych i uzupełniać analizę danych big data realizowaną w chmurze.  

Azure Stream Analytics na urządzeniach IoT Edge działa w ramach struktury [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) . Po utworzeniu zadania w ASA można wdrażać i zarządzać nią za pomocą usługi IoT Hub.

## <a name="scenarios"></a>Scenariusze
![Diagram wysokiego poziomu usługi IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Polecenie i kontrola o małym opóźnieniu**: na przykład systemy bezpieczeństwa produkcji muszą reagować na dane operacyjne z bardzo małym opóźnieniem. Za pomocą ASA na urządzeniach brzegowych IoT można analizować czujnika danych w czasie zbliżonym do rzeczywistego i wydawać polecenia, gdy wykrywa anomalie, aby zatrzymać maszynę lub wyzwolenia alertów.
*   **Ograniczona łączność z chmurą**: systemy o znaczeniu krytycznym, takie jak zdalny sprzęt górniczy, połączone statki lub przechodzenie na przybrzeżne, muszą analizować i reagować na dane nawet wtedy, gdy łączność z chmurą jest nieprzerwana. Dzięki ASA logika przesyłania strumieniowego jest uruchamiana niezależnie od łączności sieciowej, i można wybrać, co możesz wysłać do chmury do dalszego przetwarzania lub magazynowania.
* **Ograniczona przepustowość**: ilość danych wytworzonych przez aparaty Jet lub połączone samochody mogą być tak duże, że dane muszą być przefiltrowane lub wstępnie przetworzone przed wysłaniem ich do chmury. Za pomocą ASA, możesz filtrować lub agregacji danych, który ma być wysyłane do chmury.
* **Zgodność**: zgodność z przepisami może wymagać, aby pewne dane były lokalnie anonimowe lub zagregowane przed wysłaniem ich do chmury.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Zadania Edge w usłudze Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co to jest zadanie usługi "brzegu"?

Zadania programu ASA Edge są uruchamiane w kontenerach wdrożonych na [Azure IoT Edge urządzeniach](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Składają się z dwóch części:
1.  Część chmury, który jest odpowiedzialny za definicji zadania: użytkownikom Definiowanie danych wejściowych, danych wyjściowych, zapytań i innych ustawień (zdarzeń poza kolejnością, itp.) w chmurze.
2.  Moduł uruchamiania na urządzeniach IoT. Zawiera aparat ASA i odbiera definicji zadania w chmurze. 

ASA używa usługi IoT Hub do wdrożenia zadań edge do urządzeń. Więcej informacji na temat [wdrażania IoT Edge można znaleźć tutaj](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Zadania usługi Azure Stream Analytics krawędzi](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instrukcje dotyczące instalacji
Ogólne kroki są opisane w poniższej tabeli. W poniższych sekcjach podano więcej szczegółów.

|      |Krok   | Uwagi   |
| ---   | ---   |  ---      |
| 1   | **Tworzenie kontenera magazynu**   | Kontenery magazynu są używane do Zapisz swoją definicję zadania, w których są one dostępne przez urządzenia IoT. <br>  Można ponownie użyć dowolnego istniejącego kontenera magazynu.     |
| 2   | **Tworzenie zadania ASA Edge**   |  Utwórz nowe zadanie, wybierz pozycję **Edge** jako **środowisko hostingu**. <br> Te zadania są tworzone lub zarządzane w chmurze i uruchomić na urządzeniach usługi IoT Edge.     |
| 3   | **Skonfiguruj środowisko IoT Edge na urządzeniach**   | Instrukcje dla [systemu Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Wdrażanie ASA na urządzeniach IoT Edge**   |  Definicja zadania ASA są eksportowane do kontenera magazynu utworzoną wcześniej.       |

W [tym samouczku krok po kroku](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) można wdrożyć pierwsze zadanie ASA na IoT Edge. Poniższy klip wideo powinny pomóc w zrozumieniu procesu do uruchomienia zadania usługi Stream Analytics na urządzeniu usługi IoT edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Utwórz kontener magazynu
Kontener magazynu jest wymagane w celu eksportowania zapytania ASA skompilowane i konfiguracji zadania. Służy do skonfigurowania obrazu platformy Docker ASA przy użyciu określonego zapytania. 
1. Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) , aby utworzyć konto magazynu na podstawie Azure Portal. Możesz zachować wszystkie domyślne opcje tego konta za pomocą ASA.
2. W nowo utworzone konto magazynu należy utworzyć kontenera magazynu obiektów blob:
    1. Kliknij obiekt **BLOB**, a następnie pozycję **+ Container**. 
    2. Wprowadź nazwę i Zachowaj kontener jako **prywatny**.

#### <a name="create-an-asa-edge-job"></a>Tworzenie zadania usługi ASA Edge
> [!Note]
> Ten samouczek koncentruje się na tworzenie zadania ASA przy użyciu witryny Azure portal. Możesz również [użyć wtyczki programu Visual Studio, aby utworzyć zadanie ASA Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. W witrynie Azure portal należy utworzyć nowe "Zadanie usługi Stream Analytics". [Link bezpośredni do utworzenia nowego zadania asa w tym miejscu](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na ekranie Tworzenie wybierz pozycję **Edge** jako **środowisko hostingu** (zobacz poniższy obraz).

   ![Tworzenie zadania usługi Stream Analytics na urządzeniach brzegowych](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definicja zadania
    1. **Zdefiniuj strumienie wejściowe**. Umożliwia zdefiniowanie jednego lub kilku strumienie wejściowe dla zadania.
    2. Zdefiniuj dane referencyjne (opcjonalnie).
    3. **Zdefiniuj strumienie wyjściowe**. Umożliwia zdefiniowanie jednego lub wielu strumieni danych wyjściowych dla zadania. 
    4. **Zdefiniuj zapytanie**. Zdefiniuj zapytanie o ASA, w chmurze za pomocą wbudowanego edytora. Kompilator automatycznie sprawdza, czy składnia włączone dla przeglądarki edge ASA. Możesz również przetestować zapytanie, przekazując przykładowych danych. 

4. Ustaw informacje o kontenerze magazynu w menu **ustawienia IoT Edge** .

5. Ustawienia opcjonalne
    1. **Porządkowanie zdarzeń**. W portalu, można skonfigurować zasady poza kolejnością. Dokumentacja jest dostępna [tutaj](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Ustawienia regionalne**. Ustaw internalization format.



> [!Note]
> Po utworzeniu wdrożenia ASA eksportuje definicję zadania do kontenera magazynu. Ta definicja zadania pozostają bez zmian w czasie trwania wdrożenia. W rezultacie jeśli chcesz zaktualizować zadanie uruchomione na urządzeniach brzegowych, należy edytować zadania w ASA, a następnie utwórz nowe wdrożenie w usłudze IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurowanie środowiska usługi IoT Edge na Twoich urządzeń
Zadania Edge można wdrożyć na urządzeniach z systemem Azure IoT Edge.
W tym celu należy wykonać następujące kroki:
- Tworzenie Centrum Iot.
- Zainstaluj środowisko uruchomieniowe platformy Docker i usługi IoT Edge na urządzeniach brzegowych.
- Ustaw urządzenia jako **urządzenia IoT Edge** w IoT Hub.

Te kroki są opisane w dokumentacji IoT Edge dla [systemu Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Wdrożenia ASA na Twoich urządzeń usługi IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Dodawanie ASA do wdrożenia
- W Azure Portal Otwórz IoT Hub, przejdź do **IoT Edge** i kliknij urządzenie docelowe dla tego wdrożenia.
- Wybierz pozycję **Ustaw moduły**, a następnie wybierz pozycję **+ Dodaj** i wybierz pozycję **Azure Stream Analytics module**.
- Wybierz subskrypcję i zadania ASA Edge, który został utworzony. Kliknij pozycję Zapisz.
![dodać moduł ASA we wdrożeniu](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> W tym kroku ASA tworzy folder o nazwie "EdgeJobs" w kontenerze magazynu (jeśli go jeszcze nie istnieje). Dla każdego wdrożenia nowego podfolderu jest tworzony w folderze "EdgeJobs".
> Po wdrożeniu zadania do IoT Edge urządzenia ASA tworzy sygnaturę dostępu współdzielonego (SAS) dla pliku definicji zadania. Klucza sygnatury dostępu Współdzielonego jest bezpiecznie przesyłany do urządzenia usługi IoT Edge, za pomocą bliźniaczej reprezentacji urządzenia. Po upływie tego klucza jest trzech lat od dnia jego utworzenia. Po zaktualizowaniu zadania IoT Edge, sygnatura dostępu współdzielonego ulegnie zmianie, ale wersja obrazu nie ulegnie zmianie. Po **zaktualizowaniu**programu postępuj zgodnie z przepływem pracy wdrożenia, a na urządzeniu zostanie zarejestrowane powiadomienie o aktualizacji.


Aby uzyskać więcej informacji na temat wdrożeń IoT Edge, zobacz na [tej stronie](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurowanie tras
Usługi IoT Edge umożliwia deklaratywne kierowanie komunikatów między modułami i między modułami i IoT Hub. Pełna składnia została opisana [tutaj](https://docs.microsoft.com/azure/iot-edge/module-composition).
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
Ten przykład pokazuje trasy dla scenariusza opisanego na poniższej ilustracji. Zawiera ono zadanie brzegowe o nazwie "**ASA**" z danymi wejściowymi o nazwie "**temperatura**" i wyjście o nazwie "**alert**".
przykład ![diagramu](media/stream-analytics-edge/edge-message-routing-example.png) routingu wiadomości

W tym przykładzie definiuje następujące trasy:
- Każdy komunikat z **tempSensor** jest wysyłany do modułu o nazwie **ASA** do wejścia o nazwie **temperatura**,
- Wszystkie dane wyjściowe modułu **ASA** są wysyłane do IoT Hub połączonej z tym urządzeniem ($upstream),
- Wszystkie dane wyjściowe modułu **ASA** są wysyłane do punktu końcowego **kontroli** **tempSensor**.


## <a name="technical-information"></a>Informacje techniczne
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Bieżące ograniczenia dla zadań usługi IoT Edge w porównaniu do zadania w chmurze
Celem jest zapewnienie równoważności między zadaniami usługi IoT Edge i zadania w chmurze. Obsługiwane są większość funkcji języka zapytań SQL, co umożliwia uruchamianie tej samej logiki w chmurze i IoT Edge.
Jednak następujące funkcje nie są jeszcze obsługiwane w przypadku zadań edge:
* Funkcje zdefiniowane przez użytkownika (UDF) w języku JavaScript. System UDF jest dostępny w programie [ C# for IoT Edge Jobs](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (wersja zapoznawcza).
* Agregacje zdefiniowane przez użytkownika (UDA).
* Funkcje usługi Azure ML.
* Przy użyciu więcej niż 14 agregatów w jednym kroku.
* Format AVRO dla wejścia/wyjścia. W tej chwili obsługiwane są tylko CSV i JSON.
* Następujące operatory SQL:
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Wymagania dotyczące sprzętu i środowiska uruchomieniowego
Aby uruchamiać ASA na IoT Edge, potrzebne są urządzenia, które mogą uruchamiać [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA i Azure IoT Edge używają kontenerów **platformy Docker** , aby zapewnić przenośne rozwiązanie działające w wielu systemach operacyjnych hosta (Windows, Linux).

ASA na IoT Edge są udostępniane jako obrazy systemu Windows i Linux, uruchomione na architekturze x86-64 lub ARM (Advanced RISC Machines). 


### <a name="input-and-output"></a>Dane wejściowe i wyjściowe
#### <a name="input-and-output-streams"></a>Dane wejściowe i strumienie wyjściowe
Zadania ASA Edge można uzyskać dane wejściowe i wyjściowe z innych modułów, działające na urządzeniach usługi IoT Edge. Aby połączyć z i do określonych modułów, można ustawić konfiguracji routingu w czasie wdrażania. Więcej informacji znajduje się w [dokumentacji dotyczącej kompozycji modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Dane wejściowe i wyjściowe CSV i JSON formaty są obsługiwane.

Dla każdego dane wejściowe i strumień wyjściowy utworzysz w ramach zadania ASA, odpowiedni punkt końcowy jest tworzony na wdrożonym module. Te punkty końcowe może służyć w tras w danym wdrożeniu.

Co jest obecny, obsługiwana jest tylko wejście strumienia i typy danych wyjściowych strumieni są Centrum usługi Edge. Obsługuje dane wejściowe odwołania odwołań typu pliku. Inne dane wyjściowe można osiągnąć za pomocą zadania w chmurze. Na przykład zadanie usługi Stream Analytics, hostowana w przeglądarce Edge wysyła dane wyjściowe do Centrum usługi Microsoft Edge, który można wysłać dane wyjściowe do Centrum IoT Hub. Można użyć drugie zadanie usługi Azure Stream Analytics hostowane w chmurze przy użyciu danych wejściowych z Centrum IoT i danych wyjściowych do usługi Power BI lub innego typu danych wyjściowych.



##### <a name="reference-data"></a>Dane referencyjne
Dane referencyjne (znany także jako tabela odnośnika) jest ograniczone zestaw danych, który jest statyczny lub powolne, zmieniając charakter. Służy do wyszukiwania lub skorelowane ze strumienia danych. Aby korzystać z danych referencyjnych w zadaniu Azure Stream Analytics, zazwyczaj użyjesz [sprzężenia danych referencyjnych](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) w zapytaniu. Aby uzyskać więcej informacji, zobacz [Korzystanie z danych referencyjnych dla odnośników w Stream Analytics](stream-analytics-use-reference-data.md).

Tylko dane referencyjne lokalnych jest obsługiwane. Gdy zadanie jest wdrożone na urządzeniu usługi IoT Edge, ładuje danych referencyjnych ze ścieżki plików zdefiniowanych przez użytkownika.

Aby utworzyć zadanie z danymi referencyjnymi w programie Edge:

1. Utwórz nowe dane wejściowe dla zadania.

2. Wybierz **dane referencyjne** jako **Typ źródła**.

3. Ma odwołanie do pliku danych gotowe na urządzeniu. Dla kontenera Windows umieścić do referencyjnego pliku danych na dysku lokalnym, a następnie udostępniać dysku lokalnego kontenera platformy Docker. Dla kontenera systemu Linux należy utworzyć wolumin platformy Docker i wypełnić plik danych do woluminu.

4. Ustaw ścieżkę do pliku. W przypadku systemu operacyjnego hosta Windows i kontenera systemu Windows użyj ścieżki bezwzględnej: `E:\<PathToFile>\v1.csv`. W przypadku systemu operacyjnego hosta Windows i systemu Linux oraz kontenera systemu operacyjnego Linux i Linux użyj ścieżki w woluminie: `<VolumeName>/file1.txt`.

![Nowe wejściowych danych referencyjnych dla zadania usługi Azure Stream Analytics w usłudze IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Dane referencyjne w aktualizacji usługi IoT Edge jest wyzwalana przez wdrożenie. Po wyzwoleniu moduł ASA wybiera zaktualizowane dane bez konieczności zatrzymywania uruchomionego zadania.

Istnieją dwa sposoby, aby zaktualizować dane referencyjne:
* Aktualizuj odwołanie do ścieżki danych w ramach zadania ASA w witrynie Azure portal.
* Aktualizuj wdrożenie usługi IoT Edge.

## <a name="license-and-third-party-notices"></a>Bieżąca licencja i uwagi dotyczące innych firm
* [Azure Stream Analytics na urządzeniach IoT Edge licencji](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Powiadomienie innych firm dotyczące Azure Stream Analytics na urządzeniach IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informacje o obrazie modułu Azure Stream Analytics 

Informacje o tej wersji zostały ostatnio zaktualizowane w dniu 2019-06-27:

- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - obraz podstawowy: Microsoft/dotnet: 2.1.6-Runtime-Alpine 3.7
   - platformach
      - Architektura: amd64
      - system operacyjny: Linux
  
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - obraz podstawowy: Microsoft/dotnet: 2.1.6-Runtime-Bionic-arm32v7
   - platformach
      - Architektura: ARM
      - system operacyjny: Linux
  
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - obraz podstawowy: Microsoft/dotnet: 2.1.6-Runtime-nanoserver-1809
   - platformach
      - Architektura: amd64
      - system operacyjny: Windows
      
      
## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, wypróbuj [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Więcej informacji na temat usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [IoT Edge — samouczek](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Opracowywanie zadań Stream Analytics Edge przy użyciu narzędzi Visual Studio Tools](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementowanie ciągłej integracji/ciągłego dostarczania Stream Analytics przy użyciu interfejsów API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
