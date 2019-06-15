---
title: Usługa Azure Data Factory — przykłady
description: Szczegółowe informacje dotyczące przykładów, które są dostarczane za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 03127dc777588f669ef07af52c8f73d986bfe0ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61260038"
---
# <a name="azure-data-factory---samples"></a>Usługa Azure Data Factory — przykłady
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [przykłady programu PowerShell w usłudze Data Factory](../samples-powershell.md) i [przykładów w galerii przykładów kodu usługi Azure kodu](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Przykłady w serwisie GitHub
[Repozytorium GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) zawiera kilka przykładów, które ułatwiają szybkie zwiększania obciążenia za pomocą usługi Azure Data Factory (lub) zmodyfikowanie skryptów i używać go w własnych aplikacji. Samples\JSON folder zawiera fragmenty JSON dla typowych scenariuszy.

| Sample | Opis |
|:--- |:--- |
| [Przewodnik usługi ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |W tym przykładzie zawiera przewodnik end-to-end do przetwarzania plików dziennika przy użyciu usługi Azure Data Factory, aby przekształcać dane z plików dziennika w celu szczegółowych informacji. <br/><br/>W tym przewodniku potoku usługi fabryka danych umożliwia zbieranie informacji o przykładowe Dzienniki procesów uzupełnia dane z dzienników z danymi referencyjnymi i przekształca dane oceny skuteczności kampanii marketingowej, która została ostatnio uruchomiona. |
| [Przykłady kodu JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |W tym przykładzie zawiera przykłady JSON dla typowych scenariuszy. |
| [Przykładowe narzędzie do pobierania danych http](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Ten przykład prezentuje pobierania danych z punktu końcowego HTTP do usługi Azure Blob Storage przy użyciu niestandardowe działanie platformy .NET. |
| [Przykładowe działanie netto kropka AppDomain Cross](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |W tym przykładzie można tworzyć niestandardowe działanie platformy .NET, która nie jest ograniczony do wersji zestawu posługują się uruchamiania usługi ADF (na przykład 4.3.0 WindowsAzure.Storage, Newtonsoft.Json v6.0.x itp.). |
| [Uruchom skrypt języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |W tym przykładzie zawiera niestandardowe działanie usługi Data Factory, który może służyć do wywołania Rscript.exe.{0}nie. Ten przykład działa tylko w przypadku własny klaster HDInsight (nie na żądanie), który jest już zainstalowana R na nim. |
| [Wywoływanie zadań platformy Spark w klastrze usługi HDInsight Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Ten przykład pokazuje, jak użyć działania MapReduce, aby wywołać program platformy Spark. Program platformy spark tylko kopiuje dane z jednego kontenera obiektów Blob platformy Azure do innego. |
| [Analiza przy użyciu Machine Learning wsadowego oceniania działanie usługi Azure w usłudze Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |W tym przykładzie pokazano, jak za pomocą elementu AzureMLBatchScoringActivity wywołania modelu usługi Azure Machine Learning, które przeprowadza analizę tonacji twitter, oceniania i prognozowania itp. |
| [Analiza przy użyciu działań niestandardowych w usłudze Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Ten przykład pokazuje, jak niestandardowe działanie platformy .NET umożliwia wywoływanie modelu usługi Azure Machine Learning, które przeprowadza analizę tonacji twitter, oceniania i prognozowania itp. |
| [Sparametryzowany potoki pod kątem usługi Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |Przykład zawiera end-to-end C# kod, aby wdrożyć potoki N oceniania i ponownego trenowania każdego z parametrem inny region, skąd pochodzą z listy regionów na parameters.txt pliku, który jest dołączony do tego przykładu. |
| [Odwołanie do odświeżania danych dla zadań usługi Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |W tym przykładzie pokazano, jak za pomocą usługi Azure Data Factory i Azure Stream Analytics razem uruchamiać je z danymi referencyjnymi i skonfigurować odświeżanie danych referencyjnych, zgodnie z harmonogramem. |
| [Hybrydowe potoku za pomocą lokalnej usługi Hadoop z Hortonworks](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |W przykładzie użyto lokalnego klastra usługi Hadoop jako cel obliczenia dotyczące uruchamiania zadań w usłudze Data Factory, tak samo, jak należy dodać inne obliczeniowych elementów docelowych, takich jak HDInsight na podstawie klaster Hadoop w chmurze. |
| [Narzędzie do konwersji JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |To narzędzie umożliwia konwertowanie JSON Smb1sessionsetup w wersji wcześniejszej niż 2015-07-01-preview najnowsze lub 2015-07-01-preview (ustawienie domyślne). |
| [Plik wejściowy przykładowy języka U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Ten plik jest przykładowy plik używany przez działanie U-SQL. |
| [Usuń plik obiektów blob](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | W tym przykładzie przedstawiono C# pliku, który może służyć jako część usługi ADF niestandardowe działanie platformy .net do usuwania plików ze źródła lokalizacji obiektu Blob platformy Azure, skopiowania plików.|

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Następujące szablony usługi Azure Resource Manager można znaleźć w usłudze Data Factory w witrynie GitHub.

| Szablon | Opis |
| --- | --- |
| [Kopiowanie z usługi Azure Blob Storage do usługi Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Wdrażanie ten szablon tworzy usługi Azure data factory z potokiem, który kopiuje dane z określonej usługi Azure blob storage do bazy danych Azure SQL |
| [Skopiuj z usług Salesforce do usługi Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Ten szablon wdrażania tworzy usługi Azure data factory z potokiem, który kopiuje dane z określonego konta usługi Salesforce do usługi Azure blob storage. |
| [Przekształcanie danych, uruchamiając skrypt Hive w klastrze usługi Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Ten szablon wdrażania tworzy usługi Azure data factory z potokiem, który przekształca dane, uruchamiając przykładowy skrypt programu Hive w klastrze usługi Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Przykłady w witrynie Azure portal
Możesz użyć **przykładowe potoki** kafelka na stronie głównej fabryki danych, aby wdrożyć przykładowe potoki i ich skojarzone jednostki (zestawy danych i połączone usługi) w z fabryką danych.

1. Utwórz fabrykę danych lub Otwórz istniejącą fabrykę danych. Zobacz [kopiowanie danych z magazynu obiektów Blob do usługi SQL Database przy użyciu usługi Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kroki utworzyć fabrykę danych.
2. W **usługi DATA FACTORY** bloku fabryka danych kliknij **przykładowe potoki** kafelka.

    ![Przykładowe potoki kafelka](./media/data-factory-samples/SamplePipelinesTile.png)
3. W **przykładowe potoki** bloku kliknij **przykładowe** , którą chcesz wdrożyć.

    ![Przykładowe potoki bloku](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji dla przykładu. Na przykład usługi Azure konta nazwy i klucza konta magazynu, nazwy serwera Azure SQL, bazy danych, identyfikator użytkownika i hasło itp.

    ![Przykładowy blok](./media/data-factory-samples/SampleBlade.png)
5. Po wykonaniu Określanie ustawień konfiguracji, kliknij przycisk **Utwórz** do tworzenia/wdrożyć przykładowe potoki i usług/tabel połączonych posługują się potoków.
6. Zostanie wyświetlony stan wdrożenia na kafelku przykładowe wcześniej kliknięta **przykładowe potoki** bloku.

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Po wyświetleniu **wdrażanie zakończyło się pomyślnie** wiadomości na kafelku dla przykładu, Zamknij **przykładowe potoki** bloku.  
8. Na **usługi DATA FACTORY** wyświetlony blok usługi połączone, zestawy danych i potoki są dodawane do usługi data factory.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Przykłady w programie Visual Studio
### <a name="prerequisites"></a>Wymagania wstępne
Na komputerze muszą być zainstalowane następujące elementy:

* Visual Studio 2013 lub Visual Studio 2015
* Pobierz zestaw Azure SDK dla programu Visual Studio 2013 lub Visual Studio 2015. Przejdź do [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/) i kliknij pozycję **VS 2013** lub **VS 2015** w sekcji **.NET**.
* Pobierz najnowszą wtyczkę usługi Azure Data Factory dla programu Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) lub [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Jeśli używasz programu Visual Studio 2013, możesz także zaktualizować wtyczkę, wykonując następujące czynności: W menu kliknij kolejno opcje **Narzędzia** -> **Rozszerzenia i aktualizacje** -> **Online** -> **Galeria Visual Studio** -> **Narzędzia usługi Fabryka danych Microsoft Azure dla programu Visual Studio** -> **Aktualizuj**.

### <a name="use-data-factory-templates"></a>Korzystanie z szablonów fabryki danych
1. Kliknij przycisk **pliku** menu, wskaż polecenie **New**i kliknij przycisk **projektu**.
2. W oknie dialogowym **Nowy projekt** wykonaj następujące kroki:

   1. Wybierz **DataFactory** w obszarze **szablony**.
   2. Wybierz **szablony fabryki danych** w okienku po prawej stronie.
   3. Wprowadź **nazwę** dla projektu.
   4. Wybierz **lokalizacji** dla projektu.
   5. Kliknij przycisk **OK**.

      ![Okno dialogowe Nowy projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. W **szablony fabryki danych** okna dialogowego Wybierz przykładowy szablon z **przypadek użycia szablonów** sekcji, a następnie kliknij przycisk **dalej**. W poniższych krokach objaśniono możesz za pośrednictwem przy użyciu **profilowania klientów** szablonu. Kroki są podobne do innych próbek.

    ![Okno dialogowe Szablony fabryki danych](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. W **konfiguracją usługi Data Factory** okno dialogowe, kliknij przycisk **dalej** na **podstawy fabryki danych** strony.
5. Na **Konfigurowanie fabryki danych** wykonaj następujące czynności:
   1. Wybierz **Tworzenie nowej fabryki danych**. Możesz również wybrać **Użyj istniejącej fabryki danych**.
   2. Wprowadź **nazwa** fabryki danych.
   3. Wybierz **subskrypcji platformy Azure** , w której chcesz utworzyć fabrykę danych.
   4. Wybierz **grupy zasobów** fabryki danych.
   5. Wybierz **zachodnie stany USA**, **wschodnie stany USA**, lub **Europa Północna** dla **region**.
   6. Kliknij przycisk **Dalej**.
6. W **Konfigurowanie magazynów danych** Określ istniejącą, **bazy danych Azure SQL** i **konta usługi Azure storage** (lub) Utwórz bazy danych/magazyn i kliknij przycisk Dalej.
7. W **Konfigurowanie obliczeń** strony, wybierz ustawienia domyślne, a następnie kliknij przycisk **dalej**.
8. W **Podsumowanie** strony, przejrzyj wszystkie ustawienia, a następnie kliknij przycisk **dalej**.
9. W **stan wdrożenia** strony, poczekaj na zakończenie wdrożenia, a następnie kliknij przycisk **Zakończ**.
10. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie kliknij polecenie **Publikuj**.
11. Jeśli zostanie wyświetlone okno dialogowe **Logowanie na koncie Microsoft** wprowadź poświadczenia dla konta z subskrypcją Azure i kliknij przycisk **Zaloguj**.
12. Powinno zostać wyświetlone następujące okno dialogowe:

    ![Okno dialogowe publikowania](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stronie **konfigurowania fabryki danych** wykonaj następujące czynności:

    1. Upewnij się, że **Użyj istniejącej fabryki danych** opcji.
    2. Wybierz **usługi data factory** wcześniejszego wybierz przy użyciu szablonu.
    3. Kliknij przycisk **Dalej**, aby przejść na stronę **Publikowanie elementów**. (Naciśnij przycisk **TAB**, aby wyjść z pola nazwy, jeśli przycisk **Dalej** jest wyłączony).
14. Na stronie **Publikowanie elementów** upewnij się, że wszystkie jednostki usługi Fabryka danych zostały wybrane, i kliknij przycisk **Dalej**, aby przejść na stronę **Podsumowanie**.     
15. Przejrzyj podsumowanie i kliknij przycisk **Dalej**, aby rozpocząć proces wdrożenia oraz wyświetlić stronę **Stan wdrożenia**.
16. Na stronie **Stan wdrożenia** powinien zostać wyświetlony stan procesu wdrożenia. Po zakończeniu wdrożenia kliknij przycisk Zakończ.

Zobacz [tworzenie pierwszej fabryki danych (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) szczegółowe informacje na temat tworzenia jednostek usługi Data Factory przy użyciu programu Visual Studio i publikując je w usłudze Azure.          
