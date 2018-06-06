---
title: Uruchamianie i testowanie zadań U-SQL lokalnie przy użyciu zestawu SDK usługi Azure Data Lake U-SQL
description: Dowiedz się, jak uruchamianie i testowanie zadań U-SQL lokalnie przy użyciu wiersza polecenia i interfejsów w sieci lokalnej stacji roboczej programowania.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 11a2bfdcda09a071667cc034ef1ff42794b73a33
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737075"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Uruchamianie i testowanie U-SQL przy użyciu zestawu SDK usługi Azure Data Lake U-SQL

Podczas tworzenia skryptu U-SQL, jest często do uruchomienia i skrypt testu U-SQL lokalnie przed przesłać do chmury. Usługi Azure Data Lake umożliwia pakietu Nuget o nazwie zestawu SDK usługi Azure Data Lake U-SQL dla tego scenariusza, za pomocą którego można łatwo skalować uruchamianie skryptu U-SQL i testowania. Istnieje również możliwość zintegrowania ten test U-SQL przy użyciu systemu CI (ciągłej integracji) do automatyzowania kompilowania i testowania.

Jeśli potrzebne informacje jak ręcznie lokalnymi Uruchom i debugowania skryptu U-SQL z narzędzi z graficznym interfejsem użytkownika, możesz użyć usługi Azure Data Lake Tools dla programu Visual Studio, w tym. Dowiedz się więcej o [tutaj](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalacja usługi Azure Data Lake U-SQL SDK

Można pobrać zestaw SDK usługi Azure Data Lake U-SQL [tutaj](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) na Nuget.org. I przed jego użyciem należy upewnić się, że zależności w następujący sposób.

### <a name="dependencies"></a>Zależności

Data Lake U-SQL zestaw SDK wymaga następujących zależności:

- [Microsoft .NET Framework 4.6 lub nowszą](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 i zestaw Windows SDK 10.0.10240.0 lub nowszą (nazywany CppSDK w tym artykule). Istnieją dwa sposoby uzyskania CppSDK:

    - Zainstaluj [programu Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). W folderze Program Files — na przykład C:\Program Files (x86) \Windows Kits\10\ będziesz mieć folderu \Windows Kits\10. Zawiera ona również wersji zestawu Windows 10 SDK w \Windows Kits\10\Lib. Jeśli nie widzisz tych folderów, zainstaluj ponownie program Visual Studio i należy wybrać podczas instalacji zestawu Windows 10 SDK. Jeśli masz to zainstalowane z programem Visual Studio kompilatora lokalnego skryptu U-SQL znajdziesz ją automatycznie.

    ![Narzędzia Data Lake Tools dla programu Visual Studio lokalnego uruchomienia systemu Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Zainstaluj [Data Lake Tools dla programu Visual Studio](http://aka.ms/adltoolsvs). Można znaleźć opakowaniach jednostkowych Visual C++ i zestaw Windows SDK plików w C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. W takim przypadku kompilatora lokalnego skryptu U-SQL nie można automatycznie odnaleźć zależności. Należy określić ścieżkę CppSDK dla niego. Możesz skopiować pliki do innej lokalizacji lub użyj go jako jest.

## <a name="understand-basic-concepts"></a>Zrozumienie podstawowych pojęć

### <a name="data-root"></a>Główny danych

Folder główny danych jest "Magazyn lokalny" dla konta lokalnego obliczeń. Odpowiada to konto usługi Azure Data Lake Store z kontem usługi Data Lake Analytics. Przełączanie do folderu różnych głównych danych jest podobnie jak przełączanie do konta magazynu innego. Jeśli chcesz uzyskać dostęp do danych często udostępnione z innego katalogu głównym danych folderów, należy użyć ścieżek bezwzględnych w skryptach. Lub Utwórz łącza symbolicznego systemu plików (na przykład **mklink** na NTFS) w folderze głównym danych wskaż udostępnionych danych.

Folder główny danych służy do:

- Przechowywania metadanych lokalnego, w tym baz danych, tabel, przechowywanymi w tabeli funkcji (funkcji Tvf) i zestawy.
- Wyszukiwanie wejściowymi i wyjściowymi ścieżek, które są zdefiniowane jako ścieżek względnych w języku U-SQL. Używanie ścieżek względnych ułatwia wdrażanie projektów języka U-SQL na platformie Azure.

### <a name="file-path-in-u-sql"></a>Ścieżka pliku w języku U-SQL

W skryptów U-SQL, można użyć zarówno ścieżki względnej, jak i lokalną ścieżkę bezwzględną. Ścieżka względna jest względem ścieżki katalogu głównym danych określonego folderu. Firma Microsoft zaleca użycie "/" jako separatora ścieżki do sprawia, że skrypty zgodne z po stronie serwera. Oto kilka przykładów ścieżek względnych i ich równoważne ścieżki bezwzględnej. W tych przykładach C:\LocalRunDataRoot jest folder główny danych.

|Ścieżka względna|Ścieżki bezwzględne|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Katalog roboczy

Podczas uruchamiania lokalnego skryptu U-SQL, przejdź do katalogu roboczego został utworzony podczas kompilacji w bieżącym katalogu uruchamiania. Oprócz dane wyjściowe kompilacji pliki obsługi potrzebne dla lokalnych wykonań będzie kopiowany do katalogu roboczego w tle. Folder główny katalog roboczy jest nazywany "ScopeWorkDir" i pliki w katalogu roboczego są następujące:

|Pliku lub katalogu|Pliku lub katalogu|Pliku lub katalogu|Definicja|Opis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Ciąg skrótu wersji środowiska wykonawczego|Pliki środowiska uruchomieniowego niezbędne do wykonania lokalnej kopii w tle|
| |Script_66AE4909AA0ED06C| |Nazwa skryptu + wyznaczania wartości skrótu ciągu ścieżki skryptu|Dane wyjściowe kompilacji i wykonywanie kroku rejestrowania|
| | |\_skrypt\_.abr|Dane wyjściowe kompilatora|Plik algebraiczną|
| | |\_ScopeCodeGen\_.*|Dane wyjściowe kompilatora|Wygenerowanego kodu zarządzanego|
| | |\_ScopeCodeGenEngine\_.*|Dane wyjściowe kompilatora|Wygenerowany kod natywny|
| | |przywoływanych zestawach|Odwołanie do zestawu|Przywoływany zestaw plików|
| | |deployed_resources|Wdrażanie zasobu|Pliki zasobów wdrożenia|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Dziennik wykonywania.|Dziennik wykonywania czynności|


## <a name="use-the-sdk-from-the-command-line"></a>Korzystanie z zestawu SDK z poziomu wiersza polecenia

### <a name="command-line-interface-of-the-helper-application"></a>Interfejs wiersza polecenia z aplikacją pomocniczą

W obszarze SDK directory\build\runtime LocalRunHelper.exe to aplikacja pomocnika wiersza polecenia, która zapewnia interfejsy do większości często używane funkcje lokalnego uruchomienia. Należy pamiętać, że zarówno polecenia i przełączniki argumentów jest rozróżniana wielkość liter. Aby wywołać go:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Uruchom LocalRunHelper.exe bez argumentów lub z **pomocy** przełącznik, aby wyświetlić informacje pomocy:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Informacje pomocy:

-  **Polecenie** nadaje nazwę polecenia.  
-  **Argument wymagany** listę argumentów, które należy podać.  
-  **Opcjonalny Argument** listę argumentów, które są opcjonalne, wartościami domyślnymi.  Logiczna Argumenty opcjonalne nie ma parametrów, a ich wyglądu oznacza ujemna przywrócić wartości domyślne.

### <a name="return-value-and-logging"></a>Wartość zwracana i rejestrowanie

Zwraca aplikacją pomocniczą **0** w przypadku powodzenia i **-1** niepowodzenia. Domyślnie pomocnika wysyła komunikaty do bieżącej konsoli. Jednak większość potrzebnych poleceń obsługuje **path_to_log_file - MessageOut** opcjonalny argument, który przekierowuje dane wyjściowe do pliku dziennika.

### <a name="environment-variable-configuring"></a>Konfigurowanie zmiennej środowiskowej

Uruchom wymaga głównego określone dane jako konto magazynu lokalnego, jak i zależności dla określonej ścieżki CppSDK lokalnego U-SQL. Można jednocześnie ustawić argument w zmiennej środowiskowej wiersza polecenia lub ustaw dla nich.

- Ustaw **SCOPE_CPP_SDK** zmiennej środowiskowej.

    Jeśli środowisko Microsoft Visual C++ i zestaw Windows SDK, instalując narzędzi Data Lake Tools dla programu Visual Studio, sprawdź, czy następujący folder:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Zdefiniuj nową zmienną środowiskową o nazwie **SCOPE_CPP_SDK** wskaż tego katalogu. Skopiuj folder do innej lokalizacji lub określ **SCOPE_CPP_SDK** jak.

    Oprócz ustawienie zmiennej środowiskowej, można określić **- CppSDK** argumentu podczas korzystania z wiersza polecenia. Ten argument jest zastąpienie zmiennej środowiskowej CppSDK Twojego domyślne.

- Ustaw **LOCALRUN_DATAROOT** zmiennej środowiskowej.

    Zdefiniuj nową zmienną środowiskową o nazwie **LOCALRUN_DATAROOT** wskazującego w katalogu głównym danych.

    Oprócz ustawienie zmiennej środowiskowej, można określić **- DataRoot** argumentu ze ścieżką katalogu głównym danych podczas korzystania z wiersza polecenia. Ten argument zastępuje zmiennej środowiskowej katalogu głównym danych programu domyślnego. Należy dodać ten argument do każdego wiersza polecenia, którego używasz, dzięki czemu mogą zastępować zmiennej środowiskowej danych głównych domyślnego dla wszystkich operacji.

### <a name="sdk-command-line-usage-samples"></a>Przykłady użycia wiersza polecenia zestawu SDK

#### <a name="compile-and-run"></a>Kompilowanie i uruchamianie

**Uruchom** polecenie służy do Kompiluj skrypt, a następnie wykonaj skompilowanych wyników. Argumenty wiersza polecenia są kombinacją od **skompilować** i **wykonania**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **Uruchom**:


|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|Plik CodeBehind-|False|Skrypt ma .cs kodzie|
|-CppSDK| |CppSDK katalogu|
|-DataRoot| Zmienna środowiskowa DataRoot|DataRoot dla lokalnego uruchomienia domyślną do zmiennej środowiskowej "LOCALRUN_DATAROOT"|
|-MessageOut| |Komunikaty w konsoli w pliku zrzutu|
|-Równoległe|1|Uruchom planu z określonym równoległości|
|— Odwołania| |Lista ścieżek do zestawów odwołań dodatkowe lub pliki danych w kodzie, oddzielone ";"|
|-UdoRedirect|False|Generowanie konfiguracji przekierowania zestawu Udo|
|-UseDatabase|master|Bazy danych do użycia na potrzeby kodu tymczasowej zestawu rejestracji|
|-Verbose|False|Pokaż szczegółowe dane wyjściowe z środowiska wykonawczego|
|-WorkDir|Bieżący katalog|Katalog dla kompilatora użycia i dane wyjściowe|
|-RunScopeCEP|0|Tryb ScopeCEP|
|-ScopeCEPTempPath|Temp|Tymczasowy ścieżkę używaną do strumieniowego przesyłania danych|
|-OptFlags| |Rozdzielana przecinkami lista flagi optymalizacji|


Oto przykład:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Oprócz łączenie **skompilować** i **wykonania**, możesz skompilować i wykonywać skompilowane pliki wykonywalne niezależnie.

#### <a name="compile-a-u-sql-script"></a>Kompiluj skrypt U-SQL

**Skompilować** polecenia jest używana do kompilowania skryptu U-SQL do plików wykonywalnych.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **skompilować**:


|Argument|Opis|
|--------|-----------|
| -Plik CodeBehind [domyślna wartość 'False']|Skrypt ma .cs kodzie|
| -CppSDK [wartość domyślna "]|CppSDK katalogu|
| -DataRoot [wartość domyślna "DataRoot zmiennej środowiskowej"]|DataRoot dla lokalnego uruchomienia domyślną do zmiennej środowiskowej "LOCALRUN_DATAROOT"|
| -MessageOut [wartość domyślna "]|Komunikaty w konsoli w pliku zrzutu|
| -Odwołuje się do [wartość domyślna "]|Lista ścieżek do zestawów odwołań dodatkowe lub pliki danych w kodzie, oddzielone ";"|
| -Małym [domyślną wartość 'False']|Skrócona kompilacji|
| -UdoRedirect [domyślna wartość 'False']|Generowanie konfiguracji przekierowania zestawu Udo|
| -UseDatabase [wartość domyślna 'master']|Bazy danych do użycia na potrzeby kodu tymczasowej zestawu rejestracji|
| -WorkDir [wartość domyślna "Bieżący katalog"]|Katalog dla kompilatora użycia i dane wyjściowe|
| -RunScopeCEP [domyślna wartość "0"]|Tryb ScopeCEP|
| -ScopeCEPTempPath [wartość domyślna "temp"]|Tymczasowy ścieżkę używaną do strumieniowego przesyłania danych|
| -OptFlags [wartość domyślna "]|Rozdzielana przecinkami lista flagi optymalizacji|


Oto kilka przykładów użycia.

Kompiluj skrypt U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Kompiluj skrypt U-SQL i ustaw folder główny danych. Należy pamiętać, że spowoduje to zastąpienie ustaw dla zmiennej środowiskowej.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Kompiluj skrypt U-SQL i Ustaw katalog roboczy, odwołanie do zestawu i bazy danych:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Wykonanie skompilowanych wyników

**Wykonania** polecenie służy do wykonywania skompilowanych wyników.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **wykonania**:

|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|-DataRoot | '' |Główny danych wykonywania metadanych. Domyślnie **LOCALRUN_DATAROOT** zmiennej środowiskowej.|
|-MessageOut | '' |Zrzut wiadomości na konsoli do pliku.|
|-Równoległe | "1" |Wskaźnik wykonanie kroków wygenerowanego lokalnego uruchomienia równoległości określony poziom.|
|-Verbose | "Fałsz" |Wskaźnik do wyświetlenia szczegółowych danych wyjściowych ze środowiska uruchomieniowego.|

Oto przykład użycia:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Interfejsy programowania za pomocą zestawu SDK

Interfejsy programowania znajdują się w LocalRunHelper.exe. Można używać ich do integracji funkcji zestawu SDK U-SQL oraz struktury testowej C#, aby skalować test lokalnego skryptu U-SQL. W tym artykule I używa standardowych C# jednostkowy projekt testowy do pokazują, jak używać tych interfejsów, aby przetestować skrypt U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1: Tworzenie projektu testu jednostki C# i konfiguracji

- Tworzenie projektu testu jednostki C# za pomocą pliku > Nowy > Projekt > Visual C# > Test > projektu testu jednostki.
- Dodaj LocalRunHelper.exe jako odwołanie dla projektu. LocalRunHelper.exe znajduje się pod adresem \build\runtime\LocalRunHelper.exe w pakiecie Nuget.

    ![Zestaw SDK usługi Azure Data Lake U-SQL Dodaj odwołanie](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- Zestaw SDK U-SQL **tylko** środowisko obsługi x64, upewnij się, by ustawić je jako x64 kompilacji platformy docelowej. Które można ustawić za pomocą właściwości projektu > kompilacji > platformy docelowej.

    ![Zestaw SDK usługi Azure Data Lake U-SQL skonfigurować x64 projektu](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Upewnij się, by ustawić je jako x64 środowiska testowego. W programie Visual Studio, możesz ustawić za pomocą testu > Testuj ustawienia > domyślny procesor > x64.

    ![Zestaw SDK usługi Azure Data Lake U-SQL skonfigurować x64 środowiska testowego](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Upewnij się skopiować wszystkie pliki zależności w NugetPackage\build\runtime\ do katalogu, który zazwyczaj znajduje się w obszarze ProjectFolder\bin\x64\Debug roboczego projektu.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2: Tworzenie przypadku testowego skryptu U-SQL

Poniżej znajduje się przykładowy kod dla testu skryptu U-SQL. Do testowania, musisz przygotować skrypty, pliki wejściowe i oczekiwane dane wyjściowe.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfejsy programowania w języku LocalRunHelper.exe

LocalRunHelper.exe udostępnia interfejsów programowania dla kompilacji lokalnego skryptu U-SQL, uruchom itp. Poniżej wymieniono interfejsy.

**Konstruktor**

publiczny LocalRunHelper ([System.IO.TextWriter messageOutput = null])

|Parametr|Typ|Opis|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|dane wyjściowe wiadomości ustawić wartości null przy użyciu konsoli|

**właściwości**

|Właściwość|Typ|Opis|
|--------|----|-----------|
|AlgebraPath|ciąg|Ścieżka do pliku algebraiczną (plik algebraiczną jest jednym z rezultatów kompilacji)|
|CodeBehindReferences|ciąg|Jeśli skrypt ma dodatkowe kodzie odwołań, określ ścieżki oddzielone znakiem ";"|
|CppSdkDir|ciąg|CppSDK katalogu|
|CurrentDir|ciąg|Bieżący katalog|
|DataRoot|ciąg|Ścieżka katalogu głównego danych|
|DebuggerMailPath|ciąg|Ścieżka do mailslot debugera|
|GenerateUdoRedirect|wartość logiczna|Jeśli chcemy, aby generować przekierowywania zastąpienie konfiguracji ładowania zestawu|
|HasCodeBehind|wartość logiczna|Jeśli skrypt ma kodzie|
|InputDir|ciąg|Katalog dla danych wejściowych|
|MessagePath|ciąg|Ścieżka pliku zrzutu wiadomości|
|OutputDir|ciąg|Katalog danych wyjściowych|
|Równoległość|int|Równoległość do uruchomienia algebraiczną|
|ParentPid|int|Identyfikator procesu elementu nadrzędnego, na którym monitoruje usługę, aby zakończyć, równa 0 lub negatywną ignorowanie|
|ResultPath|ciąg|Ścieżka pliku zrzutu wyników|
|RuntimeDir|ciąg|Katalogu środowiska uruchomieniowego|
|ScriptPath|ciąg|Gdzie można znaleźć skryptu|
|Skrócona|wartość logiczna|Skrócona kompilacji lub nie|
|TempDir|ciąg|Katalog tymczasowy|
|UseDataBase|ciąg|Określ bazę danych do użycia na potrzeby kodzie rejestracji tymczasowego zestawu głównego domyślnie|
|WorkDir|ciąg|Preferowany katalog roboczy|


**— Metoda**

|Metoda|Opis|Zwraca|Parametr|
|------|-----------|------|---------|
|publiczny bool DoCompile()|Kompiluj skrypt U-SQL|Wartość true w przypadku powodzenia| |
|publiczny bool DoExec()|Skompilowany wynik wykonywania|Wartość true w przypadku powodzenia| |
|publiczny bool DoRun()|Uruchom skrypt U-SQL (kompilacji + Execute)|Wartość true w przypadku powodzenia| |
|publiczny bool IsValidRuntimeDir (ciąg ścieżki)|Sprawdź, czy podana ścieżka jest runtime prawidłową ścieżkę|Wartość true dla prawidłowe|Ścieżka katalogu środowiska uruchomieniowego|


## <a name="faq-about-common-issue"></a>Często zadawane pytania dotyczące typowych problem

### <a name="error-1"></a>Błąd 1:
E_CSC_SYSTEM_INTERNAL: Błąd wewnętrzny! Nie można załadować pliku lub zestawu 'ScopeEngineManaged.dll' lub jednej z jego zależności. Nie można odnaleźć określonego modułu.

Sprawdź, czy następujące czynności:

- Upewnij się, że masz x64 środowiska. Platforma docelowa kompilacji i środowiska testowego należy x64, zapoznaj się **krok 1: tworzenie C# jednostki Testowanie projektu i konfiguracji** powyżej.
- Upewnij się, że wszystkie pliki zależności w NugetPackage\build\runtime\ zostały skopiowane do katalogu roboczego projektu.


## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Rejestrowanie informacji diagnostycznych, zobacz [uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Aby wyświetlić bardziej złożonego zapytania, zobacz [analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby wyświetlić szczegóły zadania, zobacz [użyj przeglądarki zadania i widok zadań dla zadania usługi Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Aby użyć widoku wykonania wierzchołka, zobacz [użyć widoku wykonania wierzchołka w narzędzi Data Lake Tools dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
