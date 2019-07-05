---
title: Rozwiązywanie problemów z usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługi Azure Data Factory. Dokument wspólne dla wszystkich zewnętrznych i działania sterowania.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshoot
ms.subservice: troubleshoot
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 8d6ab565098e1ea40ede5c650f05e670a1edc7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452685"
---
# <a name="troubleshooting-azure-data-factory"></a>Rozwiązywanie problemów z usługi Azure Data Factory
W tym artykule wymieniono typowe pytania dotyczące rozwiązywania problemów.

- [Usługa Azure Databricks (Notes, plikach JAR, python)](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [Custom (Azure Batch)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, Pig, przesyłanie strumieniowe usługi Hadoop)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Działanie internetowe](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| Kod błędu | Komunikat o błędzie                                          | Opis problemu                             | Poprawka możliwości / Zalecana akcja                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Błąd 403                                                    | Wygasł token dostępu usługi Databricks.                         | Domyślnie token dostępu usługi Databricks jest ważny przez 90 dni.  Utwórz nowy token i zaktualizuj połączoną usługę. |
| 3201           | Brak pola wymaganego: settings.task.notebook_task.notebook_path | Tworzenia nieprawidłowe: Nie określono poprawnie ścieżkę notesu. | Określ ścieżkę notesu usługi Databricks działania. |
| 3201           | Klastra... nie istnieje                                 | Błąd tworzenia pakietów administracyjnych: Databricks w klastrze nie istnieje lub została usunięta. | Sprawdź, czy istnieje klastra usługi Databricks. |
| 3201           | Python nieprawidłowy plik identyfikatora URI:... Odwiedź stronę usługi Databricks i obsługi obsługiwane schematy identyfikatorów URI. | Tworzenia nieprawidłowe                                                | Określ ścieżki bezwzględnej dla schematów adresowania obszaru roboczego lub "dbfs:/folder/subfolder/foo.py" dla plików przechowywanych w DBFS. |
| 3201           | {0}   LinkedService powinien być domeny i accessToken wymaganych właściwości | Tworzenia nieprawidłowe                                                | Sprawdź, czy [definicji usługi połączonej](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   LinkedService określić istniejący klaster identyfikator lub nowe informacje o klastrze dla tworzenia | Tworzenia nieprawidłowe                                                | Sprawdź, czy [definicji usługi połączonej](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Typ węzła Standard_D16S_v3 nie jest obsługiwany. Obsługiwane typy węzłów:   Standardowa_ds3_v2 Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, maszyna wirtualna Standard_D3_v2, maszyna wirtualna Standard_D8_v3, maszyna wirtualna Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, maszyna wirtualna Standard_D12_v2, maszyna wirtualna Standard_D13_v2, Maszyna wirtualna Standard_D14_v2, maszyna wirtualna Standard_D15_v2, Standard_DS12_v2, standardowa_ds13_v2, Standard_DS14_v2, standardowa_ds15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s Standard_F8s, Standard_F16s, maszyna wirtualna standard_h16 —, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, maszyna wirtualna Standard_NC12, maszyna wirtualna Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ Nc24s_v3 w warstwie, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2 Standard_L64s_v2, Standard_L80s_v2 | Tworzenia nieprawidłowe                                                | Zapoznaj się komunikat o błędzie                                          |
| 3201           | Nieprawidłowy notebook_path:... Obecnie obsługiwane są tylko bezwzględne ścieżki. Musi zaczynać się ścieżki "/". | Tworzenia nieprawidłowe                                                | Zapoznaj się komunikat o błędzie                                          |
| 3202           | Zostały już 1000 zadania utworzone w poprzednich 3600 sekund przekraczające limit żądań:   Tworzenie zadania 1000 na 3600 sekund. | Zbyt wiele przebiegów usługi Databricks w ciągu godziny.                         | Sprawdź wszystkie potoki, które używają tego obszaru roboczego usługi Databricks na kurs tworzenia zadania.  Jeśli potoki uruchomiony zbyt wiele przebiegów usługi Databricks w agregacji, należy migrować niektóre potoki, do nowego obszaru roboczego. |
| 3202           | Nie można przeanalizować obiekt żądania: Oczekiwano "key" i "value" należy ustawić dla base_parameters pola mapy JSON, otrzymano "klucz:"..."" Zamiast tego. | Błąd tworzenia pakietów administracyjnych: Brak wartości parametru         | Zbadaj json potoku i upewnij się, że wszystkie parametry w notesie baseParameters niepusta wartość określony. |
| 3202           | Użytkownik: SimpleUserContext {userId =..., nazwa =user@company.com, identyfikatorem organizacji =...} nie ma autoryzacji do dostępu do klastra | Użytkownik, który wygenerował token dostępu nie może dostęp do klastra usługi Databricks, określonej w połączonej usłudze. | Upewnij się, że użytkownik ma wymagane uprawnienia w obszarze roboczym.   |
| 3203           | Klaster jest w stanie zwolniony, nie jest dostępne do pobrania zadań. Usuń klaster lub spróbuj ponownie później. | Klaster został zakończony.    Interaktywne klastra może to być sytuacja wyścigu. | Najlepszym sposobem uniknięcia tego problemu jest korzystają z klastrów zadania.             |
| 3204           | Wykonanie zadania nie powiodło się. Może to być dowolna liczba komunikatów o błędach ze stanu klastra nieoczekiwany komunikat specyficzne dla działania.  Najczęściej występujące jest bez komunikatu o błędzie. | ND                                                          | ND                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| Kod błędu:         | Komunikat o błędzie                                                | Opis problemu                                          | Poprawka możliwości / Zalecana akcja                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Token dostępu pochodzi od niewłaściwego dzierżawy                    | Niepoprawne dzierżawy usługi AAD                                         | Nazwa główna usługi, które umożliwiają dostęp do usługi ADLA należy do innej dzierżawy usługi AAD. W tej samej dzierżawy jako konto ADLA, Utwórz nową jednostkę usługi. |
| 2711,   2705,   2704 | Forbidden. Nie można zweryfikować listy ACL. Zasób nie istnieje albo użytkownik nie ma autoryzacji do wykonania żądanej operacji<br/><br/>Użytkownik nie będzie mógł uzyskać dostępu do magazynu datalake  <br/><br/>Użytkownik nie ma uprawnień do usługi data lake analytics | Nazwa główna usługi lub podany certyfikat nie ma dostępu do pliku w magazynie | Upewnij się, że nazwa główna usługi lub certyfikat, podane dla zadań ADLA ma dostęp do domyślnego magazynu usługi ADLS oraz konta ADLA dla niego wobec folderu głównego. |
| 2711                 | Nie można odnaleźć "Usługi Azure Data Lake Store" pliku lub folderu       | Ścieżka do pliku skryptu u-SQL jest nieprawidłowy lub połączonej usługi, poświadczenia nie masz dostępu | Sprawdź, czy ścieżka i poświadczenia podane w połączonej usłudze |
| 2707                 | Nie można rozpoznać konta AzureDataLakeAnalytics. Sprawdź, czy "AccountName" i "Wartości DataLakeAnalyticsUri". | Konto ADLA w połączonej usłudze jest nieprawidłowy                  | Sprawdź, czy odpowiednie konto jest udostępniane             |
| 2703                 | Identyfikator błędu: E_CQO_SYSTEM_INTERNAL_ERROR lub dowolnego błędu rozpoczyna się od ciągu "Identyfikator błędu:" | Błąd pochodzi z usługi ADLA                                    | Wszelkie błędy, które wygląda na to przykład oznacza, że zadanie zostało przesłane ADLA i istnieje skryptu nie powiodło się. Badanie musi odbywać się dla usługi ADLA. Jeśli po otwarciu portalu przejdź do konta ADLA, poszukaj zadania za pomocą usługi ADF działania Uruchom Id (identyfikator uruchomienia potoku). Zadanie będzie mieć więcej informacji o błędzie i ma na celu rozwiązywanie problemów. Jeśli rozdzielczość nie jest jasne, skontaktuj się z zespołem pomocy technicznej ADLA i podaj adres URL zadania, która zawiera nazwę konta i identyfikator zadania. |
| 2709                 | Firma Microsoft nie może akceptować zadania w tej chwili. Maksymalna liczba zadań w kolejce dla swojego konta to 200. | Ograniczanie przepustowości dla usługi ADLA                                           | Zmniejsz liczbę przesłane zadania na konto ADLA, zmieniając ADF wyzwalaczy i ustawieniami współbieżności działań lub zwiększyć limity dla usługi ADLA |
| 2709                 | To zadanie zostało odrzucone, ponieważ wymaga ona 24 AUs i to konto ma zasadę zdefiniowane przez administratora, który uniemożliwia zadania przy użyciu więcej niż 5 jednostek analizy. | Ograniczanie przepustowości dla usługi ADLA                                           | Zmniejsz liczbę przesłane zadania na konto ADLA, zmieniając ADF wyzwalaczy i ustawieniami współbieżności działań lub zwiększyć limity dla usługi ADLA |



## <a name="azure-functions"></a>Azure Functions

| Kod błędu: | Komunikat o błędzie                           | Opis                                                  | Poprawka możliwości / Zalecana akcja                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Zawartość odpowiedzi nie jest prawidłową jobject zostanie opróżniony | Oznacza to, że funkcji platformy Azure, który wywołuje się, masz nie zwrócił w odpowiedzi ładunek JSON. Działanie funkcji ADF Azure obsługuje tylko zawartość odpowiedzi w formacie JSON. | Aktualizacja funkcji platformy Azure, aby zwrócić nieprawidłowy ładunek w formacie JSON, np. C# funkcja może zwrócić nowe (ActionResult) < OkObjectResult ("{`\"Id\":\"123\"`}"); |
| 3600         | Nieprawidłowy HttpMethod: "..".               | Oznacza to, że metoda Http, określony w ładunku działania nie jest obsługiwana przez działanie funkcji platformy Azure. | Dostępne są następujące metody Http, które są obsługiwane:  <br/>PUT, POST, POBIERZ, USUWANIE, OPCJE, HEAD, ŚLEDZENIA |



## <a name="custom-azure-batch"></a>Custom (Azure Batch)
| Kod błędu: | Komunikat o błędzie                                                | Opis                                                  | Poprawka możliwości / Zalecana akcja                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Wprowadzam polecenie nieoczekiwany wyjątek i wykonywanie nie powiodło się.             | Nie można uruchomić polecenia lub program zwrócił kod błędu. | Sprawdź, czy istnieje plik wykonywalny. Jeśli program jest uruchamiany, sprawdź stdout.txt i stderr.txt przekazane do konta magazynu. Jest dobrą praktyką do emitowania dzienników dużą w kodzie do debugowania. |
| 2501         | Nie można uzyskać dostępu do konta usługi batch, sprawdź, czy ustawienia konta usługi batch. | Niepoprawne Batch dostępu do klucza lub puli podano nazwę.            | Musimy zweryfikować nazwę puli i klucz dostępu usługi Batch w połączonej usłudze. |
| 2502         | Może nie magazyn użytkownika z dostępem do konta,. Sprawdź ustawienia konta magazynu. | Nieprawidłowy klucz konta magazynu nazwy lub dostępu podany.       | Musimy zweryfikować magazynu konta nazwy i klucza dostępu w połączonej usłudze. |
| 2504         | Operacja zwróciła nieprawidłowy kod stanu "Element BadRequest."     | Zbyt wiele plików w folderPath Jeśli działanie niestandardowe.  (Całkowity rozmiar resourceFiles nie może być więcej niż wynosić 32 768 znaków). | Usuń niepotrzebne pliki lub skompresować je, dodając polecenie unzip w taki sposób, aby wyodrębnić, na przykład: powershell.exe - nologo - noprofile — polecenie "& {Add-Type - 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | Nie można utworzyć sygnaturę dostępu współdzielonego, chyba że używane są poświadczenia klucza konta. | Działania niestandardowe obsługują tylko kont magazynu, które używają klucza dostępu. | Zobacz opis                                            |
| 2507         | Ścieżka folderu nie istnieje lub jest pusty:...            | Brak plików na koncie magazynu w określonej ścieżce.       | FolderPath musi zawierać pliki wykonywalne, które chcesz uruchomić. |
| 2508         | Istnieją zduplikowane pliki w folderze zasobów.               | Istnieje wiele plików o takiej samej nazwie w różnych podfolderach folderPath. | Działania niestandardowe, spłaszczenie strukturę folderów w obszarze folderPath.  Jeśli struktura folderów musi zostać zachowane, pliki zip i wyodrębnić je w usłudze Azure Batch przy użyciu polecenia unzip, na przykład: powershell.exe - nologo - noprofile — polecenie "& {Add-Type - 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | Adres url usługi Batch... jest nieprawidłowa, musi być w formacie identyfikatora Uri.         | Adresy URL usługi Batch musi mieć postać podobną do https://mybatchaccount.eastus.batch.azure.com | Zobacz opis                                            |
| 2510         | Wystąpił błąd podczas wysyłania żądania.               | Adres URL usługi Batch jest nieprawidłowa                                         | Sprawdź adres URL usługi batch.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, Pig, przesyłanie strumieniowe usługi Hadoop)

| Kod błędu: | Komunikat o błędzie                                                | Opis                                                  | Poprawka możliwości / Zalecana akcja                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Przesyłanie zadań Hadoop nie powiodło się. Błąd: Nie można rozpoznać nazwy zdalnej. <br/><br/>Nie można odnaleźć klastra. | Podany identyfikator URI klastra jest nieprawidłowy                              | Upewnij się, że klaster nie został usunięty i podany identyfikator URI jest prawidłowa. Można otworzyć identyfikatora URI w dowolnej przeglądarce i powinna zostać wyświetlona interfejsu użytkownika Ambari. Jeśli klaster znajduje się w sieci wirtualnej, następnie identyfikator URI powinien być prywatnego identyfikatora URI, a następnie należy próbować otworzyć go z maszyny Wirtualnej, która jest częścią tej samej sieci wirtualnej. Więcej informacji o dla [sieci wirtualnej w HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Przesyłanie zadań Hadoop nie powiodło się. Zadanie:..., klaster:... /. Błąd: Zadanie zostało anulowane. | Przekroczono limit czasu przesyłania zadania.                         | Może to być problem z łącznością ogólne HDInsight lub problem z łącznością sieciową. Najpierw upewnij się, że interfejsu użytkownika Ambari HDInsight jest dostępna za pomocą dowolnej przeglądarki i poświadczenia są prawidłowe. Pamiętaj to zrobić z poziomu maszyny Wirtualnej/machine gdy własne środowisko IR jest zainstalowany, jeśli za pomocą Self-Hosted IR A następnie spróbuj ponownie przesłać zadanie usługi ADF. Jeśli nadal nie, skontaktuj się z usługi ADF zespołu pomocy technicznej. |
| 2300         | Brak autoryzacji:   Nazwa użytkownika Ambari lub hasło są niepoprawne  <br/><br/>Brak autoryzacji:   Administrator użytkownika jest zablokowane w Ambari   <br/><br/>403 — Dostęp zabroniony: Odmowa dostępu | Dostarczone poświadczenia dla HDInsight są nieprawidłowe lub wygasłe | Popraw je i ponownie wdrożyć połączoną usługę. Upewnij się, że poświadczenia pracować nad HDInsight najpierw, otwierając identyfikator URI klastra w dowolnej przeglądarce i próby logowania. Jeśli nie działają, można zresetować je z witryny Azure Portal. |
| 2300,   2310 | 502 — Serwer sieci web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy       <br/>Zła brama | Błąd pochodzi z HDInsight                               | Ten błąd jest pochodzące z klastrem HDInsight. Zapoznaj się [HDInsight narzędzia do rozwiązywania problemów](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) przy użyciu typowych błędów.    <br/>W przypadku klastrów Spark on może być także spowodowane ze względu na [to](https://hdinsight.github.io/spark/spark-thriftserver-errors.html). <br/><br/>[Dodatkowe łącze](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2300         | Przesyłanie zadań Hadoop nie powiodło się. Zadanie:..., klaster:... Błąd: {\"błąd\":\"nie można zrealizować żądania zadania przesyłania, ponieważ usługa templeton jest zajęty ze zbyt dużą liczbą żądań zadania przesyłania. Zaczekaj przez pewien czas przed ponowieniem próby wykonania operacji. Zapoznaj się templeton.parallellism.job.submit konfiguracji do konfigurowania jednoczesnych żądań. \  <br/><br/>Przesyłanie zadań Hadoop nie powiodło się. Zadanie: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, klaster: https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Nie można przesłać application_1561147195099_3730 do YARN: org.apache.hadoop.security.AccessControlException: Kolejka root.joblauncher już ma 500 aplikacji, nie może akceptować przesyłania aplikacji: application_1561147195099_3730\ | Zbyt wiele zadań są przesyłane do HDInsight w tym samym czasie | Należy rozważyć ograniczenie liczby równoczesnych zadań, które są przesyłane do usługi HDI. Zapoznaj się concurrency działania usługi ADF, jeśli są przesyłane przez tego samego działania. Zmień wyzwalacze, aby uruchomienia równoczesnych potoku są rozproszone wraz z upływem czasu. Także zapoznać się w witrynie docs HDInsight w celu dostosowanie "templeton.parallellism.job.submit", ponieważ sugeruje błąd. |
| 2303,   2347 | Zadanie Hadoop nie powiodło się z kodem zakończenia "5". Zobacz "wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr" Aby uzyskać więcej informacji.  <br/><br/>Wykonanie programu hive nie powiodło się z kodem błędu "UserErrorHiveOdbcCommandExecutionFailure".   Zobacz "wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out" Aby uzyskać więcej informacji | Zadanie zostało przesłane do HDInsight, a nie powiodła się HDInsight | Zadanie zostało pomyślnie przesłane do HDInsight. Nie powiodło się w klastrze. Można otworzyć zadania w interfejsie użytkownika Ambari HDInsight i otwórz dzienniki istnieje albo otwórz plik z magazynu jako punkty komunikat o błędzie się. Szczegóły błędu będą w tym pliku. |
| 2328         | Wystąpił błąd wewnętrzny serwera podczas przetwarzania żądania. Ponów próbę żądania lub skontaktuj się z działem pomocy technicznej | Odbywa się na HDInsight na żądanie.                              | Ten błąd pochodzi z usługi HDInsight podczas inicjowania obsługi HDInsight zakończy się niepowodzeniem. Skontaktuj się z zespołem HDInsight i podaj nazwę klastra na żądanie. |
| 2310         | java.lang.NullPointerException                               | Wystąpił błąd podczas przesyłania zadania do klastra Spark      | Ten wyjątek pochodzi z HDInsight i jest ukrywaniu rzeczywistego problemu.   Skontaktuj się z zespołem HDInsight, pomocy technicznej i zapewnić ich nazwy klastra i uruchomienie zakres czasu działania. |
|              | Inne błędy                                             |                                                              | Zapoznaj się [HDInsight narzędzia do rozwiązywania problemów](../hdinsight/hdinsight-troubleshoot-guide.md) i [HDInsight — często zadawane pytania](https://hdinsight.github.io/) |



## <a name="web-activity"></a>Działanie internetowe

| Kod błędu: | Komunikat o błędzie                                                | Opis                                                  | Poprawka możliwości / Zalecana akcja                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Nieprawidłowy HttpMethod: "..".                                    | Oznacza to, że metoda Http, określony w ładunku działania nie jest obsługiwana przez działanie w sieci Web. | Dostępne są następujące metody Http, które są obsługiwane: <br/>PUT, POST, POBIERANIE I USUWANIE |
| 2108         | Błąd nieprawidłowy serwer 500                                     | Błąd wewnętrzny w punkcie końcowym                               | Sprawdź funkcje dotyczące adresu URL (za pomocą programu Fiddler/narzędzia Postman): [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 401 dotyczącego braku autoryzacji                                             | Brak uwierzytelniania prawidłowe na żądanie                      | Zapewnia metodę uwierzytelniania prawidłowy (token mogły wygasnąć).   <br/><br/>Sprawdź funkcje dotyczące adresu URL (za pomocą programu Fiddler/narzędzia Postman): [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Forbidden 403                                                | Brak wymaganych uprawnień                                 | Sprawdź uprawnienia użytkownika na dostęp do zasobów.   <br/><br/>Sprawdź funkcje dotyczące adresu URL (za pomocą programu Fiddler/narzędzia Postman): [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Nieprawidłowe żądanie 400                                              | Nieprawidłowe żądanie Http                                         | Sprawdź adres URL, zlecenie i treści żądania.   <br/><br/>Użyj narzędzia Fiddler/narzędzia Postman w celu zweryfikowania żądania: [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Nie można odnaleźć 404                                                | Nie znaleziono zasobu                                       | Użyj narzędzia Fiddler/narzędzia Postman w celu zweryfikowania żądania: [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Usługa jest niedostępna                                          | Usługa jest niedostępna                                       | Użyj narzędzia Fiddler/narzędzia Postman w celu zweryfikowania żądania: [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Nieobsługiwany typ nośnika                                       | Niezgodny Content-Type z treścią działania w sieci Web           | Określ prawidłowy typ zawartości zgodnej format ładunku użycie narzędzia Fiddler/narzędzia Postman w celu zweryfikowania żądania: [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Zasób, którego szukasz dla został usunięty, ma zmienioną nazwę lub jest tymczasowo niedostępna. | Zasób jest niedostępny                                | Aby sprawdzić punkt końcowy, użyj narzędzia Fiddler/Postman: [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Nie można wyświetlić strony, której szukasz, ponieważ jest używana nieprawidłowa metoda (Zlecenie HTTP). | Nieprawidłowa metoda działanie sieci Web został określony w żądaniu   | Aby sprawdzić punkt końcowy, użyj narzędzia Fiddler/Postman: [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | Treści dla działania w sieci Web jest nieprawidłowy                       | Aby sprawdzić punkt końcowy, użyj narzędzia Fiddler/Postman: [Jak używać narzędzia Fiddler można utworzyć sesji HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>Jak używać narzędzia Fiddler do tworzenia aplikacji sieci web monitorowanych sesji HTTP

1. Pobierz i zainstaluj [programu Fiddler](https://www.telerik.com/download/fiddler)

2. Jeśli aplikacja sieci web używa protokołu HTTPS:

   1. Otwórz program Fiddler

   2. Przejdź do **Narzędzia > Opcje programu Fiddler** i wybierz pozycję, podobnie jak poniższy zrzut ekranu. 

      ![Opcje programu fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. Jeśli aplikacja korzysta z certyfikatów SSL, musisz dodać certyfikat narzędzia Fiddler do Twojego urządzenia.

4. Aby dodać certyfikat narzędzia Fiddler do Twojego urządzenia, przejdź do **narzędzia** > **opcje programu Fiddler** > **HTTPS**  >   **Akcje** > **wyeksportować certyfikat główny pulpit** w celu uzyskania certyfikatu programu Fiddler.

5. Wyłącz Przechwytywanie tak, aby rozpocząć od nowa śledzenia można wyczyścić pamięci podręcznej przeglądarki.

6. 1. Przejdź do **pliku** > **Przechwytywanie ruchu** lub naciśnij **F12**.
   2. Wyczyść pamięć podręczną przeglądarki, aby wszystkie elementy pamięci podręcznej są usuwane, a musi być pobrane ponownie.

7. Tworzenie żądania: 

8. 1. Kliknij kartę Composer (kompozytor)
   2. Ustaw metodę Http oraz adres URL
   3. Dodaj nagłówki i treść żądania, jeśli jest to wymagane
   4. Kliknij przycisk Wykonaj

9. Rozpocznij ponownie Przechwytywanie ruchu sieciowego i ukończyć problematyczne transakcji na stronie.

10. Po ukończeniu, przejdź do **pliku** > **Zapisz** > **wszystkie sesje**.

Więcej informacji na temat programu Fiddler [tutaj](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pomoc w znalezieniu rozwiązania problemu poniżej przedstawiono niektóre zasoby, które można wypróbować.

*  [Blogi](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji](https://feedback.azure.com/forums/270578-data-factory)
*  [Filmy wideo](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



