---
title: Importowanie danych treningowych
titleSuffix: ML Studio (classic) - Azure
description: Jak zaimportować dane do usługi Azure Machine Learning Studio (klasyczne) z różnych źródeł danych. Dowiedz się, jakie typy danych i formaty danych są obsługiwane.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: cee49124a7547399889e425008a8580b9b25945a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217981"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio (klasyczne) z różnych źródeł danych

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Aby użyć własnych danych w usłudze Machine Learning Studio (klasyczny) do opracowania i przeszkolenia rozwiązania do analizy predykcyjnej, można użyć danych z: 

* **Plik lokalny** — ładowanie danych lokalnych z wyprzedzeniem z dysku twardego w celu utworzenia modułu zestawu danych w obszarze roboczym
* **Źródła danych online** — użyj modułu [Importuj dane,][import-data] aby uzyskać dostęp do danych z jednego z kilku źródeł online podczas eksperymentu
* **Eksperyment Machine Learning Studio (klasyczny)** — użyj danych zapisanych jako zestaw danych w uściślii Machine Learning Studio (klasyczny)
* [**Lokalna baza danych programu SQL Server**](use-data-from-an-on-premises-sql-server.md) — używanie danych z lokalnej bazy danych programu SQL Server bez konieczności ręcznego kopiowania danych

> [!NOTE]
> Istnieje wiele przykładowych zestawów danych dostępnych w uściślii machine learning studio (klasyczny), których można użyć do danych szkoleniowych. Aby uzyskać informacje na ten temat, zobacz [Użyj przykładowych zestawów danych w usłudze Azure Machine Learning Studio (klasycznym).](use-sample-datasets.md)

## <a name="prepare-data"></a>Przygotowywanie danych

Machine Learning Studio (klasyczny) jest przeznaczony do pracy z prostokątnymi lub tabelarycznymi danymi, takimi jak dane tekstowe, które są rozdzielone lub strukturalne dane z bazy danych, chociaż w niektórych okolicznościach mogą być używane dane niesektularne.

Najlepiej, jeśli dane są stosunkowo czyste przed zaimportowania go do Studio (classic). Na przykład należy zająć się problemami, takimi jak niecytowane ciągi.

Istnieją jednak moduły dostępne w Studio (klasyczne), które umożliwiają pewne manipulowanie danymi w eksperymencie po zaimportowaniu danych. W zależności od algorytmów uczenia maszynowego, które będą używane, może być konieczne podjęcie decyzji, jak będziesz obsługiwać problemy strukturalne danych, takie jak brakujące wartości i dane rozrzedzone, a istnieją moduły, które mogą w tym pomóc. Poszukaj w sekcji **Transformacja danych** palety modułów dla modułów, które wykonują te funkcje.

W dowolnym momencie eksperymentu można wyświetlić lub pobrać dane, które są produkowane przez moduł, klikając port wyjściowy. W zależności od modułu mogą być dostępne różne opcje pobierania lub można wizualizować dane w przeglądarce internetowej w Studio (klasyczny).

## <a name="supported-data-formats-and-data-types"></a>Obsługiwane formaty danych i typy danych

Do eksperymentu można zaimportować wiele typów danych, w zależności od mechanizmu używanego do importowania danych i skąd pochodzą:

* Zwykły tekst (txt)
* Wartości oddzielone przecinkami (CSV) z nagłówkiem (csv) lub bez (.nh.csv)
* Wartości oddzielone tabulatorami (TSV) z nagłówkiem (.tsv) lub bez (.nh.tsv)
* Plik programu Excel
* Tabela platformy Azure
* Tabela gałęzi
* Tabela bazy danych SQL
* Wartości OData
* Dane SVMLight (.svmlight) (informacje o formacie można znaleźć w [definicji SVMLight)](http://svmlight.joachims.org/)
* Dane formatu arff (arff) (patrz [definicja ARFF)](https://weka.wikispaces.com/ARFF)
* Plik zip (.zip)
* R plik obiektu lub obszaru roboczego (. rdata)

Jeśli importujesz dane w formacie takim jak ARFF, który zawiera metadane, Studio (klasyczne) używa tych metadanych do definiowania nagłówka i typu danych każdej kolumny.

W przypadku importowania danych, takich jak format TSV lub CSV, które nie zawierają tych metadanych, Studio (klasyczne) wnioskuje typ danych dla każdej kolumny, pobierając próbki danych. Jeśli dane również nie mają nagłówków kolumn, Studio (klasyczne) udostępnia nazwy domyślne.

Można jawnie określić lub zmienić nagłówki i typy danych dla kolumn za pomocą modułu [Edytuj metadane.][edit-metadata]

Następujące typy danych są rozpoznawane przez Studio (klasyczny):

* Ciąg
* Liczba całkowita
* Double
* Wartość logiczna
* DateTime
* przedział_czasu

Studio używa wewnętrznego typu danych o nazwie ***tabela danych*** do przekazywania danych między modułami. Dane można jawnie przekonwertować na format tabeli danych za pomocą modułu [Konwertuj na zestaw danych.][convert-to-dataset]

Każdy moduł, który akceptuje formaty inne niż tabela danych, przekonwertuje dane na tabelę danych w trybie dyskretnym przed przekazaniem ich do następnego modułu.

W razie potrzeby można przekonwertować format tabeli danych z powrotem na format CSV, TSV, ARFF lub SVMLight przy użyciu innych modułów konwersji.
Poszukaj w sekcji **Konwersje formatu danych** palety modułów dla modułów, które wykonują te funkcje.

## <a name="data-capacities"></a>Pojemność danych

Moduły w uściślijaniu maszynowym (klasycznym) obsługują zestawy danych o gęstości danych liczbowych o gęstości do 10 GB w typowych przypadkach użycia. Jeśli moduł przyjmuje więcej niż jedną operację wprowadzania danych wejściowych, wówczas 10 GB to łączny rozmiar wszystkich danych wejściowych. Można przykładać większych zestawów danych przy użyciu zapytań z bazy danych Hive lub Azure SQL Database lub można użyć uczenia przez liczniki przetwarzania wstępnego przed zaimportowanie danych.  

Podczas normalizacji funkcji następujące typy danych mogą ulegać rozszerzaniu do większych zestawów danych. Takie dane muszą być mniejsze niż 10 GB:

* Rozrzedzone
* Podzielone na kategorie
* Ciągi
* Dane binarne

W przypadku następujących modułów obowiązuje ograniczenie do zestawów danych mniejszych niż 10 GB:

* Moduły polecania
* Moduł Synthetic Minority Oversampling Technique (SMOTE)
* Moduły skryptów: R, Python, SQL
* Moduły, w których rozmiar danych wyjściowych może być większy niż rozmiar danych wejściowych, na przykład Przyłączenie lub Tworzenie skrótu funkcji
* Krzyżowa weryfikacja, Hiperparametry modelu strojenia, Regresja porządkowa oraz Multiklasa Jedna kontra wszystkie, gdy liczba iteracji jest bardzo duża

W przypadku zestawów danych, które są większe niż kilka GB, przekaż dane do usługi Azure Storage lub usługi Azure SQL Database lub użyj usługi Azure HDInsight, zamiast przekazywania bezpośrednio z pliku lokalnego.

Informacje o danych obrazu można znaleźć w odwołaniu do modułu [Importuj obrazy.](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes)

## <a name="import-from-a-local-file"></a>Importowanie z pliku lokalnego

Możesz przesłać plik danych z dysku twardego, aby użyć go jako danych szkoleniowych w Studio (klasyczny). Podczas importowania pliku danych należy utworzyć moduł zestawu danych gotowy do użycia w eksperymentach w obszarze roboczym.

Aby zaimportować dane z lokalnego dysku twardego, wykonaj następujące czynności:

1. Kliknij **+NOWOŚĆ** w dolnej części okna Studio (klasyczne).
2. Wybierz **zestaw danych** i z pliku **lokalnego**.
3. W oknie **dialogowym Przekazywanie nowego zestawu danych** przejdź do pliku, który chcesz przekazać.
4. Wprowadź nazwę, zidentyfikuj typ danych i opcjonalnie wprowadź opis. Zaleca się opis — umożliwia rejestrowanie wszelkich cech danych, które mają być zapamiętane podczas korzystania z danych w przyszłości.
5. Pole wyboru **Jest to nowa wersja istniejącego zestawu danych** umożliwia zaktualizowanie istniejącego zestawu danych o nowe dane. Aby to zrobić, kliknij to pole wyboru, a następnie wprowadź nazwę istniejącego zestawu danych.

![Przekazywanie nowego zestawu danych](./media/import-data/upload-dataset-from-local-file.png)

Czas przesyłania zależy od rozmiaru danych i szybkości połączenia z usługą. Jeśli wiesz, że plik zajmie dużo czasu, możesz robić inne rzeczy wewnątrz Studio (klasyczny) podczas oczekiwania. Jednak zamknięcie przeglądarki przed zakończeniem przekazywania danych powoduje niepowodzenie przekazywania.

Po przesłaniu danych są one przechowywane w module zestawu danych i dostępne dla każdego eksperymentu w obszarze roboczym.

Podczas edytowania eksperymentu zestawy danych zostały przekazane na liście **Moje zestawy danych** na liście Zapisane zestawy danych w palecie **modułów.** Możesz przeciągnąć i upuścić zestaw danych na kanwę eksperymentu, jeśli chcesz użyć zestawu danych do dalszej analizy i uczenia maszynowego.

## <a name="import-from-online-data-sources"></a>Importowanie ze źródeł danych online

Korzystając z modułu [Importuj dane,][import-data] eksperyment może importować dane z różnych źródeł danych online podczas uruchamiania eksperymentu.

> [!NOTE]
> Ten artykuł zawiera ogólne informacje na temat modułu [Importuj dane.][import-data] Aby uzyskać bardziej szczegółowe informacje na temat typów danych, do których można uzyskać dostęp, formatów, parametrów i odpowiedzi na typowe pytania, zobacz temat odwołania do modułu [Importuj dane.][import-data]

Korzystając z modułu [Importuj dane,][import-data] można uzyskać dostęp do danych z jednego z kilku źródeł danych online podczas uruchamiania eksperymentu:

* Adres URL sieci Web przy użyciu protokołu HTTP
* Hadoop za pomocą HiveQL
* Azure Blob Storage
* Tabela platformy Azure
* Baza danych SQL platformy Azure lub program SQL Server na maszynie Wirtualnej platformy Azure
* Lokalna baza danych programu SQL Server
* Dostawca zestawienia danych, OData obecnie
* Azure Cosmos DB

Ponieważ te dane szkoleniowe są dostępne podczas eksperymentu jest uruchomiony, jest dostępny tylko w tym eksperymencie. Dla porównania dane, które zostały zapisane w module zestawu danych jest dostępna dla każdego eksperymentu w obszarze roboczym.

Aby uzyskać dostęp do źródeł danych online w eksperymencie Studio (klasycznym), dodaj moduł [Importuj dane][import-data] do eksperymentu. Następnie wybierz pozycję **Uruchom Kreatora importu danych** w obszarze **Właściwości,** aby uzyskać instrukcje krok po kroku, aby wybrać i skonfigurować źródło danych. Alternatywnie można ręcznie wybrać **źródło danych** w obszarze **Właściwości** i podać parametry potrzebne do uzyskania dostępu do danych.

Obsługiwane źródła danych online są wyszczególnione w poniższej tabeli. W tej tabeli podsumowano również obsługiwane formaty plików oraz parametry używane do uzyskiwania dostępu do danych.

> [!IMPORTANT]
> Obecnie moduły [Importuj dane][import-data] i [Eksportuj dane][export-data] mogą odczytywać i zapisywać dane tylko z magazynu platformy Azure utworzonego przy użyciu klasycznego modelu wdrażania. Innymi słowy nowy typ konta usługi Azure Blob Storage, który oferuje warstwę dostępu do magazynu na gorąco lub warstwę dostępu do magazynu chłodnego nie jest jeszcze obsługiwana.
>
> Ogólnie rzecz biorąc nie powinno to mieć wpływu na wszystkie konta magazynu platformy Azure, które zostały utworzone przed udostępnieniem tej opcji usługi.
> Jeśli chcesz utworzyć nowe konto, wybierz **klasyczne** dla modelu wdrażania lub użyj Menedżera zasobów i wybierz pozycję **Ogólne przeznaczenie** zamiast magazynu **obiektów Blob** dla **rodzaju konta**.
>
> Aby uzyskać więcej informacji, zobacz [Usługi Azure Blob Storage: Hot i Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Obsługiwane źródła danych online
Moduł danych **importu** usługi Azure Machine Learning Studio (klasyczny) obsługuje następujące źródła danych:

| Źródło danych | Opis | Parametry |
| --- | --- | --- |
| Adres URL sieci Web za pośrednictwem protokołu HTTP |Odczytuje dane w formatach wartości oddzielonych przecinkami (CSV), wartości oddzielone kartami (TSV), format pliku relacji atrybutów (ARFF) i obsługa maszyn wektorowych (SVM-light) z dowolnego adresu URL sieci Web, który używa protokołu HTTP |<b>ADRES URL</b>: Określa pełną nazwę pliku, w tym adres URL witryny i nazwę pliku, z dowolnym rozszerzeniem. <br/><br/><b>Format danych</b>: Określa jeden z obsługiwanych formatów danych: CSV, TSV, ARFF lub SVM-light. Jeśli dane mają wiersz nagłówka, jest on używany do przypisywania nazw kolumn. |
| Hadoop/HDFS |Odczytuje dane z rozproszonego magazynu w Hadoop. Określ dane, które mają być używane przy użyciu HiveQL, języka zapytań podobnych do SQL. HiveQL może również służyć do agregowania danych i wykonywania filtrowania danych przed dodaniem danych do Studio (classic). |<b>Kwerenda bazy danych gałęzi:</b>Określa kwerendę hive używaną do generowania danych.<br/><br/><b>Identyfikator URI serwera HCatalog</b> : Określ nazwę klastra przy użyciu formatu * &lt;nazwy&gt;klastra .azurehdinsight.net.*<br/><br/><b>Nazwa konta użytkownika Hadoop</b>: Określa nazwę konta użytkownika Hadoop używanego do aprowizowania klastra.<br/><br/><b>Hasło konta użytkownika Hadoop</b> : Określa poświadczenia używane podczas inicjowania obsługi administracyjnej klastra. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów Hadoop w pliku HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Lokalizacja danych wyjściowych:</b>Określa, czy dane są przechowywane w rozproszonym systemie plików Hadoop (HDFS) lub na platformie Azure. <br/><ul>Jeśli przechowujesz dane wyjściowe w systemie PLIKÓW HDFS, określ identyfikator URI serwera HDFS. (Pamiętaj, aby użyć nazwy klastra HDInsight bez prefiksu HTTPS://). <br/><br/>Jeśli przechowujesz dane wyjściowe na platformie Azure, należy określić nazwę konta magazynu platformy Azure, klucz dostępu magazynu i nazwę kontenera magazynu.</ul> |
| Baza danych SQL |Odczytuje dane przechowywane w bazie danych SQL platformy Azure lub w bazie danych programu SQL Server uruchomionej na maszynie wirtualnej platformy Azure. |<b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym jest uruchomiona baza danych.<br/><ul>W przypadku usługi Azure SQL Database wprowadź nazwę serwera, który jest generowany. Zazwyczaj ma formularz * &lt;generated_identifier&gt;.database.windows.net.* <br/><br/>W przypadku serwera SQL hostowanego na maszynie wirtualnej platformy Azure wprowadź *tcp:&lt;Nazwa&gt;DNS maszyny wirtualnej , 1433*</ul><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika konta, które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Hasło konta użytkownika serwera</b>: Określa hasło dla konta użytkownika.<br/><br/><b>Kwerenda bazy danych</b>:Wprowadź instrukcję SQL opisującą dane, które chcesz odczytać. |
| Lokalna baza danych SQL |Odczytuje dane przechowywane w lokalnej bazie danych SQL. |<b>Brama danych</b>: Określa nazwę bramy zarządzania danymi zainstalowanej na komputerze, na którym może ona uzyskać dostęp do bazy danych programu SQL Server. Aby uzyskać informacje dotyczące konfigurowania bramy, zobacz [Wykonywanie zaawansowanej analizy za pomocą usługi Azure Machine Learning Studio (klasycznie) przy użyciu danych z lokalnego serwera SQL](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym jest uruchomiona baza danych.<br/><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika konta, które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Nazwa użytkownika i hasło:</b>Kliknij przycisk <b>Wprowadź wartości,</b> aby wprowadzić poświadczenia bazy danych. Uwierzytelniania zintegrowanego systemu Windows lub uwierzytelniania programu SQL Server można używać w zależności od konfiguracji lokalnego programu SQL Server.<br/><br/><b>Kwerenda bazy danych</b>:Wprowadź instrukcję SQL opisującą dane, które chcesz odczytać. |
| Tabela platformy Azure |Odczytuje dane z usługi tabel w usłudze Azure Storage.<br/><br/>Jeśli czytasz duże ilości danych rzadko, użyj usługi Azure Table Service. Zapewnia elastyczne, nierelacyjne (NoSQL), masowo skalowalne, niedrogie i o wysokiej dostępności rozwiązanie pamięci masowej. |Opcje w **importowanych danych** zmieniają się w zależności od tego, czy uzyskujesz dostęp do informacji publicznych, czy do konta magazynu prywatnego, które wymaga poświadczeń logowania. Jest to określane przez <b>typ uwierzytelniania,</b> który może mieć wartość "PublicOrSAS" lub "Konto", z których każdy ma swój własny zestaw parametrów. <br/><br/><b>Identyfikator URI (Sygnatura dostępu współdzielonego) lub udostępnionego :</b>Parametry są następujące:<br/><br/><ul><b>Identyfikator URI tabeli:</b>Określa publiczny lub sygnaturowy adres URL tabeli.<br/><br/><b>Określa wiersze do skanowania w poszukiwaniu nazw właściwości:</b>Wartości są <i>TopN</i> do skanowania określonej liczby wierszy lub <i>ScanAll,</i> aby uzyskać wszystkie wiersze w tabeli. <br/><br/>Jeśli dane są jednorodne i przewidywalne, zaleca się wybranie *opcji TopN* i wprowadzenie liczby dla N. W przypadku dużych tabel może to spowodować szybsze czasy odczytu.<br/><br/>Jeśli dane są uporządkowane za pomocą zestawów właściwości, które różnią się w zależności od głębokości i położenia tabeli, wybierz opcję *ScanAll,* aby zeskanować wszystkie wiersze. Zapewnia to integralność wynikowej właściwości i konwersji metadanych.<br/><br/></ul><b>Prywatne konto pamięci masowej</b>: Parametry to: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta zawierającego tabelę do odczytu.<br/><br/><b>Klucz konta:</b>Określa klucz magazynu skojarzony z kontem.<br/><br/><b>Nazwa tabeli</b> : Określa nazwę tabeli zawierającej dane do odczytu.<br/><br/><b>Wiersze do skanowania w poszukiwaniu nazw właściwości:</b>Wartości są <i>TopN</i> do skanowania określonej liczby wierszy lub <i>ScanAll,</i> aby uzyskać wszystkie wiersze w tabeli.<br/><br/>Jeśli dane są jednorodne i przewidywalne, zaleca się, aby wybrać *TopN* i wprowadzić liczbę dla N. W przypadku dużych tabel może to spowodować szybsze czasy odczytu.<br/><br/>Jeśli dane są uporządkowane za pomocą zestawów właściwości, które różnią się w zależności od głębokości i położenia tabeli, wybierz opcję *ScanAll,* aby zeskanować wszystkie wiersze. Zapewnia to integralność wynikowej właściwości i konwersji metadanych.<br/><br/> |
| Azure Blob Storage |Odczytuje dane przechowywane w usłudze obiektów Blob w usłudze Azure Storage, w tym obrazy, tekst bez struktury lub dane binarne.<br/><br/>Za pomocą usługi obiektów Blob można publicznie uwidaczniać dane lub przechowywać dane aplikacji prywatnie. Dostęp do danych można uzyskać z dowolnego miejsca za pomocą połączeń HTTP lub HTTPS. |Opcje w module **Importuj dane** zmieniają się w zależności od tego, czy uzyskujesz dostęp do informacji publicznych, czy do konta magazynu prywatnego, które wymaga poświadczeń logowania. Jest to określane przez <b>typ uwierzytelniania,</b> który może mieć wartość "PublicOrSAS" lub "Konto".<br/><br/><b>Identyfikator URI (Sygnatura dostępu współdzielonego) lub udostępnionego :</b>Parametry są następujące:<br/><br/><ul><b>Identyfikator URI</b>: Określa publiczny lub sygnaturowy adres URL obiektu blob magazynu.<br/><br/><b>Format pliku</b>: Określa format danych w usłudze obiektów Blob. Obsługiwane formaty to CSV, TSV i ARFF.<br/><br/></ul><b>Prywatne konto pamięci masowej</b>: Parametry to: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta zawierającego obiekt blob, który ma zostać odczytany.<br/><br/><b>Klucz konta:</b>Określa klucz magazynu skojarzony z kontem.<br/><br/><b>Ścieżka do kontenera, katalogu lub obiektu blob:</b> Określa nazwę obiektu blob, który zawiera dane do odczytu.<br/><br/><b>Format pliku obiektów blob</b>: Określa format danych w usłudze obiektów blob. Obsługiwane formaty danych to CSV, TSV, ARFF, CSV z określonym kodowaniem i Excel. <br/><br/><ul>Jeśli format jest CSV lub TSV, należy wskazać, czy plik zawiera wiersz nagłówka.<br/><br/>Za pomocą opcji Programu Excel można odczytywać dane ze skoroszytów programu Excel. W opcji <i>Format danych programu Excel</i> wskaż, czy dane są w zakresie arkusza programu Excel, czy w tabeli programu Excel. W <i>arkuszu programu Excel lub tabeli osadzonej </i>określ nazwę arkusza lub tabeli, z której chcesz odczytać.</ul><br/> |
| Dostawca danych |Odczytuje dane od obsługiwanego dostawcy pliku danych. Obecnie obsługiwany jest tylko format Protokołu Otwartych Danych (OData). |<b>Typ zawartości danych</b>: Określa format OData.<br/><br/><b>Źródłowy adres URL</b>: Określa pełny adres URL źródła danych. <br/>Na przykład następujący adres URL odczytuje z przykładowej bazy danych Northwind:https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importowanie z innego eksperymentu

Będą chwile, kiedy będziesz chciał wziąć wynik pośredni z jednego eksperymentu i użyć go jako część innego eksperymentu. Aby to zrobić, należy zapisać moduł jako zestaw danych:

1. Kliknij dane wyjściowe modułu, który chcesz zapisać jako zestaw danych.
2. Kliknij **pozycję Zapisz jako zestaw danych**.
3. Po wyświetleniu monitu wprowadź nazwę i opis, który umożliwia łatwą identyfikację zestawu danych.
4. Kliknij znacznik wyboru **OK.**

Po zakończeniu zapisywania zestaw danych będzie dostępny do użycia w dowolnym eksperymencie w obszarze roboczym. Można go znaleźć na liście **Zapisane zestawy danych** w palecie modułów.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie usług sieci Web usługi Azure Machine Learning Studio korzystające z modułów importu danych i eksportu danych](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
