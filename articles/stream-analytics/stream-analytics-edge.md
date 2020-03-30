---
title: Azure Stream Analytics na urządzeniach IoT Edge
description: Twórz zadania brzegowe w usłudze Azure Stream Analytics i wdrażaj je na urządzeniach z usługą Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb1bd018866bda9270b78507f0462b6c4d4ea17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475896"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na urządzeniach IoT Edge
 
Usługa Azure Stream Analytics (ASA) w usłudze IoT Edge umożliwia deweloperom wdrażanie analizy bliskiej czasowi rzeczywistemu bliżej urządzeń Internetu rzeczy, aby w pełni wykorzystać dane wygenerowane na urządzeniach. Usługę Azure Stream Analytics zaprojektowano pod kątem małych opóźnień, odporności, wydajnego wykorzystywania przepustowości i zgodności. Przedsiębiorstwa mogą teraz wdrażać logikę sterowania blisko operacji przemysłowych i uzupełniać analizę danych big data realizowaną w chmurze.  

Usługa Azure Stream Analytics w usłudze IoT Edge działa w ramach usługi [Azure IoT Edge.](https://azure.microsoft.com/campaigns/iot-edge/) Po utworzeniu zadania w asa, można wdrożyć i zarządzać nim za pomocą Usługi IoT Hub.

## <a name="scenarios"></a>Scenariusze
![Diagram wysokiego poziomu IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* Polecenie i kontrola o **małym opóźnieniu:** Na przykład systemy bezpieczeństwa produkcji muszą reagować na dane operacyjne z bardzo małym opóźnieniem. Dzięki asa na IoT Edge, można analizować dane z czujników w czasie zbliżonym do rzeczywistego i wydawać polecenia podczas wykrywania anomalii, aby zatrzymać komputer lub wyzwalać alerty.
*   **Ograniczona łączność z chmurą:** systemy o znaczeniu krytycznym, takie jak zdalny sprzęt górniczy, podłączone statki lub wiercenie na morzu, muszą analizować dane i reagować na nie, nawet gdy łączność w chmurze jest przerywana. Dzięki usłudze ASA logika przesyłania strumieniowego działa niezależnie od łączności sieciowej i można wybrać, co wysyłasz do chmury w celu dalszego przetwarzania lub przechowywania.
* **Ograniczona przepustowość:** Ilość danych wytwarzanych przez silniki odrzutowe lub podłączone samochody może być tak duża, że dane muszą być filtrowane lub wstępnie przetworzone przed wysłaniem ich do chmury. Za pomocą asa, można filtrować lub agregować dane, które muszą być wysyłane do chmury.
* **Zgodność:** Zgodność z przepisami może wymagać, aby niektóre dane były anonimowe lokalnie lub agregowane przed wysłaniem do chmury.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Zadania brzegowe w usłudze Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co to jest zadanie "krawędzi"?

Zadania ASA Edge są uruchamiane w kontenerach wdrożonych na [urządzeniach usługi Azure IoT Edge.](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works) Składają się one z dwóch części:
1.  Część chmury, która jest odpowiedzialna za definicję zadania: użytkownicy definiują dane wejściowe, dane wyjściowe, kwerendy i inne ustawienia (zdarzenia poza kolejnością itp.) w chmurze.
2.  Moduł uruchomiony na urządzeniach IoT. Zawiera aparat ASA i odbiera definicję zadania z chmury. 

ASA używa usługi IoT Hub do wdrażania zadań brzegowych na urządzeniach. Więcej informacji na temat [wdrażania usługi IoT Edge można znaleźć tutaj](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Zadanie usługi Azure Stream Analytics Edge](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instrukcje instalacji
Kroki wysokiego poziomu są opisane w poniższej tabeli. Więcej szczegółów podano w poniższych sekcjach.

|      |Krok   | Uwagi   |
| ---   | ---   |  ---      |
| 1   | **Tworzenie kontenera magazynu**   | Kontenery magazynu są używane do zapisywania definicji zadania, gdzie są dostępne dla urządzeń IoT. <br>  Można ponownie użyć dowolnego istniejącego kontenera magazynu.     |
| 2   | **Tworzenie zadania krawędzi ASA**   |  Utwórz nowe zadanie, wybierz **Edge** jako **środowisko hostingu**. <br> Te zadania są tworzone/zarządzane z chmury i uruchamiane na własnych urządzeniach Usługi IoT Edge.     |
| 3   | **Konfigurowanie środowiska Usługi IoT Edge na urządzeniach**   | Instrukcje dla [systemu Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Wdrażanie asa na urządzeniach ioT Edge**   |  Definicja zadania ASA jest eksportowana do kontenera magazynu utworzonego wcześniej.       |

Możesz wykonać [ten samouczek krok po kroku,](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) aby wdrożyć swoje pierwsze zadanie ASA w umywłanie IoT Edge. Poniższy klip wideo powinien pomóc w zrozumieniu procesu uruchamiania zadania usługi Stream Analytics na urządzeniu brzegowym IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
Kontener magazynu jest wymagany do wyeksportowania skompilowanego zapytania ASA i konfiguracji zadania. Służy do konfigurowania obrazu platformy Asa Docker za pomocą określonej kwerendy. 
1. Postępuj zgodnie z [tymi instrukcjami,](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) aby utworzyć konto magazynu z witryny Azure portal. Możesz zachować wszystkie opcje domyślne, aby korzystać z tego konta w asa.
2. Na nowo utworzonym koncie magazynu utwórz kontener magazynu obiektów blob:
    1. Kliknij **na obiekty Blobs**, a następnie **+ Kontener**. 
    2. Wprowadź nazwę i zachowaj kontener jako **prywatny**.

#### <a name="create-an-asa-edge-job"></a>Tworzenie zadania ASA Edge
> [!Note]
> Ten samouczek koncentruje się na tworzeniu zadań ASA przy użyciu witryny Azure portal. Można również [użyć wtyczki programu Visual Studio, aby utworzyć zadanie ASA Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. W witrynie Azure portal utwórz nowe zadanie "Usługa Analizy strumienia". [Bezpośrednie łącze, aby utworzyć nowe zadanie ASA tutaj](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na ekranie tworzenia wybierz **Edge** jako **środowisko hostingu** (zobacz poniższy rysunek)

   ![Tworzenie zadania usługi Stream Analytics na przeglądarce Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definicja zadania
    1. **Zdefiniuj strumienie wejściowe**. Zdefiniuj jeden lub kilka strumieni wejściowych dla zadania.
    2. Zdefiniuj dane referencyjne (opcjonalnie).
    3. **Zdefiniuj strumienie wyjściowe**. Zdefiniuj jeden lub kilka strumieni wyjściowych dla zadania. 
    4. **Zdefiniuj kwerendę**. Zdefiniuj kwerendę ASA w chmurze za pomocą edytora wbudowanego. Kompilator automatycznie sprawdza składnię włączoną dla krawędzi ASA. Można również przetestować zapytanie, przekazując przykładowe dane. 

4. Ustaw informacje o kontenerze magazynu w menu **ustawienia usługi IoT Edge.**

5. Ustawianie ustawień opcjonalnych
    1. **Kolejność zdarzeń**. Zasady poza kolejnością można skonfigurować w portalu. Dokumentacja jest dostępna [tutaj](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Ustawienia regionalne**. Ustaw format internalizacji.



> [!Note]
> Podczas tworzenia wdrożenia asa eksportuje definicję zadania do kontenera magazynu. Ta definicja zadania pozostają takie same w czasie trwania wdrożenia. W związku z tym, jeśli chcesz zaktualizować zadanie uruchomione na krawędzi, należy edytować zadanie w asa, a następnie utworzyć nowe wdrożenie w Centrum IoT.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurowanie środowiska IoT Edge na urządzeniach
Zadania usługi Edge można wdrożyć na urządzeniach z usługą Azure IoT Edge.
W tym celu należy wykonać następujące kroki:
- Utwórz Centrum Iot.
- Zainstaluj środowisko wykonawcze Platformy Docker i IoT Edge na urządzeniach brzegowych.
- Ustaw swoje urządzenia jako **urządzenia Usługi IoT Edge** w Uorce IoT.

Te kroki są opisane w dokumentacji usługi IoT Edge dla [systemu Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Wdrażanie asa na urządzeniach usługi IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Dodawanie asa do wdrożenia
- W witrynie Azure portal otwórz centrum IoT Hub, przejdź do **usługi IoT Edge** i kliknij urządzenie, na które chcesz kierować dane dotyczące tego wdrożenia.
- Wybierz **pozycję Ustaw moduły**, a następnie wybierz + **Dodaj** i wybierz moduł usługi Azure **Stream Analytics**.
- Wybierz subskrypcję i utworzone zadanie ASA Edge. Kliknij pozycję Zapisz.
![Dodawanie modułu ASA we wdrożeniu](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Podczas tego kroku ASA tworzy folder o nazwie "EdgeJobs" w kontenerze magazynu (jeśli jeszcze nie istnieje). Dla każdego wdrożenia w folderze "EdgeJobs" tworzony jest nowy podfolder.
> Po wdrożeniu zadania na urządzeniach Z krawędzi IoT ASA tworzy sygnaturę dostępu współdzielonego (SAS) dla pliku definicji zadania. Klucz SAS jest bezpiecznie przesyłany do urządzeń Usługi IoT Edge przy użyciu bliźniaczej reprezentacji urządzenia. Wygaśnięcie tego klucza wynosi trzy lata od dnia jego utworzenia. Po zaktualizowaniu zadania usługi IoT Edge sygnatura dostępu Współdzielonego zmieni się, ale wersja obrazu nie ulegnie zmianie. Po **zaktualizowaniu**postępuj zgodnie z przepływem pracy wdrażania, a powiadomienie o aktualizacji jest rejestrowane na urządzeniu.


Aby uzyskać więcej informacji na temat wdrożeń usługi IoT Edge, zobacz [tę stronę](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurowanie tras
Aplikacja IoT Edge umożliwia deklaratywne rozsyłanie komunikatów między modułami oraz między modułami i centrum IoT Hub. Pełna składnia jest [opisana w tym miejscu](https://docs.microsoft.com/azure/iot-edge/module-composition).
Nazwy danych wejściowych i wyjściowych utworzonych w zadaniu ASA mogą być używane jako punkty końcowe dla routingu.  

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
W tym przykładzie przedstawiono trasy dla scenariusza opisanego na poniższej ilustracji. Zawiera zadanie krawędzi o nazwie "**ASA**", z wejściem o nazwie "**temperature**" i wyjście o nazwie "**alert**".
![Przykład diagramu routingu wiadomości](media/stream-analytics-edge/edge-message-routing-example.png)

W tym przykładzie zdefiniowano następujące trasy:
- Każdy komunikat z **tempSensor** jest wysyłany do modułu o nazwie **ASA** do wejścia o nazwie **temperatura**,
- Wszystkie wyjścia modułu **ASA** są wysyłane do Centrum IoT połączonego z tym urządzeniem ($upstream),
- Wszystkie wyjścia modułu **ASA** są wysyłane do punktu końcowego **sterowania** **tempSensor**.


## <a name="technical-information"></a>Informacje techniczne
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Bieżące ograniczenia dla zadań usługi IoT Edge w porównaniu z zadaniami w chmurze
Celem jest, aby mieć parzystość między zadaniami usługi IoT Edge i zadania w chmurze. Większość funkcji języka zapytań SQL są obsługiwane, umożliwiając uruchomienie tej samej logiki w chmurze i usługi IoT Edge.
Jednak następujące funkcje nie są jeszcze obsługiwane dla zadań brzegowych:
* Funkcje zdefiniowane przez użytkownika (UDF) w języku JavaScript. UDF są dostępne w [języku C# dla zadań usługi IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (wersja zapoznawcza).
* Agregaty zdefiniowane przez użytkownika (UDA).
* Funkcje usługi Azure ML.
* Przy użyciu więcej niż 14 agregatów w jednym kroku.
* format AVRO dla wejścia/wyjścia. W tej chwili obsługiwane są tylko csv i JSON.
* Następujące operatory SQL:
    * PARTYCJA PRZEZ
    * GetMetadataPropertyValue
* Zasady dotyczące późnego przyjazdu

### <a name="runtime-and-hardware-requirements"></a>Wymagania dotyczące środowiska wykonawczego i sprzętu
Aby uruchomić asa na ioT Edge, potrzebujesz urządzeń, które mogą uruchamiać [usługę Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA i Usługa Azure IoT Edge używają kontenerów **platformy Docker,** aby zapewnić przenośne rozwiązanie działające w wielu systemach operacyjnych hosta (Windows, Linux).

ASA na IoT Edge jest dostępna jako obrazy Windows i Linux, działająca zarówno na architekturach x86-64, jak i ARM (Advanced RISC Machines). 


### <a name="input-and-output"></a>Dane wejściowe i wyjściowe
#### <a name="input-and-output-streams"></a>Strumienie wejściowe i wyjściowe
Zadania ASA Edge mogą uzyskać wejścia i wyjścia z innych modułów uruchomionych na urządzeniach IoT Edge. Aby połączyć się z i do określonych modułów, można ustawić konfigurację routingu w czasie wdrażania. Więcej informacji opisano w [dokumentacji składu modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Zarówno dla wejść, jak i wyjść obsługiwane są formaty CSV i JSON.

Dla każdego strumienia wejściowego i wyjściowego utworzonego w zadaniu ASA odpowiedni punkt końcowy jest tworzony w wdrożonym module. Te punkty końcowe mogą być używane w trasach wdrożenia.

Obecnie jedynymi obsługiwanymi typami danych wejściowych i wyjściowych strumienia są Edge Hub. Wejście referencyjne obsługuje typ pliku odwołania. Inne dane wyjściowe można uzyskać za pomocą zadania chmury niższego rzędu. Na przykład zadanie usługi Stream Analytics hostowane w aplikacji Edge wysyła dane wyjściowe do usługi Edge Hub, które następnie można wysłać dane wyjściowe do usługi IoT Hub. Można użyć drugiego zadania usługi Azure Stream Analytics hostowanego w chmurze z danymi wejściowymi z usługi IoT Hub i danymi wyjściowymi do usługi Power BI lub innego typu wyjściowego.



##### <a name="reference-data"></a>Dane referencyjne
Dane referencyjne (znane również jako tabela odnośne) to skończony zestaw danych, który ma charakter statyczny lub wolno się zmienia. Służy do wykonywania wyszukiwania lub skorelowania ze strumieniem danych. Aby korzystać z danych referencyjnych w zadaniu usługi Azure Stream Analytics, zazwyczaj należy użyć [przyłącza danych referencyjnych](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) w zapytaniu. Aby uzyskać więcej informacji, zobacz [korzystanie z danych referencyjnych dla wyszukiwania w usłudze Stream Analytics](stream-analytics-use-reference-data.md).

Obsługiwane są tylko lokalne dane referencyjne. Gdy zadanie jest wdrażane na urządzeniu Usługi IoT Edge, ładuje dane referencyjne ze ścieżki pliku zdefiniowanej przez użytkownika.

Aby utworzyć zadanie z danymi referencyjnymi na krawędzi:

1. Utwórz nowe dane wejściowe dla zadania.

2. Wybierz pozycję **Dane referencyjne** jako **typ źródła**.

3. Przygotuj plik danych referencyjnych na urządzeniu. W przypadku kontenera systemu Windows umieść plik danych referencyjnych na dysku lokalnym i udostępnij dysk lokalny kontenerowi platformy Docker. W przypadku kontenera systemu Linux utwórz wolumin platformy Docker i wypełnij plik danych na woluminie.

4. Ustaw ścieżkę pliku. W przypadku systemu operacyjnego Windows Host i `E:\<PathToFile>\v1.csv`kontenera systemu Windows należy użyć ścieżki bezwzględnej: . W przypadku kontenera windows hosta i systemu Linux lub kontenera systemu `<VolumeName>/file1.txt`operacyjnego Linux i systemu Linux użyj ścieżki w woluminie: .

![Nowe dane referencyjne dla zadania usługi Azure Stream Analytics w usłudze IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Dane referencyjne w aktualizacji usługi IoT Edge są wyzwalane przez wdrożenie. Po wyzwoleniu moduł ASA wybiera zaktualizowane dane bez zatrzymywania uruchomionego zadania.

Istnieją dwa sposoby aktualizowania danych referencyjnych:
* Aktualizowanie ścieżki danych referencyjnych w zadaniu asa z witryny Azure portal.
* Zaktualizuj wdrożenie usługi IoT Edge.

## <a name="license-and-third-party-notices"></a>Informacje o licencjach i powiadomieniach innych firm
* [Licencja usługi Azure Stream Analytics na podstawie usługi IoT.](https://go.microsoft.com/fwlink/?linkid=862827) 
* [Powiadomienie innych firm dotyczące usługi Azure Stream Analytics w usłudze IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informacje o obrazie modułu usługi Azure Stream Analytics 

Ta wersja została ostatnio zaktualizowana w dniu 2019-06-27:

- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - obraz podstawowy: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - Platformy:
      - architektura: amd64
      - os: linux
  
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - obraz podstawowy: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - Platformy:
      - architektura: ramię
      - os: linux
  
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - obraz podstawowy: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - Platformy:
      - architektura: amd64
      - os: okna
      
      
## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, wypróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Więcej informacji o usłudze Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Asa w UIO Edge samouczek](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Tworzenie zadań usługi Stream Analytics Edge przy użyciu narzędzi programu Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementowanie ciągłej integracji/ciągłego wdrażania dla usługi Stream Analytics przy użyciu interfejsów API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
