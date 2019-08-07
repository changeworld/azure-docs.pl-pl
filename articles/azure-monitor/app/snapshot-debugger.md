---
title: Usługi Azure Application Insights Snapshot Debugger dla aplikacji platformy .NET | Dokumentacja firmy Microsoft
description: Debugowanie migawek są automatycznie zbierane, gdy wyjątki zostaną zgłoszone w aplikacjach .NET w środowisku produkcyjnym
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 08/06/2019
ms.author: mbullwin
ms.openlocfilehash: 02d72ab877577e97592dfdd763a58cb01b201d8b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839354"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Debugowanie migawek wyjątków w aplikacjach .NET
Gdy wystąpi wyjątek, może automatycznie zbierać migawek debugowania z działającej aplikacji internetowej. Migawka przedstawia stan zmiennych i kod źródłowy w momencie utworzenia zgłoszenia wyjątku. Rozszerzenie Snapshot Debugger (wersja zapoznawcza) w [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoruje dane telemetryczne dotyczące wyjątków z aplikacji sieci web. Zbiera migawki na listy wyjątków zgłaszanie górnej dzięki temu uzyskasz informacje potrzebne do diagnozowania problemów w środowisku produkcyjnym. Obejmują [pakiet NuGet modułu zbierającego migawki](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w aplikacji i opcjonalnie skonfigurować kolekcję parametrów w [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Migawki są wyświetlane na [wyjątki](../../azure-monitor/app/asp-net-exceptions.md) w portalu Application Insights.

Migawki debugowania można wyświetlić w portalu, aby zobaczyć stos wywołań i sprawdzić zmienne w każdej ramce tego stosu. Aby uzyskać bardziej zaawansowane środowisko debugowania z kodem źródłowym, Otwórz migawki w programie Visual Studio 2019 Enterprise. W programie Visual Studio, możesz również [Ustaw punkty przyciągania do interaktywnego robienia migawek](https://aka.ms/snappoint) bez oczekiwania na wyjątek.

Debugowanie migawki są przechowywane przez siedem dni. Te zasady przechowywania jest ustawiona na podstawie poszczególnych aplikacji. Jeśli potrzebujesz zwiększyć tę wartość, możesz poprosić o zwiększenie przez otwarcie zgłoszenia do pomocy technicznej w witrynie Azure portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Włącz Snapshot Debugger Application Insights dla aplikacji
Zbieranie migawek jest dostępna dla:
* .NET framework i ASP.NET działających .NET Framework 4.5 lub nowszej.
* Aplikacje platformy .NET core 2.0 i ASP.NET Core 2.0, systemem Windows.

Są obsługiwane w następujących środowiskach:

* [Usługa Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Platforma Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem operacyjnym 4 lub nowszym
* [Usługi Service Fabric platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) działające w systemie Windows Server 2012 R2 lub nowszym
* [Platforma Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem Windows Server 2012 R2 lub nowszym
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem Windows Server 2012 R2 lub nowszym

> [!NOTE]
> Aplikacje klienckie (na przykład, WPF, formularze Windows lub platformy uniwersalnej systemu Windows) nie są obsługiwane.

Jeśli włączono Snapshot Debugger, ale nie widzisz migawek, zapoznaj się [z naszym przewodnikiem rozwiązywania problemów](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Udzielenie uprawnień

Dostęp do migawek są chronione przez kontroli dostępu opartej na rolach (RBAC). Aby przeprowadzić inspekcję migawki, możesz należy najpierw dodać do roli wymagane przez właściciela subskrypcji.

> [!NOTE]
> Właściciele i współautorzy automatycznie bez tej roli. Jeśli chcą wyświetlić migawki, co zwiększa się do roli.

Właściciele subskrypcji należy przypisać `Application Insights Snapshot Debugger` ról do użytkowników, którzy będą inspekcji migawki. Tę rolę można przypisać do poszczególnych użytkowników lub grupy według właścicieli subskrypcji dla elementu docelowego zasobu usługi Application Insights lub grupy zasobów lub subskrypcji.

1. Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
1. Kliknij przycisk **kontrola dostępu (IAM)** .
1. Kliknij przycisk **+ Dodaj przypisanie roli** przycisku.
1. Wybierz **Application Insights Snapshot Debugger** z **role** listy rozwijanej.
1. Wyszukaj, a następnie wprowadź nazwę użytkownika do dodania.
1. Kliknij przycisk **Zapisz** przycisk, aby dodać użytkownika do roli.


> [!IMPORTANT]
> Migawki mogą potencjalnie zawierać osobistych, jak i innych informacji poufnych w zmiennej i wartości parametrów.

## <a name="view-snapshots-in-the-portal"></a>Wyświetlanie migawek w portalu

Gdy w aplikacji wystąpił wyjątek, a migawka została utworzona, należy utworzyć migawki do wyświetlenia. Wykonanie migawki w portalu może potrwać od 5 do 10 minut od wyjątku. Aby wyświetlić migawki, w okienku **awaria** wybierz przycisk **operacje** podczas wyświetlania karty **operacje** lub wybierz przycisk **wyjątki** podczas wyświetlania karty **wyjątki** :

![Strona błędów](./media/snapshot-debugger/failures-page.png)

Wybierz operację lub wyjątek w okienku po prawej stronie, aby otworzyć okienko **szczegóły końca transakcji** , a następnie wybierz zdarzenie wyjątku. Jeśli dla danego wyjątku dostępna jest migawka, w okienku po prawej stronie zostanie wyświetlony przycisk **Otwórz migawkę debugowania** z szczegółowymi informacjami dotyczącymi [wyjątku](../../azure-monitor/app/asp-net-exceptions.md).

![Przycisk Otwórz migawkę debugowania przy wyjątku](./media/snapshot-debugger/e2e-transaction-page.png)

W widoku debugowania migawki zobaczysz stos wywołań i okienko zmiennych. Po wybraniu ramki stosu wywołań w panelu stosu wywołań, mogą wyświetlać zmienne lokalne i parametry dla tej funkcji wywołanie w okienku zmiennych.

![Wyświetl migawkę debugowania w portalu](./media/snapshot-debugger/open-snapshot-portal.png)

Migawki mogą zawierać poufne informacje, a domyślnie nie są widoczne. Aby wyświetlić migawki, konieczne jest posiadanie `Application Insights Snapshot Debugger` rola przypisana użytkownikowi.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Wyświetlanie migawek w programie Visual Studio 2017 Enterprise lub nowszym
1. Kliknij przycisk **Pobierz migawkę** , aby pobrać `.diagsession` plik, który może zostać otwarty przez Visual Studio Enterprise.

2. Aby otworzyć `.diagsession` plik, należy zainstalować składnik programu Snapshot Debugger Visual Studio. Składnik Snapshot Debugger jest wymaganym składnikiem obciążenia ASP.net w programie Visual Studio i można go wybrać z listy poszczególnych składników w Instalatorze programu Visual Studio. Jeśli używasz wersji programu Visual Studio starszej niż wersja 15,5 programu Visual 2017 Studio, musisz zainstalować rozszerzenie z [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Po otwarciu pliku migawki zostanie wyświetlona strona minizrzutu w programie Visual Studio. Kliknij przycisk **debugowanie kodu zarządzanego** można rozpocząć debugowania migawki. Migawka zostanie otwarty do wiersza kodu, w którym wystąpił wyjątek, aby można było debugować bieżący stan procesu.

    ![Wyświetl migawkę debugowania w programie Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Pobrany migawki zawiera wszystkie pliki symboli, które zostały odnalezione na serwerze aplikacji sieci web. Te pliki symboli są wymagane, aby skojarzyć dane migawki z kodem źródłowym. W przypadku aplikacji usługi App Service upewnij się, że umożliwiają wdrażanie symboli, podczas publikowania aplikacji sieci web.

## <a name="how-snapshots-work"></a>Jak działają migawek

Modułu zbierającego migawki jest implementowany jako [Application Insights Telemetrii procesora](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po uruchomieniu aplikacji, procesor danych Telemetrycznych modułu zbierającego migawki jest dodawany do potoku telemetrii Twojej aplikacji.
Każdym wywołaniami aplikacji [metoda TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), modułu zbierającego migawki oblicza identyfikator problemu z rodzaj zgłaszanego wyjątku i zgłaszanie metody.
Za każdym razem, Twoja aplikacja wywołuje metoda TrackException, licznik jest zwiększany odpowiedni identyfikator problemu. Jeśli licznik osiągnie `ThresholdForSnapshotting` wartości, identyfikator problemu jest dodawany do planu zbierania.

Jak są one generowane przez Subskrybowanie modułu zbierającego migawki również monitoruje wyjątki [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) zdarzeń. Gdy zostanie wyzwolony tego zdarzenia, identyfikator problemu wyjątku jest obliczana i porównywana z identyfikatorami Problem w planie kolekcji.
Jeśli istnieje dopasowanie, tworzona jest migawka uruchomionego procesu. Migawka jest przypisany unikatowy identyfikator, a wyjątek jest oznaczony za pomocą tego identyfikatora. Po powrocie obsługi FirstChanceException zgłoszony wyjątek są przetwarzane w zwykły sposób. Po pewnym czasie wyjątek osiągnie ponownie metodą metoda TrackException gdzie, oraz identyfikator migawki, jest zgłaszany do usługi Application Insights.

Główny proces jest kontynuowany do uruchamiania i obsługiwać ruch do użytkowników z niewielkim przerwy. W tym samym czasie migawki jest przekazywane do procesu Przekazywarka migawki. Przekazujesz migawki tworzy minizrzutu i przekazuje je do usługi Application Insights oraz wszystkie pliki odpowiednich symboli (.pdb).

> [!TIP]
> - Migawki procesu jest wstrzymane klonu uruchomionego procesu.
> - Tworzenie migawki trwa około 10 do 20 MS.
> - Wartością domyślną dla `ThresholdForSnapshotting` 1. Jest to również wartość minimalna. W związku z tym, Twoja aplikacja ma wyzwolić ten sam wyjątek **dwukrotnie** przed utworzeniem migawki.
> - Ustaw `IsEnabledInDeveloperMode` wartość true, jeśli chcesz wygenerować migawki podczas debugowania w programie Visual Studio.
> - Częstotliwość tworzenia migawki jest ograniczona przez `SnapshotsPerTenMinutesLimit` ustawienie. Domyślnie limit jest jedną migawkę, co 10 minut.
> - Mogą być przekazywane nie więcej niż 50 migawek na dzień.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych wynosi 15 dni. Dla każdego wystąpienia Application Insights dozwolonych jest maksymalnie 50 migawek dziennie.

### <a name="publish-symbols"></a>Publikuj symbole
Rozszerzenie Snapshot Debugger wymaga plików symboli na serwerze produkcyjnym do zdekodowania zmienne i aby zapewnić środowisko debugowania w programie Visual Studio.
Wersja 15.2 (lub nowszy) programu Visual Studio 2017 publikuje symbole dla wersji kompilacji domyślnie, publikuje w usłudze App Service. W poprzednich wersjach, należy dodać następujący wiersz do Twojego profilu publikowania `.pubxml` plik symboli są publikowane w trybie wydania:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

W przypadku usługi Azure Compute a innymi typami danych, upewnij się, że pliki symbol znajdują się w tym samym folderze plik .dll aplikacji głównej (zazwyczaj `wwwroot/bin`) lub są dostępne w bieżącej ścieżce.

> [!NOTE]
> Więcej informacji o różnych opcjach symboli dostępnych można znaleźć w [dokumentacji](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)programu Visual Studio. Aby uzyskać najlepsze wyniki, zalecamy użycie "Full", "Portable" lub "Embedded".

### <a name="optimized-builds"></a>Zoptymalizowane kompilacjami
W niektórych przypadkach nie można wyświetlić zmiennych lokalnych w wydawanych wersjach ze względu na optymalizacje, które są stosowane przy użyciu kompilatora JIT.
Jednak w usłudze Azure App Services, modułu zbierającego migawki można deoptimize zgłaszanie metod, które są częścią planu zbierania.

> [!TIP]
> W usłudze App Service, aby uzyskać pomoc techniczną deoptimization, należy zainstalować rozszerzenie witryny usługi Application Insights.

## <a name="next-steps"></a>Następne kroki
Włącz Snapshot Debugger Application Insights dla aplikacji:

* [Usługa Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi Service Fabric platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Poza Application Insights Snapshot Debugger:
 
* [Ustaw punkty przyciągania w kodzie](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) można pobrać migawek bez oczekiwania na wyjątek.
* [Diagnozowanie wyjątków w aplikacjach sieci web](../../azure-monitor/app/asp-net-exceptions.md) wyjaśnia, jak ustawienie widoczności więcej wyjątków usługi Application Insights.
* [Wykrywanie inteligentne](../../azure-monitor/app/proactive-diagnostics.md) automatycznie wykrywa anomalie wydajność.
