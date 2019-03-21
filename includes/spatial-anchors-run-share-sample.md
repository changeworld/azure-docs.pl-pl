---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305159"
---
## <a name="set-up-your-device"></a>Konfigurowanie urządzenia

### <a name="set-up-an-android-device"></a>Konfigurowanie urządzenia z systemem Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurowanie urządzeniu z systemem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Skonfiguruj identyfikator konta i klucz

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchorsPlugin/Examples` i otwórz plik sceny `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

W **Inspektor** okienku, wprowadź `Sharing Anchors Service url` (z poziomu aplikacji sieci web platformy ASP.NET wdrażania na platformie Azure) jako wartość pozycji `Base Sharing Url`, zastępując ciąg `index.html` z `api/anchors`. Powinien wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors`.

Zapisz scenę, wybierając pozycje **File (Plik)** > **Save (Zapisz)**.

## <a name="to-deploy-the-app-to-an-android-device"></a>Aby wdrożyć aplikację na urządzeniu z systemem Android

Zaloguj się na urządzeniu z systemem Android i podłącz go do komputera za pomocą kabla USB.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **sceny w kompilacji**, zaznacz pole wyboru obok pozycji `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen i wyczyść wyboru oznacza od wszystkich innych sceny.

Upewnij się, że **Eksportowanie projektu** nie ma znacznik wyboru. Wybierz **kompilowanie i uruchamianie**. Zostanie wyświetlony monit, aby zapisać swoje `.apk` pliku. Możesz wybrać dowolną nazwę.

Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać **Utwórz i Udostępnij zakotwiczenia** lub **Znajdź udostępniony zakotwiczenia**. Pierwsza opcja umożliwia tworzenie kotwicę, która może znajdować się na później w tym samym urządzeniu lub na inną. Druga opcja, jeśli została już uruchomiono aplikację, na tym samym urządzeniu lub na inną, umożliwia umieszczanie kotwic wcześniej udostępniony.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Aby wdrożyć aplikację na urządzeniu z systemem iOS

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **sceny w kompilacji**, zaznacz pole wyboru obok pozycji `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen i wyczyść wyboru oznacza od wszystkich innych sceny.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać **Utwórz i Udostępnij zakotwiczenia** lub **Znajdź udostępniony zakotwiczenia**. Pierwsza opcja umożliwia tworzenie kotwicę, która może znajdować się na później w tym samym urządzeniu lub na inną. Druga opcja, jeśli została już uruchomiono aplikację, na tym samym urządzeniu lub na inną, umożliwia umieszczanie kotwic wcześniej udostępniony.

W programie Xcode, Zatrzymaj aplikację, wybierając **zatrzymać**.
