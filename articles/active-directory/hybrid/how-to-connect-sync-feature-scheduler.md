---
title: 'Synchronizacja usługi Azure AD Connect: harmonogram | Dokumenty firmy Microsoft'
description: W tym temacie opisano wbudowaną funkcję harmonogramu w synchronizacji usługi Azure AD Connect.
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
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309adfbebd4f4b615ac1f4061823ca01f3d3ee15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261075"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizacja programu Azure AD Connect: Harmonogram
W tym temacie opisano wbudowany harmonogram w synchronizacji usługi Azure AD Connect (aparat synchronizacji).

Ta funkcja została wprowadzona w kompilacji 1.1.105.0 (wydana w lutym 2016).

## <a name="overview"></a>Omówienie
Synchronizacja usługi Azure AD Connect synchronizuje zmiany zachodzące w katalogu lokalnym przy użyciu harmonogramu. Istnieją dwa procesy harmonogramu, jeden do synchronizacji haseł i drugi dla zadań synchronizacji i konserwacji obiektów/atrybutów. Ten temat obejmuje ten ostatni.

We wcześniejszych wersjach harmonogram dla obiektów i atrybutów był zewnętrzny do aparatu synchronizacji. Do wyzwolenia procesu synchronizacji użyto harmonogramu zadań systemu Windows lub oddzielnej usługi systemu Windows. Harmonogram jest z 1.1 zwalnia wbudowany do aparatu synchronizacji i pozwalają na pewne dostosowanie. Nowa domyślna częstotliwość synchronizacji wynosi 30 minut.

Harmonogram jest odpowiedzialny za dwa zadania:

* **Cykl synchronizacji**. Proces importowania, synchronizowania i eksportowania zmian.
* **Zadania konserwacyjne**. Odnawianie kluczy i certyfikatów dla usługi resetowania hasła i rejestracji urządzeń (DRS). Przeczyść stare wpisy w dzienniku operacji.

Sam harmonogram jest zawsze uruchomiony, ale można go skonfigurować tak, aby uruchamiał tylko jedno lub żadne z tych zadań. Na przykład jeśli trzeba mieć własny proces cyklu synchronizacji, można wyłączyć to zadanie w harmonogramie, ale nadal uruchomić zadanie konserwacji.

## <a name="scheduler-configuration"></a>Konfiguracja harmonogramu
Aby wyświetlić bieżące ustawienia konfiguracji, przejdź `Get-ADSyncScheduler`do programu PowerShell i uruchom program . To pokazuje coś takiego obrazu:

![GetSyncScheduler ( GetSyncScheduler )](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Jeśli zostanie wyświetlony **polecenie synchronizacja lub polecenie cmdlet nie jest dostępne** po uruchomieniu tego polecenia cmdlet, moduł programu PowerShell nie jest ładowany. Ten problem może się zdarzyć, jeśli uruchomisz usługę Azure AD Connect na kontrolerze domeny lub na serwerze z wyższymi poziomami ograniczeń programu PowerShell niż ustawienia domyślne. Jeśli widzisz ten błąd, `Import-Module ADSync` uruchom, aby udostępnić polecenie cmdlet.

* **AllowedSyncCycleInterval**. Najkrótszy przedział czasu między cyklami synchronizacji dozwolonymi przez usługę Azure AD. Nie można synchronizować częściej niż to ustawienie i nadal być obsługiwane.
* **CurrentlyEffectiveSyncCycleInterval**. Aktualnie obowiązuje harmonogram. Ma taką samą wartość jak CustomizedSyncInterval (jeśli jest ustawiona), jeśli nie jest częstszy niż AllowedSyncInterval. Jeśli używasz kompilacji przed 1.1.281 i zmienisz CustomizedSyncCycleInterval, ta zmiana zacznie obowiązywać po następnym cyklu synchronizacji. Od kompilacji 1.1.281 zmiana wchodzi w życie natychmiast.
* **CustomizedSyncCycleInterval**. Jeśli chcesz, aby harmonogram był uruchamiany z dowolną inną częstotliwością niż domyślne 30 minut, skonfiguruj to ustawienie. Na powyższym zdjęciu harmonogram został ustawiony do uruchamiania co godzinę. Jeśli to ustawienie zostanie ustawione na wartość niższą niż AllowedSyncInterval, zostanie użyte to ostatnie.
* **NextSyncCyclePolicyType**. Delta lub Początkowe. Określa, czy następne uruchomienie powinno przetwarzać tylko zmiany różnicowe, czy następne uruchomienie powinno wykonać pełny import i synchronizację. Te ostatnie ponownie przetworzyłyby również wszelkie nowe lub zmienione zasady.
* **NextSyncCycleStartTimeInUTC**. Następnym razem, gdy harmonogram uruchamia następny cykl synchronizacji.
* **PurgeRunHistoryInterval**. Dzienniki operacji czasu powinny być przechowywane. Te dzienniki można przejrzeć w menedżerze usługi synchronizacji. Domyślnie te dzienniki są rejestrowane przez 7 dni.
* **SyncCycleEnabled**. Wskazuje, czy harmonogram jest uruchomiony procesów importu, synchronizacji i eksportu w ramach jego działania.
* **MaintenanceEnabled**. Pokazuje, czy proces konserwacji jest włączony. Aktualizuje certyfikaty/klucze i czyści dziennik operacji.
* **StagingModeEnabled**. Pokazuje, czy [tryb przemieszczania](how-to-connect-sync-staging-server.md) jest włączony. Jeśli to ustawienie jest włączone, powoduje pomijanie eksportu z uruchamiania, ale nadal uruchomić import i synchronizację.
* **HarmonogramSuspended**. Ustaw przez Connect podczas uaktualniania, aby tymczasowo zablokować uruchamianie harmonogramu.

Niektóre z tych ustawień `Set-ADSyncScheduler`można zmienić za pomocą pliku . Można zmodyfikować następujące parametry:

* DostosowaneŁączaowanie cyklisynkowych
* Następny Typ polityki cyklisynkowych
* Przeczyszczanie HistoriaInterwalna
* SyncCycleEnabled
* KonserwacjaWładna

We wcześniejszych kompilacjach usługi Azure AD Connect **isStagingModeEnabled** został ujawniony w usłudze Set-ADSyncScheduler. Nie jest **obsługiwane,** aby ustawić tę właściwość. Właściwość **SchedulerSuspended** powinna być modyfikowana tylko przez Connect. Nie jest **obsługiwane,** aby ustawić to bezpośrednio z programem PowerShell.

Konfiguracja harmonogramu jest przechowywana w usłudze Azure AD. Jeśli masz serwer przejściowy, wszelkie zmiany na serwerze podstawowym mają również wpływ na serwer przejściowy (z wyjątkiem IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>DostosowaneŁączaowanie cyklisynkowych
Składni:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dni, HH - godziny, mm - minuty, ss - sekundy

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Zmienia harmonogram do uruchamiania co 3 godziny.

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Zmiany zmieniają harmonogram do uruchamiania codziennie.

### <a name="disable-the-scheduler"></a>Wyłączanie harmonogramu  
Jeśli musisz wprowadzić zmiany konfiguracji, chcesz wyłączyć harmonogram. Na przykład podczas [konfigurowania filtrowania](how-to-connect-sync-configure-filtering.md) lub [wprowadzania zmian w regułach synchronizacji](how-to-connect-sync-change-the-configuration.md).

Aby wyłączyć harmonogram, `Set-ADSyncScheduler -SyncCycleEnabled $false`uruchom program .

![Wyłączanie harmonogramu](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Po wprowadzeniu zmian nie zapomnij ponownie włączyć harmonogramu za `Set-ADSyncScheduler -SyncCycleEnabled $true`pomocą pliku .

## <a name="start-the-scheduler"></a>Uruchamianie harmonogramu
Harmonogram jest domyślnie uruchamiany co 30 minut. W niektórych przypadkach można uruchomić cykl synchronizacji między zaplanowanymi cyklami lub musisz uruchomić inny typ.

### <a name="delta-sync-cycle"></a>Cykl synchronizacji różnicowej
Cykl synchronizacji różnicowej obejmuje następujące kroki:


- Import delta na wszystkich złączach
- Synchronizacja delta na wszystkich złączach
- Eksport na wszystkich łącznikach

### <a name="full-sync-cycle"></a>Pełny cykl synchronizacji
Pełny cykl synchronizacji obejmuje następujące kroki:

- Pełny import na wszystkich złączach
- Pełna synchronizacja na wszystkich złączach
- Eksport na wszystkich łącznikach

Może to być pilna zmiana, która musi zostać natychmiast zsynchronizowana, dlatego musisz ręcznie uruchomić cykl. 

Jeśli chcesz ręcznie uruchomić cykl synchronizacji, uruchom program `Start-ADSyncSyncCycle -PolicyType Delta`PowerShell .

Aby zainicjować pełny cykl `Start-ADSyncSyncCycle -PolicyType Initial` synchronizacji, uruchom z monitu programu PowerShell.   

Uruchamianie pełnego cyklu synchronizacji może być bardzo czasochłonne, przeczytaj następną sekcję, aby przeczytać, jak zoptymalizować ten proces.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Kroki synchronizacji wymagane dla różnych zmian konfiguracji
Różne zmiany konfiguracji wymagają różnych kroków synchronizacji, aby upewnić się, że zmiany są poprawnie stosowane do wszystkich obiektów.

- Dodano więcej obiektów lub atrybutów do zaimportowania z katalogu źródłowego (przez dodanie/zmodyfikowanie reguł synchronizacji)
    - Pełny import jest wymagany w łączniku dla tego katalogu źródłowego
- Wprowadzono zmiany w regułach synchronizacji
    - Pełna synchronizacja jest wymagana w łączniku dla zmienionych reguł synchronizacji
- Zmieniono [filtrowanie,](how-to-connect-sync-configure-filtering.md) aby uwzględnić inną liczbę obiektów
    - Pełny import jest wymagany w łączniku dla każdego łącznika usługi AD, chyba że używasz filtrowania opartego na atrybutach na podstawie atrybutów, które są już importowane do aparatu synchronizacji

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Dostosowywanie cyklu synchronizacji przebiegało zgodnie z właściwą kombinacją kroków Delta i Pełna synchronizacja
Aby uniknąć uruchamiania pełnego cyklu synchronizacji, można oznaczyć określone łączniki, aby uruchomić pełny krok przy użyciu następujących poleceń cmdlet.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Przykład: Jeśli wprowadzono zmiany w regułach synchronizacji łącznika "Las usługi AD A", które nie wymagają żadnych nowych atrybutów do zaimportowania, należy uruchomić następujące polecenia cmdlet, aby uruchomić cykl synchronizacji różnicowej, który również wykonał krok pełnej synchronizacji dla tego łącznika.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Przykład: Jeśli wprowadzono zmiany w regułach synchronizacji łącznika "Las usługi AD A", tak aby teraz wymagały one zaimportowania nowego atrybutu, należy uruchomić następujące polecenia cmdlet, aby uruchomić cykl synchronizacji różnicowej, który również wykonał pełny import, krok pełnej synchronizacji dla tego łącznika.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Zatrzymaj harmonogram
Jeśli harmonogram jest obecnie uruchomiony cykl synchronizacji, może być konieczne jego zatrzymanie. Na przykład po uruchomieniu kreatora instalacji i zostanie wyświetlony ten błąd:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Gdy cykl synchronizacji jest uruchomiony, nie można wprowadzać zmian konfiguracji. Można poczekać, aż harmonogram zakończy proces, ale można również zatrzymać go, dzięki czemu można wprowadzić zmiany natychmiast. Zatrzymanie bieżącego cyklu nie jest szkodliwe i oczekujące zmiany są przetwarzane przy następnym uruchomieniu.

1. Zacznij od poinformowania harmonogramu o zatrzymaniu bieżącego cyklu za pomocą polecenia cmdlet programu PowerShell `Stop-ADSyncSyncCycle`.
2. Jeśli używasz kompilacji przed 1.1.281, a następnie zatrzymanie harmonogramu nie zatrzymuje bieżącego łącznika z jego bieżącego zadania. Aby wymusić zatrzymanie łącznika, należy ![podjąć następujące czynności: StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Uruchom **usługę synchronizacji** z menu Start. Przejdź do **łączników**, wyróżnij łącznik ze stanem **Uruchomiony**i wybierz **pozycję Zatrzymaj** z akcji.

Harmonogram jest nadal aktywny i uruchamia się ponownie przy następnej okazji.

## <a name="custom-scheduler"></a>Harmonogram niestandardowy
Polecenia cmdlet opisane w tej sekcji są dostępne tylko w kompilacji [1.1.130.0](reference-connect-version-history.md#111300) i nowszych.

Jeśli wbudowany harmonogram nie spełnia wymagań, można zaplanować łączniki przy użyciu programu PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Wywoływanie-ADSyncRunProfile
Profil łącznika można uruchomić w ten sposób:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Nazwy używane dla [nazw łączników](how-to-connect-sync-service-manager-ui-connectors.md) i [nazw profilów uruchamiania](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) można znaleźć w interfejsie [użytkownika programu Synchronizacja menedżera usług](how-to-connect-sync-service-manager-ui.md).

![Wywołaj profil uruchamiania](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

Polecenie `Invoke-ADSyncRunProfile` cmdlet jest synchroniczne, oznacza to, że nie zwraca kontroli, dopóki łącznik nie zakończy operację, pomyślnie lub z błędem.

Podczas planowania łączników zaleca się zaplanowanie ich w następującej kolejności:

1. (Pełna/Delta) Importowanie z katalogów lokalnych, takich jak usługa Active Directory
2. (Pełna/Delta) Importowanie z usługi Azure AD
3. (Pełna/Delta) Synchronizacja z katalogów lokalnych, takich jak usługa Active Directory
4. (Pełna/Delta) Synchronizacja z usługi Azure AD
5. Eksportowanie do usługi Azure AD
6. Eksportowanie do katalogów lokalnych, takich jak usługa Active Directory

W tej kolejności jest jak wbudowany harmonogram uruchamia łączniki.

### <a name="get-adsyncconnectorrunstatus"></a>Stan Połączenia Get-AD
Można również monitorować aparat synchronizacji, aby sprawdzić, czy jest zajęty lub bezczynny. To polecenie cmdlet zwraca pusty wynik, jeśli aparat synchronizacji jest bezczynny i nie działa łącznik. Jeśli łącznik jest uruchomiony, zwraca nazwę łącznika.

```
Get-ADSyncConnectorRunStatus
```

![Stan uruchomienia łącznika](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na powyższym rysunku pierwsza linia pochodzi ze stanu, w którym aparat synchronizacji jest bezczynny. Drugi wiersz od momentu uruchomienia usługi Azure AD Connector.

## <a name="scheduler-and-installation-wizard"></a>Kreator harmonogramu i instalacji
Po uruchomieniu kreatora instalacji harmonogram zostanie tymczasowo zawieszony. To zachowanie jest, ponieważ zakłada się, że zmiany konfiguracji i te ustawienia nie mogą być stosowane, jeśli aparat synchronizacji jest aktywnie uruchomiony. Z tego powodu kreator instalacji nie należy pozostawiać otwartego kreatora instalacji, ponieważ uniemożliwia on aparatowi synchronizacji wykonywanie jakichkolwiek akcji synchronizacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
