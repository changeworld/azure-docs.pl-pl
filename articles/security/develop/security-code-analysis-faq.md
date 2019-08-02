---
title: Microsoft Azure dokumentacja dotycząca analizy kodu zabezpieczeń — często zadawane pytania
description: Ten artykuł zawiera często zadawane pytania dotyczące rozszerzenia analizy kodu zabezpieczeń
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 1fc5e83c2c46a7da2a4b56879a2d596405fd8bee
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718316"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania
Masz pytania? Zapoznaj się z często zadawanymi pytaniami, aby uzyskać więcej informacji.

## <a name="general-faqs"></a>Ogólne często zadawane pytania

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>Czy mogę zainstalować rozszerzenie na serwerze TFS (nie na platformie Azure DevOps)? 

Nie, rozszerzenie nie jest dostępne do pobrania i zainstalowania dla programu TFS.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Czy muszę uruchomić analizę kodu zabezpieczeń firmy Microsoft z moją kompilacją? 

Tak i nie. W zależności od typu narzędzia analizy sam kod źródłowy może być jedynym wymaganym warunkiem lub może być wymagana wartość wyjściowa kompilacji. Na przykład, ponieważ skaner poświadczeń analizuje pliki w strukturze folderów repozytorium kodu, można uruchomić skaner poświadczeń i opublikować dzienniki analizy zabezpieczeń zadania kompilacji w autonomicznej kompilacji w celu pobrania wyników.
W przypadku innych narzędzi służących do analizowania artefaktów kompilacji, takich jak BinSkim, kompilacja będzie wymagana najpierw.

### <a name="can-i-break-my-build-when-results-are-found"></a>Czy mogę przerwać kompilację po znalezieniu wyników? 
Tak, możesz wprowadzić przerwanie kompilacji, gdy dowolne narzędzie zgłosi problem, wyszukiwanie w jego pliku dziennika. Wystarczy dodać zadanie kompilacji po analizie i zaznaczyć pole wyboru dla każdego narzędzia, dla którego chcesz przerwać kompilację. Możesz przerwać kompilację, gdy dowolne narzędzie zgłosi błędy lub ostrzeżenia i błędy w interfejsie użytkownika zadania po wykonaniu analizy.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>Czym różnią się argumenty wiersza polecenia w usłudze Azure DevOps niż w autonomicznych narzędziach pulpitu? 

W większości przypadków zadania kompilacji Azure DevOps są bezpośrednimi otokami wokół argumentów wiersza polecenia narzędzi zabezpieczeń. Wszystkie elementy, które zwykle przechodzą do narzędzia w wierszu polecenia z pulpitu, można przekazać do danych wejściowych argumentów zadania kompilacji.
Poniżej znajduje się lista zauważalnych różnic:
 - Narzędzie zostanie wykonane z folderu źródłowego agenta $ (Build. SourcesDirectory) lub% BUILD_SOURCESDIRECTORY%. Przykład: C:\agent\_work\1\s 
 - Ścieżki w argumentach mogą być względne względem katalogu głównego katalogu źródłowego wymienionego powyżej lub bezwzględnie przez uruchomienie agenta Premium ze znanymi lokalizacjami wdrożenia zasobów lokalnych lub użyciem zmiennych kompilacji DevOps platformy Azure
 - Narzędzia będą automatycznie podawać ścieżkę lub folder pliku wyjściowego, jeśli podano ścieżkę wyjściową, zostanie ona usunięta i zastąpiona ścieżką do naszej dobrze znanej lokalizacji dzienników w agencie kompilacji.
 - Niektóre dodatkowe parametry wiersza polecenia są oczyszczane i usuwane z niektórych narzędzi, takich jak dodawanie lub usuwanie opcji, aby upewnić się, że nie zostanie uruchomiony graficzny interfejs użytkownika.

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Czy można uruchomić zadanie kompilacji (na przykład skaner poświadczeń) dla wielu repozytoriów w kompilacji DevOps Azure? 

Nie, uruchomienie bezpiecznych narzędzi programistycznych dla wielu repozytoriów w pojedynczym potoku nie jest obecnie obsługiwane.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>Określony plik wyjściowy nie jest tworzony/nie można znaleźć określonego pliku wyjściowego 

Zadania kompilacji obecnie oczyszczają dane wejściowe użytkownika i aktualizują lokalizację wygenerowanego pliku wyjściowego do wspólnej lokalizacji w agencie kompilacji. Aby uzyskać więcej informacji na temat tej lokalizacji, zobacz następujące pytania.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Gdzie znajdują się pliki wyjściowe generowane przez narzędzia? 

Zadania kompilacji automatycznie dodają ścieżki wyjściowe do następującej dobrze znanej lokalizacji w agencie kompilacji $ (Agent. BuildDirectory)\_sdt\logs. Dzięki standaryzacji w tej lokalizacji możemy zagwarantować, że inne zespoły wytwarzające dzienniki analizy kodu lub zużywające je będą miały dostęp.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Czy można kolejkować kompilację, aby uruchamiać te zadania na hostowanym agencie kompilacji? 

Tak, wszystkie zadania i narzędzia w rozszerzeniu można wykonać na hostowanym agencie kompilacji.

>[!NOTE]
> Zadanie kompilacji chroniącej przed złośliwym oprogramowaniem wymaga agenta kompilacji z włączonym usługą Windows Defender, który ma wartość true w "hostowanej program VS2017" lub nowszych agentach kompilacji. (Nie zostanie uruchomiony na starszym agencie "Hosted"/"programu VS2015"). Nie można zaktualizować podpisów tych agentów, ale sygnatura powinna być zawsze stosunkowo aktualna, krótsza niż 3 godziny.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Czy można uruchamiać te zadania kompilacji w ramach potoku wydania (w przeciwieństwie do potoku kompilacji)? 
W większości przypadków tak. Jednak zadania, które publikują artefakty, nie są obsługiwane przez usługę Azure DevOps do uruchamiania z potoków wydań: "Jedyną kategorią zadań, które nie powinny być używane z wersją, są te, które publikują artefakty. Jest to spowodowane tym, że obecnie nie obsługujemy publikowania artefaktów w wersji ".
Zapobiega to pomyślnym uruchomieniu zadania "Opublikuj dzienniki analizy zabezpieczeń" z potoku wersji; zakończy się niepowodzeniem, a opisowy komunikat o błędzie.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Skąd zadania kompilacji pobierają narzędzia? 
Zadania kompilacji a) pobierają pakiety NuGet dla narzędzi z następującego [źródła danych usługi Azure DevOps zarządzanie pakietami](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) lub używają menedżera pakietów węzła, który musi być wstępnie zainstalowany na agencie kompilacji (przykład: "npm Install tslint").

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>Jakie znaczenie ma zainstalowanie rozszerzenia w mojej organizacji usługi Azure DevOps? 

Podczas instalowania programu zadania kompilacji zabezpieczeń udostępniane przez rozszerzenie staną się dostępne do użycia przez wszystkich użytkowników w organizacji. Podczas tworzenia lub edytowania potoku platformy Azure te zadania będą dostępne do dodania z listy kolekcja zadań kompilacji. W przeciwnym razie instalacja rozszerzenia w organizacji usługi Azure DevOps nie ma żadnego wpływu. Nie modyfikuje żadnych ustawień konta ani projektu ani potoków.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>Czy zostanie zainstalowane rozszerzenie Modyfikuj moje istniejące Azure Pipelines? 

Nie. Zainstalowanie rozszerzenia spowoduje, że będą dostępne zadania kompilacji zabezpieczeń do dodania do Azure Pipelines. Użytkownicy nadal muszą dodać lub zaktualizować definicje kompilacji, aby zintegrować narzędzia w procesie kompilacji.

## <a name="task-specific-faqs"></a>Często zadawane pytania dotyczące zadań

W tej sekcji są wyświetlane często zadawane pytania dotyczące zadań kompilacji.

### <a name="credential-scanner-faqs"></a>Często zadawane pytania dotyczące skanerów poświadczeń

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>Jakie są typowe scenariusze pomijania i przykłady? 
Poniżej przedstawiono dwa najczęstsze scenariusze pomijania:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Pomiń wszystkie wystąpienia danego wpisu tajnego w określonej ścieżce 
Klucz skrótu wpisu tajnego z pliku wyjściowego skanera poświadczeń jest wymagany, jak pokazano w poniższym przykładzie
   
        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> Klucz skrótu jest generowany przez część pasującej wartości lub zawartości pliku. Każda Poprawka kodu źródłowego może zmienić klucz skrótu i wyłączyć regułę pomijania. 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Aby pominąć wszystkie wpisy tajne w określonym pliku (lub aby pominąć sam plik tajny) 
Wyrażeniem pliku może być nazwa pliku lub każda przyrostkowa część pełnej ścieżki/nazwy pliku. Symbole wieloznaczne nie są obsługiwane. 

**Przykład** 

Plik, który ma zostać pominięty: [InputPath] \src\JS\lib\angular.js 

Prawidłowe reguły pomijania: 
- [InputPath] \src\JS\lib\angular.js--pomijanie pliku w określonej ścieżce
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- kątow. js--Pomijaj każdy plik o tej samej nazwie

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Wszystkie przyszłe wpisy tajne dodane do pliku będą również automatycznie pomijane. 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>Jakie są zalecane wskazówki dotyczące zarządzania kluczami tajnymi? 
Podczas wykrywania stałych zakodowanych wpisów tajnych w odpowiednim czasie i łagodzenia ryzyka jest to jeszcze lepsze, jeśli jedna z nich nie pozwala na całkowite sprawdzenie wpisów tajnych. W tym przypadku firma Microsoft udostępniła analizatora kodu CredScan jako część [rozszerzenia Microsoft DevLabs](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) dla programu Visual Studio. Na wczesnej wersji zapoznawczej oferuje deweloperom wbudowane środowisko do wykrywania potencjalnych wpisów tajnych w kodzie, dzięki czemu można rozwiązać te problemy w czasie rzeczywistym. Aby uzyskać więcej informacji, zapoznaj się [z blogiem](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) dotyczącym bezpiecznego zarządzania kluczami tajnymi w chmurze. Poniżej przedstawiono kilka dodatkowych zasobów, które ułatwiają zarządzanie wpisami tajnymi i dostęp do poufnych informacji z aplikacji w bezpieczny sposób: 
 - [Usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
 - [Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
 - [tożsamość usługi zarządzanej usługi Azure AD](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Tożsamość usługi zarządzanej (MSI) dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
 - [tożsamość usługi zarządzanej platformy Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
 - [Biblioteka AppAuthentication](https://docs.microsoft.com/azure/key-vault/service-to-service-authentication)

#### <a name="can-i-write-my-own-custom-searchers"></a>Czy mogę napisać własne niestandardowe wyszukiwania?

Skaner poświadczeń bazuje na zestawie wyszukiwania zawartości często zdefiniowanym w pliku **buildsearchers. XML** . Plik zawiera tablicę serializowanych obiektów XML, które reprezentują obiekt ContentSearcher. Program jest dystrybuowany z zestawem analityków, które zostały dobrze przetestowane, ale pozwala na implementację własnych niestandardowych wyszukiwań. 

Wyszukiwanie zawartości jest zdefiniowane w następujący sposób: 

- **Name** — nazwa opisowa wyszukiwania, który ma być używany w pliku wyjściowym skanera poświadczeń. Zaleca się użycie notacji CamelCase konwencji nazewnictwa przypadków dla nazw wyszukiwania. 
- **RuleID** — stabilny identyfikator nieprzezroczysty dla wyszukiwania. 
    - Domyślne wyszukiwania skanera poświadczeń są przypisywane za pomocą RuleIds, takich jak CSCAN0010, CSCAN0020, CSCAN0030 itp. Ostatnia cyfra jest zarezerwowana dla potencjalnej grupy wyrażeń regularnych wyszukiwania lub dzielenia.
    - RuleId dla dostosowanych wyszukiwań powinna mieć własną przestrzeń nazw w formacie: CSCAN-{Namespace} 0,010, CSCAN-{Namespace} 0020, CSCAN-{Namespace} 0030 itd.
    - W pełni kwalifikowana nazwa wyszukiwania jest kombinacją RuleId i nazwy elementu Searcher. Przykład CSCAN0010. KeyStoreFiles, CSCAN0020. Base64EncodedCertificate itp.
- **ResourceMatchPattern** — wyrażenie regularne rozszerzeń plików do sprawdzenia względem wyszukiwania
- **ContentSearchPatterns** — tablica ciągów zawierających instrukcje wyrażenia regularnego do dopasowania. Jeśli nie zdefiniowano żadnych wzorców wyszukiwania, wszystkie pliki zgodne ze wzorcem dopasowania zasobów zostaną zwrócone.
- **ContentSearchFilters** — tablica ciągów zawierających instrukcje wyrażenia regularnego do filtrowania konkretnych fałszywych wyników wyszukiwania.
- **Matchdetails** — opis komunikatu i/lub instrukcje ograniczenia, które mają zostać dodane dla każdego dopasowania do usługi wyszukiwania.
- **Zalecenie** — zapewnia zawartość pola sugestii dla dopasowania przy użyciu formatu szybkiego raportu.
- **Ważność** — liczba całkowita odzwierciedlająca ważność problemu (najwyższa = 1).
![Konfiguracja skanera poświadczeń](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Roslyn analizatory — często zadawane pytania

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Jakie są najczęstsze błędy podczas korzystania z zadania analizatorów Roslyn?

**Błąd: Projekt został przywrócony przy użyciu programu Microsoft. servicecore. App w wersji x. x. x, ale z bieżącymi ustawieniami, zamiast tego zostanie użyta wersja y. y. y. Aby rozwiązać ten problem, upewnij się, że te same ustawienia są używane do przywracania i dla kolejnych operacji, takich jak Kompilowanie lub publikowanie. Zazwyczaj ten problem może wystąpić, jeśli właściwość RuntimeIdentifier jest ustawiona podczas kompilowania lub publikowania, ale nie podczas przywracania:**

Analizatory Roslyn są uruchamiane jako część kompilacji, dlatego drzewo źródłowe na maszynie kompilacji musi być w stanie możliwego do skompilowania. Krok (prawdopodobnie "program dotnet. exe Publish") między główną kompilacją a analizatorami Roslyn może umieścić drzewo źródłowe w stanie niemożliwego do skompilowania. Może to być duplikowanie kroku, który wykonuje przywracanie NuGet, tuż przed krokiem analizatora Roslyn, umieści drzewo źródłowe w stanie możliwego do skompilowania.

**"CSC. exe" zakończył pracę z kodem błędu 1 — nie można utworzyć wystąpienia analizatora AAAA z C:\BBBB.dll: Nie można załadować pliku lub zestawu "Microsoft. CodeAnalysis, Version = X. X. X. X, Culture = neutral, PublicKeyToken = 31bf3856ad364e35" lub jednej z jego zależności. System nie może odnaleźć określonego pliku.**

Upewnij się, że kompilator obsługuje analizatory Roslyn. "CSC. exe/Version" powinien raportować co najmniej v 2.6. x. W niektórych przypadkach poszczególne pliki. csproj mogą zastąpić instalację programu Visual Studio na maszynie kompilacji, odwołując się do pakietu z Microsoft.Net. Kompilators. Jeśli użycie określonej wersji kompilatora nie zostało zamierzone, Usuń odwołania do Microsoft.Net. kompilatorów. W przeciwnym razie upewnij się, że przywoływany pakiet jest również co najmniej v 2.6. x. Spróbuj uzyskać dziennik błędów, który można znaleźć w parametrze/ErrorLog: w wierszu polecenia CSC. exe (znajdującym się w dzienniku zadania kompilacji Roslyn). Może wyglądać podobnie do tego:/ErrorLog: f:\ts-Services-123\_work\456\s\Some\Project\Code\Code.csproj.Sarif

**C# Kompilator nie jest wystarczająco ostatni (musi być > = 2,6)**

Najnowsze wersje C# kompilatora są opublikowane tutaj: https://www.nuget.org/packages/Microsoft.Net.Compilers. Aby uzyskać zainstalowaną wersję, użyj polecenia `C:\>csc.exe /version` Uruchom z wiersza poleceń. Upewnij się, że nie masz żadnych odwołań do pakietu NuGet Microsoft.Net. COMPILERS, który jest < v 2.6.

**Nie znaleziono dzienników programu MSBuild/VSBuild**

Ze względu na sposób działania zadania to zadanie wymaga wykonania zapytania dotyczącego usługi Azure DevOps dla dziennika MSBuild z zadania kompilacji MSBuild. Jeśli to zadanie zostanie uruchomione natychmiast po zadaniu kompilacji MSBuild, dziennik nie będzie jeszcze dostępny; Umieść inne zadania kompilacji, w tym zadania kompilacji SecDevTools, takie jak Binskim, skanowanie chroniące przed złośliwym kodem i inne), między zadaniem kompilacji MSBuild i zadaniem do tworzenia analizatorów Roslyn. 

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz dodatkowej pomocy, pomoc techniczna firmy Microsoft do analizy kodu zabezpieczeń jest dostępna od poniedziałku do piątku od 9:00 AM – 5:00 PM (pacyficzny czas standardowy)

  - Dołączanie — skontaktuj się z kierownikami ds. klientów, aby rozpocząć pracę. 
  >[!NOTE] 
  >Jeśli nie masz jeszcze płatnej pomocy technicznej z firmą Microsoft lub jeśli masz ofertę pomocy technicznej, która nie pozwala na zakup usług w katalogu Phoenix, odwiedź naszą [stronę główną usług pomocy technicznej](https://www.microsoft.com/enterprise/services/support) , aby uzyskać więcej informacji.

  - Pomoc techniczna — Wyślij wiadomość E-mail do naszego zespołu przy [pomocy technicznej analizy kodu zabezpieczeń firmy Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)