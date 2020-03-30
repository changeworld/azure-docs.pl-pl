---
title: Rozwiązywanie problemów z błędami kopii zapasowej baz danych SAP HANA
description: W tym artykule opisano, jak rozwiązywać typowe błędy, które mogą wystąpić podczas tworzenia kopii zapasowej usługi Azure Backup w celu utworzenia kopii zapasowej baz danych SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459314"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych baz danych SAP HANA na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z tworzeniem kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji na temat scenariuszy tworzenia kopii zapasowych SAP HANA, które obecnie obsługujemy, zobacz [Obsługa scenariuszy](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Wymagania wstępne i uprawnienia

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i [Co skrypt rejestracji wstępnej robi](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) sekcje przed skonfigurowaniem kopii zapasowych.

## <a name="common-user-errors"></a>Typowe błędy użytkownika

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Komunikat o błędzie**      | <span style="font-weight:normal">Tworzenie kopii zapasowej platformy Azure nie ma wymaganych uprawnień roli do wykonywania kopii zapasowej</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Rola mogła zostać zastąpiona.                          |
| **Zalecana akcja** | Aby rozwiązać ten problem, uruchom skrypt z okienka **Discover DB** lub pobierz go [tutaj](https://aka.ms/scriptforpermsonhana). Alternatywnie dodaj rolę "SAP_INTERNAL_HANA_SUPPORT" do użytkownika kopii zapasowej obciążenia (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Komunikat o błędzie      | <span style="font-weight:normal">Nie można połączyć się z systemem HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA może być w dół.<br/>Nie ustawiono wymaganych uprawnień do tworzenia kopii zapasowych platformy Azure do interakcji z bazą danych HANA. |
| **Zalecana akcja** | Sprawdź, czy baza danych SAP HANA jest w górę. Jeśli baza danych jest uruchomiona, sprawdź, czy są ustawione wszystkie wymagane uprawnienia. Jeśli brakuje któregokolwiek z uprawnień, uruchom [skrypt rejestracji wstępnej,](https://aka.ms/scriptforpermsonhana) aby dodać brakujące uprawnienia. |

### <a name="usererrorhanainstancenameinvalid"></a>UżytkownikErrorHanaInstanceNameInvalid

| Komunikat o błędzie      | <span style="font-weight:normal">Określone wystąpienie SAP HANA jest nieprawidłowe lub nie można go odnaleźć</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Nie można wyw. |
| **Zalecana akcja** | Uruchom [skrypt rejestracji wstępnej](https://aka.ms/scriptforpermsonhana) w wystąpieniu SAP HANA, którego chcesz zrobić, aby uzyskać jego utworzenie kopii zapasowej. Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Komunikat o błędzie      | <span style="font-weight:normal">Określona operacja SAP HANA nie jest obsługiwana</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Tworzenie kopii zapasowej platformy Azure dla systemu SAP HANA nie obsługuje przyrostowej kopii zapasowej i akcji wykonywanych na klientach natywnych SAP HANA (Kokpit Studio/ Kokpit/ DBA) |
| **Zalecana akcja** | Aby uzyskać więcej informacji, zapoznaj się [z tym tutaj](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Komunikat o błędzie      | <span style="font-weight:normal">Ta baza danych SAP HANA nie obsługuje żądanego typu kopii zapasowej</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Tworzenie kopii zapasowej platformy Azure nie obsługuje przyrostowej kopii zapasowej i tworzenia kopii zapasowych przy użyciu migawek |
| **Zalecana akcja** | Aby uzyskać więcej informacji, zapoznaj się [z tym tutaj](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UżytkownikErrorHANALSNValidationFailure

| Komunikat o błędzie      | <span style="font-weight:normal">Łańcuch dziennika kopii zapasowej jest uszkodzony</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Miejsce docelowe kopii zapasowej dziennika mogło zostać zaktualizowane z zaplecza do systemu plików lub plik wykonywalny backint mógł zostać zmieniony |
| **Zalecana akcja** | Wyzwalanie pełnej kopii zapasowej w celu rozwiązania problemu                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UżytkownikErrorIncomaptibleSrcTargetSystsemsForRestore

| Komunikat o błędzie      | <span style="font-weight:normal">Systemy źródłowe i docelowe do przywracania są niezgodne</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Docelowy system przywracania jest niezgodny ze źródłem |
| **Zalecana akcja** | Zapoznaj się z notatką SAP [1642148,](https://launchpad.support.sap.com/#/notes/1642148) aby dowiedzieć się więcej o obsługiwanych obecnie typach przywracania |

### <a name="usererrorsdctomdcupgradedetected"></a>UżytkownikErrorSDCtoMDCUpgradeDetected

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto uaktualnienie SDC do MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA zostało uaktualnione z sdc do mdc. Kopie zapasowe nie powiedzie się po aktualizacji. |
| **Zalecana akcja** | Postępuj zgodnie z instrukcjami podanymi w [sekcji Uaktualnianie z sap HANA 1.0 do 2.0,](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) aby rozwiązać problem |

### <a name="usererrorinvalidbackintconfiguration"></a>Konfiguracja użytkownikaErrorInvalidBackintConfiguracja

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto nieprawidłową konfigurację backint</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Parametry kopii zapasowej są niepoprawnie określone dla kopii zapasowej platformy Azure |
| **Zalecana akcja** | Sprawdź, czy są ustawione następujące parametry (backint):<br/>\*[catalog_backup_using_backint:true]<br/>\*[enable_accumulated_catalog_backup:false]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Jeśli parametry oparte na backint są obecne w hostze, usuń je. Jeśli parametry nie są obecne na poziomie hosta, ale zostały ręcznie zmodyfikowane na poziomie bazy danych, należy przywrócić je do odpowiednich wartości, jak opisano wcześniej. Możesz też uruchomić [ochronę zatrzymania i zachować dane kopii zapasowej](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) z witryny Azure portal, a następnie wybrać pozycję **Wznów kopię zapasową**. |

## <a name="restore-checks"></a>Przywracanie kontroli

### <a name="single-container-database-sdc-restore"></a>Przywracanie bazy danych pojedynczego kontenera (SDC)

Zadbaj o dane wejściowe podczas przywracania bazy danych pojedynczego kontenera (SDC) dla hana do innego komputera SDC. Nazwa bazy danych powinna być podana za pomocą małych liter i z "sdc" dołączone w nawiasach. Wystąpienie HANA będzie wyświetlane w stolicach.

Załóżmy, że sdc hana wystąpienie "H21" jest kopia zapasowa. Na stronie elementów kopii zapasowej zostanie wyświetlona nazwa elementu kopii zapasowej jako **"h21(sdc)"**. Jeśli spróbujesz przywrócić tę bazę danych do innego docelowego sdc, powiedzmy H11, a następnie następujące dane wejściowe muszą być dostarczone.

![Przywrócona nazwa bazy danych SDC](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Pamiętaj o następujących kwestiach:

- Domyślnie przywrócona nazwa bazy danych zostanie wypełniona nazwą elementu kopii zapasowej. W tym przypadku h21(sdc).
- Wybranie obiektu docelowego jako H11 NIE spowoduje automatycznej zmiany przywróconej nazwy bazy danych. **Powinien być edytowany do h11(sdc)**. W odniesieniu do SDC przywrócona nazwa bazy danych będzie identyfikatorem wystąpienia docelowego z literami małych liter i "sdc" dołączanym w nawiasach.
- Ponieważ kontroler SDC może mieć tylko jedną bazę danych, należy również kliknąć to pole wyboru, aby zezwolić na zastąpienie istniejących danych bazy danych danymi punktu odzyskiwania.
- W systemie Linux rozróżniana jest wielkość liter. Więc uważaj, aby zachować sprawę.

### <a name="multiple-container-database-mdc-restore"></a>Przywracanie bazy danych wielu kontenerów (MDC)

W wielu bazach danych kontenerów dla hana standardowa konfiguracja jest SYSTEMDB + 1 lub więcej bloków DBs dzierżawy. Przywracanie całego wystąpienia SAP HANA oznacza przywrócić zarówno SYSTEMDB i dzierżawy DBs. Jeden przywraca systemdb pierwszy, a następnie przechodzi do dzierżawy DB. Baza danych systemu zasadniczo oznacza zastąpienie informacji o systemie na wybranym celu. To przywracanie zastępuje również informacje związane z backint w wystąpieniu docelowym. Tak więc po przywróceniu bazy danych systemu do wystąpienia docelowego uruchom ponownie skrypt rejestracji wstępnej. Tylko wtedy kolejne przywraca bazy danych dzierżawy zakończy się pomyślnie.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Aktualizacja z SAP HANA 1.0 do 2.0

Jeśli chronisz bazy danych SAP HANA 1.0 i chcesz uaktualnić do 2.0, wykonaj następujące kroki:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z zachowaniem danych dla starej bazy danych SDC.
- Wykonaj uaktualnienie. Po zakończeniu, HANA jest teraz MDC z systemem DB i dzierżawy DB (s)
- Uruchom ponownie [skrypt rejestracji wstępnej](https://aka.ms/scriptforpermsonhana) z poprawnymi szczegółami (sid i mdc).
- Ponownie zarejestruj rozszerzenie dla tego samego komputera w witrynie Azure portal (Backup -> wyświetlić szczegóły -> Wybierz odpowiednią maszynę wirtualną platformy Azure -> ponownie zarejestrować).
- Kliknij przycisk Odkryj ponownie na nowo dla tej samej maszyny Wirtualnej. Ta akcja powinna pokazać nowe DB w kroku 2 z poprawnymi szczegółami (SYSTEMDB i Db dzierżawy, a nie SDC).
- Skonfiguruj kopię zapasową dla tych nowych baz danych.

## <a name="upgrading-without-an-sid-change"></a>Uaktualnianie bez zmiany identyfikatora SID

Uaktualnienia do systemu operacyjnego lub SAP HANA, które nie powodują zmiany identyfikatora SID, mogą być obsługiwane zgodnie z poniższymi opisami:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z zachowaniem danych dla bazy danych
- Wykonaj uaktualnienie.
- Uruchom ponownie [skrypt rejestracji wstępnej](https://aka.ms/scriptforpermsonhana). Zazwyczaj widzieliśmy proces uaktualniania usuwa niezbędne role. Uruchomienie skryptu rejestracji wstępnej pomoże zweryfikować wszystkie wymagane role.
- [Ponowne wznowienie ochrony](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) bazy danych

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Przed uruchomieniem operacji ponownego rejestrowania należy sprawdzić co najmniej jeden z następujących objawów:

- Wszystkie operacje (takie jak tworzenie kopii zapasowych, przywracanie i konfigurowanie kopii zapasowej) kończą się niepowodzeniem na maszynie Wirtualnej z jednym z następujących kodów błędów: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Jeśli obszar **Stan kopii zapasowej** elementu kopii zapasowej jest wyświetlany jako **niedoścignialny,** wyklucz wszystkie inne przyczyny, które mogą spowodować ten sam stan:

  - Brak uprawnień do wykonywania operacji związanych z tworzeniem kopii zapasowych na maszynie Wirtualnej
  - Maszyna wirtualna jest zamykana, więc kopie zapasowe nie mogą mieć miejsca
  - Problemy z siecią

Objawy te mogą wystąpić z co najmniej jednego z następujących powodów:

- Rozszerzenie zostało usunięte lub odinstalowane z portalu.
- Maszyna wirtualna została przywrócona w czasie za pomocą przywracania dysku w miejscu.
- Maszyna wirtualna została zamknięta na dłuższy okres, więc konfiguracja rozszerzenia na nim wygasła.
- Maszyna wirtualna została usunięta, a inna maszyna wirtualna została utworzona o tej samej nazwie i w tej samej grupie zasobów co usunięta maszyna wirtualna.

W poprzednich scenariuszach zaleca się wyzwolenie operacji ponownego rejestrowania na maszynie Wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [często zadawanymi pytaniami](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) dotyczącymi tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.
