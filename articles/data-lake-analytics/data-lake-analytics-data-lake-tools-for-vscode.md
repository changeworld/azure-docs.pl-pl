---
title: Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code
description: Dowiedz się, jak używać narzędzi usługi Azure Data Lake Tools for Visual Studio Code do tworzenia, testowania i uruchamiania skryptów U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60509344"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code

W tym artykule dowiesz się, jak używać narzędzi usługi Azure Data Lake Tools for Visual Studio Code (VS Code) do tworzenia, testowania i uruchamiania skryptów U-SQL. Informacje te są również opisane w poniższym filmie:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzia usługi Azure Data Lake tools for VS Code obsługują systemy Windows, Linux i macOS.Lokalne uruchomienie U-SQL i debugowanie lokalne działa tylko w systemie Windows.

- [Kod programu Visual Studio](https://www.visualstudio.com/products/code-vs.aspx)

Dla Systemów MacOS i Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalowanie narzędzi usługi Azure Data Lake

Po zainstalowaniu wymagań wstępnych można zainstalować narzędzia usługi Azure Data Lake Tools for VS Code.

**Aby zainstalować narzędzia usługi Azure Data Lake**

1. Otwórz program Visual Studio Code.
2. Wybierz **rozszerzenia** w lewym okienku. Wprowadź **narzędzia usługi Azure Data Lake** w polu wyszukiwania.
3. Wybierz **pozycję Zainstaluj** obok pozycji Narzędzia usługi Azure Data Lake **.** 

   ![Wybór instalacji narzędzi usługi Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po kilku sekundach przycisk **Zainstaluj** zmieni się na **Przeładowanie**.
4. Wybierz **ponownie załadować,** aby aktywować rozszerzenie **narzędzia usługi Azure Data Lake.**
5. Wybierz **polecenie Załaduj ponownie okno,** aby potwierdzić. **Narzędzia usługi Azure Data Lake można** wyświetlić w okienku **Rozszerzenia.**

 
## <a name="activate-azure-data-lake-tools"></a>Aktywowanie narzędzi usługi Azure Data Lake
Utwórz plik .usql lub otwórz istniejący plik usql, aby aktywować rozszerzenie. 


## <a name="work-with-u-sql"></a>Praca z U-SQL

Aby pracować z U-SQL, należy otworzyć plik U-SQL lub folder.

**Aby otworzyć przykładowy skrypt**

Otwórz paletę poleceń (Ctrl+Shift+P) i wprowadź **ADL: Otwórz przykładowy skrypt**. Otwiera inne wystąpienie tego przykładu. Można również edytować, konfigurować i przesyłać skrypt w tym wystąpieniu.

**Aby otworzyć folder dla projektu U-SQL**

1. W programie Visual Studio Code wybierz menu **Plik,** a następnie wybierz polecenie **Otwórz folder**.
2. Określ folder, a następnie wybierz pozycję **Wybierz folder**.
3. Wybierz menu **Plik,** a następnie wybierz polecenie **Nowy**. Plik bez tytułu 1 jest dodawany do projektu.
4. Wprowadź następujący kod w pliku Bez tytułu 1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Skrypt tworzy plik departments.csv z niektórymi danymi zawartymi w folderze /output.

5. Zapisz plik jako **myUSQL.usql** w otwartym folderze.

**Aby skompilować skrypt U-SQL**

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Skompiluj skrypt**. Wyniki kompilacji są wyświetlane w oknie **Dane wyjściowe.** Można również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybrać **ADL: Compile Script,** aby skompilować zadanie U-SQL. Wynik kompilacji pojawia się w okienku **Dane wyjściowe.**
 
**Aby przesłać skrypt U-SQL**

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Prześlij zadanie**. Można również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybrać **polecenie ADL: Prześlij zadanie**. 

Po przesłaniu zadania U-SQL dzienniki przesyłania są wyświetlane w oknie **Dane wyjściowe** w programie VS Code. Widok zadania pojawi się w prawym okienku. Jeśli przesłanie zakończy się pomyślnie, pojawi się również adres URL zadania. Możesz otworzyć adres URL zadania w przeglądarce internetowej, aby śledzić stan zadania w czasie rzeczywistym. 

Na karcie **PODSUMOWANIE** w widoku zadania możesz zobaczyć szczegóły zadania. Główne funkcje obejmują ponowne przesłanie skryptu, zduplikowanie skryptu i otwarcie w portalu. Na karcie **DANE** w widoku zadania można odwoływać się do plików wejściowych, plików wyjściowych i plików zasobów. Pliki można pobrać na komputer lokalny.

![Karta Podsumowanie w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Karta Dane w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Aby ustawić kontekst domyślny**

Domyślny kontekst można ustawić tak, aby zastosować to ustawienie do wszystkich plików skryptów, jeśli nie ustawiono parametrów dla plików indywidualnie.

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Ustaw domyślny kontekst**. Możesz też kliknąć prawym przyciskiem myszy edytor skryptów i wybrać pozycję **ADL: Ustaw domyślny kontekst**.
3. Wybierz konto, bazę danych i schemat, który chcesz. Ustawienie zostanie zapisane w pliku konfiguracyjnym xxx_settings.json.

   ![Konto, baza danych i schemat ustawione jako kontekst domyślny](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Aby ustawić parametry skryptu**

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Ustaw parametry skryptu**.
3. Plik xxx_settings.json jest otwierany z następującymi właściwościami:

   - **konto:** konto usługi Azure Data Lake Analytics w ramach subskrypcji platformy Azure, które jest potrzebne do kompilowania i uruchamiania zadań U-SQL. Należy skonfigurować konto komputera przed skompilowaniem i uruchomieniem zadań U-SQL.
   - **baza danych**: Baza danych na twoim koncie. Wartość domyślna to **master**.
   - **schemat**: Schemat w bazie danych. Wartość domyślna to **dbo**.
   - **opcjonalnieStawienia:**
        - **priorytet**: Zakres priorytetów wynosi od 1 do 1000, przy czym 1 jest najwyższym priorytetem. Wartość domyślna to **1000**.
        - **degreeOfParallelism**: Zakres równoległości wynosi od 1 do 150. Wartością domyślną jest maksymalna równoległość dozwolona na koncie usługi Azure Data Lake Analytics. 

   ![Zawartość pliku JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Po zapisaniu konfiguracji informacje o koncie, bazie danych i schemacie są wyświetlane na pasku stanu w lewym dolnym rogu odpowiedniego pliku .usql, jeśli nie masz skonfigurowanego kontekstu domyślnego.

**Aby ustawić ignorowanie git**

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Ustaw Git Ignore**.

   - Jeśli nie masz pliku **.gitIgnore** w folderze roboczym programu VS Code, w folderze zostanie utworzony plik o nazwie **.gitIgnore.** Cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) są domyślnie dodawane w pliku. W razie potrzeby można wprowadzić więcej aktualizacji.
   - Jeśli masz już plik **.gitIgnore** w folderze roboczym VS Code, narzędzie dodaje cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) w pliku **.gitIgnore,** jeśli cztery elementy nie zostały uwzględnione w pliku.

   ![Elementy w pliku gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Praca z plikami związanymi z kodem: C Sharp, Python i R

Narzędzia usługi Azure Data Lake obsługuje wiele kodów niestandardowych. Aby uzyskać instrukcje, zobacz [Tworzenie języka U-SQL z języka Python, R i C Sharp dla usługi Azure Data Lake Analytics w programie VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Praca z złożeniami

Aby uzyskać informacje na temat tworzenia zestawów, zobacz [Tworzenie zestawów U-SQL dla zadań usługi Azure Data Lake Analytics.](data-lake-analytics-u-sql-develop-assemblies.md)

Za pomocą narzędzia Usługi Data Lake Tools można rejestrować zestawy kodu niestandardowego w katalogu usługi Data Lake Analytics.

**Aby zarejestrować zestaw**

Można zarejestrować zestaw za pomocą **polecenia ADL: Register Assembly** lub **ADL: Register Assembly (Advanced).**

**Aby zarejestrować się za pomocą polecenia ADL: Register Assembly**
1.  Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2.  Wprowadź **ADL: Zarejestruj zestaw**. 
3.  Określ ścieżkę zespołu lokalnego. 
4.  Wybierz konto Usługi Data Lake Analytics.
5.  Wybierz bazę danych.

Portal jest otwierany w przeglądarce i wyświetla proces rejestracji zestawu.  

Wygodniejszym sposobem wyzwolenia polecenia **ADL: Register Assembly** jest kliknięcie prawym przyciskiem myszy pliku dll w Eksploratorze plików. 

**Aby zarejestrować się za pomocą polecenia ADL: Register Assembly (Advanced)**
1.  Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2.  Wprowadź **ADL: Zarejestruj zestaw (zaawansowany)**. 
3.  Określ ścieżkę zespołu lokalnego. 
4.  Zostanie wyświetlony plik JSON. Przegląd i edytować zależności zestawu i parametry zasobów, w razie potrzeby. Instrukcje są wyświetlane w oknie **Wyjście.** Aby przejść do rejestracji zestawu, zapisz (Ctrl+S) plik JSON.

    ![Plik JSON z zależnościami zestawu i parametrami zasobów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Narzędzia usługi Azure Data Lake tools automatycznie wytyczuje, czy biblioteka DLL ma żadnych zależności zestawu. Zależności są wyświetlane w pliku JSON po ich wykryciu. 
>- Zasoby biblioteki DLL (na przykład .txt, png i csv) można przekazać jako część rejestracji zestawu. 

Innym sposobem wyzwolenia polecenia **ADL: Register Assembly (Advanced)** jest kliknięcie prawym przyciskiem myszy pliku dll w Eksploratorze plików. 

Poniższy kod U-SQL pokazuje, jak wywołać zestaw. W próbce nazwa zestawu jest *test*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Użyj uruchomienia lokalnego U-SQL i lokalnego debugowania dla użytkowników systemu Windows
Lokalne uruchomienie U-SQL testuje dane lokalne i sprawdza poprawność skryptu lokalnie przed opublikowaniem kodu w usłudze Data Lake Analytics. Za pomocą funkcji debugowania lokalnego można wykonać następujące zadania przed przesłaniem kodu do usługi Data Lake Analytics: 
- Debugowanie kodu C#. 
- Krok po kroku przez kod. 
- Sprawdź poprawność skryptu lokalnie.

Funkcja uruchamiania lokalnego i lokalnego debugowania działa tylko w środowiskach systemu Windows i nie jest obsługiwana w systemach operacyjnych macOS i Linux.

Aby uzyskać instrukcje dotyczące uruchamiania lokalnego i lokalnego debugowania, zobacz [Uruchamianie lokalne U-SQL i debugowanie lokalne za pomocą programu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby można było skompilować i uruchomić skrypty U-SQL w usłudze Data Lake Analytics, należy połączyć się z kontem platformy Azure.

<b id="sign-in-by-command">Aby połączyć się z platformą Azure za pomocą polecenia</b>

1.  Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń. 
2.  Wpisz **ADL: Zaloguj się**. Informacje logowania pojawią się w prawym dolnym dolnośląskim.

    ![Wprowadzanie polecenia logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Powiadomienie o logowanie i uwierzytelnianie](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Wybierz **opcję Kopiuj & Otwórz,** aby otworzyć [stronę logowania](https://aka.ms/devicelogin). Wklej kod do pola, a następnie wybierz pozycję **Kontynuuj**.

    ![Strona logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Postępuj zgodnie z instrukcjami, aby zalogować się na stronie internetowej. Po nawiązaniu połączenia nazwa konta platformy Azure pojawia się na pasku stanu w lewym dolnym rogu okna vs code. 

> [!NOTE] 
>- Data Lake Tools automatycznie podpisze Cię następnym razem, jeśli nie wylogujesz się.
>- Jeśli na twoim koncie włączono dwa czynniki, zalecamy użycie uwierzytelniania telefonicznego zamiast numeru PIN.


Aby się wylogować, wprowadź polecenie **ADL: Wyloguj**.

**Aby połączyć się z platformą Azure za pomocą eksploratora**

Rozwiń pozycję **Azure DATALAKE**, wybierz pozycję **Zaloguj się na platformę Azure**, a następnie wykonaj krok 3 i 4 aby [połączyć się z platformą Azure za pomocą polecenia.](#sign-in-by-command)

![Wybór "Zaloguj się na platformie Azure" w eksploratorze](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nie można wylogować się z eksploratora. Aby się wylogować, zobacz [Aby połączyć się z platformą Azure za pomocą polecenia](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Tworzenie skryptu wyodrębniania 
Skrypt wyodrębniania dla plików csv, tsv i txt można utworzyć za pomocą polecenia **ADL: Create EXTRACT Script** lub z eksploratora usługi Azure Data Lake.

**Aby utworzyć skrypt wyodrębniania za pomocą polecenia**

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Create EXTRACT Script**.
2. Określ pełną ścieżkę dla pliku usługi Azure Storage i wybierz klawisz Enter.
3. Wybierz jedno konto.
4. W przypadku pliku txt wybierz ogranicznik, aby wyodrębnić plik. 

![Proces tworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skrypt wyodrębniania jest generowany na podstawie wpisów. Dla skryptu, który nie może wykryć kolumny, wybierz jedną z dwóch opcji. Jeśli nie, zostanie wygenerowany tylko jeden skrypt.

![Wynik utworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Aby utworzyć skrypt wyodrębniania z eksploratora**

Innym sposobem utworzenia skryptu wyodrębniania jest menu kliknięcie prawym przyciskiem myszy (skrót) w pliku csv, tsv lub txt w usłudze Azure Data Lake Store lub magazynie obiektów Blob platformy Azure.

![Polecenie "Utwórz skrypt WYODRĘBNIJ" z menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integracja z usługą Azure Data Lake Analytics za pomocą polecenia

Możesz uzyskać dostęp do zasobów usługi Azure Data Lake Analytics, aby wyświetlić konta, uzyskać dostęp do metadanych i wyświetlić zadania analizy. 

**Aby wyświetlić listę kont usługi Azure Data Lake Analytics w ramach subskrypcji platformy Azure**

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: Lista kont**. Konta są wyświetlane w okienku **Dane wyjściowe.**

**Aby uzyskać dostęp do metadanych usługi Azure Data Lake Analytics**

1.  Wybierz klawisze Ctrl+Shift+P, a następnie wprowadź **pozycję ADL: List Tables**.
2.  Wybierz jedno z kont Usługi Data Lake Analytics.
3.  Wybierz jedną z baz danych usługi Data Lake Analytics.
4.  Wybierz jeden ze schematów. Możesz zobaczyć listę tabel.

**Aby wyświetlić zadania usługi Azure Data Lake Analytics**
1.  Otwórz paletę poleceń (Ctrl+Shift+P) i wybierz **pozycję ADL: Pokaż zadania**. 
2.  Wybierz konto Usługi Data Lake Analytics lub konto lokalne. 
3.  Poczekaj, aż lista zadań pojawi się dla konta.
4.  Wybierz zadanie z listy zadań. Narzędzia usługi Data Lake Tools otwiera widok zadania w prawym okienku i wyświetla niektóre informacje w danych wyjściowych kodu VS.

    ![Lista ofert pracy](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integracja z usługą Azure Data Lake Store za pomocą polecenia

Za pomocą poleceń związanych z usługą Azure Data Lake Store można użyć do:
 - [Przeglądanie zasobów usługi Azure Data Lake Store](#list-the-storage-path) 
 - [Wyświetlanie podglądu pliku usługi Azure Data Lake Store](#preview-the-storage-file) 
 - Przekaż plik bezpośrednio do magazynu usługi Azure Data Lake Store w programie VS Code
 - Pobierz plik bezpośrednio z usługi Azure Data Lake Store w programie VS Code

### <a name="list-the-storage-path"></a>Wyświetl listę ścieżki magazynowania 

**Aby wyświetlić ścieżkę magazynu za pośrednictwem palety poleceń**

1. Kliknij prawym przyciskiem myszy edytor skryptów i wybierz polecenie **ADL: Ścieżka listy**.
2. Wybierz folder na liście lub wybierz pozycję **Wprowadź ścieżkę** lub **Przeglądaj ze ścieżki głównej**. (Jako przykład używamy **enter ścieżki).** 
3. Wybierz swoje konto Usługi Data Lake Analytics.
4. Przejdź do ścieżki folderu magazynu lub wprowadź go (na przykład /output/).  

Paleta poleceń zawiera listę informacji o ścieżce na podstawie wpisów.

![Wyniki ścieżki magazynowania](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Wygodniejszym sposobem wyświetlenia ścieżki względnej jest menu skrótów.

**Aby wyświetlić ścieżkę przechowywania w menu skrótów**

Kliknij prawym przyciskiem myszy ciąg ścieżki i wybierz polecenie **Ścieżka listy**.

!["Ścieżka listy" w menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Podgląd pliku magazynu

1. Kliknij prawym przyciskiem myszy edytor skryptów i wybierz polecenie **ADL: Podgląd pliku**.
2. Wybierz swoje konto Usługi Data Lake Analytics. 
3. Wprowadź ścieżkę pliku usługi Azure Storage (na przykład /output/SearchLog.txt). 

Plik zostanie otwarty w programie VS Code.

![Kroki i wynik podglądu pliku magazynu](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Innym sposobem podglądu pliku jest menu skrótów w pełnej ścieżce pliku lub ścieżka względna pliku w edytorze skryptów. 

### <a name="upload-a-file-or-folder"></a>Przekazywanie pliku lub folderu

1. Kliknij prawym przyciskiem myszy edytor skryptów i wybierz polecenie **Przekaż plik** lub **Przekaż folder**.
2. Wybierz jeden plik lub wiele plików, jeśli wybrano **opcję Przekaż plik,** lub wybierz cały folder, jeśli wybrano **folder przekazywania**. Następnie wybierz przycisk **Upload** (Przekaż). 
3. Wybierz folder magazynu na liście lub wybierz pozycję **Wprowadź ścieżkę** lub **Przeglądaj ze ścieżki głównej**. (Jako przykład używamy **enter ścieżki).** 
4. Wybierz swoje konto Usługi Data Lake Analytics. 
5. Przejdź do ścieżki folderu magazynu lub wprowadź go (na przykład /output/). 
6. Wybierz **pozycję Wybierz bieżący folder,** aby określić miejsce docelowe przekazywania.

![Kroki i wynik przekazywania pliku lub folderu](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Innym sposobem przekazywania plików do magazynu jest menu skrótów w pełnej ścieżce pliku lub ścieżka względna pliku w edytorze skryptów.

Możesz [monitorować stan przesyłania](#check-storage-tasks-status).


### <a name="download-a-file"></a>Pobieranie pliku 
Plik można pobrać za pomocą polecenia **ADL: Download File** lub **ADL: Download File (Advanced)**.

**Aby pobrać plik za pomocą polecenia ADL: Download File (Advanced)**
1. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz polecenie **Pobierz plik (zaawansowany)**.
2. Vs Code wyświetla plik JSON. Można wprowadzać ścieżki plików i pobierać wiele plików jednocześnie. Instrukcje są wyświetlane w oknie **Wyjście.** Aby przystąpić do pobierania pliku lub plików, zapisz (Ctrl+S) plik JSON.

    ![Plik JSON ze ścieżkami do pobrania pliku](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

Okno **Dane wyjściowe** wyświetla stan pobierania pliku.

![Okno wyjściowe ze stanem pobierania](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Stan pobierania można [monitorować](#check-storage-tasks-status).

**Aby pobrać plik za pomocą polecenia ADL: Download File**

1. Kliknij prawym przyciskiem myszy edytor skryptów, wybierz polecenie **Pobierz plik**, a następnie wybierz folder docelowy z okna dialogowego **Wybieranie folderu.**
2. Wybierz folder na liście lub wybierz pozycję **Wprowadź ścieżkę** lub **Przeglądaj ze ścieżki głównej**. (Jako przykład używamy **enter ścieżki).** 
3. Wybierz swoje konto Usługi Data Lake Analytics. 
4. Przejdź do ścieżki folderu magazynu (na przykład /output/), a następnie wybierz plik do pobrania.

![Kroki i wynik pobierania pliku](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Innym sposobem pobierania plików magazynu jest menu skrótów na pełnej ścieżce pliku lub względna ścieżka pliku w edytorze skryptów.

Stan pobierania można [monitorować](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Sprawdzanie stanu zadań magazynowania
Stan przekazywania i pobierania pojawi się na pasku stanu. Wybierz pasek stanu, a następnie stan pojawi się na karcie **WYJŚCIE.**

![Pasek stanu i szczegóły wyjściowe](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integracja z usługą Azure Data Lake Analytics z eksploratora

Po zalogowaniu wszystkie subskrypcje dla konta platformy Azure są wyświetlane w lewym okienku w obszarze **AZURE DATALAKE**. 

![Eksplorator usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Nawigacja metadanych usługi Data Lake Analytics

Rozwiń swoją subskrypcję platformy Azure. W węźle **Bazy danych U-SQL** można przeglądać bazę danych U-SQL i wyświetlać foldery, takie jak **Schematy, Poświadczenia,** **Zestawy,** **Tabele**i **Indeks**. **Schemas**

### <a name="data-lake-analytics-metadata-entity-management"></a>Zarządzanie jednostkami metadanych usługi Data Lake Analytics

Rozwiń **bazy danych U-SQL**. Można utworzyć bazę danych, schemat, tabelę, typ tabeli, indeks lub statystykę, klikając prawym przyciskiem myszy odpowiedni węzeł, a następnie wybierając **polecenie Skrypt do utworzenia** w menu skrótów. Na otwartej stronie skryptu edytuj skrypt zgodnie z potrzebami. Następnie prześlij zadanie, klikając je prawym przyciskiem myszy i wybierając **polecenie ADL: Prześlij zadanie**. 

Po zakończeniu tworzenia elementu kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Odśwież,** aby wyświetlić element. Element można również usunąć, klikając go prawym przyciskiem myszy, a następnie wybierając polecenie **Usuń**.

![Polecenie "Skrypt do utworzenia" w menu skrótów w eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Strona skryptu dla nowego elementu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Rejestracja zestawu Usługi Data Lake Analytics

Można zarejestrować zestaw w odpowiedniej bazie danych, klikając prawym przyciskiem myszy węzeł **Złożenia,** a następnie wybierając **polecenie Zarejestruj zestaw**.

![Polecenie "Zarejestruj zestaw" w menu skrótów dla węzła Złożenia](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integracja z usługą Azure Data Lake Store z eksploratora

Przejdź do **magazynu Data Lake:**

- W menu skrótów można kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć poleceń **Odśwież**, **Usuń**, **Przekaż** **folder**, **Kopiuj ścieżkę względną**i **Kopiuj pełną ścieżkę.**

   ![Polecenia menu skrótów dla węzła folderu w eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Można kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć poleceń **Podgląd**, **Pobierz**, **Usuń**, **Utwórz skrypt WYODRĘBNIĆ** (dostępny tylko dla plików CSV, TSV i TXT), **Kopiuj ścieżkę względną**i **Kopiuj pełną ścieżkę** w menu skrótów.

   ![Polecenia menu skrótów dla węzła pliku w eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integracja z magazynem obiektów Blob platformy Azure z eksploratora

Przejdź do magazynu obiektów blob:

- Można kliknąć prawym przyciskiem myszy węzeł kontenera obiektów blob, a następnie użyć poleceń **Odśwież**, **Usuń kontener obiektów blob**i Przekaż obiekt **blob** w menu skrótów.

   ![Polecenia menu skrótów dla węzła kontenera obiektu blob w obszarze Magazyn obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Możesz kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć poleceń **Odśwież** i **Przekaż obiekt blob** w menu skrótów.

   ![Polecenia menu skrótów dla węzła folderu w obszarze Magazyn obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Można kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć polecenia **Podgląd/Edycja**, **Pobierz**, **Usuń**, **Utwórz skrypt WYODRĘBNIĆ** (dostępny tylko dla plików CSV, TSV i TXT), **Kopiuj ścieżkę względną**i **Kopiuj pełną ścieżkę** w menu skrótów.

    ![Polecenia menu skrótów dla węzła plików w obszarze Magazyn obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Otwieranie eksploratora usługi Data Lake w portalu
1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **Open Web Azure Storage Explorer** lub kliknij prawym przyciskiem myszy ścieżkę względną lub pełną ścieżkę w edytorze skryptów, a następnie wybierz pozycję Otwórz **Eksploratora usługi Azure w sieci Web**.
3. Wybierz konto Usługi Data Lake Analytics.

Narzędzia usługi Data Lake Tools otwierają ścieżkę usługi Azure Storage w witrynie Azure portal. Ścieżkę można znaleźć i wyświetlić podgląd pliku z sieci Web.

## <a name="additional-features"></a>Dodatkowe funkcje

Narzędzia usługi Data Lake Tools for VS Code obsługują następujące funkcje:

-   **Autouzupełniania IntelliSense: Sugestie**pojawiają się w oknach podręcznych wokół elementów, takich jak słowa kluczowe, metody i zmienne. Różne ikony reprezentują różne typy obiektów:

    - Typ danych Scala
    - Złożony typ danych
    - Wbudowane udts
    - Kolekcja i klasy .NET
    - Wyrażenia C#
    - Wbudowane pliki UDF, UDO i UDAAGs 
    - Funkcje U-SQL
    - Funkcje okienowania U-SQL
 
    ![Typy obiektów IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Autouzupełnienie intellisense na metadanych usługi Data Lake Analytics:** Data Lake Tools pobiera informacje o metadanych Data Lake Analytics lokalnie. Funkcja IntelliSense automatycznie wypełnia obiekty z metadanych usługi Data Lake Analytics. Obiekty te obejmują bazę danych, schemat, tabelę, widok, funkcję wycenioną w tabeli, procedury i zestawy C#.
 
    ![Metadane IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Znacznik błędu IntelliSense:** Data Lake Tools podkreśla błędy edycji u-SQL i C#. 
-   Najważniejsze informacje o **składni:** Narzędzia jeziora danych używają kolorów do rozróżniania elementów, takich jak zmienne, słowa kluczowe, typy danych i funkcje. 

    ![Składnia w różnych kolorach](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Zaleca się uaktualnienie do usługi Azure Data Lake Tools dla programu Visual Studio w wersji 2.3.3000.4 lub nowszej. Poprzednie wersje są niedostępne do pobrania i przestarzałe.  
   
## <a name="next-steps"></a>Następne kroki
- [Tworzenie języka U-SQL za pomocą języka Python, R i C Sharp dla usługi Azure Data Lake Analytics w programie VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokalne uruchomienie U-SQL i debugowanie lokalne za pomocą kodu programu Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Samouczek: Wprowadzenie do usługi Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Samouczek: Tworzenie skryptów U-SQL przy użyciu narzędzi usługi Data Lake Tools dla programu Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
