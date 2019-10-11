---
title: Unrealość projektu i integracja z Wwise
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano integrację z wtyczkami Unreal i Wwise w projekcie.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243063"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Unrealość projektu i integracja z Wwise
W tym artykule opisano, jak zintegrować pakiet plug-in akustyczny projektu z istniejącym projektem gier Unreal i Wwise.

Wymagania dotyczące oprogramowania:
* [Aparat Unreal](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [Wtyczka Wwise dla Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Jeśli używasz bezpośredniej integracji zestawu SDK Wwise, a nie wtyczki Wwise Unreal, zapoznaj się z wtyczką nadaną w projekcie, a następnie dostosuj wywołania interfejsu API Wwise.

Aby użyć akustycznych projektów z silnikiem audio innym niż Wwise, wprowadź żądanie usprawnienia na [forum dyskusyjnym projektora](https://github.com/microsoft/ProjectAcoustics/issues). Możesz użyć wtyczki Unreali projektu, aby badać dane akustyczne i wykonywać wywołania interfejsu API do aparatu.

## <a name="download-project-acoustics"></a>Pobierz akustyczne projektu
Pobierz [pakiet plug-in Unreal i Wwise](https://www.microsoft.com/download/details.aspx?id=58090) , jeśli jeszcze tego nie zrobiono.

Wtyczka aparatu Unreal i wtyczka miksera Wwise są zawarte w pakiecie. Wtyczka Unreal zapewnia integrację edytora i środowiska uruchomieniowego. Podczas rozgrywkęi, projekt akustyczny Unreal oblicza parametry, takie jak zamknięcia dla każdego obiektu gry dla każdej ramki. Te parametry są tłumaczone na wywołania interfejsu API Wwise.

## <a name="integration-steps"></a>Kroki integracji

Wykonaj następujące kroki, aby zainstalować pakiet i wdrożyć go w grze.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Instalowanie wtyczki miksera dla projektu
1. Otwórz Wwise. Na karcie **wtyczki** w obszarze **Instaluj nowe wtyczki**wybierz pozycję **Dodaj z katalogu**.

    ![Instalowanie wtyczki w programie Wwise](media/wwise-install-new-plugin.png)

1. Wybierz katalog *AcousticsWwisePlugin\ProjectAcoustics* , który znajduje się w pakiecie pobierania. Zawiera pakiet wtyczki miksera Wwise.

   Program Wwise zainstaluje wtyczkę. W programie Wwise są wyświetlane na liście zainstalowanych wtyczek.  
![Wwise zainstalowano listę wtyczek po instalacji akustycznej projektu](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Wdróż Wwise w grze
Wdróż ponownie Wwise w grze, nawet jeśli już masz integrację Wwise. Ten krok integruje wtyczkę "Wwise" w projekcie.

   > [!NOTE]
   > **Wtyczka aparatu:** Jeśli zainstalowano Wwise jako wtyczkę gier w projekcie Unreal C++ , Pomiń ten krok. Jeśli jest zainstalowana, a nie jako wtyczka aparatu, na przykład ponieważ projekt Unreal jest tylko planem, wdrożenie Wwise z dodatkiem plug-in miksera jest bardziej skomplikowane. Utwórz fikcyjny pusty projekt C++ Unreal. Zamknij Edytor Unreal, jeśli zostanie otwarty, i postępuj zgodnie z pozostałą procedurą, aby wdrożyć Wwise w projekcie fikcyjnym. Następnie skopiuj wdrożoną wtyczkę Wwise.
 
1. W obszarze uruchamiania Wwise wybierz kartę **aparat Unreal** . Wybierz menu "Hamburger" (ikona) obok pozycji **ostatnie projekty aparatu Unreal** , a następnie wybierz pozycję **Przeglądaj w poszukiwaniu projektu**. Otwórz plik Project Unreal projektu gry *.*

    ![Karta Wwise Uruchom Unreal](media/wwise-unreal-tab.png)

1. Wybierz pozycję **Zintegruj Wwise w projekcie** lub **Modyfikuj Wwise w projekcie**. Ten krok integruje pliki binarne Wwise do projektu, w tym wtyczkę miksera dla projektu.

   > [!NOTE]
   > **Wtyczka aparatu:** Jeśli używasz Wwise jako wtyczki aparatu i utworzysz projekt fikcyjny zgodnie z wcześniejszym opisem, skopiuj folder, który Wwise wdrożony: *[DummyUProject] \Plugins\Wwise*. Wklej ją nad *[UESource] \Engine\Plugins\Wwise*. *[DummyUProject]* jest pustą ścieżką projektu Unreal C++ , a *[UESource]* to miejsce, w którym są zainstalowane źródła aparatu Unreal. Po skopiowaniu folderu można usunąć fikcyjny projekt.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Dodawanie wtyczki Unreal w projekcie do gry
 
1. Skopiuj folder *Unreal\ProjectAcoustics* w pakiecie wtyczki. Utwórz nowy folder *[UProjectDir] \Plugins\ProjectAcoustics*, gdzie *[UProjectDir]* jest folderem projektu gry, który zawiera plik *. uproject* .

   > [!NOTE]
   > **Wtyczka aparatu**: Jeśli używasz Wwise jako wtyczki aparatu, należy również użyć funkcji akustycznych projektu jako wtyczki aparatu Unreal. Zamiast pocytowanego wcześniej katalogu docelowego Użyj polecenia *[UESource] \Engine\Plugins\ProjectAcoustics*.

1. Upewnij się, że został wyświetlony folder *Wwise* obok folderu *ProjectAcoustics* . Zawiera on wtyczkę Wwise oraz pliki binarne dla wtyczki miksera wdrożonej wcześniej.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozszerzona funkcjonalność wtyczki Wwise Unreal
Wtyczka unreala akustyczna projektu wymaga dodatkowego zachowania uwidocznionego na podstawie interfejsu API dodatku plug-in Wwise Unreal zgodnie z [tymi wskazówkami](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Dodaliśmy plik wsadowy służący do automatyzowania procedury poprawek.

* W *Plugins\ProjectAcoustics\Resources*, uruchom *PatchWwise. bat*. Poniższy przykład obrazu używa naszego przykładowego projektu AcousticsGame.

    ![Okno Eksploratora Windows ze skryptem do poprawek Wwise wyróżnione](media/patch-wwise-script.png)

* Jeśli nie masz zainstalowanego zestawu SDK programu DirectX: w zależności od używanej wersji programu Wwise może być konieczne komentowanie wiersza zawierającego `DXSDK_DIR` w *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Edytor kodu przedstawiający komentarz "DXSDK"](media/directx-sdk-comment.png)

* Jeśli kompilujesz za pomocą programu Visual Studio 2019: Aby obejść błąd łączenia z Wwise, ręcznie zmień wartość domyślną `VSVersion` w *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Edytor kodu przedstawiający "VSVersion" został zmieniony na "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Kompiluj grę i sprawdź, czy język Python jest włączony

1. Kompiluj grę i upewnij się, że kompiluje się poprawnie. Jeśli nie kompiluje się, przed kontynuowaniem sprawdź poprzednie kroki.

1. Otwórz projekt w edytorze Unreal.

    > [!NOTE]
    > **Wtyczka aparatu:** Jeśli używasz ProjectAcoustics jako wtyczki aparatu, upewnij się również, że jest włączona w obszarze "wbudowane" wtyczki.

    Powinien pojawić się nowy tryb wskazujący, że akustyczne projektu zostały zintegrowane.

    ![Tryb akustyczny jest pełny w Unreal](media/acoustics-mode-full.png)

1. Upewnij się, że wtyczka języka Python dla Unreal jest włączona, aby integracja z edytorem działa prawidłowo.

    ![Rozszerzenia języka Python w edytorze Unreal są włączone](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Konfigurowanie projektu Wwise do korzystania z akustycznych projektu

Przykładowy projekt Wwise jest dołączany do pobierania próbek. Zalecamy zapoznanie się z tymi instrukcjami. Zrzuty ekranu w dalszej części tego artykułu pochodzą z tego projektu.

#### <a name="bus-setup"></a>Konfiguracja magistrali
Wtyczka Unreal akustyczny projektu będzie szukać skojarzonej wtyczki miksera na magistrali o dokładnej nazwie `Project Acoustics Bus`. Utwórz nową magistralę audio o tej samej nazwie. Wtyczka miksera może współpracować w różnych konfiguracjach. Jednak na razie Załóżmy, że będzie on używany tylko do przetwarzania Reverb. Ta magistrala będzie obsługiwać mieszany sygnał Reverb dla wszystkich źródeł, które używają akustycznych. Może on mieszać wszystkie struktury miksowania magistrali. Przykład jest pokazywany w tym miejscu z przykładowego projektu Wwise, który znajduje się w przykładowym pobieraniu.

![Magistrale Wwise przedstawiające autobusy akustyczne projektu](media/acoustics-bus.png)

1. Ustaw konfigurację kanału na magistrali na *1,0*, *2,0*, *4,0*, *5,1*lub *7,1*. Inne ustawienie spowoduje Brak danych wyjściowych na magistrali.

    ![Opcje konfiguracji kanału dla magistrali akustycznej projektu](media/acoustics-bus-channel-config.png)

1. Przejdź do szczegółów magistrali akustycznej projektu i upewnij się, że można wyświetlić kartę **wtyczka miksera** .

    ![Wwise z kartą wtyczki miksera z włączoną magistralą akustyczną projektu](media/mixer-tab-enable.png)

1. Przejdź do karty **wtyczka miksera** i Dodaj wtyczkę miksera w projekcie do magistrali.

    ![Diagram przedstawiający sposób dodawania wtyczki "mieszanie" projektu do magistrali Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Konfiguracja hierarchii dla miksera
W celu uzyskania najlepszej wydajności, akustyczne projektu stosuje przetwarzanie sygnałów cyfrowych audio do wszystkich źródeł jednocześnie. Dlatego wtyczka musi działać jako wtyczka miksera. Wwise wymaga, aby wtyczki miksera były przechowywane na magistrali wyjściowej, mimo że magistrala wyjściowa zwykle przenosi suchy sygnał wyjściowy. Akustyczny projektowe wymaga, aby suchy sygnał był kierowany przez magistrale AUX, podczas gdy na `Project Acoustics Bus` jest wykonywany sygnał mokry. Następujący proces obsługuje stopniowe Migrowanie do tego przepływu sygnałów.

Załóżmy, że masz istniejący projekt z hierarchią miksera aktora, która zawiera *takie rozwiązanie wprowadziły*, *broń*i inne na najwyższego poziomu. Każdy z nich ma odpowiednią magistralę wyjściową dla swojego suchego miksu. Załóżmy, że chcesz przeprowadzić migrację takie rozwiązanie wprowadziły, aby korzystać z nich. Najpierw Utwórz odpowiednią magistralę pomocniczą, aby przenieść suchy Submix, który jest elementem podrzędnym magistrali wyjściowej takie rozwiązanie wprowadziły. Na przykład na poniższym obrazie został użyty prefiks "suchy", ale dokładna nazwa nie jest ważna. Wszelkie liczniki lub efekty, które były dostępne na magistrali takie rozwiązanie wprowadziły, będą nadal działać tak jak wcześniej.

![Zalecana konfiguracja Wwise w postaci suchej](media/wwise-dry-mix-setup.png)

Następnie zmodyfikuj strukturę wyjściową magistrali dla miksera aktora takie rozwiązanie wprowadziły w następujący sposób, z *magistralą akustyczną projektu* ustawioną jako **magistrala wyjściowa**i *Dry_Footsteps* ustawioną jako magistrala AUX zdefiniowana przez użytkownika.

![Zalecana konfiguracja magistrali miksera aktora Wwise](media/actor-mixer-bus-settings.png)

Teraz wszystkie takie rozwiązanie wprowadziły są traktowane jako akustyczne i wyjściowe Reverb na magistrali akustycznej projektu. Suchy sygnał jest kierowany przez Dry_Footsteps i przewidziany w zwykły sposób.

Akustyczne projektu dotyczą tylko dźwięków, które mają lokalizację 3W na świecie. Zgodnie z [dokumentacją Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)należy ustawić właściwości pozycjonowania, jak pokazano. Ustawienie **Spatialization 3W** może mieć wartość *Position* lub *Position + Orientacja* odpowiednio do wymagań.

![Zalecane ustawienia aktora Wwise](media/wwise-positioning.png)

Nie można ustawić **magistrali wyjściowej** na inną magistralę, która miesza się z *magistralą akustyczną w projekcie*. Wwise nakłada to wymaganie na wtyczki miksera.

Jeśli chcesz, aby element podrzędny w hierarchii takie rozwiązanie wprowadziły aktora nie korzystał z hałasu, możesz użyć opcji "Zastąp element nadrzędny", aby go wycofać.

W przypadku korzystania z funkcji, zdefiniowanych przez użytkownika lub do Reverb na dowolnym z nich, można je wyłączyć na tym mikserze aktora, aby uniknąć dwukrotnego zastosowania Reverb.

#### <a name="spatialization"></a>Spatialization
Wtyczka Wwise miksera w projekcie jest stosowana domyślnie Convolution Reverb, pozostawiając Wwise do przesuwania spatialization. W przypadku korzystania z ustawień akustycznych projektu w tej domyślnej konfiguracji tylko Reverb można użyć dowolnej konfiguracji kanału i metody spatialization na wysuszonym połączeniu. Dzięki temu można mieszać i dopasowywać niemal dowolnych spatializer za pomocą reverbów projektu. Dostępne opcje obejmują [Ambisonics Binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) i [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Protokoły akustyczne projektu zawierają opcjonalny spatializer, który obsługuje zarówno renderowanie, jak i panoramowanie HRTF o wysokiej rozdzielczości oparte na obiektach. Zaznacz pole wyboru **Wykonaj Spatialization** w ustawieniach wtyczki miksera i wybierz opcję między *HRTF* lub *panoramowanie*. Ponadto należy wyłączyć funkcję, która zdefiniowana przez użytkownika jest wysyłana do wszystkich suchych magistrali, aby uniknąć spatializing dwukrotnie przez wtyczkę miksera i Wwise. Nie można zmienić trybu spatialization w czasie rzeczywistym, ponieważ wymaga to ponownego wygenerowania danych z banku. Uruchom ponownie program Unreal, a następnie wykonaj ponowne generowanie soundbanks przed wybraniem opcji Odtwórz, aby zintegrować zmiany w konfiguracji wtyczki miksera, takie jak pole wyboru **Wykonaj Spatialization** .

![Ustawienia Spatialization wtyczki Wwise mieszarki](media/mixer-spatial-settings.png)

Niestety inne wtyczki spatializer oparte na obiektach nie są obecnie obsługiwane. Są one implementowane jako wtyczki miksera, a Wwise nie zezwala na przypisanie wielu wtyczek miksera do pojedynczego miksera aktora.  

### <a name="audio-setup-in-unreal"></a>Konfiguracja audio w Unreal
1. Najpierw należy tworzenie poziom gry, aby utworzyć element zawartości akustycznej, który zostanie umieszczony w *Content\Acoustics*. Zapoznaj się z [samouczkiem Unreal tworzenie](unreal-baking.md). Niektóre wstępnie rozszerzania poziomy są zawarte w pakiecie przykładowym.

1. Utwórz aktora obszaru akustycznego w scenie. Można utworzyć tylko jedną z tych aktorów na poziomie, ponieważ reprezentuje ona wartości akustyczne całego poziomu.

    ![Tworzenie aktora przestrzeni dźwiękowej w edytorze Unreal](media/create-acoustics-space.png)

1. Przypisz rozszerzania akustyczny do miejsca danych akustycznych aktora obszaru. Twoja scena ma teraz akustyczne!

    ![Przydzielenie zasobów akustycznych w edytorze Unreal](media/acoustics-asset-assign.png)

1. Dodaj pustą aktora. Skonfiguruj ją w następujący sposób.

    ![Edytor Unreal pokazuje akustyczne użycie składników w pustym aktorze](media/acoustics-component-usage.png)

       
    <sup>1</sup> dodanie składnika audio akustycznego do aktora. Ten składnik dodaje funkcje akustyczne projektu do składnika audio Wwise.
        
    <sup>2</sup> pole **Odtwórz przy uruchomieniu** jest zaznaczone domyślnie. To ustawienie wyzwala skojarzone zdarzenie Wwise na poziomie uruchamiania.</li>
         
    <sup>3</sup> Użyj pola wyboru **Pokaż parametry akustyczne** , aby drukować informacje debugowania na ekranie dotyczące źródła.

    ![Panel akustyczny edytora Unreal w źródle dźwięku z włączonymi wartościami debugowania](media/debug-values.png)

    <sup>4</sup> Przypisz zdarzenie Wwise na zwykły przepływ pracy Wwise.
       
    <sup>5</sup> upewnij się, że **Funkcja use audio przestrzenny** jest wyłączona. Jeśli używasz akustycznych projektów dla określonego składnika audio, nie możesz jednocześnie używać Wwiseego aparatu audio dla hałasu.</li>
       
Wszystko jest gotowe. Poruszaj się wokół sceny i Eksploruj efekty akustyczne!

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [projekt Unreal/Wwise w samouczku](unreal-workflow.md)dotyczącym hałasu.
* Dowiedz się [, jak tworzony](unreal-baking.md) na potrzeby scen gry.
