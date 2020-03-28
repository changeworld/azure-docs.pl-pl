---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72675627"
---
Aby utworzyć projekt programu Visual Studio dla wieloplatformowej aplikacji mobilnej .NET rozwoju z xamarin, należy:
- Konfigurowanie opcji programowania programu Visual Studio.
- Utwórz projekt i wybierz architekturę docelową. 
- Zainstaluj pakiet SDK mowy.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji tworzenia programu Visual Studio

Aby rozpocząć, upewnij się, że poprawnie skonfigurowane w programie Visual Studio dla rozwoju mobilnego między platformami z .NET:

1. Otwórz program Visual Studio 2019.

1. Na pasku menu programu Visual Studio wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje,** aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie.**

   ![Karta Obciążenia, okno dialogowe Modyfikowanie, Instalator programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na karcie **Obciążenia w** obszarze **Windows**znajdź program Mobile development z obciążeniem **platformy .NET.** Jeśli pole wyboru obok tego obciążenia jest już zaznaczone, zamknij okno dialogowe **Modyfikowanie** i przejdź do kroku 5.

1. Zaznacz pole wyboru **Mobile development with .NET** i wybierz pozycję **Modyfikuj**. W oknie dialogowym **Przed rozpoczęciem wybierz** pozycję **Kontynuuj,** aby zainstalować programowanie mobilne z obciążeniem platformy .NET. Instalacja nowej funkcji może trochę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project"></a>Tworzenie projektu

1. Na pasku menu programu Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu,** aby wyświetlić okno **Utwórz nowy projekt.**

   ![Tworzenie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Znajdź i wybierz **aplikację mobilną (Xamarin.Forms)**.

1. Wybierz **przycisk Dalej,** aby wyświetlić ekran **Konfigurowanie nowego projektu.**

   ![Konfigurowanie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. W **nazwie projektu**wprowadź *helloworld*.

1. W **obszarze Lokalizacja**przejdź do folderu i wybierz go, aby zapisać projekt.

1. Wybierz **pozycję Utwórz,** aby przejść do okna **Projekt nowej aplikacji mobilnej Xamarin Forms.**

   ![Nowe okno dialogowe Projektu platformy systemu Uniwersalnego systemu Windows — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Wybierz **pusty** szablon.

1. W **obszarze Platforma**wybierz pola dla systemów **Android**, **iOS**i **Windows (UWP).**

1. Kliknij przycisk **OK**. Powrót do ide programu Visual Studio, z nowym projektem utworzony i widoczne w **eksploratorze rozwiązań** okienka.

   ![Projekt helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Teraz wybierz architekturę platformy docelowej i projekt startowy. Na pasku narzędzi programu Visual Studio znajdź pole listy rozwijanej **Platformy rozwiązań.** (Jeśli go nie widzisz, wybierz opcję **Wyświetl** > **paski narzędzi Standardowy,** > **Standard** aby wyświetlić pasek narzędzi **zawierający platformy rozwiązań).** Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64** w polu rozwijany. Jeśli chcesz, możesz wybrać **x86,** ponieważ 64-bitowy system Windows może również uruchamiać aplikacje 32-bitowe. W polu rozwijany **Projekty startowe** ustaw **helloworld. UwP (Universal Windows)**.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zainstaluj [pakiet NuGet zestawu SDK mowy](https://aka.ms/csspeech/nuget)i odwołaj się do zestawu SDK mowy w projekcie.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie. Wybierz **pozycję Zarządzaj pakietami NuGet dla rozwiązania,** aby przejść do okna **NuGet - Rozwiązanie.**

1. Wybierz pozycję **Przeglądaj**.

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. W **źródle pakietu**wybierz nuget.org.

1. W polu **Wyszukiwania** wprowadź plik *Microsoft.CognitiveServices.Speech*. Następnie wybierz ten pakiet po jego wyświetleniu w wynikach wyszukiwania.

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > Biblioteka systemu `Microsoft.CognitiveServices.Speech` iOS wewnątrz NuGet nie ma włączonego kodu bitowego. Jeśli potrzebujesz biblioteki kodu bitowego włączone `Microsoft.CognitiveServices.Speech.Xamarin.iOS` dla aplikacji, należy użyć NuGet dla projektu systemu iOS w szczególności.

1. W okienku stanu pakietu obok wyników wyszukiwania wybierz wszystkie projekty: **helloworld**, **helloworld. Android**, **helloworld.iOS**i **helloworld. Platformy uniwersalnej systemu zm.**

1. Wybierz pozycję **Zainstaluj**.

1. W oknie **dialogowym Podgląd zmian** wybierz przycisk **OK**.

1. W oknie dialogowym **Akceptacja licencji** wyświetl licencję, a następnie wybierz pozycję **Akceptuję**. Zainstaluj odwołanie do pakietu zestawu SDK mowy do wszystkich projektów. Po pomyślnym zakończeniu instalacji może zostać wyświetlone następujące ostrzeżenie dla helloworld.iOS. Jest to znany problem i nie powinno mieć wpływu na funkcjonalność aplikacji.

   > Nie można rozpoznać odwołania "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Jeśli to odwołanie jest wymagane przez kod, mogą pojawić się błędy kompilacji.
