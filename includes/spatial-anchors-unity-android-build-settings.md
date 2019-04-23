---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012126"
---
Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W sekcji **Platform (Platforma)** wybierz pozycję **Android**. Zmiana **System kompilacji** do **Gradle** i wybierz **Eksportowanie projektu**.

Wybierz pozycję **Switch Platform (Przełącz platformę)**, aby zmienić platformę na **Android**. Unity może spowodować wyświetlenie monitu do zainstalowania składników obsługi systemu Android, jeśli są one Brak.

![Okno ustawień kompilacji platformy Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)**.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Pobierz i zaimportuj zestaw ARCore SDK dla aparatu Unity

Pobierz `unitypackage` plik wchodzącej w skład [zwalnia ARCore SDK dla środowiska Unity w wersji 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). W projekcie Unity, wybierz **zasoby** > **Importuj pakiet** > **niestandardowy pakiet** , a następnie wybierz `unitypackage` plik wcześniej pobrane. W **Importowanie pakietu Unity** okno dialogowe, upewnij się, że zaznaczone są wszystkie pliki, a następnie wybierz pozycję **importu**.
