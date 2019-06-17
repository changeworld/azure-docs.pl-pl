---
title: Omówienie dzienniki diagnostyczne platformy Azure
description: Dowiedz się więcej o dziennikach diagnostycznych platformy Azure w usłudze Azure Monitor i jak można je zrozumieć zdarzenia występujące w obrębie zasobu platformy Azure.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244878"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Omówienie dzienniki diagnostyczne platformy Azure

**Dzienniki diagnostyczne** zapewniają zaawansowane, często dane dotyczące operacji zasobu platformy Azure. Usługa Azure Monitor sprawia, że dwa rodzaje dzienników diagnostycznych:

* **Dzienniki dzierżawy** — te dzienniki pochodzą z usługi na poziomie dzierżawy, które znajdują się poza subskrypcją platformy Azure, takich jak dzienniki usługi Azure Active Directory.
* **Dzienniki zasobów** — te dzienniki pochodzą z usług platformy Azure, które wdrażanie zasobów w ramach subskrypcji platformy Azure, takich jak sieciowe grupy zabezpieczeń lub konta magazynu.

    ![Dzienniki diagnostyczne zasobu a inne typy dzienników](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Zawartość tych dzienników jest zależna od typu usługi i zasobów platformy Azure. Na przykład dwa typy dzienników diagnostycznych są liczniki reguł sieciowej grupy zabezpieczeń i inspekcji w usłudze Key Vault.

Dzienniki te różnią się od [dziennika aktywności](activity-logs-overview.md). Dziennik aktywności zapewnia wgląd w operacje, które zostały wykonane na zasoby w subskrypcji przy użyciu usługi Resource Manager, na przykład tworzenia maszyny wirtualnej lub usuwanie aplikacji logiki. Dziennik aktywności jest dziennika poziomu subskrypcji. Zasób poziom dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonywanych w ramach tego samego zasobu, na przykład wprowadzenie klucza tajnego z usługi Key Vault.

Dzienniki te różnią się także z poziomu systemu operacyjnego gościa, dzienniki diagnostyczne. System operacyjny gościa, dzienniki diagnostyczne są te zebranych przez agenta uruchomionego na maszynie wirtualnej lub innych obsługiwany typ zasobu. Dzienniki diagnostyczne na poziomie zasobów wymagają nie agenta i przechwytywania danych specyficznych dla zasobów z platformą Azure, natomiast dzienniki diagnostyczne na poziomie systemu operacyjnego gościa przechwytywanie danych z systemu operacyjnego i aplikacji uruchomionych na maszynie wirtualnej.

Nie wszystkie usługi pomocy technicznej opisane w tym miejscu dzienniki diagnostyczne. [Ten artykuł zawiera listę sekcji usług, które obsługują dzienniki diagnostyczne](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Co można zrobić za pomocą dzienników diagnostycznych
Oto kilka rzeczy, które można zrobić za pomocą dzienników diagnostycznych:

![Logiczne umieszczania dzienników diagnostycznych](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Zapisywanie ich [ **konta magazynu** ](../../azure-monitor/platform/archive-diagnostic-logs.md) do wglądu, inspekcji czy ręcznie. Można określić przy użyciu czasu (w dniach) przechowywania **ustawień diagnostycznych zasobu**.
* [Stream im **usługi Event Hubs** ](diagnostic-logs-stream-event-hubs.md) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowe, takie jak Power BI.
* Analizuj je za pomocą [usługi Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), gdzie dane są zapisywane bezpośrednio do usługi Azure Monitor bez konieczności najpierw zapisać dane do magazynu.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Można użyć konta magazynu lub przestrzeni nazw usługi Event Hubs, która nie znajduje się w tej samej subskrypcji co emitowane dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć odpowiedni dostęp RBAC do obu subskrypcji.

> [!NOTE]
>  Obecnie nie można zarchiwizować dzienniki przepływu sieciowych do konta magazynu, który znajduje się za zabezpieczonej sieci wirtualnej.

## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Dzienniki diagnostyczne zasobu są skonfigurowane za pomocą ustawień diagnostycznych zasobu. Dzienniki diagnostyczne dzierżawy są skonfigurowane przy użyciu ustawienia diagnostyczne dzierżawy. **Ustawienia diagnostyczne** kontroli usługi:

* Której dzienniki diagnostyczne i metryki są wysyłane (konto magazynu, usługa Event Hubs i/lub usługi Azure Monitor).
* Kategorie dziennika, które są wysyłane i tego, czy też są wysyłane dane metryk.
* Jak długo każda kategoria dziennika powinny być przechowywane na koncie magazynu.
    - Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni z zakresu od 1 do 365.
    - Jeśli ustawiono zasady przechowywania, ale przechowywania dzienników na koncie magazynu jest wyłączona (na przykład, jeśli tylko są zaznaczone opcje usługi Event Hubs lub usługi Log Analytics), zasad przechowywania przyniosło żadnego skutku.
    - Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu.

Te ustawienia są skonfigurowane przy użyciu ustawień diagnostycznych w witrynie portal, za pomocą poleceń programu Azure PowerShell i interfejsu wiersza polecenia, lub przy użyciu [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Obsługiwane usługi, kategorie i schematy dla dzienników diagnostycznych

[Ten artykuł](../../azure-monitor/platform/diagnostic-logs-schema.md) pełną listę obsługiwanych usług i Rejestruj kategorie i schematy używane przez te usługi.

## <a name="next-steps"></a>Kolejne kroki

* [Stream dzienników diagnostycznych zasobów do **usługi Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobów przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analizowanie dzienników z usługi Azure storage z usługą Azure Monitor](collect-azure-metrics-logs.md)
