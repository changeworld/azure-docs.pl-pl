---
title: Wdrażanie próbki Unity w hololens
description: Szybki start, który pokazuje, jak pobrać próbkę Unity na HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679737"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Szybki start: wdrażanie próbki Unity w hololens

Ten przewodnik Szybki start obejmuje sposób wdrażania i uruchamiania przykładowej aplikacji szybki start dla unity do HoloLens 2.

W tym przewodniku Szybki start dowiesz się, jak:

> [!div class="checklist"]
>
>* Tworzenie przykładowej aplikacji szybki start dla hololens
>* Wdrażanie próbki na urządzeniu
>* Uruchamianie próbki na urządzeniu

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start wdrożymy przykładowy projekt z [programu Szybki start: Renderowanie modelu z unity](render-model.md).

Upewnij się, że poświadczenia są poprawnie zapisane ze sceną i można połączyć się z sesją z poziomu edytora Unity.

## <a name="build-the-sample-project"></a>Tworzenie przykładowego projektu

1. Otwórz *ustawienia kompilacji > pliku*.
1. Zmień *platformę* na **uniwersalną platformę systemu Windows**
1. Ustawianie *urządzenia docelowego* na **HoloLens**
1. Ustawianie *architektury* na **ARM64**
1. Ustawianie *ustawień kompilacji* **projektu D3D na Konstruuj kompilację kompilacji kompilacji kompilacji kompilacji kompilacji kompilacji kompilacji kompilacji** ![](./media/unity-build-settings.png)
1. Wybierz **przełącz na platformę**
1. Po **naciśnięciu przycisku Build** (lub "Build And Run" zostaniesz poproszony o wybranie folderu, w którym rozwiązanie powinno być przechowywane
1. Otwieranie wygenerowanego pliku **Szybki start.sln** w programie Visual Studio
1. Zmienianie konfiguracji na **Release** i **ARM64**
1. Przełączanie trybu debugera na konfigurację rozwiązania **maszyny zdalnej** ![](media/unity-deploy-config.png)
1. Tworzenie rozwiązania (F7)
1. W przypadku projektu "Szybki start" przejdź do *właściwości > debugowanie*
    1. Upewnij się, że *wersja* konfiguracji jest aktywna
    1. Ustaw *debuger do uruchomienia* na komputerze **zdalnym**
    1. Zmień *nazwę maszyny* na adres IP urządzenia **HoleLens**

## <a name="launch-the-sample-project"></a>Uruchamianie przykładowego projektu

1. Podłącz urządzenie HoloLens za pomocą kabla USB do komputera.
1. Uruchom debugera w programie Visual Studio (F5). Zostanie ona automatycznie wdrożona aplikacji na urządzeniu.

Przykładowa aplikacja powinna zostać uruchomiona, a następnie rozpocząć nową sesję. Po pewnym czasie sesja jest gotowa, a zdalnie renderowany model pojawi się przed tobą.
Jeśli chcesz uruchomić próbkę po raz drugi później, możesz również znaleźć ją w menu Start HoloLens teraz.

## <a name="next-steps"></a>Następne kroki

W następnym przewodniku Szybki start przyjrzymy się konwersji modelu niestandardowego.

> [!div class="nextstepaction"]
> [Szybki start: konwertowanie modelu do renderowania](convert-model.md)
