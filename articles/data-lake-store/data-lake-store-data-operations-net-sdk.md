---
title: 'Zestaw SDK platformy .NET: operacje systemu plików w usłudze Azure Data Lake Storage Gen1'
description: Użyj narzędzia Azure Data Lake Storage Gen1 .NET SDK do operacji systemu plików w usłudze Data Lake Storage Gen1, takich jak tworzenie folderów itp.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638905"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operacje systemu plików w u źródła danych Lake Storage Gen1 przy użyciu sdk .NET

> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [INTERFEJS API ODPOCZYNKU](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje systemu plików w u źródła danych Lake Storage Gen1 przy użyciu pliku .NET SDK. Operacje systemu plików obejmują tworzenie folderów na koncie Data Lake Storage Gen1, przesyłanie plików, pobieranie plików itp.

Aby uzyskać instrukcje dotyczące wykonywania operacji zarządzania kontami w programie Data Lake Storage Gen1 przy użyciu sdk .NET, zobacz [Operacje zarządzania kontami w programie Data Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Visual Studio 2013 lub wyższej**. Instrukcje w tym artykule używają programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia konta, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Tworzenie aplikacji .NET

Przykładowy kod dostępny [w usłudze GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) przeprowadzi Cię przez proces tworzenia plików w magazynie, łączenia plików, pobierania pliku i usuwania niektórych plików z magazynu. Ta część artykułu przeprowadzi Cię przez najważniejsze fragmenty kodu.

1. W programie Visual Studio wybierz menu **Plik** **Nowy**, a następnie **pozycję Project**.
1. Wybierz pozycję **Aplikacja konsoli (.NET Framework),** a następnie wybierz pozycję **Dalej**.
1. W obszarze Nazwa `CreateADLApplication` **projektu**wprowadź , a następnie wybierz pozycję **Utwórz**.
1. Dodaj pakiety NuGet do swojego projektu.

   1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij polecenie **Zarządzaj pakietami NuGet**.
   1. Na karcie **Menedżer pakietów NuGet** upewnij się, że **źródło pakietu** jest ustawione na **nuget.org**. Upewnij się również, że jest zaznaczone pole wyboru **Dołącz wydanie wstępne.**
   1. Wyszukaj i zainstaluj następujące pakiety NuGet:

      * `Microsoft.Azure.DataLake.Store`- W tym artykule użyto wersji 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- Ten artykuł używa wersji 2.3.1.

      Zamknij **Menedżera pakietów NuGet**.

1. Otwórz plik **Program.cs**, usuń istniejący kod, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Zadeklaruj zmienne w pokazany poniżej sposób i podaj wartości dla symboli zastępczych. Upewnij się również, że podana tutaj ścieżka lokalna i nazwa pliku istnieją na komputerze.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

W pozostałych sekcjach artykułu można zobaczyć, jak używać dostępnych metod platformy .NET do wykonywania operacji, takich jak uwierzytelnianie, przekazywanie plików itp.

## <a name="authentication"></a>Uwierzytelnianie

* Aby uzyskać uwierzytelnianie użytkowników końcowych aplikacji, zobacz [Uwierzytelnianie użytkowników końcowych przy użyciu usługi Data Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji, zobacz [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Tworzenie obiektu klienta

Poniższy fragment kodu tworzy obiekt klienta systemu plików Data Lake Storage Gen1, który jest używany do wystawiania żądań do usługi.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Tworzenie plików i katalogów

Dodaj poniższy fragment kodu do aplikacji. Ten fragment kodu dodaje plik i dowolny katalog nadrzędny, który nie istnieje.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Dołączanie do pliku

Poniższy fragment kodu dołącza dane do istniejącego pliku na koncie Usługi Data Lake Storage Gen1.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Odczytywanie pliku

Poniższy fragment kodu odczytuje zawartość pliku w umiań magazynu usługi Data Lake.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Pobieranie właściwości pliku

Poniższy fragment kodu zwraca właściwości skojarzone z plikiem lub katalogiem.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

Definicja `PrintDirectoryEntry` metody jest dostępna jako część próbki [na GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Zmienianie nazwy pliku

Poniższy fragment kodu zmienia nazwę istniejącego pliku na koncie Usługi Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Wyliczanie katalogu

Poniższy urywek wylicza katalogi na koncie Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

Definicja `PrintDirectoryEntry` metody jest dostępna jako część próbki [na GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Rekursywne usuwanie katalogów

Poniższy fragment kodu usuwa katalog i wszystkie jego podkatalogi, cyklicznie.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Samples

Oto kilka przykładów, które pokazują, jak używać SDK systemu plików Data Lake Storage Gen1 Filesystem.

* [Próbka podstawowa w usłudze GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Przykład zaawansowany w usłudze GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Zobacz też

* [Operacje zarządzania kontami w umiań magazynowania danych w uł.1 przy użyciu sdk .NET](data-lake-store-get-started-net-sdk.md)
* [Odwołanie do sdk SDK usługi Data Lake Storage Gen1 .NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
