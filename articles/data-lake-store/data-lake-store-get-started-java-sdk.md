---
title: 'Zestaw Java SDK: operacje systemu plików w usłudze Azure Data Lake Store | Microsoft Docs'
description: Użyj zestawu Java SDK usługi Azure Data Lake Store w celu wykonania takich operacji systemu plików w usłudze Data Lake Store, jak tworzenie folderów itp.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 8f2dd4d001ae7322a351844146925c23b90f0916
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649868"
---
# <a name="filesystem-operations-on-data-lake-store-using-java-sdk"></a>Operacje systemu plików w usłudze Data Lake Store przy użyciu zestawu Java SDK
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Dowiedz się, jak używać zestawu SDK Java usługi Azure Data Lake Store, aby wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych itp. Aby uzyskać więcej informacji o usłudze Data Lake, zobacz temat [Usługa Azure Data Lake Store](data-lake-store-overview.md).

Dostęp do dokumentów interfejsu API zestawu SDK Java usługi Azure Data Lake Store można uzyskać w [dokumentacji interfejsu API języka Java usługi Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Wymagania wstępne
* Zestaw Java Development Kit (JDK 7 lub nowszy, korzystający z języka Java w wersji 1.7 lub nowszej)
* Konto usługi Azure Data Lake Store. Postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż istnieje możliwość kompilacji bez używania systemu kompilacji, takiego jak Maven lub Gradle, systemy te znacznie ułatwiają zarządzanie zależnościami.
* (Opcjonalnie) Wtyczka [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lub [Eclipse](https://www.eclipse.org/downloads/) przypominająca środowisko IDE lub podobna.

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
Przykładowy kod dostępny [w usłudze GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) przeprowadzi Cię przez proces tworzenia plików w magazynie, łączenia plików, pobierania pliku i usuwania niektórych plików z magazynu. Ta część artykułu przeprowadzi Cię przez najważniejsze fragmenty kodu.

1. Utwórz projekt Maven za pomocą polecenia [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) w wierszu polecenia lub w środowisku IDE. Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki IntelliJ, zobacz [tutaj](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki Eclipse, zobacz [tutaj](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Dodaj poniższe zależności do Twojego pliku **pom.xml** programu Maven. Dodaj poniższy fragment kodu przed tagiem **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Pierwszą zależnością jest użycie zestawu SDK usługi Data Lake Store (`azure-data-lake-store-sdk`) z repozytorium maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Zestaw SDK usługi Data Lake Store używa fasady rejestrowania [slf4j](http://www.slf4j.org/), która umożliwia wybór spośród wielu popularnych struktur rejestrowania, takich jak log4j, rejestrowanie Java, logback itp. lub brak rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](http://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Authentication

* Aby dowiedzieć się więcej o uwierzytelnianiu użytkowników końcowych w aplikacji, zobacz [End-user authentication with Data Lake Store using Java (Uwierzytelnianie użytkowników końcowych w usłudze Data Lake Store za pomocą języka Java)](data-lake-store-end-user-authenticate-java-sdk.md).
* Aby dowiedzieć się więcej o uwierzytelnianiu „usługa-usługa” w aplikacji, zobacz [Service-to-service authentication with Data Lake Store using Java (Uwierzytelnianie „usługa-usługa” w usłudze Data Lake Store za pomocą języka Java)](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-an-azure-data-lake-store-client"></a>Tworzenie klienta usługi Azure Data Lake Store
Utworzenie obiektu [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) wymaga określenia nazwy konta usługi Data Lake Store oraz dostawcy tokenu, który został wygenerowany przez Ciebie podczas uwierzytelniania za pomocą usługi Data Lake Store (patrz sekcja [Authentication (Uwierzytelnianie)](#authentication)). Nazwa konta usługi Data Lake Store musi być w pełni kwalifikowaną nazwą domeny. Na przykład zastąp ciąg **FILL-IN-HERE** (Wypełnij tutaj) czymś w rodzaju **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Fragmenty kodu w poniższych sekcjach zawierają przykłady niektórych typowych operacji systemu plików. Aby zobaczyć inne operacje, możesz zapoznać się z pełną [dokumentacją interfejsu API zestawu SDK Java usługi Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) obiektu **ADLStoreClient**.

## <a name="create-a-directory"></a>Tworzenie katalogu

Poniższy fragment kodu tworzy strukturę katalogów w katalogu głównym wskazanego konta usługi Data Lake Store.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Tworzenie pliku

Poniższy fragment kodu tworzy plik (c.txt) w strukturze katalogów i zapisuje pewne dane do tego pliku.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Możesz również utworzyć plik (d.txt) przy użyciu tablic typu Byte.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

Definicja funkcji `getSampleContent` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Dołączanie do pliku

Poniższy fragment kodu dołącza zawartość do istniejącego pliku.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

Definicja funkcji `getSampleContent` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Odczytywanie pliku

Poniższy fragment kodu odczytuje zawartość z pliku na koncie usługi Data Lake Store.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Łączenie plików

Poniższy fragment kodu łączy dwa pliki na koncie usługi Data Lake Store. W przypadku powodzenia połączony plik zastępuje te dwa istniejące pliki.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Zmienianie nazwy pliku

Poniższy fragment kodu zmienia nazwę pliku na koncie usługi Data Lake Store.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Pobranie metadanych dla pliku

Poniższy fragment kodu pobiera metadane dla pliku na koncie usługi Data Lake Store.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Ustawianie uprawnień do pliku

Poniższy fragment kodu ustawia uprawnienia do pliku, który został utworzony przez Ciebie w poprzedniej sekcji.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Poniższy fragment kodu rekursywnie wyświetla zawartość katalogu.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

Definicja funkcji `printDirectoryInfo` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Usuwanie plików i folderów

Poniższy fragment kodu rekursywnie usuwa określone pliki i foldery na koncie usługi Data Lake Store.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji
1. Aby przeprowadzić uruchomienie z poziomu środowiska IDE, znajdź i naciśnij przycisk **Run** (Uruchom). Aby przeprowadzić uruchomienie z poziomu programu Maven, użyj polecenia [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Aby wygenerować autonomiczny plik JAR, który można uruchomić z poziomu wiersza polecenia, skompiluj plik JAR zawierający wszystkie zależności, używając [wtyczki zestawu Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Przykład znajduje się w pliku pom.xml w [przykładowym kodzie źródłowym w serwisie GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml).

## <a name="next-steps"></a>Kolejne kroki
* [Zapoznawanie się z dokumentacją JavaDoc dotyczącą zestawu SDK Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)


