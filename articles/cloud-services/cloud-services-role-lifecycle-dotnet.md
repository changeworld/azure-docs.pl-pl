---
title: Obsługi zdarzeń cyklu życia usługi w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak metod cyklu życia roli usługi w chmurze można użyć w .NET
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: eb78c05df3b3cdf3887334c11bdabd5cebb74747
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843448"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Dostosowywanie cyklu życia roli Sieć Web lub Proces roboczy na platformie .NET
Podczas tworzenia roli procesu roboczego, można rozszerzyć [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) klasy, która udostępnia metody pominięcia, które pozwalają na odpowiadanie na zdarzenia cyklu życia. Dla ról sieć web tej klasy jest opcjonalny, dlatego możesz korzystać na odpowiadanie na zdarzenia cyklu życia.

## <a name="extend-the-roleentrypoint-class"></a>Rozszerzenie klasy RoleEntryPoint
[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) klasa zawiera metody, które są wywoływane przez platformę Azure podczas jego **uruchamia**, **uruchamia**, lub **zatrzymuje** roli sieci web lub procesu roboczego. Opcjonalnie można zastąpić te metody do zarządzania roli inicjowania, roli zamknięcia sekwencji lub wątku wykonawczego roli. 

Podczas rozszerzania **RoleEntryPoint**, należy zwrócić uwagę na następujące metody:

* [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) i [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) metody zwracać wartość logiczną, można powrócić **false** z tych metod.
  
   Jeśli kod zwraca **false**, nagle zakończenia procesu roli, bez konieczności uruchamiania żadnych zamykania może być w miejscu. Ogólnie rzecz biorąc, należy unikać zwracanie **false** z **OnStart** metody.
* Wszelkie nieprzechwycony wyjątek w obrębie przeciążenia **RoleEntryPoint** metoda jest traktowana jako nieobsługiwany wyjątek.
  
   Jeśli wystąpi wyjątek w jednej z metod cyklu życia, Azure zostanie podniesiony [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) zdarzeń, a następnie proces zostanie zakończony. Po roli użytkownika został przełączony w tryb offline, zostanie ono uruchomione na platformie Azure. Gdy wystąpi nieobsługiwany wyjątek, [zatrzymywanie](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) nie zdarzenia i **OnStop** nie jest wywoływana metoda.

Jeśli roli użytkownika nie można uruchomić lub jest odtwarzania między inicjowanie zajęte, a stanów zatrzymywania, kodu może zgłaszanie nieobsługiwany wyjątek w obrębie jednego zdarzenia cyklu życia każdej roli ponownym uruchomieniu. W takim przypadku należy użyć [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) zdarzenie, aby ustalić przyczynę wyjątku i odpowiednią obsługę. Roli użytkownika może też zwracać z [Uruchom](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metodę, która powoduje, że rola do ponownego uruchomienia. Aby uzyskać więcej informacji na temat stanów wdrożenia, zobacz [typowe problemy z którego przyczyna role do odtworzenia](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Jeśli używasz **Azure Tools dla programu Microsoft Visual Studio** do tworzenia aplikacji, automatycznie rozszerzenia szablonów projektu roli **RoleEntryPoint** klasy, w *WebRole.cs* i *WorkerRole.cs* plików.
> 
> 

## <a name="onstart-method"></a>OnStart — metoda
**OnStart** metoda jest wywoływana, gdy wystąpienie roli w tryb online na platformie Azure. Podczas wykonywania kodu OnStart wystąpienia roli jest oznaczony jako **zajęty** i żaden ruch zewnętrzny zostanie skierowany do niej przez moduł równoważenia obciążenia. Należy przesłonić tę metodę do wykonywania pracy inicjowania, takie jak wdrażanie programów obsługi zdarzeń i uruchamianie [diagnostyki Azure](cloud-services-how-to-monitor.md).

Jeśli **OnStart** zwraca **true**, wystąpienie został pomyślnie zainicjowany i Azure wywołuje **RoleEntryPoint.Run** metody. Jeśli **OnStart** zwraca **false**, roli kończy się bezpośrednio, bez wykonywania żadnych planowanego zamknięcia sekwencji.

Poniższy przykładowy kod przedstawia sposób przesłonięcia **OnStart** metody. Ta metoda umożliwia skonfigurowanie i rozpoczyna się monitora diagnostycznego w momencie wystąpienia roli uruchamia i konfiguruje transferu danych logowania do konta magazynu:

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
**OnStop** metoda jest wywoływana po wystąpienia roli została podjęta w trybie offline przez platformę Azure i przed kończy proces. Mogą przesłaniać tę metodę do wywołania kodu wymaganego dla wystąpienia roli do zamknięty.

> [!IMPORTANT]
> Kodu uruchamianego w **OnStop** metoda ma przez ograniczony czas na zakończenie wywołanego przyczyn innych niż zamknięcia zainicjowanego przez użytkownika. Po upływie tego czasu, proces jest zakończony, dlatego należy się upewnić, że kod w **OnStop** metody można szybko uruchomić lub zaakceptować nie działa do zakończenia. **OnStop** metoda jest wywoływana po wykonaniu **zatrzymywanie** zdarzenia.
> 
> 

## <a name="run-method"></a>Run — metoda
Można zastąpić **Uruchom** metody do wykonania wątku długotrwałe dla swojego wystąpienia roli.

Zastępowanie **Uruchom** — metoda nie jest wymagana; Domyślna implementacja uruchamia wątku, który jest w stanie uśpienia nieskończona. Jeśli zastąpienie **Uruchom** metody kodu powinna zablokować nieskończoność. Jeśli **Uruchom** metoda zwróci wartość, rola zostanie automatycznie bezpiecznie odtworzony; innymi słowy, zgłasza Azure **zatrzymywanie** zdarzeń i wywołania **OnStop** metody tak, aby sekwencjach zamknięcia może zostać wykonana, przed roli do trybu offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementacja metody cyklu życia ASP.NET dla roli sieci web
Można użyć metody cyklu życia ASP.NET, oprócz tych zapewnianych przez **RoleEntryPoint** klasy zarządzania sekwencjami inicjowania i zamykania dla roli sieci web. Może to być przydatne do celów zgodności, jeśli są przenoszenia istniejących aplikacji ASP.NET do platformy Azure. Metody cyklu życia ASP.NET są wywoływane z poziomu **RoleEntryPoint** metody. **Aplikacji\_Start** metoda jest wywoływana po wykonaniu **RoleEntryPoint.OnStart** zakończeniu działania metody. **Aplikacji\_zakończenia** metoda jest wywoływana przed **RoleEntryPoint.OnStop** metoda jest wywoływana.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [Utwórz pakiet usługi chmury](cloud-services-model-and-package.md).

