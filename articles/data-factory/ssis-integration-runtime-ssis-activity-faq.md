---
title: Rozwiązywanie problemów z wykonanie pakietu w środowiska SSIS integration runtime | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów dla wykonanie pakietu SSIS w środowiska SSIS integration runtime
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
ms.openlocfilehash: 68a5d5278e1181695695647cff187d4b95624b40
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537648"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Rozwiązywanie problemów z wykonanie pakietu w środowiska SSIS integration runtime

Ten artykuł zawiera najbardziej typowych błędów, które może się okazać, gdy wykonujesz pakiety usług SQL Server Integration Services (SSIS) w środowiska SSIS integration runtime. Opisano w nim potencjalne przyczyny i akcje problemów.

## <a name="where-to-find-logs-for-troubleshooting"></a>Gdzie można znaleźć dzienniki do rozwiązywania problemów

Aby sprawdzić wynik działania wykonywania pakietów SSIS za pomocą portalu usługi Azure Data Factory. Dane wyjściowe obejmują wynik wykonywania, komunikaty o błędach i identyfikator operacji. Aby uzyskać więcej informacji, zobacz [monitorowanie potoku](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Użyj katalogu usług SSIS (SSISDB), aby sprawdzić dzienniki szczegółów uruchomienia. Aby uzyskać więcej informacji, zobacz [pakiety uruchamiania monitora i inne operacje](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Typowe błędy, przyczyny i rozwiązania

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Komunikat o błędzie: "Upłynął limit czasu połączenia" lub "usługi napotkał błąd podczas przetwarzania Twojego żądania. Spróbuj ponownie."

Poniżej przedstawiono potencjalne przyczyny i zalecane akcje:
* Źródło danych lub miejsce docelowe jest przeciążony. Sprawdź obciążenie źródła danych lub docelowym i czy ma wystarczające moce przerobowe. Na przykład jeśli używasz usługi Azure SQL Database, należy rozważyć skalowanie w górę, jeśli baza danych jest prawdopodobnie przekroczenie limitu czasu.
* Sieć między środowiska SSIS integration runtime i źródła danych lub miejsce docelowe jest niestabilny, szczególnie w przypadku, gdy połączenie jest między regionami lub między lokalną i platformą Azure. Zastosuj wzorzec ponawiania pakietu SSIS, wykonaj następujące czynności:
  * Upewnij się, że pakiety usług SSIS, można uruchomić w przypadku niepowodzenia bez efektów ubocznych (na przykład utraty danych lub duplikacji danych).
  * Konfigurowanie **ponów** i **interwał ponawiania** z **wykonywanie pakietu SSIS** działania na **ogólne** kartę. ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * W przypadku ADO.NET i OLE DB źródłowy lub docelowy składnika, ustawić **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w pakietu SSIS lub działania SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Komunikat o błędzie: "ADO NET źródłowy ma nie można uzyskać połączenia"..."" za pomocą "Wystąpił błąd związany z siecią lub wystąpieniem podczas nawiązywania połączenia z programem SQL Server. Serwer nie został znaleziony lub jest on niedostępny. "

Ten problem zazwyczaj oznacza to źródło danych lub docelowy jest niedostępny z środowiska SSIS integration runtime. Przyczyny mogą się różnić. Spróbuj wykonać następujące akcje:
* Upewnij się, przechodząc do źródła danych lub przeznaczenia nazwa/adres IP poprawnie.
* Upewnij się, że Zapora jest skonfigurowana poprawnie.
* Upewnij się, że Twoja sieć wirtualna jest skonfigurowany prawidłowo, jeśli źródło danych lub miejsce docelowe jest w środowisku lokalnym:
  * Aby sprawdzić, czy problem jest z konfiguracji sieci wirtualnej przez udostępnienie Maszynie wirtualnej platformy Azure w tej samej sieci wirtualnej. Sprawdź, czy źródło danych lub docelowy jest dostępny z maszyny Wirtualnej platformy Azure.
  * Można znaleźć szczegółowe informacje o użyciu sieci wirtualnej przy użyciu środowiska SSIS integration runtime w [dołączyć środowisko Azure-SSIS integration runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Komunikat o błędzie: "ADO NET źródłowy ma nie można uzyskać połączenia"..."" za pomocą "Nie można utworzyć połączenia zarządzanego menedżera."

Potencjalną przyczyną jest to, że dostawca ADO.NET, użyte w pakiecie nie jest zainstalowany w środowiska SSIS integration runtime. Za pomocą niestandardowego Instalatora można zainstalować dostawcę. Można znaleźć więcej szczegółów na temat instalacji niestandardowej w [dostosować instalację środowiska Azure-SSIS integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Komunikat o błędzie: "Połączenie"..." Nie znaleziono"

Ten błąd mógł wystąpić znany problem w starszych wersjach programu SQL Server Management Studio (SSMS). Jeśli pakiet zawiera składnik niestandardowej (na przykład składniki usług SSIS Azure Feature Pack lub partnera), który nie jest zainstalowany na komputerze, gdzie SSMS jest używany w celu wdrożenia, SSMS usunie składnika i spowodować wystąpienie błędu. Uaktualnij [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) do najnowszej wersji, która ma problemu.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Komunikat o błędzie: "Nie ma wystarczającej ilości miejsca na dysku"

Ten błąd oznacza, że dysk lokalny jest używany w w węzeł środowiska SSIS integration runtime. Sprawdź, czy Twoje pakietu lub instalacja niestandardowa zużywa dużo miejsca na dysku:
* Jeśli dysk jest używany przez pakiet, nastąpi zwolnienie się po zakończeniu wykonywania pakietu.
* Jeśli dysk jest używany przez ustawienia niestandardowe, można będzie muszą można zatrzymać środowiska SSIS integration runtime zmodyfikować skrypt i uruchom ponownie środowisko integration runtime. Kontener całej usługi Azure blob, który określonym dla instalacji niestandardowej zostaną skopiowane do węzeł środowiska SSIS integration runtime, więc sprawdza, czy jest niepotrzebne zawartości w ramach tego kontenera.

### <a name="error-message-cannot-open-file-"></a>Komunikat o błędzie: "Nie można otworzyć pliku"...""

Ten błąd występuje, gdy wykonanie pakietu nie może znaleźć pliku na dysku lokalnym w środowiska SSIS integration runtime. Spróbuj wykonać następujące akcje:
* Nie używaj ścieżki bezwzględnej w pakiecie, który jest wykonywany w środowiska SSIS integration runtime. Użyj do bieżącego katalogu roboczego wykonywania (.) lub folderu tymczasowego (% TEMP %) Zamiast tego.
* Jeśli musisz utrwalić niektóre pliki z węzły środowiska SSIS integration runtime, przygotuj plików zgodnie z opisem w [dostosować instalację](how-to-configure-azure-ssis-ir-custom-setup.md). Wszystkie pliki w katalogu roboczym zostaną wyczyszczone po zakończeniu wykonywania.
* Użyj usługi Azure Files zamiast przechowywać plik w węźle środowiska SSIS integration runtime. Aby uzyskać więcej informacji, zobacz [udziałów plików platformy Azure użyj](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Komunikat o błędzie: "Bazy danych"SSISDB"osiągnęła limit przydziału rozmiaru"

Potencjalną przyczyną jest to, czy baza danych SSISDB, utworzone w bazie danych Azure SQL lub wystąpienia zarządzanego, podczas tworzenia środowiska SSIS integration runtime osiągnęła limit przydziału. Spróbuj wykonać następujące akcje:
* Należy rozważyć zwiększenie liczby jednostek DTU bazy danych. Można znaleźć szczegółowe informacje w [limity zasobów bazy danych SQL dla serwera usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Sprawdź, czy pakiet wygeneruje wiele dzienników. Jeśli tak, można skonfigurować zadania elastycznego, aby wyczyścić te dzienniki. Aby uzyskać więcej informacji, zobacz [czyszczenie dzienników bazy danych SSISDB za pomocą zadania elastycznych baz danych Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Komunikat o błędzie: "Limit żądań dla bazy danych jest... i został osiągnięty."

Jeśli wiele pakietów działają równolegle w środowiska SSIS integration runtime, ten błąd może wystąpić, ponieważ bazy danych SSISDB osiągnie limit żądań. Należy rozważyć zwiększenie DTC SSISDB, aby rozwiązać ten problem. Można znaleźć szczegółowe informacje w [limity zasobów bazy danych SQL dla serwera usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Komunikat o błędzie: "SSIS operacja nie powiodła się operacja nieoczekiwany stan:..."

Błąd najczęściej jest spowodowany przez problem przejściowy, więc spróbuj ponownie uruchomić wykonanie pakietu. Zastosuj wzorzec ponawiania pakietu SSIS, wykonaj następujące czynności:

* Upewnij się, że pakiety usług SSIS, można uruchomić w przypadku niepowodzenia bez efektów ubocznych (na przykład utraty danych lub duplikacji danych).
* Konfigurowanie **ponów** i **interwał ponawiania** z **wykonywanie pakietu SSIS** działania na **ogólne** kartę. ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* W przypadku ADO.NET i OLE DB źródłowy lub docelowy składnika, ustawić **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w pakietu SSIS lub działania SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Komunikat o błędzie: "Nie jest Brak aktywnych procesów roboczych".

Ten błąd zazwyczaj oznacza, że środowiska SSIS integration runtime jest komunikat o złej kondycji. Sprawdź portalu Azure dotyczące stanu i szczegółowe informacje o błędach. Aby uzyskać więcej informacji, zobacz [środowiska Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Komunikat o błędzie: "Środowiska integration runtime nie może zostać uaktualniony i ostatecznie przestanie działać, ponieważ firma Microsoft nie może uzyskać dostępu w kontenerze obiektów Blob platformy Azure parametru instalacji niestandardowej."

Ten błąd występuje, gdy środowiska SSIS integration runtime nie może uzyskać dostępu magazyn skonfigurowany do instalacji niestandardowej. Sprawdź, czy sygnatury dostępu współdzielonego (SAS) podany identyfikator URI jest prawidłowa i czy nie upłynął.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Komunikat o błędzie: "Dostawca Microsoft OLE DB usług Analysis Services. "Hresult: 0x80004005 opis: " Błąd modelu COM: Błąd modelu COM: mscorlib; Obiekt docelowy wywołania zgłosił wyjątek"

Jeden potencjalną przyczyną jest, że nazwa użytkownika lub hasło, za pomocą usługi Azure Multi-Factor Authentication, włączone jest skonfigurowany do uwierzytelniania usługi Azure Analysis Services. Uwierzytelnianie nie jest obsługiwana w środowiska SSIS integration runtime. Spróbuj użyć jednostki usługi do uwierzytelniania usługi Azure Analysis Services:
1. Przygotowywanie jednostki usługi, zgodnie z opisem w [automatyzacji za pomocą jednostki usługi](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. Konfigurowanie Menedżera połączeń **Użyj określonej nazwy użytkownika i hasła**: Ustaw **AppID** jako nazwa użytkownika i **clientSecret** jako hasło.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Komunikat o błędzie: "ADONET źródła nie powiodło się, można uzyskać połączenia {GUID} z następujący komunikat o błędzie: Logowanie użytkownika "NT\LOGOWANIE" powiodło się "podczas korzystania z tożsamości zarządzanej

Upewnij się, nie Konfiguruj metodę uwierzytelniania Menedżera połączeń jako **uwierzytelnianie hasłem usługi Active Directory** gdy parametr *ConnectUsingManagedIdentity* jest **True** . Możesz skonfigurować go jako **uwierzytelniania SQL** zamiast tego, która jest ignorowana, jeśli *ConnectUsingManagedIdentity* jest ustawiona.

### <a name="package-execution-takes-too-long"></a>Wykonanie pakietu trwa zbyt długo

Poniżej przedstawiono potencjalne przyczyny i zalecane akcje:
* Zbyt wiele wykonań pakietu zostało zaplanowane na środowiska SSIS integration runtime. Wszystkie te wykonań będzie czekał w kolejce ich Włącz.
  * Określ maksymalną za pomocą następującej formuły: 
    
    Liczba wykonań maksymalna liczba równoległych na środowisko IR = liczba węzłów * maksymalna liczba równoległych wykonywania na węzeł
  * Aby dowiedzieć się, jak ustawić liczbę węzłów i maksymalna wykonywanie równoległe w każdym węźle, zobacz [tworzenie środowiska Azure-SSIS integration runtime w usłudze Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Środowiska SSIS integration runtime został zatrzymany lub ma stan złej kondycji. Aby dowiedzieć się, jak sprawdzić stan środowiska uruchomieniowego integracji usług SSIS i błędów, zobacz [środowiska Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Zalecamy również ustawić limitu czasu na **ogólne** karty: ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Niska wydajność podczas wykonywania pakietu

Spróbuj wykonać następujące akcje:

* Upewnij się, że środowiska SSIS integration runtime jest w tym samym regionie, co dane źródłowe i docelowe.

* Ustaw poziom rejestrowania wykonanie pakietu do **wydajności** do zbierania informacji o czasie trwania dla każdego składnika podczas wykonywania. Aby uzyskać więcej informacji, zobacz [rejestrowania Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Sprawdź wydajność węzła IR w witrynie Azure portal:
  * Aby uzyskać informacje o sposobie monitorowania środowiska SSIS integration runtime, zobacz [środowiska Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historia Procesora/pamięci można znaleźć środowiska SSIS integration Runtime, wyświetlając metryki usługi data factory w witrynie Azure portal.
    ![Monitorowanie metryk środowiska SSIS integration Runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
