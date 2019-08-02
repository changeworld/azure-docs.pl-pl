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
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609622"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Rozwiązywanie problemów z usługą SSIS Integration Runtime Management w programie Azure Data Factory

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z zarządzaniem w systemie Azure — SQL Server Integration Services (SSIS) Integration Runtime (IR), znane także jako SSIS IR.

## <a name="overview"></a>Omówienie

W przypadku napotkania dowolnego problemu podczas aprowizacji lub anulowania obsługi usługi SSIS IR zostanie wyświetlony komunikat o błędzie w portalu Microsoft Azure Data Factory lub zwrócony błąd z polecenia cmdlet programu PowerShell. Błąd jest zawsze wyświetlany w formacie kodu błędu z szczegółowym komunikatem o błędzie.

Jeśli kod błędu to InternalServerError, usługa ma problemy przejściowe i należy ponowić próbę wykonania tej operacji później. Jeśli ponowienie próby nie pomoże, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

W przeciwnym razie trzy główne zależności zewnętrzne mogą spowodować błędy: serwer Azure SQL Database lub wystąpienie zarządzane, skrypt instalacji niestandardowej i konfigurację sieci wirtualnej.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemy z serwerem Azure SQL Database lub wystąpieniem zarządzanym

Jeśli zainicjowano program SSIS IR z bazą danych wykazu usług SSIS, wymagany jest serwer Azure SQL Database lub wystąpienie zarządzane. Środowisko IR usług SSIS musi mieć dostęp do serwera Azure SQL Database lub wystąpienia zarządzanego. Ponadto konto serwera Azure SQL Database lub wystąpienia zarządzanego powinno mieć uprawnienia do tworzenia bazy danych wykazu usług SSIS (SSISDB). Jeśli wystąpi błąd, kod błędu ze szczegółowym komunikatem o wyjątku SQL zostanie wyświetlony w portalu Data Factory. Skorzystaj z informacji podanych na poniższej liście, aby rozwiązać problemy dotyczące kodów błędów.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ten problem może pojawić się w przypadku aprowizacji nowego środowiska IR usług SSIS lub w trakcie działania środowiska IR. Jeśli ten błąd wystąpi podczas aprowizacji podczerwieni, w komunikacie o błędzie może pojawić się szczegółowy komunikat SqlException, który wskazuje jeden z następujących problemów:

* Problem z połączeniem sieciowym. Sprawdź, czy nazwa hosta SQL Server lub wystąpienia zarządzanego jest dostępna. Sprawdź również, czy żadna zapora lub sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) nie blokuje dostępu do serwera usług SSIS IR.
* Logowanie nie powiodło się podczas uwierzytelniania SQL. Podane konto nie może zalogować się do bazy danych SQL Server. Upewnij się, że podano poprawne konto użytkownika.
* Logowanie nie powiodło się podczas uwierzytelniania Microsoft Azure Active Directory (tożsamość zarządzana). Dodaj zarządzaną tożsamość fabryki do grupy usługi AAD i upewnij się, że zarządzana tożsamość ma uprawnienia dostępu do serwera bazy danych wykazu.
* Limit czasu połączenia. Ten błąd jest zawsze spowodowany przez konfigurację powiązaną z zabezpieczeniami. Zalecamy:
  1. Utwórz nową maszynę wirtualną.
  1. Dołącz maszynę wirtualną do tego samego Microsoft Azure Virtual Network, jeśli w sieci wirtualnej znajduje się port IR.
  1. Zainstaluj program SSMS i sprawdź stan serwera Azure SQL Database lub wystąpienia zarządzanego.

W przypadku innych problemów należy rozwiązać problem opisany w szczegółowym komunikacie o błędzie wyjątku SQL. Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej serwera Azure SQL Database lub wystąpienia zarządzanego.

Jeśli zostanie wyświetlony komunikat o błędzie, gdy działa środowisko IR, sieciowe grupy zabezpieczeń lub zmiany zapory prawdopodobnie uniemożliwiają uzyskiwanie dostępu do serwera Azure SQL Database lub wystąpienia zarządzanego przez węzeł procesu roboczego usługi SSIS. Odblokuj węzeł procesu roboczego podczerwieni SSIS, aby mógł uzyskać dostęp do serwera Azure SQL Database lub wystąpienia zarządzanego.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Oto, jak ten rodzaj komunikatu o błędzie może wyglądać następująco: "SSISDB bazy danych" osiągnął limit przydziału rozmiaru. Partycjonowanie lub usuwanie danych, usuwanie indeksów lub zapoznaj się z dokumentacją, aby zapoznać się z możliwymi rozwiązaniami. 

Dostępne są następujące rozwiązania:
* Zwiększ rozmiar przydziału SSISDB.
* Zmień konfigurację SSISDB, aby zmniejszyć rozmiar:
   * Zmniejszenie okresu przechowywania i liczby wersji projektu.
   * Skrócenie okresu przechowywania dziennika.
   * Zmiana domyślnego poziomu dziennika.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Ten błąd oznacza, że serwer Azure SQL Database lub wystąpienie zarządzane ma już SSISDB i że jest używany przez inny port IR. Musisz podać inny serwer Azure SQL Database lub wystąpienie zarządzane, a także usunąć istniejące SSISDB i ponownie uruchomić nowe środowisko IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Ten błąd może wystąpić z jednego z następujących powodów:

* Konto użytkownika, które jest skonfigurowane dla środowiska IR SSIS, nie ma uprawnień do tworzenia bazy danych. Można przyznać użytkownikowi uprawnienia do tworzenia bazy danych.
* Przekroczenie limitu czasu podczas tworzenia bazy danych, takich jak limit czasu wykonywania lub limit czasu operacji bazy danych. Spróbuj ponownie wykonać operację później. Jeśli ponowienie próby nie zadziała, skontaktuj się z zespołem pomocy technicznej serwera Azure SQL Database lub wystąpienia zarządzanego.

W przypadku innych problemów Sprawdź komunikat o błędzie wyjątku SQL i usuń problem opisany w szczegółach błędu. Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej serwera Azure SQL Database lub wystąpienia zarządzanego.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Ten rodzaj komunikatu o błędzie wygląda następująco: "Nieprawidłowa nazwa obiektu" Catalog. catalog_properties "." W takiej sytuacji masz już bazę danych o nazwie SSISDB, ale nie została ona utworzona przez program SSIS IR lub baza danych znajduje się w nieprawidłowym stanie spowodowanym błędami w ostatniej aprowizacji programu SSIS IR. Istnieje możliwość usunięcia istniejącej bazy danych o nazwie SSISDB lub skonfigurowania nowego serwera Azure SQL Database lub wystąpienia zarządzanego dla środowiska IR.

## <a name="custom-setup-issues"></a>Problemy z instalacją niestandardową

Konfiguracja niestandardowa udostępnia interfejs umożliwiający dodanie własnych kroków instalacyjnych podczas aprowizacji lub ponownej konfiguracji środowiska IR usług SSIS. Aby uzyskać więcej informacji, zobacz [Dostosowywanie Instalatora dla Integration Runtime Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Upewnij się, że kontener zawiera tylko niezbędne pliki instalacji niestandardowej; wszystkie pliki w kontenerze zostaną pobrane do węzła proces roboczy programu SSIS IR. Zalecamy przetestowanie niestandardowego skryptu instalacji na komputerze lokalnym, aby naprawić wszelkie problemy z wykonaniem skryptu przed uruchomieniem skryptu w środowisku SSIS IR.

Kontener skryptu instalacji niestandardowej zostanie sprawdzony podczas działania środowiska IR, ponieważ usługa SSIS IR jest regularnie aktualizowana. Ta aktualizacja wymaga dostępu do kontenera w celu pobrania niestandardowego skryptu instalacji i zainstalowania go ponownie. Proces sprawdza także, czy kontener jest dostępny i czy plik main. cmd istnieje.

W przypadku dowolnego błędu, który obejmuje instalację niestandardową, zobaczysz kod błędu CustomSetupScriptFailure z kodem podrzędnym, takim jak CustomSetupScriptBlobContainerInaccessible lub CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Ten błąd oznacza, że usługa SSIS IR nie może uzyskać dostępu do kontenera obiektów blob platformy Azure na potrzeby instalacji niestandardowej. Upewnij się, że identyfikator URI sygnatury dostępu współdzielonego kontenera jest osiągalny i nie wygasł.

Zatrzymaj środowisko IR, jeśli jest ono uruchomione, ponownie skonfiguruj środowisko IR przy użyciu nowego identyfikatora URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych, a następnie ponownie uruchom środowisko IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Ten błąd oznacza, że środowisko IR SSIS nie może znaleźć niestandardowego skryptu instalacji (Main. cmd) w kontenerze obiektów BLOB. Upewnij się, że główny. cmd istnieje w kontenerze, który jest punktem wejścia dla instalacji niestandardowej.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Ten błąd oznacza wykonanie skryptu instalacji niestandardowej (Main. cmd) nie powiodło się. Najpierw Wypróbuj skrypt na maszynie lokalnej lub Sprawdź dzienniki wykonywania niestandardowej instalacji w kontenerze obiektów BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Ten błąd wskazuje na wykonanie limitu czasu skryptu konfiguracji niestandardowej. Upewnij się, że kontener obiektów BLOB zawiera tylko niezbędne pliki instalacji niestandardowej. Należy również sprawdzić dzienniki wykonywania instalacji niestandardowej w kontenerze obiektów BLOB. Maksymalny okres instalacji niestandardowej to 45 minut przed upływem limitu czasu, a maksymalny okres obejmuje czas pobierania wszystkich plików z kontenera i instalowania go na urządzeniu SSIS IR. Jeśli potrzebujesz dłuższego okresu, zgłoś bilet pomocy technicznej.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Ten błąd oznacza, że próba przekazania dzienników wykonywania instalatora niestandardowego do kontenera obiektów BLOB nie powiodła się. Ten problem występuje, ponieważ środowisko IR SSIS nie ma uprawnień do zapisu w kontenerze obiektów blob lub z powodu problemów z magazynem lub siecią. Jeśli Konfiguracja niestandardowa zakończyła się pomyślnie, ten błąd nie wpłynie na żadną funkcję SSIS, ale brakuje dzienników. Jeśli instalacja niestandardowa kończy się niepowodzeniem z powodu innego błędu, a dziennik nie zostanie przekazany, zostanie najpierw Zgłoś ten błąd, aby można było przekazać dziennik do analizy. Ponadto po rozwiązaniu tego problemu będziemy zgłaszać bardziej szczegółowe problemy. Jeśli ten problem nie zostanie rozwiązany po ponowieniu próby, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfiguracja sieci wirtualnej

Po dołączeniu programu SSIS IR do usługi Azure Virtual Network, usługa SSIS IR używa sieci wirtualnej, która jest objęta subskrypcją użytkownika. Aby uzyskać więcej informacji, zobacz Dołączanie [Integration Runtime Azure-SSIS do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli wystąpi problem związany z Virtual Network, zobaczysz jeden z następujących błędów.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Ten błąd może wystąpić z różnych powodów. Aby rozwiązać ten problem, zobacz sekcje [zabronione](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue)i [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) .

### <a name="forbidden"></a>Zabroniony

Ten rodzaj błędu może wyglądać następująco: "SubnetId nie jest włączona dla bieżącego konta. Dostawca zasobów Microsoft. Batch nie jest zarejestrowany w ramach tej samej subskrypcji sieci wirtualnej ".

Te szczegóły oznaczają, że Azure Batch nie mogą uzyskać dostępu do sieci wirtualnej. Zarejestruj dostawcę zasobów Microsoft. Batch w ramach tej samej subskrypcji co Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Ten rodzaj błędu może być podobny do jednego z następujących: 

- "Określona Sieć wirtualna nie istnieje lub usługa Batch nie ma do niej dostępu".
- "Określona podsieć XXX nie istnieje".

Te błędy oznaczają, że sieć wirtualna nie istnieje, usługa Azure Batch nie może uzyskać do niej dostępu lub Podana podsieć nie istnieje. Upewnij się, że sieć wirtualna i podsieć są dostępne i że Azure Batch mogą uzyskać do nich dostęp.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Ten rodzaj komunikatu o błędzie może wyglądać następująco: "Nie można zainicjować obsługi administracyjnej Integration Runtime w sieci wirtualnej. Jeśli skonfigurowano ustawienia serwera DNS lub sieciowej grupy zabezpieczeń, upewnij się, że serwer DNS jest dostępny i sieciowej grupy zabezpieczeń jest prawidłowo skonfigurowany.

W takiej sytuacji prawdopodobnie istnieje dostosowana konfiguracja serwera DNS lub ustawień sieciowej grupy zabezpieczeń, co uniemożliwia rozpoznanie lub uzyskanie dostępu do nazwy serwera platformy Azure wymaganej przez program SSIS IR. Aby uzyskać więcej informacji, zobacz [konfiguracja Virtual Network środowiska SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Program SSIS IR zostanie automatycznie zaktualizowany w regularnych odstępach czasu. Podczas uaktualniania jest tworzona nowa pula Azure Batch, a stara Pula Azure Batch zostanie usunięta. Ponadto zostaną usunięte zasoby związane z Virtual Networką dla starej puli, a nowe zasoby związane z Virtual Network są tworzone w ramach subskrypcji. Ten błąd oznacza, że usunięcie zasobów związanych z Virtual Network dla starej puli nie powiodło się z powodu blokady usuwania na poziomie subskrypcji lub grupy zasobów. Ponieważ klient kontroluje i ustawia blokadę usuwania, w tej sytuacji musi usunąć blokadę usuwania.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

W przypadku niepowodzenia aprowizacji podczerwieni usług SSIS wszystkie utworzone zasoby zostaną usunięte. Jeśli jednak istnieje blokada usuwania zasobów na poziomie subskrypcji lub grupy zasobów, Virtual Network zasoby nie zostaną usunięte zgodnie z oczekiwaniami. Aby naprawić ten błąd, Usuń blokadę usuwania i uruchom ponownie środowisko IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Po zatrzymaniu środowiska SSIS IR wszystkie zasoby związane z Virtual Network są usuwane. Jednak usuwanie może zakończyć się niepowodzeniem, jeśli istnieje zasób z blokadą usuwania na poziomie subskrypcji lub grupy zasobów. W tym miejscu klient kontroluje i ustawia blokadę usuwania. W związku z tym należy usunąć blokadę usuwania, a następnie ponownie zatrzymać program SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

Ten błąd występuje, gdy działa środowisko IR i oznacza, że środowisko IR stał się złej kondycji. Ten błąd jest zawsze spowodowany przez zmianę w konfiguracji serwera DNS lub sieciowej grupy zabezpieczeń, która blokuje program SSIS IR łączenie się z niezbędną usługą. Ponieważ konfiguracja serwera DNS i sieciowej grupy zabezpieczeń jest kontrolowana przez klienta, klient musi rozwiązać problemy z blokowaniem. Aby uzyskać więcej informacji, zobacz [konfiguracja Virtual Network środowiska SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.
