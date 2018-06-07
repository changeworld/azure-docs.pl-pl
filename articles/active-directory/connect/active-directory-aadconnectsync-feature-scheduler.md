---
title: 'Synchronizacja programu Azure AD Connect: harmonogram | Dokumentacja firmy Microsoft'
description: W tym temacie opisano funkcję wbudowanych harmonogramu synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fa99de4aac11e7310085cc37e6ebaee441415c61
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594163"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizacja programu Azure AD Connect: harmonogramu
W tym temacie opisano wbudowanych harmonogramu synchronizacji Azure AD Connect () Aparat synchronizacji).

Ta funkcja została wprowadzona z kompilacją 1.1.105.0 (wydane luty 2016).

## <a name="overview"></a>Przegląd
Synchronizacja programu Azure AD Connect zsynchronizować zmiany zachodzące w katalogu lokalnym za pomocą harmonogramu. Istnieją dwa procesy harmonogramu dla synchronizacji haseł i drugi dla obiekt/atrybutu zadań synchronizacji i konserwacji. W tym temacie omówiono te ostatnie.

We wcześniejszych wersjach harmonogramu dla obiektów i atrybutów zostało zewnętrznych do aparatu synchronizacji. Użyć harmonogramu zadań systemu Windows lub osobnych usługi systemu Windows do synchronizacji. Harmonogram jest dzięki wbudowanej wersji 1.1, aby synchronizacja aparat i umożliwienia pewne dostosowania. Nowe domyślna częstotliwość synchronizacji jest 30 minut.

Harmonogram jest odpowiedzialny za dwa zadania:

* **Cykl synchronizacji**. Proces importowania, synchronizacji i eksportowanie zmiany.
* **Zadania konserwacji**. Odnów klucze i certyfikaty służące do resetowania hasła i usługi rejestracji urządzeń (DRS). Przeczyszczać stare wpisy w Dzienniku działań.

Planista się zawsze działa, ale może być skonfigurowana do uruchamiania tylko jednej lub żadna z tych zadań. Na przykład jeśli musisz mieć własnego procesu cyklu synchronizacji, można wyłączyć tego zadania w harmonogramie, ale nadal uruchamiać zadanie konserwacji.

## <a name="scheduler-configuration"></a>Konfiguracja harmonogramu
Aby zobaczyć bieżące ustawienia konfiguracji, przejdź do programu PowerShell i uruchom `Get-ADSyncScheduler`. Przedstawia on podobny do tego obrazu:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Jeśli widzisz **polecenia synchronizacji lub polecenie cmdlet nie jest dostępna** po uruchomieniu tego polecenia cmdlet, następnie modułu programu PowerShell nie jest załadowany. Ten problem może się zdarzyć, jeśli uruchomienie usługi Azure AD Connect na kontrolerze domeny lub na serwerze z wyższego poziomu ograniczeń programu PowerShell niż domyślne ustawienia. Jeśli zostanie wyświetlony ten błąd, uruchom `Import-Module ADSync` udostępnić polecenia cmdlet.

* **AllowedSyncCycleInterval**. Najkrótszy odstęp czasu między cykle synchronizacji dozwolone przez usługę Azure AD. Nie można zsynchronizować częściej niż to ustawienie i nadal obsługiwane.
* **CurrentlyEffectiveSyncCycleInterval**. Aktualnie aktywny harmonogram. Ma taką samą wartość jak CustomizedSyncInterval (Jeśli ustawiona) Jeśli nie jest krótszy niż AllowedSyncInterval. Jeśli używasz kompilacji przed 1.1.281 i zmienić CustomizedSyncCycleInterval, to zmiany zostaną uwzględnione po następnym cyklu synchronizacji. Z kompilacji 1.1.281 zmiany zaczęły obowiązywać natychmiast.
* **CustomizedSyncCycleInterval**. Jeśli chcesz uruchomić inne częstotliwością niż domyślne 30 minut harmonogram, należy skonfigurować to ustawienie. W powyższym rysunku harmonogram został ustawiony należy zamiast tego uruchomić co godzinę. Jeśli to ustawienie na wartość niższa niż AllowedSyncInterval, drugie będzie używana.
* **NextSyncCyclePolicyType**. Delta lub początkowego. Określa, czy przy następnym uruchomieniu należy tylko zmiany różnicowe procesu, lub jeśli przy następnym uruchomieniu, należy wykonać pełny importowania i synchronizacji. Drugie będzie również ponownie przetworzyć żadnych reguł nowe lub zostały zmienione.
* **NextSyncCycleStartTimeInUTC**. Następnym uruchomieniu harmonogramu następnego cyklu synchronizacji.
* **PurgeRunHistoryInterval**. Czas, powinny być przechowywane dzienniki operacji. Te dzienniki mogą być przeglądane na Menedżera usługi synchronizacji. Wartość domyślna to aby zachować te dzienniki 7 dni.
* **SyncCycleEnabled**. Wskazuje, czy harmonogram działa importu, synchronizacji i procesy eksportu w ramach jego operacji.
* **MaintenanceEnabled**. Wskazuje, czy Proces obsługi jest włączony. Aktualizuje certyfikaty/klucze, a Przeczyszcza dziennika operacji.
* **StagingModeEnabled**. Pokazuje Jeśli [Tryb przejściowy](active-directory-aadconnectsync-operations.md#staging-mode) jest włączona. Jeśli to ustawienie jest włączone, następnie go pomija eksportuje uruchamiania, ale nadal uruchamiać importowania i synchronizacji.
* **SchedulerSuspended**. Ustawioną przy użyciu Connect podczas uaktualniania tymczasowo bloku harmonogramu uruchamiania.

Można zmienić niektóre z tych ustawień z `Set-ADSyncScheduler`. Można zmodyfikować następujące parametry:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

W starszych wersjach programu Azure AD Connect **isStagingModeEnabled** luk w ADSyncScheduler zestawu. Jest **nieobsługiwany** Aby ustawić tę właściwość. Właściwość **SchedulerSuspended** powinien zostać zmodyfikowany tylko przez połączenie. Jest **nieobsługiwany** bezpośrednio ustawienie przy użyciu programu PowerShell.

Konfiguracja harmonogramu jest przechowywana w usłudze Azure AD. Jeśli masz serwer tymczasowej zmiany na serwerze podstawowym również wpływa na serwerze (z wyjątkiem IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Składnia: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d — dni, HH - godziny, mm — minuty, ss — sekundy

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Zmienia harmonogram co 3 godziny.

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Zmiany wpływają na codzienne Uruchamianie harmonogramu.

### <a name="disable-the-scheduler"></a>Wyłączanie harmonogramu  
Jeśli chcesz wprowadzić zmiany w konfiguracji, następnie chcesz wyłączyć harmonogram. Na przykład, gdy użytkownik [skonfigurować filtrowanie](active-directory-aadconnectsync-configure-filtering.md) lub [dokonać zmian reguły synchronizacji](active-directory-aadconnectsync-change-the-configuration.md).

Aby wyłączyć harmonogram, uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Wyłączanie harmonogramu](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

Po dokonaniu zmiany, pamiętaj włączyć harmonogram ponownie, podając `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Uruchomiony harmonogram
Harmonogram jest domyślnie uruchamiane co 30 minut. W niektórych przypadkach można wykonać cykl synchronizacji Between zaplanowanego cyklu lub należy uruchomić innego typu.

**Cykl synchronizacji różnicowej**  
Cykl synchronizacji różnicowej obejmuje następujące kroki:

* Import zmian dla wszystkich łączników
* Synchronizacja różnicowa dla wszystkich łączników
* Eksportowanie na wszystkich łączników

Możliwe, że masz pilnych zmiany, która musi być synchronizowany natychmiast, dlatego należy ręcznie uruchomić cykl. Jeśli chcesz ręcznie uruchomić cykl, następnie z programu PowerShell, uruchom `Start-ADSyncSyncCycle -PolicyType Delta`.

**Cykl pełnej synchronizacji**  
Jeśli jeden z następujących zmian konfiguracji zostały wprowadzone, należy uruchomić cykl pełnej synchronizacji () Początkowy):

* Dodano więcej obiektów i atrybutów do zaimportowania z katalogu źródłowego
* Wprowadzone zmiany reguły synchronizacji
* Zmienione [filtrowania](active-directory-aadconnectsync-configure-filtering.md) tak szereg różnych obiektów, należy włączyć

Jeśli jeden z tych zmian, następnie należy wykonać cykl pełnej synchronizacji, więc aparat synchronizacji ma możliwość ponowna przestrzeni łącznika. Cykl Pełna synchronizacja obejmuje następujące kroki:

* Pełny Import dla wszystkich łączników
* Pełna synchronizacja w wszystkich łączników
* Eksportowanie na wszystkich łączników

Aby zainicjować cykl pełną synchronizację, należy uruchomić `Start-ADSyncSyncCycle -PolicyType Initial` w wierszu programu PowerShell. To polecenie uruchamia cyklu pełnej synchronizacji.

## <a name="stop-the-scheduler"></a>Zatrzymania harmonogramu
Harmonogram jest obecnie uruchomiony cykl synchronizacji, może być konieczne Zatrzymaj ją. Na przykład jeśli Uruchom Kreatora instalacji, a ten błąd:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Podczas cyklu synchronizacji jest uruchomiona, nie można wprowadzić zmian w konfiguracji. Użytkownik może poczekaj, aż planista zakończył proces, ale można również zatrzymać go, wprowadź zmiany w natychmiast. Zatrzymywanie bieżącego cyklu nie jest szkodliwe i oczekujących zmian są przetwarzane przy następnym uruchomieniu.

1. Rozpocznij od informuje harmonogramu, aby zatrzymać bieżącego cyklu za pomocą polecenia cmdlet programu PowerShell `Stop-ADSyncSyncCycle`.
2. Jeśli używasz kompilacji przed 1.1.281, następnie zatrzymywanie harmonogramu nie zatrzymuje bieżącego łącznika z jego bieżącego zadania. Aby wymusić łącznika, aby zatrzymać, należy wykonać następujące czynności: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Uruchom **usługi synchronizacji** z start menu. Przejdź do **łączniki**, zaznacz łącznika ze stanem **systemem**i wybierz **zatrzymać** z akcji.

Harmonogram jest nadal aktywna i uruchamia ponownie w następnym możliwości.

## <a name="custom-scheduler"></a>Harmonogram niestandardowy
Polecenia cmdlet, opisane w tej sekcji są dostępne tylko w kompilacji [1.1.130.0](active-directory-aadconnect-version-history.md#111300) i nowszych.

Wbudowane harmonogramu nie spełnia wymagań, można zaplanować łączniki przy użyciu programu PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Można uruchomić profilu dla łącznika w ten sposób:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Nazwy do użycia na potrzeby [nazw łączników](active-directory-aadconnectsync-service-manager-ui-connectors.md) i [nazwy profilu uruchamiania](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) znajdują się w [interfejsu użytkownika Menedżera usługi synchronizacji](active-directory-aadconnectsync-service-manager-ui.md).

![Wywołanie profilu uruchamiania](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` Polecenia cmdlet jest synchroniczne, czyli nie zwraca sterowania aż do zakończenia operacji, łącznik, pomyślnie lub z powodu błędu.

Podczas planowania łączników, zaleca się zaplanować je w następującej kolejności:

1. (Full/Delta) Importuj z katalogów lokalnych, takich jak usługi Active Directory
2. (Full/Delta) Importuj z usługi Azure AD
3. (Full/Delta) Synchronizacja z katalogów lokalnych, takich jak usługi Active Directory
4. (Full/Delta) Synchronizacja z usługą Azure AD
5. Eksportowanie do usługi Azure AD
6. Eksportowanie do katalogów lokalnych, takich jak usługi Active Directory

Porządek ten jest sposób wbudowanych harmonogramu uruchamiania łączników.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Można również monitorować aparatu synchronizacji, aby sprawdzić, czy jest zajęty lub bezczynności. To polecenie cmdlet zwraca żadnego wyniku, jeśli aparat synchronizacji jest w stanie bezczynności i łącznika nie jest uruchomiona. Jeśli łącznik jest uruchomiona, zwraca nazwę łącznika.

```
Get-ADSyncConnectorRunStatus
```

![Stan uruchomienia łącznika](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
W powyższym rysunku pierwszy wiersz jest ze stanu, gdy aparat synchronizacji jest w stanie bezczynności. Drugi wiersz z gdy działa łącznik usługi Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Kreator harmonogramu i instalacji
Po uruchomieniu Kreatora instalacji planista tymczasowo jest wstrzymane. To zachowanie jest, ponieważ zakłada się zmian konfiguracji i nie można zastosować te ustawienia, jeśli aparat synchronizacji jest aktywnie wykonywane. Z tego powodu nie może pozostać Kreatora instalacji Otwórz ponieważ przestaje wykonywać wszystkie akcje synchronizacji aparatu synchronizacji.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
