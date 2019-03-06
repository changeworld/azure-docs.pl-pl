---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890937"
---
Wybierz pozycję **Build (Kompilacja)**, aby otworzyć okno dialogowe. Następnie wybierz folder, aby wyeksportować projekt środowiska Xcode.

Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt Xcode.

### <a name="open-the-xcode-project"></a>Otwieranie projektu Xcode

W folderze wyeksportowanego projektu Xcode uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods niezbędne dla projektu:

```bash
pod install --repo-update
```

Teraz możesz otworzyć plik `Unity-iPhone.xcworkspace`, aby wyświetlić projekt w środowisku Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Jeśli widzisz błąd `library not found for -lPods-Unity-iPhone`, prawdopodobnie został otwarty plik z rozszerzeniem `.xcodeproj` zamiast `.xcworkspace`. Otwórz plik `.xcworkspace` i ponów próbę.

Wybierz węzeł główny **Unity iPhone**, aby wyświetlić ustawienia projektu, a następnie wybierz kartę **General (Ogólne)**.

W obszarze **Signing (Podpisywanie)** wybierz pozycję **Automatically manage signing (Automatycznie zarządzaj podpisywaniem)**. W wyświetlonym oknie dialogowym wybierz pozycję **Enable Automatic (Włącz automatyzację)**, aby zresetować ustawienia kompilacji.

W obszarze **Deployment Info (Informacje o wdrożeniu)** upewnij się, że w polu **Deployment Target (Cel wdrożenia)** ustawiono wartość `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

![Wdrażanie i uruchamianie](./media/spatial-anchors-unity/deploy-run.png)