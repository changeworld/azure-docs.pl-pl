---
title: Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics
description: Informacje na temat przesyłania strumieniowego dzienników zasobów platformy Azure do obszaru roboczego Log Analytics w programie Azure Monitor.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 92de47041791c8b6c540844adb62391268b81c34
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200506"
---
# <a name="collect-azure-resource-logs-in-log-analytics-workspace-in-azure-monitor"></a>Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics w Azure Monitor
[Dzienniki zasobów](resource-logs-overview.md) na platformie Azure zapewniają rozbudowane, częste dane dotyczące wewnętrznej operacji zasobu platformy Azure. W tym artykule opisano zbieranie dzienników zasobów w Log Analytics obszarze roboczym, które umożliwiają analizowanie go przy użyciu innych danych monitorowania zebranych w dziennikach Azure Monitor przy użyciu zaawansowanych zapytań dzienników, a także do korzystania z innych funkcji Azure Monitor, takich jak alerty i wizualizacje. 


## <a name="what-you-can-do-with-resource-logs-in-a-workspace"></a>Co możesz zrobić z dziennikami zasobów w obszarze roboczym
Zbieranie dzienników zasobów w obszarze roboczym Log Analytics pozwala analizować dzienniki wszystkich zasobów platformy Azure razem i korzystać ze wszystkich funkcji dostępnych dla [Azure monitor dzienników](data-platform-logs.md) , które obejmują następujące elementy:

* **Zapytania dzienników** — Twórz [zapytania dzienników](../log-query/log-query-overview.md) przy użyciu zaawansowanego języka zapytań, aby szybko analizować i uzyskiwać wgląd w dane diagnostyczne i analizować je z danymi zebranymi z innych źródeł w Azure monitor.
* **Alerty** — Uzyskaj aktywne powiadomienie o krytycznych warunkach i wzorcach zidentyfikowanych w dziennikach zasobów przy użyciu [alertów dziennika w Azure monitor](alerts-log.md).
* **Wizualizacje** — Przypnij wyniki zapytania dziennika do pulpitu nawigacyjnego platformy Azure lub Dołącz go do skoroszytu jako część raportu interaktywnego.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze tego nie zrobiono, należy [utworzyć nowy obszar roboczy](../learn/quick-create-workspace.md) . Obszar roboczy nie musi znajdować się w tej samej subskrypcji co zasób wysyła dzienniki, dopóki użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji.

## <a name="create-a-diagnostic-setting"></a>Utwórz ustawienie diagnostyczne
Dzienniki zasobów nie są domyślnie zbierane. Zbierz je w obszarze roboczym Log Analytics i innych miejscach docelowych, tworząc ustawienia diagnostyczne dla zasobu platformy Azure. Aby uzyskać szczegółowe informacje [, zobacz Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md) .

## <a name="collection-mode"></a>Tryb kolekcji
Dane zbierane w obszarze roboczym Log Analytics są przechowywane w tabelach zgodnie z opisem w temacie [struktura dzienników Azure monitor](../log-query/logs-structure.md). Tabele używane przez dzienniki zasobów zależą od typu kolekcji używanej przez zasób:

- Diagnostyka Azure — wszystkie dane są zapisywane w tabeli _AzureDiagnostics_ .
- Specyficzne dla zasobów — dane są zapisywane w pojedynczej tabeli dla każdej kategorii zasobu.

### <a name="azure-diagnostics-mode"></a>Tryb Diagnostyka Azure 
W tym trybie wszystkie dane z dowolnego [Ustawienia diagnostycznego](diagnostic-settings.md) zostaną zebrane w tabeli _AzureDiagnostics_ . Jest to Starsza Metoda używana dzisiaj przez większość usług platformy Azure.

Ponieważ wiele typów zasobów wysyła dane do tej samej tabeli, jego schemat jest nadzbiorem schematów wszystkich typów zbieranych danych.

Rozważmy następujący przykład, w którym ustawienia diagnostyczne są zbierane w tym samym obszarze roboczym dla następujących typów danych:

- Dzienniki inspekcji usługi 1 (schemat zawierający kolumny A, B i C)  
- Dzienniki błędów usługi 1 (schemat zawierający kolumny D, E i F)  
- Dzienniki inspekcji usługi 2 (schemat zawierający kolumny G, H i i)  

Tabela AzureDiagnostics będzie wyglądać następująco:  

| ResourceProvider    | Kategoria     | A  | B  | C  | D  | Adres  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | pierwszym | W1 | E1 |    |    |    |
| Microsoft. Językowej2 | AuditLogs    |    |    |    |    |    |    | J1 | K1 | L1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | Q2 | W2 | punktu |    |    |    |
| Microsoft. Językowej2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | Scale |
| Microsoft. Service1 | AuditLogs    | x5 | Y5 | z5 |    |    |    |    |    |    |
| Przyciski ... |

### <a name="resource-specific"></a>Specyficzne dla zasobów
W tym trybie poszczególne tabele w wybranym obszarze roboczym są tworzone dla każdej kategorii wybranej w ustawieniu diagnostyki. Ta metoda jest zalecana, ponieważ znacznie ułatwia pracę z danymi w dziennikach zapytań, zapewnia lepszą wykrywalność schematów i ich strukturę, zwiększa wydajność zarówno opóźnienia pozyskiwania, jak i czasy wykonywania zapytań oraz możliwość przyznawania praw RBAC na określona tabela. Wszystkie usługi platformy Azure zostaną ostatecznie przemigrowane do trybu specyficznego dla zasobów. 

W powyższym przykładzie powstaje trzy tabele:
 
- *Service1AuditLogs* tabeli w następujący sposób:

    | Dostawca zasobów | Kategoria | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | Y1 | z1 |
    | Service1 | AuditLogs | x5 | Y5 | z5 |
    | Przyciski ... |

- *Service1ErrorLogs* tabeli w następujący sposób:  

    | Dostawca zasobów | Kategoria | D | Adres | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  pierwszym | W1 | E1 |
    | Service1 | ErrorLogs |  Q2 | W2 | punktu |
    | Przyciski ... |

- *Service2AuditLogs* tabeli w następujący sposób:  

    | Dostawca zasobów | Kategoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Językowej2 | AuditLogs | J1 | K1 | L1|
    | Językowej2 | AuditLogs | j3 | k3 | Scale|
    | Przyciski ... |



### <a name="select-the-collection-mode"></a>Wybierz tryb kolekcji
Większość zasobów platformy Azure będzie zapisywać dane w obszarze roboczym w trybie **diagnostycznym platformy Azure** lub na **konkretnym poziomie zasobów** bez konieczności wyboru. Zapoznaj się z [dokumentacją dla każdej usługi](diagnostic-logs-schema.md) , aby uzyskać szczegółowe informacje na temat używanego trybu. Wszystkie usługi platformy Azure ostatecznie będą korzystać z trybu specyficznego dla zasobów. W ramach tego przejścia niektóre zasoby umożliwią wybranie trybu w ustawieniu diagnostyki. Należy określić tryb specyficzny dla zasobów dla nowych ustawień diagnostycznych, ponieważ ułatwia to zarządzanie danymi i może pomóc w uniknięciu złożonych migracji w późniejszym czasie.
  
   ![Selektor trybu ustawień diagnostycznych](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Obecnie można wybrać tylko **diagnostykę platformy Azure** i tryb **specyficzny dla zasobów** podczas konfigurowania ustawień diagnostycznych w Azure Portal. W przypadku skonfigurowania ustawienia za pomocą interfejsu wiersza polecenia, programu PowerShell lub API REST będzie on domyślnie używany jako **Diagnostyka platformy Azure**.

Istniejące ustawienie diagnostyczne można zmodyfikować z trybem specyficznym dla zasobów. W takim przypadku dane, które zostały już zebrane, pozostaną w tabeli _AzureDiagnostics_ , dopóki nie zostanie usunięte zgodnie z ustawieniami przechowywania dla obszaru roboczego. Nowe dane zostaną zebrane do dedykowanej tabeli. Użyj operatora [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) do wykonywania zapytań dotyczących danych w obu tabelach.

Przejdź do blogu [aktualizacji platformy Azure](https://azure.microsoft.com/updates/) , aby poznać anonse dotyczące usług platformy Azure obsługujących tryb specyficzny dla zasobów.

### <a name="column-limit-in-azurediagnostics"></a>Limit kolumn w AzureDiagnostics
Dla każdej tabeli w dziennikach Azure Monitor istnieje limit właściwości 500. Po osiągnięciu tego limitu wszystkie wiersze zawierające dane z właściwości spoza pierwszej 500 zostaną porzucone w czasie pozyskiwania. Tabela *AzureDiagnostics* jest szczególnie podatna na ten limit, ponieważ obejmuje ona właściwości wszystkich usług platformy Azure, które są w niej zapisywane.

W przypadku zbierania dzienników diagnostycznych z wielu usług _AzureDiagnostics_ może przekroczyć ten limit, a dane zostaną pominięte. Dopóki wszystkie usługi platformy Azure nie obsługują trybu określonego zasobów, należy skonfigurować zasoby do zapisu w wielu obszarach roboczych, aby zmniejszyć prawdopodobieństwo osiągnięcia limitu liczby kolumn 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ze względu na bardzo szczegółowy zestaw dzienników to usługa, która jest znana, aby napisać dużą liczbę kolumn i potencjalnie spowodować przekroczenie limitu przez _AzureDiagnostics_ . W przypadku wszystkich ustawień diagnostycznych skonfigurowanych przed włączeniem trybu określonego zasobu zostanie utworzona nowa kolumna dla każdego unikatowego parametru użytkownika dla każdego działania. Zostanie utworzona więcej kolumn z powodu pełnego charakteru danych wejściowych i wyjściowych działań.
 
Aby jak najszybciej użyć trybu określonego dla zasobu, należy przeprowadzić migrację dzienników. Jeśli nie możesz tego zrobić natychmiast, przejściową alternatywą jest izolowanie dzienników Azure Data Factory w ich własnym obszarze roboczym, aby zminimalizować prawdopodobieństwo, że te dzienniki wpływają na inne typy dzienników gromadzone w obszarach roboczych.


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dziennikach zasobów platformy Azure, zobacz [Omówienie dzienników zasobów platformy Azure](resource-logs-overview.md).
* Aby utworzyć ustawienie diagnostyczne w celu zbierania dzienników zasobów do obszaru roboczego Log Analytics, zobacz [Tworzenie ustawienia diagnostycznego w celu zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md).
