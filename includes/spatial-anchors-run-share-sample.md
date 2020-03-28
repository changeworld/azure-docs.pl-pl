---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882311"
---
## <a name="android"></a>[Android](#tab/Android)

Przykładowy android Java obsługuje udostępnianie na różnych urządzeniach.
Otwórz plik `SharedActivity.java` z folderu przykładów w Android Studio. Wprowadź adres URL uzyskany w poprzednim kroku (z ASP.NET wdrożenia platformy Azure `SharingAnchorsServiceUrl` aplikacji `SharedActivity.java` sieci web) jako wartość w pliku. Zastąp `index.html` w `api/anchors`adresie URL . Powinno to wyglądać `https://<app_name>.azurewebsites.net/api/anchors`tak: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Przykład objective-c systemu iOS obsługuje udostępnianie na różnych urządzeniach.
Otwórz plik `SharedDemoViewController.m` w folderze próbek. Wprowadź adres URL uzyskany w poprzednim kroku (z ASP.NET wdrożenia platformy Azure `SharingAnchorsServiceUrl` aplikacji `SharedActivity.java` sieci web) jako wartość w pliku. Zastąp `index.html` w `api/anchors`adresie URL . Powinno to wyglądać `https://<app_name>.azurewebsites.net/api/anchors`tak: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Przykłady platformy Xamarin z systemem Android i iOS obsługują udostępnianie na różnych urządzeniach.
Otwórz plik `AccountDetails.cs` w folderze próbek. Wprowadź adres URL uzyskany w poprzednim kroku (z ASP.NET wdrożenia platformy Azure `AnchorSharingServiceUrl` aplikacji `SharedActivity.java` sieci web) jako wartość w pliku. Zastąp `index.html` w `api/anchors`adresie URL . Powinno to wyglądać `https://<app_name>.azurewebsites.net/api/anchors`tak: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurowanie urządzenia z systemem Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurowanie urządzenia z systemem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurowanie identyfikatora konta i klucza

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchorsPlugin/Examples` i otwórz plik sceny `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

W okienku **Projekt** przejdź `Assets\AzureSpatialAnchors.Examples\Resources`do pozycji . Wybierz pozycję `SpatialAnchorSamplesConfig`. Następnie w okienku **Inspektor** wprowadź `Sharing Anchors Service url` (z ASP.NET wdrożenia aplikacji sieci Web platformy `Base Sharing Url`Azure) jako wartość , zastępując `index.html` . `api/anchors` Powinno to wyglądać `https://<app_name>.azurewebsites.net/api/anchors`tak: .

Zapisz scenę, wybierając **pozycję Zapisz plik** > **Save**.

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

### <a name="deploy-to-android-device"></a>Wdrażanie na urządzeniu z systemem Android

Zaloguj się na urządzeniu z Androidem i podłącz go do komputera za pomocą kabla USB.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **Sceny w kompilacji**upewnij się, że wszystkie sceny mają obok nich znacznik wyboru.

Upewnij się, że **program Eksportuj** projekt nie ma znacznika wyboru. Wybierz **pozycję Zbuduj i uruchom**. Zostanie wyświetlony monit o zapisanie `.apk` pliku. Możesz wybrać dowolną nazwę dla niego.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Wdrażanie na urządzeniu z systemem iOS

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **Sceny w kompilacji**upewnij się, że wszystkie sceny mają obok nich znacznik wyboru.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

W xcode zatrzymaj aplikację, wybierając pozycję **Zatrzymaj**.
