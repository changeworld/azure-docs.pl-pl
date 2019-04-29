---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681388"
---
## <a name="set-up-your-device"></a>Konfigurowanie urządzenia

Na platformie Unity, otwórz projekt w `Unity` folderu.

![Okno aparatu Unity](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Konfigurowanie urządzenia z systemem Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurowanie urządzeniu z systemem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Skonfiguruj identyfikator konta i klucz

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchorsPlugin/Examples` i otwórz plik sceny `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

W **Inspektor** okienku, wprowadź `Sharing Anchors Service url` (z poziomu aplikacji sieci web platformy ASP.NET wdrażania na platformie Azure) jako wartość pozycji `Base Sharing Url`, zastępując ciąg `index.html` z `api/anchors`. Powinien wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors`.

Zapisz scenę, wybierając pozycje **File (Plik)** > **Save (Zapisz)**.

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

### <a name="deploy-to-android-device"></a>Wdrażanie na urządzeniu z systemem Android

Zaloguj się na urządzeniu z systemem Android i podłącz go do komputera za pomocą kabla USB.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **sceny w kompilacji**, zaznacz pole wyboru obok pozycji `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen i wyczyść wyboru oznacza od wszystkich innych sceny.

Upewnij się, że **Eksportowanie projektu** nie ma znacznik wyboru. Wybierz **kompilowanie i uruchamianie**. Zostanie wyświetlony monit, aby zapisać swoje `.apk` pliku. Możesz wybrać dowolną nazwę.

Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać **Utwórz i Udostępnij zakotwiczenia** lub **Znajdź udostępniony zakotwiczenia**. Pierwszy scenariusz umożliwia tworzenie kotwicę, która może znajdować się na później w tym samym urządzeniu lub na inną. Drugi scenariusz, jeśli została już uruchomiono aplikację, na tym samym urządzeniu lub na inną, umożliwia umieszczanie kotwic wcześniej udostępniony. Po pobraniu Twojemu scenariuszowi, aplikacja przeprowadzi Cię z dalszymi instrukcjami wokół co zrobić, aby. Na przykład użytkownik zostanie zapytany przenoszenie urządzenia do zbierania informacji o środowisku. W dalszej można będzie umieścić kotwicy na całym świecie, będzie Zaczekaj, aż przekazuje i tak dalej.

### <a name="deploy-to-an-ios-device"></a>Wdrażanie na urządzeniu z systemem iOS

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **sceny w kompilacji**, zaznacz pole wyboru obok pozycji `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen i wyczyść wyboru oznacza od wszystkich innych sceny.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać **Utwórz i Udostępnij zakotwiczenia** lub **Znajdź udostępniony zakotwiczenia**. Pierwszy scenariusz umożliwia tworzenie kotwicę, która może znajdować się na później w tym samym urządzeniu lub na inną. Drugi scenariusz, jeśli została już uruchomiono aplikację, na tym samym urządzeniu lub na inną, umożliwia umieszczanie kotwic wcześniej udostępniony. Po pobraniu Twojemu scenariuszowi, aplikacja przeprowadzi Cię z dalszymi instrukcjami wokół co zrobić, aby. Na przykład użytkownik zostanie zapytany przenoszenie urządzenia do zbierania informacji o środowisku. W dalszej można będzie umieścić kotwicy na całym świecie, będzie Zaczekaj, aż przekazuje i tak dalej.

W programie Xcode, Zatrzymaj aplikację, wybierając **zatrzymać**.
