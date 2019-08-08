---
title: Dźwięk — Przewodnik Szybki Start z Unreal
titlesuffix: Azure Cognitive Services
description: Korzystając z przykładowej zawartości, należy eksperymentować z kontrolkami projektu w Unreal i Wwise i wdrażać je na pulpicie systemu Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8809c80f2ecba4ea2b3e3d280be0c4ad81d78d37
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854853"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Akustyczny projekt Unreal/Wwise — Szybki Start
W tym przewodniku szybki start Poeksperymentujesz z kontrolkami projektu akustycznego projektu przy użyciu dostarczonej zawartości przykładowej dla aparatu Unreal i Wwise.

Wymagania dotyczące oprogramowania:
* [Aparat Unreal](https://www.unrealengine.com/) 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6

## <a name="download-the-sample-package"></a>Pobierz pakiet przykładowy
Pobierz [pakiet "Unreal + Wwise](https://www.microsoft.com/download/details.aspx?id=58090)" w projekcie. Przykładowy pakiet zawiera projekt aparatu Unreal, projekt Wwise dla tego projektu Unreal oraz wtyczkę Wwiseą dla projektu.

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurowanie przykładowego projektu akustycznego projektu
Aby skonfigurować projekt Unreal/Wwise, należy najpierw zainstalować wtyczkę akustyczną projektu w Wwise. Następnie wdróż pliki binarne Wwise w projekcie Unreal i Dostosuj wtyczkę Unreal Wwise, aby obsługiwać ustawienia akustyczne projektu.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Instalowanie wtyczki Wwise w projekcie
Otwórz program uruchamiającego Wwise, a następnie na karcie **wtyczki** w obszarze **Instaluj nowe wtyczki**wybierz pozycję **Dodaj z katalogu**. `AcousticsWwisePlugin\ProjectAcoustics` Wybierz katalog, który został dołączony do pobranego pakietu.

![Zrzut ekranu przedstawiający program Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Dodawanie plików binarnych Wwise do projektu Unreal przykładowy projekt
W obszarze Uruchamianie Wwise kliknij kartę **aparat Unreal** , a następnie kliknij menu Hamburger obok pozycji **ostatnie projekty aparatu Unreal** i wybierz polecenie **Przeglądaj w poszukiwaniu projektu**. Otwórz plik przykładowego projektu `.uproject` Unreal w pakiecie. `AcousticsSample\AcousticsGame\AcousticsGame.uproject`

![Zrzut ekranu karty Wwise Uruchom jako](media/wwise-unreal-tab.png)

Następnie obok przykładowego projektu akustycznego projektu kliknij pozycję **Integruj Wwise w programie Project**.

![Zrzut ekranu z programem Wwise](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Rozszerzona funkcja wtyczki Wwise Unreal
Wtyczka Unreal akustyczna projektu wymaga, aby dodatkowe zachowanie było widoczne z interfejsu API wtyczki Unreal Wwise. Uruchom plik wsadowy z wtyczką Unreal akustyczną projektu w celu zautomatyzowania tych modyfikacji:
* Wewnątrz `AcousticsGame\Plugins\ProjectAcoustics\Resources`, uruchom `PatchWwise.bat`polecenie.

    ![Zrzut ekranu okna Eksploratora Windows przedstawiający skrypt służący do poprawki projektu Wwise](media/patch-wwise-script.png)

* Jeśli nie masz zainstalowanego zestawu SDK programu DirectX, w zależności od używanej wersji programu Wwise może być konieczne komentowanie wiersza zawierającego `DXSDK_DIR` w: `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Zrzut ekranu edytora kodu pokazujący DXSDK z komentarzem](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Otwórz projekt Unreal. 
Zostanie zaproszony o ponowne skompilowanie modułów; Kliknij przycisk Tak.

>Jeśli otwarcie projektu zakończy się niepowodzeniem w przypadku niepowodzeń kompilacji, sprawdź, czy zainstalowano wtyczkę Wwise akustyczną projektu do tej samej wersji Wwise, która jest używana w przykładowym projekcie.

>Jeśli nie korzystasz z [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6, musisz ponownie wygenerować banki dźwięku, zanim dźwięk będzie odtwarzany w przykładowym projekcie.

## <a name="experiment-with-project-acoustics-design-controls"></a>Eksperymentowanie z kontrolkami projektu
Nasłuchiwanie dźwięku sceny przez kliknięcie przycisku Odtwórz w edytorze Unreal. Aby poruszać się na pulpicie, użyj W, a, S, D i myszy. Aby wyświetlić skróty klawiaturowe zapewniające większą liczbę kontrolek, naciśnij klawisz **F1**. Poniżej przedstawiono niektóre czynności projektowe do wypróbowania:

### <a name="modify-occlusion-and-transmission"></a>Modyfikuj zamknięcia i transmisja
Istnieją kontrolki projektowe akustyczne dla każdego źródła na każdy aktor dźwięku Unreal:

![Zrzut ekranu przedstawiający kontrolki projektowania w edytorze Unreal](media/demo-scene-sound-source-design-controls.png)

Jeśli mnożnik **zamknięcia** jest większy niż 1 (wartość domyślna to 1), zamknięcia będzie exaggerated. Ustawienie go mniejszej niż 1 sprawia, że efekt zamknięcia jest bardziej subtelny.

Aby włączyć przekazywanie przez ściany, przesuń suwak **transmisja (DB)** na najniższy poziom. 

### <a name="modify-wetness-for-a-source"></a>Modyfikuj wetness dla źródła
Aby zmienić, jak szybko wetness zmiany z odległości, użyj **wypaczenia odległości Percepcyjna**. Ruch akustyczny projektu oblicza poziomy w całym miejscu od symulacji, które różnią się bezproblemowo i zapewniają podpowiedzi Percepcyjna. Zwiększenie zakrzywienia odległości exaggerates ten efekt przez zwiększenie poziomów mokrych związanych z odległością. Zniekształcanie wartości mniejszej niż 1 sprawia, że reverberation na odległość zmienia się bardziej subtelne. Ten efekt można również dostosować w szczegółowym szczegółach przez dostosowanie **wetness (DB)** .

Zwiększ czas zaniku w całym miejscu, dopasowując **skalę czasu zanikania**. Rozważ przypadek, w którym wynik symulacji to czas zaniku wynoszący 1,5 s. Ustawienie **skali czasu zaniku** na 2 spowoduje zastosowany czas pozostały do źródła 3 s.

### <a name="modify-distance-based-attenuation"></a>Modyfikuj tłumienie na podstawie odległości
Wtyczka Wwisea jest zgodna z rozłożeniami opartymi na odległości, które są wbudowane w Wwise. Zmiana tej krzywej spowoduje zmianę poziomu ścieżki suchej. Wtyczka akustyczna projektu dostosowuje poziom mokry w celu utrzymania mieszanki suchej suchej określonej przez symulację i kontrolki projektowania.

![Zrzut ekranu przedstawiający panel krzywych Wwise z tłumieniem do zera przed granicą symulacji](media/demo-sounds-attenuation.png)

Akustyczny projekt obliczeń w polu "region symulacji" jest wyśrodkowany wokół każdej symulowanej lokalizacji odtwarzacza. Zasoby akustyczne w przykładowym pakiecie zostały rozszerzania za pomocą promienia regionu symulacji 45 m, a tłumienie zostały zaprojektowane tak, aby mieściły się w przeliczeniu do 0 przed 45 m. Chociaż nie jest to rygorystyczne wymaganie, powoduje to, że tylko geometria w obrębie 45 m odbiornika occlude dźwięki.

## <a name="next-steps"></a>Następne kroki
* [Integruj](unreal-integration.md) wtyczkę akustyczną projektu z projektem Unreal
* [Utwórz konto platformy Azure](create-azure-account.md) dla własnych przypadków tworzenia


