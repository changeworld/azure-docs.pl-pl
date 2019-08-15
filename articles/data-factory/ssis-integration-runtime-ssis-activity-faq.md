---
title: Rozwiązywanie problemów z wykonywaniem pakietów w środowisku SSIS Integration Runtime | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z wykonaniem pakietu SSIS w środowisku SSIS Integration Runtime
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
ms.openlocfilehash: a7ad0f3be754029c654b04d19750aab7bbcd210d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933632"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Rozwiązywanie problemów z wykonywaniem pakietów w środowisku SSIS Integration Runtime

Ten artykuł zawiera najczęstsze błędy, które można napotkać podczas wykonywania pakietów SQL Server Integration Services (SSIS) w programie SSIS Integration Runtime. Opisuje potencjalne przyczyny i akcje rozwiązywania błędów.

## <a name="where-to-find-logs-for-troubleshooting"></a>Gdzie można znaleźć dzienniki do rozwiązywania problemów

Użyj portalu Azure Data Factory, aby sprawdzić dane wyjściowe działania wykonywania pakietu SSIS. Dane wyjściowe obejmują wyniki wykonywania, komunikaty o błędach i identyfikator operacji. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie potoku](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Użyj wykazu usług SSIS (SSISDB), aby sprawdzić dzienniki szczegółów wykonania. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie uruchomionych pakietów i innych operacji](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Typowe błędy, przyczyny i rozwiązania

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Komunikat o błędzie: "Upłynął limit czasu połączenia" lub "usługa napotkała błąd podczas przetwarzania żądania. Spróbuj ponownie. "

Oto potencjalne przyczyny i zalecane akcje:
* Źródło danych lub miejsce docelowe są przeciążone. Sprawdź obciążenie źródła danych lub miejsca docelowego i zobacz, czy ma ono wystarczającą pojemność. Na przykład jeśli używasz Azure SQL Database, rozważ skalowanie w górę, jeśli baza danych prawdopodobnie przekroczy limit czasu.
* Sieć między środowiskiem programu SSIS Integration Runtime a źródłem danych lub miejscem docelowym jest niestabilna, szczególnie w przypadku, gdy połączenie jest w wielu regionach lub między środowiskiem lokalnym i platformą Azure. Zastosuj wzorzec ponawiania prób w pakiecie SSIS, wykonując następujące czynności:
  * Upewnij się, że pakiety SSIS mogą być ponownie uruchamiane po awarii bez efektów ubocznych (na przykład utrata danych lub duplikowanie danych).
  * Skonfiguruj **ponowną próbę** i **interwał ponawiania próby** **wykonania pakietu SSIS** na karcie **Ogólne** . ![Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * W przypadku składnika ADO.NET i źródła OLE DB lub docelowego ustaw wartość **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w ramach pakietu SSIS lub działania SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Komunikat o błędzie: "Źródło sieci ADO nie może uzyskać połączenia"... " podczas ustanawiania połączenia z usługą SQL Server Wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został odnaleziony lub był niedostępny.

Ten problem zwykle oznacza, że źródło danych lub miejsce docelowe są niedostępne z poziomu środowiska SSIS Integration Runtime. Przyczyny mogą być różne. Wypróbuj następujące czynności:
* Upewnij się, że źródło danych lub nazwa/adres IP zostały prawidłowo przeszukane.
* Upewnij się, że Zapora jest prawidłowo ustawiona.
* Upewnij się, że sieć wirtualna jest prawidłowo skonfigurowana, jeśli źródło danych lub miejsce docelowe są lokalne:
  * Możesz sprawdzić, czy problem pochodzi z konfiguracji sieci wirtualnej, udostępniając MASZYNę wirtualną platformy Azure w tej samej sieci wirtualnej. Następnie sprawdź, czy można uzyskać dostęp do źródła danych lub miejsca docelowego z maszyny wirtualnej platformy Azure.
  * Więcej szczegółów na temat używania sieci wirtualnej z usługą SSIS Integration runtime można znaleźć w przyłączeniu [do sieci wirtualnej środowiska Azure-SSIS Integration Runtime](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Komunikat o błędzie: "Źródło sieci ADO nie może uzyskać połączenia"... " za pomocą "nie można utworzyć zarządzanego Menedżera połączeń".

Potencjalną przyczyną jest to, że dostawca ADO.NET używany w pakiecie nie jest zainstalowany w środowisku SSIS Integration Runtime. Dostawcę można zainstalować przy użyciu konfiguracji niestandardowej. Więcej informacji na temat konfiguracji niestandardowej można znaleźć w temacie [Dostosowywanie konfiguracji dla środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Komunikat o błędzie: "Połączenie"... " nie znaleziono "

Ten błąd może powodować znany problem w starszych wersjach programu SQL Server Management Studio (SSMS). Jeśli pakiet zawiera składnik niestandardowy (na przykład dodatek SSIS Azure Feature Pack lub składniki partnera), który nie jest zainstalowany na maszynie, na której przeprowadzane jest wdrożenie za pomocą programu SSMS, program SSMS usunie ten składnik i spowoduje wystąpienie błędu. Uaktualnij narzędzie [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) do najnowszej wersji o rozwiązanym problemie.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Komunikat o błędzie: "kod zakończenia modułu SSIS:-1073741819".

* Potencjalna przyczyna & zalecanej akcji:
  * Przyczyną tego błędu może być ograniczenie źródłowej i docelowej programu Excel, gdy wiele źródeł lub miejsc docelowych programu Excel jest wykonywanych równolegle w wielu wątkach. To ograniczenie można obejść przez zmianę składników programu Excel na wykonywanie w sekwencji lub oddzielenie ich na różne pakiety i wyzwalacze za pomocą właściwości ExecuteOutOfProcess o wartości true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Komunikat o błędzie: "Za mało miejsca na dysku"

Ten błąd oznacza, że dysk lokalny jest używany w węźle SSIS Integration Runtime. Sprawdź, czy pakiet lub Konfiguracja niestandardowa zużywa dużo miejsca na dysku:
* Jeśli dysk jest zużywany przez pakiet, zostanie zwolniony po zakończeniu wykonywania pakietu.
* Jeśli dysk jest używany przez konfigurację niestandardową, należy zatrzymać program SSIS Integration Runtime, zmodyfikować skrypt i ponownie uruchomić środowisko Integration Runtime. Cały kontener obiektów blob platformy Azure określony dla konfiguracji niestandardowej zostanie skopiowany do węzła SSIS Integration Runtime, dlatego sprawdź, czy w tym kontenerze nie ma żadnej niepotrzebnej zawartości.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Komunikat o błędzie: "Nie można pobrać zasobu z serwera głównego. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Kod: 300004. Opis: nie można załadować pliku "* * *".

* Potencjalna przyczyna & zalecanej akcji:
  * Jeśli działanie programu SSIS wykonuje pakiet z systemu plików (plik pakietu lub plik projektu), ten błąd wystąpi, jeśli projekt, pakiet lub plik konfiguracji nie będą dostępne z poświadczeniami dostępu do pakietu dostarczonymi w działaniu usług SSIS.
    * Jeśli używasz usługi Azure File:
      * Ścieżka pliku powinna rozpoczynać się \\od\> \< \\nazwy konta magazynu.\\File.Core.Windows.NET\<ścieżka udziału plików\>
      * Domena powinna mieć wartość "Azure"
      * Nazwa użytkownika powinna być \<nazwą konta magazynu\>
      * Hasło powinno być \<kluczem dostępu do magazynu\>
    * Jeśli używasz pliku lokalnego, sprawdź, czy sieć wirtualna, poświadczenia dostępu do pakietu i uprawnienia są prawidłowo skonfigurowane, aby środowisko Azure-SSIS Integration Runtime mogły uzyskać dostęp do lokalnego udziału plików

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Komunikat o błędzie: "Nazwa pliku"... " określone w połączeniu nie są prawidłowe "

* Potencjalna przyczyna & zalecanej akcji:
  * Określono nieprawidłową nazwę pliku
  * Upewnij się, że używasz nazwy FQDN (w pełni kwalifikowanej nazwy domeny) zamiast godziny krótkiej w Menedżerze połączeń

### <a name="error-message-cannot-open-file-"></a>Komunikat o błędzie: "Nie można otworzyć pliku"... "

Ten błąd występuje, gdy wykonanie pakietu nie może znaleźć pliku na dysku lokalnym w programie SSIS Integration Runtime. Wypróbuj następujące czynności:
* Nie używaj ścieżki bezwzględnej w pakiecie, który jest wykonywany w programie SSIS Integration Runtime. Użyj bieżącego katalogu roboczego wykonywania (.) lub folderu tymczasowego (% TEMP%) INSTEAD.
* Aby zachować niektóre pliki w węzłach SSIS Integration Runtime, przygotuj pliki zgodnie z opisem w temacie [Dostosowywanie Instalatora](how-to-configure-azure-ssis-ir-custom-setup.md). Wszystkie pliki w katalogu roboczym zostaną oczyszczone po zakończeniu wykonywania.
* Użyj Azure Files zamiast przechowywania pliku w węźle SSIS Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz [Korzystanie z udziałów plików platformy Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Komunikat o błędzie: "SSISDB bazy danych" osiągnął limit przydziału rozmiaru "

Możliwą przyczyną jest osiągnięcie przydziału rozmiaru przez bazę danych SSISDB, która została utworzona w bazie danych Azure SQL Database lub wystąpieniu zarządzanym podczas tworzenia środowiska SSIS Integration Runtime. Wypróbuj następujące czynności:
* Rozważ zwiększenie liczby jednostek DTU bazy danych. Aby uzyskać więcej informacji, zobacz [Limity zasobów bazy danych SQL Database dla serwera usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Sprawdź, czy pakiet wygeneruje wiele dzienników. Jeśli tak, możesz skonfigurować zadanie elastyczne, które oczyści te dzienniki. Aby uzyskać szczegółowe informacje, zobacz [Oczyszczanie dzienników SSISDB za pomocą zadań Azure Elastic Database](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Komunikat o błędzie: "Limit żądań dla bazy danych to... i został osiągnięty. "

Jeśli wiele pakietów jest uruchomionych równolegle w programie SSIS Integration Runtime, ten błąd może wystąpić, ponieważ SSISDB osiągnął limit żądań. Aby rozwiązać ten problem, należy rozważyć zwiększenie poziomu usługi DTC SSISDB. Aby uzyskać więcej informacji, zobacz [Limity zasobów bazy danych SQL Database dla serwera usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Komunikat o błędzie: "Operacja SSIS nie powiodła się z nieoczekiwanym stanem operacji:..."

Ten błąd jest głównie spowodowany przez przejściowy problem, dlatego spróbuj ponownie uruchomić wykonywanie pakietu. Zastosuj wzorzec ponawiania prób w pakiecie SSIS, wykonując następujące czynności:

* Upewnij się, że pakiety SSIS mogą być ponownie uruchamiane po awarii bez efektów ubocznych (na przykład utrata danych lub duplikowanie danych).
* Skonfiguruj **ponowną próbę** i **interwał ponawiania próby** **wykonania pakietu SSIS** na karcie **Ogólne** . ![Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* W przypadku składnika ADO.NET i źródła OLE DB lub docelowego ustaw wartość **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w ramach pakietu SSIS lub działania SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Komunikat o błędzie: "Nie ma aktywnego procesu roboczego".

Ten błąd zazwyczaj oznacza, że środowisko SSIS Integration Runtime ma stan złej kondycji. Sprawdź Azure Portal stanu i szczegółowe błędy. Aby uzyskać więcej informacji, zobacz [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Komunikat o błędzie: "Środowisko Integration Runtime nie może zostać uaktualnione i ostatecznie przestanie działać, ponieważ nie można uzyskać dostępu do kontenera obiektów blob platformy Azure podanego dla konfiguracji niestandardowej".

Ten błąd występuje, gdy środowisko SSIS Integration Runtime nie może uzyskać dostępu do magazynu skonfigurowanego na potrzeby instalacji niestandardowej. Sprawdź, czy podany identyfikator URI sygnatury dostępu współdzielonego (SAS) jest prawidłowy i nie wygasł.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Komunikat o błędzie: "Dostawca OLE DB firmy Microsoft dla Analysis Services. Wynik Opis 0x80004005: ' Błąd modelu COM: Błąd modelu COM: mscorlib; Element docelowy wywołania zgłosił wyjątek.

Jedną z potencjalnych przyczyn jest to, że nazwa użytkownika lub hasło z włączonym uwierzytelnianiem usługi Azure MFA jest skonfigurowane pod kątem uwierzytelniania Azure Analysis Services. To uwierzytelnianie nie jest obsługiwane w programie SSIS Integration Runtime. Spróbuj użyć jednostki usługi do uwierzytelniania Azure Analysis Services:
1. Przygotuj jednostkę usługi zgodnie z opisem w temacie [Automatyzacja przy użyciu jednostek usługi](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. W Menedżerze połączeń Skonfiguruj **użycie określonej nazwy użytkownika i hasła**: Ustaw **Identyfikator aplikacji** jako nazwę użytkownika i **clientSecret** jako hasło.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Komunikat o błędzie: "ADONET źródła nie powiodło się uzyskanie połączenia {GUID} z następującym komunikatem o błędzie: Logowanie użytkownika "NT AUTHORITY\ANONYMOUS LOGON" "przy użyciu tożsamości zarządzanej nie powiodło się

Upewnij się, że nie skonfigurowano metody uwierzytelniania Menedżera połączeń jako **Active Directory uwierzytelniania hasła** , gdy parametr *ConnectUsingManagedIdentity* ma **wartość true**. Zamiast tego można skonfigurować go jako **uwierzytelnianie SQL** , który jest ignorowany, jeśli ustawiono *ConnectUsingManagedIdentity* .

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Nieoczekiwane wyzwolenie wielu wykonań pakietów

* Potencjalna przyczyna & zalecanej akcji:
  * Działanie procedury składowanej ADF służy do wyzwalania wykonywania pakietów SSIS. Polecenie t-SQL może napotkać przejściowy problem i wyzwolić ponowne uruchomienie, co może spowodować wykonanie wielu pakietów.
  * Zamiast tego użyj działania ExecuteSSISPackage, aby zapewnić, że wykonanie pakietu nie zostanie uruchomione ponownie, chyba że w działaniu zostanie określona liczba ponownych prób Szczegóły można znaleźć pod adresem[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

### <a name="package-execution-takes-too-long"></a>Wykonanie pakietu trwa zbyt długo

Oto potencjalne przyczyny i zalecane akcje:

* Zaplanowano zbyt wiele wykonań pakietów w programie SSIS Integration Runtime. Wszystkie te wykonania będą oczekiwać w kolejce na ich włączenie.
  * Określ maksymalną wartość przy użyciu tej formuły:

    Maksymalna liczba równoległych wykonań na IR = liczba węzłów * maksymalne wykonywanie równoległe na węzeł
  * Aby dowiedzieć się, jak ustawić liczbę węzłów i maksymalne wykonywanie równoległe na węzeł, zobacz temat [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Środowisko uruchomieniowe środowiska SSIS zostało zatrzymane lub ma stan złej kondycji. Aby dowiedzieć się, jak sprawdzić stan i błędy programu SSIS Integration Runtime, zobacz [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Zalecamy również ustawienie limitu czasu na karcie **Ogólne** : ![Ustaw właściwości na karcie](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)ogólne.

### <a name="poor-performance-in-package-execution"></a>Niska wydajność podczas wykonywania pakietu

Wypróbuj następujące czynności:

* Upewnij się, że środowisko w środowisku SSIS Integration Runtime znajduje się w tym samym regionie, co źródło danych i miejsce docelowe.

* Ustaw poziom rejestrowania wykonania pakietu na **wydajność** , aby zbierać informacje o czasie trwania dla każdego składnika w ramach wykonywania. Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie usług Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Sprawdź wydajność węzłów IR w Azure Portal:
  * Aby uzyskać informacje o sposobach monitorowania środowiska usług SSIS Integration Runtime, zobacz [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historię procesora CPU/pamięci dla środowiska SSIS Integration runtime można znaleźć, wyświetlając metryki fabryki danych w Azure Portal.
    ![Monitoruj metryki programu SSIS Integration Runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
