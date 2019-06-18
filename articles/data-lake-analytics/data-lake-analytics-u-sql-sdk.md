---
title: Uruchamianie i testowanie zadań U-SQL lokalnie przy użyciu zestawu SDK usługi Azure Data Lake U-SQL
description: Dowiedz się, jak uruchomić i przetestować zadań U-SQL lokalnie przy użyciu wiersza polecenia i interfejsy na lokalnej stacji roboczej.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 14908225e78b79cb748e712ae23643ddde4a4242
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813489"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Uruchamianie i testowanie U-SQL przy użyciu zestawu SDK usługi Azure Data Lake U-SQL

Podczas tworzenia skryptu U-SQL, są często do uruchomienia i skrypt testu U-SQL lokalnie przed przesłaniem jej do chmury. Usługa Azure Data Lake udostępnia pakiet Nuget o nazwie zestawu SDK usługi Azure Data Lake U-SQL, w tym scenariuszu, za pomocą którego można łatwo skalować U-SQL, uruchamianie i testowanie. Istnieje również możliwość integracji ten test U-SQL przy użyciu systemu ciągłej integracji (ciągła integracja) do zautomatyzowania kompilowania i testowania.

Jeśli interesujące Cię jak ręcznie lokalne uruchamianie i debugowanie skryptu U-SQL za pomocą narzędzi z graficznym interfejsem użytkownika, możesz użyć narzędzi Azure Data Lake Tools dla programu Visual Studio, w tym. Dowiedz się więcej z [tutaj](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Zestaw SDK instalacji usługi Azure Data Lake w języku U-SQL

Można uzyskać zestawu SDK usługi Azure Data Lake U-SQL [tutaj](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) w witrynie Nuget.org. I przed jej użyciem należy upewnij się, że masz następujące zależności.

### <a name="dependencies"></a>Zależności

Zestaw SDK danych U-SQL Lake wymaga następujących zależności:

- [Microsoft .NET Framework 4.6 lub nowszej](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 i zestaw Windows SDK 10.0.10240.0 lub nowszej (nazywana CppSDK w tym artykule). Istnieją dwa sposoby uzyskania CppSDK:

  - Zainstaluj [programu Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). W folderze Program Files — na przykład C:\Program Files (x86) \Windows Kits\10\, będziesz mieć folderu \Windows Kits\10. Zawiera ona również wersję zestawu Windows 10 SDK w ramach \Windows Kits\10\Lib. Jeśli nie widzisz tych folderów, zainstaluj ponownie program Visual Studio i pamiętaj o wybraniu zestawu Windows 10 SDK podczas instalacji. Jeśli masz to zainstalowane z programem Visual Studio, kompilator lokalnego języka U-SQL zostanie okazać się automatycznie.

    ![Narzędzia Data Lake Tools for Visual Studio SDK systemu Windows 10 do uruchamiania lokalnego](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Zainstaluj [usługi Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs). Możesz znaleźć, to wstępnie spakowane zestawy Visual C++ i Windows SDK plików w C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. W tym przypadku kompilator lokalnego języka U-SQL nie można automatycznie odnaleźć zależności. Należy określić ścieżkę CppSDK dla niego. Możesz skopiować pliki do innej lokalizacji lub użyć go jako jest.

## <a name="understand-basic-concepts"></a>Zrozumieć podstawowe pojęcia

### <a name="data-root"></a>Katalog głównych danych

Folder główny danych jest "Magazyn lokalny" dla konta lokalnego obliczeń. Jest to równoważne do konta usługi Azure Data Lake Store w ramach konta usługi Data Lake Analytics. Przełączanie do folderu innego katalogu głównego danych jest podobnie jak przełączenie na konto magazynu w innej. Jeśli chcesz uzyskać dostęp do danych powszechnie udostępnione z innego katalogu głównego danych folderami, należy użyć ścieżek bezwzględnych w skryptach. Lub, tworzenie łączy symbolicznych systemu plików (na przykład **mklink** na systemie plików NTFS) w folderze katalogu głównego danych, aby wskazywał udostępnionych danych.

Folder główny danych służy do:

- Store metadane lokalnego, w tym baz danych, tabel, funkcji z wartościami przechowywanymi w tabeli funkcji (Tvf) i zestawy.
- Wyszukaj ścieżki wejściowe i wyjściowe, które są zdefiniowane jako ścieżki względne w języku U-SQL. Używanie ścieżek względnych sprawia, że łatwiej wdrażać swoje projekty U-SQL na platformie Azure.

### <a name="file-path-in-u-sql"></a>Ścieżka pliku w języku U-SQL

Można użyć ścieżki względnej i lokalną ścieżkę bezwzględną w skryptów U-SQL. Ścieżka względna jest się względem katalogu głównego danych określonej ścieżki folderu. Firma Microsoft zaleca użycie "/" jako separatora ścieżki, aby wprowadzić skryptów zgodnych z po stronie serwera. Poniżej przedstawiono kilka przykładów ścieżek względnych oraz ich równoważne ścieżek bezwzględnych. W tych przykładach C:\LocalRunDataRoot jest folder katalogu głównego danych.

|Ścieżka względna|Ścieżka bezwzględna|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Katalog roboczy

Podczas uruchamiania skryptu U-SQL lokalnie, katalog roboczy jest tworzony podczas kompilacji w ramach bieżącego katalogu uruchamiania. Oprócz danych wyjściowych kompilacji pliki potrzebne środowiska uruchomieniowego pod kątem lokalnego wykonania są kopie w tle woluminów do tego katalogu roboczego. Folder główny katalog roboczy jest nazywana "ScopeWorkDir" i pliki w katalogu roboczym znajdują się w następujący sposób:

|Pliku lub katalogu|Pliku lub katalogu|Pliku lub katalogu|Definicja|Opis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Ciąg skrótu wersji środowiska uruchomieniowego|Pliki środowiska uruchomieniowego, potrzebne pod kątem lokalnego wykonania kopii w tle|
| |Script_66AE4909AA0ED06C| |Nazwa skryptu i wyznaczania wartości skrótu ciągu ścieżka skryptu|Dane wyjściowe kompilacji i wykonania kroku rejestrowania|
| | |\_skrypt\_.abr|Dane wyjściowe kompilatora|Plik algebry|
| | |\_ScopeCodeGen\_.*|Dane wyjściowe kompilatora|Wygenerowane z kodu zarządzanego|
| | |\_ScopeCodeGenEngine\_.*|Dane wyjściowe kompilatora|Wygenerowany kod natywny|
| | |przywoływanych zestawach|Odwołanie do zestawu|Przywoływany zestaw plików|
| | |deployed_resources|Wdrażanie zasobów|Pliki wdrożenia zasobów|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Dziennik wykonywania|Dziennik wykonywania|


## <a name="use-the-sdk-from-the-command-line"></a>Korzystanie z zestawu SDK z poziomu wiersza polecenia

### <a name="command-line-interface-of-the-helper-application"></a>Interfejs wiersza polecenia z aplikacją pomocniczą

W obszarze SDK directory\build\runtime LocalRunHelper.exe to aplikacja wiersza polecenia pomocnika, która zapewnia interfejsy do większości powszechnie używane funkcje uruchamiania lokalnego. Należy pamiętać, że polecenia i przełączniki argumentów jest rozróżniana wielkość liter. Aby wywołać go:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Uruchom LocalRunHelper.exe bez argumentów lub za pomocą **pomocy** przełącznika, aby wyświetlić informacje pomocy:

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
-  **Wymagany Argument** listę argumentów, które muszą być dostarczane.  
-  **Opcjonalny Argument** listę argumentów, które są opcjonalne, przy użyciu wartości domyślnych.  Opcjonalne argumenty logiczne nie ma parametrów, a ich wygląd oznacza ujemna, aby przywrócić wartości domyślne.

### <a name="return-value-and-logging"></a>Rejestrowanie i wartość zwracana

Zwraca aplikacją pomocniczą **0** w celu osiągnięcia sukcesu i **-1** błędu. Domyślnie pomocnika przesyła wszystkie komunikaty do bieżącej konsoli. Jednak większość poleceń obsługują **path_to_log_file - MessageOut** opcjonalny argument, który przekierowuje dane wyjściowe do pliku dziennika.

### <a name="environment-variable-configuring"></a>Konfigurowanie zmiennych środowiska

Wymaga uruchomienia głównego określone dane jako konto magazynu lokalnego, a także określona ścieżka CppSDK zależności lokalnego języka U-SQL. Można jednocześnie ustawić argument w zmiennej środowiskowej wiersza polecenia lub ustaw dla nich.

- Ustaw **SCOPE_CPP_SDK** zmiennej środowiskowej.

    Jeśli program Microsoft Visual C++ i zestaw SDK Windows po zainstalowaniu narzędzi Data Lake Tools for Visual Studio, sprawdź, czy następujący folder:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Zdefiniuj zmienną środowiskową o nazwie **SCOPE_CPP_SDK** aby wskazywały do tego katalogu. Skopiuj folder do innej lokalizacji lub określ **SCOPE_CPP_SDK** jak.

    Oprócz ustawienie zmiennej środowiskowej, można określić **- CppSDK** argument podczas korzystania z wiersza polecenia. Argument ten zastępuje swojej zmiennej środowiskowej CppSDK domyślne.

- Ustaw **LOCALRUN_DATAROOT** zmiennej środowiskowej.

    Zdefiniuj zmienną środowiskową o nazwie **LOCALRUN_DATAROOT** wskazuje katalog głównych danych.

    Oprócz ustawienie zmiennej środowiskowej, można określić **— ścieżka folderu DataRoot** argument o ścieżce katalogu głównego danych podczas korzystania z wiersza polecenia. Argument ten zastępuje zmiennej środowiska domyślnego katalogu głównego danych. Należy dodać ten argument do każdego wiersza polecenia, którego używasz, dzięki czemu można zastąpić zmienną środowiskową domyślnego katalogu głównego danych dla wszystkich operacji.

### <a name="sdk-command-line-usage-samples"></a>Przykłady użycia wiersza polecenia zestawu SDK

#### <a name="compile-and-run"></a>Kompilowanie i uruchamianie

**Uruchom** polecenie służy do kompilować skrypt, a następnie uruchomić skompilowany wyników. Argumenty wiersza polecenia są te z kombinacją **skompilować** i **wykonania**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **Uruchom**:


|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|-CodeBehind|False|Skrypt zawiera .cs CodeBehind|
|-CppSDK| |CppSDK Directory|
|-DataRoot| Ścieżka folderu DataRoot, zmienna środowiskowa|Ścieżka folderu DataRoot dla lokalnego Uruchom domyślną wartość zmiennej środowiskowej "LOCALRUN_DATAROOT"|
|-MessageOut| |Komunikaty na konsoli do pliku zrzutu|
|-Równoległych|1|Uruchom planu z określonym równoległości|
|— Odwołania| |Lista ścieżek do dodatkowych odwołań do zestawów lub plików z danymi o kodzie, oddzielone ";"|
|-UdoRedirect|False|Generowanie konfiguracji przekierowania zestawu operatory zdefiniowane przez użytkownika|
|-UseDatabase|master|Bazy danych na potrzeby kod związany z rejestracji zestawów tymczasowego|
|-Verbose|False|Pokaż szczegółowe dane wyjściowe z środowiska uruchomieniowego|
|-WorkDir|Bieżący katalog|Katalog do użycia przez kompilator i danych wyjściowych|
|-RunScopeCEP|0|Tryb ScopeCEP do użycia|
|-ScopeCEPTempPath|Temp|Ścieżka tymczasowa do użycia dla danych przesyłanych strumieniowo|
|-OptFlags| |Rozdzielaną przecinkami listę flag optymalizatora|


Oto przykład:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Oprócz łączenie **skompilować** i **wykonania**, można kompilować, a także wykonywanie skompilowanych plikach wykonywalnych oddzielnie.

#### <a name="compile-a-u-sql-script"></a>Kompiluj skrypt U-SQL

**Skompilować** polecenie służy do kompilowania skryptu U-SQL do plików wykonywalnych.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **skompilować**:


|Argument|Opis|
|--------|-----------|
| -CodeBehind [wartość domyślna "False"]|Skrypt zawiera .cs CodeBehind|
| -CppSDK [wartość domyślna "]|CppSDK Directory|
| -DataRoot [wartość domyślna 'Zmiennej środowiskowej ścieżka folderu DataRoot']|Ścieżka folderu DataRoot dla lokalnego Uruchom domyślną wartość zmiennej środowiskowej "LOCALRUN_DATAROOT"|
| -MessageOut [wartość domyślna "]|Komunikaty na konsoli do pliku zrzutu|
| -Odwołuje się do [wartość domyślna "]|Lista ścieżek do dodatkowych odwołań do zestawów lub plików z danymi o kodzie, oddzielone ";"|
| -Produkowanymi [wartość domyślna "False"]|Skrócona kompilacji|
| -UdoRedirect [wartość domyślna "False"]|Generowanie konfiguracji przekierowania zestawu operatory zdefiniowane przez użytkownika|
| -UseDatabase [wartość domyślna "master"]|Bazy danych na potrzeby kod związany z rejestracji zestawów tymczasowego|
| -WorkDir [wartość domyślna 'Bieżący katalog']|Katalog do użycia przez kompilator i danych wyjściowych|
| -RunScopeCEP [wartość domyślna "0"]|Tryb ScopeCEP do użycia|
| -ScopeCEPTempPath [wartość domyślna "temp"]|Ścieżka tymczasowa do użycia dla danych przesyłanych strumieniowo|
| -OptFlags [wartość domyślna "]|Rozdzielaną przecinkami listę flag optymalizatora|


Poniżej przedstawiono kilka przykładów użycia.

Kompiluj skrypt U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Skompiluj skrypt U-SQL, a następnie ustaw folder katalogu głównego danych. Należy pamiętać, że spowoduje to zastąpienie Ustaw zmienną środowiskową.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Skompilować skrypt U-SQL i ustawić katalogu roboczego, odwołanie do zestawu i bazy danych:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Wykonaj skompilowanych wyników

**Wykonania** polecenie służy do wykonywania skompilowanych wyników.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Poniżej przedstawiono opcjonalne argumenty **wykonania**:

|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|-DataRoot | '' |Do wykonania metadanych katalogu głównego danych. Jego wartość domyślna to **LOCALRUN_DATAROOT** zmiennej środowiskowej.|
|-MessageOut | '' |Zrzuć komunikaty na konsoli do pliku.|
|-Równoległych | '1' |Wskaźnik do uruchomienia wygenerowanego kroki uruchamiania lokalnego z poziomu określonego równoległości.|
|-Verbose | 'False' |Wskazuje, aby wyświetlić szczegółowe dane wyjściowe z środowiska uruchomieniowego.|

Poniżej przedstawiono przykład użycia:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Interfejsy programowania za pomocą zestawu SDK

Interfejsy programowania znajdują się w LocalRunHelper.exe. Można je zintegrować funkcje zestawu SDK U-SQL i struktury testowej C#, skalowanie testu lokalnego skryptu U-SQL. W tym artykule użyję standard języka C# projekt testu jednostkowego i pokazuje, jak przetestować skrypt U-SQL przy użyciu tych interfejsów.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1: Tworzenie C# testów jednostkowych projektu i konfiguracji

- Tworzenie projektu testu jednostkowego języka C# za pomocą pliku > Nowy > Projekt > Visual C# > Test > Projekt testów jednostkowych.
- Dodaj LocalRunHelper.exe jako odwołanie dla projektu. LocalRunHelper.exe znajduje się w \build\runtime\LocalRunHelper.exe pakietu Nuget.

    ![Zestaw SDK usługi Azure Data Lake w języku U-SQL, Dodaj odwołanie](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- Zestaw SDK języka U-SQL **tylko** środowisko obsługi x64, upewnij się ustawić element docelowy kompilacji platformy jako x64. Można ustawić, za pomocą właściwości Projekt > kompilacji > platformę docelową.

    ![Zestaw SDK usługi Azure Data Lake w języku U-SQL, skonfiguruj x64 projektu](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Upewnij się ustawić danego środowiska testowego jako x64. W programie Visual Studio, można ustawić za pomocą testu > Ustawienia testu > domyślna architektura procesora > x64.

    ![Zestaw SDK usługi Azure Data Lake w języku U-SQL, skonfiguruj x64 środowiska testowego](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Upewnij się skopiować wszystkie pliki zależności, w obszarze NugetPackage\build\runtime\ do katalogu, który zazwyczaj znajduje się w obszarze ProjectFolder\bin\x64\Debug roboczego projektu.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2: Utwórz przypadek testowy skryptu U-SQL

Poniżej przedstawiono przykładowy kod do testowania skryptów U-SQL. W przypadku testowania, które trzeba przygotować skrypty, pliki wejściowe i pliki oczekiwanych danych wyjściowych.

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

LocalRunHelper.exe zapewnia interfejsy programowania U-SQL lokalnej kompilacji, uruchom itp. Interfejsy są wymienione w następujący sposób.

**Konstruktor**

publiczne LocalRunHelper ([System.IO.TextWriter messageOutput = null])

|Parametr|Typ|Opis|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|Komunikaty wyjściowe ustawiona na wartość null, przy użyciu konsoli|

**Właściwości**

|Właściwość|Typ|Opis|
|--------|----|-----------|
|AlgebraPath|string|Ścieżka do pliku algebry (plik algebry jest jeden z wyników kompilacji)|
|CodeBehindReferences|string|Jeśli skrypt zawiera dodatkowy kod związany z odwołania, określ ścieżki oddzielonych ";"|
|CppSdkDir|string|CppSDK directory|
|CurrentDir|string|Bieżący katalog|
|DataRoot|string|Ścieżka katalogu głównego danych|
|DebuggerMailPath|string|Ścieżka do debugera mailslot|
|GenerateUdoRedirect|bool|Jeśli chcemy wygenerować z ładowaniem zestawu, przekierowanie zastępują konfiguracji|
|HasCodeBehind|bool|Jeśli skrypt zawiera kod związany z|
|InputDir|string|Katalog dla danych wejściowych|
|MessagePath|string|Ścieżka pliku zrzutu wiadomości|
|OutputDir|string|Katalog danych wyjściowych|
|Równoległość|int|Równoległość topologii w celu uruchomienia algebry|
|ParentPid|int|Identyfikator PID elementu nadrzędnego, na którym usługa monitoruje aby wyjść, równa 0 lub ujemne, aby zignorować|
|ResultPath|string|Ścieżka pliku zrzutu wyniku|
|RuntimeDir|string|Katalogu środowiska uruchomieniowego|
|ScriptPath|string|Gdzie można znaleźć skryptu|
|Skrócona|bool|Skrócona kompilacji, czy nie|
|Element TempDir|string|Katalog tymczasowy|
|UseDataBase|string|Określ bazę danych do użytku w kodzie rejestracji tymczasowe zestawu głównego domyślnie|
|WorkDir|string|Preferowany katalog roboczy|


**— Metoda**

|Metoda|Opis|Wróć|Parametr|
|------|-----------|------|---------|
|public bool DoCompile()|Kompiluj skrypt U-SQL|Wartość true w przypadku powodzenia| |
|publiczne bool DoExec()|Wynik wykonania|Wartość true w przypadku powodzenia| |
|publiczne bool DoRun()|Uruchom skrypt U-SQL (kompilacji i wykonania)|Wartość true w przypadku powodzenia| |
|publiczne bool IsValidRuntimeDir (ciąg ścieżki)|Sprawdź, czy ścieżka prawidłowy środowiska uruchomieniowego podanej ścieżce|Wartość true, aby uzyskać prawidłowy|Ścieżka katalogu środowiska uruchomieniowego|


## <a name="faq-about-common-issue"></a>Często zadawane pytania dotyczące Typowym problemem

### <a name="error-1"></a>Błąd 1:
E_CSC_SYSTEM_INTERNAL: Błąd wewnętrzny! Nie można załadować pliku lub zestawu "ScopeEngineManaged.dll" lub jednej z jego zależności. Nie można odnaleźć określonego modułu.

Sprawdź, czy następujące czynności:

- Upewnij się, że masz x64 środowiska. Platforma docelowa kompilacji i środowiska testowego należy x64, zapoznaj się **krok 1: Tworzenie C# testów jednostkowych projektu i konfiguracji** powyżej.
- Upewnij się, że wszystkie pliki zależności w ramach NugetPackage\build\runtime\ zostały skopiowane do katalogu roboczego projektu.


## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Aby rejestrować informacje diagnostyczne, zobacz [dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Aby wyświetlić bardziej złożonego zapytania, zobacz [analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby wyświetlić szczegóły zadania, zobacz [korzystanie z przeglądarki zadań i widoku zadań dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Aby użyć widoku wykonania wierzchołka, zobacz [korzystanie z widoku wykonania wierzchołka w narzędziach Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
