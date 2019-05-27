---
title: 'ZESTAW SDK PLATFORMY .NET: Operacje systemu plików w usłudze Azure Data Lake magazynu Gen1 | Dokumentacja firmy Microsoft'
description: Użyj usługi Azure Storage Gen1 .NET zestawu SDK Data Lake można wykonywać operacje systemu plików w Data Lake Storage Gen1, takie jak tworzenie folderów itp.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0771c9c5311e264fb996bbac1c540f9ed11873cb
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908060"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operacje systemu plików w usłudze Azure Data Lake magazynu Gen1 przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje systemu plików w Data Lake Storage Gen1 przy użyciu zestawu .NET SDK. Operacje systemu plików obejmują tworzenie folderów na koncie usługi Data Lake Storage Gen1, przekazywanie plików, pobieranie plików itp.

Aby uzyskać instrukcje na temat sposobu wykonywania operacji zarządzania kontem w Data Lake Storage Gen1 przy użyciu zestawu .NET SDK, zobacz [operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2013 lub nowszy**. Poniższe instrukcje korzystają z programu Visual Studio 2019 r.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia konta, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Tworzenie aplikacji .NET
Przykładowy kod dostępny [w usłudze GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) przeprowadzi Cię przez proces tworzenia plików w magazynie, łączenia plików, pobierania pliku i usuwania niektórych plików z magazynu. Ta część artykułu przeprowadzi Cię przez najważniejsze fragmenty kodu.

1. W programie Visual Studio, wybierz **pliku** menu **New**, a następnie **projektu**.
2. Wybierz **Aplikacja konsoli (.NET Framework)**, a następnie wybierz pozycję **dalej**.
3. W **Nazwa projektu**, wprowadź `CreateADLApplication`, a następnie wybierz pozycję **Utwórz**.

4. Dodaj pakiety NuGet do swojego projektu.

   1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij polecenie **Zarządzaj pakietami NuGet**.
   2. Na karcie **Menedżer pakietów NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość **nuget.org** i że zaznaczone jest pole wyboru **Uwzględnij wersję wstępną**.
   3. Wyszukaj i zainstaluj następujące pakiety NuGet:

      * `Microsoft.Azure.DataLake.Store` — w tym samouczku jest używana wersja 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — w tym samouczku jest używana wersja 2.3.1.
    
      Zamknij **Menedżera pakietów NuGet**.

5. Otwórz plik **Program.cs**, usuń istniejący kod, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6. Zadeklaruj zmienne w pokazany poniżej sposób i podaj wartości dla symboli zastępczych. Upewnij się również, że podana tutaj ścieżka lokalna i nazwa pliku istnieją na komputerze.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

Z pozostałych akapitów tego artykułu możesz dowiedzieć się, w jaki sposób używać dostępnych metod .NET w celu wykonywania operacji, takich jak uwierzytelnianie, przekazywanie plików itp.

## <a name="authentication"></a>Authentication

* Do uwierzytelniania użytkownika końcowego dla aplikacji, zobacz [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Do uwierzytelniania service to service dla aplikacji, zobacz [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Tworzenie obiektu klienta
Poniższy fragment kodu tworzy obiekt klienta systemu plików Data Lake Storage Gen1, który jest używany do wysyłania żądań do usługi.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Tworzenie plików i katalogów
Dodaj poniższy fragment kodu do aplikacji. Ten fragment kodu dodaje plik, a także dowolny katalog nadrzędny, który nie istnieje.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Dołączanie do pliku
Poniższy fragment kodu dołącza dane do istniejącego pliku na koncie usługi Data Lake Storage Gen1.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Odczytywanie pliku
Poniższy fragment kodu odczytuje zawartość pliku w Data Lake Storage Gen1.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Pobierz właściwości pliku
Poniższy fragment kodu zwraca właściwości skojarzone z plikiem lub katalogiem.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

Definicja `PrintDirectoryEntry` metoda jest dostępna w ramach przykładu [w serwisie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Zmienianie nazwy pliku
Poniższy fragment kodu zmienia nazwę istniejącego pliku w ramach konta Data Lake Storage Gen1.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Wyliczanie katalogu
Poniższy fragment kodu wylicza katalogi na koncie usługi Data Lake Storage Gen1

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

Definicja `PrintDirectoryEntry` metoda jest dostępna w ramach przykładu [w serwisie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Rekursywne usuwanie katalogów
Poniższy fragment kodu rekursywnie usuwa katalog i wszystkie jego podkatalogi.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Przykłady
Poniżej przedstawiono kilka przykładów na temat korzystania z zestawu SDK systemu plików Gen1 usługi Data Lake Storage.
* [Podstawowy przykład w witrynie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Zaawansowany przykład w witrynie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Zobacz także
* [Operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dokumentacja zestawu SDK .NET Gen1 Lake magazynu danych](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
