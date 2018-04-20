---
title: 'Usługi Azure Data Lake Tools: Użycia usługi Azure Data Lake Tools dla programu Visual Studio Code | Dokumentacja firmy Microsoft'
description: 'Dowiedz się, jak Azure Data Lake Tools dla Visual Studio Code umożliwia tworzenie, testowanie i uruchamianie skryptów U-SQL. '
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: ''
author: Jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: jejiang
ms.openlocfilehash: fcd821c91a8c94792eeed83940abe1c72d0b2fb8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Użyj usługi Azure Data Lake Tools dla programu Visual Studio Code

Omówienie narzędzi Azure Data Lake Tools dla programu Visual Studio (kod VS) do tworzenia, testowania i uruchamiać skrypty U-SQL. Informacje są także omówione w poniższym klipie wideo:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Wymagania wstępne

Azure Data Lake Tools for VSCode obsługuje Windows, Linux lub MacOS.  

- [Program Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

System MacOS i Linux:
- [Oprogramowanie .NET core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Instalowanie narzędzi Data Lake Tools

Po zainstalowaniu wymagań wstępnych, można zainstalować narzędzi Data Lake Tools dla programu VS kodu.

**Aby zainstalować narzędzia Data Lake Tools**

1. Otwórz program Visual Studio Code.
2. Kliknij przycisk **rozszerzenia** w okienku po lewej stronie. Wprowadź **usługi Azure Data Lake** w polu wyszukiwania.
3. Kliknij przycisk **zainstalować** obok **usługi Azure Data Lake tools**. Po kilku sekundach **zainstalować** przycisk zostanie zmieniony na **Załaduj ponownie**.
4. Kliknij przycisk **Załaduj ponownie** aktywować **usługi Azure Data Lake tools** rozszerzenia.
5. Kliknij przycisk **ponownego załadowania okna** o potwierdzenie. Widać **usługi Azure Data Lake tools** w okienku rozszerzenia.

    ![Narzędzia Data Lake Tools dla okienka rozszerzenia kodu programu Visual Studio](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Aktywacja usługi Azure Data Lake Tools
Utwórz nowy plik .usql lub Otwórz istniejący plik .usql aktywować rozszerzenia. 

## <a name="open-the-sample-script"></a>Otwórz przykładowy skrypt
Otwórz palety polecenia (Ctrl + Shift + P), a następnie wprowadź **ADL: Otwórz przykładowy skrypt**. Otwiera inne wystąpienie tego przykładu. Można również edytować, skonfigurować i przesłać skrypt w tym wystąpieniu.

## <a name="work-with-u-sql"></a>Praca z języka U-SQL

Należy otworzyć plik U-SQL lub folder do pracy w języku U-SQL.

**Aby otworzyć folder projektu U-SQL**

1. W programie Visual Studio Code, wybierz **pliku** menu, a następnie wybierz **Otwórz Folder**.
2. Określ folder, a następnie wybierz **wybierz Folder**.
3. Wybierz **pliku** menu, a następnie wybierz **nowy**. Plik 1 bez tytułu zostanie dodany do projektu.
4. Wprowadź następujący kod do pliku bez tytułu 1:

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
2. Wprowadź **ADL: przesłać zadania**.  Można również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybierz **ADL: Prześlij zadanie**. 

 Po przesłaniu zadania skryptu U-SQL w dziennikach przesyłania są wyświetlane w **dane wyjściowe** okna w kodzie VS. Wyświetl zadania jest wyświetlany w okienku po prawej stronie. Jeśli przesyłanie zakończy się pomyślnie, zadanie adres URL pojawia się również. Adres URL zadania można otworzyć w przeglądarce sieci web, aby śledzić stan zadania w czasie rzeczywistym. Na karcie zadania Wyświetl podsumowanie można wyświetlić szczegóły zadania. Główne funkcje obejmują ponownie prześlij skrypt, zduplikowany skryptu, Otwórz w portalu. Na karcie Dane widoku zadania mogą odwoływać się do plików danych wejściowych, pliki wyjściowe, zasobów. Plików może być pobierany na komputerze lokalnym.

   ![Narzędzia Data Lake Tools dla Visual Studio Code pliku konfiguracji](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![Narzędzia Data Lake Tools dla Visual Studio Code pliku konfiguracji](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Ustaw domyślny kontekst**

 Możesz ustawić domyślny kontekst do zastosowania tego ustawienia do wszystkich plików skryptów, jeśli nie zostały ustawione parametry dla pliku odpowiednio.

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: Ustaw domyślny kontekst**.
3. Lub kliknij prawym przyciskiem myszy edytora skryptów i wybierz polecenie **ADL: Ustaw domyślny kontekst**, a następnie wybierz konto, bazę danych i schemat ma. To ustawienie jest zapisane do pliku konfiguracji xxx_settings.json.

    ![Narzędzia Data Lake Tools dla Visual Studio Code pliku konfiguracji](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Ustaw parametry skryptu**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: Ustaw parametry skryptu**.
3. xxx_settings.JSON plik jest otwarty z następującymi właściwościami:

  - Konto: Konta Data Lake Analytics w ramach Twojej subskrypcji platformy Azure, który jest wymagany do kompilowanie i uruchamianie zadań U-SQL, dlatego należy skonfigurować konto komputera przed kompilowania i uruchamiania zadań U-SQL.
    - Baza danych: Baza danych przy użyciu tego konta. Wartość domyślna to **wzorca**.
    - Schemat: Schemat w bazie danych. Wartość domyślna to **dbo**.
    - Ustawienia opcjonalne:
        - Priorytet: Zakres priorytetu jest z zakresu od 1 do 1000 z 1 oznacza najwyższy priorytet. Wartość domyślna to **1000**.
        - Równoległość: Zakres równoległości jest z zakresu od 1 do 150. Wartość domyślna to równoległości maksymalny dozwolony na koncie usługi Azure Data Lake Analytics. 

        ![Narzędzia Data Lake Tools dla Visual Studio Code pliku konfiguracji](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > Po zapisaniu konfiguracji konta, bazy danych i informacji o schemacie są wyświetlane na pasku stanu, w lewym dolnym rogu odpowiedniego pliku .usql Jeśli nie masz domyślny kontekst Konfigurowanie.

**Ignoruj Git zestawu**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2. Wprowadź **ADL: Ignoruj Git zestawu**.

    - Jeśli nie masz **.gitIgnore** plik w folderze pracy VSCode plik o nazwie **.gitIgnor** jest tworzony w folderze. Cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) są domyślnie dodawane do pliku. Aktualizacje można tworzyć dodatkowe, w razie potrzeby.
    - Jeśli masz już **.gitIgnore** plik w folderze pracy VSCode narzędzie dodaje cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) do sieci **.gitIgnore** plik, jeśli cztery elementy nie zostały uwzględnione w pliku.

    ![Narzędzia Data Lake Tools dla Visual Studio Code pliku konfiguracji](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Użyj pliku CodeBehind Python, R i CSharp
Azure Data Lake narzędzie obsługuje wiele kodów niestandardowych, zobacz instrukcje [opracowanie U-SQL z języka Python, R i CSharp dla usługi Azure Data Lake Analytics w VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="use-assemblies"></a>Korzystanie z zestawów

Aby uzyskać informacje na temat tworzenia zespołów, zobacz [opracowanie U-SQL zestawy dla usługi Azure Data Lake Analytics zadań](data-lake-analytics-u-sql-develop-assemblies.md).

Data Lake Tools służy do rejestrowania zestawów niestandardowych kodów w katalogu Data Lake Analytics.

**Można zarejestrować zestawu**

Można zarejestrować zestawu za pomocą **ADL: Zarejestruj zestawu** lub **ADL: Zarejestruj zestawu (zaawansowane)** poleceń.

**Aby zarejestrować za pomocą ADL: polecenie zarejestrować zestawu**
1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2.  Wprowadź **ADL: zarejestrować zestawu**. 
3.  Określ ścieżkę zestawu lokalnego. 
4.  Wybierz konto usługi Data Lake Analytics.
5.  Wybierz bazę danych.

Wyniki: Portal jest otwarty w przeglądarce i przedstawia proces rejestracji zestawu.  

Inny wygodny sposób, aby wyzwolić **ADL: Zarejestruj zestawu** polecenie jest kliknij prawym przyciskiem myszy plik .dll w Eksploratorze plików. 

**Aby zarejestrować jednak ADL: Zarejestruj zestawu (zaawansowane)**
1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2.  Wprowadź **ADL: zarejestrować zestawu (zaawansowane)**. 
3.  Określ ścieżkę zestawu lokalnego. 
4.  Plik JSON jest wyświetlany. Przejrzyj i Edytuj zależności zestawu i parametry zasobu, jeśli to konieczne. Instrukcje są wyświetlane w **dane wyjściowe** okna. Aby przejść do rejestrowania zestawów, Zapisz (Ctrl + S) w pliku JSON.

    ![Narzędzia Data Lake Tools dla Visual Studio Code kodem](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- Zależności zestawu: automatycznie wykrywa Azure Data Lake Tools Określa, czy biblioteka DLL nie ma żadnych zależności. Zależności są wyświetlane w pliku JSON, po ich wykryciu. 
   >- Zasoby: Można przekazać biblioteki DLL zasobów (na przykład txt, .png i .csv) jako część zestawu rejestracji. 

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


## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Można było skompilować i uruchomić skrypty U-SQL w usługi Data Lake Analytics, należy połączyć z kontem platformy Azure.

**Do nawiązania połączenia platformy Azure**

1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia. 
2.  Wprowadź **ADL: logowania**. Dane logowania zostanie wyświetlony w obszarze top.

    ![Data Lake Tools dla Visual Studio Code polecenia palety](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![narzędzi Data Lake Tools dla Visual Studio Code informacji o logowaniu urządzenia](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  Kliknij przycisk **skopiuj & Otwórz** aby otworzyć stronę sieci Web logowania z adresem URL: https://aka.ms/devicelogin. Wklej kod **G567LX42V** w polu tekstowym, a następnie wybierz **Kontynuuj**.

   ![Narzędzia Data Lake Tools dla Visual Studio Code logowania Wklej kod](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Postępuj zgodnie z instrukcjami, aby zalogować się w stronę sieci Web. Po nawiązaniu połączenia nazwa konta platformy Azure jest wyświetlana na pasku stanu, w lewym dolnym rogu **kodzie VS** okna. 

    > [!NOTE] 
    >- Data Lake narzędzia loguje następnym automatycznie, jeśli użytkownik jest zarejestrowany, ale użytkownik nie zalogował jeszcze.
    >- Jeśli konto ma dwa składniki włączone, zalecane jest użycie z telefonu uwierzytelniania, a nie za pomocą numeru PIN.


Aby się wylogować, wprowadź polecenie **ADL: wylogowania**.

## <a name="list-your-data-lake-analytics-accounts"></a>Lista kont usługi Data Lake Analytics

Aby przetestować połączenie, wyświetlenie listy kont usługi Data Lake Analytics.

**Aby wyświetlić listę kont usługi Data Lake Analytics w ramach Twojej subskrypcji platformy Azure**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2. Wprowadź **ADL: listy kont**. Konta są wyświetlane w **dane wyjściowe** okienka.


## <a name="access-the-data-lake-analytics-catalog"></a>Dostęp do katalogu Data Lake Analytics

Po podłączeniu do platformy Azure, służy następujące kroki, aby uzyskać dostęp do katalogu U-SQL.

**Aby uzyskać dostępu do metadanych usługi Azure Data Lake Analytics**

1.  Wybierz kombinację klawiszy Ctrl + Shift + P, a następnie wprowadź **ADL: listy tabel**.
2.  Wybierz jedno z kont usługi Data Lake Analytics.
3.  Wybierz jedną z bazy danych usługi Data Lake Analytics.
4.  Wybierz jeden ze schematów. Można wyświetlić listę tabel.

## <a name="view-data-lake-analytics-jobs"></a>Widok Data Lake Analytics zadania

**Aby wyświetlić zadania usługi Data Lake Analytics**
1.  Otwórz palety polecenia (Ctrl + Shift + P) i wybierz **ADL: Pokaż zadania**. 
2.  Wybierz usługi Data Lake Analytics lub konta lokalnego. 
3.  Poczekaj na liście zadania konta są wyświetlane.
4.  Wybierz zadanie z listy zadań, narzędzi Data Lake Tools spowoduje otwarcie widoku zadania w okienku po prawej stronie, a niektóre informacje w kodzie VS **dane wyjściowe**.

    ![Typy obiektów narzędzi Data Lake Tools dla programu Visual Studio kodu IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Integracja magazynu usługi Azure Data Lake

Można użyć poleceń usługi Azure Data Lake Storage, aby:
 - Przeglądaj zasoby usługi Azure Data Lake Storage. [Lista ścieżki do magazynu](#list-the-storage-path). 
 - Wyświetl podgląd pliku usługi Azure Data Lake Storage. [Podgląd pliku magazynu](#preview-the-storage-file). 
 - Przekaż plik bezpośrednio do usługi Azure Data Lake Storage w kodzie VS. [Przekaż plik lub folder](#upload-file-or-folder).
 - Pobierz plik bezpośrednio z usługi Azure Data Lake Storage w kodzie VS. [Pobierz plik](#download-file).

### <a name="list-the-storage-path"></a>Lista ścieżki do magazynu 

**Aby wyświetlić listę ścieżki magazynu za pomocą polecenia palety**

Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **ADL: ścieżka listy**.

Wybierz folder z listy lub kliknij przycisk **wprowadź ścieżkę** lub **przejść z głównego** (używa wprowadź ścieżkę jako przykład). -> Wybierz Twoje **konta ADLA**. -> Navigate lub wprowadź ścieżkę do folderu magazynu (na przykład: / wyjście /). -> Informacje o ścieżce na podstawie wpisów list poleceń palety.

![Narzędzia Data Lake Tools dla Visual Studio Code lista wyników ścieżki magazynu](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Bardziej wygodne ścieżka względna jest za pośrednictwem menu kontekstowym kliknij prawym przyciskiem myszy.

**Aby wyświetlić listę ścieżki magazynu za pośrednictwem kliknij prawym przyciskiem myszy**

Kliknij prawym przyciskiem myszy ciąg ścieżki, aby wybrać **Ścieżka listy** aby kontynuować.

![Narzędzia Data Lake Tools dla Visual Studio Code kliknij prawym przyciskiem myszy menu kontekstowe](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Podgląd pliku magazynu

Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **ADL: Podgląd pliku**.

Wybierz użytkownika **konta ADLA**. -> Wprowadź ścieżkę pliku magazynu Azure (na przykład /output/SearchLog.txt). -> Wynik: plik zostanie otwarty w VSCode.

   ![Narzędzia Data Lake Tools dla Visual Studio Code Podgląd pliku wyników](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Innym sposobem Podgląd pliku jest za pomocą menu kliknij prawym przyciskiem myszy na pełną ścieżkę pliku lub ścieżki względnej pliku w Edytorze skryptów. 

### <a name="upload-file-or-folder"></a>Przekaż plik lub folder

1. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **Przekaż plik** lub **przekazać folderu**.

2. Wybierz jednego lub wielu plików, jeśli przekazywanie wybierz plik, lub wybierz cały folder, jeśli wybierz folder przekazywania następnie kliknij przycisk **przekazać**. -> Wybierz folder magazynu na liście, lub kliknij przycisk **wprowadź ścieżkę** lub **przejść z głównego** (używa wprowadź ścieżkę jako przykład). -> Wybierz Twoje **konta ADLA**. -> Navigate lub wprowadź ścieżkę do folderu magazynu (na przykład: / wyjście /). -> kliknij **Wybierz bieżący Folder** do określenia folderu docelowego przekazywania.

   ![Narzędzia Data Lake Tools dla Visual Studio Code przekazać stanu](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Innym sposobem przekazywania plików do magazynu jest za pomocą menu kliknij prawym przyciskiem myszy na pełną ścieżkę pliku lub ścieżki względnej pliku w Edytorze skryptów.

Jednocześnie, można monitorować [stan przekazywania](#check-storage-tasks-status).


### <a name="download-file"></a>Pobierz plik 
Pliki można pobrać przez wprowadzenie polecenia **ADL: Pobierz plik** lub **ADL: Pobierz plik (zaawansowane)**.

**Aby pobrać jednak plików ADL: Pobierz plik (zaawansowane)**
1. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz **Pobierz plik (zaawansowane)**.
2. VS kod przedstawia plik JSON. Można wprowadzić ścieżki do pliku i pobrać wiele plików w tym samym czasie. Instrukcje są wyświetlane w **dane wyjściowe** okna. Aby kontynuować, aby pobrać plik, Zapisz (Ctrl + S) w pliku JSON.

    ![Narzędzia Data Lake Tools dla programu Visual Studio Code Pobierz pliki z konfiguracji](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Wyniki: **dane wyjściowe** okno wyświetla stan przekazywania plików.

    ![Data Lake Tools dla programu Visual Studio Code Pobierz wiele plików wyników](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Jednocześnie, można monitorować [pobieranie stanu](#check-storage-tasks-status).

**Mimo że pobierania plików ADL: Pobierz plik**

1. Kliknij prawym przyciskiem myszy edytora skryptów, wybierz **Pobierz plik**, a następnie wybierz folder docelowy z **wybierz Folder** okna dialogowego.

2. Wybierz folder z listy lub kliknij przycisk **wprowadź ścieżkę** lub **przejść z głównego** (używa wprowadź ścieżkę jako przykład). -> Wybierz Twoje **konta ADLA**. -> Navigate lub wprowadź ścieżkę do folderu magazynu (na przykład: / wyjście /) -> Wybierz plik do pobrania.

   ![Stan pobierania narzędzia Data Lake Tools dla Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Innym sposobem pobierania magazyn plików jest za pomocą menu kliknij prawym przyciskiem myszy na pełną ścieżkę pliku lub ścieżki względnej pliku w Edytorze skryptów.

Jednocześnie, można monitorować [pobieranie stanu](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Sprawdź stan zadania magazynu
Stan jest wyświetlany w dolnej części paska stanu, po zakończeniu pobierania i przekazywania.
1. Kliknij przycisk poniżej paska stanu, a następnie wyświetlać pobieranie i stan przekazywania w **dane wyjściowe** panelu.

   ![Narzędzia Data Lake Tools dla programu Visual Studio Code Sprawdź magazynu stanu](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Eksplorator VSCode Integracja z usługą Azure Data Lake

**Integracja Azure** 

- Przed logowania do platformy Azure, zawsze można rozwinąć **AZURE DATALAKE**, następnie kliknij przycisk **logowanie do platformy Azure** do logowania do platformy Azure. Po logowania, będą widzieli wszystkie subskrypcje w ramach konta platformy Azure są wyświetlane w lewym panelu **AZURE DATALAKE**. 

   ![Usługa DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![Usługa DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**ADLA metadanych nawigacji**

- Rozwiń subskrypcji platformy Azure, można przejść U-SQL bazy danych, widok **schematy**, **poświadczenia**, **zestawy**, **tabeli**, **Indeksu**i tak dalej w węźle U-SQL bazy danych.

**Zarządzanie metadanych jednostki ADLA**

- Rozwiń węzeł **U-SQL bazy danych**, można utworzyć nową bazę danych, schematu, tabeli, typach tabel, indeks, statystyki, klikając prawym przyciskiem myszy **skrypt w celu tworzenia** menu kontekstowego w węźle odpowiednie. Na stronie otwartą skryptu edytowanie skryptu zgodnie z potrzebami, a następnie przesłać zadanie, klikając prawym przyciskiem myszy menu kontekstowe **ADL: Prześlij zadanie**. Po zakończeniu go utworzyć, kliknij menu kontekstowe **Odśwież** wyświetlenie nowego elementu utworzony. Możesz także usunąć element, klikając prawym przyciskiem myszy menu kontekstowe **usunąć**.

   ![Usługa DataLake explorer tworzy nowy element menu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![Eksploratora danych powoduje utworzenie nowego elementu skryptu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**ADLA rejestracji zestawu**

 - Możesz **zarejestrować zestawu** do odpowiedniej bazy danych, klikając prawym przyciskiem myszy **zestawy** węzła.

    ![Usługa DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**Integracja ADLS** 

Przejdź do **usługi Data Lake Store**

 - W węźle folderu można **Odśwież**, **usunąć**, **przekazać**, **przekazać folderu**, **Kopiuj ścieżkę względną**, **Kopiuj pełną ścieżkę** w menu kontekstowym kliknij prawym przyciskiem myszy.

   ![Usługa DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

 - W węźle pliku możesz **Pobierz**, **Podgląd**, **usunąć**, **Kopiuj ścieżkę względną**, **Kopiuj pełną ścieżkę**w menu kontekstowym kliknij prawym przyciskiem myszy. 

   ![Usługa DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

**Integracja WASB**

Przejdź do **magazynu obiektów Blob**

- W węźle kontenera obiektów blob można **Odśwież**, **usunąć kontener obiektów Blob**, **przekazania obiektu Blob** w menu kontekstowym kliknij prawym przyciskiem myszy.

    ![Węzeł kontenera obiektów blob magazynu obiektów blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- W węźle folderu można **Odśwież**, **przekazania obiektu Blob** w menu kontekstowym kliknij prawym przyciskiem myszy.

    ![Węzeł folderu magazynu obiektów blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- W węźle pliku możesz **Podgląd i edytować**, **Pobierz**, **usunąć**, **Kopiuj ścieżkę względną**, **Kopiuj pełną ścieżkę** w menu kontekstowym kliknij prawym przyciskiem myszy.

    ![Węzeł pliku magazynu obiektów blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-file-node.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Otwórz Eksploratora usługi storage ADL w portalu
1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia.
2. Wprowadź **Otwórz Eksploratora usługi Storage Azure Web** lub kliknij prawym przyciskiem myszy na ścieżkę względną lub pełną ścieżkę w Edytorze skryptów, a następnie wybierz **Otwórz Eksploratora usługi Storage Azure Web**.
3. Wybierz konto usługi Data Lake Analytics.

Narzędzia Data Lake Tools otwiera ścieżki do magazynu Azure w portalu Azure. Można znaleźć ścieżki i wyświetlić podgląd pliku z sieci web.

## <a name="local-run-and-local-debug-for-windows-users"></a>Przebieg lokalny i lokalny debugowania dla systemu Windows użytkownicy
Uruchamiania lokalnego skryptu U-SQL testów danych lokalnych i weryfikuje skrypt lokalnie, przed opublikowaniem kodu do usługi Data Lake Analytics. Funkcja debugowania lokalnego umożliwia wykonywanie następujących zadań przed przesłaniem do usługi Data Lake Analytics kodu: 
- Debugowanie programu C# związane z kodem. 
- Kroków opisanych w kodzie. 
- Sprawdź poprawność skrypt lokalnie.

Aby uzyskać instrukcje dotyczące przebiegu lokalnego i debugowania lokalnego, zobacz [uruchamiania lokalnego skryptu U-SQL i debugowania lokalnego z kodem Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Dodatkowe funkcje

Narzędzia Data Lake Tools dla programu VS kodu obsługuje następujące funkcje:

-   Funkcja automatycznego uzupełniania IntelliSense: sugestie są wyświetlane w wyskakujące wokół elementów, takich jak słowa kluczowe, metod i zmiennych. Inne ikony reprezentują różnych typów obiektów:

    - Scala — typ danych
    - Typ danych złożonych
    - Wbudowanych typów
    - Klasy i kolekcji .NET
    - Wyrażenia języka C#
    - Funkcje UDF wbudowane C#, obiektów udo i UDAAGs 
    - Funkcje języka U-SQL
    - Funkcją okienkową U-SQL
 
    ![Typy obiektów narzędzi Data Lake Tools dla programu Visual Studio kodu IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Funkcja automatycznego uzupełniania w metadanych usługi Data Lake Analytics IntelliSense: narzędzi Data Lake Tools pobierze lokalnie informacje metadanych usługi Data Lake Analytics. Funkcja IntelliSense automatycznie wypełni obiektów, łącznie z bazy danych, schematu, tabeli, widoku, funkcji zwracającej tabelę, procedury i zestawów języka C# z metadanych usługi Data Lake Analytics.
 
    ![Narzędzia Data Lake Tools dla programu Visual Studio kodu IntelliSense metadanych](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Znacznik błąd IntelliSense: narzędzi Data Lake Tools podkreśla błędy edytowania U-SQL i C#. 
-   Najważniejsze funkcje Składnia: narzędzi Data Lake Tools używa różne kolory do odróżnienia elementów, takich jak zmienne, słowa kluczowe, typ danych i funkcji. 

    ![Zawiera opis narzędzi Data Lake Tools dla Visual Studio Code składni](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie skryptu U-SQL, Python, R i CSharp dla usługi Azure Data Lake Analytics w VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Uruchamiania lokalnego skryptu U-SQL i debugowania lokalnego z kodem Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Samouczek: Rozpoczynanie pracy z usługą Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Samouczek: Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Tworzenie zestawów języka U-SQL do zadania usługi Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)




