---
title: Przykłady usługi Azure Storage przy użyciu języka JavaScript | Dokumenty firmy Microsoft
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odnajdowanie przykładów wprowadzenie do obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienta magazynu JavaScript/Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/19/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 1d6b4d37a3d7c4accc3e407fb6cf841600ac1c16
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77485849"
---
# <a name="azure-storage-samples-using-v12-javascript-client-libraries"></a>Przykłady usługi Azure Storage przy użyciu bibliotek klienckich JavaScript w wersji 12

Poniższe tabele zawierają przegląd naszego repozytorium przykładów i scenariuszy omówionych w każdej próbce. Kliknij łącza, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

> [!NOTE]
> Te przykłady używają najnowszej biblioteki JavaScript języka Azure Storage w wersji 12. W przypadku starszego kodu w wersji 11 zobacz [Wprowadzenie do usługi Azure Blob w pliku Node.js](https://github.com/Azure-Samples/storage-blob-node-getting-started) w repozytorium GitHub.

## <a name="blob-samples"></a>Przykłady obiektów blob

### <a name="authentication"></a>Uwierzytelnianie

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu ciągu połączenia](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/withConnString.js#L14)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu ciągu połączenia sygnatury dostępu Współdzielonego](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/withConnString.js#L14)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/sharedKeyCred.js#L5)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu anonymouscredential](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/anonymousCred.js#L18)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnianie za pomocą usługi Azure Active Directory](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/azureAdAuth.js#L47)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu serwera proxy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/proxyAuth.js#L28)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Łączenie za pomocą niestandardowego potoku](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/customPipeline.js#L26)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob service

:::row:::
   :::column span="2":::
      [Tworzenie klienta usługi obiektów blob przy użyciu adresu URL sygnatury dostępu Współdzielonego](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L39)
   :::column-end:::
:::row-end:::

### <a name="container"></a>Kontener

:::row:::
   :::column span="":::
      [Tworzenie kontenera](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L53)
   :::column-end:::
   :::column span="":::
      [Tworzenie kontenera przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L38)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wyświetlanie listy kontenerów](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L48)
   :::column-end:::
   :::column span="":::
      [Lista kontenerów przy użyciu iteratora](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-containers.js#L28)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Lista kontenerów według strony](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-containers.js#L53)
   :::column-end:::
   :::column span="":::
      [Usuwanie kontenera](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L82)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Obiekt blob

:::row:::
   :::column span="":::
      [Tworzenie obiektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L60)
   :::column-end:::
   :::column span="":::
      [Wyświetlanie listy obiektów blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L67)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobieranie obiektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L73)
   :::column-end:::
   :::column span="":::
      [Lista obiektów blob przy użyciu iteratora](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs.js#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Lista obiektów blob według strony](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs.js#L66)
   :::column-end:::
   :::column span="":::
      [Lista obiektów blob według hierarchii](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs-hierarchy.js#L70)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wyświetlanie listy obiektów blob bez użycia await](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-without-await.js#L42)
   :::column-end:::
   :::column span="":::
      [Tworzenie migawki obiektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobieranie migawki obiektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L61)
   :::column-end:::
   :::column span="":::
      [Równoległe przekazywanie strumienia do obiektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Obiekt blob bloku pobierania równoległego](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L99)
   :::column-end:::
   :::column span="":::
      [Ustawianie warstwy dostępu na obiekcie blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L118)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów

:::row:::
   :::column span="2":::
      [Wyzwalanie nieodwracalnego błędu przy użyciu klienta kontenera](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/errorsAndResponses.js#L33)
   :::column-end:::
:::row-end:::


## <a name="data-lake-storage-gen2-samples"></a>Próbki Data Lake Storage Gen2

:::row:::
   :::column span="":::
      [Create a Data Lake service client](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L36)
   :::column-end:::
   :::column span="":::
      [Tworzenie systemu plików](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L47)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Lista systemów plików](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L42)
   :::column-end:::
   :::column span="":::
      [Tworzenie pliku](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L54)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wyświetlanie ścieżek w systemie plików](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L63)
   :::column-end:::
   :::column span="":::
      [Pobieranie pliku](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L69)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Usuwanie systemu plików](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L78)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Przykłady usług Azure Files

### <a name="authentication"></a>Uwierzytelnianie

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu ciągu połączenia](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/withConnString.js)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/sharedKeyCred.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu anonymouscredential](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/anonymousCred.js)
   :::column-end:::
   :::column span="":::
      [Łączenie za pomocą niestandardowego potoku](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/customPipeline.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Łączenie się za pomocą serwera proxy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/proxyAuth.js)
   :::column-end:::
:::row-end:::

### <a name="share"></a>Udostępnij

:::row:::
   :::column span="":::
      [Tworzenie udziału](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L48)
   :::column-end:::
   :::column span="":::
      [Lista udziałów](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-shares.js#L27)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Lista udziałów według strony](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-shares.js#L51)
   :::column-end:::
   :::column span="":::
      [Usuwanie udziału](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L104)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Katalog

:::row:::
   :::column span="":::
      [Tworzenie katalogu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L54)
   :::column-end:::
   :::column span="":::
      [Lista plików i katalogów](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-files-and-directories.js#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Lista plików i katalogów według strony](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-files-and-directories.js#L90)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Plik

:::row:::
   :::column span="":::
      [Równoległe przekazywanie pliku](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L65)
   :::column-end:::
   :::column span="":::
      [Równoległe przekazywanie czytelnego strumienia](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobieranie równoległe pliku](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L93)
   :::column-end:::
   :::column span="":::
      [Uchwyty plików listy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-handles.js#L43)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Lista uchwytów plików według strony](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-handles.js#L79)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Próbki kolejek

### <a name="authentication"></a>Uwierzytelnianie

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu ciągu połączenia](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/withConnString.js)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/sharedKeyCred.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu anonymouscredential](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/anonymousCred.js)
   :::column-end:::
   :::column span="":::
      [Łączenie za pomocą niestandardowego potoku](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/customPipeline.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Łączenie się za pomocą serwera proxy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/proxyAuth.js)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie za pomocą usługi Azure Active Directory](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/azureAdAuth.js)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>Usługa kolejki

:::row:::
   :::column span="2":::
      [Tworzenie klienta usługi kolejki](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L42)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Kolejka

:::row:::
   :::column span="":::
      [Tworzenie nowej kolejki](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L54)
   :::column-end:::
   :::column span="":::
      [Kolejki listy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/iterators.js#L27)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Lista kolejek według strony](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/iterators.js#L51)
   :::column-end:::
   :::column span="":::
      [Usuwanie kolejki](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L89)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Komunikat

:::row:::
   :::column span="":::
      [Wysyłanie wiadomości do kolejki](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L62)
   :::column-end:::
   :::column span="":::
      [Wgląd w wiadomości](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Odbieranie komunikatów](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L76)
   :::column-end:::
   :::column span="":::
      [Usuwanie wiadomości](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L80)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Przykłady tabeli (w wersji 11)

:::row:::
   :::column span="":::
      [Encje partii](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87)
   :::column-end:::
   :::column span="":::
      [Tworzenie tabeli](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Usuwanie encji/tabeli](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67)
   :::column-end:::
   :::column span="":::
      [Wstawianie/scalanie/zamienianie elementu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Listy tabel](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63)
   :::column-end:::
   :::column span="":::
      [Encje kwerend](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wykonywanie zapytań dotyczących tabel](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140)
   :::column-end:::
   :::column span="":::
      [Kwerenda zakres](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Sygnatura dostępu współdzielonego](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87)
   :::column-end:::
   :::column span="":::
      [Tabela ACL](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Reguły udostępniania zasobów między źródłami (TABLE Cross-Origin)](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149)
   :::column-end:::
   :::column span="":::
      [Właściwości tabeli](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Statystyki tabeli](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243)
   :::column-end:::
   :::column span="":::
      [Aktualizuj encję](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Przykładowe biblioteki kodu platformy Azure

Aby wyświetlić kompletne przykładowe biblioteki JavaScript, przejdź do:

* [Przykłady kodu obiektów blob platformy Azure](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples/javascript)
* [Przykłady kodu usługi Azure Data Lake](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples/javascript)
* [Przykłady kodu usługi Azure Files](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-share/samples/javascript)
* [Przykłady kodu kolejki platformy Azure](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples/javascript)

Repozytorium GitHub można przeglądać i klonować dla każdej biblioteki.

## <a name="getting-started-guides"></a>Przewodniki wprowadzenie

Zapoznaj się z poniższymi przewodnikami, jeśli szukasz instrukcji dotyczących instalowania bibliotek klienta usługi Azure Storage i rozpoczynania ich pracy.

* [Wprowadzenie do usługi Azure Blob w języku JavaScript](../blobs/storage-quickstart-blobs-nodejs.md)
* [Wprowadzenie do usługi kolejkowania platformy Azure w języku JavaScript](../queues/storage-quickstart-queues-nodejs.md)
* [Wprowadzenie do usługi tabel platformy Azure w języku JavaScript](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* .NET: [Przykłady usługi Azure Storage przy użyciu platformy .NET](storage-samples-dotnet.md)
* Java: [przykłady usługi Azure Storage przy użyciu języka Java](storage-samples-java.md)
* Python: [przykłady usługi Azure Storage przy użyciu języka Python](storage-samples-python.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](storage-samples.md)
