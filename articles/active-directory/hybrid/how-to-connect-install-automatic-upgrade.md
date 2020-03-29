---
title: 'Usługa Azure AD Connect: automatyczne uaktualnianie | Dokumenty firmy Microsoft'
description: W tym temacie opisano wbudowaną funkcję automatycznego uaktualniania w synchronizacji usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd61b78ca3027ade1f2f48dec33e0a8ed508d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60349848"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatyczne uaktualnianie
Ta funkcja została wprowadzona wraz z kompilacją [1.1.105.0 (wydana w lutym 2016 r.)](reference-connect-version-history.md#111050).  Ta funkcja została zaktualizowana w [kompilacji 1.1.561](reference-connect-version-history.md#115610) i obsługuje teraz dodatkowe scenariusze, które wcześniej nie były obsługiwane.

## <a name="overview"></a>Omówienie
Upewnienie się, że instalacja usługi Azure AD Connect jest zawsze aktualna, nigdy nie była łatwiejsza dzięki funkcji **automatycznego uaktualniania.** Ta funkcja jest domyślnie włączona dla instalacji ekspresowych i uaktualnień dirsync. Po wydaniu nowej wersji instalacja jest automatycznie uaktualniany.
Automatyczne uaktualnianie jest domyślnie włączone dla następujących czynności:

* Instalacja ustawień ekspresowych i uaktualnień DirSync.
* Korzystanie z programu SQL Express LocalDB, czyli tego, czego zawsze używają ustawienia ekspresowe. DirSync z SQL Express również użyć LocalDB.
* Konto USŁUGI AD jest domyślnym kontem MSOL_ utworzonym przez ustawienia expressowe i program DirSync.
* Mają mniej niż 100.000 obiektów w metaverse.

Bieżący stan automatycznego uaktualniania można wyświetlić za `Get-ADSyncAutoUpgrade`pomocą polecenia cmdlet programu PowerShell . Ma następujące stany:

| Stan | Komentarz |
| --- | --- |
| Enabled (Włączony) |Automatyczne uaktualnianie jest włączone. |
| Suspended |Tylko dla systemu. System nie kwalifikuje **się obecnie** do automatycznych uaktualnień. |
| Disabled (Wyłączony) |Automatyczne uaktualnianie jest wyłączone. |

Można zmieniać między **włączone** i `Set-ADSyncAutoUpgrade` **wyłączone** za pomocą . Tylko system powinien ustawić stan **Zawieszony**.  Przed ustawieniem 1.1.750.0 polecenie cmdlet Set-ADSyncAutoUpgrade blokowałby autoupgrade, jeśli stan automatycznego uaktualniania został ustawiony na Zawieszone. Ta funkcja została zmieniona, więc nie blokuje AutoUpgrade.

Automatyczne uaktualnianie jest przy użyciu usługi Azure AD Connect Health dla infrastruktury uaktualniania. Aby automatyczne uaktualnianie do pracy, upewnij się, że zostały otwarte adresy URL na serwerze proxy dla **usługi Azure AD Connect Health,** zgodnie z dokumentami w [adresach URL i zakresach adresów IP usługi Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)


Jeśli interfejs **użytkownika programu Synchronizacja Menedżera usług** jest uruchomiony na serwerze, uaktualnienie jest zawieszone do czasu zamknięcia interfejsu użytkownika.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli instalacja Connect nie uaktualnia się zgodnie z oczekiwaniami, wykonaj następujące kroki, aby dowiedzieć się, co może być nie tak.

Po pierwsze nie należy oczekiwać, że automatyczne uaktualnienie zostanie podjęta pierwszego dnia wydania nowej wersji. Istnieje celowa losowość przed próbą uaktualnienia, więc nie martw się, jeśli instalacja nie zostanie uaktualniona natychmiast.

Jeśli uważasz, że coś jest `Get-ADSyncAutoUpgrade` nie tak, a następnie najpierw uruchomić, aby upewnić się, że automatyczne uaktualnianie jest włączona.

Następnie upewnij się, że w serwerze proxy lub zaporze zostały otwarte wymagane adresy URL. Automatyczna aktualizacja korzysta z usługi Azure AD Connect Health zgodnie z opisem w [przeglądzie](#overview). Jeśli używasz serwera proxy, upewnij się, że health został skonfigurowany do używania [serwera proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Również przetestować [łączność kondycji](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do usługi Azure AD.

Po zweryfikować łączność z usługą Azure AD, nadszedł czas, aby przyjrzeć się dzienników zdarzeń. Uruchom podgląd zdarzeń i poszukaj w dzienniku zdarzeń **aplikacji.** Dodaj filtr eventlogu dla źródłowego **uaktualnienia usługi Azure AD Connect** i zakresu identyfikatorów zdarzeń **300-399**.  
![Filtr Eventlog do automatycznego uaktualniania](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Teraz można zobaczyć dzienniki zdarzeń skojarzone ze stanem automatycznego uaktualniania.  
![Filtr Eventlog do automatycznego uaktualniania](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Kod wyniku ma prefiks z przeglądem stanu.

| Prefiks kodu wyniku | Opis |
| --- | --- |
| Powodzenie |Instalacja została pomyślnie uaktualniona. |
| UpgradeAborted |Tymczasowy stan zatrzymał uaktualnienie. Zostanie ponownie ponowiony i oczekuje się, że uda się później. |
| UpgradeNotSupported |System ma konfigurację, która blokuje automatyczne uaktualnianie systemu. Zostanie on ponowiony, aby sprawdzić, czy stan się zmienia, ale oczekuje się, że system musi zostać uaktualniony ręcznie. |

Oto lista najczęściej spotykanych wiadomości, które można znaleźć. Nie zawiera listy wszystkich, ale komunikat wynik powinien być jasny, z czym jest problem.

| Komunikat o wyniku | Opis |
| --- | --- |
| **UpgradeAborted** | |
| Uaktualnienie AbortedCouldNotSetUpgradeMarker |Nie można zapisać w rejestrze. |
| UpgradeAbortedInsufficientDatabasePermissions |Wbudowana grupa administratorów nie ma uprawnień do bazy danych. Ręcznie uaktualnić do najnowszej wersji usługi Azure AD Connect, aby rozwiązać ten problem. |
| UpgradeAbortedInsufficientDiskSpace |Nie ma wystarczającej ilości miejsca na dysku, aby obsługiwać uaktualnienie. |
| UpgradeAbortedSecurityGroupsNotPresent |Nie można znaleźć i rozwiązać wszystkich grup zabezpieczeń używanych przez aparat synchronizacji. |
| UpgradeAbortedServiceCanNotBeStarted |Nie można uruchomić usługi NT **Usługi Microsoft Azure AD Sync.** |
| UpgradeAbortedServiceCanNotBeStopped |Nie można zatrzymać usługi NT **Usługi Microsoft Azure AD Sync.** |
| UpgradeAbortedServiceIsNotRunning |Usługa NT **Microsoft Azure AD Sync** nie jest uruchomiona. |
| Uaktualnienie AbortedSyncCycleDisabled |Opcja SyncCycle w [harmonogramie](how-to-connect-sync-feature-scheduler.md) została wyłączona. |
| UpgradeAbortedSyncExeInUse |Interfejs [użytkownika menedżera usług synchronizacji](how-to-connect-sync-service-manager-ui.md) jest otwarty na serwerze. |
| UpgradeAbortedSyncOrConfigurationInProgress |Kreator instalacji jest uruchomiony lub synchronizacja została zaplanowana poza harmonogramem. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMetoda | Jako metodę logowania wybrano opcję Adfs. |
| UpgradeNotSupportedCustomizedSyncRules |Dodano własne reguły niestandardowe do konfiguracji. |
| UpgradeNotSupportedDeviceWritebackEnabled |Włączono funkcję [stornia zwrotnego urządzenia.](how-to-connect-device-writeback.md) |
| UpgradeNotSupportedGroupWritebackEnabled |Włączono funkcję [stornia grupy.](how-to-connect-preview.md#group-writeback) |
| UpgradeNotSupportedInvalidPersistedState |Instalacja nie jest ustawieniami ekspresowymi ani uaktualnianiem DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Masz ponad 100.000 obiektów w metaverse. |
| UpgradeNotSupportedMultiForestSetup |Łączysz się z więcej niż jednym lasem. Konfiguracja ekspresowa łączy się tylko z jednym lasem. |
| UpgradeNotSupportedNonLocalDbInstall |Nie używasz bazy danych SQL Server Express LocalDB. |
| Konto UpgradeNotSupportedNonMsol |[Konto łącznika usług AD DS](reference-connect-accounts-permissions.md#ad-ds-connector-account) nie jest już domyślnym kontem MSOL_. |
| UpgradeNotSupportedNotConfiguredSignInMetod | Podczas konfigurowania połączenia AAD connect podczas wybierania metody logowania wybrano opcję *Nie konfiguruj.* |
| UpgradeNotSupportedPtaSignInMetoda | Jako metodę logowania wybrano uwierzytelnianie przekazywane. |
| UpgradeNotSupportedStagingModeEnabled |Serwer jest ustawiony jako w [trybie przejściowym](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Włączono funkcję [stornia zwrotnego użytkownika.](how-to-connect-preview.md#user-writeback) |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
