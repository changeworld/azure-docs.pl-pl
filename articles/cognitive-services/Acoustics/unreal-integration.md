---
title: Integracja Wwise i Unreal Akustyka projektu
titlesuffix: Azure Cognitive Services
description: Niniejszy instruktaż zawiera opis integracji programów Project Akustyka Unreal i Wwise wtyczki do projektu.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: c6baa9f8330338c1e5fdc9ee0b5a8cc8b344e871
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436145"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integracja Wwise i Unreal Akustyka projektu
Niniejszy instruktaż zawiera kroki szczegółowe integracji pakietu wtyczki Akustyka projektu do istniejącego projektu gier Unreal i Wwise. 

Wymagania dotyczące oprogramowania:
* [Aparatu unreal Engine](https://www.unrealengine.com/) 4.20 lub 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Wtyczka Wwise dla Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Jeśli używasz bezpośredniego integracji zestawu Wwise SDK zamiast korzystać z wtyczek Wwise Unreal, zapoznaj się z projektu Akustyka Unreal wtyczki i dostosować Wwise interfejsu API.

Jeśli chcesz Akustyka projektu za pomocą aparat audio w innych niż Wwise, skontaktuj się z nami na [forów Akustyka projektu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). Wtyczki projektu Akustyka Unreal służy do wysyłania zapytań dotyczących danych Akustyka, a następnie wywołań interfejsu API usługi aparatu.

## <a name="download-project-acoustics"></a>Pobierz Akustyka projektu
Jeśli nie jest jeszcze pobrać [pakietu wtyczki projektu Akustyka Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090)). 

Dołączyliśmy wtyczki aparatu Unreal Engine i wtyczki mixer Wwise w pakiecie. Wtyczka Unreal zapewnia integrację edytora i środowiska uruchomieniowego. Podczas gry unreal Engine Akustyka projektu wtyczka oblicza parametry, takie jak zamknięcia dla każdego obiektu gry każdej ramce. Te parametry są tłumaczone na wywołania interfejsu API Wwise.

## <a name="review-integration-steps"></a>Przejrzyj kroki integracji

Istnieją następujące główne kroki, należy zainstalować pakiet i wdrożyć ją w grze.
1. Instalowanie wtyczki mixer Wwise Akustyka projektu
2. (Ponownej) należy wdrożyć Wwise w grze. W tym kroku propaguje mixer wtyczki do projektu gier.
3. Dodawanie projektu Akustyka Unreal wtyczki do gry
4. Rozszerzanie funkcji wtyczki unreal Engine firmy Wwise
5. Tworzenie gier i sprawdź, czy włączono języka Python
6. Konfigurowanie projektu Wwise używać Akustyka projektu
7. Konfiguracja audio w Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Instalowanie wtyczki mixer Akustyka projektu
* Następnie otworzyć uruchamiania Wwise w **wtyczek** , w obszarze **instalowanie nowych wtyczek**, wybierz opcję **dodawanie z katalogu**. 

    ![Zrzut ekranu przedstawiający Instalowanie wtyczki uruchamiania Wwise](media/wwise-install-new-plugin.png)

* Wybierz `AcousticsWwisePlugin\ProjectAcoustics` katalogu, która została uwzględniona w pakiecie, który został pobrany. Zawiera ona pakietu wtyczki Wwise mixer.

* Wwise zainstaluje wtyczkę. Akustyka projektu powinien teraz wyświetlane na liście zainstalowanych wtyczek w Wwise.
![Lista wtyczek zrzut ekranu Wwise zainstalowane po instalacji Akustyka projektu](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Ponownej) wdrożenia Wwise w grze
Ponownie wdróż Wwise do gry, nawet jeśli została już zintegrowana Wwise. Przejmuje wtyczki Wwise Akustyka projektu.

* **Aparat wtyczki:** Jeśli masz zainstalowany jako gier wtyczki w projekcie Unreal C++ Wwise, Pomiń ten krok. Jeśli jest zainstalowany zamiast tego, jak wtyczki aparatu, ponieważ Unreal projektu jest plan, Wwise wdrożeniu obsługującym tylko za pomocą naszych wtyczki mixer wystąpienia jest bardziej złożone. Tworzenie projektu Unreal C++ fikcyjnego, pusty, zamknij go, jeśli zostanie otwarty Edytor Unreal i wykonaj pozostałe procedurę, aby wdrożyć Wwise do tego projektu zastępczy. Następnie skopiuj z wdrożonym wtyczki Wwise.
 
* Z obszaru uruchamiania Wwise, kliknij przycisk **Unreal Engine** kartę, a następnie kliknij menu "hamburger" **ostatnie projekty systemu Unreal Engine** i wybierz **Przeglądaj w poszukiwaniu projektu**. Otwórz projekt Unreal swoją grę `.uproject` pliku.

    ![Zrzut ekranu z Wwise uruchamiania na karcie Unreal](media/wwise-unreal-tab.png)

* Następnie kliknij przycisk **integracja Wwise w projekcie** lub **zmodyfikować Wwise w projekcie**. Ten krok (ponownej) integruje się z plików binarnych Wwise projektu, w tym teraz wtyczki mixer Akustyka projektu.

* **Aparat wtyczki:** Jeśli używasz Wwise jako wtyczki aparatu i utworzono projekt zastępczy, jak powyżej, skopiuj folder Wwise wdrożone: `[DummyUProject]\Plugins\Wwise` i wklej go w miejsce `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` Pusta ścieżka projektu Unreal C++ i `[UESource]` jest, w którym masz źródeł aparatu Unreal Engine zainstalowane. Po zakończeniu kopiowania, można usunąć projekt zastępczy.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Dodawanie projektu Akustyka Unreal wtyczki do gry
 
* Kopia `Unreal\ProjectAcoustics` folderu we wtyczce pakietu i Utwórz nowy folder `[UProjectDir]\Plugins\ProjectAcoustics`, gdzie `UProjectDir` to zawierający folder projektu swoją grę `.uproject` pliku.
  * **Wtyczki aparatu**: Jeśli używasz Wwise jako wtyczki aparatu, należy użyć Akustyka projektu jako wtyczki aparatu Unreal engine, jak również. Zamiast powyższych katalogu docelowego, użyj: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Upewnij się, zostanie wyświetlony `Wwise` folder wraz z `ProjectAcoustics` folderu. Zawiera ona wtyczki Wwise wraz z plikami binarnymi dla wtyczki mixer powrotnego wdrożony w kroku 2 powyżej.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Rozszerzanie funkcji wtyczki unreal Engine firmy Wwise
* Projekt Akustyka Unreal wtyczka wymaga dodatkowych zachowanie ujawnianie z wtyczki Wwise Unreal interfejsu API na [te wytyczne](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Dołączyliśmy Automatyzowanie stosowania poprawek procedury pliku wsadowego. 
* Wewnątrz `Plugins\ProjectAcoustics\Resources`Uruchom `PatchWwise.bat`. Na poniższej ilustracji przykład używa naszej AcousticsGame przykładowy projekt.

    ![Wyróżnianie okna zrzut ekranu Eksploratora Windows podany skrypt do poprawki Wwise](media/patch-wwise-script.png)

* Jeśli masz zainstalowany zestaw SDK programu DirectX, należy przekształcić w komentarz wiersz zawierający DXSDK_DIR w `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Zrzut ekranu przedstawiający edytor kodu, przedstawiający DXSDK oznaczone jako komentarz](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Tworzenie gier i sprawdź, czy włączono języka Python

* Skompiluj swoją grę i upewnij się, poprawnie kompilacji. W przeciwnym razie sprawdź poprzednie kroki dokładnie przed kontynuowaniem. 
* Otwórz swój projekt w edytorze Unreal. 
* **Aparat wtyczki:** Jeśli używasz ProjectAcoustics jako aparat wtyczki, upewnij się również czy jest włączona, na liście w obszarze "wbudowane" wtyczek.
* Powinien zostać wyświetlony nowy tryb oznacza, że został zintegrowany Akustyka projektu.

    ![Zrzut ekranu z Unreal przedstawiający Akustyka tryb pełny](media/acoustics-mode-full.png)

* Upewnij się, że wtyczka języka Python dla Unreal włączone. Jest to wymagane dla integracji edytora do poprawnego działania.

    ![Zrzut ekranu przedstawiający Włączanie rozszerzenia języka Python w edytorze Unreal](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Ustawienia projektu Wwise

Przykładowy projekt Wwise jest dołączony do pobierania próbek. Zaleca się, przeglądając wraz z tych instrukcji. Poniższe zrzuty ekranu są pobierane z tego projektu.

### <a name="bus-setup"></a>Instalator magistrali
* Wtyczka Unreal Akustyka projektu będzie szukał wtyczki skojarzone mixer na magistrali, w tym ***dokładnie*** name: `Project Acoustics Bus`. Tworzenie nowej magistrali audio o tej nazwie. Wtyczka mixer może pracować w różnych konfiguracjach, ale na razie Załóżmy, że będzie służyć do tylko pogłosu przetwarzania. Ta magistrali przeprowadzi sygnału mieszane pogłosu dla wszystkich źródeł, które używają Akustyka. Można go łączyć nadrzędne do dowolnego magistrali mieszanie struktury, przykładem jest pokazany poniżej i pobierane z naszego przykładowego projektu Wwise włączony do pobrania próbki.

    ![Zrzut ekranu Wwise magistrale przedstawiający magistrali Akustyka projektu](media/acoustics-bus.png)

* Konfiguracja kanału na magistrali musi być ustawiona na jeden z: `1.0, 2.0, 4.0, 5.1 or 7.1`. Inne konfiguracje spowoduje żadne dane wyjściowe tej magistrali.

    ![Zrzut ekranu przedstawiający opcje konfiguracji kanału dla magistrali Akustyka projektu](media/acoustics-bus-channel-config.png)

* Teraz przejdź do Akustyka projektu magistrali szczegółowe informacje, a następnie upewnij się, że widoczne na karcie wtyczki Mixer

    ![Zrzut ekranu pokazujący sposób włączyć na karcie wtyczki Mixer magistrali Akustyka projektu Wwise](media/mixer-tab-enable.png)

* Następnie przejdź do karty wtyczka Mixer i Dodaj mixer Akustyka projektu dodatku typu plug-in do magistrali

    ![Service bus Screenshow Wwise przedstawiająca sposób dodać wtyczkę Mixer Akustyka projektu](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Konfiguracja hierarchii mixer aktora
* Ze względu na wydajność Akustyka projektu dotyczy audio DSP wszystkich źródeł jednocześnie. Wymaga to dodatek do działania jako wtyczka mixer. Wwise wymaga wtyczki mixer się na magistrali danych wyjściowych, chociaż w magistrali danych wyjściowych zazwyczaj zawiera susz sygnałów. Akustyka projektu wymaga susz sygnału kierowane za pośrednictwem magistrale aux podczas mokrą sygnał jest prowadzona `Project Acoustics Bus`. Następujący proces obsługuje Stopniowa migracja z tym przepływem sygnału.

* Załóżmy, że masz istniejący projekt zawierający wprowadziły, broni i inne osoby na najwyższym poziomie hierarchia mixer aktora. Każda ma odpowiednie magistrali danych wyjściowych dla jego susz mieszanego. Załóżmy, że chcesz przeprowadzić migrację wprowadziły używać Akustyka umożliwia. Najpierw należy utworzyć odpowiednie magistrali aux do przenoszenia ich susz submix, który jest elementem podrzędnym wprowadziły magistrali danych wyjściowych. Na przykład była używana "Susz" prefiks w poniższy obraz do organizowania, mimo że dokładna nazwa nie jest ważna. Liczniki ani efekty, jaką miał na magistrali wprowadziły będą nadal działać tak jak poprzednio.

    ![Zrzut ekranu przedstawiający zalecane ustawienia Wwise susz mieszany](media/wwise-dry-mix-setup.png)

* Następnie zmodyfikuj następujący struktury danych wyjściowych magistrali wprowadziły Aktor — Mixer, z magistralą Akustyka projektu ustawiony na magistrali danych wyjściowych, a Dry_Footsteps Ustaw jako magistrali aux zdefiniowanych przez użytkownika.

    ![Zrzut ekranu przedstawiający zalecane ustawienia magistrali Mixer Wwise aktora](media/actor-mixer-bus-settings.png)

* Teraz wszystkie wprowadziły leczą Akustyka i danych wyjściowych ich pogłosu magistrali Akustyka projektu. Sygnał susz jest kierowany przez Dry_Footsteps i spatialized w zwykły sposób.

* Projekt Akustyka dotyczy tylko dźwięki, które mają 3D lokalizacji na całym świecie. Następujące [dokumentacji Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), właściwości pozycjonowania należy skonfigurować, jak pokazano. Ustawienie "3D Spatialization" może być "Pozycja" lub "Pozycja + orientacja", zgodnie z potrzebami.

    ![Zrzut ekranu przedstawiający Wwise aktora pozycjonowanie ustawień zalecanych](media/wwise-positioning.png)

* Ustawienie w magistrali danych wyjściowych do innych magistrali, który napisana nadrzędne do **magistrali Akustyka projektu** nie będzie działać. Wwise narzuca to wymaganie na wtyczek mixer.

* Chcąc podrzędnej w hierarchii mixer aktora wprowadziły, aby nie używać Akustyka, zawsze umożliwia "przesłonięcia nadrzędnej" on zoptymalizowany pod kątem.

* Jeśli używasz wysyła gry lub użytkownika dla pogłosu na wszelkie mixer aktora, gry, wyłącz te na tym mixer aktora, aby uniknąć stosowania pogłosu dwa razy.

### <a name="spatialization"></a>Spatialization
Domyślnie wtyczka mixer Wwise Akustyka projektu stosuje pogłosu konwolucji, pozostawiając Wwise celu przesuwania spatialization. Używając Akustyka projektu w tej domyślnej konfiguracji tylko do pogłosu, możesz użyć dowolnej metody konfiguracji i spatialization kanału susz kombinacji operacji we umożliwia mieszanie i dopasowywanie praktycznie dowolne spatializer z pogłosu Akustyka projektu. Opcje obejmują [na podstawie Ambisonics binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) i [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Akustyka projektu zawiera opcjonalne spatializer, który obsługuje zarówno opartej na obiektach wysokiej rozdzielczości HRTF renderowania i przesuwania. Zaznacz pole wyboru "Spatialization wykonania" w ustawieniach wtyczki mixer i wybrać HRTF lub sekcja Panoramowanie i wyłączyć wysyła aux zdefiniowanych przez użytkownika z ustawionym powyżej do wszystkich susz magistrale w celu uniknięcia spatializing dwa razy, zarówno za pomocą wtyczki mixer Akustyka projektu i Wwise. Nie można zmienić trybu spatialization w czasie rzeczywistym, ponieważ wymaga ona ponownego wygenerowania bank dźwięku. Należy ponownie uruchomić Unreal, a następnie ponownie wygenerować soundbanks przed osiągnięcia play, aby wczytać mixer wtyczki config zmiany, takie jak pola wyboru "Spatialization wykonania".

![Zrzut ekranu z Wwise Mixer wtyczki Spatialization ustawienia](media/mixer-spatial-settings.png)

Niestety innych wtyczek opartej na obiektach spatializer nie może w tej chwili obsługiwane są implementowane jako mixer wtyczek i Wwise obecnie nie zezwala na wiele wtyczek mixer przypisane do jednej mixer aktora.  

## <a name="7-audio-setup-in-unreal"></a>7. Konfiguracja audio w Unreal
* Najpierw musisz wprowadzić swoje gry poziomie, aby utworzyć zasób usługi Akustyka, które zostaną umieszczone w `Content\Acoustics`. Zapoznaj się z [Unreal samouczek tworzenie](unreal-baking.md) i wznowić w tym miejscu. Niektóre wstępnie ramach poziomy znajdują się w pakiecie przykładowych.
* Utwórz Aktor Akustyka miejsca w sceny. Utworzyć tylko jedną Aktorzy na poziomie ponieważ reprezentuje on Akustyka dla całego poziomu. 

    ![Edytor Unreal zrzut ekranu przedstawiający Tworzenie miejsca Akustyka aktora](media/create-acoustics-space.png)

* Teraz można przypisać zasobów w ramach dane akustyczne do gniazda Akustyka danych dla aktora Akustyka miejsca. Scena ma teraz Akustyka!

    ![Zrzut ekranu Unreal edytora s Akustyka howing przypisania zasobów](media/acoustics-asset-assign.png)

* Teraz Dodaj pusty aktora i wykonaj następujące czynności:

    ![Edytor Unreal zrzut ekranu przedstawiający użycie składnika Akustyka pusty aktora](media/acoustics-component-usage.png)

1. Dodawanie składnika Audio Akustyka aktora. Ten składnik rozszerza składnika audio Wwise z funkcjonalnością Akustyka projektu.
2. Gry w polu rozpoczęcia jest sprawdzana przez domyślna, która wyzwoli skojarzone ze zdarzeniem Wwise przy uruchamianiu poziomu.
3. Użyj pola wyboru Pokaż parametry Akustyka do drukowania na ekranie informacje debugowania dotyczące źródła.
    ![Zrzut ekranu Unreal panel Akustyka Edytor źródła dźwięku z wartościami debugowania włączone](media/debug-values.png)
4. Przypisz zdarzenie Wwise na standardowym Wwise przepływu pracy
5. Upewnij się, że używanie dźwięku przestrzennego jest wyłączona. W tej chwili Jeśli używasz Akustyka projektu określonego składnika audio, jednocześnie nie można użyć aparatu przestrzenne Audio firmy Wwise dla Akustyka.

Wszystko jest gotowe. Poruszanie się sceny i Poznaj efekty akustyczny!

## <a name="next-steps"></a>Kolejne kroki
* [Projekt](unreal-workflow.md) samouczek dotyczący Akustyka projektu w Unreal/Wwise
* [Dowiedz się, jak to zrobić, tworzony](unreal-baking.md) dla Twojej gry scen 
