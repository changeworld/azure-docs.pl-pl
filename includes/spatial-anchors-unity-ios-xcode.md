---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72933471"
---
Wybierz **pozycję Zbuduj**. W oknie dialogowym, które zostanie otwarte, wybierz folder, do który chcesz wyeksportować projekt Xcode.

Po zakończeniu eksportowania pojawi się folder zawierający eksportowany projekt Xcode.

> [!NOTE]
> Jeśli pojawi się okno z pytaniem, czy chcesz zastąpić lub dołączyć, zalecamy wybranie opcji **Dołącz,** ponieważ jest szybsze. Jeśli zmieniasz zasoby w scenie, musisz wybrać opcję **Zamień.** (Na przykład, jeśli dodajesz, usuwasz lub zmieniasz relacje nadrzędne i podrzędne lub dodajesz, usuwasz lub zmieniasz właściwości). Jeśli wprowadzasz tylko zmiany kodu źródłowego, **dołącz** powinno wystarczyć.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Konwertowanie projektu Xcode na xcworkspace zawierający odwołania do zakotwiczeń przestrzennych platformy Azure

W wyeksportowanym folderze projektu Xcode uruchom to polecenie w terminalu, aby zainstalować niezbędne cocoaPods dla projektu:

```bash
pod install --repo-update
```

Teraz możesz `Unity-iPhone.xcworkspace` otworzyć projekt w Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Zapoznaj się z instrukcjami rozwiązywania [problemów tutaj,](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) jeśli masz problemy z CocoaPod po uaktualnieniu do systemu macOS Catalina (10.15).

Wybierz główny węzeł **Unity-iPhone,** aby wyświetlić ustawienia projektu, a następnie wybierz kartę **Ogólne.**

W obszarze **Podpisywanie**upewnij się, że **funkcja Automatyczne zarządzanie podpisywaniem** jest włączona. Jeśli tak nie jest, włącz go, a następnie wybierz **pozycję Włącz opcję Automatyczna** w oknie dialogowym, które wydaje się resetować ustawienia kompilacji.

W obszarze **Deployment Info (Informacje o wdrożeniu)** upewnij się, że w polu **Deployment Target (Cel wdrożenia)** ustawiono wartość `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

![Wdrażanie i uruchamianie](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Jeśli widzisz `library not found for -lPods-Unity-iPhone` błąd, prawdopodobnie `.xcodeproj` otworzyłeś plik zamiast `.xcworkspace` pliku.
