---
title: Rozwiązywanie problemów podczas tworzenia kopii zapasowych baz danych SAP HANA przy użyciu Azure Backup | Microsoft Docs
description: Opisuje sposób rozwiązywania typowych błędów, które mogą wystąpić podczas tworzenia kopii zapasowej SAP HANA baz danych przy użyciu Azure Backup.
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: pullabhk
ms.openlocfilehash: 221b669c141681749709d6a5a406c78499f21032
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465471"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych baz danych SAP HANA na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W ramach [wymagań wstępnych](backup-azure-sap-hana-database.md#prerequisites)upewnij się, że skrypt rejestracji wstępnej został uruchomiony na maszynie wirtualnej, na której zainstalowano system Hana.

### <a name="setting-up-permissions"></a>Konfigurowanie uprawnień

Co robi skrypt przed rejestracją:

1. Tworzy AZUREWLBACKUPHANAUSER w systemie HANA i dodaje te wymagane role i uprawnienia:
    - Administrator bazy danych: tworzenie nowych baz danych podczas przywracania.
    - Odczyt wykazu: odczytywanie wykazu kopii zapasowych.
    - SAP_INTERNAL_HANA_SUPPORT: Aby uzyskać dostęp do kilku tabel prywatnych.
2. Dodaje klucz do Hdbuserstore dla wtyczki HANA w celu obsługi wszystkich operacji (zapytań bazy danych, operacji przywracania, konfigurowania i uruchamiania kopii zapasowej).
   
   Aby potwierdzić utworzenie klucza, uruchom polecenie HDBSQL na maszynie HANA z poświadczeniami SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    W danych wyjściowych polecenia powinien być wyświetlany klucz {SID} {dbname}, który jest wyświetlany jako AZUREWLBACKUPHANAUSER.

> [!NOTE]
> Upewnij się, że masz unikatowy zestaw plików SSFS w obszarze **/usr/SAP/{SID}/Home/.HDB/** . W tej ścieżce powinien znajdować się tylko jeden folder.

### <a name="setting-up-backint-parameters"></a>Konfigurowanie parametrów BackInt

Po wybraniu bazy danych do wykonania kopii zapasowej usługa Azure Backup konfiguruje parametry backInt na poziomie bazy danych:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout: 7200]

> [!NOTE]
> Upewnij się, że te parametry *nie* występują na poziomie hosta. Parametry na poziomie hosta przesłonią te parametry i mogą spowodować nieoczekiwane zachowanie.

## <a name="common-user-errors"></a>Typowe błędy użytkowników

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można nawiązać połączenia z systemem HANA. Sprawdź, czy system jest uruchomiony.| Usługa Azure Backup nie może połączyć się z platformą HANA, ponieważ baza danych HANA nie działa. Lub HANA jest uruchomiony, ale nie zezwala na łączenie się z usługą Azure Backup. | Sprawdź, czy baza danych lub usługa HANA nie działa. Jeśli baza danych lub usługa HANA jest uruchomiona, sprawdź, czy [zostały ustawione wszystkie uprawnienia](#setting-up-permissions). Jeśli brakuje klucza, uruchom ponownie skrypt rejestracyjny, aby utworzyć nowy klucz. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Wykryto nieprawidłową konfigurację BACKINT. Zatrzymaj ochronę i ponownie skonfiguruj bazę danych.| Parametry backInt są niepoprawnie określone dla Azure Backup. | Sprawdź [, czy parametry są ustawione](#setting-up-backint-parameters). Jeśli na HOŚCIE znajdują się backInt parametry, usuń je. Jeśli na poziomie hosta nie ma parametrów, ale zostały ręcznie zmodyfikowane na poziomie bazy danych, przywróć je do odpowiednich wartości zgodnie z wcześniejszym opisem. Możesz też uruchomić polecenie **Zatrzymaj ochronę i zachować dane kopii zapasowej** z Azure Portal, a następnie wybrać polecenie **Wznów wykonywanie kopii zapasowej**.|
