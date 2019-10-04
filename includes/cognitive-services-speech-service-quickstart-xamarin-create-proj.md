---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837441"
---
Aby utworzyć projekt programu Visual Studio na potrzeby programowania aplikacji mobilnych dla wielu platform w środowisku Xamarin, musisz skonfigurować opcje programistyczne programu Visual Studio, utworzyć projekt, wybrać architekturę docelową i zainstalować zestaw Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji programistycznych programu Visual Studio

Aby rozpocząć, upewnij się, że program Visual Studio jest poprawnie skonfigurowany do tworzenia aplikacji mobilnych na wiele platform za pomocą platformy .NET:

1. Otwórz program Visual Studio 2019.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **narzędzia** > **Pobierz narzędzia i funkcje** , aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie** .

   ![Karta obciążenia, modyfikowanie okna dialogowego, Instalator programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na karcie **obciążenia** w obszarze **systemu Windows**Znajdź **Programowanie aplikacji mobilnych za pomocą platformy .NET** . Jeśli pole wyboru obok tego obciążenia jest już zaznaczone, Zamknij okno dialogowe **Modyfikowanie** i przejdź do kroku 5.

1. Zaznacz pole wyboru **Programowanie aplikacji mobilnych przy użyciu platformy .NET** , wybierz pozycję **Modyfikuj**, a następnie w oknie dialogowym **przed** rozpoczęciem wybierz pozycję **Kontynuuj** , aby zainstalować Programowanie aplikacji mobilnych za pomocą obciążenia .NET. Instalacja nowej funkcji może trochę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project"></a>Utwórz projekt

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **plik** > **Nowy** > **projekt** , aby wyświetlić okno **Utwórz nowy projekt** .

   ![Tworzenie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Znajdź i wybierz pozycję **aplikacja mobilna (Formularze platformy Xamarin)** .

1. Wybierz pozycję **dalej** , aby wyświetlić ekran **Konfiguruj nowy projekt** . 

   ![Konfigurowanie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. W polu **Nazwa projektu**wprowadź `helloworld`.

1. W obszarze **Lokalizacja**przejdź do, a następnie wybierz lub Utwórz folder, w którym ma zostać zapisany projekt.

1. Wybierz pozycję **Utwórz** , aby przejść do **nowego okna projektu aplikacji mobilnej platformy Xamarin** .

   ![Okno dialogowe Nowy projekt platforma uniwersalna systemu Windows — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Wybierz **pusty** szablon

1. Na **platformie**zaznacz pola wyboru dla systemów **Android**, **iOS** i **Windows (platformy UWP)** .

1. Wybierz **przycisk OK**. Nastąpi powrót do środowiska IDE programu Visual Studio z nowym projektem utworzonym i widocznym w okienku **Eksplorator rozwiązań** .

   ![projekt HelloWorld — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Teraz wybierz architekturę platformy docelowej i projekt startowy. Na pasku narzędzi programu Visual Studio Znajdź pole listy rozwijanej **platformy rozwiązania** . (Jeśli go nie widzisz, wybierz pozycję **wyświetl** > **paski narzędzi** > **Standard** , aby wyświetlić pasek narzędzi zawierający **platformy rozwiązań**). Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64** w polu listy rozwijanej. 64 — bit systemu Windows może również uruchamiać aplikacje 32-bitowe, więc możesz wybrać **x86** , jeśli wolisz. W polu listy rozwijanej **projekty startowe** Ustaw HelloWorld. PLATFORMY UWP (uniwersalny system Windows).

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu Speech SDK

Zainstaluj [pakiet NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget)i odwołuje się do zestawu Speech SDK w projekcie:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania** , aby przejść do okna **rozwiązania NuGet** .

1. Wybierz pozycję **Przeglądaj**.

   ![Zrzut ekranu przedstawiający okno dialogowe Zarządzanie pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. W obszarze **Źródło pakietu**wybierz pozycję **NuGet.org**.

1. W polu **wyszukiwania** wprowadź `Microsoft.CognitiveServices.Speech`, a następnie wybierz ten pakiet po pojawieniu się w wynikach wyszukiwania.

   ![Zrzut ekranu przedstawiający okno dialogowe Zarządzanie pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Uwaga: Biblioteka systemu iOS w programie Microsoft. CognitiveServices. Speech NuGet nie ma włączonej kodu bitowego. Jeśli potrzebujesz biblioteki kodu bitowego Enabled dla aplikacji, użyj programu Microsoft. CognitiveServices. Speech. Xamarin. iOS NuGet dla projektu systemu iOS.

1. W okienku stan pakietu obok pozycji wyniki wyszukiwania wybierz pozycję Wszystkie projekty. **HelloWorld**, **HelloWorld. Android**, **HelloWorld. iOS** i **HelloWorld. PLATFORMY UWP**.

1. Wybierz pozycję **Zainstaluj**.

1. W oknie dialogowym **Podgląd zmian** wybierz **przycisk OK**.

1. W oknie dialogowym **Akceptacja licencji** Wyświetl licencję, a następnie wybierz pozycję **Akceptuję** i zainstaluj odwołanie do pakietu SDK mowy do wszystkich projektów. Po pomyślnym zakończeniu instalacji może pojawić się następujące ostrzeżenie dla HelloWorld. iOS. Jest to znany problem i nie ma wpływu na funkcjonalność aplikacji.

> Nie można rozpoznać odwołania "C:\Users\Default @ no__t-0nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Jeśli to odwołanie jest wymagane przez kod, mogą wystąpić błędy kompilacji.
