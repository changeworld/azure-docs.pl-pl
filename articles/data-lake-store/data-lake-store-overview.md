---
title: Co to jest usługa Azure Data Lake Storage Gen1? | Microsoft Docs
description: Omówienie usługi Data Lake Storage Gen1 (wcześniej znanego jako Azure Data Lake Store) i wartości, które zapewnia w innych magazynach danych
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118801"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Co to jest usługa Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Usługa Azure Data Lake Storage Gen1 to repozytorium hiperskali dla dużych zbiorów danych w całym przedsiębiorstwie. Usługa Azure Data Lake umożliwia przechwytywanie danych dowolnego typu, o dowolnym rozmiarze i szybkości wprowadzania oraz przechowywanie ich w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Dostęp do usługi Data Lake Storage Gen1 można uzyskać z usługi Hadoop (dostępnej w klastrze HDInsight) przy użyciu interfejsów API REST zgodnych ze standardem WebHDFS. Został zaprojektowany, aby umożliwić analizę przechowywanych danych i jest dostrojony pod kątem wydajności scenariuszy analizy danych. Data Lake Storage Gen1 zawiera wszystkie funkcje klasy korporacyjnej: bezpieczeństwo, łatwość zarządzania, skalowalność, niezawodność i dostępność.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Najważniejsze możliwości

Niektóre z kluczowych funkcji usługi Data Lake Storage Gen1 są następujące.

### <a name="built-for-hadoop"></a>Stworzona dla platformy Hadoop

Data Lake Storage Gen1 to system plików Apache Hadoop, który jest kompatybilny z Hadoop Distributed File System (HDFS) i współpracuje z ekosystemem Hadoop. Istniejące aplikacje lub usługi HDInsight korzystające z interfejsu API usługi WebHDFS można łatwo zintegrować z usługą Data Lake Storage Gen1. Data Lake Storage Gen1 udostępnia również interfejs REST zgodny z usługą WebHDFS dla aplikacji.

Można łatwo analizować dane przechowywane w umiaźnej pamięci masowej danych Gen1 przy użyciu struktur analitycznych Hadoop, takich jak MapReduce lub Hive. Można aprowizować klastry usługi Azure HDInsight i skonfigurować je do bezpośredniego dostępu do danych przechowywanych w usłudze Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczoną ilość miejsca do magazynowania i może przechowywać różne dane do analizy. Nie nakłada żadnych ograniczeń na rozmiary kont, rozmiary plików ani ilość danych, które mogą być przechowywane w uliczce danych. Poszczególne pliki mogą mieć rozmiar od kilobajtów do petabajtów. Dane są przechowywane trwale przez wykonanie wielu kopii. Nie ma limitu czasu, przez jaki dane mogą być przechowywane w jeziorze danych.

### <a name="performance-tuned-for-big-data-analytics"></a>Wydajność dostosowana na potrzeby analizy danych big data

Data Lake Storage Gen1 jest przeznaczony do uruchamiania dużych systemów analitycznych, które wymagają ogromnej przepływności do wykonywania zapytań i analizowania dużych ilości danych. Usługa Data Lake rozmieszcza części pliku na wielu serwerach magazynu. Zwiększa to przepływność odczytu podczas odczytywania pliku równolegle w celu wykonywania analizy danych.

### <a name="enterprise-ready-highly-available-and-secure"></a>Gotowość do pracy w przedsiębiorstwie: wysoka dostępna i bezpieczna

Data Lake Storage Gen1 zapewnia dostępność i niezawodność w standardzie branżowym. Dane są przechowywane trwale dzięki wykonywaniu nadmiarowych kopii, aby zapewnić ochronę danych na wypadek nieoczekiwanych awarii.

Data Lake Storage Gen1 zapewnia również bezpieczeństwo przechowywanech danych klasy korporacyjnej. Aby uzyskać więcej informacji, zobacz [zabezpieczanie danych w usłudze Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Wszystkie dane

Data Lake Storage Gen1 może przechowywać dowolne dane w formacie macierzystym, bez konieczności żadnych wcześniejszych przekształceń. Data Lake Storage Gen1 nie wymaga zdefiniowania schematu przed załadowaniem danych, pozostawiając go do poszczególnych struktur analitycznych do interpretacji danych i zdefiniować schemat w czasie analizy. Możliwość przechowywania plików o dowolnych rozmiarach i formatach umożliwia usługi Data Lake Storage Gen1 do obsługi danych strukturalnych, częściowo ustrukturyzowanych i nieustrukturyzowanych.

Kontenery Data Lake Storage Gen1 dla danych są zasadniczo folderami i plikami. Działasz na przechowywanych danych przy użyciu zestawów SDK, witryny Azure portal i usługi Azure Powershell. Jeśli umieścisz dane w magazynie przy użyciu tych interfejsów i przy użyciu odpowiednich kontenerów, można przechowywać dowolny typ danych. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych, które przechowuje.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Zabezpieczanie danych

Usługa Data Lake Storage Gen1 używa usługi Azure Active Directory (Azure AD) do uwierzytelniania i list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| Funkcja | Opis |
| --- | --- |
| Uwierzytelnianie |Usługa Data Lake Storage Gen1 integruje się z usługą Azure AD w celu zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w usłudze Data Lake Storage Gen1. Ze względu na integrację usługa Data Lake Storage Gen1 korzysta ze wszystkich funkcji usługi Azure AD, takich jak uwierzytelnianie wieloskładnikowe, dostęp warunkowy, kontrola dostępu oparta na rolach, monitorowanie użycia aplikacji, monitorowanie zabezpieczeń i alerty itd. Data Lake Storage Gen1 obsługuje protokół OAuth 2.0 do uwierzytelniania w interfejsie REST. Zobacz [Uwierzytelnianie gen1 magazynu magazynu danych](data-lakes-store-authentication-using-azure-active-directory.md).|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu, obsługując uprawnienia w stylu POSIX udostępniane przez protokół WebHDFS. Listy ACL można włączyć w folderze głównym, w podfolderach i w poszczególnych plikach. Aby uzyskać więcej informacji na temat działania list ACL w kontekście pamięci masowej Data Lake Storage Gen1, zobacz [Kontrola dostępu w programie Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 zapewnia również szyfrowanie danych przechowywanych na koncie. Ustawienia szyfrowania można określić podczas tworzenia konta Usługi Data Lake Storage Gen1. Możesz zdecydować się na szyfrowanie danych lub zdecydować się na brak szyfrowania. Aby uzyskać więcej informacji, zobacz [Szyfrowanie w pamięci masowej usługi Data Lake Gen1](data-lake-store-encryption.md). Aby uzyskać instrukcje dotyczące dostarczania konfiguracji związanej z szyfrowaniem, zobacz Wprowadzenie do [usługi Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md). |

Aby uzyskać instrukcje dotyczące zabezpieczania danych w usłudze Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Zgodność aplikacji

Data Lake Storage Gen1 jest kompatybilny z większością komponentów open source w ekosystemie Hadoop. Dobrze integruje się również z innymi usługami platformy Azure. Aby dowiedzieć się więcej o tym, jak używać usługi Data Lake Storage Gen1 ze składnikami open source i innymi usługami platformy Azure, skorzystaj z następujących łączy:

- Zobacz [Aplikacje i usługi zgodne z usługą Azure Data Lake Storage Gen1,](data-lake-store-compatible-oss-other-applications.md) aby uzyskać listę aplikacji typu open source interoperacyjnych z usługą Data Lake Storage Gen1.
- Zobacz [Integrowanie z innymi usługami platformy Azure,](data-lake-store-integrate-with-other-services.md) aby dowiedzieć się, jak korzystać z usługi Data Lake Storage Gen1 z innymi usługami platformy Azure, aby włączyć szerszy zakres scenariuszy.
- Zobacz [Scenariusze dotyczące korzystania z usługi Data Lake Storage Gen1,](data-lake-store-data-scenarios.md) aby dowiedzieć się, jak używać usługi Data Lake Storage Gen1 w scenariuszach, takich jak pozyskiwania danych, przetwarzania danych, pobierania danych i wizualizacji danych.

## <a name="data-lake-storage-gen1-file-system"></a>System plików Data Lake Storage Gen1

Data Lake Storage Gen1 można uzyskać za pośrednictwem systemu plików AzureDataLakeFilesystem (adl://) w środowiskach Hadoop (dostępne z klastrem HDInsight). Aplikacje i usługi korzystające z adl:// mogą korzystać z dalszych optymalizacji wydajności, które nie są obecnie dostępne w uprzeglądarce WebHDFS. W rezultacie data lake storage gen1 zapewnia elastyczność korzystania z najlepszej wydajności z zalecaną opcją używania adl:// lub utrzymania istniejącego kodu przez kontynuowanie bezpośredniego korzystania z interfejsu API systemu WebHDFS. Usługa Azure HDInsight w pełni wykorzystuje system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność w usłudze Data Lake Storage Gen1.

Dostęp do danych można uzyskać w `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`aplikacji Data Lake Storage Gen1 za pomocą programu . Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych w programie Data Lake Storage Gen1, zobacz [Wyświetlanie właściwości przechowywanych danych](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md)
- [Wprowadzenie do usługi Data Lake Storage Gen1 przy użyciu sdk .NET](data-lake-store-get-started-net-sdk.md)
- [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)