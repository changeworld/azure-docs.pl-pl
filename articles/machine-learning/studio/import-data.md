---
title: Importuj dane z różnych źródeł danych
titleSuffix: Azure Machine Learning Studio
description: Jak zaimportować dane do usługi Azure Machine Learning Studio z różnych źródeł danych. Dowiedz się, jakie typy danych i formatów danych są obsługiwane.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 41cc1d6638871f26ae942e724a402e17f52150fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811009"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio z różnych źródeł danych

Aby skorzystać z własnych danych w usłudze Machine Learning Studio, tworzenie i uczenie rozwiązania do analizy predykcyjnej, można używać danych z: 

* **Plik lokalny** -ładowanie danych lokalnych, które wcześniej z dysku twardego do tworzenia modułu zestawu danych w obszarze roboczym
* **Źródła danych online** — użyj [importu danych] [ import-data] modułu dostępu do danych z jednego z kilku źródeł online po uruchomieniu eksperymentu
* **Usługi Machine Learning Studio eksperymentu** — korzystanie z danych, który został zapisany jako zestaw danych w usłudze Machine Learning Studio
* [**Baza danych programu SQL Server w środowisku lokalnym** ](use-data-from-an-on-premises-sql-server.md) -użyte dane z lokalnej bazy danych programu SQL Server, bez konieczności ręcznego kopiowania danych

> [!NOTE]
> Wiele przykładowych zestawów danych są dostępne w usłudze Machine Learning Studio, można użyć danych szkoleniowych. Aby uzyskać informacje na ten temat, zobacz [użyj przykładowych zestawów danych w usłudze Azure Machine Learning Studio](use-sample-datasets.md).

## <a name="prepare-data"></a>Przygotowywanie danych

Usługa Machine Learning Studio jest przeznaczony do pracy z prostokątne lub tabelaryczne dane, takie jak dane tekstowe, rozdzielonych lub ze strukturą danych z bazy danych, chociaż w niektórych sytuacjach może użyć danych innych niż prostokątne.

Najlepiej Twoje dane są stosunkowo czystego, przed jego zaimportowaniem do Studio. Na przykład należy zadbać o problemy, takie jak ciągi bez cudzysłowów.

Istnieją moduły dostępne w Studio, które umożliwiają niektóre operacje na danych w ramach eksperymentu, po zaimportowaniu danych. W zależności od algorytmów, których będziesz korzystać z uczenia maszynowego, konieczne może być zdecydować, jak będzie obsługiwać danych strukturalnych problemów, takich jak brakujące wartości i danych rozrzedzonych, wiąże się z modułów, które mogą pomóc przy użyciu których. Szukaj w **przekształcania danych** części palety modułów dla modułów, które wykonują te funkcje.

W dowolnym momencie w eksperymencie można wyświetlić lub pobrać dane, który jest wytwarzany przez moduł, klikając pozycję port wyjściowy. W zależności od modułu może istnieć opcje pobierania różnych dostępnych lub można wizualizować dane w przeglądarce sieci web w programie Studio.

## <a name="supported-data-formats-and-data-types"></a>Formaty obsługiwane dane i typy danych

Importujesz wiele typów danych do eksperymentu, w zależności od tego, jakie mechanizm służy do importowania danych i gdzie pochodzi z:

* Zwykły tekst (txt)
* Wartości rozdzielanych przecinkami (CSV) przy użyciu nagłówka (.csv) lub bez (. nh.csv)
* Wartości rozdzielane tabulatorami (TSV) przy użyciu nagłówka (tsv) lub bez (. nh.tsv)
* Excel file
* Tabela platformy Azure
* Tabela programu hive
* Tabela bazy danych SQL
* Wartości OData
* Dane SVMLight (.svmlight) (zobacz [definicji SVMLight](http://svmlight.joachims.org/) dla informacji o formacie)
* Atrybut danych relacji pliku Format (ARFF) (.arff) (zobacz [definicji ARFF](https://weka.wikispaces.com/ARFF) dla informacji o formacie)
* Zip file (.zip)
* Plik obiektu lub obszaru roboczego R (. Dane_rekordu)

Po zaimportowaniu danych w formacie, takich jak ARFF, który zawiera metadane Studio używa tych metadanych do definiowania nagłówka i typu danych każdej kolumny.

Po zaimportowaniu danych, takie jak format TSV lub CSV, który nie zawiera metadanych Studio wnioskuje typ danych dla każdej kolumny przez próbkowanie danych. Jeśli dane nie ma również nagłówki kolumn, Studio zapewnia domyślne nazwy.

Można jawnie określ lub zmień nagłówki i typy danych dla kolumn przy użyciu [edytować metadane] [ edit-metadata] modułu.

Następujące typy danych są rozpoznawane przez Studio:

* String
* Integer
* Double
* Boolean
* DateTime
* TimeSpan

Studio korzysta z typem danych wewnętrznych, o nazwie ***tabeli danych*** do przekazywania danych między modułami. Można jawnie przekonwertować danych w formacie tabeli danych, używając [przekonwertować zestawu danych] [ convert-to-dataset] modułu.

Wszelkie moduł, który akceptuje formatach niż tabela danych dyskretnie przekonwertuje tabeli danych danych przed przekazaniem go do następnego modułu.

Jeśli to konieczne, możesz przekonwertować format tabeli danych do pliku CSV, TSV, ARFF SVMLight format lub przy użyciu innych modułów konwersji.
Szukaj w **konwersje formatu danych** części palety modułów dla modułów, które wykonują te funkcje.

## <a name="data-capacities"></a>Pojemności danych.

W typowych przypadkach użycia moduły w usłudze Machine Learning Studio obsługują zestawy danych o rozmiarze maksymalnie 10 GB, zawierające gęsto upakowane dane liczbowe. Jeśli moduł przyjmuje więcej niż jedną operację wprowadzania danych wejściowych, wówczas 10 GB to łączny rozmiar wszystkich danych wejściowych. Próbkowanie większych zestawów danych, korzystając z zapytań programu Hive lub usługi Azure SQL Database, możesz też uczenia przez liczenie przetwarzania wstępnego, przed zaimportowaniem danych.  

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

W przypadku zestawów danych o rozmiarach większych niż kilka GB przekazać dane do usługi Azure Storage lub Azure SQL Database lub użyć usługi Azure HDInsight, zamiast przekazywać dane bezpośrednio z pliku lokalnego.

Można znaleźć informacje o danych obrazu w [Import obrazów](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) odwołania do modułu.

## <a name="import-from-a-local-file"></a>Importuj z pliku lokalnego

Możesz przekazać plik danych z dysku twardego do użycia jako dane szkoleniowe w programie Studio. Podczas importowania pliku danych, należy utworzyć moduł zestawu danych gotowe do użycia w eksperymentów w obszarze roboczym.

Aby zaimportować dane z lokalnego dysku twardego, wykonaj następujące czynności:

1. Kliknij przycisk **+ nowy** w dolnej części okna Studio.
2. Wybierz **DATASET** i **z pliku lokalnego**.
3. W **przekazać nowy zestaw danych** okno dialogowe, przejdź do pliku, który chcesz przekazać.
4. Wprowadź nazwę, identyfikator typu danych i opcjonalnie wprowadź opis. Opis jest zalecane — umożliwia zarejestrowanie dowolnej właściwości o dane, które należy pamiętać, korzystając z danych w przyszłości.
5. Pole wyboru **to nowa wersja istniejący zestaw danych** pozwala zaktualizować istniejący zestaw danych przy użyciu nowych danych. Aby to zrobić, kliknij to pole wyboru, a następnie wprowadź nazwę istniejący zestaw danych.

![Przekaż nowy zestaw danych](./media/import-data/upload-dataset-from-local-file.png)

Przekaż czas zależy od rozmiaru danych i prędkości połączenia z usługą. Jeśli wiesz, że plik będzie zająć dużo czasu, można wykonywać inne czynności, w programie Studio, podczas oczekiwania. Jednak zamknięcie przeglądarki przed zakończeniem przekazywania danych powoduje, że przekazywanie nie powiedzie się.

Po przekazaniu danych są przechowywane w module zestawu danych i jest dostępny dla każdego doświadczenia w obszarze roboczym.

Podczas edycji eksperymentu, możesz znaleźć zestawy danych przekazane w **Moje zestawy danych** w obszarze **zapisane zestawów danych** liście palety modułów. Możesz przeciągać i upuszczać zestawu danych do obszaru roboczego eksperymentu, gdy użytkownik chce używać zestawu danych do dalszej analizy i uczenia maszynowego.

## <a name="import-from-online-data-sources"></a>Importuj ze źródeł danych w trybie online

Za pomocą [Import danych] [ import-data] modułu eksperymentu można importować dane z różnych źródeł danych w trybie online podczas eksperymentu uruchomiona.

> [!NOTE]
> Ten artykuł zawiera ogólne informacje na temat [importu danych] [ import-data] modułu. Aby uzyskać szczegółowe informacje o typach danych, możesz uzyskać dostęp, formatów, parametrów i odpowiedzi na typowe pytania, zobacz temat odwołania modułu dla [importu danych] [ import-data] modułu.

Za pomocą [importu danych] [ import-data] modułu dostęp można uzyskać danych z jednego z kilku źródeł danych w trybie online po uruchomieniu eksperymentu:

* Adres URL sieci Web przy użyciu protokołu HTTP
* Usługi Hadoop przy użyciu HiveQL
* Magazyn obiektów blob Azure
* Tabela platformy Azure
* Usługa Azure SQL database lub SQL Server na maszynie Wirtualnej platformy Azure
* Baza danych programu SQL Server w środowisku lokalnym
* Dostawca, obecnie OData strumieniowych źródeł danych
* Azure Cosmos DB

Ponieważ te dane szkoleniowe odbywa się po uruchomieniu eksperymentu, tylko jest dostępna w tym eksperymencie. Natomiast dane, które zostały zapisane w module zestaw danych jest dostępna dla każdego doświadczenia w obszarze roboczym.

Aby uzyskać dostęp do źródeł danych w trybie online w eksperymencie Studio, należy dodać [importu danych] [ import-data] modułu do eksperymentu. Następnie wybierz pozycję **Uruchom Kreatora importu danych** w obszarze **właściwości** Aby uzyskać szczegółowe instrukcje krok po kroku, aby wybrać i skonfigurować źródło danych. Alternatywnie, można ręcznie wybrać **źródła danych** w obszarze **właściwości** i podać parametrów wymaganych do uzyskania dostępu do danych.

Źródła danych online, które są obsługiwane, są wymienione w poniższej tabeli. Ta tabela zawiera podsumowanie, formaty plików, które są obsługiwane i parametry, które są używane do uzyskania dostępu do danych.

> [!IMPORTANT]
> Obecnie [importu danych] [ import-data] i [Eksport danych] [ export-data] moduły mogą odczytywać i zapisywać dane tylko z usługi Azure storage, utworzony za pomocą klasycznego model wdrażania. Innymi słowy nowy typ konta usługi Azure Blob Storage oferuje Warstwa dostępu gorąca lub chłodna Warstwa dostępu nie jest jeszcze obsługiwane.
>
> Ogólnie rzecz biorąc, wszystkie konta magazynu platformy Azure może być utworzone przed tej opcji usługa stały się dostępne nie powinny zależeć.
> Jeśli potrzebujesz utworzyć nowe konto, wybierz **klasycznego** wdrożenia modelu, lub użyj usługi Resource manager i wybierz **ogólnego przeznaczenia** zamiast **magazynu obiektów Blob** dla  **Rodzaj konta**.
>
> Aby uzyskać więcej informacji, zobacz [usługi Azure Blob Storage: Gorącego i chłodnego warstw magazynowania](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Obsługiwane źródła danych online
Usługa Azure Machine Learning Studio **importu danych** Moduł obsługuje następujące źródła danych:

| Źródło danych | Opis | Parametry |
| --- | --- | --- |
| Adres URL sieci Web za pośrednictwem protokołu HTTP |Odczytuje dane w wartości rozdzielanych przecinkami (CSV), wartości rozdzielane tabulatorami (TSV), relacja atrybutu pliku format (ARFF) i formatów pomocy technicznej wektor maszyny (SVM światła) z adres URL sieci web, która korzysta z protokołu HTTP |<b>ADRES URL</b>: Określa pełną nazwę pliku, w tym adres URL witryny i nazwę pliku z dowolnym rozszerzeniem. <br/><br/><b>Format danych</b>: Określa jeden z formatów obsługiwanych danych: CSV, TSV, ARFF lub SVM światła. Jeśli dane mają wiersz nagłówka, służy do przypisywania nazw kolumn. |
| Hadoop/HDFS |Odczytuje dane z rozproszonego magazynu na platformie Hadoop. Należy określić dane, które chcesz, aby za pomocą HiveQL, język zapytań przypominający SQL. Można również HiveQL do agregowania danych i wykonywania filtrowania, aby dodać dane do Studio danych. |<b>Zapytanie bazy danych programu hive</b>: Określa zapytania programu Hive służącego do generowania danych.<br/><br/><b>Identyfikator URI serwera HCatalog </b> : Określona nazwa klastra przy użyciu formatu  *&lt;nazwy klastra&gt;. azurehdinsight.net.*<br/><br/><b>Nazwa konta użytkownika usługi Hadoop</b>: Określa nazwę konta użytkownika usługi Hadoop, używane do inicjowania obsługi klastra.<br/><br/><b>Hasło konta użytkownika usługi Hadoop</b> : Określa poświadczenia używane podczas aprowizowania klastra. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów usługi Hadoop w HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Lokalizacja danych wyjściowych</b>: Określa, czy dane są przechowywane w Rozproszony system plików Hadoop (HDFS) lub na platformie Azure. <br/><ul>Jeśli przechowujesz dane wyjściowe w systemie plików HDFS, należy określić identyfikator URI serwera systemu plików HDFS. (Należy użyć nazwy klastra HDInsight bez prefiksu HTTPS://). <br/><br/>Jeśli dane wyjściowe są przechowywane na platformie Azure, należy określić nazwę konta usługi Azure storage, klucz dostępu do magazynu i nazwa kontenera magazynu.</ul> |
| Baza danych SQL |Odczytuje dane są przechowywane w bazie danych Azure SQL lub w bazie danych programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure. |<b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym działa baza danych.<br/><ul>W przypadku usługi Azure SQL Database, wprowadź nazwę serwera, który jest generowany. Zwykle ma on postać  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>W przypadku programu SQL server hostowana na maszynie wirtualnej platformy Azure wprowadź *tcp:&lt;nazwę DNS maszyny wirtualnej&gt;, 1433*</ul><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Hasło konta użytkownika serwera</b>: Określa hasło dla konta użytkownika.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL, która opisuje dane chcesz odczytać. |
| Lokalna baza danych SQL |Odczytuje dane, która jest przechowywana w bazie danych SQL w środowisku lokalnym. |<b>Data gateway</b>: Określa nazwę bramy zarządzania danymi, które są zainstalowane na komputerze, na którym ją ma dostęp do bazy danych programu SQL Server. Aby uzyskać informacji na temat konfigurowania bramy, zobacz [wykonaj advanced analytics, za pomocą usługi Azure Machine Learning Studio przy użyciu danych z programu SQL server w środowisku lokalnym](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym działa baza danych.<br/><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Nazwa użytkownika i hasło</b>: Kliknij przycisk <b>wprowadź wartości</b> o podanie poświadczeń bazy danych. Można użyć zintegrowanego uwierzytelniania Windows lub uwierzytelniania programu SQL Server, w zależności od sposobu skonfigurowania na lokalnym serwerze SQL Server.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL, która opisuje dane chcesz odczytać. |
| Tabela platformy Azure |Odczytuje dane z usługi tabel w usłudze Azure Storage.<br/><br/>Jeśli odczyt dużej ilości danych z rzadko, należy użyć usługę Azure Table Service. Zapewnia elastyczny i nierelacyjnych (NoSQL), rozwiązanie do magazynowania wysoce skalowalnych, niedrogich i o wysokiej dostępności. |Opcje w **importu danych** zmianie w zależności od tego, czy dostęp do informacji publicznych lub konto magazynu prywatnego, które wymaga poświadczeń logowania. Jest to określane przez <b>typ uwierzytelniania</b> który może mieć wartość "PublicOrSAS" lub "Account", z których każda ma swój własny zestaw parametrów. <br/><br/><b>Publicznej lub współdzielonej Access Signature (SAS) identyfikator URI</b>: Dostępne są następujące parametry:<br/><br/><ul><b>Identyfikator URI tabeli</b>: Określa publiczną lub adres URL sygnatury dostępu Współdzielonego dla tabeli.<br/><br/><b>Określa wiersze do skanowania w poszukiwaniu nazwy właściwości</b>: Wartości są <i>TopN</i> skanowanie określoną liczbę wierszy, lub <i>ScanAll</i> można pobrać wszystkie wiersze w tabeli. <br/><br/>Jeśli dane są jednorodne i przewidywalne, zalecane jest wybranie *TopN* i wprowadzić numer N. W przypadku dużych tabel może to spowodować szybsze czasy odczytu.<br/><br/>Jeśli dane są skonstruowane z zestawy właściwości, które różnią się zależnie od głębokości i położenie tabeli, wybierz opcję *ScanAll* opcję, aby skanować wszystkie wiersze. Dzięki temu integralności wynikowy właściwości i metadanych konwersji.<br/><br/></ul><b>Konto magazynu prywatnego</b>: Dostępne są następujące parametry: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, które zawiera tabelę do odczytu.<br/><br/><b>Klucz konta</b>: Określa klucz magazynu skojarzonego z kontem.<br/><br/><b>Nazwa tabeli</b> : Określa nazwę tabeli, która zawiera dane do odczytu.<br/><br/><b>Wiersze do skanowania w poszukiwaniu nazwy właściwości</b>: Wartości są <i>TopN</i> skanowanie określoną liczbę wierszy, lub <i>ScanAll</i> można pobrać wszystkie wiersze w tabeli.<br/><br/>Jeśli dane są jednorodne i przewidywalne, firma Microsoft zaleca wybranie *TopN* i wprowadzić numer N. W przypadku dużych tabel może to spowodować szybsze czasy odczytu.<br/><br/>Jeśli dane są skonstruowane z zestawy właściwości, które różnią się zależnie od głębokości i położenie tabeli, wybierz opcję *ScanAll* opcję, aby skanować wszystkie wiersze. Dzięki temu integralności wynikowy właściwości i metadanych konwersji.<br/><br/> |
| Azure Blob Storage |Odczytuje dane przechowywane w usłudze obiektów Blob w usłudze Azure Storage, w tym obrazów, tekstu bez struktury lub dane binarne.<br/><br/>Można użyć usługi obiektów Blob, publicznie ujawniać dane lub prywatnie przechowywać dane aplikacji. Uzyskiwać dostęp do danych z dowolnego miejsca przy użyciu połączeń HTTP lub HTTPS. |Opcje w **importu danych** zmiana modułu, w zależności od tego, czy dostęp do informacji publicznych lub konto magazynu prywatnego, które wymaga poświadczeń logowania. Jest to określane przez <b>typ uwierzytelniania</b> który może mieć wartość "PublicOrSAS" lub "Konto".<br/><br/><b>Publicznej lub współdzielonej Access Signature (SAS) identyfikator URI</b>: Dostępne są następujące parametry:<br/><br/><ul><b>IDENTYFIKATOR URI</b>: Określa publiczny lub adres URL sygnatury dostępu Współdzielonego dla obiektu blob magazynu.<br/><br/><b>Format pliku</b>: Określa format danych w usłudze obiektów Blob. Obsługiwane formaty to formatu CSV, TSV i ARFF.<br/><br/></ul><b>Konto magazynu prywatnego</b>: Dostępne są następujące parametry: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, które zawiera obiekt blob, który chcesz odczytać.<br/><br/><b>Klucz konta</b>: Określa klucz magazynu skojarzonego z kontem.<br/><br/><b>Ścieżka do kontenera, katalogu lub obiektu blob </b> : Określa nazwę obiektu blob, który zawiera dane do odczytu.<br/><br/><b>Format pliku obiektu blob</b>: Określa format danych w usłudze obiektów blob. Formaty obsługiwane dane są CSV, TSV, ARFF, CSV określone kodowanie i program Excel. <br/><br/><ul>Jeśli jest w formacie CSV lub TSV, pamiętaj wskazać, czy ten plik zawiera wiersz nagłówka.<br/><br/>Opcja Excel można odczytać danych ze skoroszytów programu Excel. W <i>format danych programu Excel</i> opcji, określ, czy dane znajdują się w zakresie arkusza programu Excel lub w tabeli programu Excel. W <i>arkusza programu Excel lub tabeli osadzone </i>opcji, należy określić nazwę arkusza lub tabeli, która ma zostać odczytany.</ul><br/> |
| Dostawca strumieniowych źródeł danych |Odczytuje dane z obsługiwanego dostawcy źródła danych. Obecnie jest obsługiwany tylko format Open Data Protocol (OData). |<b>Typ zawartości danych</b>: Określa OData format.<br/><br/><b>Źródłowy adres URL</b>: Określa pełny adres URL strumieniowego źródła danych. <br/>Na przykład następujący adres URL odczytuje z przykładowej bazy danych Northwind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importuj z innego eksperymentu

Będzie czasy, kiedy należy podjąć wyniki pośrednie na podstawie jednego eksperymentu i używać go jako część innego eksperymentu. Aby to zrobić, możesz zapisać modułu jako zestaw danych:

1. Kliknij wyjście modułu, w którym chcesz zapisać jako zestaw danych.
2. Kliknij przycisk **Zapisz jako zestaw danych**.
3. Po wyświetleniu monitu wprowadź nazwę i opis, który pozwoli na łatwe identyfikowanie zestawu danych.
4. Kliknij przycisk **OK** znacznik wyboru.

Po zakończeniu Zapisz zestaw danych będą dostępne do użycia w dowolnym eksperymentu w obszarze roboczym. Znaleźć go w **zapisane zestawów danych** liście palety modułów.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie usługi Azure Machine Learning studio w sieci web korzystające z modułów importu i eksportu danych](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
