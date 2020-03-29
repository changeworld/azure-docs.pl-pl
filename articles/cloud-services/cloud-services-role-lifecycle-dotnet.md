---
title: Obsługa zdarzeń cyklu życia usługi w chmurze | Dokumenty firmy Microsoft
description: Dowiedz się, jak w .NET można używać metod cyklu życia roli usługi w chmurze
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 0a9c32affc50a6d357d4160e00486c896d762e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75385817"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Dostosowywanie cyklu życia roli sieci Web lub pracownika w sieci .NET
Podczas tworzenia roli procesu roboczego rozszerza się klasa [RoleEntryPoint,](/previous-versions/azure/reference/ee758619(v=azure.100)) która udostępnia metody zastępowania, które umożliwiają reagowanie na zdarzenia cyklu życia. Dla ról sieci web ta klasa jest opcjonalna, więc należy jej używać do reagowania na zdarzenia cyklu życia.

## <a name="extend-the-roleentrypoint-class"></a>Rozszerzanie klasy programu RoleEntryPoint
Klasa [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) zawiera metody, które są wywoływane przez platformę Azure podczas **uruchamiania,** **uruchamiania**lub **zatrzymywania** roli sieci web lub procesu roboczego. Opcjonalnie można zastąpić te metody do zarządzania inicjowania roli, sekwencje zamykania ról lub wątku wykonywania roli. 

Podczas rozszerzania **programu RoleEntryPoint**należy pamiętać o następujących zachowaniach metod:

* [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) i [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) metody zwracają wartość logiczną, więc jest możliwe do zwrócenia **false** z tych metod.
  
   Jeśli kod zwraca **false**, proces roli jest nagle zakończony, bez uruchamiania sekwencji zamykania może mieć w miejscu. Ogólnie rzecz biorąc należy unikać zwracania **false** z **OnStart** metody.
* Każdy nieprześlicony wyjątek w ramach przeciążenia metody **Programu RoleEntryPoint** jest traktowany jako nieobsługiowany wyjątek.
  
   Jeśli wyjątek występuje w ramach jednej z metod cyklu życia, platforma Azure spowoduje podniesienie [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) zdarzenia, a następnie proces zostanie zakończony. Po przetraktowane w trybie offline roli zostanie ponownie uruchomiona przez platformę Azure. Gdy wystąpi nieobsługiwana wyjątek, [zatrzymanie](/previous-versions/azure/reference/ee758136(v=azure.100)) zdarzenie nie jest wywoływane, a **OnStop** metoda nie jest wywoływana.

Jeśli rola nie rozpoczyna się lub jest recyklingu między inicjowania, zajęty i zatrzymania stanów, kod może być zgłaszanie nieobsługiwana wyjątek w ramach jednego ze zdarzeń cyklu życia za każdym razem, gdy rola jest ponownie uruchamiana. W takim przypadku należy użyć [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) zdarzenia, aby określić przyczynę wyjątku i obsługiwać go odpowiednio. Twoja rola może również zwracać z [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) metody, co powoduje, że rola do ponownego uruchomienia. Aby uzyskać więcej informacji na temat stanów wdrażania, zobacz [Typowe problemy, które powodują, że role do recyklingu](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Jeśli używasz **narzędzia azure dla programu Microsoft Visual Studio** do tworzenia aplikacji, szablony projektu roli automatycznie rozszerzyć **RoleEntryPoint** klasy dla Ciebie, w *WebRole.cs* i *WorkerRole.cs* plików.
> 
> 

## <a name="onstart-method"></a>Metoda OnStart
**OnStart** Metoda jest wywoływana, gdy wystąpienie roli jest przenoszony do trybu online przez platformę Azure. Podczas wykonywania kodu OnStart, wystąpienie roli jest oznaczony jako **zajęty** i żaden ruch zewnętrzny nie zostanie skierowany do niego przez moduł równoważenia obciążenia. Tę metodę można zastąpić w celu wykonania pracy inicjującej, takiej jak implementowanie programów obsługi zdarzeń i [uruchamianie usługi Azure Diagnostics.](cloud-services-how-to-monitor.md)

Jeśli **OnStart** zwraca **wartość true,** wystąpienie zostanie pomyślnie zainicjowane, a platforma Azure wywołuje metodę **RoleEntryPoint.Run.** Jeśli **OnStart** zwraca **false,** rola kończy się natychmiast, bez wykonywania żadnych planowanych sekwencji zamykania.

Poniższy przykład kodu pokazuje, jak zastąpić **OnStart** metody. Ta metoda konfiguruje i uruchamia monitor diagnostyczny po uruchomieniu wystąpienia roli i konfiguruje transfer danych rejestrowania do konta magazynu:

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

## <a name="onstop-method"></a>Metoda OnStop
**OnStop** Metoda jest wywoływana po wystąpieniu roli została przetraktowana w trybie offline przez platformę Azure i przed zakończeniem procesu. Można zastąpić tę metodę, aby wywołać kod wymagany dla wystąpienia roli, aby czysto zamknąć.

> [!IMPORTANT]
> Kod uruchomiony w **OnStop** metody ma ograniczony czas, aby zakończyć, gdy jest wywoływana z powodów innych niż zamknięcia zainicjowane przez użytkownika. Po upływie tego czasu proces zostanie zakończony, więc należy upewnić się, że kod w **OnStop** metody można uruchomić szybko lub toleruje nie działa do zakończenia. **OnStop** Metoda jest wywoływana po **zatrzymanie** zdarzenia jest wywoływana.
> 
> 

## <a name="run-method"></a>Uruchom metodę
Można zastąpić **Run** metody zaimplementowania wątku długo działającego dla wystąpienia roli.

Zastępowanie **Run** metoda nie jest wymagane; domyślna implementacja rozpoczyna wątek, który śpi na zawsze. Jeśli nie zastąpić **Run** metody, kod powinien blokować przez czas nieokreślony. Jeśli **Run** metoda zwraca, rola jest automatycznie bezpiecznie recyklingu; innymi słowy Azure wywołuje **zatrzymanie** zdarzenia i wywołuje **OnStop** metody, dzięki czemu sekwencje zamknięcia mogą być wykonywane przed roli zostanie przesuń do trybu offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementowanie metod cyklu życia ASP.NET dla roli sieci Web
Oprócz metod dostępnych w programie RoleEntryPoint można używać metod cyklu życia ASP.NET, oprócz metod dostępnych w klasie **RoleEntryPoint,** aby zarządzać sekwencjami inicjowania i zamykania dla roli sieci Web. Może to być przydatne na potrzeby zgodności, jeśli przenosisz istniejącą aplikację ASP.NET na platformę Azure. Metody cyklu życia ASP.NET są wywoływane z poziomu metod **Programu RoleEntryPoint.** Metoda **\_Uruchamianie aplikacji** jest wywoływana po zakończeniu metody **RoleEntryPoint.OnStart.** **Metoda\_zakończenia aplikacji** jest wywoływana przed wywołaniem metody **RoleEntryPoint.OnStop.**

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [utworzyć pakiet usług w chmurze](cloud-services-model-and-package.md).




