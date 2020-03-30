---
title: Zbieranie dzienników zasobów platformy Azure w obszarze roboczym usługi Log Analytics
description: Dowiedz się, jak przesyłać strumieniowo dzienniki zasobów platformy Azure do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248595"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Zbieranie dzienników platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dzienniki aktywności platformy Azure i dzienniki zasobów, zawierają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, na których zależą. W tym artykule opisano zbieranie dzienników zasobów w obszarze roboczym usługi Log Analytics, który umożliwia analizowanie ich przy użyciu innych danych monitorowania zebranych w dziennikach usługi Azure Monitor przy użyciu zaawansowanych zapytań dziennika, a także w celu wykorzystania innych funkcji usługi Azure Monitor, takich jak alerty i Wizualizacje. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Co można zrobić z dziennikami platformy w obszarze roboczym
Zbieranie dzienników platformy do obszaru roboczego usługi Log Analytics umożliwia analizowanie dzienników wszystkich zasobów platformy Azure razem i korzystanie ze wszystkich funkcji dostępnych [dla dzienników usługi Azure Monitor,](data-platform-logs.md) które obejmują następujące czynności:

* **Kwerendy dziennika** — tworzenie [zapytań dziennika](../log-query/log-query-overview.md) przy użyciu zaawansowanego języka zapytań, aby szybko analizować i uzyskiwać wgląd w dane diagnostyczne i analizować je za pomocą danych zebranych z innych źródeł w usłudze Azure Monitor.
* **Alerty** — otrzymuj proaktywne powiadomienia o krytycznych warunkach i wzorcach zidentyfikowanych w dziennikach zasobów przy użyciu [alertów dziennika w usłudze Azure Monitor.](alerts-log.md)
* **Wizualizacje** — przypnij wyniki kwerendy dziennika do pulpitu nawigacyjnego platformy Azure lub dołącz ją do skoroszytu jako część raportu interaktywnego.

## <a name="prerequisites"></a>Wymagania wstępne
Musisz [utworzyć nowy obszar roboczy,](../learn/quick-create-workspace.md) jeśli jeszcze go nie masz. Obszar roboczy nie musi być w tej samej subskrypcji co dzienniki wysyłania zasobu, o ile użytkownik, który konfiguruje to ustawienie, ma odpowiedni dostęp RBAC do obu subskrypcji.

## <a name="create-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego
Wysyłaj dzienniki platformy do obszaru roboczego usługi Log Analytics i innych miejsc docelowych, tworząc ustawienie diagnostyczne dla zasobu platformy Azure. Zobacz [Tworzenie ustawień diagnostycznych do zbierania dzienników i metryk na platformie Azure,](diagnostic-settings.md) aby uzyskać szczegółowe informacje.


## <a name="activity-log-collection"></a>Zbieranie dzienników aktywności
Dziennik aktywności można wysłać z dowolnej pojedynczej subskrypcji do maksymalnie pięciu obszarów roboczych usługi Log Analytics. Dane dziennika zasobów zebrane w obszarze roboczym usługi Log Analytics są przechowywane w tabeli **AzureActivity.** 

## <a name="resource-log-collection-mode"></a>Tryb zbierania dzienników zasobów
Dane dziennika zasobów zebrane w obszarze roboczym usługi Log Analytics są przechowywane w tabelach zgodnie z opisem w [strukturze dzienników usługi Azure Monitor](../log-query/logs-structure.md). Tabele używane przez dzienniki zasobów zależą od typu kolekcji, której używa zasób:

- Diagnostyka platformy Azure — wszystkie zapisane dane są przesyłane do tabeli _AzureDiagnostics._
- Specyficzne dla zasobu — dane są zapisywane w pojedynczej tabeli dla każdej kategorii zasobu.

### <a name="azure-diagnostics-mode"></a>Tryb diagnostyki platformy Azure 
W tym trybie wszystkie dane z dowolnego [ustawienia diagnostycznego](diagnostic-settings.md) będą zbierane w tabeli _AzureDiagnostics._ Jest to starsza metoda używana obecnie przez większość usług platformy Azure.

Ponieważ wiele typów zasobów wysyła dane do tej samej tabeli, jego schemat jest nadzbiorem schematów wszystkich różnych typów danych, które są zbierane.

Rozważmy następujący przykład, w którym ustawienia diagnostyczne są zbierane w tym samym obszarze roboczym dla następujących typów danych:

- Dzienniki inspekcji usługi 1 (ze schematem składającym się z kolumn A, B i C)  
- Dzienniki błędów usługi 1 (o schemacie składającym się z kolumn D, E i F)  
- Dzienniki inspekcji usługi 2 (o schemacie składającym się z kolumn G, H i I)  

Tabela AzureDiagnostics będzie wyglądać następująco:  

| ResourceProvider    | Kategoria     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | Dzienniki inspekcji    | x1 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft.Service1 | Dzienniki błędów    |    |    |    | Q1 | w 1 | e1 |    |    |    |
| Microsoft.Service2 | Dzienniki inspekcji    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | Dzienniki błędów    |    |    |    | W II kw. | w2 | e2 |    |    |    |
| Microsoft.Service2 | Dzienniki inspekcji    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | Dzienniki inspekcji    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specyficzne dla zasobu
W tym trybie dla każdej kategorii wybranej w ustawieniu diagnostycznym tworzone są poszczególne tabele w wybranym obszarze roboczym. Ta metoda jest zalecana, ponieważ znacznie ułatwia pracę z danymi w zapytaniach dziennika, zapewnia lepszą wykrywalność schematów i ich struktury, poprawia wydajność zarówno w czasie opóźnienia pozyskiwania, jak i zapytania, a także możliwość udzielania praw RBAC na konkretnej tabeli. Wszystkie usługi platformy Azure zostaną ostatecznie przeniesione do trybu specyficznego dla zasobu. 

Powyższy przykład spowoduje utworzenie trzech tabel:
 
- Table *Service1AuditLogs* w następujący sposób:

    | Dostawca zasobów | Kategoria | A | B | C |
    | -- | -- | -- | -- | -- |
    | Serwis1 | Dzienniki inspekcji | x1 | y1 | Z1 |
    | Serwis1 | Dzienniki inspekcji | x5 | y5 | z5 |
    | ... |

- Tabela *Service1ErrorLogs* w następujący sposób:  

    | Dostawca zasobów | Kategoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Serwis1 | Dzienniki błędów |  Q1 | w 1 | e1 |
    | Serwis1 | Dzienniki błędów |  W II kw. | w2 | e2 |
    | ... |

- Tabela *Service2AuditLogs* w następujący sposób:  

    | Dostawca zasobów | Kategoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Serwis2 | Dzienniki inspekcji | j1 | k1 | l1|
    | Serwis2 | Dzienniki inspekcji | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Wybierz tryb zbierania
Większość zasobów platformy Azure będzie zapisywać dane w obszarze roboczym w **trybie** **diagnostyki platformy Azure** lub specyficzne dla zasobów bez podawania wyboru. Szczegółowe informacje na temat używanego trybu można znaleźć w [dokumentacji każdej usługi.](diagnostic-logs-schema.md) Wszystkie usługi platformy Azure po pewnym czasie użyją trybu specyficznego dla zasobów. W ramach tego przejścia niektóre zasoby umożliwiają wybranie trybu w ustawieniu diagnostycznym. Należy określić tryb specyficzny dla zasobów dla wszystkich nowych ustawień diagnostycznych, ponieważ ułatwia to zarządzanie danymi i może pomóc uniknąć złożonych migracji w późniejszym terminie.
  
   ![Selektor trybu ustawień diagnostycznych](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Obecnie **diagnostyki platformy Azure** i tryb **specyficzny dla zasobów** można wybrać tylko podczas konfigurowania ustawienia diagnostycznego w witrynie Azure portal. Jeśli ustawienie zostanie skonfigurowane przy użyciu interfejsu WIERSZA POLECENIA, programu PowerShell lub interfejsu API odpoczynku, domyślnie będzie ono **konfigurowane**przez diagnostykę platformy Azure .

Istniejące ustawienie diagnostyczne można zmodyfikować do trybu specyficznego dla zasobu. W takim przypadku dane, które zostały już zebrane pozostanie w _azurediagnostics_ tabeli, dopóki nie zostanie usunięty zgodnie z ustawieniami przechowywania dla obszaru roboczego. Nowe dane zostaną zebrane w specjalnej tabeli. Operator [unii](https://docs.microsoft.com/azure/kusto/query/unionoperator) służy do wykonywania zapytań o dane w obu tabelach.

Kontynuuj oglądanie [bloga aktualizacje platformy Azure](https://azure.microsoft.com/updates/) dla ogłoszeń dotyczących usług platformy Azure obsługujących tryb specyficzny dla zasobów.

### <a name="column-limit-in-azurediagnostics"></a>Limit kolumn w usłudze AzureDiagnostics
Istnieje limit właściwości 500 dla dowolnej tabeli w dziennikach usługi Azure Monitor. Po osiągnięciu tego limitu wszystkie wiersze zawierające dane z dowolną właściwością poza pierwszą 500 zostaną usunięte po czasie pozyskiwania. *Tabela AzureDiagnostics* jest szczególnie podatna na ten limit, ponieważ zawiera właściwości dla wszystkich usług platformy Azure piszących do niej.

Jeśli zbierasz dzienniki zasobów z wielu usług, _AzureDiagnostics_ może przekroczyć ten limit, a dane zostaną pominięte. Dopóki wszystkie usługi platformy Azure nie obsługują trybu specyficznego dla zasobów, należy skonfigurować zasoby do zapisu w wielu obszarach roboczych, aby zmniejszyć możliwość osiągnięcia limitu kolumny 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory, ze względu na bardzo szczegółowy zestaw dzienników, to usługa, która jest znana do pisania dużej liczby kolumn i potencjalnie spowodować _AzureDiagnostics_ przekroczyć limit. Dla wszystkich ustawień diagnostycznych skonfigurowanych przed włączonym trybem specyficznym dla zasobu zostanie utworzona nowa kolumna dla każdego parametru użytkownika o unikatowym imieniu względem dowolnej aktywności. Więcej kolumn zostanie utworzonych ze względu na pełny charakter danych wejściowych i wyjściowych aktywności.
 
Należy przeprowadzić migrację dzienników, aby jak najszybciej użyć trybu specyficznego dla zasobu. Jeśli nie możesz tego zrobić natychmiast, tymczasową alternatywą jest wyizolowanie dzienników usługi Azure Data Factory do własnego obszaru roboczego, aby zminimalizować prawdopodobieństwo, że te dzienniki będą miały wpływ na inne typy dzienników zbierane w obszarach roboczych.


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach zasobów](platform-logs-overview.md).
* [Utwórz ustawienie diagnostyczne do zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md).
