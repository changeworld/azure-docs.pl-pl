---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752728"
---
Otwórz aparat Unity i otwórz projekt w folderze `Unity`.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** -> **Build Settings (Ustawienia kompilacji)**.

W sekcji **Platform (Platforma)** wybierz pozycję **Android**. Następnie zmień wartość pola **Build System (System kompilacji)** na **Gradle** i zaznacz pole wyboru **Export Project (Eksportuj projekt)**.

Wybierz pozycję **Switch Platform (Przełącz platformę)**, aby zmienić platformę na **Android**. Aparat Unity może poprosić o zainstalowanie składników obsługi systemu Android, jeśli ich brakuje.

![Ustawienia kompilacji aparatu Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)**.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Pobierz i zaimportuj zestaw ARCore SDK dla aparatu Unity

Pobierz plik `unitypackage` z sekcji [wersji zestawu ARCore SDK dla aparatu Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). W projekcie aparatu Unity wybierz pozycję **Assets (Zasoby)** -> **Import Package (Importuj pakiet)** -> **Custom Package... (Pakiet niestandardowy...)**, a następnie wybierz pobrany wcześniej plik `unitypackage`. W oknie dialogowym **Import Unity Package (Importowanie pakietu aparatu Unity)** upewnij się, że wybrane są wszystkie pliki, a następnie wybierz pozycję **Import (Importuj)**.
