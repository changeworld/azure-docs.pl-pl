---
title: Omówienie usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Zrozumienie, jakie Gen1 magazynu Data Lake jest (wcześniej znana jako Azure Data Lake Store) i wartość, która jest lepsza od innych magazynów danych
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 4dff8f4ff9fc324d48391c0399677b64824493c6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036455"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Omówienie usługi Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Store to repozytorium w hiperskali obsługujące całe przedsiębiorstwo na potrzeby obciążeń analizy dużych ilości danych (big data). Usługa Azure Data Lake umożliwia przechwytywanie danych dowolnego typu, o dowolnym rozmiarze i szybkości wprowadzania oraz przechowywanie ich w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

> [!TIP]
> Skorzystaj ze [ścieżki szkoleniowej usługi Data Lake Store](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/), aby rozpocząć eksplorowanie usługi Azure Data Lake Store.
> 
> 

Do usługi Azure Data Lake Store można uzyskać dostęp z platformy Hadoop (dostępnej z klastrem usługi HDInsight) przy użyciu zgodnych interfejsów API REST WebHDFS. Usługa została specjalnie zaprojektowana w celu umożliwienia analizy przechowywanych danych i dostosowana pod kątem wydajności dla scenariuszy analizy danych. Natychmiast po wdrożeniu zapewnia wszystkie funkcje klasy korporacyjnej — zabezpieczeń, zarządzania, skalowalności, niezawodności i dostępności — niezbędne do obsługi rzeczywistych przypadków użycia w przedsiębiorstwie.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Poniżej przedstawiono niektóre z kluczowych możliwości usługi Azure Data Lake.

### <a name="built-for-hadoop"></a>Stworzona dla platformy Hadoop
Usługa Azure Data Lake Store jest systemem plików Apache Hadoop zgodnym z rozproszonym systemem plików Hadoop (HDFS, Hadoop Distributed File System) i działa z ekosystemem Hadoop.  Istniejące aplikacje lub usługi HDInsight korzystające z interfejsu API WebHDFS można łatwo zintegrować z usługą Data Lake Store. Usługa Data Lake Store udostępnia również interfejs REST zgodny z WebHDFS dla aplikacji.

Dane przechowywane w usłudze Data Lake Store można łatwo analizować za pomocą platform analitycznych Hadoop, takich jak MapReduce lub Hive. Klastry usługi Microsoft Azure HDInsight można zainicjować i skonfigurować tak, aby miały bezpośredni dostęp do danych przechowywanych w usłudze Data Lake Store.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki
Usługa Azure Data Lake Store zapewnia nieograniczony magazyn i nadaje się do przechowywania różnorodnych danych na potrzeby analiz. Nie nakłada żadnych limitów dotyczących rozmiarów kont, rozmiarów plików lub ilości danych, które mogą być przechowywane w usłudze Data Lake. Pojedyncze pliki mogą mieć rozmiar od kilobajta do petabajtów, co czyni usługę doskonałym wyborem do przechowywania danych dowolnego typu. Dane są przechowywane trwale dzięki tworzeniu wielu kopii i nie ma żadnego limitu czasu przechowywania danych w usłudze Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Wydajność dostosowana na potrzeby analizy danych big data
Usługa Azure Data Lake Store została stworzona z myślą o obsłudze działania dużych systemów analitycznych, które wymagają ogromnej przepływności w celu odpytywania i analizowania dużych ilości danych. Usługa Data Lake rozmieszcza części pliku na wielu serwerach magazynu. Zwiększa to przepływność odczytu podczas odczytywania pliku równolegle w celu wykonywania analizy danych.

### <a name="enterprise-ready-highly-available-and-secure"></a>Gotowa do użycia w przedsiębiorstwie: wysoce dostępna i bezpieczna
Usługa Azure Data Lake Store zapewnia dostępność i niezawodność zgodne ze standardami branżowymi. Dane są przechowywane trwale dzięki wykonywaniu nadmiarowych kopii, aby zapewnić ochronę danych na wypadek nieoczekiwanych awarii. Przedsiębiorstwa mogą wykorzystywać usługę Azure Data Lake w swoich rozwiązaniach jako istotny element istniejącej platformy danych.

Usługa Data Lake Store zapewnia również zabezpieczenia klasy korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake Store](#DataLakeStoreSecurity).

### <a name="all-data"></a>Wszystkie dane
Usługa Azure Data Lake Store może przechowywać wszystkie dane w ich natywnym formacie, bez wymagania jakiegokolwiek uprzedniego przekształcania danych. Usługa Data Lake Store nie wymaga zdefiniowania schematu przed załadowaniem danych, pozostawiając poszczególnym środowiskom analitycznym interpretację danych i zdefiniowanie schematu w czasie analizy. Dzięki możliwości przechowywania plików dowolnych rozmiarów i w dowolnych formatach usługa Data Lake Store obsługuje dane z pełną lub częściową strukturą i bez struktury.

Kontenerami danych usługi Azure Data Lake Store są zasadniczo foldery i pliki. Przechowywane dane można obsługiwać przy użyciu zestawów SDK, witryny Azure Portal i programu Azure Powershell. Dopóki dane są umieszczane w magazynie usługi za pomocą tych interfejsów i przy użyciu odpowiednich kontenerów, można w niej przechowywać dane dowolnego typu. Usługa Data Lake Store nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych, które przechowuje.

## <a name="DataLakeStoreSecurity"></a>Zabezpieczanie danych w usłudze Azure Data Lake Store
Usługa Azure Data Lake Store używa usługi Azure Active Directory na potrzeby uwierzytelniania oraz list kontroli dostępu (ACL, access control list) w celu zarządzania dostępem do danych.

| Cecha | Opis |
| --- | --- |
| Authentication |Usługa Azure Data Lake Store integruje się z usługą Azure Active Directory (AAD) na potrzeby zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w usłudze Azure Data Lake Store. Dzięki tej integracji usługa Azure Data Lake korzysta ze wszystkich funkcji usługi AAD, w tym uwierzytelniania wieloskładnikowego, dostępu warunkowego, kontroli dostępu opartej na rolach, monitorowania użycia aplikacji, monitorowania zabezpieczeń, alertów zabezpieczeń itd. Usługa Azure Data Lake obsługuje protokół OAuth 2.0 na potrzeby uwierzytelniania przy użyciu interfejsu REST. Zobacz [Data Lake Store authentication](data-lakes-store-authentication-using-azure-active-directory.md) (Uwierzytelnianie w usłudze Data Lake Store)|
| Kontrola dostępu |Usługa Azure Data Lake Store zapewnia kontrolę dostępu dzięki obsłudze uprawnień POSIX udostępnianych przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat sposobu działania list kontroli dostępu w kontekście usługi Data Lake Store, zobacz [Kontrola dostępu w usłudze Data Lake Store](data-lake-store-access-control.md). |
| Szyfrowanie |Usługa Data Lake Store umożliwia także szyfrowanie danych przechowywanych w ramach konta. Ustawienia szyfrowania możesz określić podczas tworzenia konta usługi Data Lake Store. Możesz wybrać szyfrowanie danych lub jego brak. Aby uzyskać więcej informacji, zobacz [Szyfrowanie w usłudze Data Lake Store](data-lake-store-encryption.md). Aby uzyskać instrukcje dotyczące sposobu zapewnienia konfiguracji odnoszącej się do szyfrowania, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Store za pośrednictwem witryny Azure Portal](data-lake-store-get-started-portal.md). |

Dowiedz się więcej na temat zabezpieczania danych w usłudze Data Lake Store. Skorzystaj z poniższych linków.

* Aby uzyskać instrukcje, jak zabezpieczyć dane w usłudze Data Lake Store, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake Store](data-lake-store-secure-data.md).
* Wolisz filmy wideo? [Obejrzyj ten film](https://mix.office.com/watch/1q2mgzh9nn5lx), aby dowiedzieć się, jak zabezpieczyć dane przechowywane w usłudze Data Lake Store.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Aplikacje zgodne z usługą Azure Data Lake Store
Usługa Azure Data Lake Store jest zgodna z większością składników typu „open source” w ekosystemie Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure. Wszystko to sprawia, że usługa Data Lake Store jest doskonałą opcją dla potrzeb magazynu danych. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o tym, jak usługa Data Lake Store może być używana ze składnikami typu „open source” i innymi usługami Azure.

* Zobacz [Aplikacje i usługi zgodne z usługą Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md), aby uzyskać listę aplikacji typu „open source” współpracujących z usługą Azure Data Lake Store.
* Zobacz [Integracja z innymi usługami Azure](data-lake-store-integrate-with-other-services.md), aby zrozumieć możliwości korzystania z usługi Data Lake Store w połączeniu z innymi usługami Azure w celu realizacji szerszego zakresu scenariuszy.
* Zobacz [Scenariusze korzystania z usługi Data Lake Store](data-lake-store-data-scenarios.md), aby dowiedzieć się, jak korzystać z usługi Data Lake Store w scenariuszach takich jak odbieranie danych, przetwarzanie danych, pobieranie danych i wizualizacja danych.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Co to jest system plików usługi Azure Data Lake Store (adl://)?
Za pomocą nowego systemu plików AzureDataLakeFilesystem (adl://) można uzyskiwać dostęp do usługi Data Lake Store w środowiskach Hadoop (dostępnych z klastrem usługi HDInsight). Aplikacje i usługi używające systemu plików adl:// mogą skorzystać z dalszej optymalizacji wydajności, która nie jest obecnie dostępna w systemie plików WebHDFS. W związku z tym usługa Data Lake Store zapewnia elastyczność — można uzyskać najlepszą wydajność dzięki używaniu systemu plików adl:// (zalecana opcja) lub nadal obsługiwać istniejący kod bezpośrednio przy użyciu interfejsu API WebHDFS. Usługa Azure HDInsight w pełni wykorzystuje system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność usługi Data Lake Store.

Dostęp do danych w usłudze Data Lake Store można uzyskać przy użyciu adresu `adl://<data_lake_store_name>.azuredatalakestore.net`. Aby uzyskać więcej informacji na temat sposobu uzyskiwania dostępu do danych w usłudze Data Lake Store, zobacz [Wyświetlanie właściwości przechowywanych danych](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Data Lake Store przy użyciu portalu Azure](data-lake-store-get-started-portal.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Store z użyciem zestawu SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)