---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 9798e5f76881be38fb27e1f428565caba6e50bf2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135060"
---
Otwórz obszar **Build Settings (Ustawienia kompilacji)** , wybierając pozycje **File (Plik)**  > **Build Settings (Ustawienia kompilacji)** .

W sekcji **Platform (Platforma)** wybierz pozycję **Android**. Zmiana **System kompilacji** do **Gradle** i upewnij się, **Eksportowanie projektu** pole wyboru nie ma znacznik wyboru.

Wybierz pozycję **Switch Platform (Przełącz platformę)** , aby zmienić platformę na **Android**. Unity może spowodować wyświetlenie monitu do zainstalowania składników obsługi systemu Android, jeśli są one Brak.

![Okno ustawień kompilacji platformy Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)** .

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Pobierz i zaimportuj zestaw ARCore SDK dla aparatu Unity

Pobierz `unitypackage` plik wchodzącej w skład [zwalnia ARCore SDK dla środowiska Unity w wersji 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). W projekcie Unity, wybierz **zasoby** > **Importuj pakiet** > **niestandardowy pakiet** , a następnie wybierz `unitypackage` plik wcześniej pobrane. W **Importowanie pakietu Unity** okno dialogowe, upewnij się, że zaznaczone są wszystkie pliki, a następnie wybierz pozycję **importu**.
