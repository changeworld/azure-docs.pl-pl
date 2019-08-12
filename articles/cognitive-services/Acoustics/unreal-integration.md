---
title: Unrealość projektu i integracja z Wwise
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano integrację projektów akustycznych Unreal i Wwise w projekcie.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 47f39e8dcd96ea3bdba564df348e9b89a6b036ba
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933161"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Unrealość projektu i integracja z Wwise
Ta instrukcja zapewnia szczegółowe kroki integracji pakietu wtyczki akustycznej projektu do istniejącego projektu gier Unreal i Wwise. 

Wymagania dotyczące oprogramowania:
* [Aparat Unreal](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1.\*
* [Wtyczka Wwise dla Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Jeśli używasz bezpośredniej integracji zestawu SDK Wwise zamiast korzystania z wtyczek Unreal Wwise, zapoznaj się z wtyczką "akustyczne projektu Unreal" i Dostosuj wywołania interfejsu API Wwise.

Jeśli chcesz używać akustycznych projektów z silnikiem audio innym niż Wwise, wprowadź żądanie usprawnienia na [forum dyskusyjnym](https://github.com/microsoft/ProjectAcoustics/issues)dotyczącym projektora. Wtyczki Unreal można użyć do wykonywania zapytań dotyczących danych akustycznych, a następnie dokonywać wywołań interfejsu API do aparatu.

## <a name="download-project-acoustics"></a>Pobierz akustyczne projektu
Jeśli jeszcze tego nie zrobiono, Pobierz [pakiet plug-in "& Unreale Wwises](https://www.microsoft.com/download/details.aspx?id=58090)". 

W pakiecie dołączono wtyczkę aparatu Unreal oraz wtyczkę miksera Wwise. Wtyczka Unreal zapewnia integrację edytora i środowiska uruchomieniowego. W trakcie rozgrywkęi, projekt akustyczny Unreal przetwarza parametry, takie jak zamknięcia dla każdego obiektu gry każdej ramki. Te parametry są tłumaczone na wywołania interfejsu API Wwise.

## <a name="review-integration-steps"></a>Przegląd kroków integracji

Istnieją następujące podstawowe kroki, które należy wykonać, aby zainstalować pakiet i wdrożyć go w grze.
1. Instalowanie wtyczki Wwise miksera projektu
2. (Re) Wdróż Wwise na grze. Ten krok propaguje wtyczkę miksera do projektu gry.
3. Dodawanie wtyczki Unrealości projektu do gry
4. Rozszerzona funkcja wtyczki Wwise Unreal
5. Kompilacja gier i sprawdzanie języka Python jest włączona
6. Konfigurowanie projektu Wwise do korzystania z akustycznych projektu
7. Konfiguracja audio w Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Zainstaluj wtyczkę miksera w projekcie
* Otwórz program uruchamiającego Wwise, a następnie na karcie **wtyczki** w obszarze **Instaluj nowe wtyczki**wybierz pozycję **Dodaj z katalogu**. 

    ![Zrzut ekranu przedstawiający Instalowanie wtyczki w programie Wwise](media/wwise-install-new-plugin.png)

* `AcousticsWwisePlugin\ProjectAcoustics` Wybierz katalog, który został dołączony do pobranego pakietu. Zawiera pakiet wtyczki miksera Wwise.

* Program Wwise zainstaluje wtyczkę. W programie Wwise na liście zainstalowanych wtyczek powinna być teraz wyświetlana wartość akustyczna projektu.  
![Zrzut ekranu przedstawiający listę wtyczek programu Wwise po instalacji hałasu projektu](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) Wdróż Wwise w grze
Ponownie Wdróż Wwise w grze, nawet jeśli już masz integrację Wwise. Spowoduje to wyszukanie wtyczki Wwise dla projektu.

* **Wtyczka aparatu:** Jeśli zainstalowano Wwise jako wtyczkę gier w projekcie Unreal C++ , Pomiń ten krok. Jeśli jest zainstalowana, a nie jako wtyczka aparatu, na przykład, ponieważ projekt Unreal jest tylko planem, wdrożenie Wwise z naszą wtyczką miksera jest bardziej skomplikowane. Utwórz fikcyjny, pusty projekt C++ Unreal, zamknij go, jeśli zostanie otwarty Edytor Unreal i postępuj zgodnie z pozostałą procedurą, aby wdrożyć Wwise w tym fikcyjnym projekcie. Następnie skopiuj wdrożony dodatek Wwise.
 
* W obszarze Uruchamianie Wwise kliknij kartę **aparat Unreal** , a następnie kliknij menu Hamburger obok pozycji **ostatnie projekty aparatu Unreal** i wybierz polecenie **Przeglądaj w poszukiwaniu projektu**. Otwórz plik projektu `.uproject` Unreal gry.

    ![Zrzut ekranu karty Unreal modułu uruchamiania Wwise](media/wwise-unreal-tab.png)

* Następnie kliknij pozycję **Zintegruj Wwise w projekcie** lub **zmodyfikuj Wwise w projekcie**. Ten krok (ponownie) integruje pliki binarne Wwise z projektem, a teraz łącznie z wtyczką miksera dla projektu.

* **Wtyczka aparatu:** Jeśli używasz Wwise jako wtyczki aparatu i utworzysz projekt fikcyjny jak powyżej, skopiuj folder Wwise wdrożony: `[DummyUProject]\Plugins\Wwise` i wklej `[UESource]\Engine\Plugins\Wwise`go. `[DummyUProject]`jest pustą ścieżką projektu Unreal C++ i `[UESource]` jest miejscem, w którym są zainstalowane źródła aparatu Unreal. Po zakończeniu kopiowania można usunąć fikcyjny projekt.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Dodawanie wtyczki Unrealości projektu do gry
 
* Skopiuj folder w pakiecie wtyczki i Utwórz nowy folder `[UProjectDir]\Plugins\ProjectAcoustics`, gdzie `UProjectDir` `.uproject` jest folderem projektu gry zawierającego plik. `Unreal\ProjectAcoustics`
  * **Wtyczka aparatu**: Jeśli używasz Wwise jako wtyczki aparatu, należy również użyć funkcji akustycznych projektu jako wtyczki aparatu Unreal. Zamiast powyższego katalogu docelowego Użyj: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Potwierdź, że zobaczysz `Wwise` folder `ProjectAcoustics` obok folderu. Zawiera wtyczkę Wwise wraz z plikami binarnymi dla wtyczki miksera, która została wdrożona w kroku 2 powyżej.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Rozszerzona funkcja wtyczki Wwise Unreal
* Wtyczka Unreal akustyczna projektu wymaga, aby dodatkowe zachowanie było udostępniane z interfejsu API wtyczki Unreal Wwise zgodnie z [tymi wskazówkami](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Dodaliśmy plik wsadowy służący do automatyzowania procedury poprawek. 
* Wewnątrz `Plugins\ProjectAcoustics\Resources`, uruchom `PatchWwise.bat`polecenie. Poniższy przykład obrazu używa naszego przykładowego projektu AcousticsGame.

    ![Zrzut ekranu przedstawiający okno Eksplorator Windows z wyróżnionym skryptem do poprawki Wwise](media/patch-wwise-script.png)

* Jeśli nie masz zainstalowanego zestawu SDK programu DirectX, w zależności od używanej wersji programu Wwise może być konieczne komentowanie wiersza zawierającego `DXSDK_DIR` w: `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Zrzut ekranu edytora kodu pokazujący DXSDK z komentarzem](media/directx-sdk-comment.png)

* W przypadku kompilowania z programem Visual Studio 2019, aby obejść błąd łączenia z Wwise, ręcznie Edytuj wartość `VSVersion` domyślną `vc150`w `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` :

    ![Zrzut ekranu edytora kodu pokazujący VSVersion zmieniony na vc150](media/vsversion-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Kompilacja gier i sprawdzanie języka Python jest włączona

* Kompiluj grę i upewnij się, że kompilacja została poprawna. W przeciwnym razie przed kontynuowaniem sprawdź poprzednie kroki. 
* Otwórz projekt w edytorze Unreal. 
* **Wtyczka aparatu:** W przypadku używania ProjectAcoustics jako wtyczki aparatu należy również upewnić się, że jest on włączony, na liście wtyczek "wbudowane".
* Powinien pojawić się nowy tryb wskazujący, że akustyczne projektu zostały zintegrowane.

    ![Zrzut ekranu przedstawiający Unreal z trybem akustycznym](media/acoustics-mode-full.png)

* Upewnij się, że jest włączona wtyczka języka Python dla Unreal. Jest to wymagane do poprawnego działania integracji edytora.

    ![Zrzut ekranu przedstawiający Włączanie rozszerzeń języka Python w edytorze Unreal](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Konfiguracja projektu Wwise

Przykładowy projekt Wwise jest dołączany do pobierania próbek. Zalecamy zapoznanie się z tymi instrukcjami. Poniższe zrzuty ekranu są pobierane z tego projektu.

### <a name="bus-setup"></a>Konfiguracja magistrali
* Wtyczka Unreal akustycznych projektu będzie szukać skojarzonej wtyczki miksera na magistrali o tej ***samej*** nazwie: `Project Acoustics Bus`. Utwórz nową magistralę audio o tej nazwie. Wtyczka miksera może działać w różnych konfiguracjach, ale na razie założono, że zostanie ona użyta do Reverb przetwarzania. Ta magistrala będzie obsługiwać mieszany sygnał Reverb dla wszystkich źródeł, które używają akustycznych. Może on mieszać wszystkie struktury miksowania magistrali. przykład jest przedstawiony poniżej, pobrany z naszego przykładowego projektu Wwise, zawartego w pobieranym przykładzie.

    ![Zrzut ekranu przedstawiający Wwise Busses z magistralą akustyczną projektu](media/acoustics-bus.png)

* Konfiguracja kanału na magistrali musi być ustawiona na jedną z: `1.0, 2.0, 4.0, 5.1 or 7.1`. Inne konfiguracje nie spowodują wyjścia w tej magistrali.

    ![Zrzut ekranu przedstawiający opcje konfiguracji kanału dla magistrali akustycznej projektu](media/acoustics-bus-channel-config.png)

* Teraz przejdź do szczegółów magistrali akustycznej projektu i sprawdź, czy jest wyświetlana karta wtyczka miksera

    ![Zrzut ekranu przedstawiający Wwise pokazujący, jak włączyć kartę wtyczki miksera dla magistrali akustycznej projektu](media/mixer-tab-enable.png)

* Następnie przejdź do karty wtyczka miksera i Dodaj wtyczkę Mieszarki do zestawu akustycznego projektu do magistrali

    ![Zrzut ekranu przedstawiający magistralę Wwise](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Konfiguracja hierarchii dla miksera
* Ze względu na wydajność, akustyczne projektu stosuje procesor DSP audio do wszystkich źródeł jednocześnie. Wymaga to, aby wtyczka działała jako wtyczka miksera. Wwise wymaga, aby wtyczki miksera były przechowywane na magistrali wyjściowej, mimo że magistrala wyjściowa zwykle przenosi suchy sygnał wyjściowy. Akustyczne projektowe wymaga, aby suchy sygnał był kierowany przez Busses AUX, podczas gdy `Project Acoustics Bus`na. Następujący proces obsługuje stopniowe Migrowanie do tego przepływu sygnałów.

* Załóżmy, że masz istniejący projekt z hierarchią miksera aktora zawierającą takie rozwiązanie wprowadziły, broń i inne na najwyższego poziomu. Każda z nich ma odpowiednią magistralę wyjściową dla swojego suchego miksu. Pozwala na to, że chcesz migrować takie rozwiązanie wprowadziły do korzystania z akustycznych. Najpierw Utwórz odpowiednią magistralę pomocniczą, aby przenieść jej suchy Submix, który jest elementem podrzędnym magistrali wyjściowej takie rozwiązanie wprowadziły. Na przykład w poniższej ilustracji użyto prefiksu "suchy", ale dokładna nazwa nie jest ważna. Wszystkie liczniki lub efekty, które były dostępne na magistrali takie rozwiązanie wprowadziły, będą nadal działać tak jak wcześniej.

    ![Zrzut ekranu przedstawiający zalecaną konfigurację miksu Wwise](media/wwise-dry-mix-setup.png)

* Następnie zmodyfikuj strukturę wyjściową magistrali dla miksera aktora takie rozwiązanie wprowadziły w następujący sposób, z magistralą akustyczną projektu ustawioną jako magistrala wyjściowa i Dry_Footsteps ustawioną jako magistrala AUX zdefiniowana przez użytkownika.

    ![Zrzut ekranu przedstawiający zalecaną konfigurację magistrali miksera aktora Wwise](media/actor-mixer-bus-settings.png)

* Teraz wszystkie takie rozwiązanie wprowadziły są traktowane jako akustyczne i wyjściowe Reverb na magistrali akustycznej projektu. Suchy sygnał jest kierowany przez Dry_Footsteps i przewidziany w zwykły sposób.

* Akustyczne projektu dotyczą tylko dźwięków, które mają lokalizację 3W na świecie. Zgodnie z [dokumentacją Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)należy ustawić właściwości pozycjonowania, jak pokazano. Ustawienie "3D Spatialization" może mieć wartość "position" lub "Position + Orientacja" zgodnie z wymaganiami.

    ![Zrzut ekranu przedstawiający zalecane ustawienia pozycjonowania aktora Wwise](media/wwise-positioning.png)

* Ustawienie magistrali wyjściowej na inną magistralę, która miesza się z magistralą dźwiękową, nie będzie działała. Wwise nakłada to wymaganie na wtyczki miksera.

* Jeśli chcesz, aby element podrzędny w hierarchii takie rozwiązanie wprowadziły aktora nie korzystał z ustawień akustycznych, możesz zawsze użyć opcji "Zastąp element nadrzędny", aby go wycofać.

* W przypadku korzystania z funkcji wysyłania przez użytkownika lub do Reverb na dowolnym z nich miksera aktora w grze należy wyłączyć te elementy w ramach tego miksera aktora, aby uniknąć konieczności stosowania Reverb dwa razy.

### <a name="spatialization"></a>Spatialization
Domyślnie wtyczka Wwise jest stosowana w programie Convolution Reverb, pozostawiając Wwise do przesuwania spatialization. W przypadku korzystania z funkcji akustycznych projektu w tej domyślnej konfiguracji tylko Reverb można korzystać z konfiguracji kanału i metody spatialization w ramach mieszanki suchej, co umożliwia mieszanie i dopasowywanie niemal wszelkich spatializer za pomocą Reverb akustycznych projektu. Dostępne opcje obejmują [Ambisonics Binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) i [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Wartości akustyczne projektu obejmują opcjonalny spatializer, który obsługuje zarówno renderowanie HRTF o wysokiej rozdzielczości oparte na obiektach, jak i panoramowanie. Zaznacz pole wyboru "wykonaj Spatialization" w ustawieniach wtyczki miksera i wybierz opcję między HRTF lub panoramowanie i Wyłącz zdefiniowane przez użytkownika elementy pomocnicze, które zostały skonfigurowane powyżej dla wszystkich suchych Busses, aby uniknąć spatializing dwa razy, zarówno z wtyczką miksera w projekcie, jak i Wwise. Nie można zmienić trybu spatialization w czasie rzeczywistym, ponieważ wymaga to ponownego wygenerowania danych z banku. Musisz ponownie uruchomić usługę Unreal, a następnie wygenerowanie soundbanks przed naciśnięciem przycisku odtwarzania, aby pobrać zmiany konfiguracji wtyczki miksera, takie jak pole wyboru "wykonaj Spatialization".

![Zrzut ekranu ustawień Spatialization wtyczki Wwise miksera](media/mixer-spatial-settings.png)

Niestety, w tej chwili nie można obsługiwać innych wtyczek spatializer opartych na obiektach, ponieważ są one zaimplementowane jako wtyczki miksera, a Wwise nie zezwala obecnie na wiele wtyczek do obsługi jednego aktora.  

## <a name="7-audio-setup-in-unreal"></a>7. Konfiguracja audio w Unreal
* Najpierw należy tworzenie poziom gry, aby utworzyć zasób akustyczny, który zostanie umieszczony w `Content\Acoustics`. Zapoznaj się z [samouczkiem Unreal tworzenie](unreal-baking.md) i Wznów to tutaj. Niektóre wstępnie rozszerzania poziomy są zawarte w pakiecie przykładowym.
* Utwórz aktora obszaru akustycznego w scenie. Utwórz tylko jedną z tych aktorów na poziomie, ponieważ reprezentuje ona wartości akustyczne całego poziomu. 

    ![Zrzut ekranu edytora Unreal pokazujący tworzenie aktora przestrzeni](media/create-acoustics-space.png)

* Teraz Przypisz zasób danych akustycznych rozszerzania do miejsca na dane akustyczne aktora obszaru. Twoja scena ma teraz akustyczne!

    ![Zrzut ekranu edytora Unreal pokazujący akustyczne przypisanie zasobów](media/acoustics-asset-assign.png)

* Teraz Dodaj pusty aktor i wykonaj następujące czynności:

    ![Zrzut ekranu edytora Unreal przedstawiający użycie składnika akustycznego w pustym aktorze](media/acoustics-component-usage.png)

1. Dodaj składnik audio akustyczny do aktora. Ten składnik rozszerza składnik audio Wwise z funkcjonalnością dla akustycznych projektu.
2. Pole rozpoczęcie odtwarzania jest domyślnie zaznaczone, co spowoduje wyzwolenie skojarzonego zdarzenia Wwise na poziomie uruchamiania.
3. Pole wyboru Pokaż parametry akustyczne służy do drukowania informacji o debugowaniu na ekranie.  
    ![Zrzut ekranu przedstawiający panel akustyczny edytora Unreal w źródle dźwięku z włączonymi wartościami debugowania](media/debug-values.png)
4. Przypisywanie zdarzenia Wwise na typowym przepływie pracy Wwise
5. Upewnij się, że funkcja use audio przestrzenny jest wyłączona. W tej chwili, jeśli używasz akustycznych projektów dla określonego składnika audio, nie można jednocześnie używać Wwiseego aparatu audio dla hałasu.

Wszystko jest gotowe. Poruszaj się wokół sceny i Eksploruj efekty akustyczne!

## <a name="next-steps"></a>Następne kroki
* [](unreal-workflow.md) Samouczek projektowy dotyczący akustycznych projektów w Unreal/Wwise
* [Dowiedz się, jak tworzony](unreal-baking.md) na potrzeby scen gry 
