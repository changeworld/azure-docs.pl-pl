---
title: Debugowanie usługi w chmurze Azure lub maszyny wirtualnej w programie Visual Studio | Dokumentacja firmy Microsoft
description: Debugowanie usługi w chmurze lub maszyny wirtualnej w programie Visual Studio
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: d230632fe49874385e3c337a7b07d1f1acec585e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292802"
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Debugowanie usługi w chmurze Azure lub maszyny wirtualnej w programie Visual Studio

Visual Studio zapewnia różne opcje do debugowania usług w chmurze Azure i maszyn wirtualnych.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>Usługi w chmurze na komputerze lokalnym debugowania

Można oszczędzić czas i pieniądze przy użyciu platformy Azure obliczeniowe emulatora debugowania usługi w chmurze na komputerze lokalnym. Przez lokalnie debugowania usługi przed wdrożeniem, można zwiększyć niezawodność i wydajność bez płatności dla czasu obliczeniowego. Jednak niektóre mogą wystąpić błędy tylko po uruchomieniu usługi w chmurze na platformie Azure samej siebie. Te błędy można debugować po włączeniu zdalnego debugowania podczas publikowania usługi, a następnie dołącz debuger do wystąpienia roli.

Emulator symuluje rozwiązań usługi obliczenia Azure usługi i jest uruchamiany w środowisku lokalnym, aby umożliwić testowanie i debugowanie usługi w chmurze, przed przystąpieniem do wdrażania. Emulator obsługuje cyklem życia wystąpienia roli i zapewnia dostęp do symulowanego zasoby, takie jak magazyn lokalny. Podczas debugowania i uruchamiania usługi z programu Visual Studio emulator jest automatycznie uruchamiana jako aplikacja w tle, a następnie wdraża emulator usługi. Aby wyświetlić usługi, gdy jest uruchamiany w środowisku lokalnym można użyć emulatora. Można uruchomić pełną wersję lub ekspresowej wersji emulatora. (Począwszy od wersji 2.3 Azure ekspresowej wersji emulatora jest wartość domyślna). Zobacz [by przeprowadzić debugowanie usługi w chmurze lokalnie za pomocą ekspresowej wersji emulatora](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Aby debugować usługi w chmurze na komputerze lokalnym

1. Na pasku menu wybierz **debugowania**, **Rozpocznij debugowanie** do uruchomienia projektu usługi chmury Azure. Alternatywnie naciśnij klawisz F5. Zobaczysz komunikat, który uruchamia emulatora obliczeniowe. Po uruchomieniu emulatora, ikona na pasku zadań systemu potwierdza go.

    ![Emulator usługi Azure na pasku zadań](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

2. Wyświetlanie interfejsu użytkownika dla emulatora obliczeń, otwierając menu skrótów dla usługi Azure ikony w obszarze powiadomień, a następnie wybierz **Pokaż interfejs użytkownika emulatora obliczeń**.

    W okienku po lewej stronie interfejsu użytkownika pokazano usług, które obecnie są wdrażane na emulator obliczeń i wystąpień ról, które każda usługa jest uruchomiona. Można wybrać usługi lub role do wyświetlania w okienku po prawej stronie Cykl życia, rejestrowania i informacji diagnostycznych. Fokus jest włączony w górny margines dołączone okna, jego rozszerzeniu wypełnienia w okienku po prawej stronie.

3. Kroki do aplikacji przez wybranie polecenia na **debugowania** menu i ustawianie punktów przerwania w kodzie. Podczas wykonywania kroków przy użyciu aplikacji w debugerze okienka są aktualizowane przy bieżącym stanie aplikacji. Po zatrzymaniu debugowania, wdrażania aplikacji jest usuwana. Jeśli aplikacja zawiera rolę sieci web i ustawiono właściwość Akcja uruchamiania dla uruchomienia przeglądarki sieci web, Visual Studio uruchamia aplikację sieci web w przeglądarce. W przypadku zmiany liczby wystąpień roli w konfiguracji usługi, należy zatrzymać usługi w chmurze i ponownie uruchom debugowanie, aby umożliwić debugowanie tych nowych wystąpień roli.

    **Uwaga:** po zatrzymaniu uruchamiania lub debugowania usługi emulatora obliczeń lokalnych i emulatora magazynu nie są zablokowane. Musisz zatrzymać je bezpośrednio z poziomu obszaru powiadomień.

## <a name="debug-a-cloud-service-in-azure"></a>Usługi w chmurze na platformie Azure debugowania

Aby debugować usługi w chmurze z komputera zdalnego, należy włączyć te funkcje jawnie podczas wdrażania usługi w chmurze tak, że wymagane usługi (na przykład msvsmon.exe) są zainstalowane na maszynach wirtualnych, które uruchomienie wystąpienia roli. Jeśli nie zostanie włączone, zdalne debugowanie po opublikowaniu usługi, należy ponownie opublikować usługi z włączonym debugowaniem zdalnym.

Włączenie debugowania zdalnego dla usługi w chmurze, on nie działać z mniejszą wydajnością lub spowodować naliczenie dodatkowych opłat. Nie należy używać zdalnego debugowania na usługi produkcji, ponieważ klienci korzystający z usługi może mieć niekorzystny wpływ na.

> [!NOTE]
> Po opublikowaniu usługi w chmurze w programie Visual Studio, aby umożliwić **IntelliTrace** wszystkich ról tej usługi, które są przeznaczone dla platformy .NET Framework 4 lub .NET Framework 4.5. Za pomocą **IntelliTrace**, można zbadać zdarzenia, które wystąpiły w wystąpieniu roli w przeszłości i Odtwórz kontekstu od tego momentu. Zobacz [debugowania usługi opublikowana chmura za pomocą funkcji IntelliTrace i Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) i [przy użyciu funkcji IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Aby włączyć debugowanie zdalne usługi w chmurze

1. Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **publikowania**.

2. Wybierz **przemieszczania** środowiska i **debugowania** konfiguracji.

    Jest to tylko wytyczne. Można wybrać do uruchomienia środowiska testowego w środowisku produkcyjnym. Jednak użytkownik może niekorzystnie wpłynąć na użytkowników po włączeniu zdalnego debugowania w środowisku produkcyjnym. Można wybrać do konfiguracji wydania, ale z konfiguracji debugowania powoduje, że ułatwia debugowanie.

    ![Wybierz konfigurację debugowania](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

3. Wykonaj kroki zwykle, ale wybierz **Włącz zdalny debuger dla wszystkich ról** pole wyboru na **Zaawansowane ustawienia** kartę.

    ![Konfiguracja debugowania](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Aby dołączyć debuger do usługi w chmurze na platformie Azure

1. W Eksploratorze serwera rozwiń węzeł usługi w chmurze.

2. Otwórz menu skrótów dla roli lub wystąpienia roli, do której chcesz dołączyć, a następnie wybierz **dołączyć debuger**.

    Jeśli debugowania roli debuger programu Visual Studio dołącza do każdego wystąpienia tej roli. Debuger przerwie na punkt przerwania w pierwszym wystąpieniu roli, uruchamia wiersza kodu, który spełnia wszystkie warunki tego punktu przerwania. Jeśli wystąpienie dołącza debuger do tego wystąpienia i podziału na punkt przerwania tylko wtedy, gdy to wystąpienie określonego wiersza kodu spełnia warunki punkt przerwania debugowania.

    ![Dołącz debuger](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

3. Po dołącza debuger do wystąpienia, debugowania w zwykły sposób. Debuger automatyczne dołączenie do procesu odpowiedniego hosta dla roli użytkownika. W zależności od tego, co to jest rola debuger dołącza do w3wp.exe, WaWorkerHost.exe lub WaIISHost.exe. Aby sprawdzić procesu, do której jest dołączony debuger, rozwiń węzeł wystąpienia w Eksploratorze serwera. Zobacz [Azure roli architektura](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Aby uzyskać więcej informacji o procesach platformy Azure.

    ![Wybór typu kodu — okno dialogowe](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

4. Do identyfikacji procesów, do których jest dołączony debuger, Otwórz okno dialogowe procesów, z menu, wybierając debugowania, Windows, procesów. (Klawiatury: Ctrl + Alt + Z) Aby odłączyć określonego procesu, otwórz menu skrótów, a następnie wybierz **odłączyć procesu**. Lub, zlokalizuj węzeł wystąpienia w Eksploratorze serwera, Znajdź proces, otwórz menu skrótów, a następnie wybierz **odłączyć procesu**.

    ![Debugowanie procesów](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Uniknąć długich zatrzymane na punktów przerwania podczas zdalnego debugowania. Azure traktuje procesu, który został zatrzymany przez czas dłuższy niż kilka minut odpowiada, a następnie zatrzymuje wysyłania ruchu do tego wystąpienia. Jeśli zatrzymasz zbyt długo, msvsmon.exe odłączenie od procesu.

Aby odłączyć debugera od wszystkich procesów w roli lub wystąpienia, otwórz menu skrótów dla roli lub wystąpienia, debugowanie, a następnie wybierz **odłączyć debugera**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Ograniczenia debugowania zdalnego na platformie Azure

Z 2.3 zestawu SDK platformy Azure zdalnego debugowania ma następujące ograniczenia:

* Z funkcją debugowania zdalnego włączone, nie można opublikować w którym dowolnej roli ma więcej niż 25 wystąpień usługi w chmurze.
* Debuger używa portów 30400 do 30424, 31400 do 31424 i 32400 do 32424. Jeśli spróbujesz użyć dowolnego z tych portów, nie będzie można opublikować usługi, a jeden z następujących komunikatów o błędach pojawi się w dzienniku aktywności platformy Azure:

  * Wystąpił błąd podczas sprawdzania pliku .cscfg względem pliku csdef.
    Zakres zarezerwowanych portów "range" dla punktu końcowego Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector roli "roli" nakłada się na już zdefiniowany port lub zakresu.
  * Alokacja nie powiodła się. Spróbuj ponownie później, spróbuj zmniejszyć rozmiar maszyny Wirtualnej lub liczbę wystąpień roli lub spróbuj przeprowadzić wdrożenie w innym regionie.

## <a name="debugging-azure-virtual-machines"></a>Debugowanie maszyn wirtualnych platformy Azure

Można debugować programy uruchamiane na maszynach wirtualnych Azure za pomocą Eksploratora serwera w programie Visual Studio. Po włączeniu zdalnego debugowania na maszynie wirtualnej platformy Azure, Azure instaluje zdalnego debugowania rozszerzenia na maszynie wirtualnej. Następnie można dołączyć do procesów na maszynie wirtualnej i debugowania w zwykły sposób.

> [!NOTE]
> Maszyny wirtualne utworzone przez stos Menedżera zasobów platformy Azure można zdalnie debugować przy użyciu Eksplorator chmury w programie Visual Studio 2015. Aby uzyskać więcej informacji, zobacz [zarządzania zasobami Azure za pomocą Eksploratora chmury](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Aby debugować maszyny wirtualnej platformy Azure

1. W Eksploratorze serwera rozwiń węzeł maszyny wirtualnej, a następnie wybierz węzeł maszynę wirtualną, którą chcesz debugować.

2. Otwórz menu kontekstowe i wybierz **włączyć debugowanie**. Po otrzymaniu monitu, jeśli masz pewności, jeśli chcesz włączyć debugowanie na maszynie wirtualnej, wybierz opcję **tak**.

    Azure instaluje zdalnego debugowania rozszerzenia na maszynie wirtualnej, aby włączyć debugowanie.

    ![Maszyna wirtualna włączyć polecenie debugowania](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Dziennik aktywności platformy Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

3. Po zakończeniu instalowania rozszerzenia debugowania zdalnego otwórz menu kontekstowe maszyny wirtualnej i wybierz **dołączyć debuger...**

    Azure pobiera listę procesów w maszynie wirtualnej i spowoduje dołączanie do procesu, okno dialogowe.

    ![Dołącz debuger polecenia](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

4. W **dołączyć do procesu** okno dialogowe, wybierz opcję **wybierz** do ograniczania listy wyników, aby wyświetlić tylko typy kodu, które chcesz debugować. Można debugować 32-bitowy lub 64-bitowego kodu zarządzanego i kodu natywnego.

    ![Wybór typu kodu — okno dialogowe](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

5. Wybierz procesy, które mają debugowania na maszynie wirtualnej, a następnie wybierz **Attach**. Na przykład możesz wybrać procesu w3wp.exe, jeśli chcesz debugować aplikację sieci web na maszynie wirtualnej. Zobacz [debugowania jeden lub więcej procesów w programie Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) i [Azure roli architektura](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Aby uzyskać więcej informacji.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Tworzenie projektu sieci web i maszyny wirtualnej do debugowania

Przed opublikowaniem projektu platformy Azure, użytkownik może być bardzo przydatne testowania go w środowisku zawartych w niej obsługującej debugowania i testowania scenariuszy i której można zainstalować testowania i monitorowanie programów. Jednym ze sposobów uruchamianie tych testów jest zdalne debugowanie aplikacji na maszynie wirtualnej.

Projekty Visual Studio ASP.NET oferują możliwość utworzenia przydatną maszyny wirtualnej, która służy do testowania aplikacji. Maszyna wirtualna zawiera potrzebnych punktów końcowych, takie jak środowiska PowerShell, pulpitu zdalnego i WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Aby utworzyć projekt sieci web i maszyny wirtualnej do debugowania

1. W programie Visual Studio Utwórz nową aplikację sieci Web ASP.NET.

2. W oknie dialogowym Nowy projekt ASP.NET, w sekcji platformy Azure, wybierz **maszyny wirtualnej** w polu listy rozwijanej. Pozostaw **Utwórz zasoby zdalne** zaznaczone pole wyboru. Wybierz **OK** aby kontynuować.

    **Utwórz maszynę wirtualną na platformie Azure** zostanie wyświetlone okno dialogowe.

    ![Tworzenie projektu sieci web platformy ASP.NET — okno dialogowe](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Uwaga:** użytkownik zostanie zapytany do logowania się do konta platformy Azure, jeśli użytkownik nie jest jeszcze zarejestrowany.

3. Wybierz różne ustawienia dla maszyny wirtualnej, a następnie wybierz **OK**. Zobacz [maszyn wirtualnych](http://go.microsoft.com/fwlink/?LinkId=623033) Aby uzyskać więcej informacji.

    Nazwa wprowadzona dla nazwy DNS będzie nazwę maszyny wirtualnej.

    ![Utwórz maszynę wirtualną na okno dialogowe Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure tworzy maszynę wirtualną, a następnie przepisów i konfiguruje punkty końcowe, takie jak pulpitu zdalnego i narzędzia Web Deploy

4. Po pełnej konfiguracji maszyny wirtualnej, wybierz węzeł maszyny wirtualnej w Eksploratorze serwera.

5. Otwórz menu kontekstowe i wybierz **włączyć debugowanie**. Po otrzymaniu monitu, jeśli masz pewności, jeśli chcesz włączyć debugowanie na maszynie wirtualnej, wybierz opcję **tak**.

    Azure instaluje zdalnego debugowania rozszerzenie do maszyny wirtualnej, aby włączyć debugowanie.

    ![Maszyna wirtualna włączyć polecenie debugowania](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Dziennik aktywności platformy Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

6. Opublikować projekt w sposób opisany w [porady: Wdrażanie publikowania projektu sieci Web za pomocą jednego kliknięcia w programie Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Ponieważ chcesz debugować na maszynie wirtualnej na **ustawienia** strony **publikowanie w sieci Web** kreatora wybierz **debugowania** jako konfiguracji. Dzięki temu symbole kodu są dostępne podczas debugowania.

    ![Ustawienia publikowania](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

7. W **opcji publikowania pliku**, wybierz pozycję **Usuń dodatkowe pliki w miejscu docelowym** Jeśli wcześniej została już wdrożony projekt.

8. Po publikuje projektu, w menu kontekstowym maszyny wirtualnej w Eksploratorze serwera wybierz **dołączyć debuger...**

    Azure pobiera listę procesów w maszynie wirtualnej i spowoduje dołączanie do procesu, okno dialogowe.

    ![Dołącz debuger polecenia](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

9. W **dołączyć do procesu** okno dialogowe, wybierz opcję **wybierz** do ograniczania listy wyników, aby wyświetlić tylko typy kodu, które chcesz debugować. Można debugować 32-bitowy lub 64-bitowego kodu zarządzanego i kodu natywnego.

    ![Wybór typu kodu — okno dialogowe](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

10. Wybierz procesy, które mają debugowania na maszynie wirtualnej, a następnie wybierz **Attach**. Na przykład możesz wybrać procesu w3wp.exe, jeśli chcesz debugować aplikację sieci web na maszynie wirtualnej. Zobacz [debugowania jeden lub więcej procesów w programie Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

* Użyj **Intellitrace** do zbierania dzienników wywołań i zdarzenia z wersji serwera. Zobacz [debugowania usługi opublikowana chmura za pomocą funkcji IntelliTrace i Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).

* Użyj **diagnostyki Azure** logowania szczegółowe informacje o uruchamianiu kodu w ramach ról, czy role są uruchomione w środowisku programistycznym lub na platformie Azure. Zobacz [zbierania danych rejestrowania za pomocą diagnostyki Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).
