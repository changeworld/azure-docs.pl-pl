---
title: Rozwiązywanie problemów z usługą SSIS Integration Runtime Management w programie Azure Data Factory | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z zarządzaniem Integration Runtime SSIS (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253013"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Rozwiązywanie problemów z usługą SSIS Integration Runtime Management w programie Azure Data Factory

Ten dokument zawiera przewodniki dotyczące rozwiązywania problemów związanych z zarządzaniem Integration Runtime SSIS (SSIS IR).

## <a name="overview"></a>Omówienie

W portalu usługi ADF zostanie wyświetlony komunikat o błędzie, który zostanie zwrócony z polecenia cmdlet programu PowerShell, jeśli wystąpi problem związany z inicjowaniem obsługi lub anulowaniem aprowizacji usługi SSIS IR. Ten błąd jest zawsze w formacie jako kod błędu z szczegółowym komunikatem o błędzie.

Oznacza to, że usługa ma pewne problemy przejściowe, jeśli kod błędu to InternalServerError. Możesz ponowić próbę wykonania operacji później. Skontaktuj się z zespołem pomocy technicznej Azure Data Factory, jeśli ponowienie nie pomoże.

Istnieją trzy główne zależności zewnętrzne, które mogą spowodować błędy: Azure SQL Database serwera lub wystąpienia zarządzanego, niestandardowego skryptu instalacji i konfiguracji Virtual Network, jeśli kod błędu nie jest InternalServerError.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemy z serwerem Azure SQL Database lub wystąpieniem zarządzanym

Serwer Azure SQL Database lub wystąpienie zarządzane jest wymagany w przypadku aprowizacji usług SSIS IR z bazą danych wykazu usług SSIS. Serwer Azure SQL Database lub wystąpienie zarządzane powinno być dostępne przez środowisko IR usług SSIS. Konto serwera Azure SQL Database lub wystąpienia zarządzanego powinno mieć uprawnienia do tworzenia bazy danych usług SSIS (SSISDB). W przypadku wystąpienia błędu kod błędu z szczegółowym komunikatem o wyjątku SQL zostanie wyświetlony w portalu ADF. Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać problemy dotyczące kodów błędów.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ten problem może pojawić się w przypadku aprowizacji nowego środowiska IR SSIS lub w trakcie pracy z systemem.

Może to być spowodowane następującymi przyczynami, jeśli wystąpi błąd podczas aprowizacji podczerwieni, a w komunikacie o błędzie można uzyskać szczegóły komunikatu SqlException.

* Problem z połączeniem sieciowym. Sprawdź, czy nazwa hosta SQL Server lub wystąpienia zarządzanego jest dostępna, a zapora lub sieciowej grupy zabezpieczeń blokuje środowisko SSIS IR, aby uzyskać dostęp do serwera.
* Logowanie nie powiodło się i użyto uwierzytelniania SQL. Oznacza to, że podane konto nie może zalogować się do SQL Server. Upewnij się, że podano poprawne konto użytkownika.
* Logowanie nie powiodło się i używane jest uwierzytelnianie usługi AAD (tożsamość zarządzana). Dodaj zarządzaną tożsamość fabryki do grupy usługi AAD, a zarządzana tożsamość ma uprawnienia dostępu do serwera bazy danych wykazu.
* Limit czasu połączenia jest zawsze spowodowany konfiguracją związaną z zabezpieczeniami. Zaleca się utworzenie nowej maszyny wirtualnej, przyłączenie maszyny wirtualnej do tej samej sieci wirtualnej, jeśli jest ona w sieci wirtualnej, a następnie zainstalowanie programu SSMS i sprawdzenie stanu serwera Azure SQL Database lub wystąpienia zarządzanego.

Inne problemy można znaleźć w komunikacie zawierającym szczegółowe informacje o błędzie wyjątku SQL i rozwiązaniu problemu wyświetlanego w komunikacie o błędzie. Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej serwera Azure SQL Database lub wystąpienia zarządzanego.

Prawdopodobnie istnieje pewna sieciowa Grupa zabezpieczeń lub zapora, jeśli wystąpi błąd podczas działania w podczerwieni, co powoduje, że węzeł procesu roboczego programu SSIS nie ma już dostępu do serwera Azure SQL Database lub wystąpienia zarządzanego. Odblokuj węzeł procesu roboczego programu SSIS IR, aby uzyskać dostęp do serwera Azure SQL Database lub wystąpienia zarządzanego.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Komunikat o błędzie jest taki jak "SSISDB bazy danych" osiągnął limit przydziału rozmiaru. Partycjonowanie lub usuwanie danych, usuwanie indeksów lub zapoznaj się z dokumentacją, aby zapoznać się z możliwymi rozwiązaniami. Dostępne są następujące rozwiązania:
* Zwiększ limit przydziału rozmiaru SSISDB.
* Zmień konfiguracje SSISDB, aby zmniejszyć rozmiar następujący:
   * Zmniejszenie okresu przechowywania i liczby wersji projektu.
   * Skrócenie okresu przechowywania dziennika.
   * Zmiana domyślnego poziomu dziennika i tak dalej.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Ten błąd oznacza, że serwer Azure SQL Database lub wystąpienie zarządzane ma już SSISDB utworzone i używane przez inne środowisko IR. Musisz podać inny serwer Azure SQL Database lub wystąpienie zarządzane lub usunąć istniejące SSISDB i ponownie uruchomić nowe środowisko IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Przyczyną tego błędu może być Poniższa przyczyna.

* Konto użytkownika skonfigurowane dla środowiska IR usług SSIS nie ma uprawnień do tworzenia bazy danych. Można przyznać użytkownikowi uprawnienia do tworzenia bazy danych.
* Utwórz limit czasu bazy danych, taki jak limit czasu wykonywania, limit czasu operacji bazy danych i tak dalej. Możesz spróbować ponownie później, aby sprawdzić, czy problem został rozwiązany. Skontaktuj się z zespołem pomocy technicznej serwera Azure SQL Database lub wystąpienia zarządzanego, jeśli próba nie zadziała.

W przypadku innych problemów Sprawdź komunikat o błędzie wyjątku SQL i usuń problem opisany w komunikacie o błędzie. Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej serwera Azure SQL Database lub wystąpienia zarządzanego.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Komunikat o błędzie jest podobny do "Nieprawidłowa nazwa obiektu wykazu. catalog_properties". "oznacza to, że masz już bazę danych o nazwie SSISDB, ale nie została ona utworzona przez program SSIS IR lub baza danych jest w nieprawidłowym stanie spowodowanym błędami w ostatniej aprowizacji programu SSIS IR. Istnieje możliwość porzucenia istniejącej bazy danych o nazwie SSISDB lub skonfigurowania nowego serwera Azure SQL Database lub wystąpienia zarządzanego dla środowiska IR.

## <a name="custom-setup"></a>Konfiguracja niestandardowa

Konfiguracja niestandardowa udostępnia interfejs umożliwiający dodanie własnych kroków instalacyjnych podczas aprowizacji lub ponownej konfiguracji środowiska IR usług SSIS. Aby uzyskać więcej informacji, zobacz [Dostosowywanie Instalatora dla środowiska Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Upewnij się, że kontener zawiera tylko niezbędne pliki instalacji niestandardowej, ponieważ wszystkie pliki w kontenerze zostaną pobrane do węzła procesu roboczego programu SSIS IR. Zaleca się przetestowanie niestandardowego skryptu instalacji na komputerze lokalnym w celu rozwiązania problemów z wykonaniem skryptu przed uruchomieniem skryptu w środowisku SSIS IR.

Kontener skryptu instalacji niestandardowej zostanie sprawdzony w trakcie działania w podczerwieni, ponieważ jest on regularnie aktualizowany, co wymaga ponownego uzyskania dostępu do kontenera w celu pobrania niestandardowego skryptu instalacji i instalacji. Sprawdzanie będzie obejmować, czy kontener jest dostępny i czy plik main. cmd istnieje.

Dowolny błąd z konfiguracją niestandardową spowoduje wystąpienie błędu z CustomSetupScriptFailure kodu, Sprawdź komunikat o błędzie z kodem błędu podrzędnego.  Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać problemy z kodami błędów podrzędnych.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Oznacza to, że usługa SSIS IR nie może uzyskać dostępu do kontenera obiektów blob platformy Azure na potrzeby instalacji niestandardowej. Sprawdź, czy identyfikator URI sygnatury dostępu współdzielonego kontenera jest osiągalny, a nie wygasły.

Najpierw Zatrzymaj środowisko IR, jeśli środowisko IR jest w stanie uruchomienia, ponownie skonfiguruj środowisko IR przy użyciu nowego identyfikatora URI sygnatury dostępu współdzielonego kontenera konfiguracji niestandardowej, a następnie uruchom ten punkt w środowisku IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Oznacza to, że w kontenerze obiektów BLOB nie można znaleźć skryptu instalacji niestandardowej (Main. cmd). Upewnij się, że Main. cmd istnieje w kontenerze, który jest punktem wejścia dla instalacji niestandardowej.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Oznacza to, że wykonywanie skryptu instalacji niestandardowej (Main. cmd) nie powiodło się, można najpierw wypróbować skrypt na komputerze lokalnym lub sprawdzić dzienniki wykonywania niestandardowych konfiguracji w kontenerze obiektów BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Oznacza to wykonanie niestandardowego limitu czasu skryptu Instalatora. Upewnij się, że kontener obiektów BLOB zawiera tylko niezbędne pliki instalacji niestandardowej. Możesz również sprawdzić dzienniki wykonywania konfiguracji niestandardowej w kontenerze obiektów BLOB. Maksymalny okres instalacji niestandardowej został ustawiony na 45 minut przed przekroczeniem limitu czasu, a maksymalny okres obejmuje pobranie wszystkich plików z kontenera i zainstalowanie ich w usłudze SSIS IR. Jeśli potrzebujesz dłuższego okresu, zgłoś bilet pomocy technicznej.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Oznacza to, że przekazywanie dzienników wykonywania instalatora niestandardowego do kontenera obiektów BLOB nie powiodło się, jest to spowodowane tym, że w przypadku programu SSIS IR nie ma uprawnień do zapisu w kontenerze obiektów blob lub niektórych problemów z magazynem lub siecią. Jeśli Konfiguracja niestandardowa zakończyła się pomyślnie, ten błąd nie ma wpływu na żadną funkcję SSIS, ale brakuje dzienników. Jeśli instalacja niestandardowa zakończyła się niepowodzeniem z powodu innego błędu i nie przekażemy dziennika, będziemy zgłosić ten błąd najpierw, aby można było przekazać dziennik do analizy, a po rozwiązaniu tego problemu zostanie zgłosić więcej określonego problemu. Jeśli ten problem nie zostanie rozwiązany po ponowieniu próby, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfiguracja sieci wirtualnej

Podczas dołączania środowiska SSIS IR do Virtual Network (VNet) używa sieci wirtualnej w ramach subskrypcji użytkownika. Aby uzyskać więcej informacji, zobacz Dołączanie [środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli wystąpi problem związany z siecią wirtualną, zobaczysz błąd jak poniżej

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Przyczyną może być przyczyny wariantów. Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać problemy z kodami błędów podrzędnych.

### <a name="forbidden"></a>Zabroniony

Komunikat o błędzie ma postać "subnetId nie jest włączona dla bieżącego konta. Dostawca zasobów Microsoft. Batch nie jest zarejestrowany w ramach tej samej subskrypcji sieci wirtualnej ".

Oznacza Azure Batch nie może uzyskać dostępu do sieci wirtualnej. Zarejestruj dostawcę zasobów Microsoft. Batch w ramach tej samej subskrypcji sieci wirtualnej.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Komunikat o błędzie jest podobny do "określona Sieć wirtualna nie istnieje lub usługa Batch nie ma do niej dostępu" lub "określona podsieć XXX nie istnieje".

Oznacza to, że sieć wirtualna nie istnieje lub usługa Azure Batch nie może uzyskać do niej dostępu lub Podana podsieć nie istnieje. Upewnij się, że sieć wirtualna i podsieci istnieją i Azure Batch mogą uzyskać do nich dostęp.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Komunikat przypomina "Niepowodzenie aprowizacji Integration Runtime w sieci wirtualnej. Jeśli skonfigurowano ustawienia serwera DNS lub sieciowej grupy zabezpieczeń, upewnij się, że serwer DNS jest dostępny i sieciowej grupy zabezpieczeń jest poprawnie skonfigurowany.

Prawdopodobnie masz niestandardową konfigurację serwera DNS lub ustawień sieciowej grupy zabezpieczeń, co spowoduje, że nie można rozpoznać nazwy serwera platformy Azure wymaganej przez program SSIS IR lub nie można uzyskać do niej dostępu. Aby uzyskać więcej informacji, zobacz dokument [konfiguracji sieci wirtualnej usługi SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) . Jeśli nadal masz problemy, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Program SSIS IR zostanie automatycznie zaktualizowany w regularnych odstępach czasu, a w trakcie uaktualniania zostanie utworzona nowa pula Azure Batch i zostanie usunięta stara Pula Azure Batch, zasób powiązany z siecią wirtualną dla starej puli zostanie usunięty i zostanie utworzony nowy zasób powiązany z siecią wirtualną. ramach. Ten błąd oznacza usunięcie zasobu związanego z siecią wirtualną dla starej puli nie powiodło się z powodu blokady usuwania na poziomie subskrypcji lub grupy zasobów. Usuń blokadę usuwania.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Inicjowanie obsługi środowiska IR SSIS może zakończyć się niepowodzeniem z powodu rodzaju przyczyny, a jeśli wystąpi awaria, wszystkie utworzone zasoby zostaną usunięte. Nie można jednak usunąć zasobów sieci wirtualnej, ponieważ istnieje blokada usuwania zasobów na poziomie subskrypcji lub grupy zasobów. Usuń blokadę usuwania i uruchom ponownie środowisko IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Podczas zatrzymywania środowiska SSIS IR wszystkie zasoby związane z siecią wirtualną zostaną usunięte, ale usunięcie nie powiodło się z powodu blokady usuwania zasobów na poziomie subskrypcji lub grupy zasobów. Usuń blokadę usuwania i spróbuj ponownie wykonać operację.

### <a name="nodeunavailable"></a>NodeUnavailable

Ten błąd występuje w czasie, gdy działa środowisko IR, oznacza to, że środowisko IR ma kondycję przed i stanie działać w złej kondycji. jest to zawsze spowodowane zmianą konfiguracji serwera DNS lub sieciowej grupy zabezpieczeń Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci wirtualnej usług SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli nadal masz problemy, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.
