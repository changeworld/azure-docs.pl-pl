---
title: Uruchamianie zadań U-SQL lokalnie — zestaw SDK usługi Azure Data Lake U-SQL
description: Dowiedz się, jak uruchamiać i testować zadania U-SQL lokalnie przy użyciu wiersza polecenia i interfejsów programowania na lokalnej stacji roboczej.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309682"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Uruchamianie i testowanie języka U-SQL za pomocą narzędzia Azure Data Lake U-SQL SDK

Podczas tworzenia skryptu U-SQL, jest często uruchamiać i testować skrypt U-SQL lokalnie przed przesłaniem go do chmury. Usługa Azure Data Lake udostępnia pakiet Nuget o nazwie Azure Data Lake U-SQL SDK dla tego scenariusza, za pomocą którego można łatwo skalować uruchamianie i testowanie U-SQL. Istnieje również możliwość zintegrowania tego testu U-SQL z systemem ciągłej integracji ci w celu zautomatyzowania kompilacji i testowania.

Jeśli zależy Ci na tym, jak ręcznie uruchomić lokalne i debugować skrypt U-SQL za pomocą narzędzi GUI, możesz użyć narzędzia Usługi Azure Data Lake Tools dla programu Visual Studio. Możesz dowiedzieć się więcej [tutaj](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalowanie pakietu Azure Data Lake U-SQL SDK

Możesz uzyskać pakiet Azure Data Lake U-SQL SDK [tutaj](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) na Nuget.org. Przed użyciem należy upewnić się, że masz zależności w następujący sposób.

### <a name="dependencies"></a>Zależności

Zestaw SDK U-SQL usługi Data Lake wymaga następujących zależności:

- [Microsoft .NET Framework 4.6 lub nowszy](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 i Windows SDK 10.0.10240.0 lub nowsze (który jest nazywany CppSDK w tym artykule). CppSDK można uzyskać na dwa sposoby:

  - Zainstaluj [program Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Folder \Windows Kits\10 znajduje się w folderze Pliki programu — na przykład C:\Program Files (x86)\Windows Kits\10\. Wersję zestawu SDK systemu Windows 10 znajdziesz również w obszarze \Windows Kits\10\Lib. Jeśli nie widzisz tych folderów, zainstaluj ponownie program Visual Studio i upewnij się, że podczas instalacji wybierz zestaw SDK systemu Windows 10. Jeśli masz to zainstalowane w programie Visual Studio, kompilator lokalny U-SQL znajdzie go automatycznie.

    ![Narzędzia usługi Data Lake Tools dla lokalnego zestawu Windows 10 w programie Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Zainstaluj [narzędzia usługi Data Lake Tools dla programu Visual Studio](https://aka.ms/adltoolsvs). Wstępnie spakowane pliki visual c++ i windows SDK można znaleźć w witrynie C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. W takim przypadku kompilator lokalny U-SQL nie może automatycznie odnaleźć zależności. Należy określić ścieżkę CppSDK dla niego. Można skopiować pliki do innej lokalizacji lub użyć ich w stanie, w jakim jest.

## <a name="understand-basic-concepts"></a>Zrozumienie podstawowych pojęć

### <a name="data-root"></a>Katalog główny danych

Folder główny danych jest "magazynem lokalnym" dla lokalnego konta obliczeniowego. Jest to równoważne konto usługi Azure Data Lake Store konta usługi Data Lake Analytics. Przełączenie do innego folderu głównego danych jest jak przełączenie się na inne konto sklepu. Jeśli chcesz uzyskać dostęp do często udostępnionych danych z różnymi folderami głównymi danych, musisz użyć ścieżek bezwzględnych w skryptach. Możesz też utworzyć dowiązania symboliczne systemu plików (na przykład **mklink** w systemie PLIKÓW NTFS) w folderze głównym danych, aby wskazać udostępnione dane.

Folder główny danych służy do:

- Przechowuj lokalne metadane, w tym bazy danych, tabele, funkcje o wartości tabeli (TVF) i zestawy.
- Wyszukuj ścieżki wejściowe i wyjściowe, które są zdefiniowane jako ścieżki względne w U-SQL. Korzystanie ze ścieżek względnych ułatwia wdrażanie projektów U-SQL na platformie Azure.

### <a name="file-path-in-u-sql"></a>Ścieżka pliku w U-SQL

Można użyć zarówno ścieżki względnej, jak i lokalnej ścieżki bezwzględnej w skryptach U-SQL. Ścieżka względna jest względem określonej ścieżki folderu głównego danych. Zaleca się użycie "/" jako separatora ścieżki, aby skrypty były zgodne ze stroną serwera. Oto kilka przykładów ścieżek względnych i ich równoważnych ścieżek bezwzględnych. W tych przykładach C:\LocalRunDataRoot jest folderem głównym danych.

|Ścieżka względna|Ścieżka bezwzględna|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Katalog roboczy

Podczas uruchamiania skryptu U-SQL lokalnie, katalog roboczy jest tworzony podczas kompilacji w bieżącym uruchomionym katalogu. Oprócz danych wyjściowych kompilacji potrzebne pliki środowiska wykonawczego do wykonywania lokalnego będą kopiowane w tle do tego działającego katalogu. Folder główny katalogu roboczego nosi nazwę "ScopeWorkDir", a pliki w katalogu roboczym są następujące:

|Katalog/plik|Katalog/plik|Katalog/plik|Definicja|Opis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Ciąg mieszania wersji środowiska wykonawczego|Kopia w tle plików runtime potrzebnych do lokalnego wykonania|
| |Script_66AE4909AA0ED06C| |Nazwa skryptu + ciąg mieszania ścieżki skryptu|Wyniki kompilacji i rejestrowanie kroku wykonania|
| | |\_skrypt\_.abr|Dane wyjściowe kompilatora|Algebra plik|
| | |\_ScopeCodeGen\_.*|Dane wyjściowe kompilatora|Wygenerowany kod zarządzany|
| | |\_ScopeCodeGenEngin\_.*|Dane wyjściowe kompilatora|Wygenerowany kod macierzysty|
| | |zestawy, do których się odwołuje|Odwołanie do złożenia|Pliki złożenia, do których istnieje odwołanie|
| | |deployed_resources|Wdrażanie zasobów|Pliki wdrażania zasobów|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Dziennik wykonywania|Rejestrowanie kroków wykonywania|


## <a name="use-the-sdk-from-the-command-line"></a>Używanie sdk z wiersza polecenia

### <a name="command-line-interface-of-the-helper-application"></a>Interfejs wiersza polecenia aplikacji pomocniczej

W obszarze SDK directory\build\runtime LocalRunHelper.exe jest aplikacją pomocniczą wiersza polecenia, która udostępnia interfejsy do większości często używanych funkcji uruchamiania lokalnego. Należy zauważyć, że zarówno w przypadku przełączników polecenia, jak i argumentów jest rozróżniana wielkość liter. Aby go wywołać:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Uruchom LocalRunHelper.exe bez argumentów lub z przełącznikiem **pomocy,** aby wyświetlić informacje pomocy:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

W informacji o pomocy:

-  **Polecenie** podaje nazwę polecenia.  
-  **Wymagany argument** zawiera listę argumentów, które muszą być dostarczone.  
-  **Argument opcjonalny** wyświetla argumenty, które są opcjonalne, z wartościami domyślnymi.  Opcjonalne argumenty logiczne nie mają parametrów, a ich wyglądy oznaczają wartość ujemną dla ich wartości domyślnej.

### <a name="return-value-and-logging"></a>Zwraca wartość i rejestrowanie

Aplikacja pomocnika zwraca **0** dla sukcesu i **-1** dla awarii. Domyślnie pomocnik wysyła wszystkie wiadomości do bieżącej konsoli. Jednak większość poleceń obsługuje **argument -MessageOut path_to_log_file** opcjonalny, który przekierowuje dane wyjściowe do pliku dziennika.

### <a name="environment-variable-configuring"></a>Konfiguracja zmiennej środowiskowej

Lokalne uruchomienie U-SQL wymaga określonego katalogu głównego danych jako konta magazynu lokalnego, a także określonej ścieżki CppSDK dla zależności. Można ustawić argument w wierszu polecenia lub ustawić dla nich zmienną środowiskową.

- Ustaw **SCOPE_CPP_SDK** zmienną środowiskową.

    Jeśli program Microsoft Visual C++ i zestaw Windows SDK są instalowane w programie Data Lake Tools for Visual Studio, sprawdź, czy masz następujący folder:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Zdefiniuj nową zmienną środowiskową o nazwie **SCOPE_CPP_SDK,** aby wskazać ten katalog. Lub skopiuj folder do innej lokalizacji i określ **SCOPE_CPP_SDK** jako tę.

    Oprócz ustawienia zmiennej środowiskowej można określić argument **-CppSDK** podczas korzystania z wiersza polecenia. Ten argument zastępuje domyślną zmienną środowiskową CppSDK.

- Ustaw **LOCALRUN_DATAROOT** zmienną środowiskową.

    Zdefiniuj nową zmienną środowiskową o nazwie **LOCALRUN_DATAROOT,** która wskazuje katalog główny danych.

    Oprócz ustawienia zmiennej środowiskowej można określić argument **-DataRoot** ze ścieżką katalogu głównego danych podczas korzystania z wiersza polecenia. Ten argument zastępuje domyślną zmienną środowiskową danych. Należy dodać ten argument do każdego uruchomionego wiersza polecenia, aby można było zastąpić domyślną zmienną środowiska głównego danych dla wszystkich operacji.

### <a name="sdk-command-line-usage-samples"></a>Przykłady użycia wiersza polecenia SDK

#### <a name="compile-and-run"></a>Kompilowanie i uruchamianie

Polecenie **uruchom** służy do kompilowania skryptu, a następnie wykonywania skompilowanych wyników. Jego argumenty wiersza polecenia są kombinacją tych z **kompilacji** i **wykonania**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **uruchomienia:**


|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|-CodeBehind|False|Skrypt ma kod .cs za|
|-CppSDK| |Katalog CppSDK|
|-DataRoot| Zmienna środowiska DataRoot|DataRoot dla uruchomienia lokalnego, domyślnie zmienna środowiskowa "LOCALRUN_DATAROOT"|
|-MessageOut| |Zrzucanie wiadomości na konsoli do pliku|
|-Równolegle|1|Uruchamianie planu z określonym równoległością|
|-Referencje| |Lista ścieżek do dodatkowych zestawów referencyjnych lub plików danych kodu za, oddzielonych ';'|
|-UdoRedirect|False|Generowanie konfiguracji przekierowania zestawu Udo|
|-UseDatabase|master|Baza danych do użycia dla kodu za tymczasową rejestracją zestawu|
|-Pełne|False|Pokaż szczegółowe dane wyjściowe ze środowiska wykonawczego|
|-WorkDir|Bieżący katalog|Katalog dla użycia kompilatora i wyjść|
|-RunScopeCEP|0|Tryb ScopeCEP do użycia|
|-ScopeCEPTempPath|Najwyższa temp|Ścieżka tymczasowa do użycia do przesyłania strumieniowego danych|
|-OptFlags| |Oddzielona przecinkami lista flag optymalizatora|


Oto przykład:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Oprócz łączenia **kompilacji** i **wykonywania,** można kompilować i wykonywać skompilowane pliki wykonywalne oddzielnie.

#### <a name="compile-a-u-sql-script"></a>Kompilowanie skryptu U-SQL

Polecenie **kompilacji** służy do kompilowania skryptu U-SQL do plików wykonywalnych.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Poniżej przedstawiono **opcjonalne argumenty kompilacji:**


|Argument|Opis|
|--------|-----------|
| -CodeBehind [wartość domyślna 'False']|Skrypt ma kod .cs za|
| -CppSDK [wartość domyślna '']|Katalog CppSDK|
| -DataRoot [wartość domyślna 'Zmienna środowiskowa DataRoot']|DataRoot dla uruchomienia lokalnego, domyślnie zmienna środowiskowa "LOCALRUN_DATAROOT"|
| -MessageOut [wartość domyślna '']|Zrzucanie wiadomości na konsoli do pliku|
| -Odwołania [wartość domyślna '']|Lista ścieżek do dodatkowych zestawów referencyjnych lub plików danych kodu za, oddzielonych ';'|
| -Shallow [wartość domyślna 'False']|Płytka kompilacja|
| -UdoRedirect [wartość domyślna 'False']|Generowanie konfiguracji przekierowania zestawu Udo|
| -UseDatabase [wartość domyślna 'master']|Baza danych do użycia dla kodu za tymczasową rejestracją zestawu|
| -WorkDir [wartość domyślna 'Bieżący katalog']|Katalog dla użycia kompilatora i wyjść|
| -RunScopeCEP [wartość domyślna '0']|Tryb ScopeCEP do użycia|
| -ScopeCEPTempPath [wartość domyślna 'temp']|Ścieżka tymczasowa do użycia do przesyłania strumieniowego danych|
| -OptFlags [wartość domyślna '']|Oddzielona przecinkami lista flag optymalizatora|


Oto kilka przykładów użycia.

Skompiluj skrypt U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Skompiluj skrypt U-SQL i ustaw folder główny danych. Należy zauważyć, że spowoduje to zastąpienie ustawionej zmiennej środowiskowej.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Skompiluj skrypt U-SQL i ustaw działający katalog, zestaw odwołań i bazę danych:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Wykonywanie skompilowanych wyników

Polecenie **execute** jest używane do wykonywania skompilowanych wyników.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **wykonania:**

|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|-DataRoot | '' |Katalog główny danych do wykonywania metadanych. Domyślnie jest to zmienna środowiskowa **LOCALRUN_DATAROOT.**|
|-MessageOut | '' |Zrzucaj wiadomości na konsoli do pliku.|
|-Równolegle | '1' |Wskaźnik do uruchamiania generowanych kroków przebiegu lokalnego z określonym poziomem równoległości.|
|-Pełne | "Fałsz" |Wskaźnik, aby wyświetlić szczegółowe wyjścia ze środowiska wykonawczego.|

Oto przykład użycia:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Używanie SDK z interfejsami programowania

Interfejsy programowania znajdują się w LocalRunHelper.exe. Można ich używać do integracji funkcji SDK U-SQL i struktury testów C#, aby skalować test lokalny skryptu U-SQL. W tym artykule użyję standardowego projektu testu jednostkowego języka C#, aby pokazać, jak używać tych interfejsów do testowania skryptu U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1: Tworzenie projektu i konfiguracji testu jednostkowego języka C#

- Utwórz projekt testu jednostkowego języka C# za pośrednictwem projektu > > nowego > projektu > Visual C# > test > unit test project.
- Dodaj LocalRunHelper.exe jako odwołanie do projektu. LocalRunHelper.exe znajduje się w \build\runtime\LocalRunHelper.exe w pakiecie Nuget.

    ![Odwołanie do dodawania dodatku usługi Azure Data Lake U-SQL](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- Zestaw U-SQL SDK obsługuje **tylko** środowisko x64, upewnij się, że ustawisz docelową platformę kompilacji jako x64. Można ustawić, że za pośrednictwem project property > Build > platformie docelowej.

    ![Azure Data Lake U-SQL SDK Configure x64 Project](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Upewnij się, że środowisko testowe jest ustawione jako x64. W programie Visual Studio można ustawić go za pomocą ustawień testu > testowego > domyślnej architektury procesora > x64.

    ![Azure Data Lake U-SQL SDK Configure x64 Test Environment](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Upewnij się, że wszystkie pliki zależności w obszarze NugetPackage\build\runtime\ do katalogu roboczego projektu, który jest zwykle w obszarze ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2: Tworzenie przypadku testu skryptu U-SQL

Poniżej znajduje się przykładowy kod do testu skryptu U-SQL. Do testowania należy przygotować skrypty, pliki wejściowe i oczekiwane pliki wyjściowe.

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


### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfejsy programowania w LocalRunHelper.exe

LocalRunHelper.exe udostępnia interfejsy programowania dla lokalnej kompilacji U-SQL, uruchamiania itp. Interfejsy są wymienione w następujący sposób.

**Konstruktor**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parametr|Typ|Opis|
|---------|----|-----------|
|komunikatOutput|System.IO.TextWriter|dla komunikatów wyjściowych, ustawiona na wartość null, aby użyć konsoli|

**Właściwości**

|Właściwość|Typ|Opis|
|--------|----|-----------|
|AlgebraPath (AlgebraPath)|ciąg|Ścieżka do pliku algebry (plik algebra jest jednym z wyników kompilacji)|
|Wnioskowanie codebehindReferences|ciąg|Jeśli skrypt ma dodatkowy kod za odwołaniami, określ ścieżki oddzielone ';'|
|CppSdkDir ( CppSdkDir )|ciąg|Katalog CppSDK|
|CurrentDir (CurrentDir)|ciąg|Bieżący katalog|
|DataRoot|ciąg|Ścieżka katalogu głównego danych|
|DebuggerMailPath|ciąg|Ścieżka do debugera mailslot|
|GenerowanieUdoRedirect|bool|Jeśli chcemy wygenerować przekierowanie ładowania zestawu zastąpić config|
|HasCodeBehind ( HasCodeBehind )|bool|Jeśli skrypt ma za sobą kod|
|InputDir (dir danych wejściowych|ciąg|Katalog danych wejściowych|
|Ścieżka wiadomości|ciąg|Ścieżka pliku zrzutu wiadomości|
|WynikiDir|ciąg|Katalog danych wyjściowych|
|Równoległości prostych|int|Równoległość do uruchomienia algebry|
|Identyfikator nadrzędny|int|PID nadrzędnego, na którym usługa monitoruje wyjście, ustawiona na 0 lub ujemną, aby zignorować|
|Resultpath|ciąg|Ścieżka pliku zrzutu wyników|
|Program runtimeDir|ciąg|Katalog środowiska uruchomieniowego|
|Ścieżka skryptu|ciąg|Gdzie znaleźć skrypt|
|Płytkie|bool|Płytka kompilacja lub nie|
|TempDir ( TempDir )|ciąg|Katalog temp|
|Użyj Bazy danych|ciąg|Określ bazę danych, która ma być używana dla kodu za tymczasową rejestracją zestawu, domyślnie wzorzec|
|Program roboczy|ciąg|Preferowany katalog roboczy|


**Metoda**

|Metoda|Opis|Zwraca|Parametr|
|------|-----------|------|---------|
|publiczne bool DoCompile()|Kompilowanie skryptu U-SQL|Prawda o sukcesie| |
|publiczny bool DoExec()|Wykonywanie skompilowanego wyniku|Prawda o sukcesie| |
|publiczny bool DoRun()|Uruchamianie skryptu U-SQL (Compile + Execute)|Prawda o sukcesie| |
|publiczny bool IsValidRuntimeDir(ścieżka ciągu)|Sprawdzanie, czy dana ścieżka jest prawidłowa, ścieżka środowiska uruchomieniowego|Prawda dla prawidłowego|Ścieżka katalogu środowiska wykonawczego|


## <a name="faq-about-common-issue"></a>Często zadawane pytania dotyczące wspólnego problemu

### <a name="error-1"></a>Błąd 1:
E_CSC_SYSTEM_INTERNAL: Błąd wewnętrzny! Nie można załadować pliku lub zestawu "ScopeEngineManaged.dll" lub jednej z jego zależności. Nie można odnaleźć określonego modułu.

Proszę sprawdzić, co następuje:

- Upewnij się, że masz środowisko x64. Platforma docelowa kompilacji i środowiska testowego powinny być x64, odnoszą się do **kroku 1: Tworzenie projektu testu jednostkowego języka C# i konfiguracji** powyżej.
- Upewnij się, że wszystkie pliki zależności zostały skopiowane w obszarze NugetPackage\build\runtime\ do katalogu roboczego projektu.


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Aby rejestrować informacje diagnostyczne, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Aby wyświetlić bardziej złożoną kwerendę, zobacz [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby wyświetlić szczegóły zadania, zobacz [Korzystanie z funkcji Przeglądarka zadań i Widok zadań dla usługi Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Aby użyć widoku wykonywania wierzchołków, zobacz [Użyj widoku wykonania wierzchołka w narzędziach usługi Data Lake dla programu Visual Studio.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
