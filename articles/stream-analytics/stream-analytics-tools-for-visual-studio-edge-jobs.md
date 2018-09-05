---
title: Zadania Edge w usłudze Azure Stream Analytics tools dla programu Visual Studio
description: W tym artykule opisano, jak tworzyć, debugować i tworzenie zadań usługi Stream Analytics Edge przy użyciu narzędzia Stream Analytics dla programu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/13/2018
ms.openlocfilehash: 5dc90a1334b525c02be3eae2985900ab07cf2e05
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696252"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Tworzenie zadań usługi Stream Analytics Edge przy użyciu narzędzi programu Visual Studio

W tym samouczku dowiesz się, jak używać usługi Stream Analytics tools for Visual Studio do tworzenia, debugowania i Utwórz zadania usługi Stream Analytics Edge. Po utworzeniu i przetestować zadanie, możesz przejść do witryny Azure portal w celu jego wdrożenia na urządzeniach z systemem. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące wymagania wstępne do ukończenia tego samouczka:

* Zainstaluj [programu Visual Studio 2017](https://www.visualstudio.com/downloads/), [programu Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), lub [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Obsługiwane są wersje Enterprise (Ultimate/Premium), Professional i Community. Wersja Express nie jest obsługiwana.  

* Postępuj zgodnie z [instrukcje dotyczące instalacji](stream-analytics-tools-for-visual-studio-edge-jobs.md) do zainstalowania narzędzia Stream Analytics tools for Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Utwórz projekt usługi Stream Analytics Edge 

W programie Visual Studio, wybierz **pliku** > **New** > **projektu**. Przejdź do **szablony** liście po lewej stronie > Rozwiń **usługi Azure Stream Analytics** > **usługi Stream Analytics Edge**  >   **Usługa Azure Stream Analytics krawędzi aplikacji**. Podaj nazwę, lokalizację i rozwiązania nazwę projektu i wybierz **OK**.

![Nowy projekt usługi Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Po utworzeniu projektu pobiera przejdź do **Eksploratora rozwiązań** Aby wyświetlić hierarchię folderów.

![Widok Eksploratora rozwiązań](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Wybierz poprawną subskrypcję

1. Z programu Visual Studio **widoku** menu, wybierz opcję **Eksploratora serwera**.  

2. Kliknij prawym przyciskiem myszy **Azure** > Wybierz **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure** >, a następnie zaloguj się przy użyciu konta platformy Azure.

## <a name="define-inputs"></a>Zdefiniuj dane wejściowe

1. Z **Eksploratora rozwiązań**, rozwiń węzeł **dane wejściowe** węzła powinny zostać wyświetlone dane wejściowe o nazwie **EdgeInput.json**. Kliknij dwukrotnie, aby wyświetlić jej ustawienia.  

2. Upewnij się, że typ źródła jest ustawiony **Stream danych** > źródło jest ustawiona na **Centrum usługi Edge** > Format serializacji zdarzenia równa **Json** > i kodowanie jest ustawiona na **UTF8**. Opcjonalnie możesz zmienić nazwę **Alias danych wejściowych**, możemy pozostawić w tym przykładzie. W przypadku, gdy zmienisz alias danych wejściowych, użyj imienia lub pseudonimu określony podczas definiowania kwerendy. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.  
   ![Konfiguracja danych wejściowych](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Zdefiniuj dane wyjściowe

1. Z **Eksploratora rozwiązań**, rozwiń węzeł **dane wyjściowe** węzłów dane wyjściowe powinny być o nazwie **EdgeOutput.json**. Kliknij dwukrotnie, aby wyświetlić jej ustawienia.  

2. Upewnij się, że ujścia jest ustawiona na wybierz **Centrum usługi Edge** > Format serializacji zdarzenia równa **Json** > i kodowanie jest ustawiona na **UTF8** > i Format jest ustawiona na  **Tablica**. Opcjonalnie możesz zmienić nazwę **Alias wyjściowy**, możemy pozostawić w tym przykładzie. W przypadku, gdy zmienisz nazwę aliasu danych wyjściowych, użyj imienia lub pseudonimu określony podczas definiowania kwerendy. Wybierz pozycję **Zapisz**, aby zapisać ustawienia. 
   ![Konfiguracja danych wyjściowych](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

Wdrażane w środowiskach krawędzi zadania usługi Stream Analytics obsługuje większość [dokumentacja języka zapytań usługi Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), jednak następujące operacje nie są jeszcze obsługiwane w przypadku zadań Edge: 


|**Kategoria**  | **Polecenie**  |
|---------|---------|
|Operatory danych geoprzestrzennych |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Inne operatory | <ul><li>PARTITION BY</li><li>SYGNATURA CZASOWA W TRYB FAILOVER</li><li>ODRĘBNE</li><li>Parametr wyrażenia w COUNT — operator</li><li>Mikrosekund w funkcji daty i godziny</li><li>JavaScript — UDA (Ta funkcja jest nadal w wersji zapoznawczej dla zadań wdrożonych w chmurze)</li></ul>   |

Po utworzeniu zadania usługi Edge w portalu kompilator będzie automatycznie ostrzega użytkownika, jeśli nie używasz obsługiwanej operatora.

Z programu Visual Studio, należy zdefiniować następujące zapytanie przekształcenia w edytorze zapytań (**pliku script.asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testowanie zadania lokalnie

Lokalnie, aby przetestować zapytanie należy przekazać przykładowe dane. Przykładowe dane można uzyskać, pobierając dane rejestracji z [repozytorium GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) i zapisz go na komputerze lokalnym. 

1. Aby przekazać przykładowe dane > kliknij prawym przyciskiem myszy **EdgeInput.json** Plik > Wybierz **dodawanie lokalnych danych wejściowych**  

2. W oknie podręcznym > **Przeglądaj** przykładowe dane z ścieżki lokalnej > Wybierz **Zapisz**.
   ![Lokalne konfiguracja danych wejściowych](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Plik o nazwie **local_EdgeInput.json** jest automatycznie dodawany do folderu danych wejściowych.  
4. Możesz uruchomić go lokalnie lub przesłać na platformę Azure. Aby przetestować zapytanie > Wybierz **uruchom lokalnie**.  
   ![Opcje uruchamiania](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. W oknie wiersza polecenia wyświetla stan zadania. Gdy zadanie zostanie wykonane pomyślnie, tworzy folder, który wygląda jak "2018-02-23-11-31-42" w ścieżce folderu projektu "2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42 programu Visual Studio". Przejdź do ścieżki folderu, aby wyświetlić wyniki w lokalnym folderze:

   Możesz również zalogować się do witryny Azure portal i sprawdź, czy zadanie jest tworzone. 

   ![Wynik folderu](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Prześlij zadanie na platformie Azure

1. Przed przesłaniem zadania na platformie Azure, musisz połączyć się z subskrypcją platformy Azure. Otwórz **Eksploratora serwera** > kliknij prawym przyciskiem myszy **Azure** > **nawiązywanie połączenia z subskrypcją Microsoft Azure** > Zaloguj się do subskrypcji platformy Azure.  

2. Aby przesłać zadanie do platformy Azure, przejdź do edytora zapytań > Wybierz **przesyłania na platformie Azure**.  

3. Zostanie otwarte okno podręczne, w którym można zaktualizować istniejące zadanie Microsoft Edge lub Utwórz nową. Podczas aktualizacji istniejącego zadania spowoduje zastąpienie wszystkich konfiguracji zadania, w tym scenariuszu, spowoduje opublikowanie nowego zadania. Wybierz **Tworzenie nowego zadania usługi Azure Stream Analytics** > Wprowadź nazwę dla zadania coś w rodzaju **MyASAEdgeJob** > Wybierz wymagane **subskrypcji**, **Grupy zasobów**, i **lokalizacji** > Wybierz **przesłać**.

   ![Przekaż do platformy Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Po utworzeniu zadania usługi Stream Analytics Edge mogą odwoływać się do [uruchamiaj zadania zgodnie z samouczkiem usługi IoT Edge](stream-analytics-edge.md) dowiesz się, jak można go wdrożyć na urządzeniach. 

## <a name="manage-the-job"></a>Zarządzanie nim 

Można wyświetlić stan zadania i diagram zadania z poziomu Eksploratora serwera. Z **Eksploratora serwera** > **usługi Stream Analytics** > Rozwiń subskrypcję i grupę zasobów, w której została wdrożona zadanie Edge > można wyświetlić MyASAEdgejob ze stanem  **Utworzone**. Rozwiń węzeł zadania i kliknij dwukrotnie ją, aby otworzyć widok zadań.

![Opcje Eksploratora serwera](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Okno widok zadania umożliwia operacje, takie jak odświeżyć zadanie trwa usuwanie zadania, Otwieranie zadania z platformy Azure portal itp.

![Diagram zadania i inne opcje](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat usługi Azure Iot Edge](../iot-edge/about-iot-edge.md)
* [Usługa ASA na samouczek dotyczący usługi IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Wyślij opinię do zespołu za pomocą tej ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
