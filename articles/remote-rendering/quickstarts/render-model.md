---
title: Renderowanie modelu za pomocą unity
description: Szybki start, który prowadzi użytkownika przez kroki renderowania modelu
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b0af45ba4a6b1ca7f9e751af082ff0db80776ec0
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679786"
---
# <a name="quickstart-render-a-model-with-unity"></a>Szybki start: renderowanie modelu za pomocą unity

Ten przewodnik Szybki start obejmuje sposób uruchamiania próbki Unity, która renderuje wbudowany model zdalnie przy użyciu usługi Azure Remote Rendering (ARR).

Nie będziemy wchodzić w szczegóły dotyczące samego interfejsu API ARR ani sposobu konfigurowania nowego projektu Unity. Tematy te są omówione w [tutorialu: Konfigurowanie projektu Unity od podstaw](../tutorials/unity/project-setup.md).

W tym przewodniku Szybki start dowiesz się, jak:
> [!div class="checklist"]
>
>* Konfigurowanie lokalnego środowiska programistycznego
>* Pobieranie i tworzenie przykładowej aplikacji ARR Szybki start dla unity
>* Renderowanie modelu w przykładowej aplikacji Szybki start ARR

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do usługi zdalnego renderowania platformy Azure, należy najpierw [utworzyć konto](../how-tos/create-an-account.md).

Należy zainstalować następujące oprogramowanie:

* Windows SDK 10.0.18362.0 [(do pobrania)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Najnowsza wersja programu Visual Studio 2019 [(do pobrania)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(do pobrania)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(do pobrania)](https://unity3d.com/get-unity/download)
  * Zainstaluj te moduły w unity:
    * **Uwp** — obsługa kompilacji platformy systemu Windows
    * **IL2CPP** - Obsługa kompilacji systemu Windows (IL2CPP)

## <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

Otwórz wiersz polecenia `cmd` (wpisz menu Start systemu Windows) i zmień katalog na katalog, w którym chcesz zapisać przykładowy projekt ARR.

Uruchom następujące polecenia:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Ostatnie polecenie tworzy podkatalog w katalogu ARR zawierający różne przykładowe projekty dla zdalnego renderowania platformy Azure.

Przykładowa aplikacja szybki start dla unity znajduje się w podkatalogu *Unity/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Renderowanie modelu za pomocą przykładowego projektu Unity

Otwórz Unity Hub i dodaj przykładowy projekt, którym jest folder *ARR\azure-remote-rendering\Unity\Quickstart.*
Otwórz projekt. W razie potrzeby należy zezwolić unity uaktualnić projekt do zainstalowanej wersji.

Model domyślny, który renderujemy, to [wbudowany model przykładowy](../samples/sample-model.md). Pokażemy, jak przekonwertować model niestandardowy za pomocą usługi konwersji ARR w [następnym przewodniku Szybki start.](convert-model.md)

### <a name="enter-your-account-info"></a>Wprowadź informacje o koncie

1. W przeglądarce zasobów Unity przejdź do folderu *Sceny* i otwórz scenę **szybkiego startu.**
1. Z *hierarchii*wybierz obiekt gry **RemoteRendering.**
1. W *inspektorze*wprowadź [dane logowania do konta](../how-tos/create-an-account.md).

![Informacje o koncie ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Usługa Azure Portal wyświetla domenę twojego konta tylko w *mixedreality.azure.com*. Jest to niewystarczające do pomyślnego połączenia.
> Ustaw **AccountDomain** `<region>.mixedreality.azure.com`na `<region>` miejsce, w którym znajduje [się jeden z dostępnych regionów w pobliżu.](../reference/regions.md)

Później chcemy wdrożyć ten projekt w urządzeniu HoloLens i połączyć się z usługą zdalnego renderowania z tego urządzenia. Ponieważ nie mamy łatwego sposobu wprowadzania poświadczeń na urządzeniu, przykład szybkiego startu **zapisze poświadczenia w scenie Unity**.

> [!WARNING]
> Upewnij się, że nie sprawdzić projekt z zapisanych poświadczeń w jakimś repozytorium, gdzie wyciek tajnych informacji logowania!

### <a name="create-a-session-and-view-the-default-model"></a>Tworzenie sesji i wyświetlanie modelu domyślnego

Naciśnij przycisk **Odtwórz** jedność, aby rozpocząć sesję. W dolnej części rzutni w panelu *Gra* powinna być widoczna nakładka z tekstem stanu. Sesja zostanie poddana serii przejść stanu. W stanie **Początkowa** zdalna maszyna wirtualna jest obracana, co zajmuje kilka minut. Po sukcesie przechodzi do stanu **Gotowy.** Teraz sesja wchodzi w stan **łączenia,** gdzie próbuje osiągnąć środowisko uruchomieniowe renderowania na tej maszynie wirtualnej. Po pomyślnym, przykładowe przejścia do **connected** stanu. W tym momencie rozpocznie pobieranie modelu do renderowania. Ze względu na rozmiar modelu pobieranie może potrwać jeszcze kilka minut. Następnie pojawi się model zdalnie renderowany.

![Wyjście z próbki](media/arr-sample-output.png)

Gratulacje! Teraz przeglądasz zdalnie renderowany model!

## <a name="inspecting-the-scene"></a>Sprawdzanie sceny

Po uruchomieniu zdalnego połączenia renderowania panel Inspektor aktualizuje dodatkowe informacje o stanie:

![Jedność przykładu gry](./media/arr-sample-configure-session-running.png)

Teraz można eksplorować wykres sceny, wybierając nowy węzeł i klikając pozycję **Pokaż dzieci** w Inspektorze.

![Hierarchia jedności](./media/unity-hierarchy.png)

W scenie znajduje się wycięty obiekt [płaszczyzny.](../overview/features/cut-planes.md) Spróbuj włączyć go w swoich właściwościach i przenieść go wokół:

![Zmiana płaszczyzny cięcia](media/arr-sample-unity-cutplane.png)

Aby zsynchronizować transformacje, kliknij przycisk **Synchronizuj teraz** lub zaznacz opcję **Synchronizuj każdą ramkę.** W przypadku właściwości komponentów wystarczy ich zmiana.

## <a name="next-steps"></a>Następne kroki

W następnym przewodniku Szybki start wdrożymy próbkę w hololens, aby wyświetlić model zdalnie renderowany w oryginalnym rozmiarze.

> [!div class="nextstepaction"]
> [Szybki start: wdrażanie próbki Unity w hololens](deploy-to-hololens.md)

Alternatywnie przykład można również wdrożyć na komputerze stacjonarnym.

> [!div class="nextstepaction"]
> [Szybki start: wdrażanie próbki Unity na pulpicie](deploy-to-desktop.md)