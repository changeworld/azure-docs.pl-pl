---
title: Krawędzi zadania usługi analiza strumienia Azure tools dla programu Visual Studio
description: W tym artykule opisano sposób tworzenia, debugowania i Utwórz użytkownika zadania Stream Analytics krawędzi za pomocą narzędzi usługi analiza strumienia dla programu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/13/2018
ms.openlocfilehash: ec916ea2104df9d694aad2462df7ca5a487017f2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Tworzenie zadania Stream Analytics krawędzi za pomocą narzędzi Visual Studio

W tym samouczku Dowiedz się jak używać usługi Stream Analytics tools dla Visual Studio do tworzenia, debugowania i Utwórz użytkownika zadania Stream Analytics krawędzi. Po utworzeniu i przetestować zadania, można przejść do portalu Azure, aby wdrożyć na urządzeniach. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące wymagania wstępne do ukończenia tego samouczka:

* Zainstaluj [programu Visual Studio 2017](https://www.visualstudio.com/downloads/), [programu Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), lub [programu Visual Studio 2013 z aktualizacją 4](https://www.microsoft.com/download/details.aspx?id=45326). Enterprise (Ultimate/Premium), Professional i społeczności wersje są obsługiwane. Express edition nie jest obsługiwane.  

* Postępuj zgodnie z [instrukcje dotyczące instalacji](stream-analytics-tools-for-visual-studio-edge-jobs.md) do zainstalowania narzędzia do analizy strumienia dla programu Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Tworzenie projektu krawędzi analiza strumienia 

W programie Visual Studio, wybierz **pliku** > **nowy** > **projektu**. Przejdź do **szablony** liście z lewej strony > Rozwiń **Azure Stream Analytics** > **Stream Analytics krawędzi**  >   **Azure Stream Analytics krawędzi aplikacji**. Podaj nazwę nazwy, lokalizacji i rozwiązania dla projektu i wybierz **OK**.

![Nowy projekt krawędzi](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Po utworzeniu pobiera projektu, przejdź do **Eksploratora rozwiązań** Aby wyświetlić hierarchię folderów.

![Widok Eksploratora rozwiązania](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Wybierz poprawną subskrypcję

1. Z programu Visual Studio **widoku** menu, wybierz opcję **Eksploratora serwera**.  

2. Kliknij prawym przyciskiem myszy **Azure** > Wybierz **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure** >, a następnie zaloguj się przy użyciu konta platformy Azure.

## <a name="define-inputs"></a>Definiowanie danych wejściowych

1. Z **Eksploratora rozwiązań**, rozwiń węzeł **dane wejściowe** węzła powinny pojawić się dane wejściowe o nazwie **EdgeInput.json**. Kliknij dwukrotnie, aby wyświetlić jej ustawienia.  

2. Upewnij się, że typ źródłowy jest ustawiony na **strumienia danych** > ustawiono źródła **Centrum krawędzi** > Format serializacji zdarzeń wartość **Json** > i kodowanie ma ustawioną wartość **UTF8**. Opcjonalnie można zmienić nazwy **Alias wejściowy**, umożliwia pozostawienie go jako jest w tym przykładzie. W przypadku, gdy zostanie zmieniona nazwa aliasu wejścia, należy użyć nazwy określone podczas definiowania zapytania. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.  
   ![Wejściowy konfiguracji](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definiowanie danych wyjściowych

1. Z **Eksploratora rozwiązań**, rozwiń węzeł **dane wyjściowe** węzła powinna pojawić się dane wyjściowe o nazwie **EdgeOutput.json**. Kliknij dwukrotnie, aby wyświetlić jej ustawienia.  

2. Upewnij się, czy obiekt Sink jest ustawiony na wybierz **Centrum krawędzi** > Format serializacji zdarzeń wartość **Json** > i kodowanie ma ustawioną wartość **UTF8** > i ma ustawioną wartość formatu  **Tablica**. Opcjonalnie można zmienić nazwy **Alias wyjściowy**, umożliwia pozostawienie go jako jest w tym przykładzie. W przypadku, gdy zostanie zmieniona nazwa aliasu danych wyjściowych, należy użyć nazwy określone podczas definiowania zapytania. Wybierz pozycję **Zapisz**, aby zapisać ustawienia. 
   ![Dane wyjściowe konfiguracji](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Zdefiniuj zapytanie o transformacji

Wdrożone w środowiskach krawędzi zadania usługi analiza strumienia obsługuje większość [odwołania Stream Analytics Query Language](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), ale następujące operacje nie są jeszcze obsługiwane krawędzi zadań: 


|**Kategoria**  | **Polecenie**  |
|---------|---------|
|Operatory lokalizacji geograficznych |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Inne operatory | <ul><li>PARTYCJA PRZEZ</li><li>SYGNATURA CZASOWA W TRYB FAILOVER</li><li>RÓŻNE</li><li>Parametr wyrażenia w COUNT — operator</li><li>Mikrosekund w funkcji daty i godziny</li><li>UDA JavaScript (Ta funkcja jest dostępny w wersji zapoznawczej zadań wdrożonych w chmurze)</li></ul>   |

Podczas tworzenia zadania krawędzi w portalu, kompilator będzie automatycznie ostrzega użytkownika, jeśli nie używasz operatorem obsługiwanych.

W Visual Studio, należy zdefiniować następujące zapytanie transformacji w edytorze zapytań (**pliku script.asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testowanie zadanie lokalnie

Aby przetestować zapytanie lokalnie, należy przekazać przykładowe dane. Przykładowe dane można uzyskać, pobierając danych rejestracji z [repozytorium GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) i zapisz go na komputerze lokalnym. 

1. Aby przekazać przykładowe dane > kliknij prawym przyciskiem myszy **EdgeInput.json** pliku > Wybierz **Dodaj lokalne dane wejściowe**  

2. W oknie podręcznym > **Przeglądaj** przykładowych danych z ścieżki lokalnej > Wybierz **zapisać**.
   ![Lokalnej konfiguracji wejściowych](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Plik o nazwie **local_EdgeInput.json** jest automatycznie dodawany do folderu danych wejściowych.  
4. można uruchomić lokalnie lub przesłać na platformę Azure. Aby przetestować zapytanie > Wybierz **uruchom lokalnie**.  
   ![Opcje uruchamiania](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. Okno wiersza polecenia pokazuje stan zadania. Gdy zadanie zostanie wykonane pomyślnie, tworzy folder, który wygląda jak "2018-02-23-11-31-42" w ścieżce folderu projektu "Programu Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Przejdź do ścieżki folderu, aby wyświetlić wyniki w folderze lokalnym:

   Możesz również zalogować się do portalu Azure i sprawdź, czy zadanie jest utworzony. 

   ![Folder wyników](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Przesłać zadania na platformie Azure

1. Aby przesłać zadanie na platformie Azure, należy nawiązać subskrypcji platformy Azure. Otwórz **Eksploratora serwera** > kliknij prawym przyciskiem myszy **Azure** > **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure** > Zaloguj się do subskrypcji platformy Azure.  

2. Aby przesłać zadanie na platformie Azure, przejdź do edytora zapytań > Wybierz **przesyłania Azure**.  

3. Zostanie otwarte okno podręczne, w którym można zaktualizować istniejące zadanie Edge lub Utwórz nową. Podczas aktualizacji istniejącego zadania spowoduje zastąpienie wszystkich konfiguracji zadania, w tym scenariuszu, będzie publikować nowe zadanie. Wybierz **utworzyć nowego zadania usługi analiza strumienia Azure** > Wprowadź nazwę dla zadania coś, takich jak **MyASAEdgeJob** > Wybierz wymagane **subskrypcji**, **Grupy zasobów**, i **lokalizacji** > Wybierz **przesłać**.

   ![Przekaż do platformy Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Po utworzeniu zadania Stream Analytics krawędzi mogą odwoływać się do [uruchamiania zadań na krawędzi IoT samouczek](stream-analytics-edge.md) Aby dowiedzieć się, jak wdrożyć na urządzeniach. 

## <a name="manage-the-job"></a>Zadanie zarządzania 

Można wyświetlić stan zadania i diagram zadania z Eksploratora serwera. Z **Eksploratora serwera** > **Stream Analytics** > Rozwiń subskrypcji i grupy zasobów, wdrożonym zadania krawędzi > można wyświetlić MyASAEdgejob ze stanem  **Utworzony**. Rozwiń węzeł zadanie i kliknij dwukrotnie plik można otworzyć widoku zadania.

![Opcje Eksploratora serwera](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Okno widoku zadania umożliwia operacji, takich jak odświeżyć zadania, usuwanie zadania, otwarcie zadania z itp portalu Azure.

![Diagram zadania i inne opcje](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat krawędzi Iot Azure](../iot-edge/how-iot-edge-works.md)
* [ASA na krawędzi IoT — samouczek](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Wyślij opinię do zespołu za pomocą tej ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
