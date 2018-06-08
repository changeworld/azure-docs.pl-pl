---
title: Importowanie danych do usługi Machine Learning Studio ze źródeł danych w trybie online | Dokumentacja firmy Microsoft
description: Informacje o importowaniu danych szkoleniowych Azure Machine Learning Studio z różnych źródeł online.
keywords: Importowanie danych, formatu danych, typy danych, źródła danych, dane szkoleniowe
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: ab806f8191bc8edc4d5be6003cd177d8c24d71e7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834679"
---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importowanie danych do usługi Azure Machine Learning Studio z różnych źródeł danych w trybie online za pomocą modułu importu danych
W tym artykule opisano obsługę importowanie danych z różnych źródeł i informacje potrzebne do przenoszenia danych z tych źródeł w eksperymencie usługi Azure Machine Learning.

> [!NOTE]
> Ten artykuł zawiera informacje ogólne o [i zaimportuj dane] [ import-data] modułu. Aby uzyskać szczegółowe informacje o typach danych można uzyskać dostęp, formatów, parametry oraz odpowiedzi na często zadawane pytania, zobacz temat odwołania modułu dla [i zaimportuj dane] [ import-data] modułu.
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Wprowadzenie
Za pomocą [i zaimportuj dane] [ import-data] modułu, są dostępne dane z jednego z wielu źródeł danych w trybie online jest uruchomiona eksperymentu [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* Adres URL sieci Web przy użyciu protokołu HTTP
* Hadoop przy użyciu HiveQL
* Magazyn obiektów blob Azure
* Tabela platformy Azure
* Azure SQL database lub SQL Server na maszynie Wirtualnej Azure
* Lokalną bazą danych programu SQL Server
* Strumieniowe źródło OData obecnie dostawcy danych
* Azure Cosmos DB

Aby uzyskać dostęp do źródeł danych w trybie online w eksperymencie Studio, Dodaj [i zaimportuj dane] [ import-data] modułu do sieci, wybierz **źródła danych**, a następnie podaj parametry niezbędne do uzyskania dostępu dane. Źródła danych w trybie online, które są obsługiwane są wymienione w poniższej tabeli. Ta tabela zawiera podsumowanie, formaty plików, które są obsługiwane i parametrów, które są używane do uzyskiwania dostępu do danych.

Należy pamiętać, że ponieważ dane szkoleniowe uzyskiwania dostępu do eksperymentu jest uruchomiona, jest on dostępny tylko w tym eksperymencie. Dla porównania danych przechowywanych w module zestawu danych są dostępne dla każdego doświadczenia w obszarze roboczym.

> [!IMPORTANT]
> Obecnie [i zaimportuj dane] [ import-data] i [eksportowanie danych] [ export-data] modułów może odczytywać i zapisywać dane tylko z usługi Azure storage utworzonych przy użyciu klasycznego model wdrażania. Innymi słowy nowy typ konta magazynu obiektów Blob Azure oferuje warstwy dostępu do magazynu gorącego lub warstwy dostępu do magazynu chłodnego nie jest jeszcze obsługiwane. 
> 
> Ogólnie rzecz biorąc, wszystkie konta magazynu platformy Azure może być utworzony przed tej opcji usługa stały się dostępne powinien bez zmian. 
> Jeśli musisz utworzyć nowe konto, wybierz **klasycznego** wdrożenia modelu, lub użyj Menedżera zasobów i wybierz **ogólnego przeznaczenia** zamiast **magazynu obiektów Blob** dla  **Konto rodzaju**. 
> 
> Aby uzyskać więcej informacji, zobacz [usługi Azure Blob Storage: warstwy magazynu gorącego i chłodnego](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Obsługiwane źródeł danych w trybie online
Usługa Azure Machine Learning **i zaimportuj dane** Moduł obsługuje następujące źródła danych:

| Źródło danych | Opis | Parametry |
| --- | --- | --- |
| Adres URL sieci Web za pośrednictwem protokołu HTTP |Odczytuje dane w wartości rozdzielanych przecinkami (CSV), wartości tabulatorami (TSV) relacja atrybutu pliku format (ARFF) i formatów pomocy technicznej wektor maszyny (SVM światła) z adresu URL sieci web, który korzysta z protokołu HTTP |<b>Adres URL</b>: Określa pełną nazwę pliku, w tym adres URL witryny i z dowolnym rozszerzeniem nazwy pliku. <br/><br/><b>Format danych</b>: Określa jeden z obsługiwanych danych formaty: CSV, TSV, ARFF lub SVM-light. Jeśli dane mają wiersz nagłówka, służy można przypisać nazwy kolumn. |
| Hadoop/HDFS |Odczytuje dane z rozproszonego magazynu na platformie Hadoop. Należy określić dane, które przy użyciu HiveQL, języka przypominającego SQL zapytań. HiveQL może również służyć do agregować dane i wykonywać danych filtrowania, aby dodać dane do usługi Machine Learning Studio. |<b>Gałąź rejestru, kwerendy bazy danych</b>: Określa zapytanie Hive służący do generowania danych.<br/><br/><b>Identyfikator URI serwera HCatalog </b> : określona nazwa klastra przy użyciu formatu  *&lt;nazwa klastra&gt;. azurehdinsight.net.*<br/><br/><b>Nazwa konta użytkownika Hadoop</b>: Określa nazwę konta użytkownika platformy Hadoop, użyć do udostępnienia klastra.<br/><br/><b>Hasło konta użytkownika Hadoop</b> : Określa poświadczenia używane podczas inicjowania obsługi klastra. Aby uzyskać więcej informacji, zobacz [klastrów utworzyć Hadoop w HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Lokalizacja danych wyjściowych</b>: Określa, czy dane są przechowywane w systemie plików rozproszonych Hadoop (HDFS) lub na platformie Azure. <br/><ul>Jeśli przechowujesz dane wyjściowe w systemie plików HDFS, określ identyfikator URI serwera systemu plików HDFS. (Należy użyć nazwy klastra usługi HDInsight bez prefiksu HTTPS://). <br/><br/>Jeśli Twoje dane przechowywane na platformie Azure, należy określić nazwę konta magazynu platformy Azure, klucz dostępu do magazynu i nazwa kontenera magazynu.</ul> |
| Baza danych SQL |Odczytuje dane przechowywane w bazie danych Azure SQL lub w bazie danych programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure. |<b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym działa bazy danych.<br/><ul>W przypadku bazy danych SQL Azure wprowadź nazwę serwera, który jest generowany. Zwykle ma on formularz  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>W przypadku programu SQL server hostowanych na maszynie wirtualnej Azure wprowadź *tcp:&lt;nazwę DNS maszyny wirtualnej&gt;, 1433*</ul><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta, które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Hasło konta użytkownika serwera</b>: Określa hasło dla konta użytkownika.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL opisujący danych, który chcesz odczytać. |
| Lokalna baza danych SQL |Odczytuje dane, które są przechowywane w lokalnej bazie danych SQL. |<b>Brama danych</b>: Określa nazwę bramy zarządzania danymi, które są zainstalowane na komputerze, gdzie ona dostęp do bazy danych SQL Server. Aby uzyskać informacje dotyczące konfigurowania bramy, zobacz [zaawansowane analizy przy użyciu usługi Azure Machine Learning przy użyciu danych z lokalnego programu SQL server wykonaj](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym działa bazy danych.<br/><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta, które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Nazwa użytkownika i hasło</b>: kliknij <b>wprowadź wartości</b> Aby wprowadzić swoje poświadczenia bazy danych. Umożliwia zintegrowane uwierzytelnianie systemu Windows lub uwierzytelniania programu SQL Server, w zależności od konfiguracji serwera SQL lokalnie.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL opisujący danych, który chcesz odczytać. |
| Tabela platformy Azure |Odczytuje dane z usługi tabel w usłudze Azure Storage.<br/><br/>Jeśli odczytu jest rzadko dużych ilości danych, należy korzystać z usługi tabeli platformy Azure. Zapewnia elastyczne i nierelacyjnych (NoSQL), skalowalna na ogromną skalę, niedrogie i wysokiej dostępności pamięci masowej. |Opcje w **i zaimportuj dane** zmiany w zależności od tego, czy dostęp do informacji publicznej lub konto magazynu prywatnych, który wymaga poświadczeń logowania. Jest to określane za pomocą <b>typ uwierzytelniania</b> który może mieć wartość "PublicOrSAS" lub "Account", z których każdy ma własny zestaw parametrów. <br/><br/><b>Publiczny lub dostęp sygnatury dostępu Współdzielonego URI</b>: parametry są:<br/><br/><ul><b>Tabela URI</b>: Określa publicznego lub adres URL SAS dla tabeli.<br/><br/><b>Określa wiersze do skanowania pod kątem nazw właściwości</b>: wartości są <i>TopN</i> skanowanie określoną liczbę wierszy, lub <i>ScanAll</i> można pobrać wszystkie wiersze w tabeli. <br/><br/>Jeśli dane są jednorodne i przewidywalne, zalecane jest wybranie *TopN* i wprowadzić numer N. W przypadku dużych tabel może to spowodować szybsze czasów odczytu.<br/><br/>Jeśli dane mają strukturę z zestawów właściwości, które zależą od głębokość i położenie tabeli, wybierz *ScanAll* opcję skanowania wszystkich wierszy. To zapewnia integralność właściwość wynikowa i konwersji metadanych.<br/><br/></ul><b>Konto magazynu prywatne</b>: parametry są: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, które zawiera tabelę do odczytu.<br/><br/><b>Klucz konta</b>: Określa klucz magazynu skojarzone z kontem.<br/><br/><b>Nazwa tabeli</b> : Określa nazwę tabeli, która zawiera dane do odczytu.<br/><br/><b>Wiersze do skanowania w poszukiwaniu nazwy właściwości</b>: wartości są <i>TopN</i> skanowanie określoną liczbę wierszy, lub <i>ScanAll</i> można pobrać wszystkie wiersze w tabeli.<br/><br/>Jeśli dane są jednorodne i przewidywalne, zaleca się wybranie *TopN* i wprowadzić numer N. W przypadku dużych tabel może to spowodować szybsze czasów odczytu.<br/><br/>Jeśli dane mają strukturę z zestawów właściwości, które zależą od głębokość i położenie tabeli, wybierz *ScanAll* opcję skanowania wszystkich wierszy. To zapewnia integralność właściwość wynikowa i konwersji metadanych.<br/><br/> |
| Azure Blob Storage |Odczytuje dane przechowywane w usłudze obiektów Blob w magazynie Azure, w tym obrazów, bez struktury tekst lub dane binarne.<br/><br/>Usługa Blob można użyć publicznie udostępniania danych lub prywatnie przechowywanie danych aplikacji. Dostęp do danych z dowolnego miejsca przy użyciu połączenia protokołu HTTP lub HTTPS. |Opcje w **i zaimportuj dane** modułu zmiany w zależności od tego, czy dostęp do informacji publicznej lub konto magazynu prywatnych, który wymaga poświadczeń logowania. Jest to określane za pomocą <b>typ uwierzytelniania</b> który może mieć wartość "PublicOrSAS" lub "Konto".<br/><br/><b>Publiczny lub dostęp sygnatury dostępu Współdzielonego URI</b>: parametry są:<br/><br/><ul><b>Identyfikator URI</b>: Określa publicznego lub adres URL SAS obiektu blob magazynu.<br/><br/><b>Format pliku</b>: Określa format danych w usłudze obiektów Blob. Obsługiwane formaty to CSV, TSV i ARFF.<br/><br/></ul><b>Konto magazynu prywatne</b>: parametry są: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, która zawiera obiekt blob, który chcesz odczytać.<br/><br/><b>Klucz konta</b>: Określa klucz magazynu skojarzone z kontem.<br/><br/><b>Ścieżka do kontenera, katalogu lub obiektu blob </b> : Określa nazwę obiektu blob, który zawiera dane do odczytu.<br/><br/><b>Format pliku BLOB</b>: Określa format danych w usłudze obiektów blob. Format danych obsługiwanych są CSV, TSV, ARFF, CSV z określonego kodowania i Excel. <br/><br/><ul>Jeśli jest w formacie CSV lub TSV, pamiętaj wskazać, czy ten plik zawiera wiersz nagłówka.<br/><br/>Opcja Excel można odczytać danych ze skoroszytów programu Excel. W <i>format danych programu Excel</i> , należy wskazać, czy dane są w zakresie arkusza programu Excel lub w tabeli programu Excel. W <i>arkuszu programu Excel lub osadzonej tabeli </i>, należy określić nazwę arkusza lub tabeli, która ma być odczytywane.</ul><br/> |
| Dostawca źródła danych |Odczytuje dane z obsługiwanej dostawcy kanału informacyjnego. Obecnie jest obsługiwany tylko format Open Data Protocol (OData). |<b>Typ zawartości danych</b>: Określa OData format.<br/><br/><b>Źródłowy adres URL</b>: Określa pełny adres URL strumieniowego źródła danych. <br/>Na przykład następujący adres URL odczytuje z przykładowej bazy danych Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie usługi sieci web uczenie Maszynowe Azure korzystających z modułów danych importowanie i eksportowanie danych](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
