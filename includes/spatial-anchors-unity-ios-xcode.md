---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 6768b1b8e0f5d7d3644779268025551c4e1aef9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964236"
---
Wybierz pozycję **Build (Kompilacja)**, aby otworzyć okno dialogowe. Następnie wybierz folder, aby wyeksportować projekt środowiska Xcode.

Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt Xcode.

> [!NOTE]
> Jeśli okno dialogowe pojawia się okienko z prośbą o chęć **Zastąp** lub **Append**, **dołączania** jest to zalecane, ponieważ jest ono szybciej. Należy jedynie wykonać **Zastąp** Jeśli zmieniasz zasoby w sceny (Dodawanie, usuwanie, zmiana relacji nadrzędny/podrzędny, dodawanie/usuwanie/zmiana właściwości itp). Jeśli wykonujesz tylko zmiany kodu źródłowego, **Append** powinny wystarczyć.

### <a name="open-the-xcode-project"></a>Otwieranie projektu Xcode

W folderze projektu Xcode wyeksportowanego uruchom następujące polecenie w terminalu, aby zainstalować zasobniki CocoaPods niezbędne dla projektu:

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

W obszarze **podpisywanie**, sprawdź, czy **automatycznie zarządzała podpisywania** jest włączona. Jeśli tak nie jest, Aktywuj ją i wybierz **włączyć automatyczne** w oknie dialogowym można zresetować ustawienia kompilacji.

W obszarze **Deployment Info (Informacje o wdrożeniu)** upewnij się, że w polu **Deployment Target (Cel wdrożenia)** ustawiono wartość `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

![Wdrażanie i uruchamianie](./media/spatial-anchors-unity/deploy-run.png)