---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: e8daaaf5b6b15eb3095f11e94c707a33b4b18e28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182918"
---
Wybierz **kompilacji**. W wyświetlonym oknie dialogowym Wybierz folder projekt Xcode do eksportowania.

Po zakończeniu eksportu, pojawi się folder, który zawiera wyeksportowane projektu Xcode.

> [!NOTE]
> Jeśli zostanie wyświetlone okno z pytaniem, czy chcesz zastąpić obiektów blob lub uzupełnialnych, firma Microsoft zaleca wybranie **Append** ponieważ jest szybszy. Należy tylko należy wybrać **Zastąp** Jeśli zmieniasz zasoby w sceny. (Na przykład, jeśli w przypadku dodawania, usuwania, zmieniania relacje nadrzędne/podrzędne lub w przypadku dodawania, usuwania lub zmiany właściwości.) Jeśli wprowadzasz tylko zmiany kodu źródłowego, **Append** powinny wystarczyć.

### <a name="open-the-xcode-project"></a>Otwieranie projektu Xcode

W folderze projektu Xcode wyeksportowanego Uruchom to polecenie w terminalu, aby zainstalować zasobniki CocoaPods niezbędne dla projektu:

```bash
pod install --repo-update
```

Teraz możesz otworzyć `Unity-iPhone.xcworkspace` otworzyć projektu w środowisku Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Jeśli widzisz `library not found for -lPods-Unity-iPhone` błąd, prawdopodobnie otwarciu `.xcodeproj` pliku zamiast `.xcworkspace` pliku. 

Wybierz katalog główny **Unity iPhone** węzeł, aby wyświetlić ustawienia projektu, a następnie wybierz **ogólne** kartę.

W obszarze **podpisywanie**, upewnij się, że **automatycznie zarządzała podpisywania** jest włączona. Jeśli nie jest, należy ją włączyć, a następnie wybierz **włączyć automatyczne** w oknie dialogowym, który wygląda jak zresetować ustawienia kompilacji.

W obszarze **Deployment Info (Informacje o wdrożeniu)** upewnij się, że w polu **Deployment Target (Cel wdrożenia)** ustawiono wartość `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)** .

![Wdrażanie i uruchamianie](./media/spatial-anchors-unity/deploy-run.png)