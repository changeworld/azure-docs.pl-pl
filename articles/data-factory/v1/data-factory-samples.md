---
title: Fabryki danych Azure — przykłady
description: Szczegółowe informacje na temat przykłady, które są dostarczane w usłudze fabryka danych Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b0b8f56266f77724a60253991556b15a923e537f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-data-factory---samples"></a>Fabryki danych Azure — przykłady
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [przykłady środowiska PowerShell w wersji 2 usługi fabryka danych](../samples-powershell.md) i [przykłady w galerii Azure przykłady kodu](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Przykłady z witryny GitHub
[Repozytorium GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) zawiera kilka przykłady, które ułatwiają szybkie zwiększać usłudze fabryka danych Azure (lub) zmodyfikować skrypty i używać jej w własnych aplikacji. Samples\JSON folder zawiera fragmenty kodu JSON dla typowych scenariuszy.

| Sample | Opis |
|:--- |:--- |
| [Wskazówki ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |W tym przykładzie przedstawiono wskazówki na trasie do przetwarzania plików dziennika przy użyciu fabryki danych Azure, aby włączyć danych z plików dziennika w do szczegółowych danych. <br/><br/>W tym przewodniku potoku fabryki danych zbiera dzienniki próbki, procesów i wzbogaca danych z dzienników przy użyciu danych referencyjnych i przekształcenia danych oceny skuteczności kampanii marketingowych, która została ostatnio uruchomiona. |
| [Przykłady JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |W tym przykładzie przedstawiono przykłady JSON dla typowych scenariuszy. |
| [Przykładowe narzędzie do pobierania danych http](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Ten przykładowy pokazy pobierania danych z punktu końcowego HTTP do magazynu obiektów Blob Azure przy użyciu działań niestandardowych .NET. |
| [Krzyżowe próbki Net działanie elementu AppDomain kropka](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |W tym przykładzie umożliwia tworzenie niestandardowego działania .NET, który nie jest ograniczona do wersji zestawu używanych przez uruchamianie ADF (na przykład WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x itp.). |
| [Uruchom skrypt języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Ten przykład obejmuje działania niestandardowego fabryki danych, który może służyć do wywołania RScript.exe. Ten przykład współdziała tylko z własnego klastra usługi HDInsight (nie. na żądanie), który jest już zainstalowany R na nim. |
| [Wywołanie zadań Spark dla klastra usługi HDInsight Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Ten przykład przedstawia sposób użycia działania MapReduce do wywołania programu Spark. Spark program tylko kopiuje dane z jednego kontenera obiektów Blob platformy Azure do innego. |
| [Za pomocą usługi Azure Machine Learning oceniania działania dotyczącego partii usługi analizy w usłudze Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Ten przykład przedstawia sposób użycia elementu AzureMLBatchScoringActivity do wywołania usługi Azure Machine Learning model, który przeprowadza analizę wskaźniki nastrojów klientów twitter, oceniania prognozowania itp. |
| [Analizy przy użyciu działań niestandardowych w usłudze Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Ten przykład przedstawia sposób użycia niestandardowego działania .NET do wywołania usługi Azure Machine Learning model, który przeprowadza analizę wskaźniki nastrojów klientów twitter, oceniania prognozowania itp. |
| [Sparametryzowane potoków dla usługi Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |Przykład zawiera end-to-end kodu C# do wdrożenia potoki N oceniania i ponownego trenowania każdego z parametrem inny region, gdy lista regionów pochodzi z pliku parameters.txt, który znajduje się w tym przykładzie. |
| [Odwołanie do zadania usługi analiza strumienia Azure odświeżania danych](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |W tym przykładzie pokazano, jak za pomocą fabryki danych Azure i usługi Azure Stream Analytics razem można uruchomić zapytania z danych referencyjnych oraz ustawienia odświeżania danych referencyjnych zgodnie z harmonogramem. |
| [Hybrydowe potok wraz z lokalną Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Próbki korzysta z lokalnego klastra usługi Hadoop jako element docelowy obliczeń uruchomionych zadań w fabryce danych tak samo, jak należy dodać inne obiekty docelowe obliczeń jak HDInsight podstawie klastra usługi Hadoop w chmurze. |
| [Narzędzie konwersji JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |To narzędzie umożliwia konwertowanie JSONs z wersji przed 2015-07-01-preview najnowszej lub 2015-07-01-preview (ustawienie domyślne). |
| [Plik wejściowy próbki U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Ten plik jest przykładowy plik używany przez działanie U-SQL. |
| [Usuń plik obiektu blob](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | W tym przykładzie ilustrację pliku C#, który może służyć jako część działań niestandardowych .net ADF pliki zostaną usunięte ze źródła lokalizacji obiektu Blob Azure po skopiowaniu plików.|

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Następujące szablony usługi Azure Resource Manager można znaleźć fabryki danych w witrynie GitHub.

| Szablon | Opis |
| --- | --- |
| [Skopiuj z magazynu obiektów Blob platformy Azure do bazy danych Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Wdrażanie ten szablon tworzy fabryki danych Azure z potok, który kopiuje dane z magazynu obiektów blob platformy Azure określonej bazy danych Azure SQL |
| [Kopiowanie z Salesforce do magazynu obiektów Blob platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Wdrażanie ten szablon tworzy fabryki danych Azure z potok, który kopiuje dane z określonego konta usług Salesforce do magazynu obiektów blob platformy Azure. |
| [Przekształcanie danych za pomocą skryptu Hive w klastrze usługi HDInsight Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Wdrażanie ten szablon tworzy fabryki danych Azure z potokiem przekształcenia danych za pomocą przykładowego skryptu Hive w klastrze usługi Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Przykłady w portalu Azure
Można użyć **przykładowe potoki** kafelka na stronie głównej w fabryce danych, aby wdrożyć przykładowe potoki i ich skojarzonych elementów (zestawy danych i połączone usługi) w z fabryką danych.

1. Tworzenie fabryki danych lub Otwórz istniejącą fabrykę danych. Zobacz [skopiowanie danych z magazynu obiektów Blob do bazy danych SQL przy użyciu fabryki danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) czynności można utworzyć fabryki danych.
2. W **FABRYKI danych** bloku dla fabryki danych, kliknij przycisk **przykładowe potoki** kafelka.

    ![Przykładowe potoki kafelka](./media/data-factory-samples/SamplePipelinesTile.png)
3. W **przykładowe potoki** bloku, kliknij przycisk **próbki** , który chcesz wdrożyć.

    ![Przykład potoki bloku](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji przykładowej. Na przykład z usługi Azure storage konta nazwy i klucza konta, nazwy serwera Azure SQL, bazy danych, identyfikator użytkownika i hasło itp.

    ![Przykładowe bloku](./media/data-factory-samples/SampleBlade.png)
5. Po wykonaniu Określanie ustawień konfiguracji, kliknij przycisk **Utwórz** Utwórz/wdrażania potoki próbki i usług/tabel połączonych używane przez potoków.
6. Wyświetlany jest stan wdrożenia na kafelku próbki kliknięto wcześniej na **przykładowe potoki** bloku.

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Po wyświetleniu **wdrożenie zakończyło się pomyślnie** wiadomości na kafelku, próbki, Zamknij **przykładowe potoki** bloku.  
8. Na **FABRYKI danych** bloku, zostanie wyświetlony połączone usługi, zestawy danych i potoki są dodawane z fabryką danych.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Przykłady programu Visual Studio
### <a name="prerequisites"></a>Wymagania wstępne
Na komputerze muszą być zainstalowane następujące elementy:

* Visual Studio 2013 lub Visual Studio 2015
* Pobierz zestaw Azure SDK dla programu Visual Studio 2013 lub Visual Studio 2015. Przejdź do [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/) i kliknij pozycję **VS 2013** lub **VS 2015** w sekcji **.NET**.
* Pobierz najnowszą wtyczkę usługi Azure Data Factory dla programu Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) lub [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Jeśli używasz programu Visual Studio 2013, możesz także zaktualizować wtyczkę, wykonując następujące czynności: w menu kliknij kolejno opcje **Narzędzia** -> **Rozszerzenia i aktualizacje** -> **Online** -> **Galeria Visual Studio** -> **Narzędzia usługi Fabryka danych Microsoft Azure dla programu Visual Studio** -> **Aktualizuj**.

### <a name="use-data-factory-templates"></a>Za pomocą szablonów fabryki danych
1. Kliknij przycisk **pliku** w menu wskaż **nowy**i kliknij przycisk **projektu**.
2. W **nowy projekt** okna dialogowego wykonaj następujące czynności:

   1. Wybierz **DataFactory** w obszarze **szablony**.
   2. Wybierz **szablony fabryki danych** w okienku po prawej stronie.
   3. Wprowadź **nazwa** dla projektu.
   4. Wybierz **lokalizacji** dla projektu.
   5. Kliknij przycisk **OK**.

      ![Okno dialogowe Nowy projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. W **szablony fabryki danych** oknie dialogowym Wybierz przykładowy szablon z **przypadek użycia szablonów** sekcji, a następnie kliknij polecenie **dalej**. Poniższe kroki umożliwia przeprowadzenie przy użyciu **profilowania klienta** szablonu. Kroki są podobne do innych próbek.

    ![Okno dialogowe Szablony fabryki danych](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. W **konfiguracji fabryki danych** okna dialogowego, kliknij przycisk **dalej** na **podstawy fabryki danych** strony.
5. Na **fabryki danych skonfiguruj** wykonaj następujące czynności:
   1. Wybierz **tworzenie nowych fabryki danych**. Możesz też wybrać **Użyj istniejącą fabrykę danych**.
   2. Wprowadź **nazwa** dla fabryki danych.
   3. Wybierz **subskrypcji platformy Azure** , w której ma fabryki danych ma zostać utworzony.
   4. Wybierz **grupy zasobów** dla fabryki danych.
   5. Wybierz **zachodnie stany USA**, **wschodnie stany USA**, lub **Europa Północna, Europa** dla **region**.
   6. Kliknij przycisk **Dalej**.
6. W **Konfigurowanie magazynów danych** Określ istniejącą **bazy danych Azure SQL** i **konto magazynu Azure** (lub) Utwórz bazę danych i magazynowania i kliknij przycisk Dalej.
7. W **Konfigurowanie obliczeń** , wybierz ustawienia domyślne i kliknij przycisk **dalej**.
8. W **Podsumowanie** strony, przejrzyj wszystkie ustawienia, a następnie kliknij przycisk **dalej**.
9. W **stan wdrożenia** strony, poczekaj na zakończenie wdrożenia, a następnie kliknij przycisk **Zakończ**.
10. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie kliknij polecenie **Publikuj**.
11. Jeśli zostanie wyświetlone okno dialogowe **Logowanie na koncie Microsoft** wprowadź poświadczenia dla konta z subskrypcją Azure i kliknij przycisk **Zaloguj**.
12. Powinno zostać wyświetlone następujące okno dialogowe:

    ![Okno dialogowe publikowania](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stronie **konfigurowania fabryki danych** wykonaj następujące czynności:

    1. Upewnij się, że **Użyj istniejącą fabrykę danych** opcji.
    2. Wybierz **fabryki danych** miał wybierz przy użyciu szablonu.
    3. Kliknij przycisk **Dalej**, aby przejść na stronę **Publikowanie elementów**. (Naciśnij przycisk **TAB**, aby wyjść z pola nazwy, jeśli przycisk **Dalej** jest wyłączony).
14. Na stronie **Publikowanie elementów** upewnij się, że wszystkie jednostki usługi Fabryka danych zostały wybrane, i kliknij przycisk **Dalej**, aby przejść na stronę **Podsumowanie**.     
15. Przejrzyj podsumowanie i kliknij przycisk **Dalej**, aby rozpocząć proces wdrożenia oraz wyświetlić stronę **Stan wdrożenia**.
16. Na stronie **Stan wdrożenia** powinien zostać wyświetlony stan procesu wdrożenia. Po zakończeniu wdrożenia kliknij przycisk Zakończ.

Zobacz [kompilacji pierwszy fabryki danych (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) szczegółowe informacje na temat tworzenia jednostek fabryki danych za pomocą programu Visual Studio i publikowania ich na platformie Azure.          
