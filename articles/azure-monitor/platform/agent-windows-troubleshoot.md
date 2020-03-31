---
title: Rozwiązywanie problemów z agentem usługi Log Analytics dla systemu Windows
description: Opisz symptomy, przyczyny i rozwiązanie najczęstszych problemów z agentem usługi Log Analytics dla systemu Windows w usłudze Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333503"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Jak rozwiązywać problemy z agentem usługi Log Analytics dla systemu Windows 

Ten artykuł zawiera pomoc w rozwiązywaniu problemów z błędami, które mogą wystąpić z agentem usługi Log Analytics dla systemu Windows w usłudze Azure Monitor i sugeruje możliwe rozwiązania, aby je rozwiązać.

Jeśli żaden z tych kroków nie działa, dostępne są również następujące kanały pomocy technicznej:

* Klienci korzystający z pomocy technicznej Premier mogą otworzyć żądanie pomocy technicznej w [u. Premier](https://premier.microsoft.com/).
* Klienci korzystający z umów pomocy technicznej platformy Azure mogą otworzyć żądanie pomocy technicznej [w witrynie Azure.](https://manage.windowsazure.com/?getsupport=true)
* Odwiedź stronę Opinii usługi Log Analytics, aby [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) przejrzeć przesłane pomysły i błędy lub złożyć nową. 

## <a name="important-troubleshooting-sources"></a>Ważne źródła rozwiązywania problemów

 Aby pomóc w rozwiązywaniu problemów związanych z agentem usługi Log Analytics dla systemu Windows, agent rejestruje zdarzenia w dzienniku zdarzeń systemu Windows, w szczególności w obszarze *Aplikacja i usługi\Program Operations Manager*.  

## <a name="connectivity-issues"></a>Problemy z łącznością

Jeśli agent komunikuje się za pośrednictwem serwera proxy lub zapory, mogą istnieć ograniczenia uniemożliwiające komunikację z komputera źródłowego i usługi Azure Monitor. W przypadku, gdy komunikacja jest zablokowana, z powodu błędnej konfiguracji rejestracja w obszarze roboczym może zakończyć się niepowodzeniem podczas próby zainstalowania agenta lub skonfigurowania agenta po skonfigurowaniu do raportowania do dodatkowego obszaru roboczego. Komunikacja agenta może zakończyć się niepowodzeniem po pomyślnej rejestracji. W tej sekcji opisano metody rozwiązywania tego typu problemów z agentem systemu Windows.

Sprawdź, czy zapora lub serwer proxy jest skonfigurowany tak, aby zezwalać na następujące porty i adresy URL opisane w poniższej tabeli. Upewnij się również, że inspekcja HTTP nie jest włączona dla ruchu internetowego, ponieważ może uniemożliwić bezpieczny kanał TLS między agentem a usługą Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.oms.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.blob.core.windows.net |port 443 |Wychodzący|Tak |  

Aby uzyskać informacje o zaporze wymagane dla platformy Azure Government, zobacz [Zarządzanie dla instytucji rządowych platformy Azure](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). Jeśli planujesz używać procesu roboczego hybrydowego wiązki uruchomieniu usługi Azure Automation do łączenia się z usługą Automatyzacja i rejestrowania się w niej w celu używania wiązek ekwiwania lub rozwiązań do zarządzania w twoim środowisku, musi mieć dostęp do numeru portu i adresów URL opisanych w [temacie Konfigurowanie sieci dla hybrydowego procesu roboczego systemu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Istnieje kilka sposobów sprawdzenia, czy agent pomyślnie komunikuje się z usługą Azure Monitor.

- Włącz [ocenę kondycji agenta usługi Azure Log Analytics](../insights/solution-agenthealth.md) w obszarze roboczym. Na pulpicie nawigacyjnym kondycji agenta wyświetl kolumnę **Liczba agentów nieodpowiadających,** aby szybko sprawdzić, czy agent znajduje się na liście.  

- Uruchom następującą kwerendę, aby potwierdzić, że agent wysyła puls do obszaru roboczego, do który jest skonfigurowany do raportowania. Wymień `<ComputerName>` na rzeczywistą nazwę urządzenia.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Jeśli komputer pomyślnie komunikuje się z usługą, kwerenda powinna zwrócić wynik. Jeśli kwerenda nie zwróciła wyniku, najpierw sprawdź, czy agent jest skonfigurowany do raportowania do właściwego obszaru roboczego. Jeśli jest poprawnie skonfigurowany, przejdź do kroku 3 i wyszukaj w dzienniku zdarzeń systemu Windows, aby zidentyfikować, czy agent rejestruje, jaki problem może uniemożliwiać mu komunikowanie się z usługą Azure Monitor.

- Inną metodą identyfikowania problemu z łącznością jest uruchomienie narzędzia **TestCloudConnectivity.** Narzędzie jest instalowane domyślnie z agentem w folderze *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. W wierszu polecenia z podwyższonym poziomem uprawnień przejdź do folderu i uruchom narzędzie. Narzędzie zwraca wyniki i podświetla, gdzie test nie powiódł się (na przykład, jeśli był powiązany z określonym portem/adresem URL, który został zablokowany). 

    ![Wyniki wykonania narzędzia TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtruj dziennik zdarzeń *programu Operations Manager* według modułów - usługi kondycji źródeł **zdarzeń,** *healthservice*i *łącznika usługi* i filtruj według *ostrzeżenia* **o poziomie zdarzenia** i *błędu,* aby potwierdzić, czy napisał zdarzenia z poniższej tabeli.*Health Service Modules* Jeśli tak, przejrzyj kroki rozwiązywania problemów uwzględnione dla każdego możliwego zdarzenia.

    |Identyfikator zdarzenia |Element źródłowy |Opis |Rozwiązanie |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Usługa kondycji |Połączenie z usługą z agenta nie powiodło się |Ten błąd może wystąpić, gdy agent nie może komunikować się bezpośrednio lub za pośrednictwem serwera zapory/serwera proxy do usługi Azure Monitor. Sprawdź ustawienia serwera proxy agenta lub czy zapora sieciowa/serwer proxy zezwala na ruch TCP z komputera do usługi.|
    |2138 |Moduły usługi zdrowotnej |Serwer proxy wymaga uwierzytelnienia |Skonfiguruj ustawienia serwera proxy agenta i określ nazwę użytkownika/hasło wymagane do uwierzytelnienia na serwerze proxy. |
    |2129 |Moduły usługi zdrowotnej |Nieudane połączenie/nieudana negocjacja TLS |Sprawdź ustawienia karty sieciowej TCP/IP i ustawienia serwera proxy agenta.|
    |2127 |Moduły usługi zdrowotnej |Niepowodzenie wysyłania danych odebrany kod błędu |Jeśli dzieje się to tylko okresowo w ciągu dnia, może to być po prostu losowa anomalia, którą można zignorować. Monitoruj, aby zrozumieć, jak często to się dzieje. Jeśli zdarza się to często w ciągu dnia, najpierw sprawdź konfigurację sieci i ustawienia serwera proxy. Jeśli opis zawiera kod błędu HTTP 404 i jest to pierwszy raz, gdy agent próbuje wysłać dane do usługi, będzie zawierać błąd 500 z wewnętrznym kodem błędu 404. 404 oznacza nie znaleziono, co wskazuje, że obszar magazynowania dla nowego obszaru roboczego jest nadal aprowizacji. Przy następnej próbie ponawiania danych zostanie pomyślnie zapis w obszarze roboczym zgodnie z oczekiwaniami. Błąd HTTP 403 może wskazywać na problem z uprawnieniami lub poświadczeniami. Więcej informacji zawiera błąd 403, aby pomóc w rozwiązaniu problemu.|
    |4000 |Łącznik serwisu |Rozpoznawanie nazw DNS nie powiodło się |Komputer nie może rozpoznać adresu internetowego używanego podczas wysyłania danych do usługi. Mogą to być ustawienia programu rozpoznawania nazw DNS na komputerze, nieprawidłowe ustawienia serwera proxy lub tymczasowy problem z systemem DNS u dostawcy. Jeśli dzieje się to okresowo, może to być spowodowane przez przejściowy problem związany z siecią.|
    |4001 |Łącznik serwisu |Połączenie z usługą nie powiodło się. |Ten błąd może wystąpić, gdy agent nie może komunikować się bezpośrednio lub za pośrednictwem serwera zapory/serwera proxy do usługi Azure Monitor. Sprawdź ustawienia serwera proxy agenta lub czy zapora sieciowa/serwer proxy zezwala na ruch TCP z komputera do usługi.|
    |4002 |Łącznik serwisu |Usługa zwróciła kod stanu HTTP 403 w odpowiedzi na kwerendę. Skontaktuj się z administratorem usługi, aby uzyskać informacje o kondycji usługi. Kwerenda zostanie ponowiona później. |Ten błąd jest zapisywany podczas początkowej fazy rejestracji agenta i zobaczysz adres URL podobny do następującego: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Kod błędu 403 oznacza zabronione i może być spowodowane przez błędnie ominął identyfikator obszaru roboczego lub klucz lub dane i czas jest niepoprawna na komputerze. Jeśli czas wynosi +/- 15 minut od bieżącego czasu, dołączanie nie powiedzie się. Aby rozwiązać ten problem, zaktualizuj datę i/lub strefę czasową komputera z systemem Windows.|

## <a name="data-collection-issues"></a>Problemy z gromadzeniem danych

Po zainstalowaniu agenta i raportowaniu skonfigurowanego obszaru roboczego lub obszarów roboczych może przestać odbierać konfigurację, zbierać lub przekazywać dalej wydajność, dzienniki lub inne dane do usługi w zależności od tego, co jest włączone i kierowanie na komputer. Konieczne jest ustalenie, czy:

- Czy jest to określony typ danych lub wszystkie dane, które nie są dostępne w obszarze roboczym?
- Czy typ danych jest określony przez rozwiązanie lub określony jako część konfiguracji zbierania danych obszaru roboczego?
- Ile komputerów dotyczy? Czy jest to jeden lub wiele komputerów raportowania do obszaru roboczego?
- Czy to działa i czy zatrzymał się o określonej porze dnia, czy nigdy nie zostały zebrane? 
- Czy kwerenda wyszukiwania dziennika używasz syntaktycznie poprawne? 
- Czy agent kiedykolwiek otrzymał swoją konfigurację z usługi Azure Monitor?

Pierwszym krokiem w rozwiązywaniu problemów jest ustalenie, czy komputer wysyła zdarzenie pulsu.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Jeśli kwerenda zwraca wyniki, należy określić, czy określony typ danych nie jest zbierany i przekazywane do usługi. Może to być spowodowane przez agenta nie odbieranie zaktualizowanej konfiguracji z usługi lub innego objawu uniemożliwiającego agenta normalnie działać. Wykonaj następujące kroki, aby dalej rozwiązywać problemy.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na `net stop healthservice && net start healthservice`komputerze i uruchom ponownie usługę agenta, wpisując polecenie .
2. Otwórz dziennik zdarzeń *programu Operations Manager* i wyszukaj **identyfikatory zdarzeń** *7023, 7024, 7025, 7028* i *1210* ze **źródła zdarzeń** *HealthService*.  Te zdarzenia wskazują, że agent pomyślnie odbiera konfigurację z usługi Azure Monitor i aktywnie monitoruje komputer. Opis zdarzenia dla zdarzenia o identyfikatorze 1210 określi również w ostatnim wierszu wszystkie rozwiązania i usługi Insights, które są uwzględnione w zakresie monitorowania agenta.  

    ![Opis zdarzenia o identyfikatorze 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Jeśli po kilku minutach nie zobaczysz **oczekiwanych** danych w wynikach kwerendy lub wizualizacji, w zależności od tego, czy są wyświetlane dane z rozwiązania lub aplikacji Insight, z dziennika zdarzeń *programu Operations Manager* wyszukaj źródła zdarzeń *HealthService* i *moduły usługi kondycji* i filtruj według *ostrzeżenia* o **poziomie zdarzenia** i *błędu,* aby potwierdzić, czy zostało napisane zdarzenia z poniższej tabeli.

    |Identyfikator zdarzenia |Element źródłowy |Opis |Rozwiązanie |
    |---------|-------|------------|
    |8000 |Służba zdrowia |To zdarzenie określi, czy przepływ pracy związany z wydajnością, zdarzeniem lub innym zebranym typem danych nie może przesłać dalej do usługi w celu pobrania do obszaru roboczego. | Identyfikator zdarzenia 2136 ze źródła HealthService jest zapisywany razem z tym zdarzeniem i może wskazywać, że agent nie może komunikować się z usługą, prawdopodobnie z powodu błędnej konfiguracji serwera proxy i ustawień uwierzytelniania, awarii sieci lub zapory sieciowej/serwera proxy nie zezwala na ruch TCP z komputera do usługi.| 
    |10102 i 10103 |Moduły usługi zdrowotnej |Przepływ pracy nie może rozwiązać źródła danych. |Taka możliwość może wystąpić, jeśli określony licznik wydajności lub wystąpienie nie istnieje na komputerze lub jest niepoprawnie zdefiniowany w ustawieniach danych obszaru roboczego. Jeśli jest to [licznik wydajności](data-sources-performance-counters.md#configuring-performance-counters)określony przez użytkownika, sprawdź, czy określone informacje są zgodne z poprawnym formatem i istnieją na komputerach docelowych. |
    |26002 |Moduły usługi zdrowotnej |Przepływ pracy nie może rozwiązać źródła danych. |Taka możliwość może wystąpić, jeśli określony dziennik zdarzeń systemu Windows nie istnieje na komputerze. Ten błąd można bezpiecznie zignorować, jeśli nie oczekuje się, że ten dziennik zdarzeń zostanie zarejestrowany, w przeciwnym razie, jeśli jest to [dziennik zdarzeń](data-sources-windows-events.md#configuring-windows-event-logs)określony przez użytkownika, sprawdź, czy określone informacje są poprawne. |

