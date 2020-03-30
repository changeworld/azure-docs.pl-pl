---
title: Spraw, aby tłumacz z/Azure Functions, Cognitive Services, IoT DevKit
description: Użyj mikrofonu w zestawie IoT DevKit, aby odbierać wiadomości głosowe, a następnie używać usługi Azure Cognitive Services do przetwarzania jej na przetłumaczony tekst w języku angielskim
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953355"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Używanie funkcji IoT DevKit AZ3166 z usługami Azure Functions i Cognitive Services w celu translatora języków

W tym artykule dowiesz się, jak zrobić IoT DevKit jako tłumacz języka przy użyciu [usługi Azure Cognitive Services.](https://azure.microsoft.com/services/cognitive-services/) Rejestruje twój głos i tłumaczy go na tekst w języku angielskim wyświetlany na ekranie DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) to płyta kompatybilna z all-in-one Arduino z bogatymi urządzeniami peryferyjnymi i czujnikami. Można go opracować przy użyciu [usługi Azure IoT Device Workbench](https://aka.ms/iot-workbench) lub pakietu rozszerzeń [narzędzi Azure IoT w](https://aka.ms/azure-iot-tools) programie Visual Studio Code. [Katalog projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) zawiera przykładowe aplikacje ułatwiające tworzenie prototypów rozwiązań IoT.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym samouczku, wykonaj następujące czynności:

* Przygotuj devkit, wykonując kroki opisane w [connect IoT DevKit AZ3166 do usługi Azure IoT Hub w chmurze.](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)

## <a name="create-azure-cognitive-service"></a>Tworzenie usługi Azure Cognitive Service

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** i wyszukaj **w polu Mowa**. Wypełnij formularz, aby utworzyć usługę mowy.
  ![Usługa rozpoznawania mowy](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Przejdź do usługi mowy, którą właśnie utworzono, kliknij sekcję **Klucze,** aby skopiować i zanotuj **key1** dla DevKit uzyskującego do niego dostęp.
  ![Kopiowanie kluczy](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

1. Upewnij się, że zestaw IoT DevKit nie jest **podłączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz devkit do komputera.

1. Kliknij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz pozycję Azure **IoT Device Workbench: Open Examples...**. Następnie wybierz **IoT DevKit** jako tablicę.

1. Na stronie Przykłady programu IoT Workbench znajdź **devkit translator** i kliknij przycisk **Otwórz przykład**. Następnie wybiera domyślną ścieżkę, aby pobrać przykładowy kod.
  ![Otwórz próbkę](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Korzystanie z usługi mowy z funkcjami platformy Azure

1. W programie VS `F1`Code kliknij przycisk , wpisz i wybierz pozycję **Azure IoT Device Workbench: Aprowizuj usługi platformy Azure...**. ![Aprowizuj usługi platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Wykonaj kroki, aby zakończyć inicjowanie obsługi administracyjnej usługi Azure IoT Hub i usługi Azure Functions.
   ![Kroki inicjowania obsługi administracyjnej](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Zanotuj nazwę utworzonego urządzenia usługi Azure IoT Hub.

1. Otwórz `Functions\DevKitTranslatorFunction.cs` i zaktualizuj następujące wiersze kodu za pomocą nazwy urządzenia i klucza usługi mowy, który został odnotowany w dół.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Kliknij `F1`, wpisz i wybierz **pozycję Azure IoT Device Workbench: Deploy to Azure...**. Jeśli program VS Code poprosi o potwierdzenie ponownego rozmieszczenia, kliknij przycisk **Tak**.
   ![Ostrzeżenie o wdrażaniu](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Upewnij się, że wdrożenie zakończyło się pomyślnie.
   ![Wdrażanie sukcesu](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. W witrynie Azure portal przejdź do sekcji **Aplikacje funkcji,** znajdź właśnie utworzoną aplikację funkcji platformy Azure. Kliknij `devkit_translator`przycisk , a następnie kliknij **przycisk</> Pobierz adres URL funkcji,** aby skopiować adres URL.
   ![Adres URL funkcji kopiowania](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Wklej adres `azure_config.h` URL do pliku.
   ![Config platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Jeśli aplikacja Function nie działa poprawnie, sprawdź tę [sekcję często zadawane pytania,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) aby ją rozwiązać.

## <a name="build-and-upload-device-code"></a>Tworzenie i przesyłanie kodu urządzenia

1. Przełącz DevKit do trybu konfiguracji, przełączając go do **trybu konfiguracyjnego,** przełączając go przez:
   * Przytrzymaj przycisk **A**.
   * Naciśnij i zwolnij przycisk **Reset.**

   Na ekranie pojawi się identyfikator DevKit i **konfiguracja**.

   ![Tryb konfiguracji DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Kliknij `F1`, wpisz i wybierz pozycję **Pulpit urządzenia Usługi Azure IoT: Konfigurowanie ustawień urządzenia... > parametry połączenia urządzenia konfiguracji**. Wybierz **wybierz ciąg połączenia urządzenia centrum IoT,** aby skonfigurować go do zestawu DevKit.
   ![Konfigurowanie ciągu połączenia](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Powiadomienie zostanie wyświetlone po pomyślnym zakończeniu.
   ![Konfigurowanie sukcesu ciągu połączenia](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Kliknij `F1` ponownie, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia**. Uruchamia kompilacji i przekazać kod do DevKit.
   ![Przesyłanie urządzenia](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testowanie projektu

Po inicjacji aplikacji postępuj zgodnie z instrukcjami na ekranie DevKit. Domyślnym językiem źródłowym jest chiński.

Aby wybrać inny język tłumaczenia:

1. Naciśnij przycisk A, aby przejść do trybu konfiguracji.

2. Naciśnij przycisk B, aby przewinąć wszystkie obsługiwane języki źródłowe.

3. Naciśnij przycisk A, aby potwierdzić wybór języka źródłowego.

4. Naciśnij i przytrzymaj przycisk B podczas mówienia, a następnie zwolnij przycisk B, aby zainicjować tłumaczenie.

5. Przetłumaczony tekst w języku angielskim jest wyświetlany na ekranie.

![Przewiń, aby wybrać język](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Wynik tłumaczenia](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na ekranie wyników tłumaczenia można:

- Naciśnij przyciski A i B, aby przewinąć i wybrać język źródłowy.

- Naciśnij przycisk B, aby porozmawiać. Aby wysłać głos i uzyskać tekst tłumaczenia, zwolnij przycisk B.

## <a name="how-it-works"></a>Jak to działa

![mini-rozwiązanie-głos-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit rejestruje twój głos, a następnie publikuje żądanie HTTP do wyzwolenia funkcji platformy Azure. Usługa Azure Functions wywołuje interfejs API translatora mowy usługi poznawczej w celu wykonania tłumaczenia. Po pobiera tekst tłumaczenia usługi Azure, wysyła komunikat C2D do urządzenia. Następnie tłumaczenie jest wyświetlane na ekranie.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z [często zadawanymi pytaniami dotyczącymi programu IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak używać IoT DevKit jako tłumacza przy użyciu funkcji azure i usług cognitive services. W tym instrukcje, nauczyłeś się, jak:

> [!div class="checklist"]
> * Automatyzacja apowienia w chmurze za pomocą zadania Visual Studio Code
> * Konfigurowanie ciągu połączenia urządzenia IoT platformy Azure
> * Wdrażanie funkcji platformy Azure
> * Testowanie tłumaczenia wiadomości głosowych

Przejdź do innych samouczków, aby dowiedzieć się:

> [!div class="nextstepaction"]
> [Podłącz akceleratora rozwiązań IoT DevKit AZ3166 z akceleratorem zdalnego monitorowania usługi Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
