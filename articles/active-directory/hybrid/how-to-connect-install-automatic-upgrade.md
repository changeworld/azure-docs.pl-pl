---
title: 'Program Azure AD Connect: Automatyczne uaktualnianie | Dokumentacja firmy Microsoft'
description: W tym temacie opisano wbudowanej funkcji automatycznego uaktualniania w synchronizacji programu Azure AD Connect.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60349848"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Program Azure AD Connect: Automatycznie uaktualnianie
Ta funkcja została wprowadzona z kompilacją [1.1.105.0 (wydane w lutym 2016 r.)](reference-connect-version-history.md#111050).  Ta funkcja została zaktualizowana w [kompilacji 1.1.561](reference-connect-version-history.md#115610) i teraz obsługuje dodatkowe scenariusze, które wcześniej nie są obsługiwani.

## <a name="overview"></a>Omówienie
Zagwarantowanie, że instalacji programu Azure AD Connect jest zawsze pozostają aktualne jest łatwiejsze niż dotychczas dzięki **automatyczne uaktualnianie** funkcji. Ta funkcja jest włączona domyślnie w przypadku instalacji ekspresowej, a uaktualnienie narzędzia DirSync. Po wydaniu nowej wersji instalacji zostaje automatycznie uaktualnione.
Automatyczne uaktualnianie jest domyślnie włączona, do wykonania poniższych czynności:

* Podczas tworzenia ekspresowych ustawienia instalacji i uaktualniania narzędzia DirSync.
* Za pomocą programu SQL Express LocalDB, jest to, czego ustawień ekspresowych zawsze używać. Narzędzie DirSync z programu SQL Express również użyć programu LocalDB.
* Konto usługi AD jest domyślnym kontem MSOL_ utworzonych przez ustawień ekspresowych oraz narzędzia DirSync.
* Masz mniej niż 100 000 obiektów w magazynie metaverse.

Bieżący stan automatycznego uaktualniania można wyświetlić za pomocą polecenia cmdlet programu PowerShell `Get-ADSyncAutoUpgrade`. Obowiązują następujące stany:

| Stan | Komentarz |
| --- | --- |
| Enabled (Włączony) |Automatyczne uaktualnianie jest włączona. |
| Wstrzymane |Ustaw tylko w systemie. System jest **nie jest obecnie** prawo do otrzymania automatycznych uaktualnień. |
| Disabled (Wyłączony) |Automatyczne uaktualnianie jest wyłączona. |

Można zmieniać **włączone** i **wyłączone** z `Set-ADSyncAutoUpgrade`. Tylko do systemu, należy ustawić stan **zawieszone**.  Przed 1.1.750.0 polecenia cmdlet Set-ADSyncAutoUpgrade mogłyby spowodować zablokowanie Autoupgrade Jeśli ustawiono stan automatyczną aktualizację zawieszone. Ta funkcja zostanie zmieniona tak AutoUpgrade nie są blokowane.

Automatyczne uaktualnianie jest przy użyciu programu Azure AD Connect Health dla uaktualnienie infrastruktury. Aby uzyskać automatyczne uaktualnienie do pracy, upewnij się, adresy URL został otwarty na serwerze proxy dla **programu Azure AD Connect Health** zgodnie z opisem w [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Jeśli **Menedżera usługi synchronizacji** interfejsu użytkownika jest uruchomiona na serwerze, a następnie uaktualnienia jest zawieszona do czasu zamknięcia interfejsu użytkownika.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
W przypadku instalacji programu Connect nie uaktualnia się sam zgodnie z oczekiwaniami, należy wykonać następujące kroki, aby dowiedzieć się, co może być nieprawidłowa.

Po pierwsze nie powinno automatycznego uaktualniania, aby podjąć próbę pierwszy dzień, w których wydaniu nowej wersji. Przed próbą uaktualnienia, dzięki czemu nie przejmować, jeśli instalacja nie jest od razu uaktualnienia jest losowości zamierzone.

Jeśli Twoim zdaniem coś, co nie jest odpowiednia, najpierw uruchom `Get-ADSyncAutoUpgrade` zapewnienie jest włączone automatyczne uaktualnianie.

Następnie upewnij się, że został otwarty wymaganych adresów URL serwera proxy lub zapory. Automatyczna aktualizacja jest przy użyciu programu Azure AD Connect Health, zgodnie z opisem w [Przegląd](#overview). Jeśli używasz serwera proxy, upewnij się, kondycji został skonfigurowany do użycia [serwera proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Również przetestować [łączności kondycji](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do usługi Azure AD.

Łączność z usługą Azure AD zweryfikować nadszedł czas na możliwość przejrzenia elementami EventLog. Uruchom Podgląd zdarzeń i Znajdź **aplikacji** dziennika zdarzeń. Dodaj filtr dziennika zdarzeń dla źródła **usługi Azure AD Connect uaktualnianie** i zakres identyfikatorów zdarzeń **300-399**.  
![W dzienniku zdarzeń filtr dla uaktualnienie automatyczne](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Teraz widać elementami EventLog skojarzony stan dla uaktualnienie automatyczne.  
![W dzienniku zdarzeń filtr dla uaktualnienie automatyczne](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Kod wyniku prefiksie przegląd stanu.

| Prefiks kodu wyniku | Opis |
| --- | --- |
| Powodzenie |Instalacja została pomyślnie uaktualniona. |
| UpgradeAborted |Stan tymczasowy zatrzymane uaktualnienia. Zostanie ona ponownie ponowione i oczekuje się, że jego powodzenia później. |
| UpgradeNotSupported |System ma konfiguracji, który blokuje przez system automatycznie uaktualniany. Będzie można ponowić aby zobaczyć, jeśli stan jest zmieniany, ale oczekuje się, że system musi zostać uaktualniony ręcznie. |

Poniżej przedstawiono listę najbardziej typowych komunikatów, które znajdujesz. Nie wyświetla listy wszystkich, ale komunikat o wyniku powinna być jasno jaki problem jest.

| Komunikat o wyniku | Opis |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nie można zapisać do rejestru. |
| UpgradeAbortedInsufficientDatabasePermissions |Wbudowanej grupy Administratorzy nie ma uprawnień do bazy danych. Ręcznie Uaktualnij do najnowszej wersji programu Azure AD Connect, aby rozwiązać ten problem. |
| UpgradeAbortedInsufficientDiskSpace |Nie ma wystarczającej ilości miejsca na dysku do obsługi uaktualnienia. |
| UpgradeAbortedSecurityGroupsNotPresent |Nie można odnaleźć i rozwiązać wszystkie grupy zabezpieczeń używanego przez aparat synchronizacji. |
| UpgradeAbortedServiceCanNotBeStarted |Usługa NT **Microsoft Azure AD Sync** uruchomienie nie powiodło się. |
| UpgradeAbortedServiceCanNotBeStopped |Usługa NT **Microsoft Azure AD Sync** udało się zatrzymać. |
| UpgradeAbortedServiceIsNotRunning |Usługa NT **Microsoft Azure AD Sync** nie jest uruchomiona. |
| UpgradeAbortedSyncCycleDisabled |Opcja SyncCycle w [harmonogramu](how-to-connect-sync-feature-scheduler.md) została wyłączona. |
| UpgradeAbortedSyncExeInUse |[Interfejs użytkownika Menedżera usługi synchronizacji](how-to-connect-sync-service-manager-ui.md) jest otwarty na serwerze. |
| UpgradeAbortedSyncOrConfigurationInProgress |Kreator instalacji jest uruchomiony lub synchronizacji zostało zaplanowane poza harmonogramu. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Jako metodę logowania wybrano usług AD FS. |
| UpgradeNotSupportedCustomizedSyncRules |Własne reguły niestandardowe zostały dodane do konfiguracji. |
| UpgradeNotSupportedDeviceWritebackEnabled |Włączono [zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md) funkcji. |
| UpgradeNotSupportedGroupWritebackEnabled |Włączono [zapisu zwrotnego grup](how-to-connect-preview.md#group-writeback) funkcji. |
| UpgradeNotSupportedInvalidPersistedState |Instalacja nie jest ustawień ekspresowych lub uaktualnienie narzędzia DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Masz ponad 100 000 obiektów w magazynie metaverse. |
| UpgradeNotSupportedMultiForestSetup |Łączysz się do więcej niż jednym lesie. Instalacja ekspresowa tylko łączy się z jednego lasu. |
| UpgradeNotSupportedNonLocalDbInstall |Nie używasz bazy danych programu SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |[Konta usługi AD DS łącznika](reference-connect-accounts-permissions.md#ad-ds-connector-account) nie jest domyślnym kontem MSOL_ już. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Podczas konfigurowania program AAD Connect, możesz wybrać *nie należy konfigurować* podczas wybierania metody logowania jednokrotnego. |
| UpgradeNotSupportedPtaSignInMethod | Jako metodę logowania wybrano uwierzytelnianie przekazywane. |
| UpgradeNotSupportedStagingModeEnabled |Serwer jest ustawiony w [Tryb przejściowy](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Włączono [zapis zwrotny użytkowników](how-to-connect-preview.md#user-writeback) funkcji. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
