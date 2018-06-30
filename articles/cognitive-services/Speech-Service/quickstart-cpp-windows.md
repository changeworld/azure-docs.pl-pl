---
title: Szybki Start — mowy zestawu SDK dla języka C++ i Windows | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy z systemem Windows i C++ w usługach kognitywnych przy użyciu zestawu SDK mowy.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 4dea1209548fb3bb4f846cdf7eaf82751f938bea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108848"
---
# <a name="quickstart-for-c-and-windows"></a>Szybki Start dla języka C++ i systemu Windows

Bieżąca wersja zestawu SDK kognitywnych mowy usługi jest `0.4.0`.

Opisano sposób tworzenia aplikacji konsoli na podstawie C++ for Windows Desktop, który korzysta z zestawu SDK mowy.
Ta aplikacja jest oparta na [pakietu NuGet zestawu SDK usługi Microsoft kognitywnych](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) i 2017 r Microsoft Visual Studio.

> [!NOTE]
> Jeśli szukasz szybkiego startu dla języka C++ i Linux Przejdź [tutaj](quickstart-cpp-linux.md).<br>
> Jeśli szukasz szybkiego startu dla C# i systemu Windows, przejdź [tutaj](quickstart-csharp-windows.md).

> [!NOTE]
> Ta opcja szybkiego startu wymaga komputera z mikrofon działa.<br>
> Dla przykładu, które rozpoznaje mowy z danego pliku wejściowego audio zobacz [próbki](speech-to-text-sample.md#speech-recognition-from-a-file).

> [!NOTE]
> Upewnij się, że obejmuje instalację programu Visual Studio **tworzenia klasycznych aplikacji w języku C++** obciążenia.
> Jeśli nie masz pewności, wykonaj następujące czynności, aby Sprawdź i napraw: W programie Visual Studio 2017, wybierz **narzędzia** \> **Pobierz narzędzia i funkcje** i potwierdzić Kontrola konta użytkownika monitu, wybierając **Tak**.
> W **obciążeń** Jeśli karta **tworzenia klasycznych aplikacji w języku C++** nie ma zestawu pole wyboru obok niej, ustaw go i kliknij na **Modyfikuj** można zapisać zmian.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Tworzenie projektu aplikacji w pustej konsoli

W programie Visual Studio 2017 r Utwórz nowy Visual C++ aplikacji systemu Windows pulpitu systemu Windows konsoli o nazwie "CppHelloSpeech":

![Tworzenie aplikacji konsoli pulpitu systemu Windows Visual C++ systemu Windows](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Jeśli używasz 64-bitowej instalacji systemu Windows, przełączyć opcjonalnie platformy kompilacji do `x64`:

![Przełącz platformy kompilacji do x64](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Instalowanie i odwołują się do pakietu NuGet SDK mowy

> [!NOTE]
> Upewnij się, że Menedżer pakietów NuGet jest włączone dla tej instalacji programu Visual Studio 2017 r.
> W programie Visual Studio 2017 r, wybierz **narzędzia** \> **Pobierz narzędzia i funkcje** i potwierdzić Kontrola konta użytkownika monitu, wybierając **tak**. Następnie wybierz **pojedynczych składników** , a następnie wyszukaj **Menedżera pakietów NuGet** w obszarze **kodu narzędzia**.
> Jeśli pole wyboru po lewej stronie nie jest ustawiona, upewnij się, że go ustawić i wybierz polecenie **Modyfikuj** można zapisać zmian.
>
> ![Włącz Menedżera pakietów NuGet w programie Visual Studio ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz polecenie **Zarządzaj pakietami NuGet dla rozwiązania**.

![Kliknij prawym przyciskiem myszy Zarządzaj pakietami NuGet dla rozwiązania](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

W prawym górnym rogu w **źródła pakietu** pola, wybierz polecenie "Nuget.org".
Z **Przeglądaj** , wyszukaj pakiet "Microsoft.CognitiveServices.Speech", zaznacz go i sprawdź **projektu** i **CppHelloSpeech** pól po prawej stronie, a Wybierz **zainstalować** go zainstalować w projekcie CppHelloSpeech.

![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

Na ekranie licencji, które pojawia się Zaakceptuj licencji:

![Akceptuje](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

Zastąp swój domyślny kod początkowy następujące:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Zastąp ten, który został uzyskany klucz subskrypcji. <br>
> Zastąp [region](regions.md) z elementem skojarzone z subskrypcją, na przykład zastąpić `westus` dla bezpłatnej wersji próbnej subskrypcji.

![Dodaj klucz subskrypcji](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

Kod powinien teraz skompilować bez błędów:

![Pomyślnego utworzenia kompilacji](media/sdk/speechsdk-16-vs-cpp-build.png)

Uruchom program w debugerze przycisk Uruchom lub za pomocą skrótu klawiaturowego F5:

![Uruchom aplikację do debugowania](media/sdk/speechsdk-17-vs-cpp-f5.png)

Okno konsoli powinna wyskakujące, monitując powiedzieć coś (w języku angielskim).
Wynik uznania będą wyświetlane na ekranie.

![Dane wyjściowe konsoli po pomyślnym rozpoznawania](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>Pobieranie próbki

Dla zestawu najnowsze przykłady, zobacz [repozytorium GitHub próbki SDK kognitywnych usług mowy](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Kolejne kroki

* Odwiedź stronę [przykłady strony](samples.md) dla dodatkowych przykładów.
