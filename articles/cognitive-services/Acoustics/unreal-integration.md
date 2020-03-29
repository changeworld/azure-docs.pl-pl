---
title: Integracja Projektu Akustyka Nierealna i Wwise
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano integrację wtyczek Project Acoustics Unreal i Wwise z projektem.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243063"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integracja Projektu Akustyka Nierealna i Wwise
W tym artykule opisano sposób integracji pakietu wtyczki Project Acoustics z istniejącym projektem gry Unreal i Wwise.

Wymagania dotyczące oprogramowania:
* [Silnik Nierealny](https://www.unrealengine.com/) 4.20+
* [AudioKinetyczny Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Wtyczka Wwise dla Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Jeśli używasz bezpośredniej integracji Wwise SDK zamiast wtyczek Wwise Unreal, zapoznaj się z wtyczką Project Acoustics Unreal i dostosuj wywołania interfejsu API Wwise.

Aby użyć akustyki projektu z silnikiem audio innym niż Wwise, należy poprosić o ulepszenie na [forum dyskusyjnym Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues). Za pomocą wtyczki Project Acoustics Unreal można wysyłać zapytania do danych akustyki i wykonywać wywołania interfejsu API w silniku.

## <a name="download-project-acoustics"></a>Pobierz Akustykę projektu
Pobierz [pakiet wtyczek Project Acoustics Unreal i Wwise,](https://www.microsoft.com/download/details.aspx?id=58090) jeśli jeszcze tego nie zrobiłeś.

W pakiecie znajduje się wtyczka Unreal Engine i wtyczka miksera Wwise. Wtyczka Unreal zapewnia integrację edytora i środowiska uruchomieniowego. Podczas rozgrywki wtyczka Project Acoustics Unreal oblicza parametry, takie jak okluzja dla każdego obiektu gry dla każdej klatki. Parametry te są tłumaczone na wywołania interfejsu API Wwise.

## <a name="integration-steps"></a>Kroki integracji

Wykonaj następujące kroki, aby zainstalować pakiet i wdrożyć go w grze.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Zainstaluj wtyczkę miksera Project Acoustics
1. Otwórz wyrzutnię Wwise. Na karcie **Wtyczki w** obszarze **Instalowanie nowych wtyczek**wybierz pozycję **Dodaj z katalogu**.

    ![Zainstaluj wtyczkę w wyrzutni Wwise](media/wwise-install-new-plugin.png)

1. Wybierz katalog *AcousticsWwisePlugin\ProjectAcoustics* znajdujący się w pakiecie pobierania. Zawiera wtyczkę miksera Wwise.

   Wwise zainstaluje wtyczkę. Akustyka projektu powinna pojawić się na liście zainstalowanych wtyczek w Wwise.  
![Lista wtyczek zainstalowanych wwise po instalacji Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Dedeploy Wwise w swojej grze
Ponownie wsuń Wwise'a do swojej gry, nawet jeśli już zintegrowałeś Wwise.Redeploy Wwise into your game even if you've already integrated Wwise. Ten krok integruje wtyczkę Project Acoustics Wwise.

   > [!NOTE]
   > **Wtyczka silnika:** Jeśli w usłudze Wwise zainstalowano jako wtyczkę do gier w projekcie Unreal C++, pomiń ten krok. Jeśli zamiast tego jest zainstalowany jako wtyczka aparatu, na przykład, ponieważ projekt Unreal jest tylko Blueprint, wdrożenie Wwise z naszej wtyczki miksera jest bardziej skomplikowane. Utwórz fikcyjny pusty projekt Unreal C++. Zamknij edytor Unreal, jeśli zostanie otwarty i wykonaj pozostałą procedurę, aby wdrożyć Wwise w projekcie manekina. Następnie skopiuj wdrożoną wtyczkę Wwise.
 
1. W programie Wwise launcher wybierz kartę **Silnik nierealny.** Wybierz menu "hamburger" (ikona) obok **pozycji Ostatnie projekty silnika nierealnego,** a następnie wybierz pozycję **Przeglądaj w poszukiwaniu projektu**. Otwórz plik projektu Unreal gry *.project.*

    ![Karta Wwise launcher Unreal](media/wwise-unreal-tab.png)

1. Wybierz **pozycję Zintegruj w programie Wwise w programie Project** lub **Modyfikuj w programie Project**. Ten krok integruje pliki binarne Wwise z projektem, w tym wtyczkę miksera Akustyka projektu.

   > [!NOTE]
   > **Wtyczka silnika:** Jeśli używasz Wwise jako wtyczki silnika i utworzono projekt manekina, jak opisano wcześniej, skopiuj folder, który wdrożono w programie Wwise: *[DummyUProject]\Plugins\Wwise*. Wklej go nad *[UESource]\Engine\Plugins\Wwise*. *[DummyUProject]* jest pustą ścieżką projektu Unreal C++, a *[UESource]* jest miejscem, w którym są zainstalowane źródła aparatu unreal. Po skopiowaniu folderu można usunąć projekt manekina.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Dodawanie wtyczki Project Acoustics Unreal do gry
 
1. Skopiuj folder *Unreal\ProjectAcoustics* w pakiecie dodatku plug-in. Utwórz nowy folder *[UProjectDir]\Plugins\ProjectAcoustics*, gdzie *[UProjectDir]* jest folderem projektu gry, który zawiera plik *.wroject.*

   > [!NOTE]
   > **Wtyczka silnika:** Jeśli używasz Wwise jako wtyczki silnika, powinieneś użyć wtyczki Project Acoustics jako wtyczki silnika Unreal. Zamiast cytowanego wcześniej katalogu docelowego należy użyć funkcji *[UESource]\Engine\Plugins\ProjectAcoustics*.

1. Upewnij się, że obok folderu *ProjectAcoustics* jest wyświetlany folder *Wwise.* Zawiera wtyczkę Wwise wraz z plikami binarnymi wtyczki miksera, którą wdrożono wcześniej.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozszerzanie funkcji wtyczki Wwise Unreal
Wtyczka Project Acoustics Unreal wymaga dodatkowego zachowania ujawnionego z interfejsu API wtyczki Wwise Unreal zgodnie z [tymi wytycznymi.](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html) Mamy dołączony plik wsadowy do automatyzacji procedury łatanie.

* Inside *Plugins\ProjectAcoustics\Resources*, uruchom *PatchWwise.bat*. Poniższy przykładowy obraz używa naszego projektu próbki AcousticsGame.

    ![Okno Eksploratora Windows ze skryptem do poprawki Wwise wyróżnione](media/patch-wwise-script.png)

* Jeśli nie masz zainstalowanego SDK DirectX: W zależności od używanej wersji programu Wwise może być `DXSDK_DIR` konieczne skomentowanie wiersza zawierającego w *sekcji AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Edytor kodu pokazujący "DXSDK" skomentował](media/directx-sdk-comment.png)

* Jeśli kompilujesz przy użyciu programu Visual Studio 2019: Aby obejść błąd `VSVersion` łączenia z Wwise, ręcznie zmień wartość domyślną w *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Edytor kodu z poleceniem "VSVersion" zmieniono na "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Zbuduj grę i sprawdź, czy Python jest włączony

1. Skompiluj swoją grę i upewnij się, że buduje poprawnie. Jeśli nie jest kompilacji, sprawdź poprzednie kroki dokładnie przed kontynuowaniem.

1. Otwórz swój projekt w Edytorze nierealiskowym.

    > [!NOTE]
    > **Wtyczka silnika:** Jeśli używasz ProjectAcoustics jako wtyczki aparatu, upewnij się również, że jest ona włączona w wtyczkach "wbudowanych".

    Powinien zostać wyświetlony nowy tryb, który wskazuje, że akustyka projektu została zintegrowana.

    ![Tryb akustyki pełny w Unreal](media/acoustics-mode-full.png)

1. Upewnij się, że wtyczka Python dla Unreal jest włączona, dzięki czemu integracja edytora działa poprawnie.

    ![Rozszerzenia Języka Python w edytorze Unreal włączone](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Konfigurowanie projektu Wwise do używania akustyki projektu

Przykładowy projekt Wwise znajduje się w próbkach pobierania. Zaleca się, aby wyświetlić go wraz z tymi instrukcjami. Zrzuty ekranu w dalszej części tego artykułu pochodzą z tego projektu.

#### <a name="bus-setup"></a>Konfiguracja magistrali
Wtyczka Project Acoustics Unreal będzie szukać skojarzonej wtyczki miksera na magistrali `Project Acoustics Bus`o dokładnej nazwie. Utwórz nową magistrali audio o tej samej nazwie. Wtyczka miksera może działać w różnych konfiguracjach. Ale na razie zakładamy, że będzie on używany tylko do przetwarzania pogłosu. Ta magistrala będzie nosić mieszany sygnał pogłosu dla wszystkich źródeł, które używają Akustyki. Może mieszać się w górę do dowolnej struktury mieszania magistrali. Przykład jest pokazany tutaj z projektu próbki Wwise, który jest uwzględniony w przykładowym pobraniu.

![Autobusy Wwise z projektem Akustyka Bus](media/acoustics-bus.png)

1. Ustaw konfigurację kanału na magistrali na *1.0,* *2.0,* *4.0,* *5.1*lub *7.1*. Każde inne ustawienie spowoduje brak wyjścia w magistrali.

    ![Opcje konfiguracji kanału dla project akustyki magistrali](media/acoustics-bus-channel-config.png)

1. Przejdź do szczegółów magistrali akustyki projektu i upewnij się, że widzisz kartę **Wtyczki miksera.**

    ![Wwise z włączoną zakładką wtyczek miksera dla magistrali akustyki projektu](media/mixer-tab-enable.png)

1. Przejdź do karty **Wtyczka miksera** i dodaj wtyczkę miksera akustyki projektu do magistrali.

    ![Diagram dodawania wtyczki Mikser akustyki projektu do magistrali Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Konfiguracja hierarchii aktora-miksera
Aby uzyskać najlepszą wydajność, program Project Acoustics stosuje przetwarzanie cyfrowego sygnału audio do wszystkich źródeł jednocześnie. Tak więc wtyczka musi działać jako wtyczka miksera. Wwise wymaga, aby wtyczki miksera były włączone do magistrali wyjściowej, chociaż magistrala wyjściowa zwykle przenosi suchy sygnał wyjściowy. Projekt Akustyka wymaga, aby sygnał suchy był kierowany przez autobusy aux, podczas gdy sygnał mokry jest przenoszony `Project Acoustics Bus`na . Poniższy proces obsługuje stopniową migrację do tego przepływu sygnału.

Załóżmy, że masz istniejący projekt z hierarchią aktor-mikser, która zawiera *kroki,* *broń*i inne na najwyższym poziomie. Każdy z nich ma odpowiednią magistralę wyjściową dla swojej suchej mieszanki. Załóżmy, że chcesz przeprowadzić migrację kroków, aby użyć akustyki. Najpierw utwórz odpowiednią magistralę aux do przewozu suchego submixu, który jest dzieckiem magistrali wyjściowej kroków. Na przykład użyliśmy prefiksu "Suche" na poniższej ilustracji do zorganizowania autobusów, chociaż dokładna nazwa nie jest ważna. Wszelkie liczniki lub efekty, które miałeś na magistrali po śladach, nadal będą funkcjonować tak jak poprzednio.

![Zalecana konfiguracja suchej mieszanki Wwise](media/wwise-dry-mix-setup.png)

Następnie zmodyfikuj strukturę wyjściową magistrali dla mieszacza aktora Footsteps w następujący sposób, z *ustawioną magistralą akustyki projektu* jako **magistralą wyjściową**i *Dry_Footsteps* ustawioną jako magistralę aux zdefiniowaną przez użytkownika.

![Zalecana konfiguracja magistrali miksera aktora Wwise](media/actor-mixer-bus-settings.png)

Teraz wszystkie kroki uzyskać akustykę leczenia i produkcji ich pogłos na Project Acoustics Bus. Suchy sygnał jest kierowany przez Dry_Footsteps i przestrzennie jak zwykle.

Akustyka projektu dotyczy tylko dźwięków, które mają lokalizację 3D na świecie. Zgodnie z [dokumentacją firmy Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)należy ustawić właściwości pozycjonowania w sposób pokazany. Ustawienie **spatialization 3D** może być *położenie* lub położenie *+ orientacja* w razie potrzeby.

![Zalecane ustawienia pozycjonowania aktora Wwise](media/wwise-positioning.png)

Nie można ustawić **magistrali wyjściowej** na inną magistralę, która miesza się w górę rzeki do *project akustyki magistrali*. Wwise nakłada to wymagania na wtyczki miksera.

Jeśli chcesz, aby element podrzędny w hierarchii aktora-miksera nie używał akustyki, możesz użyć "zastępuj nadrzędnego", aby go zrezygnować.

Jeśli używasz zdefiniowanych przez grę lub zdefiniowanych przez użytkownika połaci pogłosu dowolnego aktora-miksera w grze, wyłącz je na tym mikserze aktora, aby uniknąć dwukrotnego stosowania pogłosu.

#### <a name="spatialization"></a>Spatialization (Spatialization)
Wtyczka miksera Project Acoustics Wwise domyślnie stosuje pogłos splotu, pozostawiając Wwise'owi przestrzenność przesuwania. Korzystając z akustyki projektu w tej domyślnej konfiguracji tylko pogłosu, można użyć dowolnej konfiguracji kanału i metody przestrzenności na suchej mieszance. Tak, można mieszać i dopasować prawie każdy spatializer z pogłosem Akustyka projektu. Dostępne opcje obejmują [obuusznikowe obuuszniki oparte na ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) i [system Windows Sonic.](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)
 
Projekt Akustyka zawiera opcjonalny spatializer, który obsługuje zarówno obiektowe renderowanie HRTF wysokiej rozdzielczości i przesuwanie. Zaznacz pole wyboru **Wykonywanie przestrzenności** w ustawieniach wtyczki miksera i wybierz między *HRTF* lub *Przesuwaniem*. Ponadto wyłącz zdefiniowane przez użytkownika aux wysyła do wszystkich suchych magistrali, aby uniknąć przestrzenności dwukrotnie przez projekt akustyka mikser plug-in i Wwise. Trybu przestrzenności nie można zmienić w czasie rzeczywistym, ponieważ wymaga on regeneracji banku dźwięku. Uruchom ponownie unreal, a następnie ponownie wygeneruj soundbanks przed wybraniem play w celu zintegrowania zmian konfiguracji wtyczek miksera, takich jak ustawienie wyboru **Wykonaj spatialization.**

![Ustawienia spatialization wtyczek wiarek mixeru](media/mixer-spatial-settings.png)

Niestety inne wtyczki spatializer oparte na obiektach nie są obecnie obsługiwane. Są one implementowane jako wtyczki miksera, a Wwise nie zezwala na przypisywanie wielu wtyczek miksera do jednego miksera aktora.  

### <a name="audio-setup-in-unreal"></a>Konfiguracja dźwięku w unreal
1. Po pierwsze, musisz upiec swój poziom gry, aby stworzyć akustykę, która zostanie umieszczona w *Content\Acoustics*. Zapoznaj się z [Unreal Bake Tutorial](unreal-baking.md). Niektóre wstępnie upieczone poziomy są zawarte w opakowaniu próbki.

1. Utwórz aktora przestrzeni Akustyki w scenie. Tylko utworzyć jeden z tych aktorów na poziomie, ponieważ reprezentuje akustykę dla całego poziomu.

    ![Tworzenie aktora przestrzeni Akustyki w edytorze Unreal](media/create-acoustics-space.png)

1. Przypisz zasób wypieków akustycznych do gniazda danych Akustyka w aktorze przestrzeni Akustyki. Twoja scena ma teraz akustykę!

    ![Przypisanie zasobów akustyki w edytorze Unreal](media/acoustics-asset-assign.png)

1. Dodaj pustego aktora. Skonfiguruj go w następujący sposób.

    ![Edytor Unreal pokazuje użycie komponentu Akustyka w pustym aktorze](media/acoustics-component-usage.png)

       
    <sup>1</sup> Dodaj składnik dźwięku akustyki do aktora. Ten składnik dodaje funkcjonalność Akustyka projektu do składnika audio Wwise.
        
    <sup>2</sup> Domyślnie zaznaczone jest pole **Odtwórz na starcie.** To ustawienie wyzwala skojarzone zdarzenie Wwise podczas uruchamiania poziomu.</li>
         
    <sup>3</sup> Użyj pola wyboru **Pokaż parametry akustyki,** aby wydrukować na ekranie informacje o źródle.

    ![Panel Akustyka edytora Unreal na źródle dźwięku z włączonymi wartościami debugowania](media/debug-values.png)

    <sup>4</sup> Przypisz zdarzenie Wwise dla zwykłego przepływu pracy Wwise.4 Assign a Wwise event per the usual Wwise workflow.
       
    <sup>5</sup> Upewnij się, że **opcja Użyj dźwięku przestrzennego** jest wyłączona. Jeśli używasz akustyki projektu dla określonego komponentu audio, nie można jednocześnie używać silnika dźwięku przestrzennego firmy Wwise do akustyki.</li>
       
Wszystko jest gotowe. Poruszaj się po scenie i odkrywaj efekty akustyczne!

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [tutorial Projekt Akustyka Nierealna/Wwise Design.](unreal-workflow.md)
* Dowiedz [się, jak zrobić piecze](unreal-baking.md) dla scen gry.
