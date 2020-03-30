---
title: 'Szybki start: tworzenie aplikacji na Androida'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu Android przy użyciu usługi Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0501c8bb1d71c6cff6033fc937cda019c8890056
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75376464"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Szybki start: tworzenie aplikacji na Androida za pomocą zakotwiczeń przestrzennych platformy Azure

W tym przewodniku Szybki start opisano, jak utworzyć aplikację dla systemu Android przy użyciu usługi [Azure Spatial Anchors](../overview.md) w języku Java lub C++/NDK. Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARCore dla systemu Android, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Komputer z systemem Windows lub macOS z <a href="https://developer.android.com/studio/" target="_blank">systemem Android Studio 3.4+</a>.
  - Jeśli działa w systemie Windows, będziesz również potrzebować <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a> i <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Jeśli działa na macOS, pobierz Git zainstalowany przez HomeBrew. Wprowadź następujące polecenie w jednym wierszu `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`terminala: . Następnie uruchom `brew install git` `brew install git-lfs`i .
  - Aby utworzyć przykład NDK, musisz również zainstalować narzędzia SDK NDK i CMake 3.6 lub nowszych w ucieszeniu w ucieszeniu.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.
  - Aby komputer mógł komunikować się z urządzeniem z Androidem, mogą być wymagane dodatkowe sterowniki urządzeń. Zobacz [tutaj,](https://developer.android.com/studio/run/device.html) aby uzyskać dodatkowe informacje i instrukcje.
- Aplikacja musi być skierowana do arcore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Pobierz `arcore_c_api.h` [stąd](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) i umieść `Android\NDK\libraries\include`go w .

Z poziomu nowo sklonowanego repozytorium inicjuj podmodule, uruchamiając następujące polecenie:

```console
git submodule update --init --recursive
```

---

Otwórz program Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Wybierz opcję **Open an existing Android Studio project (Otwórz istniejący projekt Android Studio)** i wybierz projekt znajdujący się w katalogu `Android/Java/`.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Wybierz opcję **Open an existing Android Studio project (Otwórz istniejący projekt Android Studio)** i wybierz projekt znajdujący się w katalogu `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji do używania identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobu Zakotwiczenia przestrzenne](#create-a-spatial-anchors-resource).

# <a name="java"></a>[Java](#tab/openproject-java)

Otwórz plik `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Otwórz plik `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

---

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
> [Samouczek: Udostępnianie zakotwiczeń przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
