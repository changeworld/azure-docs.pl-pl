---
title: Obsługa zdarzeń cyklu życia usługi w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można metody cyklu życia roli usługi w chmurze na platformie .NET
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 13f500b32bb85bdc0f84b812ef4ef9188a257771
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798019"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Dostosowywanie cyklu życia roli Sieć Web lub Proces roboczy na platformie .NET
Podczas tworzenia roli procesu roboczego, możesz rozszerzyć [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) klasę, która zapewnia metody do zastąpienia, które umożliwiają reagowanie na zdarzenia cyklu życia. Dla ról sieci web ta klasa jest opcjonalne, więc musisz ją wykorzystać w celu reagowania na zdarzenia cyklu życia.

## <a name="extend-the-roleentrypoint-class"></a>Rozszerzenie klasy RoleEntryPoint
[RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) klasa zawiera metody, które są wywoływane przez platformę Azure podczas jego **uruchamia**, **uruchamia**, lub **zatrzymuje** rola sieć web lub proces roboczy. Opcjonalnie można zastąpić tych metod do zarządzania roli inicjowania, sekwencji zamykania roli lub wątek wykonywania roli. 

Podczas rozszerzania **RoleEntryPoint**, jednak należy zwrócić uwagę na następujące metody:

* [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) i [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) metody zwracają wartość logiczną, więc istnieje możliwość powrotu **false** z tych metod.
  
   Jeśli Twój kod zwraca **false**nagle zakończenia procesu roli, bez konieczności uruchamiania dowolnej sekwencji zamykania systemu mogą mieć w miejscu. Ogólnie rzecz biorąc, należy unikać, zwracając **false** z **OnStart** metody.
* Dowolny nieprzechwycony wyjątek w obrębie przeciążenia **RoleEntryPoint** metoda jest traktowana jako nieobsługiwany wyjątek.
  
   Jeśli wystąpi wyjątek w ramach jednej z metod cyklu życia, Azure zgłosi [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) zdarzeń, a następnie proces zostanie zakończony. Po swojej roli została podjęta w trybie offline, zostanie ono uruchomione na platformie Azure. Po wystąpieniu nieobsługiwanego wyjątku [zatrzymywanie](/previous-versions/azure/reference/ee758136(v=azure.100)) zdarzeń nie jest inicjowane i **OnStop** nie jest wywoływana metoda.

Jeśli Twojej roli nie uruchamia się lub jest odtwarzane między inicjowanie, zajęta i Stany zatrzymywania, kod może zgłaszanie nieobsługiwany wyjątek w obrębie jednego ze zdarzeń cyklu życia każdego rolę ponownym uruchomieniu. W takim przypadku należy użyć [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) zdarzenie, aby ustalić przyczynę wyjątku i odpowiednio go obsłużyć. Roli użytkownika może również zwracać z [Uruchom](/previous-versions/azure/reference/ee772746(v=azure.100)) metody, która powoduje, że rola zostanie uruchomiona ponownie. Aby uzyskać więcej informacji na temat stany wdrożeń zobacz [typowe problemy z którym Przyczyna role do odtwarzania](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Jeśli używasz **Azure Tools for Microsoft Visual Studio** do tworzenia aplikacji, automatyczne rozszerzanie szablonów projektu roli **RoleEntryPoint** klasy, w  *WebRole.cs* i *WorkerRole.cs* plików.
> 
> 

## <a name="onstart-method"></a>Metoda OnStart
**OnStart** metoda jest wywoływana, gdy Twoje wystąpienie roli w tryb online przez platformę Azure. Podczas wykonywania kodu dla metody OnStart wystąpienie roli jest oznaczony jako **zajęty** i żaden ruch zewnętrzny nastąpi przekierowanie do niej przez moduł równoważenia obciążenia. Możesz zastąpić tę metodę w celu wykonania prac inicjowania, takich jak implementacja procedury obsługi zdarzeń i uruchamianie [diagnostyki Azure](cloud-services-how-to-monitor.md).

Jeśli **OnStart** zwraca **true**, wystąpienie jest pomyślnie zainicjowany i platforma Azure wywołuje **RoleEntryPoint.Run** metody. Jeśli **OnStart** zwraca **false**, rola kończy działanie natychmiast, bez wykonywania żadnych sekwencje planowanego zamknięcia.

Poniższy przykład kodu przedstawia sposób przesłonięcia **OnStart** metody. Ta metoda umożliwia skonfigurowanie i uruchamia monitor diagnostyczny, gdy wystąpienie roli zostanie uruchomione i konfiguruje transferu danych logowania do konta magazynu:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop — metoda
**OnStop** metoda jest wywoływana po wystąpienie roli jest zajęty w trybie offline przez platformę Azure i przed kończy proces. Można zastąpić tę metodę, aby wywołać kod wymagany do wystąpienia roli w celu zamknięta.

> [!IMPORTANT]
> Kod uruchomiony w **OnStop** metoda ma ograniczony czas na zakończenie wywołanego powodów innych niż zamknięcia zainicjowanego przez użytkownika. Po upływie tego czasu, proces zostanie zakończony, więc musisz upewnić się, że kod w **OnStop** metody można szybko uruchomić lub zaakceptować tolerowane. **OnStop** metoda jest wywoływana po **zatrzymywanie** zdarzenie jest wywoływane.
> 
> 

## <a name="run-method"></a>Run — metoda
Można zastąpić **Uruchom** metody do zaimplementowania wątku długotrwałych wystąpienia roli.

Zastępowanie **Uruchom** metoda nie jest wymagana; Domyślna implementacja rozpoczyna się wątek, który jest w stanie uśpienia nieskończoność. Jeśli zastąpisz **Uruchom** metody kodu powinna blokować w sposób ciągły. Jeśli **Uruchom** metoda zwróci wartość, rola jest automatycznie bez problemu zmieniała odtwarzania; innymi słowy, zgłasza Azure **zatrzymywanie** zdarzenia i wywołania **OnStop** metoda tak, aby Twoje zamknięcie sekwencji mogą być wykonywane przed roli do trybu offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementacja metody cyklu życia programu ASP.NET dla roli sieci web
Można użyć metod cyklu życia programu ASP.NET, oprócz tych zapewnianych przez **RoleEntryPoint** klasy zarządzania sekwencjami inicjowania i zamykania dla roli sieci web. Może to być przydatne do zapewnienia zgodności, jeśli są Przenoszenie istniejących aplikacji ASP.NET na platformie Azure. Metody cyklu życia ASP.NET są wywoływane z poziomu **RoleEntryPoint** metody. **Aplikacji\_Start** metoda jest wywoływana po **RoleEntryPoint.OnStart** zakończeniu działania metody. **Aplikacji\_zakończenia** metoda jest wywoływana przed **RoleEntryPoint.OnStop** metoda jest wywoływana.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [Utwórz pakiet usługi w chmurze](cloud-services-model-and-package.md).

