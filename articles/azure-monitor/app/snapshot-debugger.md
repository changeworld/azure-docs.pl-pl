---
title: Debuger migawek usługi Azure Application Insights dla aplikacji platformy .NET
description: Migawki debugowania są zbierane automatycznie, gdy wyjątki są generowane w aplikacjach produkcyjnych .NET
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275765"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Debugowanie migawek wyjątków w aplikacjach .NET
W przypadku wystąpienia wyjątku można automatycznie zbierać migawkę debugowania z aplikacji sieci web na żywo. Migawka pokazuje stan kodu źródłowego i zmiennych w momencie, gdy wyjątek został zgłoszony. Debuger migawek w [usłudze Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoruje dane telemetryczne wyjątków z aplikacji sieci web. Zbiera migawki na wyjątki zgłaszania top, dzięki czemu masz informacje potrzebne do diagnozowania problemów w produkcji. Dołącz [pakiet NuGet modułu zbierającego migawkę](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) do aplikacji i opcjonalnie skonfiguruj parametry kolekcji w [pliku ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Migawki są wyświetlane na [wyjątki](../../azure-monitor/app/asp-net-exceptions.md) w portalu usługi Application Insights.

Migawki debugowania można wyświetlić w portalu, aby zobaczyć stos wywołań i sprawdzić zmienne w każdej ramce tego stosu. Aby uzyskać bardziej zaawansowane środowisko debugowania z kodem źródłowym, otwórz migawki za pomocą programu Visual Studio 2019 Enterprise. W programie Visual Studio można również [ustawić snappoints interaktywnie robić migawki](https://aka.ms/snappoint) bez oczekiwania na wyjątek.

Migawki debugowania są przechowywane przez 15 dni. Ta zasada przechowywania jest ustawiana na podstawie dla aplikacji. Jeśli chcesz zwiększyć tę wartość, możesz zażądać zwiększenia, otwierając przypadek pomocy technicznej w witrynie Azure portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Włączanie debugera migawek usługi Application Insights dla aplikacji
Kolekcja migawek jest dostępna dla:
* .NET Framework i ASP.NET aplikacje z systemem .NET Framework 4.5 lub nowszym.
* Aplikacje .NET Core 2.0 i ASP.NET Core 2.0 działające w systemie Windows.

Obsługiwane są następujące środowiska:

* [Usługa aplikacji platformy Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Usługi w chmurze Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z rodziną systemu operacyjnego 4 lub nowszym
* [Usługi sieci szkieletowej usług Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) w systemie Windows Server 2012 R2 lub nowszym
* [Maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem Windows Server 2012 R2 lub nowszym
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem Windows Server 2012 R2 lub nowszym lub Windows 8.1 lub nowszym

> [!NOTE]
> Aplikacje klienckie (na przykład WPF, Windows Forms lub UWP) nie są obsługiwane.

Jeśli włączono debuger migawek, ale nie widzisz migawek, zapoznaj się z naszym [przewodnikiem rozwiązywania problemów.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

## <a name="grant-permissions"></a>Udzielenie uprawnień

Dostęp do migawek jest chroniony przez kontrolę dostępu opartą na rolach (RBAC). Aby sprawdzić migawkę, należy najpierw zostać dodane do roli niezbędne przez właściciela subskrypcji.

> [!NOTE]
> Właściciele i współautorzy nie mają automatycznie tej roli. Jeśli chcą wyświetlać migawki, muszą dodać się do roli.

Właściciele subskrypcji należy `Application Insights Snapshot Debugger` przypisać rolę do użytkowników, którzy będą sprawdzać migawki. Tę rolę można przypisać do poszczególnych użytkowników lub grup przez właścicieli subskrypcji dla docelowego zasobu usługi Application Insights lub jego grupy zasobów lub subskrypcji.

1. Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
1. Kliknij pozycję **Kontrola dostępu (IAM)**.
1. Kliknij przycisk **+Dodaj przypisanie roli.**
1. Wybierz **debuger migawki usługi Application Insights** z listy rozwijanej **Role.**
1. Wyszukaj i wprowadź nazwę użytkownika do dodania.
1. Kliknij przycisk **Zapisz,** aby dodać użytkownika do roli.


> [!IMPORTANT]
> Migawki mogą potencjalnie zawierać informacje osobiste i inne poufne w wartościach zmiennych i parametrów.

## <a name="view-snapshots-in-the-portal"></a>Wyświetlanie migawek w portalu

Po wystąpieniu wyjątku w aplikacji i migawka została utworzona, powinny mieć migawki do wyświetlenia. Może upłynąć od 5 do 10 minut od wyjątku występującego do migawki gotowe i widoczne z portalu. Aby wyświetlić migawki, w **okienku Niepowodzenie** wybierz przycisk **Operacje** podczas wyświetlania karty **Operacje** lub wybierz przycisk **Wyjątki** podczas wyświetlania karty **Wyjątki:**

![Strona niepowodzenia](./media/snapshot-debugger/failures-page.png)

Wybierz operację lub wyjątek w prawym okienku, aby otworzyć okienko **Szczegóły transakcji end-to-end,** a następnie wybierz zdarzenie wyjątku. Jeśli migawka jest dostępna dla danego wyjątku, przycisk **Otwórz migawkę debugowania** pojawia się w prawym okienku ze szczegółami [wyjątku](../../azure-monitor/app/asp-net-exceptions.md).

![Otwórz przycisk Migawka debugowania w sprawie wyjątku](./media/snapshot-debugger/e2e-transaction-page.png)

W widoku migawka debugowania jest widoczna stos wywołań i okienko zmiennych. Po wybraniu ramek stosu wywołań w okienku stosu wywołań można wyświetlić zmienne lokalne i parametry dla tego wywołania funkcji w okienku zmiennych.

![Wyświetlanie migawki debugowania w portalu](./media/snapshot-debugger/open-snapshot-portal.png)

Migawki mogą zawierać poufne informacje i domyślnie nie są widoczne. Aby wyświetlić migawki, `Application Insights Snapshot Debugger` musisz mieć przypisaną rolę.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Wyświetlanie migawek w programie Visual Studio 2017 Enterprise lub wyższym
1. Kliknij przycisk **Pobierz migawkę,** `.diagsession` aby pobrać plik, który może być otwierany przez program Visual Studio Enterprise.

2. Aby otworzyć `.diagsession` plik, musisz mieć zainstalowany składnik Debuger migawki visual studio. Składnik Debuger migawki jest wymaganym składnikiem obciążenia ASP.net w programie Visual Studio i można go wybrać z listy poszczególnych składników w instalatorze programu Visual Studio. Jeśli używasz wersji programu Visual Studio przed programem Visual Studio 2017 w wersji 15.5, należy zainstalować rozszerzenie z [portalu Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Po otwarciu pliku migawki zostanie wyświetlona strona Debugowania minidump w programie Visual Studio. Kliknij **przycisk Debugowany kod zarządzany,** aby rozpocząć debugowanie migawki. Migawka otwiera się do wiersza kodu, w którym został zgłoszony wyjątek, dzięki czemu można debugować bieżący stan procesu.

    ![Wyświetlanie migawki debugowania w programie Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Pobrana migawka zawiera wszystkie pliki symboli, które zostały znalezione na serwerze aplikacji sieci web. Te pliki symboli są wymagane do skojarzenia danych migawki z kodem źródłowym. W przypadku aplikacji usługi App Service należy włączyć wdrażanie symboli podczas publikowania aplikacji sieci web.

## <a name="how-snapshots-work"></a>Jak działają migawki

Moduł zbierający migawkę jest implementowany jako [procesor telemetrii usługi Application Insights](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po uruchomieniu aplikacji procesor telemetrii modułu modułu modułu migawki jest dodawany do potoku telemetrii aplikacji.
Za każdym razem, gdy aplikacja wywołuje [TrackException,](../../azure-monitor/app/asp-net-exceptions.md#exceptions)moduł zbierający migawkę oblicza identyfikator problemu z typu zgłaszanego wyjątku i metody zgłaszania.
Za każdym razem, gdy aplikacja wywołuje TrackException, licznik jest zwiększany dla odpowiedniego identyfikatora problemu. Gdy licznik osiągnie `ThresholdForSnapshotting` wartość, identyfikator problemu jest dodawany do planu kolekcji.

Moduł zbierający migawkę monitoruje również wyjątki, ponieważ są one generowane przez subskrybowanie [zdarzenia AppDomain.CurrentDomain.FirstChanceException.](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) Po uruchomieniu tego zdarzenia identyfikator problemu wyjątku jest obliczany i porównywany z identyfikatorami problemów w planie zbierania danych.
Jeśli istnieje dopasowanie, zostanie utworzona migawka uruchomionego procesu. Migawka jest przypisywany unikatowy identyfikator i wyjątek jest stemplowany z tego identyfikatora. Po firstChanceException obsługi zwraca, wyjątek jest przetwarzany normalnie. Po pewnym czasie wyjątek osiąga TrackException metody ponownie, gdzie, wraz z identyfikatorem migawki, jest zgłaszane do usługi Application Insights.

Główny proces nadal działa i obsługuje ruch dla użytkowników z niewielką przerwą. W międzyczasie migawka jest przekazywana do procesu przekazywania migawek. Program do przekazywania migawek tworzy minidump i przesyła go do usługi Application Insights wraz z odpowiednimi plikami symboli (pdb).

> [!TIP]
> - Migawka procesu jest zawieszony klon uruchomionego procesu.
> - Tworzenie migawki trwa około 10 do 20 milisekund.
> - Wartość domyślna `ThresholdForSnapshotting` to 1. Jest to również wartość minimalna. W związku z tym aplikacja musi wyzwolić ten sam wyjątek **dwa razy** przed utworzeniem migawki.
> - Ustaw `IsEnabledInDeveloperMode` wartość true, jeśli chcesz wygenerować migawki podczas debugowania w programie Visual Studio.
> - Szybkość tworzenia migawki `SnapshotsPerTenMinutesLimit` jest ograniczona przez ustawienie. Domyślnie limit jest jedną migawką co dziesięć minut.
> - Nie więcej niż 50 migawek dziennie mogą być przesyłane.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych wynosi 15 dni. Dla każdego wystąpienia usługi Application Insights maksymalna liczba 50 migawek jest dozwolona dziennie.

### <a name="publish-symbols"></a>Publikowanie symboli
Debuger migawki wymaga plików symboli na serwerze produkcyjnym do dekodowania zmiennych i zapewnienia środowiska debugowania w programie Visual Studio.
Wersja 15.2 (lub wyższa) programu Visual Studio 2017 publikuje symbole kompilacji wersji domyślnie, gdy publikuje w usłudze App Service. W poprzednich wersjach należy dodać następujący wiersz `.pubxml` do pliku profilu publikowania, aby symbole były publikowane w trybie wydania:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

W przypadku usługi Azure Compute i innych typów upewnij się, że pliki symboli znajdują `wwwroot/bin`się w tym samym folderze głównej biblioteki dll aplikacji (zazwyczaj) lub są dostępne na bieżącej ścieżce.

> [!NOTE]
> Aby uzyskać więcej informacji na temat różnych opcji symboli, które są dostępne, zapoznaj się z [dokumentacją programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Aby uzyskać najlepsze wyniki, zalecamy użycie "Full", "Portable" lub "Embedded".

### <a name="optimized-builds"></a>Zoptymalizowane kompilacje
W niektórych przypadkach zmiennych lokalnych nie można wyświetlać w kompilacjach wersji z powodu optymalizacji, które są stosowane przez kompilator JIT.
Jednak w usłudze Azure App Services moduł zbierający migawkę można deoptymalizacji rzucanie metod, które są częścią jego planu zbierania kolekcji.

> [!TIP]
> Zainstaluj rozszerzenie witryny usługi Application Insights w usłudze App Service, aby uzyskać pomoc techniczną w celu uzyskania pomocy w celu deoptymalizacji.

## <a name="next-steps"></a>Następne kroki
Włącz debuger migawek usługi Application Insights dla aplikacji:

* [Usługa aplikacji platformy Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi sieci szkieletowej usług Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Poza debugerem migawki usługi Application Insights:
 
* [Ustaw punkty dostępu w kodzie,](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) aby uzyskać migawki bez oczekiwania na wyjątek.
* [Diagnozowanie wyjątków w aplikacjach sieci web](../../azure-monitor/app/asp-net-exceptions.md) wyjaśnia, jak uczynić więcej wyjątków widocznymi dla usługi Application Insights.
* [Inteligentne wykrywanie](../../azure-monitor/app/proactive-diagnostics.md) automatycznie wykrywa anomalie wydajności.
