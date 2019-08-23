---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904036"
---
## <a name="set-up-your-device-in-unity"></a>Konfigurowanie urządzenia w środowisku Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurowanie urządzenia z systemem Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurowanie urządzenia z systemem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Skonfiguruj identyfikator i klucz konta

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchorsPlugin/Examples` i otwórz plik sceny `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

W okienku **projekt** przejdź do `Assets\AzureSpatialAnchors.Examples\Resources`. Wybierz pozycję `SpatialAnchorSamplesConfig`. Następnie w okienku **Inspektor** `Sharing Anchors Service url` wprowadź wartość `Base Sharing Url` dlaparametru`index.html` (z wdrożenia aplikacji sieci Web ASP.NET Azure), zastępując polecenie. `api/anchors` Powinien wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors`.

Zapisz scenę, wybierając pozycje **File (Plik)**  > **Save (Zapisz)** .

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

### <a name="deploy-to-android-device"></a>Wdrażanie na urządzeniu z systemem Android

Zaloguj się na urządzeniu z systemem Android i połącz je z komputerem przy użyciu kabla USB.

Otwórz obszar **Build Settings (Ustawienia kompilacji)** , wybierając pozycje **File (Plik)**  > **Build Settings (Ustawienia kompilacji)** .

W obszarze **sceny w kompilacji**upewnij się, że wszystkie sceny mają obok nich znacznik wyboru.

Upewnij się, że **projekt eksportu** nie ma znacznika wyboru. Wybierz opcję **Kompiluj i uruchom**. Zostanie wyświetlony monit o zapisanie `.apk` pliku. Możesz wybrać dla niego dowolną nazwę.

Po uruchomieniu aplikacji w oknie dialogowym **Wybierz demonstrację** Użyj strzałek w lewo lub w prawo, aby wybrać opcję **LocalShare** , a następnie naciśnij pozycję **Przejdź!** . Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać opcję **Utwórz zakotwiczenie udostępniania &** lub **zlokalizować zakotwiczenie udostępnione**.

Pierwszy scenariusz pozwala utworzyć kotwicę, która może znajdować się później na tym samym urządzeniu lub na innym.
Drugi scenariusz, jeśli aplikacja została już uruchomiona na tym samym urządzeniu lub w innym, umożliwia zlokalizowanie wcześniej udostępnionych kotwic. Po wybraniu scenariusza aplikacja przeprowadzi Cię przez użytkownika z dalszych instrukcji dotyczących czynności, które należy wykonać. Na przykład użytkownik zostanie poproszony o przeniesienie urządzenia do programu w celu zebrania informacji o środowisku. Później Umieść kotwicę na świecie, poczekaj na jego zapis itd.

### <a name="deploy-to-an-ios-device"></a>Wdrażanie na urządzeniu z systemem iOS

Otwórz obszar **Build Settings (Ustawienia kompilacji)** , wybierając pozycje **File (Plik)**  > **Build Settings (Ustawienia kompilacji)** .

W obszarze **sceny w kompilacji**upewnij się, że wszystkie sceny mają obok nich znacznik wyboru.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Po uruchomieniu aplikacji w oknie dialogowym **Wybierz demonstrację** Użyj strzałek w lewo lub w prawo, aby wybrać opcję **LocalShare** , a następnie naciśnij pozycję **Przejdź!** . Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać opcję **Utwórz zakotwiczenie udostępniania &** lub **zlokalizować zakotwiczenie udostępnione**.

Pierwszy scenariusz pozwala utworzyć kotwicę, która może znajdować się później na tym samym urządzeniu lub na innym.
Drugi scenariusz, jeśli aplikacja została już uruchomiona na tym samym urządzeniu lub w innym, umożliwia zlokalizowanie wcześniej udostępnionych kotwic. Po wybraniu scenariusza aplikacja przeprowadzi Cię przez użytkownika z dalszych instrukcji dotyczących czynności, które należy wykonać. Na przykład użytkownik zostanie poproszony o przeniesienie urządzenia do programu w celu zebrania informacji o środowisku. Później Umieść kotwicę na świecie, poczekaj na jego zapis itd.

W programie Xcode Zatrzymaj aplikację, wybierając pozycję **Zatrzymaj**.
