---
title: Omówienie usługi Azure Data Lake Store | Microsoft Docs
description: Dowiedz się, czym jest usługa Azure Data Lake Store i dlaczego jest lepsza od innych magazynów danych
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b3475057-9427-4492-a3af-25a802a23a79
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 111906bc9e7e85e30a879d7bacff37edb4b204ac
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="overview-of-azure-data-lake-store"></a>Omówienie usługi Azure Data Lake Store
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

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Jak zacząć korzystać z usługi Azure Data Lake Store?
Zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md), aby dowiedzieć się, jak aprowizować usługę Data Lake Store za pomocą witryny Azure Portal. Po aprowizowaniu usługi Azure Data Lake możesz nauczyć się, jak korzystać z rozwiązań do obsługi danych big data, takich jak Azure Data Lake Analytics lub Azure HDInsight, z usługą Data Lake Store. Możesz również utworzyć aplikację .NET w celu utworzenia konta usługi Azure Data Lake Store i wykonywania operacji, takich jak przekazywanie danych, pobieranie danych itp.

* [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Store z użyciem zestawu SDK .NET](data-lake-store-get-started-net-sdk.md)

## <a name="data-lake-store-videos"></a>Filmy wideo dotyczące usługi Data Lake Store
Jeśli wolisz obejrzeć filmy wideo, aby nauczyć się korzystać z usługi, usługa Data Lake Store udostępnia filmy wideo dotyczące różnych funkcji.

* [Tworzenie konta usługi Azure Data Lake Store](https://mix.office.com/watch/1k1cycy4l4gen)
* [Zarządzanie danymi w usłudze Azure Data Lake Store za pomocą Eksploratora danych](https://mix.office.com/watch/icletrxrh6pc)
* [Łączenie usługi Azure Data Lake Analytics z usługą Azure Data Lake Store](https://mix.office.com/watch/qwji0dc9rx9k)
* [Uzyskiwanie dostępu do usługi Azure Data Lake Store za pomocą usługi Data Lake Analytics](https://mix.office.com/watch/1n0s45up381a8)
* [Łączenie usługi Azure HDInsight z usługą Azure Data Lake Store](https://mix.office.com/watch/l93xri2yhtp2)
* [Uzyskiwanie dostępu do usługi Azure Data Lake Store za pomocą technologii Hive i Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Kopiowanie danych do i z usługi Azure Data Lake Store za pomocą narzędzia DistCp (Distributed Copy) platformy Hadoop](https://mix.office.com/watch/1liuojvdx6sie)
* [Przenoszenie danych między źródłami relacyjnymi i usługą Azure Data Lake Store z wykorzystaniem narzędzia Apache Sqoop](https://mix.office.com/watch/1butcdjxmu114)
* [Organizowanie danych za pomocą usługi Azure Data Factory dla usługi Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Zabezpieczanie danych w usłudze Azure Data Lake Store](https://mix.office.com/watch/1q2mgzh9nn5lx)

