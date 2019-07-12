---
title: Dzienniki diagnostyczne usługi Azure Stream do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor
description: Dowiedz się, jak przesyłać strumieniowo dzienniki diagnostyczne platformy Azure z obszarem roboczym usługi Log Analytics w usłudze Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e8e6276a38f06b5c6ebb24c89f3733b9fd7220f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612840"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Dzienniki diagnostyczne usługi Azure Stream do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor

**[Dzienniki diagnostyczne platformy Azure](diagnostic-logs-overview.md)**  może być przesyłany strumieniowo w czasie zbliżonym do rzeczywistego do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor, za pomocą portalu, poleceń cmdlet programu PowerShell lub wiersza polecenia platformy Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Co można zrobić przy użyciu diagnostyki dzienników w obszarze roboczym usługi Log Analytics

Usługa Azure Monitor zapewnia elastyczne dziennika wykonywania zapytań i analizy narzędzie, które umożliwia uzyskiwanie szczegółowych danych nieprzetworzonych danych dzienników generowanych na podstawie zasobów platformy Azure. Niektóre funkcje obejmują:

* **Zapytanie dziennika** -zapisu zaawansowanych zapytań over dane dziennika korelowanie dzienniki z różnych źródeł oraz generować wykresy, które można przypiąć do pulpitu nawigacyjnego platformy Azure.
* **Alerty** -Wykryj, kiedy jeden lub więcej zdarzeń pasuje do określonego zapytania i otrzymywania powiadomień za pomocą wywołania adresu e-mail lub elementu webhook przy użyciu alertów usługi Azure Monitor.
* **Advanced analytics** — stosowanie usługi machine learning i wzorca dopasowania algorytmów w celu identyfikacji możliwych problemów ujawnionych w dziennikach.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Włączanie przesyłania strumieniowego dzienników diagnostycznych do obszaru roboczego usługi Log Analytics

Aby umożliwić przesyłanie strumieniowe dzienników diagnostycznych programowo, w portalu lub przy użyciu [interfejsów API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). W obu przypadkach można utworzyć ustawienie diagnostyczne, w którym należy określić obszar roboczy usługi Log Analytics i kategorie dzienników i metryk, które chcesz wysłać do tego obszaru roboczego. Diagnostyka **kategoria dziennika** jest typem dziennika, który może dostarczyć zasób.

Obszar roboczy usługi Log Analytics nie musi znajdować się w tej samej subskrypcji co zasób emitowane dzienniki, jak długo użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do obu subskrypcji.

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Dzienniki diagnostyczne Stream przy użyciu portalu
1. W portalu, przejdź do usługi Azure Monitor, a następnie kliknij pozycję **ustawień diagnostycznych** w **ustawienia** menu.


2. Opcjonalnie filtrować listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasób, dla którego chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij przycisk "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   W przypadku ustawienia istniejące na zasób zostanie wyświetlona lista ustawień już skonfigurowany dla tego zasobu. Kliknij pozycję "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia i zaznacz pole **wysyłanie do usługi Log Analytics**, następnie wybierz obszar roboczy usługi Log Analytics.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Kliknij polecenie **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, i jak tylko nowe dane zdarzenia są generowane dzienniki diagnostyczne są przesyłane strumieniowo do tego obszaru roboczego. Może to być między podczas zdarzenia są emitowane i, gdy pojawia się w usłudze Log Analytics do 15 minut.

### <a name="via-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Przesyłania strumieniowego za pośrednictwem [poleceń cmdlet programu PowerShell Azure](../../azure-monitor/platform/powershell-quickstart-samples.md), możesz użyć `Set-AzDiagnosticSetting` polecenia cmdlet z następującymi parametrami:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Należy pamiętać, że właściwość workspaceID przyjmuje identyfikator zasobu pełnej platformy Azure nie obszaru roboczego klucz/Identyfikatora wyświetlane w portalu usługi Log Analytics w obszarze roboczym.

### <a name="via-azure-cli"></a>Za pomocą wiersza polecenia platformy Azure

Do przesyłania strumieniowego za pośrednictwem [wiersza polecenia platformy Azure](../../azure-monitor/platform/cli-samples.md), możesz użyć [tworzenie az monitor diagnostic-settings](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Dodatkowe kategorie można dodawać do dzienników diagnostycznych, dodając słowników do tablicy JSON przekazywane jako `--logs` parametru.

`--resource-group` Argument tylko jest wymagany, jeżeli `--workspace` nie jest identyfikator obiektu.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Jak zapytanie dane z obszaru roboczego usługi Log Analytics?

W bloku dzienników w portalu usługi Azure Monitor jako część z rozwiązaniem zarządzanie dziennikiem pod tabelą AzureDiagnostics można badać dzienników diagnostycznych. Dostępne są także [kilka rozwiązań do monitorowania zasobów platformy Azure](../../azure-monitor/insights/solutions.md) można zainstalować na uzyskiwanie natychmiastowego wglądu w dane dziennika są wysyłane do usługi Azure Monitor.

### <a name="examples"></a>Przykłady

```Kusto
// Resources that collect diagnostic logs into this Log Analytics workspace, using Diagnostic Settings
AzureDiagnostics
| distinct _ResourceId
```
```Kusto
// Resource providers collecting diagnostic logs into this Log Analytics worksapce, with log volume per category
AzureDiagnostics
| summarize count() by ResourceProvider, Category
```
```Kusto
// Resource types collecting diagnostic logs into this Log Analytics workspace, with number of resources onboarded
AzureDiagnostics
| summarize ResourcesOnboarded=dcount(_ResourceId) by ResourceType
```
```Kusto
// Operations logged by specific resource provider, in this example - KeyVault
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| distinct OperationName
```

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure Diagnostics a specyficznych dla zasobów  
Po włączeniu docelowej usługi Log Analytics w konfiguracji usługi Diagnostyka Azure istnieją dwa odrębne sposoby, które dane będą widoczne w obszarze roboczym:  
- **Diagnostyka Azure** — jest to metoda starszych używanych obecnie przez większość usług platformy Azure. W tym trybie wszystkie dane z dowolnego ustawienia diagnostyczne wskazywał danego obszaru roboczego będzie znajdą się w _AzureDiagnostics_ tabeli. 
<br><br>Ponieważ wiele zasobów wysyłać dane do tej samej tabeli (_AzureDiagnostics_), schemat ta tabela jest bardzo zestaw schematów typami danych zbieranych. Na przykład jeśli utworzono ustawień diagnostycznych dla kolekcji następujących typów danych, wszystkie wysyłane do tego samego obszaru roboczego:
    - Przeprowadź inspekcję dzienników 1 zasobu (o schemacie składający się z kolumn, A, B i C)  
    - Dzienniki błędów 2 zasobów (o schemacie składający się z kolumn, D, E i f.)  
    - Dzienniki przepływu danych 3 zasobów (o składający się z kolumn G, H i I schematu)  

    Tabela AzureDiagnostics będzie wyglądać następująco, z pewnymi przykładowymi danymi:  

    | ResourceProvider | Kategoria | A | B | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | Przesłano | | | | q1 | w1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | Przesłano | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | Przyciski ... |

- **Specyficznych dla zasobów** — w tym trybie poszczególnych tabel w wybranym obszarze roboczym są tworzone dla każdej kategorii, które wybrano w konfiguracji ustawień diagnostyki. Ta metoda nowszej sprawia, że łatwiej odnaleźć dokładnie ma zostać odnaleziona za pośrednictwem wyraźnej separacji: tabelę dla każdej kategorii. Ponadto zapewnia korzyści w jego pomocy technicznej dla typów dynamicznych. Już widoczny ten tryb, wybierz pozycję typy zasobów platformy Azure, na przykład [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) lub [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) dzienniki. Ostatecznie oczekujemy, że każdego typu danych, aby przeprowadzić migrację do trybu specyficznych dla zasobów. 

    W powyższym przykładzie spowodowałoby to w trzech tabelach tworzona: 
    - Tabela _AuditLogs_ w następujący sposób:

        | ResourceProvider | Kategoria | A | B | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | Przyciski ... |

    - Tabela _przesłano_ w następujący sposób:  

        | ResourceProvider | Kategoria | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | Przesłano | q1 | w1 | e1 |
        | Microsoft.Resource2 | Przesłano | q2 | w2 | e2 |
        | Przyciski ... |

    - Tabela _DataFlowLogs_ w następujący sposób:  

        | ResourceProvider | Kategoria | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | Przyciski ... |

    Inne zalety korzystania z trybu specyficznych dla zasobów obejmują lepszą wydajność zarówno opóźnienia w pozyskiwaniu danych, jak i czas zapytania, lepsze możliwości odnajdywania schematów i ich struktury, możliwość udzielania uprawnień RBAC w określonej tabeli i nie tylko.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Wybranie diagnostycznych platformy Azure trybu vs specyficznych dla zasobów
Dla zasobów najbardziej platformy Azure nie będziesz mieć możliwość wyboru czy ma być używany tryb diagnostycznych platformy Azure lub specyficznych dla zasobów dane będą automatycznie przekazywane za pośrednictwem metody, że zasób został wybrany do użycia. Można znaleźć w dokumentacji dostarczonej przez zasób zostało włączone, aby wysyłać dane do usługi Log Analytics, aby uzyskać szczegółowe informacje, na których są stosowane trybu. 

Jak wspomniano w poprzedniej sekcji, ostatecznie to klient jest celem usługi Azure Monitor, aby wszystkie usługi w korzystanie z platformy Azure w trybie specyficznych dla zasobów. Aby ułatwić ten proces przejścia i upewnij się, że żadne dane utracone jako część jej niektórych usług platformy Azure podczas dołączania do usługi Log Analytics zapewnia wybór trybu:  
   ![Wybór ustawień diagnostycznych w trybie](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Firma Microsoft **silnie** zaleca się, aby uniknąć potencjalnie trudne migracje występujących, dowolny nowo utworzony Użyj ustawień diagnostycznych zasobu skoncentrowane na tryb.  

Dla istniejących ustawień diagnostycznych, po włączeniu przez określony zasób platformy Azure można wstecznie przełączyć się z diagnostyki Azure do trybu specyficznych dla zasobów. Wcześniej pozyskane dane będą nadal dostępne w _AzureDiagnostics_ tabeli do momentu jej wieku się zgodnie z konfiguracją ustawień przechowywania w obszarze roboczym, ale wszystkie nowe dane zostaną wysłane do dedykowanych tabeli. Oznacza to, że dla każdego zapytania, które ma obejmować dane stare i nowe (aż do starych danych pełni wieku), [Unii](https://docs.microsoft.com/azure/kusto/query/unionoperator) operatora zapytania muszą się łączyć dwóch zestawów danych.

Przejrzyj nowe informacje o nowej Azure usług pomocniczych dzienniki w trybie specyficznych dla zasobów na [aktualizacje platformy Azure](https://azure.microsoft.com/updates/) blogu!

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Znane ograniczenia: kolumny limitu w AzureDiagnostics
Ma limitu jawne dowolnej podanej tabeli dziennika platformy Azure, nie ma więcej niż 500 kolumn. Po osiągnięciu wszystkie wiersze zawierające dane z dowolnej kolumny poza wyświetlanie 500 pierwszych zostanie porzucony w momencie pozyskiwania. Tabela AzureDiagnostics znajduje się w szczególności wrażliwych, to wpływ ten limit. Zwykle dzieje się tak, ponieważ szerokiej gamy źródeł danych są wysyłane do tego samego obszaru roboczego lub kilku źródeł pełne dane są wysyłane do tego samego obszaru roboczego. 

#### <a name="azure-data-factory"></a>Azure Data Factory  
Usługa Azure Data Factory, ze względu na zestaw bardzo szczegółowych dzienników, jest zasobem, który jest znany szczególnie dotyczy to ograniczenie. Nastąpiła zwłaszcza w naszym, poszczególne ustawienia diagnostyczne skonfigurowana przed określonych zasobów tryb włączone lub jawnie wybierając do używania trybu specyficznych dla zasobów ze względu na zgodność wstecznego:  
- *Parametry użytkownika zdefiniowane w odniesieniu do dowolnych działań w potoku*: będzie nową kolumnę utworzone dla każdego parametru unikatową nazwę użytkownika względem wszelkich działań. 
- *Działanie wejściami i wyjściami*: te różnią się działanie do działania i generowanie dużej liczby kolumn z powodu ich pełne charakter. 
 
Jako szersze propozycje rozwiązania poniżej, zalecane jest Aby migrować swoje dzienniki, aby jak najszybciej korzystania z trybu specyficznych dla zasobów. Nie jest możliwe to zrobić bezpośrednio, zamiast tymczasowe czy do izolowania dzienniki usługi ADF w obszarach roboczych, aby zminimalizować prawdopodobieństwo te dzienniki wywierania wpływu na inne typy dzienników są zbierane w obszarach roboczych. 
 
#### <a name="workarounds"></a>Rozwiązania
Rozwiązaniem krótkoterminowym, dopóki wszystkie usługi systemu Azure są włączone w trybie specyficznych dla zasobów, dla wszystkich usług nie jeszcze obsługi trybu specyficznych dla zasobów, jest zalecane oddzielne typy pełne dane publikowane przez te usługi w oddzielnych obszarach roboczych zmniejszyć możliwość osiągnięcia limitu.  
 
Dłuższy okres diagnostyki platformy Azure będzie na drodze do wszystkich usług platformy Azure, Obsługa trybu specyficznych dla zasobów. Zalecamy przejście do tego trybu tak szybko, jak to możliwe, aby zmniejszyć potencjał wpływa ten limit 500 kolumny.  


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach diagnostycznych platformy Azure](diagnostic-logs-overview.md)

