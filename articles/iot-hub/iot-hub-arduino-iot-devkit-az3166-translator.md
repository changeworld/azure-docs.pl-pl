---
title: Utwórz w usłudze translator mxchip IoT DevKit za pomocą usługi Azure Functions i Cognitive Services | Dokumentacja firmy Microsoft
description: Korzystać z mikrofonu na mxchip IoT DevKit, aby otrzymywać wiadomość głosową, a następnie użyj usług Azure Cognitive Services do przetworzenia na przetłumaczonego tekstu w języku angielskim
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: df7e7b426a8c85c8051d7f588c706a6f8811e183
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518908"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Używaj DevKit az3166 usługi IoT z usługi Azure Functions i Cognitive Services umożliwia języka w usłudze translator

W tym artykule dowiesz się, jak mxchip IoT DevKit jako translator języka przy użyciu [usług Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). On rejestruje swój głos i tłumaczy je na widocznych na ekranie Mxchip tekstu w języku angielskim.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym Arduino zgodne z czujników i urządzeń peryferyjnych sformatowanego. Możesz tworzyć w ramach [Workbench urządzenia IoT Azure](https://aka.ms/iot-workbench) lub [narzędzia IoT Azure](https://aka.ms/azure-iot-tools) pakiet rozszerzenia programu Visual Studio Code. [Katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) zawiera przykładowe aplikacje ułatwiające prototypów rozwiązań IoT.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonanie czynności opisanych w tym samouczku, należy najpierw wykonać następujące zadania:

* Przygotuj swoje Mxchip wykonując kroki opisane w [połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub w chmurze](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Tworzenie usługi Azure Cognitive

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** i wyszukaj **mowy**. Wypełnij formularz, aby utworzyć usługę rozpoznawania mowy.
  ![Usługa rozpoznawania mowy](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Przejdź do usługi rozpoznawania mowy użytkownik właśnie utworzone, kliknij przycisk **klucze** sekcji, aby skopiować i zanotuj **klucz1** dla Mxchip dostęp do niego.
  ![Kopiowanie kluczy](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

1. Upewnij się, jest Twoja mxchip IoT DevKit **niepołączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz Mxchip do komputera.

1. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Otwórz przykłady...** . Następnie wybierz pozycję **mxchip IoT DevKit** jako tablica.

1. Na stronie Przykłady aplikacji Workbench IoT, Znajdź **Translator Mxchip** i kliknij przycisk **Otwórz przykładowy**. Następnie wybiera ścieżki domyślnej, aby pobrać przykładowy kod.
  ![Otwórz przykładowy](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Usługa rozpoznawania mowy za pomocą usługi Azure Functions

1. W programie VS Code kliknij `F1`wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Aprowizacja usług platformy Azure...** . ![Aprowizacja usług platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Wykonaj kroki, aby zakończyć inicjowanie obsługi usługi Azure IoT Hub i Azure Functions.
   ![Kroki aprowizacji](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Zanotuj nazwę urządzenia Azure IoT Hub, który został utworzony.

1. Otwórz `Functions\DevKitTranslatorFunction.cs` i zaktualizuj następujące wiersze kodu przy użyciu nazwy urządzenia i klucz usługi mowy, możesz zauważyć, że w dół.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Kliknij przycisk `F1`wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Wdrażanie na platformie Azure...** . Jeśli program VS Code prosi o potwierdzenie ponownego wdrożenia, kliknij przycisk **tak**.
   ![Wdrażanie ostrzeżenie](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Upewnij się, że wdrożenie zakończy się pomyślnie.
   ![Powodzenie wdrażania](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. W witrynie Azure portal, przejdź do **aplikacje funkcji** sekcji, znajdowanie aplikacji funkcji platformy Azure, właśnie utworzony. Kliknij przycisk `devkit_translator`, następnie kliknij przycisk **<> / Pobierz adres URL funkcji** można skopiować adres URL.
   ![Skopiuj adres URL funkcji](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Wklej adres URL do `azure_config.h` pliku.
   ![Konfiguracja platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Jeśli aplikacja funkcji nie działa prawidłowo, sprawdź to [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) sekcji, aby rozwiązać ten problem.

## <a name="build-and-upload-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

1. Przełącz Mxchip do **tryb konfiguracji** przez:
   * Wciśnij przycisk **A**.
   * Naciśnij i zwolnij **resetowania** przycisku.

   Zostanie wyświetlony na ekranie zostanie wyświetlony identyfikator Mxchip i **konfiguracji**.

   ![Tryb konfiguracji Mxchip](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Kliknij przycisk `F1`wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Konfigurowanie ustawień urządzenia... > Parametry połączenia urządzenia Config**. Wybierz **Wybierz parametry połączenia urządzenia Centrum IoT** ją skonfigurować do Mxchip.
   ![Konfigurowanie parametrów połączenia](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Zostanie wyświetlone powiadomienie, gdy zakończy się pomyślnie.
   ![Konfigurowanie Powodzenie ciąg połączenia](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Kliknij przycisk `F1` wpisz ponownie, a następnie wybierz **Workbench urządzenia IoT platformy Azure: Przekazywanie kodu urządzenia**. Rozpoczyna kompilacji i Przekaż kod do Mxchip.
   ![Przekazywanie urządzeń](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Projekt testowy

Po zainicjowaniu aplikacji postępuj zgodnie z instrukcjami na ekranie Mxchip. Domyślny język źródłowy jest chińskim.

Aby wybrać inny język do tłumaczenia:

1. Naciśnij przycisk A, aby przejść do trybu instalacji.

2. Naciśnij przycisk B przewiń wszystkie obsługiwane języki źródła.

3. Naciśnij przycisk A, aby potwierdzić wybór języka źródłowego.

4. Naciśnij i mówienie, przytrzymując przycisk B, a następnie zwolnij przycisk B, aby zainicjować tłumaczenia.

5. Przetłumaczony tekst w języku angielskim pokazuje na ekranie.

![Przewiń na wybór języka](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Wynik tłumaczenia](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na ekranie tłumaczenia wyników możesz wykonywać następujące czynności:

- Naciśnij przycisk, A i B, przewiń i wybierz język źródłowy.

- Naciśnij przycisk B rozmawiać. Aby wysyłać głosu i uzyskania tłumaczenia tekstu, zwolnij przycisk B.

## <a name="how-it-works"></a>Jak to działa

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Mxchip IoT DevKit rejestruje swój głos, a następnie wysyła żądanie HTTP do wyzwalacza usługi Azure Functions. Usługa Azure Functions wywołuje translatorze interfejsu API tłumaczenia mowy usługi cognitive Services. Po usługi Azure Functions pobiera tekst tłumaczenia, wysyła komunikat C2D na urządzeniu. Następnie tłumaczenie jest wyświetlany na ekranie.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [często zadawane pytania IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktowanie się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Ma przedstawiono sposób używania mxchip IoT DevKit jako translator, za pomocą usługi Azure Functions i Cognitive Services. W tym instruktażu przedstawiono sposób:

> [!div class="checklist"]
> * Zadanie programu Visual Studio Code służy do automatyzowania przepisów chmury
> * Konfigurowanie parametrów połączenia urządzenia Azure IoT
> * Wdrażanie funkcji platformy Azure
> * Testowanie tłumaczenie wiadomości głosowych

Przejdź do samouczków, aby dowiedzieć się więcej:

> [!div class="nextstepaction"]
> [Połącz DevKit az3166 usługi IoT do monitorowania zdalnego IoT Azure akceleratora rozwiązań](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
