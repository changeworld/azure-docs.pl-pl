---
title: 'Szybki Start: Rozpoznawanie mowy, języka C++ na pulpicie Windows przy użyciu Cognitive Services SDK rozpoznawania mowy'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy, języka C++ na pulpicie Windows przy użyciu Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 59c70d33d537742a5918dc1ba55ab8ac1f84e12c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "41987519"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy, języka C++ na pulpicie Windows przy użyciu zestawu SDK rozpoznawania mowy

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Opisano sposób tworzenia aplikacji konsoli opartych na języku C++ dla Windows Desktop, który korzysta z zestawu SDK rozpoznawania mowy.
Ta aplikacja jest oparta na [Microsoft Cognitive Services mowy zestawu SDK pakietu NuGet](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Komputera z systemem Windows przy użyciu mikrofonu pracy.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition lub nowszy.
* **Programowanie aplikacji klasycznych w języku C++** obciążenia w programie Visual Studio i **Menedżera pakietów NuGet** składnika w programie Visual Studio.
  Możesz włączyć zarówno w **narzędzia** \> **Pobierz narzędzia i funkcje**w obszarze **obciążeń** i **poszczególne składniki** karty , odpowiednio:

  ![Włącz programowanie aplikacji klasycznych w języku C++](media/sdk/vs-enable-cpp-workload.png)

  ![Włącz Menedżera pakietów NuGet w programie Visual Studio ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

W programie Visual Studio 2017 Utwórz nową Visual C++ Windows Desktop Windows aplikację konsoli. W **nowy projekt** okno dialogowe, w lewym okienku rozwiń **zainstalowane** \> **Visual C++** \> **Windows Desktop** , a następnie wybierz **aplikacji konsoli Windows**. Wprowadź nazwę projektu *helloworld*.

![Tworzenie aplikacji konsoli pulpitu Windows Visual C++ Windows](media/sdk/qs-cpp-windows-01-new-console-app.png)

Jeśli pracujesz w 64-bitowej instalacji Windows, opcjonalnie Przełącz platforma kompilacji do `x64`:

![Przełącz platformę kompilacji do x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Instalowanie i tworzą odwołanie do pakietu NuGet zestawu SDK rozpoznawania mowy

W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz polecenie **Zarządzaj pakietami NuGet dla rozwiązania**.

![Kliknij prawym przyciskiem myszy Zarządzaj pakietami NuGet dla rozwiązania](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

W prawym górnym rogu w **źródła pakietu** wybierz opcję "Nuget.org".
Z **Przeglądaj** , wyszukaj pakiet "Microsoft.CognitiveServices.Speech", wybierz ją i sprawdź **projektu** i **helloworld** pola po prawej stronie i wybierz pozycję **Zainstalować** do zainstalowania go do projektu helloworld.

> [!NOTE]
> Bieżąca wersja zestawu SDK mowy usługi Cognitive Services to `0.6.0`.

![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

Na ekranie licencji, które się pojawi zaakceptuj licencję:

![Zaakceptuj licencję](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. Zastąp kod początkowy domyślny z tą następujące:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Skompiluj aplikację. Na pasku menu wybierz **kompilacji** > **Kompiluj rozwiązanie**. Kod powinien teraz skompilować bez błędów:

   ![Pomyślnej kompilacji](media/sdk/qs-cpp-windows-06-build.png)

1. Uruchom aplikację. Na pasku menu wybierz **debugowania** > **Rozpocznij debugowanie**, lub naciśnij **F5**.

   ![Uruchom aplikację do debugowania](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Okno konsoli pojawi, monit, że coś (w języku angielskim).
   Wynik rozpoznawanie będą wyświetlane na ekranie.

   ![Dane wyjściowe konsoli po pomyślnym rozpoznawania](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/cpp-windows` folderu.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz nasze przykłady](speech-sdk.md#get-the-samples)
