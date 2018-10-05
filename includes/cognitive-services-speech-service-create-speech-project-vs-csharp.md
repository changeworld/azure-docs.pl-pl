---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454482"
---
1. Uruchom program Visual Studio 2017.
 
1. Upewnij się, że pakiet roboczy **Środowisko pulpitu platformy .NET** jest dostępny. Wybierz pozycję **Narzędzia** \> **Pobierz narzędzia i funkcje** na pasku menu programu Visual Studio, aby otworzyć instalatora programu Visual Studio. Jeśli ten pakiet roboczy jest już włączony, zamknij okno dialogowe. 

    W przeciwnym przypadku zaznacz pole wyboru obok pozycji **Programowanie aplikacji klasycznych dla platformy .NET** i kliknij przycisk **Modyfikuj** w prawym dolnym rogu okna dialogowego. Instalowanie nowej funkcji chwilę potrwa.

    ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Utwórz nową aplikację konsoli w języku Visual C#. W oknie dialogowym **Nowy projekt** w lewym okienku rozwiń pozycję **Zainstalowane** \> **Visual C#** \> **Pulpit Windows**, a następnie wybierz pozycję **Aplikacja konsoli (.NET Framework)**. Jako nazwę projektu podaj *helloworld*.

    ![Utwórz aplikację konsoli w języku Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Utwórz aplikację konsoli w języku Visual C# (.NET Framework)")

1. Instalowanie i przywoływanie [zestawu Speech SDK narzędzia NuGet](https://aka.ms/csspeech/nuget). W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Kliknij prawym przyciskiem myszy pozycję Zarządzaj pakietami NuGet dla rozwiązania](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Zarządzaj pakietami NuGet dla rozwiązania")

1. W prawym górnym rogu wybierz w polu **Źródło pakietu** wartość **nuget.org**. Wyszukaj pakiet `Microsoft.CognitiveServices.Speech` i zainstaluj go w projekcie **helloworld**.

    ![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Instalowanie pakietu NuGet")

1. Zaakceptuj wyświetloną licencję, aby rozpocząć instalowanie pakietu NuGet.

    ![Zaakceptuj licencję](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Zaakceptuj licencję")

    Po zainstalowaniu pakietu zostanie wyświetlone potwierdzenie w konsoli Menedżera pakietów.

1. Utwórz konfigurację platformy pasującą do używanej architektury komputera za pomocą Menedżera konfiguracji. Wybierz pozycję **Kompilacja** > **Menedżer konfiguracji**.

    ![Uruchom Menedżera konfiguracji](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Uruchom Menedżera konfiguracji")

1. W oknie dialogowym **Menedżer konfiguracji** dodaj nową platformę. Z listy rozwijanej **Aktywne platformy rozwiązania** wybierz pozycję **Nowa**.

    ![Dodaj nową platformę w oknie Menedżera konfiguracji](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Dodaj nową platformę w oknie Menedżera konfiguracji")

1. Jeśli korzystasz z 64-bitowego systemu Windows, utwórz nową konfigurację platformy o nazwie `x64`. Jeśli używasz 32-bitowego systemu Windows, utwórz nową konfigurację platformy o nazwie `x86`.

    ![W 64-bitowym systemie Windows dodaj nową platformę o nazwie „x64”](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Dodawanie platformy x64")


