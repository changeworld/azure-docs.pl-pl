---
title: Rozwiązywanie problemów z błędami podczas wykonywania kopii zapasowej baz danych SAP HANA przy użyciu usługi Azure Backup | Dokumentacja firmy Microsoft
description: Ten przewodnik wyjaśnia, jak rozwiązywać typowe błędy podczas próby tworzenia kopii zapasowych baz danych SAP HANA przy użyciu usługi Azure Backup.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514169"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Rozwiązywanie problemów z kopii zapasowej serwera SAP HANA na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów na potrzeby ochrony baz danych SAP HANA na maszynach wirtualnych platformy Azure. Przed przejściem do rozwiązywania problemów, Przyjrzyjmy się kilku kluczowych zagadnieniach dotyczących uprawnień i ustawień.

## <a name="understanding-pre-requisites"></a>Opis wymagań wstępnych

Jako część [wstępne](backup-azure-sap-hana-database.md#prerequisites), skrypt wstępnej rejestracji można uruchamiać na maszynie wirtualnej zainstalowanym HANA skonfigurować odpowiednie uprawnienia.

### <a name="setting-up-permissions"></a>Ustawianie uprawnień

Wstępnej rejestracji działanie skryptu:

1. Tworzy AZUREWLBACKUPHANAUSER systemu HANA i dodaje wymagane role i uprawnienia wymienione poniżej:
    - Administrator bazy danych — do tworzenia nowych baz danych podczas przywracania
    - Odczyt katalogu — do odczytu z wykazem kopii zapasowych
    - SAP_INTERNAL_HANA_SUPPORT — dostęp do kilku tabel prywatne
2. Dodaje klucz do Hdbuserstore dla platformy HANA wtyczki, aby wykonać wszystkie operacje (zapytanie bazy danych, konfigurowanie kopii zapasowej, tworzy kopie zapasowe, wykonując przywracania)
   
   - Aby potwierdzić Tworzenie klucza, polecenie HDBSQL maszynie HANA przy użyciu poświadczeń SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    Dane wyjściowe polecenia klucza {identyfikator SID} {DBNAME} z użytkownikiem powinien być wyświetlany jako "AZUREWLBACKUPHANAUSER".

> [!NOTE]
> Upewnij się, możesz mieć unikatowe zbiory SSFS plików w ścieżce "/ usr/sap/{SID}/home/.hdb/". Powinien istnieć tylko jeden folder w tej ścieżce.

### <a name="setting-up-backint-parameters"></a>Ustawianie parametrów BackInt

Po wybraniu bazy danych do utworzenia kopii zapasowej usługa Azure Backup skonfiguruje backInt parametrów na poziomie bazy danych.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Upewnij się, że te parametry nie są obecne na poziomie hosta. Parametry na poziomie hosta spowoduje zastąpienie tych parametrów i może spowodować, że inaczej niż oczekiwano.

## <a name="understanding-common-user-errors"></a>Informacje o typowych błędach użytkownika

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można nawiązać połączenia z system.check HANA, który system jest uruchomiona.| Usługa Azure Backup nie jest w stanie połączenia z platformą HANA, ponieważ bazy danych HANA jest wyłączona. Lub HANA jest uruchomiona, ale nie zezwala na połączenie się z usługą Azure Backup | Sprawdź, czy HANA bazy danych/usługi w dół. Jeśli usługi DB HANA jest uruchomiona, sprawdź, czy wszystkie uprawnienia zostały skonfigurowane wspomniano [tutaj](#setting-up-permissions). Jeśli brakuje klucza, należy ponownie uruchomić skrypt wstępnej rejestracji, aby utworzyć nowy klucz. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Wykryto nieprawidłowy Backint konfiguracji. Zatrzymaj ochronę i ponownie skonfigurować bazy danych.| Parametry backInt są niepoprawnie określone dla usługi Azure Backup. | Sprawdź parametry są wymienione [tutaj](#setting-up-backint-parameters). W przypadku parametrów backInt oparte na HOŚCIE, usuń je. Jeśli parametry nie są obecne na HOŚCIE, ale zostały ręcznie zmodyfikowane na poziomie bazy danych, następnie przywróć ich odpowiednie wartości jak wspomniano powyżej. Lub "Zatrzymaj ochronę za pomocą Zachowaj dane" z witryny Azure portal i "wznowienia tworzenia kopii zapasowych" jeszcze raz.|
