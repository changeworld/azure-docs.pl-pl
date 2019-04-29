---
author: erhopf
ms.service: cognitive-services
ms.topic: include
origin.date: 12/12/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 6e49db90aa9e9f933a190425afbafd15e0057fca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621141"
---
1. Uruchom program Visual Studio 2017.

1. Upewnij się, że jest dostępny pakiet roboczy do **tworzenia aplikacji na wiele platform dla platformy .NET**. Wybierz kolejno pozycje **Narzędzia** > **Pobierz narzędzia i funkcje** na pasku menu programu Visual Studio, aby otworzyć instalator programu Visual Studio. Jeśli ten pakiet roboczy jest już włączony, zamknij okno dialogowe.

   ![Zrzut ekranu Instalatora programu Visual Studio z wyróżnioną kartą Pakiety robocze](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   W przeciwnym razie zaznacz pole obok pozycji **Tworzenie aplikacji na wiele platform dla platformy .NET Core** i wybierz opcję **Modyfikuj** w prawym dolnym rogu okna dialogowego. Instalowanie nowej funkcji chwilę potrwa.

1. Utwórz nową aplikację konsolową w języku Visual C# dla platformy .NET Core. W oknie dialogowym **Nowy projekt** w lewym okienku rozwiń pozycje **Zainstalowane** > **Visual C#** > **.NET Core**. Następnie wybierz pozycję **Aplikacja konsolowa (.NET Core)**. Jako nazwę projektu podaj *helloworld*.

   ![Zrzut ekranu okna dialogowego Nowy projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Tworzenie aplikacji konsolowej w języku Visual C# (dla platformy .NET Core)")

1. Instalowanie [zestawu Speech SDK pakietu NuGet](https://aka.ms/csspeech/nuget) i odwoływanie się do niego. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

   ![Zrzut ekranu Eksploratora rozwiązań z wyróżnioną opcją Zarządzaj pakietami NuGet rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Zarządzaj pakietami NuGet rozwiązania")

1. W prawym górnym rogu wybierz w polu **Źródło pakietu** wartość **nuget.org**. Wyszukaj pakiet `Microsoft.CognitiveServices.Speech` i zainstaluj go w projekcie **helloworld**.

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instaluj pakiet NuGet")

1. Zaakceptuj wyświetloną licencję, aby rozpocząć instalowanie pakietu NuGet.

   ![Zrzut ekranu okna dialogowego Akceptacja licencji](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Akceptacja licencji")

Po zainstalowaniu pakietu w konsoli Menedżera pakietów zostanie wyświetlone potwierdzenie.
