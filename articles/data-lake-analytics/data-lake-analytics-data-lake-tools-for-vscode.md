---
title: Użyj usługi Azure Data Lake Tools dla programu Visual Studio Code
description: Dowiedz się, jak Azure Data Lake Tools dla Visual Studio Code umożliwia tworzenie, testowanie i uruchamianie skryptów U-SQL.
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 79cd1a04c99891e5146ad20cfd36b8bd4fe4d893
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261488"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Użyj usługi Azure Data Lake Tools dla programu Visual Studio Code

W tym artykule Dowiedz się, jak można użyć usługi Azure Data Lake Tools dla programu Visual Studio (kod VS) do tworzenia, testowania i uruchamiać skrypty U-SQL. Informacje są także omówione w poniższym klipie wideo:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Wymagania wstępne

Azure narzędzi Data Lake Tools dla programu VS kodu obsługuje Windows, Linux lub MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

System MacOS i Linux:
- [Oprogramowanie .NET core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](http://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Zainstaluj usługi Azure Data Lake Tools

Po zainstalowaniu wymagań wstępnych można zainstalować usługi Azure Data Lake Tools dla programu VS kodu.

**Aby zainstalować usługi Azure Data Lake Tools**

1. Otwórz program Visual Studio Code.
2. Wybierz **rozszerzenia** w okienku po lewej stronie. Wprowadź **Azure Data Lake Tools** w polu wyszukiwania.
3. Wybierz **zainstalować** obok **Azure Data Lake Tools**. 

   ![Opcje dotyczące instalowania narzędzi Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po kilku sekundach **zainstalować** przycisku zmienia się na **Załaduj ponownie**.
4. Wybierz **Załaduj ponownie** aktywować **Azure Data Lake Tools** rozszerzenia.
5. Wybierz **ponownego załadowania okna** o potwierdzenie. Widać **Azure Data Lake Tools** w **rozszerzenia** okienka.

 
## <a name="activate-azure-data-lake-tools"></a>Aktywacja usługi Azure Data Lake Tools
Utwórz plik .usql lub Otwórz istniejący plik .usql aktywować rozszerzenia. 


## <a name="work-with-u-sql"></a>Praca z języka U-SQL

Aby pracować z U-SQL, należy otworzyć U-SQL pliku lub folderu.

**Aby otworzyć przykładowy skrypt**

Otwórz palety polecenia (Ctrl + Shift + P), a następnie wprowadź **ADL: Otwórz przykładowy skrypt**. Otwiera inne wystąpienie tego przykładu. Można również edytować, skonfigurować i przesłać skrypt w tym wystąpieniu.

**Aby otworzyć folder projektu U-SQL**

1. W programie Visual Studio Code, wybierz **pliku** menu, a następnie wybierz **Otwórz Folder**.
2. Określ folder, a następnie wybierz **wybierz Folder**.
3. Wybierz **pliku** menu, a następnie wybierz **nowy**. Plik 1 bez tytułu zostanie dodany do projektu.
4. Wprowadź następujący kod w pliku bez tytułu 1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Skrypt tworzy plik departments.csv z niektóre dane zawarte w folderze/Output.

5. Zapisz plik jako **myUSQL.usql** w otwartym folderze.

**Do kompilowania skryptu U-SQL**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: kompilowania skryptu**. Zostaną wyświetlone wyniki kompilacji w **dane wyjściowe** okna. Można również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybierz **ADL: kompilowania skryptu** skompilować zadań U-SQL. Wynik kompilacji jest wyświetlana w **dane wyjściowe** okienka.
 
**Aby przesłać skrypt U-SQL**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: przesłać zadania**. Można również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybierz **ADL: Prześlij zadanie**. 

Po przesłaniu zadania skryptu U-SQL w dziennikach przesyłania są wyświetlane w **dane wyjściowe** okna w kodzie VS. Wyświetl zadania zostanie wyświetlona w okienku po prawej stronie. Jeśli przesyłanie zakończy się pomyślnie, zbyt zostanie wyświetlony adres URL zadania. Adres URL zadania można otworzyć w przeglądarce sieci web, aby śledzić stan zadania w czasie rzeczywistym. 

W widoku zadania **Podsumowanie** kartę, aby wyświetlić szczegóły zadania. Główne funkcje obejmują ponownie prześlij skrypt, zduplikowany skrypt i Otwórz w portalu. W widoku zadania **danych** kartę, można odwołać się do plików wejściowych plików wyjściowych i pliki zasobów. Pliki można pobrać na komputer lokalny.

![Karta Podsumowanie w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Na karcie dane w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Aby ustawić domyślnego kontekstu**

Możesz ustawić domyślny kontekst do zastosowania tego ustawienia do wszystkich plików skryptów, jeśli nie ustawiono parametrów dla plików pojedynczo.

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: Ustaw domyślny kontekst**. Lub kliknij prawym przyciskiem myszy edytora skryptów i wybierz polecenie **ADL: Ustaw domyślny kontekst**.
3. Wybierz konto, bazę danych i schemat, który ma. To ustawienie jest zapisane w pliku konfiguracji xxx_settings.json.

   ![Konto bazy danych i Ustaw jako domyślny kontekst schematu](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Aby ustawić parametry skryptu**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: Ustaw parametry skryptu**.
3. Plik xxx_settings.json jest otwarty z następującymi właściwościami:

   - **konto**: konta usługi Azure Data Lake Analytics w ramach Twojej subskrypcji platformy Azure, która jest potrzebna, aby skompilować i uruchomić zadań U-SQL. Należy skonfigurować konto komputera, przed kompilowanie i uruchamianie zadań U-SQL.
   - **Baza danych**: bazy danych przy użyciu tego konta. Wartość domyślna to **wzorca**.
   - **Schemat**: schemat w bazie danych. Wartość domyślna to **dbo**.
   - **optionalSettings**:
        - **priorytet**: zakres priorytetu jest z zakresu od 1 do 1000, gdzie 1 oznacza najwyższy priorytet. Wartość domyślna to **1000**.
        - **degreeOfParallelism**: zakres równoległości jest z zakresu od 1 do 150. Wartość domyślna to równoległości maksymalny dozwolony na koncie usługi Azure Data Lake Analytics. 

   ![Zawartość pliku JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Po zapisaniu konfiguracji konta, bazy danych i informacji o schemacie są wyświetlane na pasku stanu, w lewym dolnym rogu odpowiedni plik .usql Jeśli nie masz, aby skonfigurować domyślny kontekst.

**Aby ustawić Git Ignoruj**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: Ignoruj Git zestawu**.

   - Jeśli nie masz **.gitIgnore** pliku w kodzie VS folder roboczy w pliku o nazwie **.gitIgnore** jest tworzony w folderze. Cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) są domyślnie dodawane w pliku. Jeśli to konieczne, możesz wprowadzić dodatkowe aktualizacje.
   - Jeśli masz już **.gitIgnore** pliku w kodzie VS folder roboczy narzędzie dodaje cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) w Twojej **.gitIgnore** plik, jeśli cztery elementy nie zostały uwzględnione w pliku.

   ![Elementy w pliku .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Praca z plików z kodem: C Sharp, Python i R

Azure Data Lake Tools obsługują wiele niestandardowych kodów. Aby uzyskać instrukcje, zobacz [opracowanie U-SQL z języka Python, R i C Sharp dla usługi Azure Data Lake Analytics w kodzie VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Praca z zestawami

Aby uzyskać informacje na temat tworzenia zespołów, zobacz [opracowanie U-SQL zestawy dla usługi Azure Data Lake Analytics zadań](data-lake-analytics-u-sql-develop-assemblies.md).

Data Lake Tools służy do rejestrowania zestawów niestandardowych kodów w katalogu Data Lake Analytics.

**Można zarejestrować zestawu**

Można zarejestrować zestawu za pomocą **ADL: Zarejestruj zestawu** lub **ADL: Zarejestruj zestawu (zaawansowane)** polecenia.

**Aby zarejestrować za pomocą ADL: polecenie zarejestrować zestawu**
1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2.  Wprowadź **ADL: zarejestrować zestawu**. 
3.  Określ ścieżkę zestawu lokalnego. 
4.  Wybierz konto usługi Data Lake Analytics.
5.  Wybierz bazę danych.

Portal jest otwarty w przeglądarce i przedstawia proces rejestracji zestawu.  

Wygodny sposób, aby wyzwolić **ADL: Zarejestruj zestawu** polecenie jest kliknij prawym przyciskiem myszy plik .dll w Eksploratorze plików. 

**Aby zarejestrować za pomocą ADL: polecenie zarejestrować zestawu (zaawansowane)**
1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2.  Wprowadź **ADL: zarejestrować zestawu (zaawansowane)**. 
3.  Określ ścieżkę zestawu lokalnego. 
4.  Plik JSON jest wyświetlany. Przejrzyj i Edytuj zależności zestawu i parametry zasobu, jeśli to konieczne. Instrukcje są wyświetlane w **dane wyjściowe** okna. Aby przejść do rejestrowania zestawów, Zapisz (Ctrl + S) w pliku JSON.

    ![Plik JSON o parametrach zasobów i zależności zestawu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Automatycznie wykrywa narzędzia usługi Azure Data Lake Określa, czy biblioteka DLL nie ma żadnych zależności zestawu. Zależności są wyświetlane w pliku JSON, po ich są wykrywane. 
>- Biblioteka DLL zasobów (na przykład txt, .png i .csv) można przekazać jako część zestawu rejestracji. 

Innym sposobem wyzwolenia **ADL: Zarejestruj zestawu (zaawansowane)** polecenie jest kliknij prawym przyciskiem myszy plik .dll w Eksploratorze plików. 

Poniższy kod języka U-SQL przedstawia sposób wywołania zestawu. W przykładzie nazwa zestawu jest *testu*.


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Użyj uruchamiania lokalnego skryptu U-SQL i debugowania lokalnego dla użytkowników systemu Windows
Uruchamiania lokalnego skryptu U-SQL testów danych lokalnych i weryfikuje skrypt lokalnie, przed opublikowaniem kodu do usługi Data Lake Analytics. Funkcja debugowania lokalnego umożliwia wykonywanie następujących zadań przed przesłaniem do usługi Data Lake Analytics kodu: 
- Debugowanie programu C# związane z kodem. 
- Kroków opisanych w kodzie. 
- Sprawdź poprawność skrypt lokalnie.

Aby uzyskać instrukcje dotyczące przebiegu lokalnego i debugowania lokalnego, zobacz [uruchamiania lokalnego skryptu U-SQL i debugowania lokalnego z kodem Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Można było skompilować i uruchomić skrypty U-SQL w usługi Data Lake Analytics, należy połączyć z kontem platformy Azure.

<b id="sign-in-by-command">Do połączenia z platformą Azure za pomocą polecenia</b>

1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2.  Wprowadź **ADL: logowania**. Dane logowania będzie wyświetlany w prawej dolnej części strony.

    ![Wprowadzanie polecenia logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Powiadomienia dotyczące logowania i uwierzytelniania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Wybierz **skopiuj & Otwórz** otworzyć [strony sieci Web logowania](https://aka.ms/devicelogin). Wklej ten kod w polu, a następnie wybierz **Kontynuuj**.

    ![Strona sieci Web logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Postępuj zgodnie z instrukcjami, aby zalogować się w stronę sieci Web. Gdy komputer jest połączony, nazwa konta platformy Azure jest wyświetlana na pasku stanu, w lewym dolnym rogu okna programu VS kodu. 

> [!NOTE] 
>- Narzędzia Data Lake Tools automatycznie loguje się użytkownik przy następnym Jeśli nie wylogujesz.
>- Jeśli konto ma dwa składniki włączone, zalecane jest użycie z telefonu uwierzytelniania, a nie za pomocą numeru PIN.


Aby się wylogować, wprowadź polecenie **ADL: wylogowania**.

**Do nawiązania połączenia platformy Azure z Eksploratora**

Rozwiń węzeł **AZURE DATALAKE**, wybierz pozycję **logowanie do platformy Azure**, a następnie wykonaj kroki 3 i 4 z [z usługą Azure przy użyciu polecenia](#sign-in-by-command).

![Wybór "Logowanie do platformy Azure" w Eksploratorze](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nie można wylogować się z Eksploratora. Aby się wylogować, zobacz [z usługą Azure przy użyciu polecenia](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Utwórz skrypt wyodrębniania 
Można utworzyć skrypt wyodrębniania plików txt, CSV i .tsv za pomocą polecenia **ADL: Tworzenie skryptu WYODRĘBNIĆ** lub Eksploratora usługi Azure Data Lake.

**Aby utworzyć skrypt wyodrębniania przy użyciu polecenia**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia i wprowadź **ADL: Tworzenie skryptu WYODRĘBNIĆ**.
2. Wprowadź pełną ścieżkę do pliku usługi Azure Storage, a następnie wybierz klawisz Enter.
3. Wybierz jedno konto.
4. Dla pliku txt wybierz ogranicznik, aby wyodrębnić plik. 

![Proces tworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skrypt wyodrębniania jest generowany na podstawie wpisów. Skrypt, który nie może wykryć kolumn wybierz jedną z dwóch opcji. Jeśli nie zostanie wygenerowany tylko jeden skrypt.

![Wynik utworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Aby utworzyć skrypt wyodrębniania z Eksploratora**

Innym sposobem tworzenia skryptu wyodrębniania jest za pomocą menu kliknij prawym przyciskiem myszy (skrót) na CSV, .tsv lub pliku txt w magazynie usługi Azure Data Lake Store lub obiektów Blob platformy Azure.

![Polecenie "Utwórz skrypt WYODRĘBNIANIA" z menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integracja z usługą Azure Data Lake Analytics za pomocą poleceń

Dostępne zasoby usługi Azure Data Lake Analytics listy kont dostępu do metadanych i zadania usługi analiza widoku. 

**Aby wyświetlić listę kont usługi Azure Data Lake Analytics w ramach Twojej subskrypcji platformy Azure**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2. Wprowadź **ADL: listy kont**. Konta są wyświetlane w **dane wyjściowe** okienka.

**Aby uzyskać dostępu do metadanych usługi Azure Data Lake Analytics**

1.  Wybierz kombinację klawiszy Ctrl + Shift + P, a następnie wprowadź **ADL: listy tabel**.
2.  Wybierz jedno z kont usługi Data Lake Analytics.
3.  Wybierz jedną z bazy danych usługi Data Lake Analytics.
4.  Wybierz jeden ze schematów. Można wyświetlić listę tabel.

**Aby wyświetlić zadania usługi Azure Data Lake Analytics**
1.  Otwórz palety polecenia (Ctrl + Shift + P) i wybierz **ADL: Pokaż zadania**. 
2.  Wybierz usługi Data Lake Analytics lub konta lokalnego. 
3.  Poczekaj na liście zadań, aby są wyświetlane dla konta.
4.  Wybierz zadanie z listy zadań. Narzędzia Data Lake Tools spowoduje otwarcie widoku zadania w okienku po prawej stronie i wyświetla niektóre informacje w kodzie VS danych wyjściowych.

    ![Lista zadań](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integracja z usługą Azure Data Lake Store za pomocą poleceń

Można użyć poleceń Azure Data Lake Store, aby:
 - [Przeglądanie zasobów usługi Azure Data Lake Store](#list-the-storage-path) 
 - [Podgląd pliku usługi Azure Data Lake Store](#preview-the-storage-file) 
 - [Przekaż plik bezpośrednio do usługi Azure Data Lake Store w kodzie VS](#upload-file-or-folder)
 - [Pobierz plik bezpośrednio z usługi Azure Data Lake Store w kodzie VS](#download-file)

### <a name="list-the-storage-path"></a>Lista ścieżki do magazynu 

**Aby wyświetlić listę ścieżki magazynu za pomocą polecenia palety**

1. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **ADL: ścieżka listy**.
2. Wybierz folder z listy lub wybierz **wprowadź ścieżkę** lub **przejść z ścieżki katalogu głównego**. (Używamy **wprowadź ścieżkę** jako przykład.) 
3. Wybierz konto usługi Data Lake Analytics.
4. Przejdź do lub wprowadź ścieżkę do folderu magazynu (na przykład danych wyjściowych /).  

Palety polecenie wyświetla ścieżkę pliku na podstawie wpisów.

![Magazyn wyników ścieżki](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Bardziej wygodne ścieżka względna jest za pomocą menu skrótów.

**Aby wyświetlić listę ścieżki magazynu za pomocą menu skrótów**

Kliknij prawym przyciskiem myszy ciąg ścieżki, a następnie wybierz **Ścieżka listy**.

!["Ścieżka list" w menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Podgląd pliku magazynu

1. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **ADL: Podgląd pliku**.
2. Wybierz konto usługi Data Lake Analytics. 
3. Wprowadź ścieżkę pliku do usługi Azure Storage (na przykład /output/SearchLog.txt). 

Plik można otworzyć w kodzie VS.

![Kroki i wynik do podglądu pliku magazynu](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Inny sposób, aby wyświetlić podgląd pliku jest za pomocą menu skrótów na pełną ścieżkę pliku lub ścieżki względnej pliku w Edytorze skryptów. 

### <a name="upload-a-file-or-folder"></a>Przekaż plik lub folder

1. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **Przekaż plik** lub **przekazać folderu**.
2. Wybierz jednego lub wielu plików, jeśli wybrano **Przekaż plik**, lub wybierz cały folder, w przypadku wybrania **przekazać folderu**. Następnie wybierz **przekazać**. 
3. Wybierz folder magazynu na liście lub wybierz **wprowadź ścieżkę** lub **przejść z ścieżki katalogu głównego**. (Używamy **wprowadź ścieżkę** jako przykład.) 
4. Wybierz konto usługi Data Lake Analytics. 
5. Przejdź do lub wprowadź ścieżkę do folderu magazynu (na przykład danych wyjściowych /). 
6. Wybierz **Wybierz bieżący Folder** do określenia folderu docelowego przekazywania.

![Kroki i wynik do przekazywania pliku lub folderu](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Inny sposób, aby przekazać pliki do pamięci masowej jest za pomocą menu skrótów na pełną ścieżkę pliku lub ścieżki względnej pliku w Edytorze skryptów.

Możesz [monitorować stan przekazywania](#check-storage-tasks-status).


### <a name="download-a-file"></a>Pobieranie pliku 
Można pobrać pliku za pomocą polecenia **ADL: Pobierz plik** lub **ADL: Pobierz plik (zaawansowane)**.

**Można pobrać pliku za pośrednictwem ADL: polecenie Pobierz plik (zaawansowane)**
1. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **Pobierz plik (zaawansowane)**.
2. VS kod przedstawia plik JSON. Można wprowadzić ścieżki do pliku i pobrać wiele plików w tym samym czasie. Instrukcje są wyświetlane w **dane wyjściowe** okna. Aby kontynuować, aby pobrać plik lub pliki, Zapisz (Ctrl + S) w pliku JSON.

    ![Plik JSON ze ścieżkami pobierania plików](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**Dane wyjściowe** okno wyświetla stan pobierania pliku.

![Okno danych wyjściowych ze stanem pobieranie](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Możesz [monitorować stan pobierania](#check-storage-tasks-status).

**Można pobrać pliku za pośrednictwem ADL: Pobierz plik — polecenie**

1. Kliknij prawym przyciskiem myszy edytora skryptów, wybierz **Pobierz plik**, a następnie wybierz folder docelowy z **wybierz Folder** okno dialogowe.
2. Wybierz folder z listy lub wybierz **wprowadź ścieżkę** lub **przejść z ścieżki katalogu głównego**. (Używamy **wprowadź ścieżkę** jako przykład.) 
3. Wybierz konto usługi Data Lake Analytics. 
4. Przejdź do lub wprowadź ścieżkę do folderu magazynu (na przykład danych wyjściowych /), a następnie wybierz plik do pobrania.

![Kroki i wynik pobierania pliku](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Innym sposobem pobrania magazyn plików jest za pomocą menu skrótów na pełną ścieżkę pliku lub ścieżki względnej pliku w Edytorze skryptów.

Możesz [monitorować stan pobierania](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Sprawdź stan zadania magazynu
Przekazywanie i pobieranie stanu jest wyświetlany na pasku stanu. Wybierz na pasku stanu, a następnie zostanie wyświetlony stan na **dane wyjściowe** kartę.

![Pasek stanu i szczegóły danych wyjściowych](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integracja z usługą Azure Data Lake Analytics z Eksploratora

Po zalogowaniu, wszystkie subskrypcje dla konta platformy Azure są wyświetlane w okienku po lewej stronie w obszarze **AZURE DATALAKE**. 

![Eksplorator usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metadanych nawigacji

Rozwiń subskrypcji platformy Azure. W obszarze **U-SQL bazy danych** węzła, można przeglądać U-SQL bazy danych i folderów widoku, takich jak **schematy**, **poświadczenia**, **zestawy**, **Tabel**, i **indeksu**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics metadanych jednostki zarządzania

Rozwiń węzeł **baz danych U SQL**. Można utworzyć bazy danych, schematu, tabeli, typ tabeli, indeksu lub statystyki prawym przyciskiem myszy odpowiedni węzeł, a następnie wybierając **skrypt w celu tworzenia** menu skrótów. Na stronie otwartą skryptu go edytować, zgodnie z potrzebami. Następnie przesłać go prawym przyciskiem myszy i wybierając zadanie **ADL: Prześlij zadanie**. 

Po zakończeniu tworzenia elementu, kliknij prawym przyciskiem myszy węzeł, a następnie wybierz **Odśwież** do wyświetlenia elementu. Można również usunąć element prawym przyciskiem myszy, a następnie wybierając **usunąć**.

![Polecenie "Skrypt do utworzenia" w menu skrótów w Eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skrypt strony dla nowego elementu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics zestawu rejestracji

Można zarejestrować zestawu w odpowiednią bazę danych, klikając prawym przyciskiem myszy **zestawy** węzeł, a następnie wybierając **zarejestrować zestawu**.

![Polecenie "Zarejestrować zestawu" w menu skrótów węzła zestawów](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integracja z usługą Azure Data Lake Store z Eksploratora

Przejdź do **usługi Data Lake Store**:

- Można kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć **Odśwież**, **usunąć**, **przekazać**, **przekazać folderu**, **kopiowania Ścieżka względna**, i **Kopiuj pełną ścieżkę** poleceń menu skrótów.

   ![Polecenia menu skrótów węzła folder w Eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Można kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć **Podgląd**, **Pobierz**, **usunąć**, **Tworzenie skryptu WYODRĘBNIĆ** (dostępne tylko dla woluminów CSV TSV oraz pliki TXT), **Kopiuj ścieżkę względną**, i **Kopiuj pełną ścieżkę** poleceń menu skrótów.

   ![Polecenia menu skrótów węzła plików w Eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integracja z magazynu obiektów Blob platformy Azure z Eksploratora

Przejdź do magazynu obiektów Blob:

- Można kliknąć prawym przyciskiem myszy węzeł kontenera obiektów blob, a następnie użyć **Odśwież**, **usunąć kontener obiektów Blob**, i **przekazania obiektu Blob** poleceń menu skrótów.

   ![Polecenia menu skrótów węzła kontenera obiektów blob w magazynie obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Można kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć **Odśwież** i **przekazania obiektu Blob** poleceń menu skrótów.

   ![Polecenia menu skrótów do folderu węźle magazynu obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Można kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć **Podgląd i edytować**, **Pobierz**, **usunąć**, **Tworzenie skryptu WYODRĘBNIĆ** (dostępne tylko pliki TXT, CSV i TSV) **Kopiuj ścieżkę względną**, i **Kopiuj pełną ścieżkę** poleceń menu skrótów.

    ![Polecenia menu skrótów węzła pliku w magazynie obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Otwórz Eksploratora usługi Data Lake w portalu
1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2. Wprowadź **Otwórz Eksploratora usługi Storage Azure Web** lub kliknij prawym przyciskiem myszy ścieżkę względną lub pełną ścieżkę w Edytorze skryptów, a następnie wybierz **Otwórz Eksploratora usługi Storage Azure Web**.
3. Wybierz konto usługi Data Lake Analytics.

Narzędzia Data Lake Tools otwiera ścieżki do magazynu Azure w portalu Azure. Można znaleźć ścieżki i wyświetlić podgląd pliku z sieci web.

## <a name="additional-features"></a>Dodatkowe funkcje

Narzędzia Data Lake Tools dla programu VS kodu obsługuje następujące funkcje:

-   **Funkcji autocomplete IntelliSense**: sugestie są wyświetlane w wyskakujące wokół elementy, takie jak słowa kluczowe, metod i zmiennych. Inne ikony reprezentują różne typy obiektów:

    - Scala — typ danych
    - Typ danych złożonych
    - Wbudowanych typów
    - Klasy i kolekcji .NET
    - Wyrażenia języka C#
    - Funkcje UDF wbudowane C#, obiektów udo i UDAAGs 
    - Funkcje języka U-SQL
    - Funkcje obsługi okien U-SQL
 
    ![Typy obiektów IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Funkcji autocomplete IntelliSense w metadanych usługi Data Lake Analytics**: Data Lake Tools pobierze lokalnie informacje metadanych usługi Data Lake Analytics. Funkcja IntelliSense automatycznie wypełni obiektów z metadanych usługi Data Lake Analytics. Te obiekty obejmują bazy danych, schematu, tabeli, widoku, funkcji zwracającej tabelę, procedury i zestawów języka C#.
 
    ![Metadane funkcji IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Znacznik błąd IntelliSense**: Data Lake Tools podkreśla błędy edytowania U-SQL i C#. 
-   **Najważniejsze funkcje składni**: Data Lake Tools używa kolorów do odróżnienia elementy, takie jak zmiennych, słowa kluczowe, typy danych i funkcji. 

    ![Składnia przy użyciu różnych kolorów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Zaleca się uaktualnienie do usługi Azure Data Lake Tools dla programu Visual Studio w wersji 2.3.3000.4 lub nowszej. Poprzednie wersje nie są już dostępne do pobrania i obecnie są przestarzałe.  
   
## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie skryptu U-SQL, Python, R i C Sharp dla usługi Azure Data Lake Analytics w kodzie VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Uruchamiania lokalnego skryptu U-SQL i debugowania lokalnego z kodem Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Samouczek: Rozpoczynanie pracy z usługą Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Samouczek: Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
