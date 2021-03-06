---
title: Przykłady usługi Azure Storage przy użyciu platformy .NET | Dokumenty firmy Microsoft
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj wprowadzenie przykładów obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienta magazynu .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/13/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: f02c08e752575dd1f57ce91dfc3145b69e3a2fd2
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010498"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>Przykłady usługi Azure Storage przy użyciu bibliotek klienckich w wersji 12 .NET

Poniższa tabela zawiera omówienie naszego repozytorium przykładów i scenariuszy omówionych w każdej próbce. Kliknij łącza, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

> [!NOTE]
> Te przykłady używają najnowszej biblioteki usługi Azure Storage .NET w wersji 12. W przypadku starszego kodu w wersji 11 zobacz [przykłady usługi Azure Blob Storage dla platformy .NET](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started) w repozytorium Usługi GitHub.

## <a name="blob-samples"></a>Przykłady obiektów blob

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu ciągu połączenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnij się za pomocą usługi Azure Identity](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu tokenu usługi Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Anonimowy dostęp do publicznego obiektu blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Tworzenie partii

:::row:::
   :::column span="":::
      [Usuwanie kilku obiektów blob w jednym żądaniu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Ustawianie kilku warstw dostępu obiektów blob w jednym żądaniu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Kontrola drobnoziarnista w żądaniu partii](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Wychwytywuj błędy z nieudanej operacji podrzędnej](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Obiekt blob

:::row:::
   :::column span="":::
      [Przekazywanie pliku do obiektu blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Pobieranie obiektu blob do pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobieranie obrazu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Lista wszystkich obiektów blob w kontenerze](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów
:::row:::
   :::column span="2":::
      [Wyzwalanie nieodwracalnego błędu przy użyciu klienta kontenera](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Próbki Data Lake Storage Gen2

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Anonimowy dostęp do pliku publicznego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnij przy użyciu sygnatury dostępu współdzielonego (SAS)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu tokenu usługi Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>System plików
:::row:::
   :::column span="":::
      [Tworzenie pliku przy użyciu klienta systemu plików](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Pobierz właściwości pliku i katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Zmienianie nazwy pliku i katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Katalog

:::row:::
   :::column span="":::
      [Tworzenie katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [Tworzenie pliku przy użyciu klienta katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Katalogi list](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Przechodzenie przez pliki i katalogi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Plik
:::row:::
   :::column span="":::
      [Przekazywanie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Przekazywanie przez dołączenie do pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobieranie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ustawianie i uzyskiwanie listy kontroli dostępu do plików](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Ustawianie i uzyskanie uprawnień do pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów

:::row:::
   :::column span="2":::
      [Wyzwalanie nieodwracalnego błędu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Przykłady usług Azure Files

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu ciągu połączenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Uwierzytelnij się przy użyciu sygnatury dostępu współdzielonego (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Udziały plików

:::row:::
   :::column span="":::
      [Tworzenie udziału i przekazywanie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Pobieranie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Przechodzenie przez pliki i katalogi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów

:::row:::
   :::column span="2":::
      [Wyzwalanie nieodwracalnego błędu przy użyciu klienta udziału](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Próbki kolejek

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Uwierzytelnianie za pomocą usługi Azure Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu ciągu połączenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu poświadczeń klucza udostępnionego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnij się przy użyciu sygnatury dostępu współdzielonego (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Uwierzytelnij się przy użyciu tokenu usługi Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Kolejka

:::row:::
   :::column span="2":::
      [Tworzenie kolejki i dodawanie wiadomości](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Komunikat

:::row:::
   :::column span="":::
      [Odbieranie i przetwarzanie wiadomości](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [Wgląd w wiadomości](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Odbieranie wiadomości i aktualizowanie limitu czasu widoczności](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów 
:::row:::
   :::column span="2":::
      [Wyzwalanie nieodwracalnego błędu przy użyciu klienta kolejki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Przykłady tabeli (w wersji 11)

:::row:::
   :::column span="":::
      [Tworzenie tabeli](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [Usuwanie encji/tabeli](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wstawianie/scalanie/zamienianie elementu](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Encje kwerend](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wykonywanie zapytań dotyczących tabel](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Tabela ACL/właściwości](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Aktualizuj encję](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Przykładowe biblioteki kodu platformy Azure

Aby wyświetlić kompletne przykładowe biblioteki platformy .NET, przejdź do:

* [Przykłady kodu obiektów blob platformy Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Przykłady kodu usługi Azure Data Lake](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Przykłady kodu usługi Azure Files](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Przykłady kodu kolejki platformy Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

Repozytorium GitHub można przeglądać i klonować dla każdej biblioteki.

## <a name="getting-started-guides"></a>Przewodniki wprowadzenie

Zapoznaj się z poniższymi przewodnikami, jeśli szukasz instrukcji dotyczących instalowania bibliotek klienta usługi Azure Storage i rozpoczynania ich pracy.

* [Wprowadzenie do usługi Azure Blob w usłudze .NET](../blobs/storage-quickstart-blobs-dotnet.md)
* [Wprowadzenie do usługi Kolejkowania platformy Azure w sieci .NET](../queues/storage-quickstart-queues-dotnet.md)
* [Wprowadzenie do usługi Azure Table Service w sieci .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [Wprowadzenie do usługi plików platformy Azure w sieci .NET](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* Java: [przykłady usługi Azure Storage przy użyciu języka Java](storage-samples-java.md)
* Python: [przykłady usługi Azure Storage przy użyciu języka Python](storage-samples-python.md)
* JavaScript/Node.js: [przykłady usługi Azure Storage przy użyciu języka JavaScript](storage-samples-javascript.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](storage-samples.md)
