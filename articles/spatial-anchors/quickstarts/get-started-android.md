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
ms.openlocfilehash: 003e9beb3a319802754397efae3b636f51de9e52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234390"
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

- Maszyna z systemem Windows lub macOS i programem <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 lub nowszym</a>.
  - Jeśli w systemie Windows, należy także <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a>.
  - Jeśli uruchomiona w systemie macOS, skorzystaj z usługi Git, zainstalowanych za pomocą oprogramowania HomeBrew. Wprowadź następujące polecenie w jednym wierszu w terminalu: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Następnie uruchom `brew install git`.
  - Aby skompilować przykład zestawu NDK, musisz też zainstalować zestaw NDK i narzędzia CMake 3.6 SDK Tools w programie Android Studio.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.
- ARCore 1.7 musi być przeznaczony dla twojej aplikacji.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Jeśli kompilujesz przykład zestawu Android NDK, musisz pobrać plik `arcore_c_api.h` z [tego miejsca](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.7.0/libraries/include/arcore_c_api.h) i umieścić go w katalogu `Android\NDK\libraries\include`.

Otwórz program Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Wybierz opcję **Open an existing Android Studio project (Otwórz istniejący projekt Android Studio)** i wybierz projekt znajdujący się w katalogu `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Wybierz opcję **Open an existing Android Studio project (Otwórz istniejący projekt Android Studio)** i wybierz projekt znajdujący się w katalogu `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest, aby skonfigurować aplikację do używania Twojego identyfikatora konta i klucz konta. Skopiowane do edytora tekstu po [Definiowanie zasobów przestrzenne kotwic](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Otwórz plik `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Otwórz plik `Android/NDK/app/src/main/cpp/spatial_services_application.cc`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

***

## <a name="deploy-the-app-to-your-android-device"></a>Wdrażanie aplikacji na urządzeniu z systemem Android

Włącz urządzenie z systemem Android, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB.

Wybierz polecenie **Run (Uruchom)** na pasku narzędzi programu Android Studio.

![Wdrażanie i uruchamianie w programie Android Studio](./media/get-started-android/android-studio-deploy-run.png)

Wybierz urządzenie z systemem Android w oknie dialogowym **Select Deployment Target (Wybierz miejsce docelowe wdrożenia)**, a następnie wybierz przycisk **OK**, aby uruchomić aplikację na urządzeniu z systemem Android.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

Zatrzymaj aplikację, wybierając polecenie **Stop (Zatrzymaj)** na pasku narzędzi programu Android Studio.

![Zatrzymywanie w programie Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
