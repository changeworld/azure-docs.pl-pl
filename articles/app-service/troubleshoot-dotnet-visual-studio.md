---
title: Rozwiązywanie problemów z programem Visual Studio
description: Dowiedz się, jak rozwiązywać problemy z aplikacją usługi App Service za pomocą zdalnych narzędzi debugowania, śledzenia i rejestrowania wbudowanych w program Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 3305cfb81980984574961b2a84a056f5d1879ead
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280055"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Rozwiązywanie problemów z aplikacją w usłudze Azure App Service przy użyciu programu Visual Studio
## <a name="overview"></a>Omówienie
W tym samouczku pokazano, jak używać narzędzi programu Visual Studio do debugowania aplikacji w [usłudze App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)uruchamiając zdalnie w [trybie debugowania](https://docs.microsoft.com/visualstudio/debugger/) lub wyświetlając dzienniki aplikacji i dzienniki serwera sieci Web.

Dowiesz się:

* Które funkcje zarządzania aplikacjami są dostępne w programie Visual Studio.
* Jak za pomocą widoku zdalnego programu Visual Studio, aby wprowadzić szybkie zmiany w aplikacji zdalnej.
* Jak uruchomić tryb debugowania zdalnie, gdy projekt jest uruchomiony na platformie Azure, zarówno dla aplikacji, jak i dla webjob.
* Jak utworzyć dzienniki śledzenia aplikacji i wyświetlić je podczas tworzenia ich przez aplikację.
* Jak wyświetlić dzienniki serwera sieci web, w tym szczegółowe komunikaty o błędach i śledzenie żądań nie powiodło się.
* Jak wysłać dzienniki diagnostyczne do konta usługi Azure Storage i wyświetlić je tam.

Jeśli masz visual studio ultimate, można również użyć [IntelliTrace](/visualstudio/debugger/intellitrace) do debugowania. IntelliTrace nie jest omówione w tym samouczku.

## <a name="prerequisites"></a><a name="prerequisites"></a>Wymagania wstępne
Ten samouczek współpracuje ze środowiskiem programistycznym, projektem sieci Web i aplikacją usługi App Service skonfigurowaną w [temacie Tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md). W sekcjach WebJobs potrzebna jest aplikacja utworzona w obszarze [Wprowadzenie do usługi Azure WebJobs SDK][GetStartedWJ].

Przykłady kodu pokazane w tym samouczku są dla aplikacji sieci web MVC języka C#, ale procedury rozwiązywania problemów są takie same dla aplikacji Visual Basic i formularzy sieci Web.

W samouczku przyjęto założenie, że używasz programu Visual Studio 2019. 

Funkcja dzienników przesyłania strumieniowego działa tylko w przypadku aplikacji docelowych .NET Framework 4 lub nowszych.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Konfiguracja aplikacji i zarządzanie nimi
Program Visual Studio zapewnia dostęp do podzbioru funkcji zarządzania aplikacjami i ustawień konfiguracji dostępnych w [witrynie Azure portal](https://go.microsoft.com/fwlink/?LinkId=529715). W tej sekcji zobaczysz, co jest dostępne przy użyciu **Eksploratora serwera**. Aby wyświetlić najnowsze funkcje integracji platformy Azure, wypróbuj również **Cloud Explorer.** Oba okna można otworzyć z menu **Widok.**

1. Jeśli nie zalogowano się jeszcze na platformie Azure w programie Visual Studio, kliknij prawym przyciskiem myszy **pozycję Azure** i wybierz pozycję Połącz z **subskrypcją platformy Microsoft Azure** w **Eksploratorze serwera.**

    Alternatywą jest zainstalowanie certyfikatu zarządzania, który umożliwia dostęp do konta. Jeśli zdecydujesz się zainstalować certyfikat, kliknij prawym przyciskiem myszy węzeł **platformy Azure** w **Eksploratorze serwera,** a następnie wybierz polecenie **Zarządzaj subskrypcjami i filtruj** w menu kontekstowym. W oknie dialogowym **Zarządzanie subskrypcjami platformy Microsoft Azure** kliknij kartę **Certyfikaty,** a następnie kliknij pozycję **Importuj**. Postępuj zgodnie ze wskazówkami, aby pobrać, a następnie zaimportować plik subskrypcji (nazywany również plikiem *.publishsettings)* dla swojego konta platformy Azure.

   > [!NOTE]
   > Jeśli pobierzesz plik subskrypcji, zapisz go w folderze spoza katalogów kodu źródłowego (na przykład w folderze Pobrane), a następnie usuń go po zakończeniu importu. Złośliwy użytkownik, który uzyskuje dostęp do pliku subskrypcji, może edytować, tworzyć i usuwać usługi platformy Azure.
   >
   >

    Aby uzyskać więcej informacji na temat łączenia się z zasobami platformy Azure z programu Visual Studio, zobacz [Zarządzanie kontami, subskrypcjami i rolami administracyjnymi.](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)
2. W **Eksploratorze serwerów**rozwiń pozycję **Azure** i rozwiń **usługę App Service**.
3. Rozwiń grupę zasobów zawierającą aplikację utworzoną w [programie Utwórz aplikację ASP.NET w usłudze Azure App Service,](app-service-web-get-started-dotnet-framework.md)a następnie kliknij prawym przyciskiem myszy węzeł aplikacji i kliknij polecenie **Wyświetl ustawienia**.

    ![Wyświetlanie ustawień w Eksploratorze serwera](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Zostanie wyświetlone karta **Aplikacja Azure Web App** i można tam wyświetlić zadania zarządzania aplikacjami i konfiguracji, które są dostępne w programie Visual Studio.

    ![Okno aplikacji azure web app](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    W tym samouczku użyjesz listy rozwijanej rejestrowania i śledzenia. Użyjesz również zdalnego debugowania, ale użyjesz innej metody, aby ją włączyć.

    Aby uzyskać informacje na temat pól Ustawienia aplikacji i parametry połączenia w tym oknie, zobacz [Usługa aplikacji Azure App Service: Jak działają ciągi aplikacji i parametry połączenia](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Jeśli chcesz wykonać zadanie zarządzania aplikacjami, którego nie można wykonać w tym oknie, kliknij przycisk **Otwórz w portalu zarządzania,** aby otworzyć okno przeglądarki w witrynie Azure portal.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Uzyskiwanie dostępu do plików aplikacji w Eksploratorze serwera
Zazwyczaj wdrażasz projekt sieci `customErrors` web z flagą w pliku `On` `RemoteOnly`Web.config ustawionym na lub , co oznacza, że nie jest wyświetlany pomocny komunikat o błędzie, gdy coś pójdzie nie tak. W przypadku wielu błędów otrzymujesz tylko stronę podobną do jednej z następujących:

**Błąd serwera w aplikacji '/':**

![Strona z niepomocnym błędem](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Błąd:**

![Strona z niepomocnym błędem](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Witryna sieci Web nie może wyświetlić strony**

![Strona z niepomocnym błędem](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Często najprostszym sposobem znalezienia przyczyny błędu jest włączenie szczegółowych komunikatów o błędach, które pierwszy z poprzednich zrzutów ekranu wyjaśnia, jak to zrobić. Wymaga to zmiany w wdrożonym pliku Web.config. Można edytować plik *Web.config* w projekcie i ponownie wdrożyć projekt lub utworzyć [web.config przekształcić](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) i wdrożyć kompilacji debugowania, ale jest szybszy sposób: w **Eksploratorze rozwiązań**, można bezpośrednio przeglądać i edytować pliki w aplikacji zdalnej za pomocą funkcji *widoku zdalnego.*

1. W **Eksploratorze serwera**rozwiń pozycję **Azure**, rozwiń węzeł **App Service**, rozwiń grupę zasobów, w których znajduje się aplikacja, a następnie rozwiń węzeł aplikacji.

    Widoczne są węzły, które zapewniają dostęp do plików zawartości aplikacji i plików dziennika.
2. Rozwiń węzeł **Pliki** i kliknij dwukrotnie plik *Web.config.*

    ![Otwórz plik Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Program Visual Studio otwiera plik Web.config z aplikacji zdalnej i pokazuje [Remote] obok nazwy pliku na pasku tytułu.
3. Dodaj następujący wiersz `system.web` do elementu:

    `<customErrors mode="Off"></customErrors>`

    ![Edytowanie pliku Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Odśwież przeglądarkę, która wyświetla nieprzydatny komunikat o błędzie, a teraz pojawi się szczegółowy komunikat o błędzie, taki jak poniższy przykład:

    ![Szczegółowy komunikat o błędzie](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Wyświetlony błąd został utworzony przez dodanie wiersza wyświetlanego na czerwono do *Views\Home\Index.cshtml.)*

Edytowanie pliku Web.config jest tylko jednym z przykładów scenariuszy, w których możliwość odczytu i edytowania plików w aplikacji usługi App Service ułatwia rozwiązywanie problemów.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Aplikacje do zdalnego debugowania
Jeśli szczegółowy komunikat o błędzie nie zawiera wystarczających informacji i nie można ponownie utworzyć błędu lokalnie, innym sposobem rozwiązywania problemów jest zdalne uruchomienie w trybie debugowania. Można ustawić punkty przerwania, manipulować pamięcią bezpośrednio, krok po kroku przez kod, a nawet zmienić ścieżkę kodu.

Zdalne debugowanie nie działa w wersjach Express programu Visual Studio.

W tej sekcji pokazano, jak debugować zdalnie przy użyciu projektu utworzonego w [tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Otwórz projekt sieci Web utworzony w [obszarze Tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Otwórz *kontrolery\HomeController.cs*.

1. Usuń `About()` metodę i wstaw poniższy kod w jej miejsce.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Ustaw punkt przerwania](https://docs.microsoft.com/visualstudio/debugger/) w wierszu. `ViewBag.Message`

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie kliknij polecenie **Publikuj**.

1. Z listy rozwijanej **Profil** wybierz ten sam profil, który został użyty w [obszarze Tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md). Następnie kliknij pozycję Ustawienia.

1. W oknie dialogowym **Publikowanie** kliknij kartę **Ustawienia,** a następnie zmień **konfigurację** na **Debugowanie,** a następnie kliknij pozycję **Zapisz**.

    ![Publikowanie w trybie debugowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Kliknij przycisk **Opublikuj**. Po zakończeniu wdrażania i otworzy się przeglądarka na adres URL platformy Azure aplikacji, zamknij przeglądarkę.

1. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy aplikację, a następnie kliknij polecenie **Dołącz debuger**.

    ![Dołączanie debugera](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Przeglądarka automatycznie otwiera się na stronie głównej uruchomionej na platformie Azure. Może być trzeba odczekać około 20 sekund, podczas gdy platforma Azure konfiguruje serwer do debugowania. To opóźnienie występuje tylko przy pierwszym uruchomieniu w trybie debugowania w aplikacji w okresie 48-godzinnym. Po ponownym uruchomieniu debugowania w tym samym okresie, nie ma opóźnienia.

    > [!NOTE] 
    > Jeśli masz jakiekolwiek problemy z uruchomieniem debugera, spróbuj to zrobić za pomocą **Cloud Explorer** zamiast **Eksploratora serwera**.
    >

1. Kliknij polecenie **Informacje** w menu.

    Visual Studio zatrzymuje się w punkcie przerwania, a kod jest uruchomiony na platformie Azure, a nie na komputerze lokalnym.

1. Umieść wskaźnik `currentTime` myszy na zmiennej, aby wyświetlić wartość czasu.

    ![Wyświetlanie zmiennej w trybie debugowania uruchomionego na platformie Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Wyświetlony czas to czas serwera platformy Azure, który może znajdować się w innej strefie czasowej niż komputer lokalny.

1. Wprowadź nową wartość `currentTime` zmiennej, taką jak "Teraz działa na platformie Azure".

1. Naciśnij klawisz F5, aby kontynuować bieg.

     Strona Informacje uruchomiona na platformie Azure wyświetla nową wartość, która została wprowadzona do zmiennej currentTime.

     ![Strona Informacje o nowej wartości](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a>Zdalne debugowanie webjobs
W tej sekcji pokazano, jak debugować zdalnie przy użyciu projektu i aplikacji utworzonej w [aplikacji Wprowadzenie do usługi Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funkcje wyświetlane w tej sekcji są dostępne tylko w programie Visual Studio 2013 z aktualizacją 4 lub nowszą.

Zdalne debugowanie działa tylko z ciągłymi webjobs. Zaplanowane i na żądanie WebJobs nie obsługują debugowania.

1. Otwórz projekt sieci Web utworzony w [programie Wprowadzenie do programu Azure WebJobs SDK][GetStartedWJ].

2. W projekcie ContosoAdsWebJob otwórz *Functions.cs*.

3. [Ustaw punkt przerwania](https://docs.microsoft.com/visualstudio/debugger/) dla pierwszej `GnerateThumbnail` instrukcji w metodzie.

    ![Ustawianie punktu przerwania](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt sieci Web (nie projekt WebJob) i kliknij polecenie **Publikuj**.

5. Z listy rozwijanej **Profil** wybierz ten sam profil, który został użyty w [aplikacji Wprowadzenie do usługi Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Kliknij kartę **Ustawienia,** a następnie zmień **konfigurację** na **Debugowanie,** a następnie kliknij pozycję **Publikuj**.

    Visual Studio wdraża projekty web i WebJob, a przeglądarka otwiera się na adres URL platformy Azure aplikacji.

7. W **Eksploratorze serwera**rozwiń węzeł **Azure > App Service > grupę zasobów > aplikacji > WebJobs > Continuous**, a następnie kliknij prawym przyciskiem myszy **contosoAdsWebJob**.

8. Kliknij **pozycję Dołącz debuger**.

    ![Dołączanie debugera](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Przeglądarka automatycznie otwiera się na stronie głównej uruchomionej na platformie Azure. Może być trzeba odczekać około 20 sekund, podczas gdy platforma Azure konfiguruje serwer do debugowania. To opóźnienie występuje tylko przy pierwszym uruchomieniu w trybie debugowania w aplikacji w okresie 48-godzinnym. Po ponownym uruchomieniu debugowania w tym samym okresie, nie ma opóźnienia.

9. W przeglądarce internetowej otwartej na stronie głównej Reklamy Contoso utwórz nową reklamę.

    Utworzenie reklamy powoduje utworzenie wiadomości kolejki, która jest pobierana przez webjob i przetwarzana. Gdy WebJobs SDK wywołuje funkcję do przetwarzania wiadomości kolejki, kod uderza punkt przerwania.

10. Gdy debuger przerwy w punkcie przerwania, można sprawdzić i zmienić wartości zmiennych, gdy program jest uruchomiony w chmurze. Na poniższej ilustracji debuger pokazuje zawartość obiektu blobInfo, `GenerateThumbnail` który został przekazany do metody.

     ![obiekt blobInfo w debugerze](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Naciśnij klawisz F5, aby kontynuować bieg.

     Metoda `GenerateThumbnail` kończy tworzenie miniatury.

12. W przeglądarce odśwież stronę Indeks, a zobaczysz miniaturę.

13. W programie Visual Studio naciśnij klawisze SHIFT+F5, aby zatrzymać debugowanie.

14. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy węzeł ContosoAdsWebJob i kliknij polecenie **Wyświetl pulpit nawigacyjny**.

15. Zaloguj się przy użyciu poświadczeń platformy Azure, a następnie kliknij nazwę webjob, aby przejść do strony dla webjob.

     ![Kliknij pozycję ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Pulpit nawigacyjny pokazuje, że `GenerateThumbnail` funkcja wykonana niedawno.

     (Przy następnym **kliknięciu przycisku Wyświetl pulpit nawigacyjny**nie trzeba się logować, a przeglądarka przechodzi bezpośrednio na stronę witryny WebJob).

16. Kliknij nazwę funkcji, aby wyświetlić szczegółowe informacje o wykonaniu funkcji.

     ![Szczegóły funkcji](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Jeśli funkcja [napisał dzienniki](https://github.com/Azure/azure-webjobs-sdk/wiki), można kliknąć **ToggleOutput,** aby je zobaczyć.

## <a name="notes-about-remote-debugging"></a>Notes about remote debugging (Uwagi dotyczące zdalnego debugowania)

* Nie zaleca się uruchamiania w trybie debugowania w produkcji. Jeśli aplikacja produkcyjna nie jest skalowana w poziomie do wielu wystąpień serwera, debugowanie uniemożliwia serwerowi sieci web odpowiadanie na inne żądania. Jeśli masz wiele wystąpień serwera sieci web, po dołączeniu do debugera, otrzymasz losowe wystąpienie i nie masz sposobu, aby upewnić się, że kolejne żądania przeglądarki przejść do tego samego wystąpienia. Ponadto zazwyczaj nie wdrożyć kompilacji debugowania do produkcji i optymalizacji kompilatora dla kompilacji wydania może uniemożliwić pokazanie, co się dzieje wiersz po wierszu w kodzie źródłowym. W celu rozwiązywania problemów produkcyjnych najlepszym zasobem jest śledzenie aplikacji i dzienniki serwera sieci Web.
* Unikaj długich postojów w punktach przerwania podczas zdalnego debugowania. Platforma Azure traktuje proces, który jest zatrzymany na dłużej niż kilka minut jako proces nie odpowiada i zamyka go w dół.
* Podczas debugowania serwer wysyła dane do programu Visual Studio, co może mieć wpływ na opłaty za przepustowość. Aby uzyskać informacje na temat szybkości przepustowości, zobacz [Cennik platformy Azure](https://azure.microsoft.com/pricing/calculator/).
* Upewnij się, `debug` że atrybut `compilation` elementu w pliku *Web.config* jest ustawiony na wartość true. Jest ustawiona na true domyślnie podczas publikowania konfiguracji kompilacji debugowania.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Jeśli okaże się, że debuger nie krok do kodu, który chcesz debugować, może być konieczna zmiana just my code ustawienie.  Aby uzyskać więcej informacji, zobacz [Określanie, czy debugować tylko kod użytkownika przy użyciu tylko mój kod w programie Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* Czasomierz uruchamia się na serwerze po włączeniu funkcji zdalnego debugowania, a po 48 godzinach funkcja jest automatycznie wyłączona. Ten 48-godzinny limit odbywa się ze względów bezpieczeństwa i wydajności. Możesz łatwo włączyć tę funkcję z powrotem tyle razy, ile chcesz. Zaleca się pozostawienie go wyłączone, gdy nie są aktywnie debugowania.
* Debuger można ręcznie dołączyć do dowolnego procesu, nie tylko procesu aplikacji (w3wp.exe). Aby uzyskać więcej informacji na temat używania trybu debugowania w programie Visual Studio, zobacz [Debugowanie w programie Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Omówienie dzienników diagnostycznych
Aplikacja ASP.NET, która działa w aplikacji usługi App Service, może tworzyć następujące rodzaje dzienników:

* **Dzienniki śledzenia aplikacji**<br/>
  Aplikacja tworzy te dzienniki za pomocą metod wywoływania [system.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) klasy.
* **Dzienniki serwera sieci Web**<br/>
  Serwer sieci web tworzy wpis dziennika dla każdego żądania HTTP do aplikacji.
* **Szczegółowe dzienniki komunikatów o błędach**<br/>
  Serwer sieci web tworzy stronę HTML z dodatkowymi informacjami dla nieudanych żądań HTTP (żądania, które skutkują kodem stanu 400 lub wyższym).
* **Dzienniki śledzenia żądań nie powiodły się**<br/>
  Serwer sieci web tworzy plik XML ze szczegółowymi informacjami o śledzeniu dla nieudanych żądań HTTP. Serwer www udostępnia również plik XSL do formatowania pliku XML w przeglądarce.

Rejestrowanie wpływa na wydajność aplikacji, dzięki czemu platforma Azure umożliwia włączenie lub wyłączenie każdego typu dziennika w razie potrzeby. W przypadku dzienników aplikacji można określić, że należy zapisać tylko dzienniki powyżej określonego poziomu ważności. Podczas tworzenia nowej aplikacji domyślnie wszystkie rejestrowanie jest wyłączone.

Dzienniki są zapisywane w plikach w folderze *LogFiles* w systemie plików aplikacji i są dostępne za pośrednictwem protokołu FTP. Dzienniki serwera sieci Web i dzienniki aplikacji można również zapisywać na koncie usługi Azure Storage. Można zachować większą liczbę dzienników na koncie magazynu, niż jest to możliwe w systemie plików. Podczas korzystania z systemu plików jest ograniczona do maksymalnie 100 megabajtów dzienników. (Dzienniki systemu plików służą tylko do przechowywania krótkoterminowego. Platforma Azure usuwa stare pliki dziennika, aby zrobić miejsce dla nowych po osiągnięciu limitu.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Tworzenie i wyświetlanie dzienników śledzenia aplikacji
W tej sekcji wykonaj następujące zadania:

* Dodawanie instrukcji śledzenia do projektu sieci web utworzonego w [programie Wprowadzenie do platformy Azure i ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Wyświetlanie dzienników po uruchomieniu projektu lokalnie.
* Wyświetlanie dzienników, ponieważ są one generowane przez aplikację działającą na platformie Azure.

Aby uzyskać informacje dotyczące tworzenia dzienników aplikacji w webjobs, zobacz [Jak pracować z magazynem kolejek platformy Azure przy użyciu zestawu WebJobs SDK — Jak zapisywać dzienniki](https://github.com/Azure/azure-webjobs-sdk/wiki). Poniższe instrukcje dotyczące wyświetlania dzienników i kontrolowania sposobu ich przechowywania na platformie Azure dotyczą również dzienników aplikacji utworzonych przez webjobs.

### <a name="add-tracing-statements-to-the-application"></a>Dodawanie instrukcji śledzenia do aplikacji
1. Otwórz *controllers\HomeController.cs*i `Index`zastąp `About`, i `Contact` metody następującym kodem w celu dodania `Trace` instrukcji i `using` instrukcji dla: `System.Diagnostics`

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Dodaj `using System.Diagnostics;` instrukcję do górnej części pliku.

### <a name="view-the-tracing-output-locally"></a>Wyświetlanie lokalnie danych wyjściowych śledzenia
1. Naciśnij klawisz F5, aby uruchomić aplikację w trybie debugowania.

    Domyślny odbiornik śledzenia zapisuje wszystkie dane wyjściowe śledzenia do okna **Dane wyjściowe,** wraz z innymi dane wyjściowe debugowania. Na poniższej ilustracji przedstawiono dane wyjściowe `Index` z instrukcji śledzenia, które zostały dodane do metody.

    ![Śledzenie w oknie debugowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Poniższe kroki pokazują, jak wyświetlić dane wyjściowe śledzenia na stronie sieci web, bez kompilowania w trybie debugowania.
1. Otwórz plik Web.config aplikacji (ten znajdujący się w `<system.diagnostics>` folderze projektu) i dodaj `</configuration>` element na końcu pliku tuż przed elementem zamknięcia:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

Umożliwia `WebPageTraceListener` wyświetlanie danych wyjściowych śledzenia `/trace.axd`przez przejście do .
1. Dodaj <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">element</a> śledzenia `<system.web>` w pliku Web.config, na przykład w następującym przykładzie:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Naciśnij klawisze CTRL+F5, aby uruchomić aplikację.
1. Na pasku adresu okna przeglądarki dodaj *plik trace.axd* do adresu URL, a `http://localhost:53370/trace.axd`następnie naciśnij klawisz Enter (adres URL jest podobny do ).
1. Na stronie **Śledzenie aplikacji** kliknij pozycję **Wyświetl szczegóły** w pierwszym wierszu (nie w wierszu BrowserLink).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Zostanie wyświetlona strona **Szczegóły żądania,** a w sekcji **Informacje śledzenia** zobaczysz dane `Index` wyjściowe z instrukcji śledzenia dodanych do metody.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Domyślnie `trace.axd` jest dostępna tylko lokalnie. Jeśli chcesz udostępnić go z aplikacji zdalnej, można dodać `localOnly="false"` do `trace` elementu w pliku *Web.config,* jak pokazano w poniższym przykładzie:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Jednak włączenie `trace.axd` w aplikacji produkcyjnej nie jest zalecane ze względów bezpieczeństwa. W poniższych sekcjach zobaczysz łatwiejszy sposób odczytywania dzienników śledzenia w aplikacji usługi App Service.

### <a name="view-the-tracing-output-in-azure"></a>Wyświetlanie danych wyjściowych śledzenia na platformie Azure
1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt sieci Web i kliknij polecenie **Publikuj**.
2. W oknie dialogowym **Publikowanie w sieci Web** kliknij pozycję **Publikuj**.

    Po opublikowaniu aktualizacji przez program Visual Studio otwiera okno przeglądarki na stronie głównej (przy założeniu, że **nie wyczyszczyłeś docelowego adresu URL** na karcie **Połączenie).**
3. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Wyświetl dzienniki przesyłania strumieniowego**.

    ![Wyświetlanie dzienników przesyłania strumieniowego w menu kontekstowym](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    Okno **Dane wyjściowe** pokazuje, że masz połączenie z usługą przesyłania strumieniowego dziennika i dodaje wiersz powiadomień co minutę, który przechodzi bez dziennika do wyświetlenia.

    ![Wyświetlanie dzienników przesyłania strumieniowego w menu kontekstowym](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. W oknie przeglądarki, które pokazuje stronę główną aplikacji, kliknij pozycję **Kontakt**.

    W ciągu kilku sekund dane wyjściowe z śledzenia `Contact` na poziomie błędu dodane do metody pojawia się w oknie **Dane wyjściowe.**

    ![Śledzenie błędów w oknie Dane wyjściowe](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio wyświetla tylko ślady na poziomie błędu, ponieważ jest to ustawienie domyślne po włączeniu usługi monitorowania dziennika. Podczas tworzenia nowej aplikacji usługi App Service wszystkie rejestrowanie jest domyślnie wyłączone, tak jak widać po wcześniejszym otwarciu strony ustawień:

    ![Wylogowywanie aplikacji](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Jednak po wybraniu **opcji Wyświetl dzienniki przesyłania strumieniowego**program Visual Studio automatycznie zmienił **rejestrowanie aplikacji (system plików)** na **błąd**, co oznacza, że dzienniki na poziomie błędu są zgłaszane. Aby wyświetlić wszystkie dzienniki śledzenia, można zmienić to ustawienie na **Pełne**. Po wybraniu poziomu ważności niższego niż błąd, wszystkie dzienniki dla wyższych poziomów ważności są również zgłaszane. Tak więc po wybraniu pełnej, można również zobaczyć informacje, ostrzeżenie i dzienniki błędów.  

5. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy aplikację, a następnie kliknij polecenie **Wyświetl ustawienia** tak jak wcześniej.
6. Zmień **rejestrowanie aplikacji (system plików)** na **pełne**, a następnie kliknij przycisk **Zapisz**.

    ![Ustawianie poziomu śledzenia na pełny](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. W oknie przeglądarki, w którym jest teraz wyświetlana strona **Kontakt,** kliknij pozycję **Narzędzia główna**, a następnie kliknij pozycję **Informacje**, a następnie kliknij pozycję **Kontakt**.

    W ciągu kilku sekund **okno Dane wyjściowe** pokazuje wszystkie dane wyjściowe śledzenia.

    ![Pełne dane wyjściowe śledzenia](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    W tej sekcji włączono i wyłączono rejestrowanie przy użyciu ustawień aplikacji. Można również włączyć i wyłączyć detektory śledzenia, modyfikując plik Web.config. Jednak modyfikacja pliku Web.config powoduje, że domena aplikacji do recyklingu, podczas gdy włączenie rejestrowania za pośrednictwem konfiguracji aplikacji nie robi tego. Jeśli odtworzenie problemu zajmuje dużo czasu lub jest przerywane, recykling domeny aplikacji może ją "naprawić" i zmusić do zaczekania, aż nastąpi to ponownie. Włączenie diagnostyki na platformie Azure umożliwia natychmiastowe przechwytywanie informacji o błędach bez konieczności odtwarzania domeny aplikacji.

### <a name="output-window-features"></a>Funkcje okna wyjściowego
Karta **Dzienniki platformy Microsoft Azure** w oknie danych **wyjściowych** zawiera kilka przycisków i pole tekstowe:

![Przyciski kart Dzienniki](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Pełnią one następujące funkcje:

* Wyczyść okno **Dane wyjściowe.**
* Włączanie lub wyłączanie zawijania wyrazów.
* Uruchamianie lub zatrzymywania dzienników monitorowania.
* Określ, które dzienniki mają być monitorowane.
* Pobierz dzienniki.
* Filtruj dzienniki na podstawie ciągu wyszukiwania lub wyrażenia regularnego.
* Zamknij okno **Dane wyjściowe.**

Jeśli wprowadzisz ciąg wyszukiwania lub wyrażenie regularne, program Visual Studio filtruje informacje rejestrowania na kliencie. Oznacza to, że można wprowadzić kryteria po dzienniki są wyświetlane w oknie **Dane wyjściowe** i można zmienić kryteria filtrowania bez konieczności ponownego generowania dzienników.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Wyświetlanie dzienników serwera sieci Web
Dzienniki serwera sieci Web rejestrują całą aktywność HTTP dla aplikacji. Aby wyświetlić je w oknie **Dane wyjściowe,** należy włączyć je dla aplikacji i poinformować program Visual Studio, że chcesz je monitorować.

1. Na karcie **Konfiguracja aplikacji azure web** otwarty z **Eksploratora serwera**zmień logowanie serwera sieci Web na **Włączone,** a następnie kliknij przycisk **Zapisz**.

    ![Włączanie rejestrowania serwera sieci Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. W oknie **dane wyjściowe** kliknij przycisk **Określ dzienniki platformy Microsoft Azure, aby monitorować.**

    ![Określanie dzienników platformy Azure do monitorowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. W oknie dialogowym **Opcje rejestrowania platformy Microsoft Azure** wybierz pozycję **Dzienniki serwera sieci Web**, a następnie kliknij przycisk **OK**.

    ![Monitorowanie dzienników serwera www](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. W oknie przeglądarki, w które znajduje się aplikacja, kliknij pozycję **Narzędzia główna**, a następnie kliknij pozycję **Informacje**, a następnie kliknij pozycję **Kontakt**.

    Dzienniki aplikacji zazwyczaj pojawiają się najpierw, a następnie dzienniki serwera sieci web. Może być trzeba chwilę poczekać, aż dzienniki się pojawią.

    ![Loguje się do serwera sieci Web w oknie Dane wyjściowe](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Domyślnie po pierwszym włączeniu dzienników serwera sieci web przy użyciu programu Visual Studio, platforma Azure zapisuje dzienniki do systemu plików. Alternatywnie można użyć witryny Azure portal, aby określić, że dzienniki serwera sieci web powinny być zapisywane w kontenerze obiektów blob na koncie magazynu.

Jeśli używasz portalu, aby włączyć rejestrowanie serwera sieci web do konta magazynu platformy Azure, a następnie wyłączyć rejestrowanie w programie Visual Studio, po ponownym włączeniu rejestrowania w programie Visual Studio ustawienia konta magazynu są przywracane.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Wyświetlanie szczegółowych dzienników komunikatów o błędach
Szczegółowe dzienniki błędów zawierają dodatkowe informacje o żądaniach HTTP, które powodują kody odpowiedzi na błędy (400 lub więcej). Aby wyświetlić je w oknie **Dane wyjściowe,** należy włączyć je dla aplikacji i powiedzieć visual studio, że chcesz je monitorować.

1. Na karcie **Konfiguracja aplikacji azure web** otwarty z **Eksploratora serwera**zmień szczegółowe **komunikaty o błędach** **na Włączone,** a następnie kliknij przycisk **Zapisz**.

    ![Włączanie szczegółowych komunikatów o błędach](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. W oknie **dane wyjściowe** kliknij przycisk **Określ dzienniki platformy Microsoft Azure, aby monitorować.**

3. W oknie dialogowym **Opcje rejestrowania platformy Microsoft Azure** kliknij pozycję Wszystkie **dzienniki**, a następnie kliknij przycisk **OK**.

    ![Monitoruj wszystkie dzienniki](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na pasku adresu okna przeglądarki dodaj dodatkowy znak do adresu URL, aby spowodować `http://localhost:53370/Home/Contactx`błąd 404 (na przykład) i naciśnij klawisz Enter.

    Po kilku sekundach szczegółowy dziennik błędów pojawia się w oknie **dane wyjściowe** programu Visual Studio.

    ![Szczegółowy dziennik błędów — okno Wyjście](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Control+kliknij łącze, aby wyświetlić dane wyjściowe dziennika sformatowane w przeglądarce:

    ![Szczegółowy dziennik błędów — okno przeglądarki](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Pobieranie dzienników systemu plików
Wszystkie dzienniki, które można monitorować w oknie **Dane wyjściowe,** można również pobrać jako plik *zip.*

1. W oknie **Dane wyjściowe** kliknij pozycję **Pobierz dzienniki przesyłania strumieniowego**.

    ![Przyciski kart Dzienniki](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Eksplorator plików zostanie otwarty w folderze *Pobrane* z wybranym pobranym plikiem.

    ![Pobrany plik](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Wyodrębnij plik *zip,* a zobaczysz następującą strukturę folderów:

    ![Pobrany plik](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Dzienniki śledzenia aplikacji znajdują się w plikach *txt* w folderze *LogFiles\Application.*
   * Dzienniki serwera sieci Web znajdują się w plikach *dziennika* w folderze *LogFiles\http\RawLogs.* Można użyć narzędzia, takiego jak [Analizator dzienników,](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) aby wyświetlić i manipulować tymi plikami.
   * Szczegółowe dzienniki komunikatów o błędach znajdują się w plikach *.html* w folderze *LogFiles\DetailedErrors.*

     (Folder *wdrożeń* jest przeznaczony dla plików utworzonych przez publikowanie kontroli źródła; nie ma nic związanego z publikowaniem programu Visual Studio. Folder *Git* służy do śledzenia związanych z publikowaniem kontroli źródła i usługą przesyłania strumieniowego plików dziennika).  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Wyświetlanie dzienników śledzenia żądań nie powiodło się
Dzienniki śledzenia żądań nie powiodło się są przydatne, gdy trzeba zrozumieć szczegóły, jak usługi IIS obsługuje żądanie HTTP, w scenariuszach, takich jak przepisywanie adresów URL lub problemy z uwierzytelnianiem.

Aplikacje usługi App Service korzystają z tej samej funkcji śledzenia żądań, które zostały udostępnione w usługach IIS 7.0 lub nowszych. Nie masz jednak dostępu do ustawień IIS, które konfigurują, które błędy są rejestrowane. Po włączeniu śledzenia żądań nie powiodło się, wszystkie błędy są przechwytywane.

Śledzenie żądań nie powiodło się za pomocą programu Visual Studio, ale nie można ich wyświetlić w programie Visual Studio. Te dzienniki są plikami XML. Usługa dziennika przesyłania strumieniowego monitoruje tylko pliki, które są uważane za czytelne w trybie zwykłego tekstu: *.txt*, *.html*i *.log.*

Dzienniki śledzenia żądań nie powiodło się można wyświetlać w przeglądarce bezpośrednio za pośrednictwem protokołu FTP lub lokalnie po użyciu narzędzia FTP w celu pobrania ich na komputer lokalny. W tej sekcji wyświetlisz je bezpośrednio w przeglądarce.

1. Na karcie **Konfiguracja** okna **aplikacji Azure Web App,** które zostało otwarte z **Eksploratora serwera,** zmień **śledzenie żądań nie powiodło** się **na włączone,** a następnie kliknij przycisk **Zapisz**.

    ![Włącz śledzenie żądań nie powiodło się](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na pasku adresu okna przeglądarki, w które znajduje się aplikacja, dodaj dodatkowy znak do adresu URL i kliknij przycisk Enter, aby spowodować błąd 404.

    Powoduje to, że dziennik śledzenia żądania nie powiodło się, aby utworzyć, a następujące kroki pokazują, jak wyświetlić lub pobrać dziennik.

3. W programie Visual Studio na karcie **Konfiguracja** okna **aplikacji Azure Web App** kliknij pozycję Otwórz w portalu **zarządzania**.

4. Na stronie **Ustawienia witryny** [Azure portal](https://portal.azure.com) dla aplikacji kliknij pozycję **Poświadczenia wdrożenia**, a następnie wprowadź nową nazwę użytkownika i hasło.

    ![Nowa nazwa użytkownika i hasło FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Podczas logowania należy użyć pełnej nazwy użytkownika z poprzedzonym nazwą aplikacji. Na przykład, jeśli wpiszesz "myid" jako nazwę użytkownika, a witryna jest "myexample", zalogujesz się jako "myexample\myid".
    >

5. W nowym oknie przeglądarki przejdź do adresu URL wyświetlanego w obszarze **Nazwa hosta FTP** lub **NAZWA HOSTA FTPS** na stronie **Przegląd** aplikacji.

6. Zaloguj się przy użyciu poświadczeń FTP utworzonych wcześniej (w tym prefiks nazwy aplikacji dla nazwy użytkownika).

    Przeglądarka pokazuje folder główny aplikacji.

7. Otwórz folder *LogFiles.*

    ![Otwórz folder LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Otwórz folder o nazwie W3SVC plus wartość liczbową.

    ![Otwórz folder W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Folder zawiera pliki XML dla wszystkich błędów, które zostały zarejestrowane po włączeniu śledzenia żądań nie powiodło się, oraz plik XSL, którego przeglądarka może użyć do formatowania kodu XML.

    ![Folder W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Kliknij plik XML dla żądania, dla którego nie powiodło się żądanie, dla którego chcesz wyświetlić informacje o ślieniu.

    Na poniższej ilustracji przedstawiono część informacji śledzenia dla przykładowego błędu.

    ![Śledzenie żądań nie powiodło się w przeglądarce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Kolejne kroki
Widzisz, jak Visual Studio ułatwia wyświetlanie dzienników utworzonych przez aplikację usługi App Service. W poniższych sekcjach znajdują się łącza do większej liczby zasobów dotyczących powiązanych tematów:

* Rozwiązywanie problemów z usługą App Service
* Debugowanie w Visual Studio
* Zdalne debugowanie na platformie Azure
* Śledzenie w aplikacjach ASP.NET
* Analizowanie dzienników serwera sieci Web
* Analizowanie dzienników śledzenia żądań nie powiodło się
* Debugowanie usług w chmurze

### <a name="app-service-troubleshooting"></a>Rozwiązywanie problemów z usługą App Service
Aby uzyskać więcej informacji na temat rozwiązywania problemów z aplikacjami w usłudze Azure App Service, zobacz następujące zasoby:

* [Jak monitorować aplikacje](web-sites-monitor.md)
* [Badanie przecieków pamięci w usłudze Azure App Service za pomocą programu Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Wpis w blogu usługi Microsoft ALM dotyczący funkcji programu Visual Studio do analizowania problemów z pamięcią zarządzaną.
* [Narzędzia online usługi Azure App Service, o których powinieneś wiedzieć.](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) Wpis na blogu Amit Apple.

Aby uzyskać pomoc dotyczącą określonego pytania dotyczące rozwiązywania problemów, uruchom wątek na jednym z następujących forów:

* [Forum platformy Azure w witrynie ASP.NET](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum platformy Azure w systemie Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Debugowanie w Visual Studio
Aby uzyskać więcej informacji na temat używania trybu debugowania w programie Visual Studio, zobacz [Debugowanie w programie Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) i [wskazówki dotyczące debugowania za pomocą programu Visual Studio 2010.](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)

### <a name="remote-debugging-in-azure"></a>Zdalne debugowanie na platformie Azure
Aby uzyskać więcej informacji na temat zdalnego debugowania aplikacji usługi App Service i aplikacji WebJobs, zobacz następujące zasoby:

* [Wprowadzenie do zdalnego debugowania usługi Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Wprowadzenie do zdalnego debugowania usługi Azure App Service część 2 — wewnątrz zdalnego debugowania](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Wprowadzenie do zdalnego debugowania w usłudze Azure App Service część 3 — środowisko wielu wystąpień i git](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Debugowanie webjobs (wideo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Jeśli aplikacja korzysta z interfejsu api sieci Web platformy Azure lub usług przenośnych zaplecza i trzeba debugować, zobacz [Debugowanie .NET backend w programie Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Śledzenie w aplikacjach ASP.NET
Nie ma dokładnych i aktualnych wprowadzeń do śledzenia ASP.NET dostępnych w Internecie. Najlepsze, co możesz zrobić, to zacząć od starych materiałów wprowadzających napisanych dla formularzy sieci Web, ponieważ MVC jeszcze nie istniał, i uzupełnić to nowszymi wpisami na blogu, które koncentrują się na konkretnych kwestiach. Kilka dobrych miejsc do rozpoczęcia są następujące zasoby:

* [Monitorowanie i telemetria (tworzenie rzeczywistych aplikacji w chmurze za pomocą platformy Azure).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)<br>
  Rozdział książki elektronicznej z zaleceniami dotyczącymi śledzenia w aplikacjach w chmurze platformy Azure.
* [ASP.NET śledzenie](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Stary, ale nadal dobry zasób do podstawowego wprowadzenia do tematu.
* [Obiekty nasłuchujące śledzenia](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informacje o odbiornikach śledzenia, ale nie wspomina [o Stronie WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Przewodnik: Integracja ASP.NET śledzenia z system.Diagnostics Tracing](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Ten artykuł jest również stary, ale zawiera kilka dodatkowych informacji, które nie obejmuje artykuł wprowadzający.
* [Śledzenie w ASP.NET widoków maszynki do golenia MVC](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Oprócz śledzenia w widokach Razor, post wyjaśnia również, jak utworzyć filtr błędów w celu rejestrowania wszystkich nieobsługiwał wyjątków w aplikacji MVC. Aby uzyskać informacje dotyczące sposobu rejestrowania wszystkich nieobsługiwałem wyjątków w aplikacji formularzy sieci Web, zobacz global.asax przykład w [kompletny przykład dla obsługi błędów](/previous-versions/bb397417(v=vs.140)) w msdn. W formularzach MVC lub Web Forms, jeśli chcesz rejestrować pewne wyjątki, ale niech domyślna obsługa struktury zacznie obowiązywać dla nich, można złapać i rethrow jak w poniższym przykładzie:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Rejestrowanie śledzenia diagnostyki przesyłania strumieniowego z wiersza polecenia platformy Azure (plus spojrzenie!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Jak używać wiersza polecenia, aby zrobić to, co ten samouczek pokazuje, jak to zrobić w programie Visual Studio. [Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) to narzędzie do debugowania aplikacji ASP.NET.
* [Korzystanie z rejestrowania i diagnostyki aplikacji internetowych - z Davidem Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) i [dziennikami strumieniowymi z aplikacji internetowych - z Davidem Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Filmy Scotta Hanselmana i Davida Ebbo.

W przypadku rejestrowania błędów alternatywą dla pisania własnego kodu śledzenia jest użycie struktury rejestrowania typu open source, takiej jak [ELMAH](https://nuget.org/packages/elmah/). Aby uzyskać więcej informacji, zobacz [scott Hanselman blogu o ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Ponadto nie trzeba używać ASP.NET lub `System.Diagnostics` śledzenia, aby uzyskać dzienniki przesyłania strumieniowego z platformy Azure. Usługa przesyłania strumieniowego dziennika aplikacji App Service strumieniuje dowolny plik *.txt*, *.html*lub *.log,* który znajduje się w folderze *LogFiles.* W związku z tym można utworzyć własny system rejestrowania, który zapisuje do systemu plików aplikacji, a plik jest automatycznie przesyłany strumieniowo i pobierane. Wszystko, co musisz zrobić, to napisać kod aplikacji, który tworzy pliki w folderze *d:\home\logfiles.*

### <a name="analyzing-web-server-logs"></a>Analizowanie dzienników serwera sieci Web
Aby uzyskać więcej informacji na temat analizowania dzienników serwera sieci web, zobacz następujące zasoby:

* [Usługa LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Narzędzie do przeglądania danych w dziennikach serwera www (*.log* files).
* [Rozwiązywanie problemów z wydajnością usług IIS lub błędów aplikacji przy użyciu programu LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Wprowadzenie do narzędzia Analizator dziennika, którego można używać do analizowania dzienników serwera sieci web.
* [Posty na blogu Robert McMurray na korzystanie LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Kod stanu HTTP w programach IIS 7.0, IIS 7.5 i IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analizowanie dzienników śledzenia żądań nie powiodło się
Witryna sieci Web microsoft technet zawiera [za pomocą śledzenia żądań nie powiodło się,](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) które mogą być przydatne do zrozumienia, jak korzystać z tych dzienników. Jednak ta dokumentacja koncentruje się głównie na konfigurowaniu śledzenia żądań nie powiodło się w usługach IIS, czego nie można zrobić w usłudze Azure App Service.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
