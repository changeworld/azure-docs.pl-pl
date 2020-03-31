---
title: Znane problemy/ograniczenia migracji przy użyciu trybu hybrydowego
description: Dowiedz się więcej o znanych problemach/ograniczeniach migracji przy użyciu usługi migracji bazy danych Azure w trybie hybrydowym.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649606"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Znane problemy/ograniczenia migracji przy użyciu trybu hybrydowego

Znane problemy i ograniczenia związane z korzystaniem z usługi migracji bazy danych Azure w trybie hybrydowym są opisane w poniższych sekcjach.

## <a name="installer-fails-to-authenticate"></a>Nie można uwierzytelnić instalatora

Po przesłaniu certyfikatu do aplikacji AdApp opóźnienie wynosi do kilku minut, zanim będzie mógł uwierzytelnić się za pomocą platformy Azure. Instalator spróbuje ponowić próbę z pewnym opóźnieniem, ale jest możliwe, że opóźnienie propagacji będzie dłuższe niż ponowna próba, a zobaczysz komunikat **FailedToGetAccessTokenException.** Jeśli certyfikat został przesłany do właściwego adapp i poprawny AppId został dostarczony w dmsSettings.json, spróbuj ponownie uruchomić polecenie install.

## <a name="service-offline-after-successful-installation"></a>Usługa "offline" po pomyślnej instalacji

Jeśli usługa jest wyświetlana w trybie offline po pomyślnym zakończeniu procesu instalacji, spróbuj wykonać następujące kroki.

1. W witrynie Azure portal w przypadku usługi migracji bazy danych azure przejdź do karty Ustawienia **hybrydowe,** a następnie sprawdź, czy pracownik jest zarejestrowany, sprawdzając siatkę zarejestrowanych pracowników.

    Stan tego pracownika powinien być **w trybie online,** ale może być wyświetlane jako **w trybie offline,** jeśli występuje problem.

2. Na komputerze roboczym sprawdź stan usługi, uruchamiając następujące polecenie programu PowerShell:

    ```
    Get-Service Scenario*
    ```

    To polecenie podaje stan usługi systemu Windows z uruchomionym pracownikiem. Powinien być tylko jeden wynik. Jeśli pracownik zostanie zatrzymany, można spróbować go ponownie uruchomić za pomocą następującego polecenia programu PowerShell:

    ```
    Start-Service Scenario*
    ```

    Można również sprawdzić usługę w interfejsie użytkownika usług systemu Windows.

3. Jeśli cykle usługi systemu Windows między uruchamianiem i zatrzymaniem, pracownik napotkał problemy z uruchomieniem. Sprawdź dzienniki hybrydowych procesów roboczych usługi migracji usługi Azure Database, aby ustalić problem.

    - Dzienniki procesu instalacji są przechowywane w folderze "dzienniki" w folderze, z którego uruchomiono plik wykonywalny instalatora.
    - Dzienniki procesów roboczych usługi migracji bazy danych Azure są przechowywane w folderze **WorkerLogs** w folderze, w którym jest zainstalowany proces roboczy. Domyślną lokalizacją plików dziennika procesu roboczego hybrydowego jest **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Korzystanie z własnego podpisanego certyfikatu

Certyfikat wygenerowany przez akcję GenerateCert jest certyfikatem z podpisem własnym, który może być nie do przyjęcia na podstawie wewnętrznych zasad zabezpieczeń. Zamiast używać tego certyfikatu, można podać własny certyfikat i podać odcisk palca w dmsSettings.json. Ten certyfikat należy przekazać do adapp i zainstalować na komputerze, na którym instalujesz pracownika hybrydowego usługi migracji usługi Azure Database. Następnie zainstaluj ten certyfikat z kluczem prywatnym w magazynie certyfikatów komputera lokalnego.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Uruchamianie usługi pracownika jako konta o niskich uprawnieniach

Domyślnie usługa hybrydowego procesu migracji usługi azure database migration service działa jako konto systemu lokalnego. Konto używane dla tej usługi można zmienić, o ile konto, którego używasz, ma uprawnienia sieciowe. Aby zmienić konto "uruchom jako" usługi, użyj następującego procesu.

1. Zatrzymaj usługę za pośrednictwem usług systemu Windows lub za pomocą polecenia Zatrzymaj usługę w programie PowerShell.

2. Zaktualizuj usługę, aby użyć innego konta logowania.

3. W certmgr dla certyfikatów komputera lokalnego należy przyznać uprawnienia klucza prywatnego do nowego konta dla certyfikatów **par kluczy hybrydowych aplikacji DMS** i **dms scenario.**

    a. Otwórz certmgr, aby wyświetlić następujące klawisze:

    - Klucz aplikacji hybrydowej DMS
    - Klucz konfiguracji hybrydowego procesu roboczego DMS
    - Para kluczy silnika scenariusza DMS

    b. Kliknij prawym przyciskiem myszy wpis **Klucz hybrydowy DMS,** wskaż **polecenie Wszystkie zadania**, a następnie wybierz polecenie **Zarządzaj kluczami prywatnymi**.

    d. Na karcie **Zabezpieczenia** wybierz pozycję **Dodaj**, a następnie wprowadź nazwę konta.

    d. Użyj tych samych kroków, aby udzielić uprawnień klucza prywatnego dla nowego konta do certyfikatu **pary kluczy aparatu scenariusza DMS.**

## <a name="unregistering-the-worker-manually"></a>Ręczne wyrejestrowanie pracownika

Jeśli nie masz już dostępu do komputera roboczego, możesz wyrejestrować pracownika i ponownie użyć wystąpienia usługi migracji usługi Azure Database, wykonując następujące kroki:

1. W witrynie Azure portal przejdź do wystąpienia usługi migracji bazy danych azure, a następnie przejdź do strony ustawień **hybrydowych.**

   Wpis pracownika pojawi się na liście ze stanem wyświetlanym jako **Offline**.

2. Po prawej stronie listy wpisu pracownika wybierz elipsy, a następnie wybierz pozycję **Wyrejestruj**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Rozwiązywanie problemów dotyczących konkretnych scenariuszy migracji

W poniższych sekcjach opisano problemy specyficzne dla scenariusza związane z używaniem trybu hybrydowego usługi Azure Database Migration Service do przeprowadzania migracji online.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Migracje online do wystąpienia zarządzanego usługi Azure SQL Database

**Wysokie użycie procesora**

**Problem:** W przypadku migracji online do wystąpienia zarządzanego bazy danych SQL komputer z uruchomień hybrydowych napotka wysokie użycie procesora CPU, jeśli istnieje zbyt wiele kopii zapasowych lub jeśli kopie zapasowe są zbyt duże.

**Środki zaradcze:** Aby złagodzić ten problem, należy użyć skompresowanych kopii zapasowych, podzielić migrację, aby używać wielu udziałów lub skalować komputer z systemem hybrydowego procesu roboczego.
