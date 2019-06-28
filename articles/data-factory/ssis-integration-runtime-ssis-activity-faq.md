---
title: Rozwiązywanie problemów z wykonanie pakietu w środowiska SSIS integration runtime | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów dla wykonanie pakietu SSIS w środowiska SSIS Integration Runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: a018a383de855a05b14aa6e1f1c465f8868f672d
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312163"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>Rozwiązywanie problemów z wykonanie pakietu w środowiska SSIS integration runtime

Ten artykuł zawiera najbardziej typowych błędów, które może napotkać podczas wykonywania SSIS pakietów w środowiska SSIS Integration Runtime, potencjalne przyczyny i akcje problemów.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>Gdzie mogę znaleźć dzienników dla rozwiązywania problemów

* Portal usługi ADF można sprawdzić wynik działania wykonanie pakietu SSIS, w tym wynik wykonywania, komunikaty o błędach i identyfikator operacji. Szczegółowe informacje można znaleźć w [monitorowanie potoku](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* Wykaz usług SSIS (SSISDB), można zapoznać się z dziennikami szczegółów uruchomienia. Szczegóły można znaleźć w [pakiety uruchamiania monitora i inne operacje](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>Typowe błędy, przyczyny i rozwiązania

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Komunikat o błędzie: `"Connection Timeout Expired."` lub `"The service has encountered an error processing your request. Please try again."`

* Potencjalną przyczyną & Zalecana akcja:
  * Lokalizacja danych źródłowa/docelowa jest przeciążony. Sprawdź obciążenia na swoje dane źródłowe/docelowe i zobacz, czy ma wystarczające moce przerobowe. Na przykład Azure SQL jest używany, zaleca należy wziąć pod uwagę skalowania się Jeśli baza danych jest prawdopodobnie przekroczenie limitu czasu.
  * Sieć między środowiska SSIS Integration Runtime i danych źródłowa/docelowa jest niestabilny, szczególnie w przypadku, gdy połączenie jest między regionami lub między w środowisku lokalnym i platformą azure. Zaleca się zastosowanie wzorca ponawiania pakietu SSIS przez następujące kroki:
    * Upewnij się, że pakiety usług SSIS, można uruchomić w przypadku niepowodzenia bez efekt uboczny (na przykład. utraty danych, dane dup...)
    * Konfigurowanie **ponów** i **interwał ponawiania** wykonania działania pakietów SSIS na karcie Ogólne ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Składnik programu ADO.NET i OLEDB źródłowa/docelowa można ustawić ConnectRetryCount i ConnectRetryInterval Menedżera połączeń pakietu SSIS lub działania SSIS

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Komunikat o błędzie: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* Potencjalną przyczyną & Zalecana akcja:
  * Ten problem zazwyczaj oznacza, że dane źródłowe/docelowe jest niedostępny z środowiska SSIS Integration Runtime, która może wynikać z różnych powodów:
    * Upewnij się, że poprawnie przechodząc danych źródłowa/docelowa nazwa/adres IP
    * Upewnij się, że Zapora jest skonfigurowana poprawnie
    * Upewnij się, że jeśli źródłowa/docelowa Twoje dane znajdują się w lokalnej sieci wirtualnej jest skonfigurowany prawidłowo.
      * Aby sprawdzić, czy problem jest z konfiguracji sieci wirtualnej przez udostępnienie Maszynie wirtualnej platformy Azure w tej samej sieci wirtualnej. A następnie sprawdź, czy dane źródłowa/docelowa jest dostępny z maszyny Wirtualnej platformy Azure
      * Można znaleźć więcej szczegółów dotyczących korzystania z sieci wirtualnej przy użyciu środowiska SSIS Integration Runtime w [dołączyć środowisko Azure-SSIS integration runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>Komunikat o błędzie: "`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* Potencjalną przyczyną & Zalecana akcja:
  * Dostawca ADO.NET, użyte w pakiecie nie jest zainstalowany w środowiska SSIS Integration Runtime. Dostawcę można zainstalować za pomocą ustawień niestandardowych. Więcej informacji na temat ustawień niestandardowych można znaleźć w [dostosować instalację środowiska Azure-SSIS integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Komunikat o błędzie: "`The connection '...' is not found`"

* Potencjalną przyczyną & Zalecana akcja:
  * Ten błąd może być spowodowane znanym problemem w starszej wersji programu SSMS. Jeśli pakiet zawiera składnik niestandardowej (na przykład pakiety usług SSIS Azure Feature Pack lub 3 składników innej firmy), który nie jest zainstalowany na komputerze, gdzie SSMS jest używany w celu wdrożenia, składnik zostanie usunięta z dniem SSMS i spowodować wystąpienie błędu. Uaktualnij [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) do najnowszej wersji, która ma problemu.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Komunikat o błędzie: "Nie ma wystarczającej ilości miejsca na dysku"

* Potencjalną przyczyną & Zalecana akcja:
  * Ten błąd oznacza, że dysk lokalny jest używany w w węźle środowiska SSIS Integration Runtime. Sprawdź, czy Twojego pakietu lub instalacja niestandardowa czy korzystać wielu spacje dysku.
    * Jeśli dysk jest używany przez pakiet, nastąpi zwolnienie się po zakończeniu wykonywania pakietu.
    * Jeśli dysk jest używany przez ustawienia niestandardowe, należy zatrzymać środowiska SSIS Integration Runtime, zmodyfikuj skrypt i uruchom ponownie środowiska SSIS Integration Runtime. Całego kontenera obiektów Blob platformy Azure, określony dla ustawień niestandardowych, spowoduje to skopiowanie do węzła SSIS IR, dlatego Sprawdź, czy jest niepotrzebne zawartości w ramach tego kontenera.

### <a name="error-message-cannot-open-file-"></a>Komunikat o błędzie: "Nie można otworzyć pliku"...""

* Potencjalną przyczyną & Zalecana akcja:
  * Ten błąd występuje, gdy wykonanie pakietu nie może odnaleźć pliku na dysku lokalnym w środowiska SSIS Integration Runtime.
    * Nie zaleca się w pakiecie wykonywanie środowiska SSIS Integration Runtime za pomocą ścieżki bezwzględnej. Użyj bieżącego katalogu roboczego wykonywania (.) lub folderu tymczasowego (% TEMP %) Zamiast tego.
    * Jeśli jest on wymagany do utrwalenia niektórych plików w węzłach środowiska SSIS Integration Runtime, zalecane jest przygotowanie plików za pomocą [dostosować ustawienia](how-to-configure-azure-ssis-ir-custom-setup.md). Wszystkie pliki w katalogu roboczym wykonywania zostaną wyczyszczone po zakończeniu wykonywania.
    * Innym rozwiązaniem jest używanie plików platformy Azure zamiast przechowywać plik w węźle środowiska SSIS Integration Runtime. Więcej szczegółów znajduje się w temacie [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Komunikat o błędzie: "Bazy danych"SSISDB"osiągnęła limit przydziału rozmiaru"

* Potencjalną przyczyną & Zalecana akcja:
  * Bazy danych SSISDB tworzone w usłudze Azure SQL lub wystąpienia zarządzanego, podczas tworzenia środowiska SSIS Integration Runtime osiągnęła limit przydziału.
    * Należy rozważyć zwiększenie liczby jednostek DTU bazy danych, aby rozwiązać ten problem. Szczegółowe informacje można znaleźć w [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * Sprawdź, czy pakiet wygeneruje wiele dzienników. Jeśli tak, aby wyczyścić te dzienniki można skonfigurować zadania elastycznego. Zapoznaj się [czyszczenie dzienników bazy danych SSISDB za pomocą zadania Elastic Database usługi Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) szczegóły.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Komunikat o błędzie: "Limit żądań dla bazy danych jest... i został osiągnięty."

* Potencjalną przyczyną & Zalecana akcja:
  * Jeśli wiele pakietów są wykonywane równolegle w środowiska SSIS Integration Runtime, ten błąd może wystąpić, ponieważ ograniczenie żądania bazy danych SSISDB tych limitów zostanie osiągnięty. Należy rozważyć zwiększenie DTC swojej bazy danych SSISDB, aby rozwiązać ten problem. Szczegółowe informacje można znaleźć w [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Komunikat o błędzie: "SSIS operacja nie powiodła się operacja nieoczekiwany stan:..."

* Potencjalną przyczyną & Zalecana akcja:
  * Błąd najczęściej jest spowodowany przez błąd przejściowy, więc spróbuj ponownie uruchomić wykonanie pakietu. Zaleca się zastosowanie wzorca ponawiania pakietu SSIS przez następujące kroki:
    * Upewnij się, że pakiety usług SSIS, można uruchomić w przypadku niepowodzenia bez efekt uboczny (na przykład utraty danych, dane dup...)
    * Konfigurowanie **ponów** i **interwał ponawiania** wykonania działania pakietów SSIS na karcie Ogólne ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Składnik programu ADO.NET i OLEDB źródłowa/docelowa można ustawić ConnectRetryCount i ConnectRetryInterval Menedżera połączeń pakietu SSIS lub działania SSIS

### <a name="error-message-there-is-no-active-worker"></a>Komunikat o błędzie: "Nie jest Brak aktywnych procesów roboczych".

* Potencjalną przyczyną & Zalecana akcja:
  * Ten błąd zazwyczaj oznacza, że środowiska SSIS Integration Runtime jest w złej kondycji. Sprawdź witryny Azure portal, stan i szczegółowe błędy: [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Komunikat o błędzie: "Środowiska integration runtime nie może zostać uaktualniony i ostatecznie przestanie działać, ponieważ firma Microsoft nie może uzyskać dostępu w kontenerze obiektów Blob platformy Azure parametru instalacji niestandardowej."

* Ten błąd występuje, gdy środowiska SSIS Integration Runtime nie może uzyskać dostępu skonfigurowanego dla ustawienia niestandardowego magazynu. Sprawdź, czy podany identyfikator Uri sygnatury dostępu Współdzielonego jest prawidłowa i nie wygasło.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Komunikat o błędzie: "Dostawca Microsoft OLE DB usług Analysis Services. "Hresult: 0x80004005 opis: " Błąd modelu COM: Błąd modelu COM: mscorlib; Obiekt docelowy wywołania zgłosił wyjątek"

* Potencjalną przyczyną & Zalecana akcja:
  * Jednego z potencjalną przyczyną tej nazwy użytkownika/hasła z usługą MFA, włączone jest skonfigurowany do uwierzytelniania usług Azure Analysis Services, który nie jest obsługiwany w środowiska SSIS integration runtime na jeszcze. Spróbuj użyć jednostki usługi do uwierzytelniania usługi Azure Analysis Service:
    1. Przygotowanie nazwy głównej usługi dla AAS [https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
    2. W Menedżerze połączeń, skonfiguruj "Przy użyciu określonej nazwy użytkownika i hasło": Ustaw "AppID" jako nazwy użytkownika i "clientSecret" jako hasło

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-managed-identity"></a>Komunikat o błędzie: "ADONET źródła nie powiodło się, można uzyskać połączenia {GUID} z następujący komunikat o błędzie: Logowanie użytkownika "NT\LOGOWANIE" powiodło się "podczas korzystania z tożsamości zarządzanej

* Potencjalną przyczyną & Zalecana akcja:
  * Upewnij się, że gdy parametr "ConnectUsingManagedIdentity" ma wartość PRAWDA, nie Konfiguruj metodę uwierzytelniania Menedżera połączeń jako "Hasło uwierzytelnianie usługi Active Directory". Możesz skonfigurować go jako "Uwierzytelnianie SQL" zamiast której będzie ignorowany, jeśli ma wartość "ConnectUsingManagedIdentity"

### <a name="package-takes-unexpected-long-time-to-execute"></a>Pakiet ma nieoczekiwany długi czas wykonywania

* Potencjalną przyczyną & Zalecana akcja:
  * Zbyt wiele wykonań pakietu zostało zaplanowane na środowiska SSIS Integration Runtime. W tym przypadku wszystkich tych wykonań będzie czekał w kolejce dla ich Włącz do wykonania.
    * Liczba wykonań maksymalna liczba równoległych na środowisko IR = liczba węzłów * maksymalna liczba równoległych wykonywania na węzeł
    * Zapoznaj się [tworzenie środowiska Azure-SSIS Integration Runtime w usłudze Azure Data Factory](create-azure-ssis-integration-runtime.md) ustawiania liczby węzłów i maksymalna liczba wykonywanie równoległe w każdym węźle.
  * Środowiska SSIS Integration Runtime jest zatrzymana lub w złej kondycji. Sprawdź [środowiska Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime) sprawdzania stanu środowiska SSIS Integration Runtime i błędów.
  * Zalecane jest, aby ustawić limit czasu, jeśli wiesz, że wykonanie pakietu powinien zostało zakończone, w określonym czasie: ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Niska wydajność podczas wykonywania pakietu

* Potencjalną przyczyną & Zalecana akcja:

  * Sprawdź, czy środowiska SSIS Integration Runtime w tym samym regionie jako źródła danych i miejsca docelowego.

  * Włącz poziomu rejestrowania "Performance"

      Można ustawić poziom rejestrowania wykonanie pakietu na "Performance", aby zebrać więcej szczegółowych informacji czas trwania dla każdego składnika podczas wykonywania. Szczegółowe informacje można znaleźć w folderze: [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Sprawdź wydajność węzła środowiska IR na stronie monitor IR w witrynie Azure portal.
    * Jak monitorować środowiska SSIS Integration Runtime: [Środowisko Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * Historia użycia Procesora/pamięci środowiska SSIS Integration Runtime znajduje się w temacie metryki usługi Data Factory w witrynie Azure portal ![monitorować metryki środowiska SSIS Integration Runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
