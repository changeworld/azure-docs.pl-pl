---
title: Dźwięk — Przewodnik Szybki Start z Unreal
titlesuffix: Azure Cognitive Services
description: Korzystaj z przykładowej zawartości, aby eksperymentować z kontrolkami projektu w programie Unreal i Wwise i wdrażać je na pulpicie systemu Windows.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242921"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Akustyczny projekt Unreal/Wwise — Szybki Start
W tym przewodniku szybki start Poeksperymentujesz z kontrolkami projektu akustycznego projektu przy użyciu przykładowej zawartości dla aparatu Unreal i Wwise.

Wymagania programowe dotyczące korzystania z przykładowej zawartości:
* [Aparat Unreal](https://www.unrealengine.com/) 4,22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Pobierz pakiet przykładowy
Pobierz [pakiet do próbkowania akustycznego Unreal i Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Przykładowy pakiet zawiera:
- Projekt aparatu Unreal
- Projekt Wwise dla projektu Unreal
- Wtyczka Wwisea w projekcie

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurowanie przykładowego projektu akustycznego projektu
Najpierw zainstaluj wtyczkę akustyczną projektu w Wwise. Następnie wdróż pliki binarne Wwise w projekcie Unreal. Następnie dostosuj wtyczkę Wwise Unreal, aby obsługiwać ustawienia akustyczne projektu.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Instalowanie wtyczki Wwisei projektu
Otwórz Wwise. Na karcie **wtyczki** w obszarze **Instaluj nowe wtyczki**wybierz pozycję **Dodaj z katalogu**. Wybierz katalog *AcousticsWwisePlugin\ProjectAcoustics* , który został dołączony do pobranego pakietu.

![Opcja instalacji wtyczki Wwise w oknie uruchamiania programu Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Dodawanie plików binarnych Wwise do projektu Unreal przykładowy projekt
1. W obszarze uruchamiania Wwise wybierz kartę **aparat Unreal** . 
1. Wybierz menu "Hamburger" (ikona) obok pozycji **ostatnie projekty aparatu Unreal**, a następnie wybierz polecenie **Przeglądaj w poszukiwaniu projektu**. Otwórz plik Sample Unreal Project *. uproject* w pakiecie *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Karta Unreal w oknie uruchamiania programu Wwise](media/wwise-unreal-tab.png)

3. Obok projektu przykładowy projekt akustyczny wybierz opcję **Integruj Wwise w projekcie**.

   ![W obszarze uruchamiania Wwise jest wyświetlany projekt gry akustyczne Unreal z wyróżnioną opcją integracji.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozszerzona funkcjonalność wtyczki Wwise Unreal
Wtyczka Unreal akustyczna projektu wymaga dodatkowego zachowania uwidocznionego na podstawie interfejsu API dodatku plug-in Wwise Unreal. Uruchom plik wsadowy, który został dostarczony z wtyczką Unrealą projektu, aby zautomatyzować te zmiany.
* W *AcousticsGame\Plugins\ProjectAcoustics\Resources*, uruchom *PatchWwise. bat*.

    ![Skrypt służący do poprawek projektu Wwise wyróżniony w oknie Eksploratora Windows](media/patch-wwise-script.png)

* Jeśli nie masz zainstalowanego zestawu SDK programu DirectX: w zależności od używanej wersji programu Wwise może być konieczne komentowanie wiersza zawierającego `DXSDK_DIR` w *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Edytor kodu przedstawiający komentarz "DXSDK"](media/directx-sdk-comment.png)

* Jeśli kompilujesz za pomocą programu Visual Studio 2019: Aby obejść błąd łączenia z Wwise, ręcznie zmień wartość domyślną *VSVersion* w *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Edytor kodu z VSVersion zmieniony na "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Otwórz projekt Unreal 
Po otwarciu projektu Unreal zostanie wyświetlony monit o ponowne skompilowanie modułów. Wybierz pozycję **tak**.

Jeśli otwarcie projektu zakończy się niepowodzeniem z powodu błędów kompilacji, należy sprawdzić, czy zainstalowano wtyczkę Wwise akustyczną projektu w tej samej wersji Wwise, która była używana w przykładowym projekcie.

W przypadku używania wersji programu [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) starszej niż wersja 2019,1 nie można generować banków dźwiękowych przy użyciu przykładowego projektu akustycznego projektu. Należy zintegrować Wwise w wersji 2019,1 z przykładowym projektem.

## <a name="experiment-with-project-acoustics-design-controls"></a>Eksperymentowanie z kontrolkami projektu
Nasłuchiwanie dźwięku sceny przez wybranie przycisku Odtwórz w edytorze Unreal. Użyj klawiszy W, a, S i D i myszy, aby poruszać się wokół siebie. Aby wyświetlić skróty klawiaturowe dla dodatkowych kontrolek, wybierz F1.

Poniższe informacje opisują niektóre działania projektowe do wypróbowania.

### <a name="modify-occlusion-and-transmission"></a>Modyfikuj zamknięcia i transmisja
Dla każdego aktora dźwiękowego Unreal istnieją kontrolki projektu dla każdego źródła.

![Edytor Unreal ma akustyczne kontrolki projektowe](media/demo-scene-sound-source-design-controls.png)

Jeśli mnożnik **zamknięcia** jest większy niż 1 (wartość domyślna to 1), zamknięcia jest exaggerated. Ustawienie mniejsze niż 1 sprawia, że efekt zamknięcia jest bardziej subtelny.

Aby włączyć transmisję za pośrednictwem ściany, przesuń suwak **transmisja (DB)** do najniższego poziomu.

### <a name="modify-wetness-for-a-source"></a>Modyfikuj wetness dla źródła
Aby zmienić sposób szybkiego wetness zmian z odległością, należy użyć **wypaczenia na odległość Percepcyjna**. Akustyczne projektu oblicza poziomy w całym miejscu przez symulację. Poziomy różnią się bezproblemowo z odległością i zapewniają wskaźniki odległości Percepcyjna. Aby exaggerate ten efekt, zwiększ poziom mokry związany z odległością, aby zwiększyć zakrzywienia odległości. Zniekształcanie wartości mniejszej niż 1 sprawia, że reverberation na odległość zmienia się bardziej subtelne. Możesz również wprowadzić bardziej precyzyjne zmiany w tym efekcie za pomocą ustawienia **wetness (DB)** .

Aby zwiększyć czas zaniku w całym miejscu, Dostosuj **skalę czasu zanikania**. Rozważ przypadek, w którym wynik symulacji to czas zaniku wynoszący 1,5 sekund. Ustawienie **skalowania czasu zaniku** do 2 powoduje, że dla źródła zastosowany zostanie czas zaniku wynoszący 3 sekundy.

### <a name="modify-distance-based-attenuation"></a>Modyfikuj tłumienie na podstawie odległości
Wtyczka z technologią Wwise miksera jest zgodna z rozłożeniami opartymi na odległości, które są wbudowane w Wwise. Zmiana tej krzywej spowoduje zmianę poziomu ścieżki suchej. Wtyczka akustyczna projektu dostosowuje poziom mokry, aby zachować mieszankę mokrą/suchą określoną przez symulację i kontrolki projektowania.

![Panel krzywej tłumienia Wwise, który przedstawia tłumienie z przedziału od 0 przed granicą symulacji](media/demo-sounds-attenuation.png)

W oknie "region symulacji" są używane obliczenia akustyczne projektu, które są wyśrodkowane wokół każdej symulowanej lokalizacji odtwarzacza. Elementy akustyczne w przykładowym pakiecie zostały rozszerzania za pomocą promienia regionu symulacji 45 mierników. Tłumieniey zostały zaprojektowane tak, aby mieściły się w przeliczeniu do 0 przed 45 metrów. Chociaż nie jest to rygorystyczne wymaganie, powoduje to, że tylko geometria w obrębie 45 metrów odbiornika będzie occlude dźwięki.

## <a name="next-steps"></a>Następne kroki
* [Zintegruj wtyczkę akustyczną projektu](unreal-integration.md) z projektem Unreal.
* [Utwórz konto platformy Azure](create-azure-account.md) dla własnego tworzonyu.
