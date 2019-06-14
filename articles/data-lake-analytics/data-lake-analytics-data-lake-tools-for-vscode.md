---
title: Użyj usługi Azure Data Lake Tools for Visual Studio Code
description: Dowiedz się, jak używać usługi Azure Data Lake Tools for Visual Studio Code do tworzenia, testowania i uruchamiania skryptów U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509344"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Użyj usługi Azure Data Lake Tools for Visual Studio Code

W tym artykule Dowiedz się, jak można używać usługi Azure Data Lake Tools for Visual Studio Code (VS Code) do tworzenia, testowania i uruchomić skrypty U-SQL. Informacje są również omówione w poniższym klipie wideo:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Wymagania wstępne

Azure Data Lake Tools dla programu VS Code obsługuje Windows, Linux i macOS. U-SQL działają i lokalnego debugowania lokalnego działa tylko w Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

W przypadku systemu MacOS i Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalowanie narzędzi Azure Data Lake Tools

Po zainstalowaniu wymagań wstępnych, można zainstalować narzędzi usługi Azure Data Lake Tools dla programu VS Code.

**Aby zainstalować narzędzia Azure Data Lake Tools**

1. Otwórz program Visual Studio Code.
2. Wybierz **rozszerzenia** w okienku po lewej stronie. Wprowadź **Azure Data Lake Tools** w polu wyszukiwania.
3. Wybierz **zainstalować** obok **Azure Data Lake Tools**. 

   ![Wybory dotyczące instalowania narzędzi Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po kilku sekundach **zainstalować** przycisku zmienia się na **Załaduj ponownie**.
4. Wybierz **Załaduj ponownie** aktywować **Azure Data Lake Tools** rozszerzenia.
5. Wybierz **Reload Window** o potwierdzenie. Możesz zobaczyć **Azure Data Lake Tools** w **rozszerzenia** okienka.

 
## <a name="activate-azure-data-lake-tools"></a>Aktywacja usługi Azure Data Lake Tools
Utwórz plik .usql lub Otwórz istniejący plik .usql uaktywnienie rozszerzenia. 


## <a name="work-with-u-sql"></a>Praca z języka U-SQL

Aby pracować z języka U-SQL, należy otworzyć U-SQL pliku lub folderu.

**Aby otworzyć przykładowy skrypt**

Otwórz paletę poleceń (Ctrl + Shift + P), a następnie wprowadź **ADL: Otwórz przykładowy skrypt**. Spowoduje to otwarcie inne wystąpienie tego przykładu. Można również edytować, skonfiguruj i Prześlij skrypt w tym wystąpieniu.

**Aby otworzyć folder dla projektu U-SQL**

1. W programie Visual Studio Code wybierz **pliku** menu, a następnie wybierz pozycję **Otwórz Folder**.
2. Określ folder, a następnie wybierz **wybierz Folder**.
3. Wybierz **pliku** menu, a następnie wybierz pozycję **New**. Plik bez tytułu 1 zostanie dodany do projektu.
4. Wprowadź następujący kod w pliku bez tytułu — 1:

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

    Skrypt tworzy plik departments.csv dane zawarte w folderze/Output.

5. Zapisz plik jako **myUSQL.usql** w otwartym folderze.

**Aby skompilować skrypt U-SQL**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Wykonanie skryptu kompilacji**. Wyniki kompilacji są wyświetlane w **dane wyjściowe** okna. Można również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybierz **ADL: Wykonanie skryptu kompilacji** skompilować zadania U-SQL. Wynik kompilacji pojawia się w **dane wyjściowe** okienka.
 
**Aby przesłać skrypt U-SQL**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Prześlij zadanie**. Można również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybierz **ADL: Prześlij zadanie**. 

Po przesłaniu zadania U-SQL w dziennikach przesyłania są wyświetlane w **dane wyjściowe** okna w programie VS Code. Widok zadania jest wyświetlany w okienku po prawej stronie. Jeśli przesyłanie zakończy się pomyślnie, zbyt zostanie wyświetlony adres URL zadania. Adres URL zadania można otworzyć w przeglądarce sieci web, aby śledzić stan zadania w czasie rzeczywistym. 

W widoku zadania **Podsumowanie** kartę, aby wyświetlić szczegóły zadania. Główne funkcje obejmują ponownie prześlij skrypt, zduplikowane skryptu i Otwórz w portalu. W widoku zadania **danych** karcie, można się odwoływać do plików wejściowych, pliki wyjściowe i pliki zasobów. Pliki można pobrać na komputer lokalny.

![Karta Podsumowanie w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Na karcie dane w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Aby ustawić domyślny kontekst**

Możesz ustawić domyślny kontekst, aby zastosować to ustawienie do wszystkich plików skryptu, jeśli nie ustawiono parametrów dla plików pojedynczo.

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Ustaw domyślny kontekst**. Lub kliknij prawym przyciskiem myszy w Edytorze skryptów i wybierz polecenie **ADL: Ustaw domyślny kontekst**.
3. Wybierz konto, bazę danych i schemat, który chcesz. Ustawienia są zapisywane w pliku konfiguracji xxx_settings.json.

   ![Konto, bazę danych i Ustaw jako domyślny kontekst schematu](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Aby ustawić parametry skryptu**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Ustaw parametry skryptu**.
3. Plik xxx_settings.json jest otwierany z następującymi właściwościami:

   - **Konto**: Konto usługi Azure Data Lake Analytics w ramach subskrypcji platformy Azure, które jest potrzebne, aby skompilować i uruchomić zadania U-SQL. Należy skonfigurować konto komputera, przed kompilowanie i uruchamianie zadań U-SQL.
   - **Baza danych**: Baza danych w ramach konta usługi. Wartość domyślna to **wzorca**.
   - **Schemat**: Schemat w ramach bazy danych. Wartość domyślna to **dbo**.
   - **optionalSettings**:
        - **Priorytet**: Zakres priorytetu jest z zakresu od 1 do 1000, gdzie 1 oznacza najwyższy priorytet. Wartość domyślna to **1000**.
        - **degreeOfParallelism**: Zakres równoległości jest z zakresu od 1 do 150. Wartość domyślna to równoległości maksymalny dozwolony na Twoim koncie usługi Azure Data Lake Analytics. 

   ![Zawartość pliku JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Po zapisaniu konfiguracji konta, bazy danych i informacji o schemacie są wyświetlane na pasku stanu, w lewym dolnym rogu odpowiedni plik .usql Jeśli nie masz, aby skonfigurować domyślny kontekst.

**Aby ustawić Git Ignoruj**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń. 
2. Wprowadź **ADL: Ustaw Git Ignoruj**.

   - Jeśli nie masz **.gitIgnore** pliku w folderze roboczym programu VS Code w pliku o nazwie **.gitIgnore** jest tworzony w folderze. Cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) są domyślnie dodawane w pliku. Istnieje możliwość więcej aktualizacji, jeśli to konieczne.
   - Jeśli masz już **.gitIgnore** plik w folderze roboczym programu VS Code, narzędzie dodaje cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) w swojej **.gitIgnore** plik, jeśli cztery elementy nie zostały uwzględnione w pliku.

   ![Elementy w pliku .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Praca z plikami związanymi z kodem: C Sharp, Python i R

Azure Data Lake Tools obsługują wiele niestandardowych kodów. Aby uzyskać instrukcje, zobacz [opracowywanie U-SQL przy użyciu języka Python, R i języka C Sharp dla usługi Azure Data Lake Analytics w programie VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Praca z zestawami

Aby uzyskać informacje na temat tworzenia zestawów, zobacz [zestawy opracowywanie U-SQL dla zadania usługi Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Narzędzia Data Lake Tools służy do rejestrowania zestawów kodu niestandardowego w wykazie usługi Data Lake Analytics.

**Można zarejestrować zestawu**

Można zarejestrować zestawu za pomocą **ADL: Rejestrowanie zestawów** lub **ADL: Rejestrowanie zestawów (zaawansowane)** polecenia.

**Aby zarejestrować się za pośrednictwem systemu plików ADL: Polecenie zestawu rejestracji**
1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2.  Wprowadź **ADL: Rejestrowanie zestawów**. 
3.  Określ ścieżkę zestawu lokalnego. 
4.  Wybierz konto usługi Data Lake Analytics.
5.  Wybierz bazę danych.

Portal jest otwarty w przeglądarce i wyświetla zestaw procesu rejestracji.  

Wygodny sposób, aby wyzwolić **ADL: Rejestrowanie zestawów** polecenie jest kliknięcie prawym przyciskiem myszy plik .dll w Eksploratorze plików. 

**Aby zarejestrować się za pośrednictwem systemu plików ADL: Zarejestrować polecenia zestawu (zaawansowane)**
1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2.  Wprowadź **ADL: Rejestrowanie zestawów (zaawansowane)** . 
3.  Określ ścieżkę zestawu lokalnego. 
4.  Zostanie wyświetlony plik JSON. Przejrzyj i zmodyfikuj zależności zestawów i parametry zasobu, jeśli to konieczne. Instrukcje są wyświetlane w **dane wyjściowe** okna. Aby przejść do rejestracji zestawów, Zapisz (Ctrl + S) w pliku JSON.

    ![Plik JSON z zależności zestawów i parametry zasobu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Usługa Azure Data Lake Tools w automatycznie wykrywa tego, czy biblioteka DLL ma wszelkich zależności zestawu. Po ich są wykrywane, zależności są wyświetlane w pliku JSON. 
>- Możesz przekazać biblioteki DLL zasobów (na przykład txt, PNG i CSV) w ramach rejestracji zestawów. 

Innym sposobem, aby wyzwolić **ADL: Rejestrowanie zestawów (zaawansowane)** polecenie jest kliknięcie prawym przyciskiem myszy plik .dll w Eksploratorze plików. 

Poniższy kod języka U-SQL demonstruje sposób wywoływania zestawu. W tym przykładzie nazwa zestawu jest *testu*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Użyj uruchamiania lokalnego języka U-SQL i debugowania lokalnego dla użytkowników Windows
Uruchamiania lokalnego języka U-SQL sprawdza dane lokalne i weryfikuje skryptu lokalnie, przed opublikowaniem kodu do usługi Data Lake Analytics. Funkcja debugowania lokalnego umożliwia wykonywanie następujących zadań, zanim kod jest przesyłany do usługi Data Lake Analytics: 
- Debugowanie usługi C# związanym z kodem. 
- Przejść przez kod. 
- Zweryfikuj skrypt lokalnie.

Funkcja debugowania lokalnego uruchomienia i lokalne tylko działa w środowiskach Windows, a nie jest obsługiwana w systemach macOS i systemami operacyjnymi opartymi na systemie Linux.

Aby uzyskać instrukcje dotyczące uruchamiania lokalnego i debugowania lokalnego, zobacz [uruchamiania lokalnego języka U-SQL i debugowania lokalnego przy użyciu programu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby można było skompilować i uruchomić skrypty U-SQL w usłudze Data Lake Analytics, musisz połączyć się z kontem platformy Azure.

<b id="sign-in-by-command">Aby nawiązać połączenie platformy Azure przy użyciu polecenia</b>

1.  Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń. 
2.  Wprowadź **ADL: Zaloguj się**. Informacje logowania zostanie wyświetlona w prawym dolnym rogu.

    ![Wprowadzając polecenie logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Powiadomienie o logowania i uwierzytelniania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Wybierz **Kopiuj & Otwórz** otworzyć [stronę logowania](https://aka.ms/devicelogin). Wklej kod w polu, a następnie wybierz **Kontynuuj**.

    ![Zaloguj się w sieci Web](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Postępuj zgodnie z instrukcjami, aby zalogować się z poziomu strony sieci Web. Gdy komputer jest połączony, nazwa konta platformy Azure zostanie wyświetlona na pasku stanu, w lewym dolnym rogu okna programu VS Code. 

> [!NOTE] 
>- Narzędzia Data Lake Tools automatycznie zaloguje Cię przy następnym Jeśli nie możesz się wylogować.
>- Jeśli Twoje konto ma dwa czynniki włączona, firma Microsoft zaleca używana telefonu uwierzytelniania, a nie przy użyciu numeru PIN.


Aby się wylogować, wprowadź polecenie **ADL: Wylogowywanie**.

**Aby nawiązać połączenie platformy Azure z poziomu Eksploratora**

Rozwiń **AZURE DATALAKE**, wybierz opcję **logowanie do platformy Azure**, a następnie wykonaj kroki 3 i 4 z [na łączenie się z platformą Azure przy użyciu polecenia](#sign-in-by-command).

![Wybór "Sign in Azure" w Eksploratorze](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nie można wylogować się z poziomu Eksploratora. Aby się wylogować, zobacz [na łączenie się z platformą Azure przy użyciu polecenia](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Utwórz skrypt wyodrębniania 
Możesz utworzyć skrypt wyodrębniania plików CSV, tsv i .txt przy użyciu polecenia **ADL: Utwórz skrypt WYODRĘBNIĆ** lub Eksploratora usługi Azure Data Lake.

**Aby utworzyć skrypt ekstrakcji przy użyciu polecenia**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Utwórz skrypt EXTRACT**.
2. Wprowadź pełną ścieżkę dla pliku usługi Azure Storage, a następnie naciśnij klawisz Enter.
3. Wybierz jedno konto.
4. Dla pliku txt wybierz ogranicznik, aby wyodrębnić plik. 

![Proces tworzenia skryptów wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skrypt wyodrębniania jest generowany na podstawie wpisów. Dla skryptu, który nie może wykryć kolumn, wybierz jedną z dwóch opcji. W przeciwnym razie zostanie wygenerowany tylko jeden skrypt.

![Wynik utworzenie skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Aby utworzyć skrypt wyodrębniania z poziomu Eksploratora**

Innym sposobem, aby utworzyć skrypt wyodrębniania jest za pomocą menu kliknij prawym przyciskiem myszy (skrót) w pliku CSV, tsv lub pliku txt w usłudze Azure Data Lake Store lub Azure Blob storage.

![Polecenie "Utwórz skrypt EXTRACT" z menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integracja z usługą Azure Data Lake Analytics za pomocą polecenia

Dostępne zasoby usługi Azure Data Lake Analytics, wyświetlanie listy kont dostępu do metadanych i Wyświetl zadania analizy. 

**Aby wyświetlić listę kont usługi Azure Data Lake Analytics, w ramach subskrypcji platformy Azure**

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: Wyświetlanie listy kont**. Konta są wyświetlane w **dane wyjściowe** okienka.

**Aby dostęp do metadanych usługi Azure Data Lake Analytics**

1.  Wybierz kombinację klawiszy Ctrl + Shift + P, a następnie wprowadź **ADL: Lista tabel**.
2.  Wybierz jedno z kont usługi Data Lake Analytics.
3.  Wybierz jedną z baz danych usługi Data Lake Analytics.
4.  Wybierz jeden ze schematów. Zobaczysz listę tabel.

**Aby wyświetlić zadania usługi Azure Data Lake Analytics**
1.  Otwórz paletę poleceń (Ctrl + Shift + P) i wybierz **ADL: Pokaż zadania**. 
2.  Wybierz usługi Data Lake Analytics lub konta lokalnego. 
3.  Poczekaj, aż lista zadań, które będą one widoczne dla konta.
4.  Wybierz zadanie z listy zadań. Narzędzia Data Lake Tools w okienku po prawej stronie zostanie otwarty w widoku zadania i wyświetla niektóre informacje w danych wyjściowych programu VS Code.

    ![Lista zadań](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integracja z usługą Azure Data Lake Store za pomocą polecenia

Można użyć polecenia usługi Azure Data Lake Store, aby:
 - [Przeglądanie zasobów usługi Azure Data Lake Store](#list-the-storage-path) 
 - [Podgląd pliku usługi Azure Data Lake Store](#preview-the-storage-file) 
 - Przekaż plik bezpośrednio do usługi Azure Data Lake Store w programie VS Code
 - Pobierz plik bezpośrednio z usługi Azure Data Lake Store w programie VS Code

### <a name="list-the-storage-path"></a>Ścieżka magazynu 

**Aby wyświetlić listę ścieżka magazynu za pośrednictwem paletę poleceń**

1. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz pozycję **ADL: Lista ścieżki**.
2. Wybierz folder z listy lub wybierz **wprowadź ścieżkę** lub **przejść z poziomu ścieżki katalogu głównego**. (Używamy **wprowadź ścieżkę** jako przykład.) 
3. Wybierz swoje konto usługi Data Lake Analytics.
4. Przejdź do, lub wprowadź ścieżkę do folderu magazynu (na przykład/wyjście /).  

Paleta poleceń Wyświetla informacje o ścieżce, na podstawie wpisów.

![Wyniki ścieżka magazynu](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Bardziej wygodne listy Ścieżka względna jest za pomocą menu skrótów.

**Aby wyświetlić listę ścieżka magazynu za pomocą menu skrótów**

Kliknij prawym przyciskiem myszy ciąg ścieżki, a następnie wybierz pozycję **Ścieżka listy**.

!["Ścieżka list" w menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Plik magazynu w wersji zapoznawczej

1. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz pozycję **ADL: Podgląd pliku**.
2. Wybierz swoje konto usługi Data Lake Analytics. 
3. Wprowadź ścieżkę pliku usługi Azure Storage (na przykład /output/SearchLog.txt). 

Plik zostanie otwarty w programie VS Code.

![Kroki i wyników przeglądania plików magazynu](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Innym sposobem, aby wyświetlić podgląd pliku jest za pomocą menu skrótów na pełną ścieżkę pliku lub ścieżka względna pliku w Edytorze skryptów. 

### <a name="upload-a-file-or-folder"></a>Przekazywanie pliku lub folderu

1. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz pozycję **Przekaż plik** lub **Przekaż Folder**.
2. Wybierz jednego lub wielu plików, w przypadku wybrania **Przekaż plik**, lub wybierz cały folder, w przypadku wybrania **Przekaż Folder**. Następnie wybierz pozycję **przekazywanie**. 
3. Wybierz folder magazynu na liście lub zaznacz **wprowadź ścieżkę** lub **przejść z poziomu ścieżki katalogu głównego**. (Używamy **wprowadź ścieżkę** jako przykład.) 
4. Wybierz swoje konto usługi Data Lake Analytics. 
5. Przejdź do, lub wprowadź ścieżkę do folderu magazynu (na przykład/wyjście /). 
6. Wybierz **Wybierz bieżący Folder** określić swoje miejsce docelowe przekazywania.

![Kroki i wyników do przekazywania pliku lub folderu](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Przekazywanie plików do magazynu w inny sposób to za pomocą menu skrótów na pełną ścieżkę pliku lub ścieżka względna pliku w Edytorze skryptów.

Możesz [monitorować stan przekazywania](#check-storage-tasks-status).


### <a name="download-a-file"></a>Pobieranie pliku 
Można pobrać pliku za pomocą polecenia **ADL: Pobierz plik** lub **ADL: Pobierz plik (zaawansowane)** .

**Aby pobrać plik za pomocą systemu plików ADL: Pobierz plik (zaawansowane) — polecenie**
1. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz **Pobierz plik (zaawansowane)** .
2. Program VS Code Wyświetla plik w formacie JSON. Możesz podać ścieżki do plików i pobrać wiele plików w tym samym czasie. Instrukcje są wyświetlane w **dane wyjściowe** okna. Aby kontynuować, aby pobrać plik lub pliki, Zapisz (Ctrl + S) w pliku JSON.

    ![Plik JSON ze ścieżkami do pobierania plików](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**Dane wyjściowe** okna wyświetlany jest stan pobierania plików.

![Okno danych wyjściowych ze stanem pobieranie](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Możesz [monitorować stan pobierania](#check-storage-tasks-status).

**Aby pobrać plik za pomocą systemu plików ADL: Pobierz plik — polecenie**

1. Kliknij prawym przyciskiem myszy w Edytorze skryptów, wybierz **Pobierz plik**, a następnie wybierz folder docelowy z **wybierz Folder** okno dialogowe.
2. Wybierz folder z listy lub wybierz **wprowadź ścieżkę** lub **przejść z poziomu ścieżki katalogu głównego**. (Używamy **wprowadź ścieżkę** jako przykład.) 
3. Wybierz swoje konto usługi Data Lake Analytics. 
4. Wyszukaj lub wpisz ścieżkę do folderu magazynu (na przykład/wyjście /), a następnie wybierz plik do pobrania.

![Kroki i pobierania pliku wyników](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Innym sposobem, aby pobrać pliki magazynu jest za pomocą menu skrótów na pełną ścieżkę pliku lub ścieżka względna pliku w Edytorze skryptów.

Możesz [monitorować stan pobierania](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Sprawdź stan zadania magazynu
Stan przekazywania i pobierania jest wyświetlany na pasku stanu. Wybierz na pasku stanu, a następnie zostanie wyświetlony stan na **dane wyjściowe** kartę.

![Pasek stanu i dane wyjściowe zawierają szczegóły](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integracja z usługą Azure Data Lake Analytics z poziomu Eksploratora

Po zalogowaniu, wszystkie subskrypcje dla konta platformy Azure są wymienione w okienku po lewej stronie w obszarze **AZURE DATALAKE**. 

![Usługa Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Usługa Data Lake Analytics metadanych nawigacji

Rozwiń swoją subskrypcję platformy Azure. W obszarze **bazy danych U-SQL** węzła, mogą przeglądać bazy danych U-SQL i Wyświetl foldery, takich jak **schematów**, **poświadczenia**, **zestawy**, **Tabel**, i **indeksu**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Usługa Data Lake Analytics metadanych jednostki zarządzania

Rozwiń **bazy danych U-SQL**. Można utworzyć bazy danych, schematu, tabeli, typ tabeli, indeks lub Statystyka prawym przyciskiem myszy odpowiedni węzeł, a następnie wybierając **skrypt w celu tworzenia** w menu skrótów. Na stronie otwarty skrypt Przeprowadź edycję skryptu zgodnie z potrzebami. Następnie prześlij zadanie, klikając go prawym przyciskiem myszy i wybierając polecenie **ADL: Prześlij zadanie**. 

Po zakończeniu tworzenia elementu, kliknij prawym przyciskiem myszy węzeł, a następnie wybierz pozycję **Odśwież** do wyświetlenia elementu. Możesz także usunąć element, kliknij go prawym przyciskiem myszy, a następnie wybierając **Usuń**.

![Polecenie "Skrypt do utworzenia" w menu skrótów w Eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skrypt strony dla nowego elementu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Rejestrowanie zestawu analiz usługi Data Lake

Możesz zarejestrować zestaw w odpowiedniej bazy danych, klikając prawym przyciskiem myszy **zestawy** węzeł, a następnie wybierając **zarejestrować zestawu**.

![Polecenie "Zarejestrować zestawu" w menu skrótów dla węzła zestawów](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integracja z usługą Azure Data Lake Store w Eksploratorze

Przejdź do **Data Lake Store**:

- Możesz kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć **Odśwież**, **Usuń**, **przekazywanie**, **Przekaż Folder**, **kopiowania Ścieżka względna**, i **Kopiuj pełną ścieżkę** poleceń w menu skrótów.

   ![Polecenia menu skrótów dla węzła folder w Eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Możesz kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć **(wersja zapoznawcza)** , **Pobierz**, **Usuń**, **Utwórz skrypt WYODRĘBNIĆ** (dostępne tylko dla woluminów CSV TSV, a pliki TXT), **Kopiuj ścieżkę względną**, i **Kopiuj pełną ścieżkę** poleceń w menu skrótów.

   ![Polecenia menu skrótów dla węzła pliku w Eksploratorze usługi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integracja z usługą Azure Blob storage z poziomu Eksploratora

Przejdź do magazynu obiektów Blob:

- Możesz kliknąć prawym przyciskiem myszy węzeł kontenera obiektów blob, a następnie użyć **Odśwież**, **usuń kontener obiektów Blob**, i **przekazywanie obiektu Blob** poleceń w menu skrótów.

   ![Polecenia menu skrótów dla węzła kontenera obiektów blob w ramach magazynu obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Możesz kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć **Odśwież** i **przekazywanie obiektu Blob** poleceń w menu skrótów.

   ![Polecenia menu skrótów dla węzła folder, w ramach magazynu obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Możesz kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć **(wersja zapoznawcza) oraz pozwala edytować**, **Pobierz**, **Usuń**, **Utwórz skrypt WYODRĘBNIĆ** (dostępne tylko w przypadku plików CSV, TSV i TXT) **Kopiuj ścieżkę względną**, i **Kopiuj pełną ścieżkę** poleceń w menu skrótów.

    ![Polecenia menu skrótów dla węzła pliku w ramach magazynu obiektów Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Otwórz Eksploratora usługi Data Lake w witrynie portal
1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **Otwórz Eksploratora usługi Storage Azure Web** lub kliknij prawym przyciskiem myszy względną ścieżkę lub pełną ścieżkę w Edytorze skryptów, a następnie wybierz **Otwórz Eksploratora usługi Storage platformy Azure w sieci Web**.
3. Wybierz konto usługi Data Lake Analytics.

Narzędzia Data Lake Tools otwiera Ścieżka usługi Azure Storage w witrynie Azure portal. Można znaleźć ścieżki i plik z sieci web w wersji zapoznawczej.

## <a name="additional-features"></a>Dodatkowe funkcje

Narzędzia Data Lake Tools dla programu VS Code obsługuje następujące funkcje:

-   **Automatycznego uzupełniania IntelliSense**: Sugestie są wyświetlane na wyskakujące okienka wokół elementy, takie jak słowa kluczowe, metody i zmienne. Różne ikony reprezentują różne typy obiektów:

    - Typ danych z języka scala
    - Typ złożony danych
    - Wbudowanych typów zdefiniowanych przez użytkownika
    - Klasy i kolekcji .NET
    - Wyrażeń języka C#
    - Wbudowane C#, funkcje UDF, obiektów udo i UDAAGs 
    - Funkcje języka U-SQL
    - Funkcji obsługi okien języka U-SQL
 
    ![Typy obiektów funkcji IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Autouzupełnianie funkcji IntelliSense na metadanych usługi Data Lake Analytics**: Narzędzia Data Lake Tools pobiera lokalnie informacje metadanych usługi Data Lake Analytics. Funkcja IntelliSense spowoduje automatyczne wypełnienie obiektów na podstawie metadanych usługi Data Lake Analytics. Te obiekty obejmują bazy danych, schematu, tabeli, widoku, funkcja z wartościami przechowywanymi w tabeli, procedury i zestawy języka C#.
 
    ![Metadane funkcji IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Znacznik błędów funkcji IntelliSense**: Narzędzia Data Lake Tools podkreśla błędy edytowania języka U-SQL i C#. 
-   **Najważniejsze funkcje składni**: Zmienne, słowa kluczowe, typów danych i funkcje, takie jak Data Lake Tools używa kolorów do odróżniania elementów. 

    ![Składnia przy użyciu różnych kolorów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Zaleca się uaktualnienie do usługi Azure Data Lake Tools for Visual Studio w wersji 2.3.3000.4 lub nowszej. Poprzednie wersje są niedostępne do pobrania i przestarzałe.  
   
## <a name="next-steps"></a>Kolejne kroki
- [Programowanie w języku U-SQL przy użyciu języka Python, R i języku C Sharp dla usługi Azure Data Lake Analytics w programie VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Uruchamiania lokalnego języka U-SQL i debugowania lokalnego przy użyciu programu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Samouczek: Rozpoczynanie pracy z usługą Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Samouczek: Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
