---
title: Rozwiązywanie problemów z aplikacją przy użyciu programu Visual Studio — usłudze Azure App Service
description: Dowiedz się, jak rozwiązywać problemy z aplikacji usługi App Service przy użyciu zdalne debugowanie, śledzenie i narzędzia rejestrowania, które są wbudowane w programie Visual Studio 2013.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: ''
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: cbf6a44f1a3210906ec7ab0d04eecb997bc2c470
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412815"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Rozwiązywanie problemów z aplikacją w usłudze Azure App Service przy użyciu programu Visual Studio
## <a name="overview"></a>Omówienie
W tym samouczku pokazano, jak używać narzędzi programu Visual Studio, aby pomóc w debugowaniu aplikacji w [usługi App Service](https://go.microsoft.com/fwlink/?LinkId=529714), uruchamiając w [tryb debugowania](https://docs.microsoft.com/visualstudio/debugger/) zdalne lub, wyświetlając Dzienniki aplikacji i dzienników serwera internetowego.

Dowiesz się:

* Funkcji zarządzania aplikacji są dostępne w programie Visual Studio.
* Jak szybko wprowadź zmiany w zdalnym aplikacji przy użyciu widoku zdalnego programu Visual Studio.
* Jak uruchomić tryb debugowania zdalnego, podczas projektu działa na platformie Azure, zarówno dla aplikacji, jak i zadanie WebJob.
* Jak utworzyć dzienniki śledzenia aplikacji i wyświetlać je podczas aplikacji jest utworzenie ich.
* Jak wyświetlić dzienniki serwera sieci web, w tym szczegółowe komunikaty o błędach i śledzenie żądań zakończonych niepowodzeniem.
* Jak wysyłać dzienniki diagnostyczne do usługi Azure Storage konta, a je wyświetlić.

Jeśli masz program Visual Studio Ultimate, można również użyć [IntelliTrace](/visualstudio/debugger/intellitrace) do debugowania. IntelliTrace nie została uwzględniona w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku działa środowisko programistyczne, projekt sieci web i aplikacji usługi App Service, który został skonfigurowany w [tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md). Dla sekcji zadań Webjob, konieczne będzie aplikacji, który zostanie utworzony w [Rozpoczynanie pracy z usługą Azure WebJobs SDK][GetStartedWJ].

Przykłady kodu pokazano, w tym samouczku są dla aplikacji sieci web MVC C#, ale procedur rozwiązywania problemów są takie same dla aplikacji Visual Basic i formularzy sieci Web.

W samouczku przyjęto założenie, że używasz programu Visual Studio 2019 r. 

Dzienniki przesyłania strumieniowego funkcja działa tylko dla aplikacji przeznaczonych dla środowiska .NET Framework 4 lub nowszej.

## <a name="sitemanagement"></a>Konfiguracja aplikacji i zarządzanie nim
Program Visual Studio zapewnia dostęp do podzbioru funkcji zarządzania aplikacji i ustawień konfiguracji dostępne w [witryny Azure portal](https://go.microsoft.com/fwlink/?LinkId=529715). W tej sekcji dowiesz się, co jest dostępne przy użyciu **Eksploratora serwera**. Aby wyświetlić najnowsze funkcje integracji platformy Azure, wypróbuj **programu Cloud Explorer** również. Możesz otworzyć zarówno systemu windows z **widoku** menu.

1. Jeśli nie są już zalogowano do platformy Azure w programie Visual Studio, kliknij prawym przyciskiem myszy **Azure** i wybierz pozycję Połącz z **subskrypcję platformy Microsoft Azure** w **Eksploratora serwera**.

    Alternatywą jest instalowanie certyfikatu zarządzania, który umożliwia dostęp do Twojego konta. Jeśli zdecydujesz się zainstalować certyfikat, kliknij prawym przyciskiem myszy **Azure** węzła w **Eksploratora serwera**, a następnie wybierz pozycję **subskrypcji filtru i Zarządzaj** w menu kontekstowym. W **Zarządzanie subskrypcji platformy Microsoft Azure** okno dialogowe, kliknij przycisk **certyfikaty** kartę, a następnie kliknij przycisk **importu**. Postępuj zgodnie z instrukcjami, aby pobierać i importować plik subskrypcji (nazywane również *.publishsettings* pliku) dla konta platformy Azure.

   > [!NOTE]
   > Jeśli pobierzesz plik subskrypcji, zapisz go w folderze poza katalogów kodu źródłowego (na przykład w folderze pobrane), a następnie usuń ją po zakończeniu importowania. Złośliwy użytkownik, który uzyskuje dostęp do pliku subskrypcji można edytować, tworzenie i usuwanie usług platformy Azure.
   >
   >

    Aby uzyskać więcej informacji na temat nawiązywania połączenia z zasobami platformy Azure z programu Visual Studio, zobacz [Zarządzanie kontami, subskrypcjami i rolami administracyjnymi](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. W **Eksploratora serwera**, rozwiń węzeł **Azure** i rozwiń **usługi App Service**.
3. Rozwiń grupę zasobów, która zawiera aplikacji, który został utworzony w [tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md), a następnie kliknij prawym przyciskiem myszy węzeł aplikacji i kliknij **ustawienia widoku**.

    ![Ustawienia widoku w Eksploratorze serwera](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **Aplikacji sieci Web platformy Azure** zostanie wyświetlona karta i widać tam zarządzania i konfiguracji zadania związane z aplikacjami, które są dostępne w programie Visual Studio.

    ![Usługa Azure okna aplikacji sieci Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    W tym samouczku użyjesz, rejestrowanie i śledzenie list rozwijanych. Skorzystasz także, zdalne debugowanie, ale będzie użyć innej metody, aby go włączyć.

    Aby uzyskać informacji na temat ustawień aplikacji i parametry połączenia polami w tym oknie, zobacz [usługi Azure App Service: Sposób działania ciągów Application Strings and połączenia](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Aby wykonać zadania zarządzania aplikacji, która nie może odbywać się w tym oknie, kliknij przycisk **Otwórz w portalu zarządzania** można otworzyć okna przeglądarki w witrynie Azure Portal.

## <a name="remoteview"></a>Dostęp do plików aplikacji w Eksploratorze serwera
Zazwyczaj wdrażanie projektu sieci web za pomocą `customErrors` w pliku Web.config, Ustaw flagę `On` lub `RemoteOnly`, co oznacza, że nie otrzymujesz komunikat o przydatne, gdy coś pójdzie nie tak. Wiele błędów wszystko, co możesz uzyskać jest stroną, jak jeden z poniższych:

**Błąd serwera w aplikacji» /»:**

![Strona błędu zbędny](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Wystąpił błąd:**

![Strona błędu zbędny](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Witryna sieci Web nie można wyświetlić strony**

![Strona błędu zbędny](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Najprostszym sposobem, aby znaleźć przyczynę błędu jest często Włącz szczegółowe komunikaty o błędach, które pierwszy poprzednich zrzutach ekranu wyjaśniono sposób wykonywania. Wymaga zmian w wdrożonym pliku Web.config. Można edytować *Web.config* plik w projekcie i ponownie wdrożyć projekt lub Utwórz [przekształcenia pliku Web.config](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) i wdrożyć kompilację debugowania, ale istnieje szybszy sposób: w **Eksploratora rozwiązań** , można bezpośrednio wyświetlać i edytować pliki w zdalnej aplikacji przy użyciu *widoku zdalny* funkcji.

1. W **Eksploratora serwera**, rozwiń węzeł **Azure**, rozwiń węzeł **usługi App Service**, rozwiń grupę zasobów, która aplikacja znajduje się w, a następnie rozwiń węzeł aplikacji.

    Zostanie wyświetlony węzły, które umożliwiają dostęp do plików zawartości i pliki dziennika aplikacji.
2. Rozwiń **pliki** węzłem, a następnie kliknij dwukrotnie plik *Web.config* pliku.

    ![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio otwiera plik Web.config ze zdalnej aplikacji i pokazuje [zdalnego] obok nazwy pliku, na pasku tytułu.
3. Dodaj następujący wiersz do `system.web` elementu:

    `<customErrors mode="Off"></customErrors>`

    ![Edytowanie pliku Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Odśwież przeglądarkę, który jest wyświetlany komunikat o błędzie zbędny, a teraz uzyskać szczegółowy komunikat o błędzie, takim jak poniższy:

    ![Szczegółowy komunikat o błędzie](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Błąd pokazany został utworzony przez dodanie wiersza na czerwono na *Views\Home\Index.cshtml*.)

Edytowanie pliku Web.config jest tylko jeden przykład scenariuszy, w których możliwość odczytu i edycji plików w aplikacji usługi app Service ułatwienia rozwiązania problemu.

## <a name="remotedebug"></a>Zdalne debugowanie aplikacji
Jeśli komunikat szczegółowy komunikat o błędzie nie zawiera informacji wystarczających i nie można ponownie utworzyć błąd lokalnie, rozwiązywanie problemów w inny sposób jest zdalnie uruchomić tryb debugowania. Możesz ustawić punkty przerwania, bezpośrednie manipulowanie pamięci, przejść przez kod i nawet zmienić ścieżkę kodu.

Zdalne debugowanie nie działa w wersjach Express programu Visual Studio.

W tej sekcji pokazano, jak debugowanie, zdalne za pomocą programu project, możesz utworzyć w [tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Otwórz projekt sieci web, który został utworzony w [tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md).

2. Otwórz *Controllers\HomeController.cs*.

3. Usuń `About()` metody i Wstaw następujący kod w jego miejscu.

``` c#
public ActionResult About()
{
    string currentTime = DateTime.Now.ToLongTimeString();
    ViewBag.Message = "The current time is " + currentTime;
    return View();
}
```

1. [Ustaw punkt przerwania](https://docs.microsoft.com/visualstudio/debugger/) na `ViewBag.Message` wiersza.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i kliknij przycisk **Publikuj**.

1. W **profilu** listy rozwijanej, wybierz taki sam, jak używane w profilu [tworzenie aplikacji ASP.NET w usłudze Azure App Service](app-service-web-get-started-dotnet-framework.md). Następnie kliknij przycisk Ustawienia.

1. W **Publikuj** okno dialogowe, kliknij przycisk **ustawienia** kartę, a następnie zmień **konfiguracji** do **debugowania**, a następnie kliknij przycisk  **Zapisz**.

    ![Publikowanie w trybie debugowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Kliknij przycisk **publikowania**. Po wdrożeniu zostanie zakończone i przeglądarki otwiera Azure adres URL aplikacji, zamknij przeglądarkę.

1. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy aplikację, a następnie kliknij przycisk **dołączyć debuger**.

    ![Dołącz debuger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Przeglądarka automatycznie otworzy stronę główną, działające na platformie Azure. Być może musisz Zaczekaj 20 sekund Azure konfiguruje serwer w celu debugowania. To opóźnienie występuje tylko podczas pierwszego uruchomienia w trybie debugowania aplikacji w okresie 48 godzin. Po rozpoczęciu debugowania ponownie w tym samym okresie, nie jest opóźnienie.

    > [!NOTE] 
    > Jeśli masz problemy z uruchamiania debugera, spróbuj to zrobić za pomocą **programu Cloud Explorer** zamiast **Eksploratora serwera**.
    >

1. Kliknij przycisk **o** w menu.

    Program Visual Studio zatrzymuje się na punkcie przerwania, a kod działa na platformie Azure, nie na komputerze lokalnym.

1. Umieść kursor nad `currentTime` zmiennej, aby wyświetlić wartość czasu.

    ![Zmienna widoku w trybie debugowania, działające na platformie Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Czas, który zostanie wyświetlony jest czas serwer platformy Azure, który może znajdować się w innej strefie czasowej niż komputera lokalnego.

1. Wprowadź nową wartość dla `currentTime` zmiennych, takich jak "Teraz uruchomiona na platformie Azure".

1. Naciśnij klawisz F5, aby kontynuować działanie.

     Na stronie informacje działających na platformie Azure — wyświetlenie nowej wartości wprowadzone w zmiennej bieżącagodzina.

     ![Informacje o stronie przy użyciu nowej wartości](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Zdalne debugowanie zadań Webjob
W tej sekcji pokazano, jak można debugować zdalnie przy użyciu projektu i aplikacji, możesz utworzyć w [Rozpoczynanie pracy z usługą Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funkcje przedstawione w tej sekcji są dostępne tylko w programie Visual Studio 2013 z aktualizacją Update 4 lub nowszej.

Zdalne debugowanie działa tylko ciągłych zadań Webjob. Zadania Webjob zaplanowanych, jak i na żądanie nie obsługuje debugowania.

1. Otwórz projekt sieci web, który został utworzony w [Rozpoczynanie pracy z usługą Azure WebJobs SDK][GetStartedWJ].

2. W projekcie ContosoAdsWebJob Otwórz *Functions.cs*.

3. [Ustaw punkt przerwania](https://docs.microsoft.com/visualstudio/debugger/) w pierwszej instrukcji w `GnerateThumbnail` metody.

    ![Ustaw punkt przerwania](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt sieci web (a nie projekt zadania WebJob) i kliknij przycisk **Publikuj**.

5. W **profilu** listy rozwijanej, wybierz taki sam, jak używane w profilu [Rozpoczynanie pracy z usługą Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Kliknij przycisk **ustawienia** kartę i zmień **konfiguracji** do **debugowania**, a następnie kliknij przycisk **Publikuj**.

    Program Visual Studio wdroży internetowych i projekty zadań WebJob, a w przeglądarce zostanie otwarta do adresu URL platformy Azure w Twojej aplikacji.

7. W **Eksploratora serwera**, rozwiń węzeł **Azure > usługi App Service > Grupa zasobów > aplikacji > zadania Webjob > ciągłe**i kliknij prawym przyciskiem myszy **ContosoAdsWebJob**.

8. Kliknij przycisk **dołączyć debuger**.

    ![Dołącz debuger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Przeglądarka automatycznie otworzy stronę główną, działające na platformie Azure. Być może musisz Zaczekaj 20 sekund Azure konfiguruje serwer w celu debugowania. To opóźnienie występuje tylko podczas pierwszego uruchomienia w trybie debugowania aplikacji w okresie 48 godzin. Po rozpoczęciu debugowania ponownie w tym samym okresie, nie jest opóźnienie.

9. W przeglądarce sieci web, który jest otwierany na stronę główną Contoso Ads Tworzenie nowej reklamy.

    Tworzenie usługi ad powoduje, że komunikatu w kolejce do utworzenia, który jest wykrywane przez zadania WebJob, a następnie przetwarzany. Gdy zestaw SDK zadań Webjob wywołuje funkcję można przetworzyć komunikatu kolejki, trafienia kodu, punkt przerwania.

10. Kiedy debuger przerywa na punkt przerwania, możesz sprawdzić i zmienić wartości zmiennych, gdy program jest uruchomiony w chmurze. Na poniższej ilustracji wyświetlana jest zawartość blobInfo obiektu, który został przekazany do debugera `GenerateThumbnail` metody.

     ![Obiekt blobInfo w debugerze](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Naciśnij klawisz F5, aby kontynuować działanie.

     `GenerateThumbnail` Metoda zakończy się miniaturę tworzenia.

12. W przeglądarce Odśwież stronę indeksu i zobaczyć miniaturę.

13. W programie Visual Studio naciśnij klawisze SHIFT + F5, aby zatrzymać debugowanie.

14. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy węzeł ContosoAdsWebJob i kliknij przycisk **Wyświetl pulpit nawigacyjny**.

15. Zaloguj się przy użyciu swoich poświadczeń platformy Azure, a następnie kliknij nazwę zadania WebJob, aby przejść do strony dla zadania WebJob.

     ![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Pulpit nawigacyjny pokazuje, że `GenerateThumbnail` funkcja ostatnio wykonane.

     (Przy następnym kliknięciu **Wyświetl pulpit nawigacyjny**, nie musisz się zalogować i przeglądarce przechodzi bezpośrednio do strony dla zadania WebJob.)

16. Kliknij nazwę funkcji, aby zobaczyć szczegółowe informacje dotyczące wykonywania funkcji.

     ![Szczegóły funkcji](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Jeśli funkcja [napisał dzienniki](https://github.com/Azure/azure-webjobs-sdk/wiki), możesz je kliknąć **ToggleOutput** widoczne.

## <a name="notes-about-remote-debugging"></a>Uwagi dotyczące zdalnego debugowania

* Uruchamianie w trybie debugowania w środowisku produkcyjnym nie jest zalecane. W przypadku aplikacji produkcyjnych nie zostanie przeskalowana do wielu wystąpień serwera, debugowanie zapobiega serwer sieci web odpowiada na inne żądania. W przypadku wielu wystąpieniach serwerów sieci web po dołączeniu do debugera, otrzymujesz losową wystąpienia, a nie ma możliwości, aby upewnić się, że żądania kolejnych przeglądarki przejdź do tego samego wystąpienia. Ponadto zwykle nie są wdrażane do kompilacji debugowanej do środowiska produkcyjnego i optymalizacje kompilatora dla kompilacji wydania może spowodować, że pokazać, co się dzieje linii w kodzie źródłowym. Podczas rozwiązywania problemów produkcyjnych, najlepsze zasobu to aplikacja sieci web i śledzenie dzienniki serwera.
* Unikaj długich zatrzymuje w punktach przerwania podczas zdalnego debugowania. Azure traktuje procesu, który zostanie zatrzymana przez czas dłuższy niż kilka minut jako proces nie odpowiada i zamyka go.
* Podczas debugowania, serwer wysyła dane do programu Visual Studio, które mogą wpłynąć na opłaty za przepustowość. Aby uzyskać informacji na temat stawki za przepustowość, zobacz [cennika systemu Azure](https://azure.microsoft.com/pricing/calculator/).
* Upewnij się, że `debug` atrybutu `compilation` element *Web.config* plik jest ustawiony na wartość true. Jest ustawiona wartość true, domyślnie, gdy opublikujesz konfiguracji kompilacji debugowania.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Jeśli okaże się, że debuger nie wkroczyć do kodu, który chcesz debugować, trzeba będzie zmienić ustawienie tylko mój kod.  Aby uzyskać więcej informacji, zobacz [Określ, czy w celu debugowania tylko kodu użytkownika przy użyciu tylko mój kod w programie Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* Czasomierz uruchamia się na serwerze, po włączeniu funkcji debugowania zdalnego, a po upływie 48 godzin tej funkcji jest automatycznie wyłączana. Ze względów bezpieczeństwa i wydajności odbywa się to ograniczenie 48 godzin. Można łatwo włączyć tę funkcję ponownie dowolną liczbę razy. Zaleca się pozostawienie ją wyłączoną, gdy nie są aktywnego debugowania.
* Można ręcznie dołączyć debuger do dowolnego procesu nie tylko proces aplikacji (w3wp.exe). Aby uzyskać więcej informacji o sposobie używania trybu debugowania w programie Visual Studio, zobacz [debugowania w programie Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="logsoverview"></a>Przegląd dzienników diagnostycznych
Aplikacji ASP.NET, który jest uruchamiany w aplikacji usługi App Service można utworzyć następujące rodzaje dzienników:

* **Dzienniki śledzenia aplikacji**<br/>
  Aplikacja tworzy te dzienniki przez wywołanie metody [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) klasy.
* **Dzienniki serwera sieci Web**<br/>
  Serwer sieci web tworzy wpis dziennika dla każdego żądania HTTP do aplikacji.
* **Dzienniki komunikat szczegółowy komunikat o błędzie**<br/>
  Serwer sieci web tworzy stronę HTML z pewne dodatkowe informacje dotyczące żądań zakończonych niepowodzeniem HTTP (żądania, których wynikiem kod stanu 400 lub nowszej).
* **Dzienniki nieudanych żądań śledzenia**<br/>
  Serwer sieci web tworzy plik XML z informacjami o szczegółowe śledzenie niepomyślnych żądań HTTP. Serwer sieci web udostępnia również pliku XSL do formatu XML w przeglądarce.

Rejestrowanie znacząco wpływa na wydajność aplikacji, więc Azure zapewnia możliwości, aby włączyć lub wyłączyć każdego typu dziennika, zgodnie z potrzebami. Dzienniki aplikacji można określić, zapisywane tylko dzienniki powyżej określonego poziomu ważności. Podczas tworzenia nowej aplikacji, domyślnie wszystkie logowania jest wyłączona.

Dzienniki są zapisywane w plikach w *LogFiles* folder w systemie plików, aplikacji i są dostępne za pośrednictwem protokołu FTP. Dzienniki aplikacji i dzienników serwera internetowego można również będą zapisywane na koncie usługi Azure Storage. Można przechowywać większe ilości dzienniki na koncie magazynu, niż jest to możliwe w systemie plików. Jest ograniczona do maksymalnie 100 megabajtów dzienniki, korzystając z systemu plików. (Dzienniki systemu plików są tylko do krótkoterminowego przechowywania danych. Azure usuwa stare pliki dziennika, aby zwolnić miejsce na nowe, po osiągnięciu limitu).  

## <a name="apptracelogs"></a>Tworzenie i wyświetlanie dzienników śledzenia aplikacji
W tej sekcji należy wykonać następujące zadania:

* Dodawanie instrukcji śledzenia do projektu sieci web, który został utworzony w [Rozpoczynanie pracy z platformą Azure i programu ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Przejrzyj dzienniki, gdy lokalne uruchamianie projektu.
* Przejrzyj dzienniki wygenerowane przez aplikację, działające na platformie Azure.

Informacje o sposobie tworzenia aplikacji dzienników w zadań Webjob, zobacz [sposób pracy z usługą Azure queue storage przy użyciu zestawu SDK usługi WebJobs — jak zapisywanie dzienników](https://github.com/Azure/azure-webjobs-sdk/wiki). Poniższe instrukcje do wyświetlania dzienników i kontrolowania, jak są one przechowywane na platformie Azure również zastosowanie do dzienników aplikacji utworzone przez zadania Webjob.

### <a name="add-tracing-statements-to-the-application"></a>Dodawanie instrukcji śledzenia do aplikacji
1. Otwórz *Controllers\HomeController.cs*i Zastąp `Index`, `About`, i `Contact` metody przy użyciu następującego kodu, aby można było dodać `Trace` instrukcji i `using` poufności informacji `System.Diagnostics`:

```c#
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

1. Dodaj `using System.Diagnostics;` instrukcji na górze pliku.

### <a name="view-the-tracing-output-locally"></a>Wyświetl dane wyjściowe śledzenia lokalnie
1. Naciśnij klawisz F5, aby uruchomić aplikację w trybie debugowania.

    Odbiornik śledzenia domyślnego zapisuje wszystkie dane wyjściowe śledzenia **dane wyjściowe** okna oraz inne dane wyjściowe debugowania. Na poniższej ilustracji przedstawiono dane wyjściowe z instrukcji śledzenia, które zostały dodane do `Index` metody.

    ![Śledzenie w oknie debugowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Poniższe kroki pokazują jak wyświetlać wyniki śledzenia na stronie sieci web bez kompilacji w trybie debugowania.
2. Otwórz plik Web.config (znajdujący się w folderze projektu) i Dodaj `<system.diagnostics>` element na końcu pliku, bezpośrednio przed zamykającym `</configuration>` elementu:

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

`WebPageTraceListener` Umożliwiają wyświetlanie dane wyjściowe śledzenia, przechodząc do `/trace.axd`.
1. Dodaj <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">element śledzenia</a> w obszarze `<system.web>` w pliku Web.config, takim jak poniższy:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

1. Naciśnij klawisze CTRL+F5, aby uruchomić aplikację.
1. Na pasku adresu w oknie przeglądarki Dodaj *trace.axd* do adresu URL, a następnie naciśnij klawisz Enter (adres URL jest podobny do `http://localhost:53370/trace.axd`).
1. Na **śledzenie aplikacji** kliknij **Wyświetl szczegóły** w pierwszym wierszu (a nie wiersz BrowserLink).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **Szczegóły żądania** zostanie wyświetlona strona, a następnie w **informacje o śledzeniu** zostaną wyświetlone dane wyjściowe z instrukcji śledzenia, które zostały dodane do sekcji `Index` metody.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Domyślnie `trace.axd` jest dostępna tylko lokalnie. Jeśli chcesz udostępnić ze zdalnej aplikacji, można dodać `localOnly="false"` do `trace` element *Web.config* pliku, jak pokazano w poniższym przykładzie:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Jednak włączenie `trace.axd` w środowisku produkcyjnym aplikacji jest niezalecane ze względów bezpieczeństwa. W poniższych sekcjach zobaczysz łatwiejszy sposób odczytywania dzienników śledzenia w aplikacji usługi App Service.

### <a name="view-the-tracing-output-in-azure"></a>Wyświetl dane wyjściowe śledzenia na platformie Azure
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt sieci web i kliknij przycisk **Publikuj**.
2. W **publikowanie w sieci Web** okno dialogowe, kliknij przycisk **Publikuj**.

    Po programu Visual Studio publikuje aktualizację, zostanie otwarte okno przeglądarki do strony głównej (zakładając, że nie można wyczyścić **docelowy adres URL** na **połączenia** karty).
3. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy aplikację i wybierz **Wyświetl dzienniki przesyłania strumieniowego**.

    ![Wyświetl dzienniki przesyłania strumieniowego, w menu kontekstowym](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **Dane wyjściowe** oknie wyświetlane są połączone z usługą przesyłania strumieniowego dzienników i dodaje co minutę, który przechodzi bez dziennika, aby wyświetlić wiersz powiadomienia.

    ![Wyświetl dzienniki przesyłania strumieniowego, w menu kontekstowym](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. W oknie przeglądarki, który pokazuje strony głównej aplikacji, kliknij przycisk **skontaktuj się z pomocą**.

    W ciągu kilku sekund, dane wyjściowe z poziomu błędów z dodanym do śledzenia `Contact` metoda pojawia się w **dane wyjściowe** okna.

    ![Błąd śledzenia w oknie danych wyjściowych](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Programu Visual Studio są wyświetlane tylko śledzenie poziomu błędów, ponieważ jest to domyślne ustawienie po włączeniu dziennika usługi monitorowania. Podczas tworzenia nowej aplikacji usługi App Service całego rejestrowania jest domyślnie wyłączony, jak przedstawiono wcześniej otwarty na stronie ustawień:

    ![Rejestrowanie aplikacji](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Jednakże, gdy wybrano **Wyświetl dzienniki przesyłania strumieniowego**, Visual Studio automatycznie zmieniła **Logging(File System) aplikacji** do **błąd**, co oznacza, że błąd poziom dzienniki Pobierz zgłaszane. Aby wyświetlić wszystkie dzienniki śledzenia, można zmienić to ustawienie, aby **pełne**. Po wybraniu jej poziom ważności jest niższa niż błąd, wszystkie dzienniki dla wyższe poziomy ważności są również zgłaszane. Dlatego po wybraniu pełne, widoczny również informacje, ostrzeżenie i dzienniki błędów.  

5. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy aplikację, a następnie kliknij przycisk **ustawienia widoku** jak wcześniej.
6. Zmiana **rejestrowanie aplikacji (System plików)** do **pełne**, a następnie kliknij przycisk **Zapisz**.

    ![Ustawienie poziomu śledzenia na pełne](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. W oknie przeglądarki, która jest teraz wyświetlane Twoje **skontaktuj się z** kliknij **Home**, następnie kliknij przycisk **o**, a następnie kliknij przycisk **skontaktuj się z pomocą**.

    W ciągu kilku sekund **dane wyjściowe** okno pokazuje wszystkie dane wyjściowe śledzenia.

    ![Dane wyjściowe śledzenia pełne](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    W tej sekcji włączyć i wyłączyć rejestrowanie za pomocą ustawień aplikacji. Można także włączać i wyłączać detektorów śledzenia przez zmodyfikowanie pliku Web.config. Jednakże modyfikując plik Web.config powoduje, że domena aplikacji odzyskać, gdy włączenie rejestrowania za pośrednictwem konfiguracji aplikacji nie robi. Jeśli ten problem zajmuje dużo czasu, aby odtworzyć, lub występuje sporadycznie, odtwarzania domen aplikacji może być "Napraw" i wymusić poczekaj, aż pojawia się ponownie. Włączanie diagnostyki na platformie Azure można uruchamiać, przechwytywanie informacji o błędach od razu bez odtwarzania domen aplikacji.

### <a name="output-window-features"></a>Funkcje okna danych wyjściowych
**Dzienników platformy Azure Microsoft** karcie **dane wyjściowe** okno ma kilka przycisków i pola tekstowego:

![Dzienniki karty przyciski](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Te należy wykonać następujące funkcje:

* Wyczyść **dane wyjściowe** okna.
* Włącz lub Wyłącz zawijanie wyrazów.
* Uruchom lub Zatrzymaj monitorowanie dzienników.
* Określ, który loguje się do monitorowania.
* Pobierz dzienniki.
* Filtruj dzienniki na podstawie wyszukiwany ciąg lub wyrażenie regularne.
* Zamknij **dane wyjściowe** okna.

Jeśli wprowadzasz wyszukiwany ciąg lub wyrażenie regularne, Visual Studio filtruje informacje rejestrowania po stronie klienta. Oznacza to, że kryteria można wprowadzić po dzienniki są wyświetlane w **dane wyjściowe** okna i możesz zmienić kryteria filtrowania bez konieczności ponownego generowania dzienników.

## <a name="webserverlogs"></a>Wyświetl dzienniki serwera sieci web
Dzienniki serwera sieci Web rejestrować wszystkie aktywności protokołu HTTP dla aplikacji. Aby można było wyświetlić je w **dane wyjściowe** okna, należy włączyć je do aplikacji i przekaż programowi Visual Studio, chcesz monitorować je.

1. W **konfigurację aplikacji sieci Web Azure** karty, który został otwarty z **Eksploratora serwera**, zmień Web rejestrowanie serwera **na**, a następnie kliknij przycisk **Zapisz**.

    ![Włącz rejestrowanie serwera sieci web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. W **dane wyjściowe** okna, kliknij przycisk **Określ, która tworzy dzienniki Microsoft Azure, aby monitorować** przycisku.

    ![Określ, które dzienników platformy Azure do monitorowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. W **opcje rejestrowania platformy Microsoft** okno dialogowe, wybierz opcję **dzienników serwera w sieci Web**, a następnie kliknij przycisk **OK**.

    ![Monitoruj dzienniki serwera sieci web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. W oknie przeglądarki, które przedstawiono aplikację, kliknij przycisk **Home**, następnie kliknij przycisk **o**, a następnie kliknij przycisk **skontaktuj się z pomocą**.

    Dzienniki aplikacji zwykle występować jako pierwszy, a następnie dzienniki serwera sieci web. Może być konieczne poczekaj przez pewien czas dzienniki będą widoczne.

    ![Dzienniki serwera sieci Web w oknie danych wyjściowych](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Domyślnie po pierwszym włączeniu dzienników serwera internetowego przy użyciu programu Visual Studio, Azure zapisuje dzienniki w systemie plików. Jako alternatywę można użyć witryny Azure portal do określania tego serwera sieci web, które mają być zapisywane dzienniki kontenera obiektów blob na koncie magazynu.

Jeśli na serwerze sieci web logowania do konta usługi Azure storage za pomocą portalu, a następnie wyłącz rejestrowanie w programie Visual Studio, po ponownym włączeniu rejestrowania w programie Visual Studio ustawienia konta magazynu zostaną przywrócone.

## <a name="detailederrorlogs"></a>Wyświetlanie dzienników komunikatów szczegółowy komunikat o błędzie
Dzienniki szczegółowy komunikat o błędzie zawierają dodatkowe informacje o żądaniach HTTP, których wynikiem kody odpowiedzi błąd (400 lub nowszy). Aby można było wyświetlić je w **dane wyjściowe** okna, należy włączyć je do aplikacji i przekaż programowi Visual Studio, chcesz monitorować je.

1. W **konfigurację aplikacji sieci Web Azure** karty, który został otwarty z **Eksploratora serwera**, zmień **szczegółowe komunikaty o błędach** do **na**, a następnie Kliknij przycisk **Zapisz**.

    ![Włącz szczegółowe komunikaty o błędach](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. W **dane wyjściowe** okna, kliknij przycisk **Określ, która tworzy dzienniki Microsoft Azure, aby monitorować** przycisku.

3. W **opcje rejestrowania platformy Microsoft** okno dialogowe, kliknij przycisk **wszystkie dzienniki**, a następnie kliknij przycisk **OK**.

    ![Monitorowanie wszystkich dzienników](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na pasku adresu w oknie przeglądarki, należy dodać nadmiarowy znak do adresu URL, aby spowodować błąd 404 (na przykład `http://localhost:53370/Home/Contactx`), i naciśnij klawisz Enter.

    Po kilku sekundach dziennik szczegółowy komunikat o błędzie jest wyświetlany w programie Visual Studio **dane wyjściowe** okna.

    ![Dziennik szczegółowy komunikat o błędzie — okno danych wyjściowych](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Kontrolowanie i kliknij link, aby wyświetlić dane wyjściowe dziennika sformatowane w przeglądarce:

    ![Dziennik szczegółowy komunikat o błędzie — okno przeglądarki](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Pobierz dzienniki systemu plików
Wszystkie dzienniki, które można monitorować w **dane wyjściowe** okna można również pobrać jako *zip* pliku.

1. W **dane wyjściowe** okna, kliknij przycisk **Pobierz dzienniki przesyłania strumieniowego**.

    ![Dzienniki karty przyciski](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Eksplorator plików otwiera swoje *pliki do pobrania* folder z pobranego pliku zaznaczone.

    ![Pobrany plik](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Wyodrębnij *zip* plik i wyświetlić następującą strukturę folderów:

    ![Pobrany plik](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Dzienniki śledzenia aplikacji znajdują się w *.txt* pliki *LogFiles\Application* folderu.
   * Dzienniki serwera sieci Web znajdują się w *.log* pliki *LogFiles\http\RawLogs* folderu. Można użyć narzędzia, takie jak [analizator dzienników](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) do wyświetlania i modyfikowania tych plików.
   * Dzienniki komunikat szczegółowy komunikat o błędzie znajdują się w *.html* pliki *LogFiles\DetailedErrors* folderu.

     ( *Wdrożeń* folder jest tworzonych przez kontroli źródła, publikowanie; nie ma niczego związanych z publikowaniem w programie Visual Studio. *Git* folder jest dający zapisy związane z kontrolą źródła publikowania i plików dziennika usługi transmisji strumieniowej.)  

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
## <a name="failedrequestlogs"></a>Wyświetlanie dzienników śledzenia nieudanych żądań
Dzienniki śledzenia nieudanych żądań są przydatne, gdy trzeba poznać szczegółowe informacje o jak usług IIS obsługuje żądania HTTP w scenariuszach takich jak problemy uwierzytelniania lub ponownego zapisywania adresów URL.

Aplikacje usługi App Service używają taką samą funkcjonalność śledzenia niepomyślnych żądań, które było dostępne z usługami IIS 7.0 i nowszych wersjach. Nie masz dostępu do ustawień usług IIS, skonfigurowanych, które błędy rejestrowanymi jednak. Po włączeniu funkcji śledzenia żądań zakończonych niepowodzeniem, wszystkie błędy są przechwytywane.

Można włączyć śledzenia nieudanych żądań przy użyciu programu Visual Studio, ale nie można go wyświetlić w programie Visual Studio. Te dzienniki są plikami XML. Usługi przesyłania strumieniowego dzienników monitoruje tylko pliki, które zostaną uznane za odczytać w trybie zwykłego tekstu: *.txt*, *.html*, i *.log* plików.

Dzienniki śledzenia niepomyślnych żądań można wyświetlić w przeglądarce bezpośrednio za pośrednictwem protokołu FTP lub lokalnie po nim za pomocą narzędzia FTP, aby je pobrać na komputer lokalny. W tej sekcji można będzie je wyświetlić w przeglądarce bezpośrednio.

1. W **konfiguracji** karcie **aplikacji sieci Web platformy Azure** okna, który został otwarty z **Eksploratora serwera**, zmień **Śledzenie żądań zakończonych niepowodzeniem** do **Na**, a następnie kliknij przycisk **Zapisz**.

    ![Włącz śledzenie nieudanych żądań](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na pasku adresu w oknie przeglądarki, w którym przedstawiono aplikację dodać nadmiarowy znak do adresu URL i naciśnij klawisz Enter, aby spowodować błąd 404.

    Powoduje to, że dziennik śledzenia niepomyślnych żądań, ma zostać utworzony, a poniższe kroki pokazują, jak wyświetlić lub pobrać dziennik.

3. W programie Visual Studio w **konfiguracji** karcie **aplikacji sieci Web platformy Azure** okna, kliknij przycisk **Otwórz w portalu zarządzania**.

4. W [witryny Azure portal](https://portal.azure.com) **ustawienia** stronie dla aplikacji, kliknij przycisk **poświadczenia wdrożenia**, a następnie wprowadź nową nazwę użytkownika i hasło.

    ![Nową nazwę użytkownika protokołu FTP i hasło](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Podczas logowania, należy użyć pełnej nazwy z prefiksem do niego nazwę aplikacji. Na przykład jeśli witryna jest "mój_przykład" Wprowadź "myid" jako nazwy użytkownika, możesz Zaloguj się jako "myexample\myid".
    >

5. W nowym oknie przeglądarki przejdź do adresu URL, który jest wyświetlany w obszarze **nazwy hosta FTP** lub **nazwa hosta FTPS** w **Przegląd** strony aplikacji.

6. Zaloguj się przy użyciu poświadczeń protokołu FTP, które zostały utworzone wcześniej (z uwzględnieniem aplikacji prefiks nazwy dla nazwy użytkownika).

    Przeglądarka zawiera folder główny aplikacji.

7. Otwórz *LogFiles* folderu.

    ![Otwórz folder LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Otwórz folder, który nosi nazwę W3SVC plus wartość liczbową.

    ![Otwórz folder W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Folder zawiera pliki XML pod kątem błędów, które zostały zarejestrowane po włączeniu funkcji śledzenia żądań zakończonych niepowodzeniem i pliku XSL, używanego przez przeglądarkę do formatu XML.

    ![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Kliknij plik XML dla żądania nie powiodło się, które mają być wyświetlane informacje śledzenia dla.

    Poniższa ilustracja przedstawia część informacji śledzenia błędu próbki.

    ![Śledzenie nieudanych żądań w przeglądarce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Następne kroki
Wiesz, jak Visual Studio można łatwo wyświetlić dzienniki utworzone przez aplikację usługi App Service. Poniższe sekcje zawierają linki do większej ilości zasobów na tematy pokrewne:

* Rozwiązywanie problemów w usłudze App Service
* Debugowanie w Visual Studio
* Zdalne debugowanie na platformie Azure
* Śledzenie w aplikacjach ASP.NET
* Analizowanie dzienników serwera internetowego
* Analizowanie dzienników śledzenia nieudanych żądań
* Debugowanie usług w chmurze

### <a name="app-service-troubleshooting"></a>Rozwiązywanie problemów w usłudze App Service
Aby uzyskać więcej informacji na temat rozwiązywania problemów z aplikacjami w usłudze Azure App Service zobacz następujące zasoby:

* [Jak monitorować aplikacje](web-sites-monitor.md)
* [Badanie przecieków pamięci w usłudze Azure App Service w programie Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Wpis w blogu ALM firmy Microsoft o funkcjach programu Visual Studio do analizowania problemów pamięci zarządzanej.
* [Usługa Azure narzędzia online usługi App Service, które należy wiedzieć o](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Wpis na blogu autorstwa Amitowi firmy Apple.

Aby uzyskać pomoc dotyczącą określonego zapytania dotyczące rozwiązywania problemów należy uruchomić wątku w jednym z następujących forów:

* [Forum platformy Azure w witrynie programu ASP.NET](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum platformy Azure w witrynie MSDN](https://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Debugowanie w Visual Studio
Aby uzyskać więcej informacji o sposobie używania trybu debugowania w programie Visual Studio, zobacz [debugowania w programie Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) i [debugowania porady dotyczące programu Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Zdalne debugowanie na platformie Azure
Aby uzyskać więcej informacji na temat debugowania zdalnego dla aplikacji usługi App Service i zadania Webjob zobacz następujące zasoby:

* [Wprowadzenie do zdalnego debugowania usługi Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Wprowadzenie do zdalnego debugowania usługi Azure App Service część 2 — wewnątrz zdalnego debugowania](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Wprowadzenie do zdalnego debugowania w usłudze Azure App Service, część 3 — wiele wystąpień środowiska i GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Zadania Webjob debugowania (wideo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Jeśli aplikacja korzysta z interfejsu API sieci Web platformy Azure lub usługi mobilnej zaplecza, i chcesz debugować go, zobacz [debugowania zaplecza platformy .NET w programie Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Śledzenie w aplikacjach ASP.NET
Brak dostępnych nie dokładne i aktualne wprowadzenia do śledzenia ASP.NET w Internecie. Najlepiej, które można wykonać jest wprowadzenie starego materiałów wprowadzające napisane z myślą o formularzy sieci Web ponieważ MVC nie został jeszcze istnieje w celu uzupełnienia, za pomocą nowszej blogu publikuje koncentrujących się na konkretnych problemów. Pewne dobre miejsca, aby rozpocząć są następujące zasoby:

* [Monitorowanie i Telemetria (tworzenie rzeczywistych aplikacji w chmurze dzięki platformie Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Książka elektroniczna rozdziału z zaleceniami dotyczącymi śledzenie w aplikacjach w chmurze platformy Azure.
* [Śledzenie na platformie ASP.NET](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Stary, ale nadal dobry zasobem dla wstęp do tematu.
* [Obiekty nasłuchujące śledzenia](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informacje o detektorów śledzenia, ale nie wspomina o identyfikatorach [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Wskazówki: Integracja śledzenia ASP.NET z włączonym śledzeniem System.Diagnostics](/previous-versions/b0ectfxd(v=vs.140))<br/>
  W tym artykule jest także stare, ale zawiera pewne dodatkowe informacje, które wprowadzające artykuł nie obejmuje.
* [Śledzenie widoki ASP.NET MVC Razor](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Oprócz śledzenia w widokami Razor, wpis wyjaśniono również, jak utworzyć filtr błędu w celu rejestrowania wszystkich nieobsługiwanych wyjątków w aplikacji MVC. Aby uzyskać informacje o sposobie rejestrowania wszystkich nieobsługiwanych wyjątków w aplikacji formularzy sieci Web, zobacz przykład plik Global.asax w [kompletny przykład obsługi błędu](/previous-versions/bb397417(v=vs.140)) w witrynie MSDN. W MVC lub Web Forms Aby rejestrować pewne wyjątki, ale pozwól domyślna struktura obsługi zaczęły obowiązywać dla nich można było wyłapać i zgłoś ponownie jak w poniższym przykładzie:

``` c#
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

* [Rejestrowanie z wiersza polecenia platformy Azure (oraz możliwość wypróbowania!) danych przesyłania strumieniowego śledzenia diagnostyki](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Jak skorzystać z jakich tego samouczka za pomocą wiersza polecenia pokazuje, jak to zrobić w programie Visual Studio. [Możliwość wypróbowania](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) to narzędzie do debugowania aplikacji ASP.NET.
* [Przy użyciu aplikacji sieci Web, rejestrowanie i Diagnostyka — z David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) i [przesyłanie strumieniowe dzienników z aplikacji sieci Web — David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Filmy wideo przez Scotta Hanselmana i David Ebbo.

Dla rejestrowania błędów zamiast pisania własnego kodu śledzenia ma używać struktury rejestrowania typu open source, takich jak [ELMAH](https://nuget.org/packages/elmah/). Aby uzyskać więcej informacji, zobacz [wpisy na blogu Scott Hanselman o ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Ponadto, nie musisz za pomocą programu ASP.NET lub `System.Diagnostics` śledzenie, aby pobrać przesyłania strumieniowego dzienników z platformy Azure. Aplikacja usługi App Service, strumieniowego przesyłania dzienników strumieni dowolne *.txt*, *.html*, lub *.log* pliku znalezionego w *LogFiles* folderu. W związku z tym można utworzyć własny system rejestrowania, który zapisuje w systemie plików, aplikacji i automatycznie przesyłane strumieniowo i pobrać plik. Wystarczy zrobić to zapisu aplikacji kod, który tworzy pliki w *d:\home\logfiles* folderu.

### <a name="analyzing-web-server-logs"></a>Analizowanie dzienników serwera internetowego
Aby uzyskać więcej informacji na temat analizowania dzienników serwera internetowego Zobacz następujące zasoby:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Narzędzie do wyświetlania danych w dzienniki serwera sieci web ( *.log* plików).
* [Rozwiązywanie problemów z wydajnością usług IIS lub błędy aplikacji przy użyciu LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Wprowadzenie do narzędzia Analizator dzienników, które służy do analizowania dzienników serwera internetowego.
* [Wpisy na blogu, Robert McMurray przy użyciu LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Kod stanu HTTP w usługach IIS 7.0, IIS 7.5 i IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analizowanie dzienników śledzenia nieudanych żądań
Witryny sieci Web Microsoft TechNet zawiera [za pomocą śledzenia nieudanych żądań](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) sekcji, które mogą być przydatne do zrozumienia sposobu używania tych dzienników. Jednak ta dokumentacja koncentruje się głównie na konfigurowaniu śledzenia niepomyślnych żądań w usługach IIS, czego nie możesz zrobić w usłudze Azure App Service.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
