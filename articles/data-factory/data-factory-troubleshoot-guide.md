---
title: Rozwiązywanie problemów Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy związane z zewnętrznymi kontrolkami w Azure Data Factory.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c76242c176ba4f4c9ffc0d6934f6b645743d77f4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234580"
---
# <a name="troubleshoot-azure-data-factory"></a>Rozwiązywanie problemów Azure Data Factory

W tym artykule przedstawiono typowe metody rozwiązywania problemów z zewnętrznymi działaniami związanymi z kontrolą w Azure Data Factory.

## <a name="azure-databricks"></a>Azure Databricks

| Kod błędu | Komunikat o błędzie                                          | Opis                             | Zalecenie                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Błąd 403.                                                    | Token dostępu do datakostki wygasł.                         | Domyślnie token dostępu do datakostki jest ważny przez 90 dni.  Utwórz nowy token i zaktualizuj połączoną usługę. |
| 3201           | Brak wymaganego pola: Settings. Task. notebook_task. notebook_path | Nieprawidłowe tworzenie: Ścieżka notesu nie została określona poprawnie. | Określ ścieżkę notesu w działaniu datakosteks. |
| 3201           | Klaster... nie istnieje.                                 | Błąd tworzenia: Klaster datakostki nie istnieje lub został usunięty. | Sprawdź, czy klaster datakostki istnieje. |
| 3201           | Nieprawidłowy identyfikator URI pliku języka Python... Zapoznaj się z podręcznikiem użytkownika dla obsługiwanych schematów URI. | Nieprawidłowe tworzenie.                                                | Określ ścieżki bezwzględne dla schematów adresów obszaru roboczego lub `dbfs:/folder/subfolder/foo.py` dla plików przechowywanych w systemie plików datakostki. |
| 3201           | {0}LinkedService powinny mieć domenę i accessToken jako wymagane właściwości. | Nieprawidłowe tworzenie.                                                | Sprawdź [definicję połączonej usługi](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}LinkedService powinien określać istniejący identyfikator klastra lub nowe informacje o klastrze do utworzenia. | Nieprawidłowe tworzenie.                                                | Sprawdź [definicję połączonej usługi](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Typ węzła Standard_D16S_v3 nie jest obsługiwany. Obsługiwane typy węzłów:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | Nieprawidłowe tworzenie.                                                | Zapoznaj się z komunikatem o błędzie.                                          |
| 3201           | Nieprawidłowy notebook_path:... Obecnie obsługiwane są tylko ścieżki bezwzględne. Ścieżki muszą zaczynać się od znaku "/". | Nieprawidłowe tworzenie.                                                | Zapoznaj się z komunikatem o błędzie.                                          |
| 3202           | W ciągu ostatnich 3600 sekund utworzono już 1000 zadań, które przekraczają limit szybkości:   1000 tworzenia zadań na 3600 sekund. | Zbyt wiele kostek datakostks jest wykonywanych w ciągu godziny.                         | Sprawdź wszystkie potoki korzystające z tego obszaru roboczego datakosteks w celu ich tempa tworzenia zadań.  Jeśli potoki uruchomiły zbyt wiele kostek datakostków, Migruj niektóre potoki do nowego obszaru roboczego. |
| 3202           | Nie można przeanalizować obiektu żądania: Oczekiwano wartości "Key" i "value" dla pola mapy JSON base_parameters, otrzymano "Key:"... "" INSTEAD. | Błąd tworzenia: Nie podano wartości dla parametru.         | Sprawdź kod JSON potoku i upewnij się, że wszystkie parametry w notesie baseParameters określają niepustą wartość. |
| 3202           | Użytkownik: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` nie ma uprawnień dostępu do klastra. | Użytkownik, który wygenerował token dostępu, nie może uzyskać dostępu do klastra datakostks określonego w połączonej usłudze. | Upewnij się, że użytkownik ma wymagane uprawnienia w obszarze roboczym.   |
| 3203           | Klaster jest w stanie przerwania, nie jest dostępny do odbierania zadań. Popraw klaster lub spróbuj ponownie później. | Klaster został przerwany.    W przypadku klastrów interaktywnych może to być sytuacja wyścigu. | Najlepszym sposobem uniknięcia tego błędu jest użycie klastrów zadań.             |
| 3204           | Wykonanie zadania nie powiodło się.  | Komunikaty o błędach wskazują różne problemy, takie jak nieoczekiwany stan klastra lub określone działanie. W ogóle nie jest wyświetlany żaden komunikat o błędzie.                                                          | ND                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Poniższa tabela dotyczy języka U-SQL.

| Kod błędu         | Komunikat o błędzie                                                | Opis                                          | Zalecenie                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Token dostępu pochodzi od niewłaściwej dzierżawy.                    | Nieprawidłowa dzierżawa usługi Azure Active Directory (Azure AD).                                         | Nazwa główna usługi służąca do uzyskiwania dostępu do Azure Data Lake Analytics należy do innej dzierżawy usługi Azure AD. Utwórz nową nazwę główną usługi w tej samej dzierżawie co konto Data Lake Analytics. |
| 2711,   2705,   2704 | Zabrania. Weryfikacja listy ACL nie powiodła się. Zasób nie istnieje lub użytkownik nie ma uprawnień do wykonywania żądanych operacji.<br/><br/>Użytkownik nie może uzyskać dostępu do Data Lake Store.  <br/><br/>Użytkownik nie ma uprawnień do używania Data Lake Analytics. | Nazwa główna usługi lub certyfikat nie ma dostępu do pliku w magazynie. | Upewnij się, że jednostka usługi lub certyfikat udostępniany przez użytkownika dla zadań Data Lake Analytics ma dostęp do konta Data Lake Analytics i domyślnego wystąpienia Data Lake Storage z folderu głównego. |
| 2711                 | Nie można znaleźć pliku lub folderu "Azure Data Lake Store".       | Ścieżka do pliku U-SQL jest niepoprawna lub poświadczenia połączonej usługi nie mają dostępu. | Sprawdź ścieżkę i poświadczenia podane w połączonej usłudze. |
| 2707                 | Nie można rozpoznać konta AzureDataLakeAnalytics. Sprawdź "AccountName" i "DataLakeAnalyticsUri". | Konto Data Lake Analytics w połączonej usłudze jest nieprawidłowe.                  | Sprawdź, czy odpowiednie konto zostało podane.             |
| 2703                 | Identyfikator błędu: E_CQO_SYSTEM_INTERNAL_ERROR (lub dowolny błąd zaczynający się od "Identyfikator błędu:"). | Błąd pochodzi z Data Lake Analytics.                                    | Błąd podobny do przykładu oznacza, że zadanie zostało przesłane do Data Lake Analytics, a skrypt zakończył się niepowodzeniem. Zbadaj w Data Lake Analytics. W portalu przejdź do konta Data Lake Analytics i Wyszukaj zadanie przy użyciu identyfikatora uruchomienia działania Data Factory (nie identyfikatora uruchomienia potoku). Zadanie zawiera więcej informacji o błędzie i pomaga w rozwiązywaniu problemów. Jeśli rozwiązanie nie jest wyczyszczone, skontaktuj się z zespołem pomocy technicznej Data Lake Analytics i podaj adres URL zadania, który zawiera nazwę konta i identyfikator zadania. |
| 2709                 | W tej chwili nie możemy zaakceptować Twojego zadania. Maksymalna liczba zadań w kolejce dla konta to 200. | Ten błąd jest spowodowany przez ograniczenie wydajności Data Lake Analytics.                                           | Zmniejsz liczbę przesłanych zadań do Data Lake Analytics, zmieniając wyzwalacze Data Factory i ustawienia współbieżności w działaniach. Lub Zwiększ limity Data Lake Analytics. |
| 2709                 | To zadanie zostało odrzucone, ponieważ wymaga 24 jednostek. Zasady zdefiniowane przez administratora konta uniemożliwiają użycie więcej niż 5 jednostek. | Ten błąd jest spowodowany przez ograniczenie wydajności Data Lake Analytics.                                           | Zmniejsz liczbę przesłanych zadań do Data Lake Analytics, zmieniając wyzwalacze Data Factory i ustawienia współbieżności w działaniach. Lub Zwiększ limity Data Lake Analytics. |



## <a name="azure-functions"></a>Azure Functions

| Kod błędu | Komunikat o błędzie                           | Opis                                                  | Zalecenie                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Zawartość odpowiedzi nie jest prawidłowym JObject. | Wywołana funkcja platformy Azure nie zwróciła ładunku JSON w odpowiedzi. Działanie funkcji platformy Azure w Data Factory obsługuje tylko zawartość odpowiedzi JSON. | Zaktualizuj funkcję platformy Azure, aby zwracała prawidłowy ładunek JSON. Na przykład C# funkcja może zwrócić `(ActionResult)new<OkObjectResult("{` \"identyfikator\":\"123.\"`}");` |
| 3600         | Nieprawidłowy HttpMethod: "...".               | Metoda HTTP określona w ładunku działania nie jest obsługiwana przez działanie funkcji platformy Azure. | Użyj obsługiwanej metody HTTP, takiej jak PUT, POST, GET, DELETE, OPTIONS, szef lub TRACE. |



## <a name="custom"></a>Niestandardowy

Poniższa tabela ma zastosowanie do Azure Batch.

| Kod błędu | Komunikat o błędzie                                                | Opis                                                  | Zalecenie                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Wystąpił nieoczekiwany wyjątek i wykonywanie nie powiodło się.             | Nie można uruchomić polecenia lub program zwrócił kod błędu. | Upewnij się, że plik wykonywalny istnieje. Jeśli program został uruchomiony, upewnij się, że *stdout. txt* i *stderr. txt* zostały przekazane do konta magazynu. Dobrym sposobem jest wyemitujenie dzienników copious w kodzie w celu debugowania. |
| 2501         | Nie można uzyskać dostępu do konta partii użytkownika; Sprawdź ustawienia konta w usłudze Batch. | Nieprawidłowy klucz dostępu do partii lub Nazwa puli.            | Sprawdź nazwę puli i klucz dostępu do usługi Batch w połączonej usłudze. |
| 2502         | Nie można uzyskać dostępu do konta magazynu użytkownika; Sprawdź ustawienia konta magazynu. | Niepoprawna nazwa konta magazynu lub klucz dostępu.       | Sprawdź nazwę konta magazynu i klucz dostępu w połączonej usłudze. |
| 2504         | Operacja zwróciła nieprawidłowy kod stanu "nieprawidłowego żądania".     | Zbyt wiele plików w folderPath aktywności niestandardowej. Łączny rozmiar elementu resourceFiles nie może być większy niż 32 768 znaków. | Usuń niepotrzebne pliki. Lub Dodaj do nich polecenie rozpakować, aby je wyodrębnić. Na przykład użyj`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | Nie można utworzyć sygnatury dostępu współdzielonego, jeśli nie są używane poświadczenia klucza konta. | Działania niestandardowe obsługują tylko konta magazynu używające klucza dostępu. | Zapoznaj się z opisem błędu.                                            |
| 2507         | Ścieżka folderu nie istnieje lub jest pusta:....            | Żadne pliki nie znajdują się na koncie magazynu w określonej ścieżce.       | Ścieżka folderu musi zawierać pliki wykonywalne, które chcesz uruchomić. |
| 2508         | Folder zasobów zawiera zduplikowane pliki.               | Wiele plików o tej samej nazwie znajduje się w różnych podfolderach folderPath. | Działania niestandardowe Spłaszcz strukturę folderów w obszarze folderPath.  Jeśli potrzebujesz zachować strukturę folderów, pliki zip i Wyodrębnij je w Azure Batch przy użyciu polecenia Rozpakuj. Na przykład użyj`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | Adres URL partii... jest nieprawidłowy; musi być w formacie identyfikatora URI.         | Adresy URL partii muszą być podobne do`https://mybatchaccount.eastus.batch.azure.com` | Zapoznaj się z opisem błędu.                                            |
| 2510         | Wystąpił błąd podczas wysyłania żądania.               | Adres URL partii jest nieprawidłowy.                                         | Sprawdź adres URL partii.                                            |

## <a name="hdinsight"></a>HDInsight

Poniższa tabela ma zastosowanie do przesyłania strumieniowego platformy Spark, Hive, MapReduce, trzody chlewnej i usługi Hadoop.

| Kod błędu | Komunikat o błędzie                                                | Opis                                                  | Zalecenie                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Nie można przesłać zadania usługi Hadoop. Błąd: Nie można rozpoznać nazwy zdalnej. <br/><br/>Nie można odnaleźć klastra. | Podany identyfikator URI klastra jest nieprawidłowy.                              | Upewnij się, że klaster nie został usunięty i że podany identyfikator URI jest prawidłowy. Po otwarciu identyfikatora URI w przeglądarce powinien zostać wyświetlony interfejs użytkownika Ambari. Jeśli klaster znajduje się w sieci wirtualnej, identyfikator URI powinien być prywatnym identyfikatorem URI. Aby go otworzyć, użyj maszyny wirtualnej, która jest częścią tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [bezpośrednie łączenie się z usługami Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Nie można przesłać zadania usługi Hadoop. Zadanie:..., klaster:.../. Błąd: Zadanie zostało anulowane. | Przekroczono limit czasu podczas przekazywania zadania.                         | Przyczyną problemu może być ogólna łączność z usługą HDInsight lub łączność sieciowa. Najpierw upewnij się, że interfejs użytkownika usługi HDInsight Ambari jest dostępny z dowolnej przeglądarki. Upewnij się, że poświadczenia są nadal ważne. Jeśli używasz własnego środowiska uruchomieniowego (IR), upewnij się, że jest to możliwe z maszyny wirtualnej lub maszyny, na której zainstalowano samoobsługowe środowisko IR. Następnie spróbuj ponownie przesłać zadanie z Data Factory. Jeśli nadal się nie powiedzie, skontaktuj się z zespołem Data Factory, aby uzyskać pomoc techniczną. |
| 2300         | Próby   Nazwa użytkownika Ambari lub hasło są niepoprawne  <br/><br/>Próby   Administrator użytkownika jest zablokowany w Ambari.   <br/><br/>403 — Dostęp zabroniony: Odmowa dostępu. | Poświadczenia usługi HDInsight są nieprawidłowe lub wygasły. | Popraw poświadczenia i ponownie Wdróż połączoną usługę. Najpierw upewnij się, że poświadczenia działają w usłudze HDInsight, otwierając identyfikator URI klastra w dowolnej przeglądarce i próbując się zalogować. Jeśli poświadczenia nie działają, możesz je zresetować z poziomu Azure Portal. |
| 2300,   2310 | 502 — Serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy.       <br/>Zła brama. | Ten błąd pochodzi z usługi HDInsight.                               | Ten błąd pochodzi z klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [AMBARI UI 502 Error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 błędy połączenia z serwerem spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 błędy połączenia z serwerem Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)i [Rozwiązywanie problemów z błędami bramy w Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502). |
| 2300         | Nie można przesłać zadania usługi Hadoop. Zadanie:..., klaster:... Błąd: {\"error\":\"nie można obsłużyć żądania przesłania zadania, ponieważ usługa Templeton jest zajęta zbyt wiele żądań przesłania zadania. Poczekaj przez jakiś czas przed ponowieniem próby wykonania operacji. Zapoznaj się z konfiguracją Templeton. parallellism. job. Submit, aby skonfigurować współbieżne żądania.  <br/><br/>Nie można przesłać zadania usługi Hadoop. Zleceń 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, klaster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Błąd: {\"error\":\"Java. IO. IOException: org. Apache. Hadoop. przędzy. Exceptions. przędzexception: Nie można przesłać application_1561147195099_3730 do PRZĘDZy: org. Apache. Hadoop. Security. AccessControlException: Element główny kolejki. joblauncher ma już 500 aplikacji, nie można zaakceptować przesłania aplikacji: application_1561147195099_3730 \ | Zbyt wiele zadań jest przesyłanych do usługi HDInsight w tym samym czasie. | Rozważ ograniczenie liczby współbieżnych zadań przesyłanych do usługi HDInsight. Zapoznaj się z Data Factory współbieżności działań, jeśli zadania są przesyłane przez to samo działanie. Zmień Wyzwalacze w celu rozłożenia współbieżnych uruchomień potoków w czasie. Zapoznaj się z dokumentacją usługi `templeton.parallellism.job.submit` HDInsight, aby dostosować ją w miarę sugerowania błędu. |
| 2303, 2347 | Zadanie usługi Hadoop nie powiodło się z kodem zakończenia "5". Aby uzyskaćwasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderrwięcej informacji, zobacz "".  <br/><br/>Wykonanie programu Hive nie powiodło się z kodem błędu "UserErrorHiveOdbcCommandExecutionFailure".   Aby uzyskaćwasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.outwięcej informacji, zobacz "". | Zadanie zostało przesłane do usługi HDInsight i nie powiodło się w usłudze HDInsight. | Zadanie zostało pomyślnie przesłane do usługi HDInsight. W klastrze nie powiodło się. Otwórz zadanie i dzienniki w interfejsie użytkownika usługi HDInsight Ambari lub Otwórz plik z magazynu, który sugeruje komunikat o błędzie. Plik zawiera szczegóły błędu. |
| 2328         | Wystąpił wewnętrzny błąd serwera podczas przetwarzania żądania. Spróbuj ponownie wykonać żądanie lub skontaktuj się z pomocą techniczną. | Ten błąd występuje na żądanie w usłudze HDInsight.                              | Ten błąd pochodzi z usługi HDInsight, gdy inicjowanie obsługi usługi HDInsight kończy się niepowodzeniem. Skontaktuj się z zespołem usługi HDInsight i podaj nazwę klastra na żądanie. |
| 2310         | java.lang.NullPointerException                               | Ten błąd występuje, gdy zadanie zostanie przesłane do klastra Spark.      | Ten wyjątek pochodzi z usługi HDInsight. Powoduje ukrycie rzeczywistego problemu. Skontaktuj się z zespołem usługi HDInsight, aby uzyskać pomoc techniczną. Podaj nazwę klastra i zakres czasu wykonywania działania. |
|              | Wszystkie inne błędy                                             |                                                              | Rozwiązywanie [problemów przy użyciu usługi HDInsight](../hdinsight/hdinsight-troubleshoot-guide.md) i usługi [HDInsight — często zadawane pytania](https://hdinsight.github.io/). |



## <a name="web-activity"></a>Działanie internetowe

| Kod błędu | Komunikat o błędzie                                                | Opis                                                  | Zalecenie                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Nieprawidłowy HttpMethod: "...".                                    | Działanie sieci Web nie obsługuje metody HTTP określonej w ładunku działania. | Obsługiwane metody HTTP to PUT, POST, GET i DELETE. |
| 2108         | Nieprawidłowy błąd serwera 500.                                     | Błąd wewnętrzny w punkcie końcowym.                               | Użyj programu Fiddler lub Ogłoś, aby sprawdzić funkcjonalność adresu URL. |
| 2108         | Nieautoryzowane 401.                                             | Brak prawidłowego uwierzytelniania w żądaniu.                      | Token mógł wygasnąć. Podaj prawidłową metodę uwierzytelniania. Użyj programu Fiddler lub Ogłoś, aby sprawdzić funkcjonalność adresu URL. |
| 2108         | Zabronione 403.                                                | Brak wymaganych uprawnień.                                 | Sprawdź uprawnienia użytkownika do dostępnego zasobu. Użyj programu Fiddler lub Ogłoś, aby sprawdzić funkcjonalność adresu URL.  |
| 2108         | Złe żądanie 400.                                              | Nieprawidłowe żądanie HTTP.                                         | Sprawdź adres URL, czasownik i treść żądania. Aby zweryfikować żądanie, użyj programu Fiddler lub programu Poster.  |
| 2108         | Nie znaleziono 404.                                                | Nie znaleziono zasobu.                                       | Aby zweryfikować żądanie, użyj programu Fiddler lub programu Poster.  |
| 2108         | Usługa jest niedostępna.                                          | Usługa jest niedostępna.                                       | Aby zweryfikować żądanie, użyj programu Fiddler lub programu Poster.  |
| 2108         | Nieobsługiwany typ nośnika.                                       | Typ zawartości jest niezgodny z treścią działania sieci Web.           | Określ typ zawartości zgodny z formatem ładunku. Aby zweryfikować żądanie, użyj programu Fiddler lub programu Poster. |
| 2108         | Zasób, którego szukasz, został usunięty, ma zmienioną nazwę lub jest tymczasowo niedostępna. | Zasób nie jest dostępny.                                | Aby sprawdzić punkt końcowy, użyj programu Fiddler lub programu Poster. |
| 2108         | Szukana Strona nie może zostać wyświetlona, ponieważ jest używana nieprawidłowa Metoda (czasownik HTTP). | W żądaniu określono niepoprawną metodę działania sieci Web.   | Aby sprawdzić punkt końcowy, użyj programu Fiddler lub programu Poster. |
| 2108         | invalid_payload                                              | Treść działania sieci Web jest niepoprawna.                       | Aby sprawdzić punkt końcowy, użyj programu Fiddler lub programu Poster. |

Aby użyć programu Fiddler do utworzenia sesji HTTP monitorowanej aplikacji sieci Web:

1. Pobierz, zainstaluj i Otwórz [programu Fiddler](https://www.telerik.com/download/fiddler).

1. Jeśli aplikacja sieci Web używa protokołu HTTPS, przejdź do pozycji **Narzędzia** > **programu Fiddler opcje** > **https**. Wybierz pozycję **Przechwyć połączenie HTTPS** i odszyfruj **ruch https**. 
   
   ![Opcje programu Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Jeśli aplikacja używa certyfikatów SSL, Dodaj certyfikat programu Fiddler do urządzenia. Przejdź do **narzędzi Narzędzia** > **programu Fiddler opcje** > **https** > Eksportujcertyfikat**główny do pulpitu**. > 

1. Wyłącz przechwytywanie, przechodząc do**ruchu przechwytywania** **plików** > . Lub naciśnij klawisz **F12**.

1. Wyczyść pamięć podręczną przeglądarki, aby wszystkie elementy w pamięci podręcznej zostały usunięte i muszą zostać pobrane ponownie.

1. Utwórz żądanie: 

   a. Wybierz kartę **układacz** .

   b. Ustaw metodę HTTP i adres URL.

   c. Dodaj nagłówki i treść żądania, jeśli zachodzi taka potrzeba.

   d. Wybierz pozycję **Wykonaj**.

9. Włącz ponownie przechwytywanie ruchu i Ukończ problematyczną transakcję na stronie.

10. Przejdź do **pliku** > **Zapisz** > **wszystkie sesje**.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą programu Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)



