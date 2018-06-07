---
title: 'Azure AD Connect: Automatyczne uaktualnianie | Dokumentacja firmy Microsoft'
description: W tym temacie opisano wbudowanych funkcji automatycznego uaktualniania synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: adfce1195a6a322e0225ee09a45148001d0167c8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592133"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatyczne uaktualnianie
Ta funkcja została wprowadzona z kompilacją 1.1.105.0 (wydane luty 2016).

## <a name="overview"></a>Przegląd
Zapewnienie instalacji programu Azure AD Connect jest zawsze aktualny jest wyjątkowo proste z **automatyczne uaktualnianie** funkcji. Ta funkcja jest włączona domyślnie w przypadku instalacji ekspresowej, a uaktualnienie narzędzia DirSync. Po wydaniu nowej wersji instalacji zostaje automatycznie uaktualnione.
Automatyczne uaktualnianie jest domyślnie włączona dla następujących czynności:

* Express ustawienia instalacji i uaktualnienia narzędzia DirSync.
* Za pomocą programu SQL Express LocalDB, czyli co ustawień ekspresowych zawsze używać. Narzędzie DirSync z programu SQL Express również użyć LocalDB.
* Konto usługi AD jest domyślnym kontem MSOL_ tworzone przez ustawień ekspresowych i narzędzia DirSync.
* Masz mniej niż 100 000 obiektów w magazynie metaverse.

Bieżący stan automatycznego uaktualniania można wyświetlić za pomocą polecenia cmdlet programu PowerShell `Get-ADSyncAutoUpgrade`. Wybrano następujące stany:

| Stan | Komentarz |
| --- | --- |
| Enabled (Włączony) |Włączono automatyczną aktualizację. |
| Zawieszone |Ustaw tylko w systemie. System nie jest już przysługiwać automatycznych uaktualnień. |
| Disabled (Wyłączony) |Automatyczne uaktualnianie jest wyłączona. |

Można przełączać się między **włączone** i **wyłączone** z `Set-ADSyncAutoUpgrade`. Tylko system, należy ustawić stan **zawieszone**.

Automatyczne uaktualnianie używa usługi Azure AD Connect Health dla uaktualnienia infrastruktury. Do automatycznego uaktualnienia do pracy, upewnij się, że adresy URL został otwarty na serwerze proxy dla **Azure AD Connect Health** zgodnie z opisem w [zakresów adresów IP i URL usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Jeśli **Menedżera usługi synchronizacji** interfejsu użytkownika jest uruchomiona na serwerze, a następnie Uaktualnianie jest wstrzymane do czasu zamknięcia interfejsu użytkownika.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli instalacji Connect nie uaktualnia się sam zgodnie z oczekiwaniami, postępuj te kroki, aby dowiedzieć się, co może być nieprawidłowa.

Należy najpierw nie oczekiwać automatyczne uaktualnianie, aby podjąć pierwszy dzień, który wydana nowa wersja. Jest zamierzone losowości przed próbą uaktualnienia, więc nie alarmed instalacji nie jest od razu uaktualnienia.

Jeśli uważasz, że nie jest element w prawo, najpierw uruchom `Get-ADSyncAutoUpgrade` zapewnienie włączone jest automatyczne uaktualnianie.

Następnie upewnij się, że odpowiednie adresy URL został otwarty w użyciu serwera proxy lub zapory. Automatyczna aktualizacja jest przy użyciu usługi Azure AD Connect Health, zgodnie z opisem w [omówienie](#overview). Jeśli używasz serwera proxy, upewnij się, kondycji został skonfigurowany do używania [serwera proxy](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Także przetestować [łączności kondycji](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do usługi Azure AD.

W przypadku łączności z usługą Azure AD zweryfikować z nadszedł czas do elementami EventLog. Uruchom Podgląd zdarzeń i Znajdź **aplikacji** eventlog. Dodaj filtr eventlog źródła **Azure AD Connect uaktualnienia** i zakres identyfikatora zdarzenia **300 399**.  
![Filtr dziennika zdarzeń dla automatycznego uaktualniania](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Możesz teraz przeglądać elementami Eventlog, skojarzone ze stanem dla automatycznego uaktualniania.  
![Filtr dziennika zdarzeń dla automatycznego uaktualniania](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Kod wyniku ma prefiks przegląd stanu.

| Prefiks kod wyniku | Opis |
| --- | --- |
| Powodzenie |Pomyślnie uaktualniono instalacji. |
| UpgradeAborted |Tymczasowy warunek zatrzymana uaktualnienia. Zostanie ona ponownie ponowione i oczekuje się, czy próba powiedzie się później. |
| UpgradeNotSupported |System ma konfigurację, która blokuje system z uaktualniany automatycznie. Zostanie zostać powtórzone, aby zobaczyć, jeśli stan jest zmieniany, ale oczekuje się, że system musi zostać uaktualnione ręcznie. |

W tym miejscu znajduje się lista typowych wiadomości, które można znaleźć. Nie wyświetla listy wszystkich, ale komunikat wynikowy powinien być wyczyść o jakie problem.

| Komunikat o wyniku | Opis |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nie można zapisać w rejestrze. |
| UpgradeAbortedInsufficientDatabasePermissions |Grupy wbudowanych administratorów nie ma uprawnień do bazy danych. Przeprowadź ręczne uaktualnienie do najnowszej wersji programu Azure AD Connect, aby rozwiązać ten problem. |
| UpgradeAbortedInsufficientDiskSpace |Nie jest wystarczająco dużo miejsca na dysku do obsługi uaktualnienia. |
| UpgradeAbortedSecurityGroupsNotPresent |Nie można odnaleźć i rozwiązać wszystkie grupy zabezpieczeń używane przez aparat synchronizacji. |
| UpgradeAbortedServiceCanNotBeStarted |Usługa NT **Microsoft Azure AD Sync** uruchomienie nie powiodło się. |
| UpgradeAbortedServiceCanNotBeStopped |Usługa NT **Microsoft Azure AD Sync** nie można zatrzymać. |
| UpgradeAbortedServiceIsNotRunning |Usługa NT **Microsoft Azure AD Sync** nie jest uruchomiona. |
| UpgradeAbortedSyncCycleDisabled |Opcja SyncCycle w [harmonogramu](active-directory-aadconnectsync-feature-scheduler.md) została wyłączona. |
| UpgradeAbortedSyncExeInUse |[Interfejs użytkownika Menedżera usługi synchronizacji](active-directory-aadconnectsync-service-manager-ui.md) jest otwarty na serwerze. |
| UpgradeAbortedSyncOrConfigurationInProgress |Kreator instalacji jest uruchomiony lub synchronizacji zostało zaplanowane poza harmonogramu. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Jako metodę logowania wybrano usług AD FS. | 
| UpgradeNotSupportedCustomizedSyncRules |Dodano własne reguły niestandardowe dla konfiguracji. |
| UpgradeNotSupportedDeviceWritebackEnabled |Włączono [zapisu zwrotnego urządzeń](active-directory-aadconnect-feature-device-writeback.md) funkcji. |
| UpgradeNotSupportedGroupWritebackEnabled |Włączono [zapisu zwrotnego grup](active-directory-aadconnect-feature-preview.md#group-writeback) funkcji. |
| UpgradeNotSupportedInvalidPersistedState |Instalacja nie jest ustawień ekspresowych lub uaktualnienie narzędzia DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Masz więcej niż 100 000 obiektów w magazynie metaverse. |
| UpgradeNotSupportedMultiForestSetup |Łączysz się z więcej niż jednego lasu. Instalacja ekspresowa tylko łączy się z jednego lasu. |
| UpgradeNotSupportedNonLocalDbInstall |Nie używasz bazy danych programu SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |[Konta łącznika AD](active-directory-aadconnect-accounts-permissions.md#active-directory-account) nie jest domyślnym kontem MSOL_ już. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Podczas konfigurowania AAD Connect, możesz wybrać *nieskonfigurowane* podczas wybierania metody logowania jednokrotnego. | 
| UpgradeNotSupportedPtaSignInMethod | Jako metodę logowania wybrano uwierzytelniania przekazywanego. |
| UpgradeNotSupportedStagingModeEnabled |Serwer jest ustawiona w [Tryb przejściowy](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Włączono [zapisu zwrotnego użytkowników](active-directory-aadconnect-feature-preview.md#user-writeback) funkcji. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
