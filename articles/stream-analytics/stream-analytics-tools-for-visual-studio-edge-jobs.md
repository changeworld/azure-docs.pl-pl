---
title: Zadania usługi Azure Stream Analytics Edge w programie Visual Studio
description: W tym artykule opisano sposób tworzenia, debugowania i tworzenia zadań usługi Stream Analytics w usłudze IoT Edge przy użyciu narzędzi usługi Stream Analytics dla programu Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354568"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Tworzenie zadań usługi Stream Analytics Edge przy użyciu narzędzi programu Visual Studio

W tym samouczku dowiesz się, jak korzystać z narzędzi usługi Stream Analytics dla programu Visual Studio. Dowiesz się, jak tworzyć, debugować i tworzyć zadania usługi Stream Analytics Edge. Po utworzeniu i przetestowaniu zadania można przejść do witryny Azure portal, aby wdrożyć je na urządzeniach. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące wymagania wstępne:

* Instalowanie [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [visual studio 2015](https://www.visualstudio.com/vs/older-downloads/)lub [programu Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Obsługiwane są wersje Enterprise (Ultimate/Premium), Professional i Community. Wersja ekspresowa nie jest obsługiwana.  

* Postępuj zgodnie z [instrukcjami instalacji,](stream-analytics-tools-for-visual-studio-edge-jobs.md) aby zainstalować narzędzia usługi Stream Analytics dla programu Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Tworzenie projektu usługi Stream Analytics Edge 

W programie Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu**. Przejdź do listy **Szablony** po lewej stronie > rozwiń aplikację **Azure Stream Analytics** > **Analytics Edge** > **Azure Stream Analytics Edge**. Podaj nazwę, lokalizację i nazwę rozwiązania dla projektu i wybierz **przycisk OK**.

![Nowy projekt usługi Stream Analytics Edge w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Po utworzeniu projektu przejdź do **Eksploratora rozwiązań,** aby wyświetlić hierarchię folderów.

![Widok eksploratora rozwiązań zadania usługi Stream Analytics Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Wybierz właściwą subskrypcję

1. Z menu **widoku** programu Visual Studio Wybierz pozycję **Eksplorator serwera**.  

2. Kliknij prawym przyciskiem myszy **> wybierz** **opcję Połącz z > subskrypcji platformy Microsoft Azure,** a następnie zaloguj się za pomocą konta platformy Azure.

## <a name="define-inputs"></a>Definiowanie danych wejściowych

1. W **Eksploratorze rozwiązań**rozwiń węzeł **Wejścia,** w tym powinno zostać wyświetlonego dane wejściowe o nazwie **EdgeInput.json**. Kliknij dwukrotnie, aby wyświetlić jego ustawienia.  

2. Ustaw typ źródła na **Strumień danych**. Następnie ustaw źródło na **Centrum krawędzi,** Format serializacji zdarzeń na **Json**i Kodowanie do **UTF8**. Opcjonalnie można zmienić nazwę **aliasu wejściowego**, zostawmy go tak, jak w tym przykładzie. W przypadku zmiany nazwy aliasu wejściowego należy użyć nazwy określonej podczas definiowania kwerendy. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.  
   ![Konfiguracja wprowadzania zadań usługi Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definiowanie danych wyjściowych

1. W **Eksploratorze rozwiązań**rozwiń węzeł Wyjścia powinien zostać wyświetlony dane **wyjściowe** o nazwie **EdgeOutput.json**. Kliknij dwukrotnie, aby wyświetlić jego ustawienia.  

2. Upewnij się, że ustaw zlew, aby wybrać **Koncentrator krawędzi,** ustaw format serializacji zdarzeń na **Json**, ustaw kodowanie na **UTF8**i ustaw polecenie Format **Array**. Opcjonalnie można zmienić nazwę **aliasu wyjściowego**, zostawmy go tak, jak w tym przykładzie. W przypadku zmiany nazwy aliasu wyjściowego należy użyć nazwy określonej podczas definiowania kwerendy. Wybierz pozycję **Zapisz**, aby zapisać ustawienia. 
   ![Konfiguracja wyjściowa zadania usługi Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

Zadania usługi Stream Analytics wdrożone w środowiskach usługi Stream Analytics w środowisku usługi IoT Edge obsługują większość [odwołania do języka zapytań usługi Stream Analytics.](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396) Jednak następujące operacje nie są jeszcze obsługiwane dla zadań usługi Stream Analytics Edge: 


|**Kategoria**  | **Polecenie**  |
|---------|---------|
|Inni operatorzy | <ul><li>PARTYCJA PRZEZ</li><li>SYGNATURA CZASOWA O PONAD</li><li>JavaScript UDF</li><li>Agregaty zdefiniowane przez użytkownika (UDA)</li><li>GetMetadataPropertyValue</li><li>Używanie więcej niż 14 agregatów w jednym kroku</li></ul>   |

Podczas tworzenia zadania usługi Stream Analytics Edge w portalu kompilator automatycznie ostrzega, jeśli nie używasz obsługiwanego operatora.

Z programu Visual Studio zdefiniuj następującą kwerendę transformacyjną w edytorze kwerend (**plik script.asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testowanie zadania lokalnie

Aby przetestować kwerendę lokalnie, należy przekazać przykładowe dane. Przykładowe dane można pobrać, pobierając dane rejestracyjne z [repozytorium GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) i zapisać je na komputerze lokalnym. 

1. Aby przesłać przykładowe dane, kliknij prawym przyciskiem myszy plik **EdgeInput.json** i wybierz pozycję **Dodaj dane lokalne**  

2. W wyskakującym oknie > **Przeglądaj** przykładowe dane ze ścieżki lokalnej > wybierz pozycję **Zapisz**.
   ![Lokalna konfiguracja wprowadzania w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Plik o nazwie **local_EdgeInput.json** jest dodawany automatycznie do folderu danych wejściowych.  
4. Można go uruchomić lokalnie lub przesłać do platformy Azure. Aby przetestować kwerendę, wybierz pozycję **Uruchom lokalnie**.  
   ![Opcje uruchamiania zadania usługi Stream Analytics w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. Okno wiersza polecenia pokazuje stan zadania. Po pomyślnym uruchomieniu zadania tworzy folder o wyglądzie "2018-02-23-11-31-42" w ścieżce folderu projektu "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Przejdź do ścieżki folderu, aby wyświetlić wyniki w folderze lokalnym:

   Można również zalogować się do witryny Azure portal i sprawdzić, czy zadanie jest tworzone. 

   ![Folder wyników zadania usługi Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Prześlij zadanie do platformy Azure

1. Przed przesłaniem zadania do platformy Azure należy połączyć się z subskrypcją platformy Azure. Otwórz **Eksploratora serwera** > kliknij prawym przyciskiem myszy**subskrypcję** usługi **Azure** > Connect do platformy Microsoft Azure > zalogować się do subskrypcji platformy Azure.  

2. Aby przesłać zadanie do platformy Azure, przejdź do edytora zapytań > wybierz pozycję **Prześlij na platformę Azure**.  

3. Zostanie otwarte okno podręczne. Wybierz, aby zaktualizować istniejące zadanie usługi Stream Analytics Edge lub utworzyć nowe. Po zaktualizowaniu istniejącego zadania zastąpi ono całą konfigurację zadania, w tym scenariuszu opublikujesz nowe zadanie. Wybierz **pozycję Utwórz nowe zadanie analizy usługi Azure Stream,** > wprowadź nazwę zadania na przykład **MyASAEdgeJob** > wybierz wymaganą **subskrypcję,** **grupę zasobów**i **lokalizację** > wybierz pozycję **Prześlij**.

   ![Przesyłanie zadania usługi Stream Analytics do platformy Azure z programu Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Teraz utworzono zadanie Stream Analytics Edge. Możesz zapoznać się z [samouczkiem Uruchom zadania w umyku IoT Edge,](stream-analytics-edge.md) aby dowiedzieć się, jak wdrożyć je na swoich urządzeniach. 

## <a name="manage-the-job"></a>Zarządzanie zadaniem 

Stan zadania i diagram zadań można wyświetlić w Eksploratorze serwera. Z **usługi Stream Analytics** w **Eksploratorze serwera**rozwiń subskrypcję i grupę zasobów, w której wdrożono zadanie usługi Stream Analytics Edge. Można wyświetlić myasaedgejob ze **stanem Utworzone**. Rozwiń węzeł zadania i kliknij go dwukrotnie, aby otworzyć widok zadania.

![Opcje zarządzania zadaniami eksploratora serwera](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Okno widoku zadania zapewnia operacje, takie jak odświeżanie zadania, usuwanie zadania i otwieranie zadania z witryny Azure portal.

![Diagram zadań i inne opcje w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Następne kroki

* [Więcej informacji o usłudze Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Asa w UIO Edge samouczek](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Wyślij opinię do zespołu korzystającego z tej ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
