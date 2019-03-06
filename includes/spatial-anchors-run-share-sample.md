---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753521"
---
## <a name="open-the-sample-project-in-unity"></a>Otwieranie przykładowego projektu w aparacie Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Konfigurowanie dla urządzenia z systemem Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Konfigurowanie dla urządzenia z systemem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchorsPlugin/Examples` i otwórz plik sceny `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Dodatkowo w okienku **Inspector (Inspektor)** wprowadź wartość `Sharing Anchors Service url` (z wdrożenia aplikacji internetowej platformy ASP.NET na platformie Azure) jako wartość `Base Sharing Url`, zastępując wartość `index.html` wartością `api/anchors`. Powinno to wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors`.

Zapisz scenę, wybierając pozycje **File (Plik)** -> **Save (Zapisz)**.

## <a name="to-deploy-to-an-android-device"></a>Wdrażanie na urządzeniu z systemem Android

Włącz urządzenie z systemem Android, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** -> **Build Settings (Ustawienia kompilacji)**.

W obszarze **Scenes in Build (Sceny w kompilacji)** zaznacz pole wyboru obok sceny `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` i wyczyść pola wyboru przy wszystkich innych scenach.

Upewnij się, że pole wyboru **Export Project (Eksportuj projekt)** nie jest zaznaczone. Kliknij pozycję **Build And Run (Skompiluj i uruchom)**. Zobaczysz pytanie o zapisanie pliku `.apk` — możesz wybrać dla niego dowolną nazwę.

Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać opcję **Create & Share Anchor (Utwórz i udostępnij kotwicę)** lub **Locate Shared Anchor (Zlokalizuj udostępnioną kotwicę)**. Pierwsza opcja pozwala utworzyć kotwicę, którą można później znaleźć na tym samym lub innym urządzeniu. Druga opcja, jeśli wcześniej uruchomiono aplikację (na tym samym lub innym urządzeniu), umożliwia zlokalizowanie wcześniej udostępnionych kotwic.

## <a name="to-deploy-to-an-ios-device"></a>Wdrażanie na urządzeniu z systemem iOS

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** -> **Build Settings (Ustawienia kompilacji)**.

W obszarze **Scenes in Build (Sceny w kompilacji)** zaznacz pole wyboru obok sceny `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` i wyczyść pola wyboru przy wszystkich innych scenach.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać opcję **Create & Share Anchor (Utwórz i udostępnij kotwicę)** lub **Locate Shared Anchor (Zlokalizuj udostępnioną kotwicę)**. Pierwsza opcja pozwala utworzyć kotwicę, którą można później znaleźć na tym samym lub innym urządzeniu. Druga opcja, jeśli wcześniej uruchomiono aplikację (na tym samym lub innym urządzeniu), umożliwia zlokalizowanie wcześniej udostępnionych kotwic.

W programie Xcode zatrzymaj aplikację, naciskając pozycję **Stop**.
