---
title: Sterownik systemu plików obiektów Blob platformy Azure dla usługi Azure Data Lake Storage Gen2
description: Sterownik ABFS Hadoop Filesystem
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3db039d39ef532ea51143dc9cbdb6bd5f29d6225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75970274"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Sterownik systemu plików obiektów Blob azure (ABFS): dedykowany sterownik usługi Azure Storage dla usługi Hadoop

Jedną z podstawowych metod dostępu do danych w usłudze Azure Data Lake Storage Gen2 jest za pośrednictwem [systemu plików Hadoop FileSystem.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html) Data Lake Storage Gen2 umożliwia użytkownikom usługi Azure Blob Storage dostęp do `ABFS`nowego sterownika, sterownika systemu plików obiektów Blob platformy Azure lub . ABFS jest częścią Apache Hadoop i jest zawarty w wielu komercyjnych dystrybucjach Hadoop. Za pomocą tego sterownika wiele aplikacji i struktur można uzyskać dostęp do danych w usłudze Azure Blob Storage bez kodu jawnie odwołując się do data lake storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Wcześniejsze możliwości: sterownik obiektów blob usługi Windows Azure Storage

Sterownik obiektów blob usługi Windows Azure Storage lub [sterownik WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) dostarczył oryginalnej obsługi usługi Azure Blob Storage. Ten sterownik wykonał złożone zadanie mapowania semantyki systemu plików (zgodnie z wymaganiami interfejsu Hadoop FileSystem) do interfejsu stylu magazynu obiektów udostępnianego przez usługę Azure Blob Storage. Ten sterownik nadal obsługuje ten model, zapewniając wysokiej wydajności dostęp do danych przechowywanych w obiektach blob, ale zawiera znaczną ilość kodu wykonującego to mapowanie, co utrudnia obsługę. Ponadto niektóre operacje, takie jak [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) i [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) po zastosowaniu do katalogów, wymagają od sterownika wykonywania ogromnej liczby operacji (z powodu braku obsługi katalogów w magazynach obiektów), co często prowadzi do obniżenia wydajności. Sterownik ABFS został zaprojektowany, aby przezwyciężyć nieodłączne braki WASB.

## <a name="the-azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów Blob platformy Azure

[Interfejs usługi Azure Data Lake Storage REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) został zaprojektowany do obsługi semantyki systemu plików za pomocą usługi Azure Blob Storage. Biorąc pod uwagę, że System plików Hadoop jest również przeznaczony do obsługi tej samej semantyki, nie ma wymogu dla złożonego mapowania w sterowniku. W związku z tym sterownik systemu plików obiektów Blob azure (lub ABFS) jest zwykłym podkładką klienta dla interfejsu API REST.

Istnieją jednak pewne funkcje, które sterownik musi nadal wykonywać:

### <a name="uri-scheme-to-reference-data"></a>Schemat URI do danych referencyjnych

Zgodnie z innymi implementacjami systemu plików w ramach programu Hadoop sterownik ABFS definiuje własny schemat URI, dzięki czemu zasoby (katalogi i pliki) mogą być wyraźnie rozwiązane. Schemat identyfikatorów URI jest udokumentowany w [programie Use the Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md). Struktura identyfikatora URI jest:`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Przy użyciu powyższego formatu URI, standardowe narzędzia i struktury Hadoop mogą służyć do odwoływania się do tych zasobów:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Wewnętrznie sterownik ABFS tłumaczy zasoby określone w identyfikatorze URI na pliki i katalogi i wywołuje interfejs API REST usługi Azure Data Lake Storage z tymi odwołaniami.

### <a name="authentication"></a>Uwierzytelnianie

Sterownik ABFS obsługuje dwie formy uwierzytelniania, dzięki czemu aplikacja Hadoop może bezpiecznie uzyskiwać dostęp do zasobów zawartych w koncie obsługującym usługę Data Lake Storage Gen2. Szczegółowe informacje na temat dostępnych schematów uwierzytelniania znajdują się w [przewodniku po zabezpieczeniach usługi Azure Storage.](security-recommendations.md) Oto one:

- **Klucz udostępniony:** Umożliwia to użytkownikom dostęp do wszystkich zasobów na koncie. Klucz jest szyfrowany i przechowywany w konfiguracji Hadoop.

- **Token nośnego usługi Azure Active Directory OAuth:** Tokeny nośnika usługi Azure AD są nabywane i odświeżane przez sterownik przy użyciu tożsamości użytkownika końcowego lub skonfigurowanego podmiotu zabezpieczeń usługi. Przy użyciu tego modelu uwierzytelniania, cały dostęp jest autoryzowany na podstawie połączenia przy użyciu tożsamości skojarzonej z dostarczonym tokenem i oceniane względem przypisanej listy kontroli dostępu POSIX (ACL).

   > [!NOTE]
   > Usługa Azure Data Lake Storage Gen2 obsługuje tylko punkty końcowe usługi Azure AD w wersji 1.0.

### <a name="configuration"></a>Konfigurowanie

Cała konfiguracja sterownika ABFS <code>core-site.xml</code> jest przechowywana w pliku konfiguracyjnym. W przypadku dystrybucji Hadoop z [ambari](https://ambari.apache.org/)konfiguracją można również zarządzać za pomocą portalu internetowego lub interfejsu API Ambari REST.

Szczegóły wszystkich obsługiwanych wpisów konfiguracyjnych są określone w [dokumentacji Official Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Dokumentacja Hadoop

Sterownik ABFS jest w pełni udokumentowany w [oficjalnej dokumentacji Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie klastra usług Databricks platformy Azure](./data-lake-storage-quickstart-create-databricks-account.md)
- [Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md)
