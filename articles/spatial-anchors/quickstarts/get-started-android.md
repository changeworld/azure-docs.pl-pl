---
title: Szybki start — tworzenie aplikacji dla systemu Android przy użyciu usługi Azure Spatial Anchors | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu Android przy użyciu usługi Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 40969362e339770f18374ff22af5b3fe63908e65
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845736"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Szybki start: Tworzenie aplikacji systemu Android przy użyciu usługi Azure Spatial Anchors

W tym przewodniku Szybki start opisano, jak utworzyć aplikację dla systemu Android przy użyciu usługi [Azure Spatial Anchors](../overview.md) w języku Java lub C++/NDK. Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARCore dla systemu Android, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Maszyna z systemem Windows lub macOS z <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - W przypadku uruchomienia w systemie Windows potrzebna jest również <a href="https://git-scm.com/download/win" target="_blank">git dla systemu Windows</a>.
  - W przypadku uruchamiania w systemie macOS Pobierz narzędzie git zainstalowane za pośrednictwem usługi oprogramowania homebrew. Wprowadź następujące polecenie w jednym wierszu terminalu: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Następnie uruchom `brew install git`polecenie.
  - Aby skompilować przykład NDK, należy również zainstalować CMake NDK i 3,6 lub więcej SDK Tools w Android Studio.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.
  - Aby komputer mógł komunikować się z urządzeniem z systemem Android, mogą być wymagane dodatkowe sterowniki urządzeń. Aby uzyskać dodatkowe informacje i instrukcje, zobacz [tutaj](https://developer.android.com/studio/run/device.html) .
- Twoja aplikacja musi być docelowa ARCore **1,8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Pobierz `arcore_c_api.h` z tego [miejsca](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.8.0/libraries/include/arcore_c_api.h) i umieść go `Android\NDK\libraries\include`w.

W ramach nowo sklonowanego repozytorium zainicjuj moduły podrzędne, uruchamiając następujące polecenie:

```console
git submodule update --init --recursive
```

---

Otwórz program Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Wybierz opcję **Open an existing Android Studio project (Otwórz istniejący projekt Android Studio)** i wybierz projekt znajdujący się w katalogu `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Wybierz opcję **Open an existing Android Studio project (Otwórz istniejący projekt Android Studio)** i wybierz projekt znajdujący się w katalogu `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji w taki sposób, aby korzystała z identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Otwórz plik `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Otwórz plik `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

---

## <a name="deploy-the-app-to-your-android-device"></a>Wdrażanie aplikacji na urządzeniu z systemem Android

Włącz urządzenie z systemem Android, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB.

Wybierz polecenie **Run (Uruchom)** na pasku narzędzi programu Android Studio.

![Wdrażanie i uruchamianie w programie Android Studio](./media/get-started-android/android-studio-deploy-run.png)

Wybierz urządzenie z systemem Android w oknie dialogowym **Select Deployment Target (Wybierz miejsce docelowe wdrożenia)** , a następnie wybierz przycisk **OK**, aby uruchomić aplikację na urządzeniu z systemem Android.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

Zatrzymaj aplikację, wybierając polecenie **Stop (Zatrzymaj)** na pasku narzędzi programu Android Studio.

![Zatrzymywanie w programie Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
