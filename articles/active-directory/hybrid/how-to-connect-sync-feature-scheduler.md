---
title: 'Synchronizacja programu Azure AD Connect: Usługa Scheduler | Dokumentacja firmy Microsoft'
description: W tym temacie opisano funkcję wbudowanych harmonogramu w synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d5f4dec48d81b032de293bb6c68ad62ac48d475
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347927"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizacja programu Azure AD Connect: Scheduler
W tym temacie opisano wbudowanych harmonogramu synchronizacji Azure AD Connect (zwany również aparatem synchronizacji).

Ta funkcja została wprowadzona z kompilacją 1.1.105.0 (wydane w lutym 2016 r.).

## <a name="overview"></a>Omówienie
Synchronizacja programu Azure AD Connect synchronizować zmiany zachodzące w Twoim katalogu w środowisku lokalnym przy użyciu harmonogramu. Istnieją dwa procesy harmonogramu dla synchronizacji haseł i drugi dla obiektu lub atrybutu zadań synchronizacji i konserwacji. W tym temacie omówiono te ostatnie.

We wcześniejszych wersjach zewnętrznych w aparacie synchronizacji został harmonogram dla obiektów i atrybutów. Harmonogram zadań Windows lub osobną usługą Windows on używany do synchronizacji. Usługa scheduler jest dzięki wbudowanej wersji 1.1, aby synchronizacja aparatu i umożliwienia pewne dostosowania. Częstotliwość synchronizacji nowy domyślny to 30 minut.

Harmonogram jest odpowiedzialny za dwie czynności:

* **Cykl synchronizacji**. Proces importowania, synchronizacji i eksportowania zmiany.
* **Zadania konserwacji**. Odnów klucze i certyfikaty służące do resetowania hasła i usługi rejestracji urządzeń (DRS). Przeczyszczać stare wpisy w Dzienniku działań.

Harmonogram, sama zawsze działa, ale można skonfigurować tylko uruchomienia co najmniej żadna z tych zadań. Na przykład jeśli musisz mieć własny proces cykl synchronizacji, można wyłączyć tego zadania w ramach harmonogramu zadań, ale nadal uruchamiać zadania konserwacji.

## <a name="scheduler-configuration"></a>Konfiguracja harmonogramu
Aby wyświetlić bieżące ustawienia konfiguracji, przejdź do programu PowerShell i uruchom `Get-ADSyncScheduler`. Przedstawia on podobny do tego obrazu:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Jeśli widzisz **polecenie synchronizacji lub polecenie cmdlet nie jest dostępna** po uruchomieniu tego polecenia cmdlet, następnie modułu programu PowerShell nie jest załadowany. Ten problem może się zdarzyć, jeśli program Azure AD Connect jest uruchomiony na kontrolerze domeny lub na serwerze przy użyciu programu PowerShell poziomu ograniczeń niż domyślne ustawienia. Jeśli zostanie wyświetlony ten błąd, uruchom `Import-Module ADSync` udostępnić polecenia cmdlet.

* **AllowedSyncCycleInterval**. Najkrótszy odstęp czasu między cykle synchronizacji dozwolone przez usługę Azure AD. Nie można zsynchronizować częściej niż to ustawienie i nadal obsługiwane.
* **CurrentlyEffectiveSyncCycleInterval**. Aktualnie aktywny harmonogram. Ma taką samą wartość jak CustomizedSyncInterval (jeśli ustawić) Jeśli nie jest to częściej niż AllowedSyncInterval. Jeśli zmienisz CustomizedSyncCycleInterval użyć kompilacji przed 1.1.281, ta zmiana zostanie uwzględniona po następnym cyklu synchronizacji. Z kompilacji 1.1.281 zmiany zaczęły obowiązywać natychmiast.
* **CustomizedSyncCycleInterval**. Jeśli chcesz uruchomić częstotliwością żadnych innych niż domyślne 30 minut harmonogram, możesz skonfigurować to ustawienie. Na powyższym rysunku ustawiono harmonogram uruchamiany co godzinę w zamian. Jeśli to ustawienie na wartość niższa niż AllowedSyncInterval, drugie zostanie użyta.
* **NextSyncCyclePolicyType**. Różnicowe lub początkowego. Określa, czy następnego uruchomienia należy tylko zmiany różnicowe procesu lub jeśli następnego uruchomienia, należy wykonać pełny importowania i synchronizacji. Drugim będzie również ponownie przetworzyć żadnych reguł nowe lub zostały zmienione.
* **NextSyncCycleStartTimeInUTC**. Następnym razem w następnym cyklu synchronizacji uruchamianym przez harmonogram.
* **PurgeRunHistoryInterval**. Czas, powinny być przechowywane dzienniki operacji. Te dzienniki mogą być przeglądane na Menedżera usługi synchronizacji. Wartość domyślna to na potrzeby przechowywania tych dzienników przez 7 dni.
* **SyncCycleEnabled**. Wskazuje, czy harmonogram działa importowania, synchronizacji i procesów eksportowania jako część operacji.
* **MaintenanceEnabled**. Pokazuje, czy proces konserwacji jest włączony. Są aktualizowane kluczy/certyfikatów i Przeczyszcza dziennika operacji.
* **StagingModeEnabled**. Pokazuje, jeśli [Tryb przejściowy](how-to-connect-sync-staging-server.md) jest włączona. Jeśli to ustawienie jest włączone, następnie go pomija eksporty uruchamianie, ale nadal działać, importowania i synchronizacji.
* **SchedulerSuspended**. Ustaw przy użyciu Connect podczas uaktualniania tymczasowo bloku harmonogram uruchamiania.

Można zmienić niektóre z tych ustawień z `Set-ADSyncScheduler`. Można zmodyfikować następujące parametry:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

W starszych kompilacji programu Azure AD Connect **isStagingModeEnabled** została udostępniona w ADSyncScheduler zestawu. Jest **nieobsługiwany** ustawić tę właściwość. Właściwość **SchedulerSuspended** powinny być modyfikowane tylko przez połączenie. Jest **nieobsługiwany** ustawienie przy użyciu programu PowerShell bezpośrednio.

Konfiguracja usługi scheduler jest przechowywana w usłudze Azure AD. Jeśli masz serwer przejściowy, wszelkie zmiany na serwerze podstawowym wpływa również na serwerze (z wyjątkiem IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Składnia: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d — dni HH — godziny, mm — minuty, ss — sekundy

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Zmienia harmonogram, który ma co 3 godziny.

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Zmiany zmiany harmonogramu, aby było uruchamiane codziennie.

### <a name="disable-the-scheduler"></a>Wyłącz harmonogram  
Jeśli musisz wprowadzić zmiany w konfiguracji, następnie chcesz wyłączyć harmonogram. Na przykład, gdy użytkownik [Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md) lub [zmiany reguł synchronizacji](how-to-connect-sync-change-the-configuration.md).

Aby wyłączyć harmonogram, uruchom `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Wyłącz harmonogram](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Gdy zostaną wprowadzone zmiany, nie zapomnij włączyć harmonogram ponownie, używając `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Uruchomić harmonogram
Usługa scheduler jest domyślnie uruchamiane co 30 minut. W niektórych przypadkach możesz chcieć uruchomić cykl synchronizacji, między cykle zaplanowane lub musisz uruchomić innego typu.

**Cykl synchronizacji różnicowej**  
Cykl synchronizacji różnicowej obejmuje następujące kroki:

* Import zmian na wszystkich łączników
* Synchronizacja różnicowa na wszystkich łączników
* Eksport na wszystkich łączników

Możliwe, że masz pilnych zmian, które muszą być synchronizowane od razu, dlatego należy ręcznie uruchomić cyklu. Jeśli chcesz ręcznie uruchomić cykl, następnie za pomocą programu PowerShell, uruchom `Start-ADSyncSyncCycle -PolicyType Delta`.

**Pełna synchronizacja cyklu**  
Jeśli zostały wprowadzone, jedną z następujących zmian konfiguracji, należy uruchomić cykl pełnej synchronizacji (zwane) Początkowa):

* Dodano więcej obiektów i atrybutów, które mają zostać zaimportowane z katalogu źródłowego
* Wprowadzono zmiany reguł synchronizacji
* Zmienione [filtrowanie](how-to-connect-sync-configure-filtering.md) tak różną liczbę obiektów powinien być dołączony

Jeśli utworzono jedną z tych zmian należy Uruchom cyklu pełnej synchronizacji, aby aparat synchronizacji ma możliwość ponowna łącznika miejsca do magazynowania. Cykl Pełna synchronizacja obejmuje następujące kroki:

* Pełny Import na wszystkich łączników
* Pełna synchronizacja w wszystkie łączniki
* Eksport na wszystkich łączników

Aby zainicjować cykl pełnej synchronizacji, należy uruchomić `Start-ADSyncSyncCycle -PolicyType Initial` w wierszu polecenia programu PowerShell. To polecenie uruchamia cyklu pełną synchronizację.

## <a name="stop-the-scheduler"></a>Zatrzymaj harmonogram
Harmonogram jest aktualnie uruchomione cykl synchronizacji, może być konieczne jego zatrzymanie. Na przykład, jeśli pojawi się ten błąd, uruchom Kreatora instalacji:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Gdy cykl synchronizacji jest uruchomiona, nie możesz wprowadzać zmian w konfiguracji. Można poczekaj, aż harmonogram zakończy proces, ale można je również zatrzymać go, wprowadź zmiany w natychmiast. Trwa zatrzymywanie bieżącego cyklu nie jest szkodliwy i oczekujących zmian są przetwarzane przy następnym uruchomieniu.

1. Rozpocznij od informuje harmonogramu, aby zatrzymać jej bieżącego cyklu za pomocą polecenia cmdlet programu PowerShell `Stop-ADSyncSyncCycle`.
2. Jeśli używasz kompilacji przed 1.1.281, następnie zatrzymywanie harmonogramu nie zatrzymuje bieżącego łącznika z jego bieżącego zadania. Aby wymusić łącznika Aby zatrzymać, wykonaj następujące czynności: ![StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Rozpocznij **usługi synchronizacji** z start menu. Przejdź do **łączników**, wyróżnij łącznika ze stanem **systemem**i wybierz **zatrzymać** z akcji.

Harmonogram jest nadal aktywne i jest uruchamiany ponownie w następnym szansy sprzedaży.

## <a name="custom-scheduler"></a>Harmonogram niestandardowy
Polecenia cmdlet opisane w tej sekcji są dostępne tylko w kompilacji [1.1.130.0](reference-connect-version-history.md#111300) i nowszych.

Wbudowane harmonogramu nie spełnia wymagań, można zaplanować łączników, przy użyciu programu PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Możesz uruchomić profil łącznika w ten sposób:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Nazwy, które mają być używane dla [nazwy łącznika](how-to-connect-sync-service-manager-ui-connectors.md) i [nazwy profilu uruchamiania](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) znajdują się w [interfejsu użytkownika Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

![Wywoływanie profilu uruchamiania](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` Polecenia cmdlet jest synchroniczne, oznacza to, że nie zwraca kontroli dopóki nie zakończy się łącznik operacji, pomyślnego lub z powodu błędu.

Podczas planowania łączników, zaleca się zaplanować ich w następującej kolejności:

1. (Pełnej/różnicowej) Importuj z katalogami lokalnymi, takimi jak Active Directory
2. (Pełnej/różnicowej) Importuj z usługi Azure AD
3. (Pełnej/różnicowej) Synchronizacja z katalogami lokalnymi, takimi jak Active Directory
4. (Pełnej/różnicowej) Synchronizacja z usługi Azure AD
5. Eksportowanie do usługi Azure AD
6. Eksportowanie do katalogów lokalnych, takich jak usługi Active Directory

Ta kolejność jest jak wbudowane harmonogram jest wykonywany łączników.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Można również monitorować aparat synchronizacji, aby sprawdzić, czy jest zajęty lub bezczynności. To polecenie cmdlet zwraca żadnego wyniku, jeśli aparat synchronizacji jest w stanie bezczynności, a nie łącznika. Jeśli łącznik jest uruchomiona, zwraca nazwę łącznika.

```
Get-ADSyncConnectorRunStatus
```

![Stan uruchomienia łącznika](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na powyższym rysunku pierwszy wiersz jest w stanie, gdy aparat synchronizacji jest w stanie bezczynności. Drugi wiersz, gdy łącznik usługi Azure AD jest uruchomiona.

## <a name="scheduler-and-installation-wizard"></a>Harmonogram i Kreator instalacji
W przypadku uruchomienia Kreatora instalacji, harmonogram tymczasowo jest wstrzymane. To zachowanie jest spowodowane zakłada się wprowadzić zmiany w konfiguracji i nie można zastosować te ustawienia, jeśli aparat synchronizacji jest aktywnie uruchomiona. Z tego powodu nie zamykaj Kreatora instalacji ponieważ przestaje aparatu synchronizacji z wykonywania wszystkich działań synchronizacji.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
