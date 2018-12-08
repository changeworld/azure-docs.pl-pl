---
title: Importowanie danych do usługi Machine Learning Studio ze źródeł danych online — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano obsługę importowanie danych w trybie online z różnych źródeł oraz informacjami potrzebnymi do przeniesienia danych z tych źródeł do usługi Azure Machine Learning Studio eksperymentować.
keywords: Importowanie danych, formatu danych, typów danych, źródeł danych, dane szkoleniowe
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
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
ms.openlocfilehash: 26d232567d9689ff8400726427d6dc582343a35f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103323"
---
# <a name="import-data-into-azure-machine-learning-studio-from-online-data-sources"></a>Importowanie danych do usługi Azure Machine Learning Studio ze źródeł danych online 
W tym artykule opisano obsługę importowanie danych w trybie online z różnych źródeł oraz informacjami potrzebnymi do przeniesienia danych z tych źródeł do usługi Azure Machine Learning Studio eksperymentować.

> [!NOTE]
> Ten artykuł zawiera ogólne informacje na temat [importu danych] [ import-data] modułu. Aby uzyskać szczegółowe informacje o typach danych, możesz uzyskać dostęp, formatów, parametrów i odpowiedzi na typowe pytania, zobacz temat odwołania modułu dla [importu danych] [ import-data] modułu.
> 
> 

## <a name="introduction"></a>Wprowadzenie
Za pomocą [importu danych] [ import-data] modułu dostęp można uzyskać danych z jednego z kilku źródeł danych w trybie online po uruchomieniu eksperymentu [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* Adres URL sieci Web przy użyciu protokołu HTTP
* Usługi Hadoop przy użyciu HiveQL
* Magazyn obiektów blob Azure
* Tabela platformy Azure
* Usługa Azure SQL database lub SQL Server na maszynie Wirtualnej platformy Azure
* Baza danych programu SQL Server w środowisku lokalnym
* Dostawca, obecnie OData strumieniowych źródeł danych
* Azure Cosmos DB

Aby uzyskać dostęp do źródeł danych w trybie online w eksperymencie Studio, należy dodać [importu danych] [ import-data] moduł usługi, wybierz **źródła danych**, a następnie podaj parametrów wymaganych do uzyskania dostępu dane. Źródła danych online, które są obsługiwane, są wymienione w poniższej tabeli. Ta tabela zawiera podsumowanie, formaty plików, które są obsługiwane i parametry, które są używane do uzyskania dostępu do danych.

Należy zauważyć, że ponieważ te dane szkoleniowe odbywa się po uruchomieniu eksperymentu, jest on dostępny tylko w tym eksperymencie. Natomiast danych przechowywanych w module zestawu danych są dostępne dla każdego doświadczenia w obszarze roboczym.

> [!IMPORTANT]
> Obecnie [importu danych] [ import-data] i [Eksport danych] [ export-data] moduły mogą odczytywać i zapisywać dane tylko z usługi Azure storage, utworzony za pomocą klasycznego model wdrażania. Innymi słowy nowy typ konta usługi Azure Blob Storage oferuje Warstwa dostępu gorąca lub chłodna Warstwa dostępu nie jest jeszcze obsługiwane. 
> 
> Ogólnie rzecz biorąc, wszystkie konta magazynu platformy Azure może być utworzone przed tej opcji usługa stały się dostępne nie powinny zależeć. 
> Jeśli potrzebujesz utworzyć nowe konto, wybierz **klasycznego** wdrożenia modelu, lub użyj usługi Resource manager i wybierz **ogólnego przeznaczenia** zamiast **magazynu obiektów Blob** dla  **Rodzaj konta**. 
> 
> Aby uzyskać więcej informacji, zobacz [usługi Azure Blob Storage: warstwy magazynu gorącego i chłodnego](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Obsługiwane źródła danych online
Usługa Azure Machine Learning **importu danych** Moduł obsługuje następujące źródła danych:

| Źródło danych | Opis | Parametry |
| --- | --- | --- |
| Adres URL sieci Web za pośrednictwem protokołu HTTP |Odczytuje dane w wartości rozdzielanych przecinkami (CSV), wartości rozdzielane tabulatorami (TSV), relacja atrybutu pliku format (ARFF) i formatów pomocy technicznej wektor maszyny (SVM światła) z adres URL sieci web, która korzysta z protokołu HTTP |<b>Adres URL</b>: Określa pełną nazwę pliku, w tym adres URL witryny i nazwę pliku z dowolnym rozszerzeniem. <br/><br/><b>Format danych</b>: Określa jeden z obsługiwanych danych formaty: CSV, TSV, ARFF lub SVM światła. Jeśli dane mają wiersz nagłówka, służy do przypisywania nazw kolumn. |
| Hadoop/HDFS |Odczytuje dane z rozproszonego magazynu na platformie Hadoop. Należy określić dane, które chcesz, aby za pomocą HiveQL, język zapytań przypominający SQL. Można również HiveQL do agregowania danych i wykonywania danych, filtrowania, aby dodać dane do usługi Machine Learning Studio. |<b>Zapytanie bazy danych programu hive</b>: Określa zapytania programu Hive służącego do generowania danych.<br/><br/><b>Identyfikator URI serwera HCatalog </b> : określono nazwę klastra przy użyciu formatu  *&lt;nazwy klastra&gt;. azurehdinsight.net.*<br/><br/><b>Nazwa konta użytkownika usługi Hadoop</b>: Określa nazwę konta użytkownika usługi Hadoop, używane do inicjowania obsługi klastra.<br/><br/><b>Hasło konta użytkownika usługi Hadoop</b> : Określa poświadczenia używane podczas aprowizowania klastra. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów usługi Hadoop w HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Lokalizacja danych wyjściowych</b>: Określa, czy dane są przechowywane w Rozproszony system plików Hadoop (HDFS) lub na platformie Azure. <br/><ul>Jeśli przechowujesz dane wyjściowe w systemie plików HDFS, należy określić identyfikator URI serwera systemu plików HDFS. (Należy użyć nazwy klastra HDInsight bez prefiksu HTTPS://). <br/><br/>Jeśli dane wyjściowe są przechowywane na platformie Azure, należy określić nazwę konta usługi Azure storage, klucz dostępu do magazynu i nazwa kontenera magazynu.</ul> |
| Baza danych SQL |Odczytuje dane są przechowywane w bazie danych Azure SQL lub w bazie danych programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure. |<b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym działa baza danych.<br/><ul>W przypadku usługi Azure SQL Database, wprowadź nazwę serwera, który jest generowany. Zwykle ma on postać  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>W przypadku programu SQL server hostowanych na maszynie wirtualnej platformy Azure wprowadź *tcp:&lt;nazwę DNS maszyny wirtualnej&gt;, 1433*</ul><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Hasło konta użytkownika serwera</b>: Określa hasło dla konta użytkownika.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL, która opisuje dane chcesz odczytać. |
| Lokalna baza danych SQL |Odczytuje dane, która jest przechowywana w bazie danych SQL w środowisku lokalnym. |<b>Brama danych</b>: Określa nazwę bramy zarządzania danymi, które są zainstalowane na komputerze, na którym ją ma dostęp do bazy danych programu SQL Server. Aby uzyskać informacji na temat konfigurowania bramy, zobacz [wykonaj advanced analytics z usługą Azure Machine Learning przy użyciu danych z programu SQL server w środowisku lokalnym](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym działa baza danych.<br/><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Nazwa użytkownika i hasło</b>: kliknij <b>wprowadź wartości</b> o podanie poświadczeń bazy danych. Można użyć zintegrowanego uwierzytelniania Windows lub uwierzytelniania programu SQL Server, w zależności od sposobu skonfigurowania na lokalnym serwerze SQL Server.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL, która opisuje dane chcesz odczytać. |
| Tabela platformy Azure |Odczytuje dane z usługi tabel w usłudze Azure Storage.<br/><br/>Jeśli odczyt dużej ilości danych z rzadko, należy użyć usługę Azure Table Service. Zapewnia elastyczny i nierelacyjnych (NoSQL), rozwiązanie do magazynowania wysoce skalowalnych, niedrogich i o wysokiej dostępności. |Opcje w **importu danych** zmianie w zależności od tego, czy dostęp do informacji publicznych lub konto magazynu prywatnego, które wymaga poświadczeń logowania. Jest to określane przez <b>typ uwierzytelniania</b> który może mieć wartość "PublicOrSAS" lub "Account", z których każda ma swój własny zestaw parametrów. <br/><br/><b>Publiczne lub sygnatury dostępu współdzielonego (SAS) identyfikator URI</b>: parametry są:<br/><br/><ul><b>Identyfikator URI tabeli</b>: Określa publiczną lub adres URL sygnatury dostępu Współdzielonego dla tabeli.<br/><br/><b>Określa wiersze do skanowania w poszukiwaniu nazwy właściwości</b>: wartości są <i>TopN</i> skanowanie określoną liczbę wierszy, lub <i>ScanAll</i> można pobrać wszystkie wiersze w tabeli. <br/><br/>Jeśli dane są jednorodne i przewidywalne, zalecane jest wybranie *TopN* i wprowadzić numer N. W przypadku dużych tabel może to spowodować szybsze czasy odczytu.<br/><br/>Jeśli dane są skonstruowane z zestawy właściwości, które różnią się zależnie od głębokości i położenie tabeli, wybierz opcję *ScanAll* opcję, aby skanować wszystkie wiersze. Dzięki temu integralności wynikowy właściwości i metadanych konwersji.<br/><br/></ul><b>Konto magazynu prywatnego</b>: parametry są: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, które zawiera tabelę do odczytu.<br/><br/><b>Klucz konta</b>: Określa klucz magazynu skojarzonego z kontem.<br/><br/><b>Nazwa tabeli</b> : Określa nazwę tabeli, która zawiera dane do odczytu.<br/><br/><b>Wiersze do skanowania w poszukiwaniu nazwy właściwości</b>: wartości są <i>TopN</i> skanowanie określoną liczbę wierszy, lub <i>ScanAll</i> można pobrać wszystkie wiersze w tabeli.<br/><br/>Jeśli dane są jednorodne i przewidywalne, firma Microsoft zaleca wybranie *TopN* i wprowadzić numer N. W przypadku dużych tabel może to spowodować szybsze czasy odczytu.<br/><br/>Jeśli dane są skonstruowane z zestawy właściwości, które różnią się zależnie od głębokości i położenie tabeli, wybierz opcję *ScanAll* opcję, aby skanować wszystkie wiersze. Dzięki temu integralności wynikowy właściwości i metadanych konwersji.<br/><br/> |
| Azure Blob Storage |Odczytuje dane przechowywane w usłudze obiektów Blob w usłudze Azure Storage, w tym obrazów, tekstu bez struktury lub dane binarne.<br/><br/>Można użyć usługi obiektów Blob, publicznie ujawniać dane lub prywatnie przechowywać dane aplikacji. Uzyskiwać dostęp do danych z dowolnego miejsca przy użyciu połączeń HTTP lub HTTPS. |Opcje w **importu danych** zmiana modułu, w zależności od tego, czy dostęp do informacji publicznych lub konto magazynu prywatnego, które wymaga poświadczeń logowania. Jest to określane przez <b>typ uwierzytelniania</b> który może mieć wartość "PublicOrSAS" lub "Konto".<br/><br/><b>Publiczne lub sygnatury dostępu współdzielonego (SAS) identyfikator URI</b>: parametry są:<br/><br/><ul><b>Identyfikator URI</b>: Określa publiczną lub adres URL sygnatury dostępu Współdzielonego dla obiektu blob magazynu.<br/><br/><b>Format pliku</b>: Określa format danych w usłudze obiektów Blob. Obsługiwane formaty to formatu CSV, TSV i ARFF.<br/><br/></ul><b>Konto magazynu prywatnego</b>: parametry są: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, które zawiera obiekt blob chcesz odczytać.<br/><br/><b>Klucz konta</b>: Określa klucz magazynu skojarzonego z kontem.<br/><br/><b>Ścieżka do kontenera, katalogu lub obiektu blob </b> : Określa nazwę obiektu blob, który zawiera dane do odczytu.<br/><br/><b>Format pliku obiektu blob</b>: Określa format danych w usłudze obiektów blob. Formaty obsługiwane dane są CSV, TSV, ARFF, CSV określone kodowanie i program Excel. <br/><br/><ul>Jeśli jest w formacie CSV lub TSV, pamiętaj wskazać, czy ten plik zawiera wiersz nagłówka.<br/><br/>Opcja Excel można odczytać danych ze skoroszytów programu Excel. W <i>format danych programu Excel</i> opcji, określ, czy dane znajdują się w zakresie arkusza programu Excel lub w tabeli programu Excel. W <i>arkusza programu Excel lub tabeli osadzone </i>opcji, należy określić nazwę arkusza lub tabeli, która ma zostać odczytany.</ul><br/> |
| Dostawca strumieniowych źródeł danych |Odczytuje dane z obsługiwanego dostawcy źródła danych. Obecnie jest obsługiwany tylko format Open Data Protocol (OData). |<b>Typ zawartości danych</b>: Określa OData format.<br/><br/><b>Źródłowy adres URL</b>: Określa pełny adres URL strumieniowego źródła danych. <br/>Na przykład następujący adres URL odczytuje z przykładowej bazy danych Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie usługi sieci web Azure ML używających modułów importu i eksportu danych](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
