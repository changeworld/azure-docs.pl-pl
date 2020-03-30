---
title: Fabryka danych platformy Azure — przykłady
description: Zawiera szczegółowe informacje o przykładach dostarczanych z usługą Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63db1810cbdd2133bc0577530325351997b31f30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139258"
---
# <a name="azure-data-factory---samples"></a>Fabryka danych platformy Azure — przykłady
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przykłady programu PowerShell w fabryce danych](../samples-powershell.md) i [przykłady kodu w galerii przykładów kodu azure.](https://azure.microsoft.com/resources/samples/?service=data-factory)


## <a name="samples-on-github"></a>Przykłady w serwisie GitHub
[Repozytorium GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) zawiera kilka przykładów, które pomagają szybko rozwijać się z usługą Azure Data Factory (lub) modyfikować skrypty i używać go we własnej aplikacji. Folder Samples\JSON zawiera fragmenty kodu JSON dla typowych scenariuszy.

| Sample | Opis |
|:--- |:--- |
| [Przewodnik po podajniku ADF](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |W tym przykładzie przedstawiono kompleksowy przewodnik do przetwarzania plików dziennika przy użyciu usługi Azure Data Factory, aby włączyć dane z plików dziennika do szczegółowych informacji. <br/><br/>W tym instruktażu potok fabryki danych zbiera przykładowe dzienniki, procesy i wzbogaca dane z dzienników danymi referencyjnymi i przekształca dane w celu oceny skuteczności niedawno uruchomionej kampanii marketingowej. |
| [Próbki JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Ten przykład zawiera przykłady JSON dla typowych scenariuszy. |
| [Przykład pobierania danych http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |W tym przykładzie pokazano pobieranie danych z punktu końcowego HTTP do usługi Azure Blob Storage przy użyciu niestandardowej aktywności platformy .NET. |
| [Przykład aktywności netto między aplikacjami Domeną Dota](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |W tym przykładzie można tworzyć niestandardowe działanie platformy .NET, które nie jest ograniczone do wersji zestawu używanych przez program uruchamiający podajnikiem ADF (na przykład WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x itp.). |
| [Uruchom skrypt R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Ten przykład zawiera działanie niestandardowe usługi Data Factory, które może służyć do wywoływania pliku RScript.exe. Ten przykład działa tylko z własnym (nie na żądanie) klastrem HDInsight, który ma już zainstalowany r. |
| [Wywoływanie zadań Spark w klastrze HDInsight Hadoop](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |W tym przykładzie pokazano, jak używać działania MapReduce do wywoływania programu Spark. Program iskrzący tylko kopiuje dane z jednego kontenera obiektów blob platformy Azure do innego. |
| [Analiza Twittera przy użyciu działania oceniania wsadowego usługi Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |W tym przykładzie pokazano, jak używać usługi AzureMLBatchScoringActivity do wywoływania modelu usługi Azure Machine Learning, który wykonuje analizę tonacji twitter, ocenianie, przewidywanie itp. |
| [Analiza Twittera przy użyciu aktywności niestandardowej](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |W tym przykładzie pokazano, jak używać niestandardowego działania platformy .NET do wywoływania modelu usługi Azure Machine Learning, który wykonuje analizę tonacji języka Twitter, ocenianie, przewidywanie itp. |
| [Potoki sparametryzowane dla usługi Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Przykład zawiera end-to-end kod języka C# do wdrażania potoków N do oceniania i ponownego trenowania każdego z różnych parametrów regionu, gdzie lista regionów pochodzi z pliku parameters.txt, który jest dołączony do tego przykładu. |
| [Odświeżanie danych referencyjnych dla zadań usługi Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |W tym przykładzie pokazano, jak używać usługi Azure Data Factory i usługi Azure Stream Analytics razem, aby uruchomić kwerendy z danymi referencyjnymi i skonfigurować odświeżanie dla danych referencyjnych zgodnie z harmonogramem. |
| [Rurociąg hybrydowy z lokalnymi Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |W przykładzie użyto lokalnego klastra Hadoop jako celu obliczeniowego do uruchamiania zadań w fabryce danych, podobnie jak można dodać inne obiekty docelowe obliczeń, takie jak klaster Hadoop oparty na hdinsight w chmurze. |
| [Narzędzie konwersji JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |To narzędzie umożliwia konwertowanie sieci JSOns z wersji sprzed 2015-07-01-preview do najnowszego lub 2015-07-01-preview (domyślnie). |
| [Przykładowy plik wejściowy U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Ten plik jest przykładowym plikiem używanym przez działanie U-SQL. |
| [Usuwanie pliku obiektu blob](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | W tym przykładzie jest prezentowanie pliku języka C#, który może być używany jako część niestandardowej aktywności platformy ADF .net do usuwania plików ze źródłowej lokalizacji obiektów blob platformy Azure po skopiowaniu plików.|

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
W usłudze GitHub można znaleźć następujące szablony usługi Azure Resource Manager dla usługi Data Factory.

| Szablon | Opis |
| --- | --- |
| [Kopiowanie z usługi Azure Blob Storage do bazy danych SQL platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Wdrożenie tego szablonu tworzy fabrykę danych platformy Azure z potokiem, który kopiuje dane z określonego magazynu obiektów blob platformy Azure do bazy danych SQL platformy Azure |
| [Kopiowanie z salesforce do usługi Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Wdrożenie tego szablonu tworzy fabrykę danych platformy Azure z potokiem, który kopiuje dane z określonego konta Salesforce do magazynu obiektów blob platformy Azure. |
| [Przekształcanie danych przez uruchamianie skryptu hive w klastrze usługi Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Wdrożenie tego szablonu tworzy fabrykę danych platformy Azure z potokiem, który przekształca dane, uruchamiając przykładowy skrypt hive w klastrze usługi Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Przykłady w witrynie Azure portal
Kafelek **Przykładowe potoki** można użyć na stronie głównej fabryki danych, aby wdrożyć przykładowe potoki i skojarzone z nimi jednostki (zestawy danych i połączone usługi) w fabryce danych.

1. Utwórz fabrykę danych lub otwórz istniejącą fabrykę danych. Zobacz [Kopiowanie danych z magazynu obiektów blob do bazy danych SQL przy użyciu fabryki danych,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby wykonać kroki tworzenia fabryki danych.
2. W bloku **DATA FACTORY** dla fabryki danych kliknij kafelek **Przykładowe potoki.**

    ![Przykładowy kafelek potoków](./media/data-factory-samples/SamplePipelinesTile.png)
3. W **przykładowym potoku** bloku, kliknij **przykład,** który chcesz wdrożyć.

    ![Przykładowe ostrze rurociągów](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji dla próbki. Na przykład nazwa konta i klucz konta usługi Azure storage, nazwa serwera SQL platformy Azure, baza danych, identyfikator użytkownika i hasło itp.

    ![Ostrze próbki](./media/data-factory-samples/SampleBlade.png)
5. Po zakończeniu określania ustawień konfiguracji kliknij przycisk **Utwórz,** aby utworzyć/wdrożyć przykładowe potoki i połączone usługi/tabele używane przez potoki.
6. Widać stan wdrożenia na przykładowym kafelku klikniętym wcześniej w bloku **Przykładowe potoki.**

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Po **wyświetleniu komunikatu Wdrożenie powiodło się** na kafelku dla próbki, zamknij **przykładowego potoku** bloku.  
8. W **bloku DATA FACTORY** zobaczysz, że połączone usługi, zestawy danych i potoki są dodawane do fabryki danych.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Przykłady w programie Visual Studio
### <a name="prerequisites"></a>Wymagania wstępne
Na komputerze muszą być zainstalowane następujące elementy:

* Visual Studio 2013 lub Visual Studio 2015
* Pobierz zestaw Azure SDK dla programu Visual Studio 2013 lub Visual Studio 2015. Przejdź do [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/) i kliknij pozycję **VS 2013** lub **VS 2015** w sekcji **.NET**.
* Pobierz najnowszą wtyczkę usługi Azure Data Factory dla programu Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) lub [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Jeśli używasz programu Visual Studio 2013, możesz również zaktualizować wtyczkę, wykonując następujące kroki: W menu kliknij polecenie Rozszerzenia **narzędzi** -> **i aktualizacje** -> **Online** -> **Visual Studio Microsoft** -> **Azure Data Factory Tools for Visual Studio** -> **Update**.

### <a name="use-data-factory-templates"></a>Korzystanie z szablonów fabryki danych
1. Kliknij **polecenie Plik** w menu, wskaż polecenie **Nowy**i kliknij pozycję **Projekt**.
2. W oknie dialogowym **Nowy projekt** wykonaj następujące kroki:

   1. Wybierz **DataFactory** w obszarze **Szablony**.
   2. Wybierz **pozycję Szablony fabryki danych** w prawym okienku.
   3. Wprowadź **nazwę** dla projektu.
   4. Wybierz **lokalizację** dla projektu.
   5. Kliknij przycisk **OK**.

      ![Okno dialogowe Nowy projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. W oknie dialogowym **Szablony fabryczne danych** wybierz przykładowy szablon z sekcji **Użyj-Case Templates** i kliknij przycisk **Dalej**. Poniższe kroki przebiegają przy użyciu szablonu **Profilowania klienta.** Kroki są podobne dla innych próbek.

    ![Okno dialogowe Szablony fabryki danych](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. W oknie dialogowym **Konfiguracja fabryki danych** kliknij przycisk **Dalej** na stronie Podstawowe ustawienia **fabryki danych.**
5. Na stronie **Konfigurowanie fabryki danych** wykonaj następujące czynności:
   1. Wybierz **pozycję Utwórz nową fabrykę danych**. Można również wybrać **opcję Użyj istniejącej fabryki danych**.
   2. Wprowadź **nazwę** fabryki danych.
   3. Wybierz **subskrypcję platformy Azure,** w której ma zostać utworzona fabryka danych.
   4. Wybierz **grupę zasobów** dla fabryki danych.
   5. Wybierz dla **regionu**zachodnie stany **USA,** **wschodnie**stany USA lub **Europę Północną.**
   6. Kliknij przycisk **alej**.
6. Na stronie **Konfigurowanie przechowywania danych** określ istniejącą **bazę danych SQL platformy Azure** i konto magazynu platformy **Azure** (lub) utwórz bazę danych/magazyn, a następnie kliknij przycisk Dalej.
7. Na stronie **Konfigurowanie obliczeń** wybierz pozycję Domyślne i kliknij przycisk **Dalej**.
8. Na stronie **Podsumowanie** przejrzyj wszystkie ustawienia i kliknij przycisk **Dalej**.
9. Na stronie **Stan wdrożenia** poczekaj na zakończenie wdrożenia i kliknij przycisk **Zakończ**.
10. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie kliknij polecenie **Publikuj**.
11. Jeśli zostanie wyświetlone okno dialogowe **Logowanie na koncie Microsoft** wprowadź poświadczenia dla konta z subskrypcją Azure i kliknij przycisk **Zaloguj**.
12. Powinno zostać wyświetlone następujące okno dialogowe:

    ![Okno dialogowe publikowania](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stronie **Konfigurowanie fabryki danych** wykonaj następujące czynności:

    1. Potwierdź, że użyj istniejącej opcji **fabryki danych.**
    2. Wybierz **fabrykę danych,** którą wybrałeś podczas korzystania z szablonu.
    3. Kliknij przycisk **Dalej**, aby przejść na stronę **Publikowanie elementów**. (Naciśnij przycisk **TAB**, aby wyjść z pola nazwy, jeśli przycisk **Dalej** jest wyłączony).
14. Na stronie **Publikowanie elementów** upewnij się, że wszystkie jednostki usługi Fabryka danych zostały wybrane, i kliknij przycisk **Dalej**, aby przejść na stronę **Podsumowanie**.     
15. Przejrzyj podsumowanie i kliknij przycisk **Dalej**, aby rozpocząć proces wdrożenia oraz wyświetlić stronę **Stan wdrożenia**.
16. Na stronie **Stan wdrożenia** powinien zostać wyświetlony stan procesu wdrożenia. Po zakończeniu wdrożenia kliknij przycisk Zakończ.

Zobacz [Tworzenie pierwszej fabryki danych (Visual Studio),](data-factory-build-your-first-pipeline-using-vs.md) aby uzyskać szczegółowe informacje na temat używania programu Visual Studio do tworzenia jednostek usługi Data Factory i publikowania ich na platformie Azure.          
