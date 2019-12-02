---
title: Rozwiązywanie problemów z kopiami zapasowymi baz danych SAP HANA
description: Opisuje sposób rozwiązywania typowych błędów, które mogą wystąpić podczas tworzenia kopii zapasowej SAP HANA baz danych przy użyciu Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e8bb1d3328f95b647a788c53afe3ac1455eefa13
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665342"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych baz danych SAP HANA na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji na SAP HANA obecnie obsługiwane scenariusze tworzenia kopii zapasowych, zobacz temat [Obsługa scenariusza](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Wymagania wstępne i uprawnienia

Przed skonfigurowaniem kopii zapasowych zapoznaj się z sekcjami [wymagania wstępne](tutorial-backup-sap-hana-db.md#prerequisites) i [Ustawienia uprawnień](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

## <a name="common-user-errors"></a>Typowe błędy użytkowników

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Komunikat o błędzie      | <span style="font-weight:normal">Nie można nawiązać połączenia z systemem HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA może nie działać.<br/>Nie są ustawione uprawnienia wymagane dla usługi Azure Backup w celu współdziałania z bazą danych HANA. |
| **Zalecana akcja** | Sprawdź, czy baza danych SAP HANA działa. Jeśli baza danych jest uruchomiona i działa, sprawdź, czy są ustawione wszystkie wymagane uprawnienia. Jeśli brakuje któregoś z uprawnień, uruchom [skrypt rejestracyjny](https://aka.ms/scriptforpermsonhana) , aby dodać brakujące uprawnienia. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Komunikat o błędzie      | <span style="font-weight:normal">Określone wystąpienie SAP HANA jest nieprawidłowe lub nie można go znaleźć</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Nie można utworzyć kopii zapasowej wielu wystąpień SAP HANA na jednej maszynie wirtualnej platformy Azure. |
| **Zalecana akcja** | Uruchom [skrypt rejestracji](https://aka.ms/scriptforpermsonhana) jednokrotnej w wystąpieniu SAP HANA, dla którego chcesz utworzyć kopię zapasową. Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Komunikat o błędzie      | <span style="font-weight:normal">Określona operacja SAP HANA nie jest obsługiwana</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Usługa Azure Backup dla SAP HANA nie obsługuje przyrostowych kopii zapasowych i akcji wykonywanych na SAP HANA natywnych klientów (Studio/Panel sterowania/DBA) |
| **Zalecana akcja** | Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Komunikat o błędzie      | <span style="font-weight:normal">Ta SAP HANA baza danych nie obsługuje żądanego typu kopii zapasowej</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Usługa Azure Backup nie obsługuje przyrostowych kopii zapasowych i kopii zapasowych przy użyciu migawek |
| **Zalecana akcja** | Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Komunikat o błędzie      | <span style="font-weight:normal">Łańcuch dzienników kopii zapasowej został przerwany</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Miejsce docelowe kopii zapasowej dziennika mogło zostać zaktualizowane z BACKINT do systemu plików lub plik wykonywalny BACKINT mógł zostać zmieniony |
| **Zalecana akcja** | Wyzwól pełną kopię zapasową, aby rozwiązać ten problem                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Komunikat o błędzie      | <span style="font-weight:normal">Systemy źródłowe i docelowe na potrzeby przywracania są niezgodne</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | System docelowy przywracania jest niezgodny ze źródłem |
| **Zalecana akcja** | Zapoznaj się z uwagą do [1642148](https://launchpad.support.sap.com/#/notes/1642148) dla oprogramowania SAP, aby dowiedzieć się więcej na temat typów przywracania obsługiwanych dzisiaj |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto uaktualnienie SDC do MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA zostało uaktualnione z SDC do MDC. Kopie zapasowe zakończą się niepowodzeniem po aktualizacji. |
| **Zalecana akcja** | Aby rozwiązać ten problem, wykonaj kroki opisane w [sekcji Uaktualnianie z SAP HANA 1,0 do 2,0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) . |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto nieprawidłową konfigurację BACKINT</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Parametry zapasowe są niepoprawnie określone dla usługi Azure Backup |
| **Zalecana akcja** | Sprawdź, czy są ustawione następujące parametry (BACKINT):<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: false]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Jeśli na HOŚCIE znajdują się BACKINT parametry, usuń je. Jeśli parametry nie są dostępne na poziomie hosta, ale zostały ręcznie zmodyfikowane na poziomie bazy danych, przywróć je do odpowiednich wartości zgodnie z wcześniejszym opisem. Możesz też uruchomić polecenie [Zatrzymaj ochronę i zachować dane kopii zapasowej](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) z Azure Portal, a następnie wybrać polecenie **Wznów wykonywanie kopii zapasowej**. |

## <a name="restore-checks"></a>Testy przywracania

### <a name="single-container-database-sdc-restore"></a>Przywracanie bazy danych z jednym kontenerem (SDC)

Weź pod uwagę dane wejściowe podczas przywracania pojedynczej bazy danych kontenerów (SDC) dla platformy HANA na inną maszynę SDC. Nazwa bazy danych powinna być połączona małymi literami z literą "SDC" w nawiasach. Wystąpienie HANA będzie wyświetlane w Wielkiej litery.

Załóżmy, że utworzono kopię zapasową wystąpienia SDC HANA "H21". Na stronie elementy kopii zapasowej będzie wyświetlana nazwa elementu kopii zapasowej jako **"H21 (SDC)"** . Jeśli próbujesz przywrócić tę bazę danych do innego obiektu docelowego SDC, powiedz H11, a następnie należy podać następujące dane wejściowe.

![SDC przywracania danych wejściowych](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Pamiętaj o następujących kwestiach:

- Domyślnie przywrócona Nazwa bazy danych zostanie uzupełniona nazwą elementu kopii zapasowej, np., H21 (SDC)
- Wybranie elementu docelowego jako H11 nie spowoduje automatycznej zmiany przywróconej nazwy bazy danych. **Powinien być edytowany w H11 (SDC)** . W odniesieniu do SDC nazwa przywróconej bazy danych będzie IDENTYFIKATORem wystąpienia docelowego z małymi literami i "SDC" dołączonym w nawiasach.
- Ponieważ SDC może mieć tylko jedną bazę danych, należy również kliknąć pole wyboru, aby umożliwić przesłonięcie istniejących danych bazy danych z użyciem danych punktu odzyskiwania.
- W systemie Linux jest rozróżniana wielkość liter. W związku z tym należy zachować ostrożność.

### <a name="multiple-container-database-mdc-restore"></a>Przywracanie wielu baz danych (MDC)

W przypadku wielu baz danych kontenerów dla platformy HANA Standardowa konfiguracja to SYSTEMDB + 1 lub więcej baz danych dzierżawców. Przywrócenie całego wystąpienia SAP HANA oznacza przywrócenie zarówno SYSTEMDB, jak i baz danych dzierżawcy. Najpierw przywraca SYSTEMDB, a następnie przechodzi do bazy danych dzierżawcy. Zasadniczo systemowa baza danych zastępuje informacje o systemie w wybranym miejscu docelowym. To przywracanie przesłania również informacje dotyczące BackInt w wystąpieniu docelowym. W związku z tym po przywróceniu bazy danych systemu do wystąpienia docelowego należy ponownie uruchomić skrypt przed rejestracją. Kolejne Przywracanie bazy danych dzierżawy zakończy się powodzeniem.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Uaktualnianie z SAP HANA 1,0 do 2,0

Jeśli chronisz bazy danych SAP HANA 1,0 i chcesz uaktualnić do wersji 2,0, wykonaj czynności opisane poniżej:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z zachowaniem zachowania danych dla starej bazy danych SDC.
- Uruchom ponownie [skrypt poprzedzający rejestrację](https://aka.ms/scriptforpermsonhana) z prawidłowymi szczegółami (SID i MDC).
- Ponownie zarejestruj rozszerzenie (szczegóły widoku > kopii zapasowej — > wybierz odpowiednią pozycję Azure VM-> reregister).
- Kliknij przycisk ponownie odkryj baz danych dla tej samej maszyny wirtualnej. Ta akcja powinna spowodować wyświetlenie nowego baz danych w kroku 2 z prawidłowymi szczegółami (SYSTEMDB i dzierżawcą bazy danych, a nie SDC).
- Chroń te nowe bazy danych.

## <a name="upgrading-without-an-sid-change"></a>Uaktualnianie bez zmiany identyfikatora SID

Uaktualnienia do systemu operacyjnego lub SAP HANA, które nie powodują zmiany identyfikatora SID, można obsłużyć, jak opisano poniżej:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z zachowaniem zachowania danych dla bazy danych
- Uruchom ponownie [skrypt przed rejestracją](https://aka.ms/scriptforpermsonhana)
- Ponownie [Wznów ochronę](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) bazy danych

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [często zadawane pytania](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) dotyczące tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure]
