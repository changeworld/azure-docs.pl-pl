---
title: Szybki start akustyki projektu z unreal
titlesuffix: Azure Cognitive Services
description: Przykładowa zawartość służy do eksperymentowania z formantami projektu Project Acoustics w unreal i Wwise oraz wdrażania na pulpicie systemu Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242921"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Szybki start projektu Akustyka Nierealna/Wwise
W tym przewodniku Szybki start będziesz eksperymentować z formanty projektu Akustyka projektu przy użyciu przykładowej zawartości dla Unreal Engine i Wwise.

Wymagania dotyczące oprogramowania dotyczące korzystania z przykładowej zawartości:
* [Silnik nierealny](https://www.unrealengine.com/) 4.22
* [AudioKinetyczny Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Pobierz przykładowy pakiet
Pobierz [przykładowy pakiet Project Acoustics Unreal i Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Przykładowy pakiet zawiera:
- Projekt Unreal Engine
- Projekt Wwise dla projektu Unreal
- Wtyczka Project Acoustics Wwise

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurowanie przykładowego projektu Akustyka projektu
Najpierw zainstaluj wtyczkę Project Acoustics w Wwise. Następnie wdrożyć pliki binarne Wwise do projektu Unreal. Następnie dostosuj wtyczkę Wwise Unreal do obsługi akustyki projektu.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Zainstaluj wtyczkę Project Acoustics Wwise
Otwórz wyrzutnię Wwise. Na karcie **Wtyczki** w obszarze **Instalowanie nowych wtyczek**wybierz pozycję **Dodaj z katalogu**. Wybierz katalog *AcousticsWwisePlugin\ProjectAcoustics,* który został uwzględniony w pobranym pakiecie.

![Możliwość zainstalowania wtyczki Wwise w wiwise Launcher](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Dodawanie plików binarnych Wwise do przykładowego projektu Akustyka projektu
1. W Wwise Launcher wybierz kartę **Silnik nierealny.** 
1. Wybierz menu "hamburger" (ikona) obok pozycji **Ostatnie projekty silnika nierealnego,** a następnie wybierz pozycję **Przeglądaj w poszukiwaniu projektu**. Otwórz przykładowy plik *.uproject* projektu unreal w pakiecie *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Karta Unreal w launcherze Wwise](media/wwise-unreal-tab.png)

3. Obok przykładowego projektu Akustyka projektu wybierz pozycję **Integruj Wwise w programie Project**.

   ![Wwise Launcher pokazuje projekt Acoustics Game Unreal z wyróżnioną opcją Integruj.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozszerzanie funkcji wtyczki Wwise Unreal
Wtyczka Project Acoustics Unreal wymaga dodatkowego zachowania udostępnianego z interfejsu API wtyczki Wwise Unreal. Uruchom plik wsadowy, który został doszła do wtyczki Project Acoustics Unreal, aby zautomatyzować te modyfikacje.
* Wewnątrz *AcousticsGame\Plugins\ProjectAcoustics\Resources*, uruchom *PatchWwise.bat*.

    ![Skrypt do poprawki projektu Wwise wyróżniony w oknie Eksploratora Windows](media/patch-wwise-script.png)

* Jeśli nie masz zainstalowanego SDK DirectX: W zależności od używanej wersji programu Wwise może być `DXSDK_DIR` konieczne skomentowanie wiersza zawierającego w *sekcji AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Edytor kodu pokazujący "DXSDK" skomentował](media/directx-sdk-comment.png)

* Jeśli skompilujesz przy użyciu programu Visual Studio 2019: Aby obejść błąd łączenia z Wwise, ręcznie zmień wartość domyślną *VSVersion* w *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Edytor kodu z VSVersion zmieniono na "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Otwieranie projektu nierealnego 
Po otwarciu projektu Unreal zostanie wyświetlony monit o odbudowanie modułów. Wybierz **pozycję Tak**.

Jeśli otwarcie projektu nie powiedzie się z powodu błędów kompilacji, sprawdź, czy zainstalowano wtyczkę Project Acoustics Wwise do tej samej wersji programu Wwise, która została użyta w przykładowym projekcie Akustyka projektu.

Jeśli używasz wersji [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) wcześniej niż wersja 2019.1, nie możesz wygenerować banków dźwięku przy użyciu przykładowego projektu Project Acoustics. Należy zintegrować wersję Wwise 2019.1 z przykładowym projektem.

## <a name="experiment-with-project-acoustics-design-controls"></a>Eksperymentuj z kontrolkami projektowania akustyki projektu
Posłuchaj, jak brzmi scena, wybierając przycisk odtwarzania w edytorze Unreal. Użyj klawiszy W, A, S i D oraz myszy, aby się poruszać. Aby wyświetlić skróty klawiaturowe dla dodatkowych kontrolek, wybierz pozycję F1.

Poniżej opisano niektóre działania projektowe do wypróbowania.

### <a name="modify-occlusion-and-transmission"></a>Modyfikowanie niedrożności i transmisji
Istnieją formanty projektu Akustyka projektu na źródło dla każdego aktora dźwięku Unreal.

![Elementy sterujące projektem akustyki Edytora Unreal](media/demo-scene-sound-source-design-controls.png)

Jeśli **mnożnik okluzji** jest większy niż 1 (wartość domyślna to 1), niedrożność jest przesadzone. Ustawienie mniejsze niż 1 sprawia, że efekt okluzji jest bardziej subtelny.

Aby włączyć transmisję przez ścianę, odsuń suwak **Transmisja (dB)** od najniższego poziomu.

### <a name="modify-wetness-for-a-source"></a>Modyfikowanie wilgotności dla źródła
Aby zmienić szybkość zmiany wilgotności z odległością, użyj **percepcyjnej wypaczenia odległości**. Projekt Akustyka oblicza mokre poziomy w całej przestrzeni poprzez symulację. Poziomy różnią się płynnie z odległości i zapewniają percepcyjne sygnały odległości. Aby wyolbrzymić ten efekt, zwiększ poziom mokrych związanych z odległością, aby zwiększyć osnowę na odległość. Wartości wypaczeń mniejsze niż 1 sprawiają, że pogłos oparty na odległości zmienia się bardziej subtelnie. Można również dokonać drobniejszych regulacji tego efektu za pomocą ustawienia **Wilgotność (dB).**

Aby wydłużyć czas rozpadu w całej przestrzeni, dostosuj **skalę czasu rozpadu**. Należy wziąć pod uwagę przypadek, w którym wynik symulacji jest czas rozpadu 1,5 sekundy. Ustawienie **skali czasu rozpadu** na 2 powoduje czas rozpadu 3 sekund zastosowany do źródła.

### <a name="modify-distance-based-attenuation"></a>Modyfikowanie tłumienia na podstawie odległości
Wtyczka miksera Project Acoustics Wwise jest zgodna z tłumieniem opartym na odległości na źródło, które jest wbudowane w wwise. Zmiana tej krzywej zmienia poziom suchej ścieżki. Wtyczka Project Acoustics dostosuje poziom mokrej nawierzchni, aby utrzymać mieszankę mokrą/suchą określoną przez symulację i elementy sterujące projektem.

![Panel krzywej tłumienia Wwise pokazujący tłumienie 0 przed granicą symulacji](media/demo-sounds-attenuation.png)

Projekt Akustyka oblicza w polu "region symulacji", który jest wyśrodkowany wokół każdej symulowanej lokalizacji gracza. Zasoby akustyki w opakowaniu próbki zostały upieczone o promieniu regionu symulacji 45 metrów. Tłumienie zostały zaprojektowane do spadku do 0 przed 45 metrów. Chociaż ten spadek nie jest ścisłym wymogiem, niesie ze sobą zastrzeżenie, że tylko geometria w odległości 45 metrów od słuchacza będzie zasłaniać dźwięki.

## <a name="next-steps"></a>Następne kroki
* [Zintegruj wtyczkę Project Acoustics](unreal-integration.md) z projektem Unreal.
* [Utwórz konto platformy Azure](create-azure-account.md) dla własnych wypieków.
