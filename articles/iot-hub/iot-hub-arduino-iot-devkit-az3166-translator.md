---
title: Translator zestaw deweloperski IoT przy użyciu funkcji platformy Azure i usługi kognitywnych | Dokumentacja firmy Microsoft
description: W systemie mikrofon zestaw deweloperski IoT odbierać wiadomości głosowe i kognitywnych usług Azure do przetworzenia na przetłumaczony tekst w języku angielskim.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: d17f117d71eb0616201df18aea6dc48749ae24a8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Używaj AZ3166 zestaw deweloperski IoT z kognitywnych usług i funkcji platformy Azure umożliwia translatora języka

W tym artykule opisano sposób utworzyć przy użyciu zestaw deweloperski IoT jako translator języka [kognitywnych usług Azure](https://azure.microsoft.com/services/cognitive-services/). Rejestruje Twój głos, a tłumaczy je na tekst wyświetlany na ekranie zestaw deweloperski w języku angielskim.

[Zestaw deweloperski IoT MXChip](https://aka.ms/iot-devkit) jest w jednym Arduino tablicy zgodny z czujników i sformatowanego urządzenia peryferyjne. Można utworzyć dla niego przy użyciu [rozszerzenia Visual Studio Code Arduino](https://aka.ms/arduino). I pochodzi z rosnącym [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) prowadzące prototypu Internetu rzeczy (IoT) rozwiązania, które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje zestaw deweloperski ma podłączony do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować za pomocą jednej z tych dwóch metod:

* Aktywuj [bezpłatne 30-dniowej wersji próbnej konto Microsoft Azure](https://azure.microsoft.com/en-us/free/)
* Oświadczenie użytkownika [Azure środki](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) w przypadku subskrybentów MSDN lub Visual Studio

## <a name="step-1-open-the-project-folder"></a>Krok 1. Otwórz folder projektu

### <a name="a-start-vs-code"></a>A. Kod uruchomienia programu VS

- Upewnij się, że Twoje zestaw deweloperski nie jest podłączony do komputera.
- Kod uruchomienia programu VS
- Zestaw deweloperski można połączyć się z komputerem.

Kod VS automatycznie wyszukuje zestaw deweloperski i spowoduje otwarcie strony wprowadzenie:

![Strona wprowadzenia](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_start.png)

### <a name="b-open-the-arduino-examples-folder"></a>B. Otwórz folder Arduino przykłady

Po lewej stronie rozwiń **przykłady ARDUINO > przykłady MXCHIP AZ3166 > AzureIoT**i wybierz **DevKitTranslator**. Otwiera nowe okno kodzie VS z folderu projektu DEVKITTRANSLATOR w nim.

![Przykłady zestaw deweloperski IoT](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

Jeśli wystąpi aby zamknąć okienko, zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) do otwarcia palety polecenia, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="step-2-provision-azure-services"></a>Krok 2. Udostępnianie usług platformy Azure

W oknie rozwiązania wpisz `Ctrl+P` (macOS: `Cmd+P`), a następnie wprowadź `task cloud-provision`.

W kodzie VS terminali interakcyjne wiersza polecenia przeprowadzi inicjowania obsługi wszystkich niezbędnych usług Azure:

![Zadanie udostępniania chmury](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Krok 3. Wdrażanie usługi Azure Functions

Użyj `Ctrl+P` (macOS: `Cmd+P`) do uruchamiania `task cloud-deploy` do wdrożenia usługi Azure Functions kodu. Ten proces może zająć zwykle 2 do 5 minut:

![Zadanie wdrażania w chmurze](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Po funkcji platformy Azure wdraża się pomyślnie, wypełnij azure_config.h plik o nazwie aplikacji funkcji. Można przejść do [portalu Azure](https://portal.azure.com/) znajdź go:

![Znajdowanie nazwy aplikacji Azure — funkcja](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Jeśli funkcja Azure nie działa prawidłowo, sprawdzić [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) sekcji, aby go rozwiązać.

## <a name="step-4-build-and-upload-the-device-code"></a>Krok 4. Tworzenie i przekazywanie kodu urządzenia

1. Użyj `Ctrl+P` (macOS: `Cmd+P`) do uruchamiania `task config-device-connection`.

2. Terminal zapyta, czy chcesz użyć parametrów połączenia, które są pobierane z `task cloud-provision` kroku. Możesz również wpisać własne parametry połączenia urządzenia, wybierając **"Utwórz nowy..."**

3. Terminal wyświetli monit o wprowadzenie trybu konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony identyfikator zestaw deweloperski i "Konfiguracja".
  ![Weryfikacja i przekazywanie szkicu Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Po `task config-device-connection` gotowe, kliknij przycisk `F1` załadować polecenia kodzie VS i wybierz `Arduino: Upload`, następnie kod VS rozpoczyna się weryfikowanie i przekazywanie Arduino szkic: ![weryfikacji i przekazywanie szkicu Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

## <a name="test-the-project"></a>Projekt testowy

Po zainicjowaniu aplikacji postępuj zgodnie z instrukcjami na ekranie zestaw deweloperski. Domyślny język źródła jest chińskiej wersji językowej.

Aby wybrać inny język tłumaczenia:

1. Naciśnij przycisk A, aby przejść do trybu instalacji.
2. Naciśnij przycisk B przewijanie wszystkich obsługiwanych językach źródła.
3. Naciśnij przycisk A, aby potwierdzić wybór języka źródłowego.
4. Naciśnij i mówiąc, przytrzymując przycisk B, a następnie zwolnij przycisk B zainicjować tłumaczenia.
5. Przetłumaczony tekst w angielskiej wersji językowej pokazuje na ekranie.

![Przewiń na wybór języka](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Wynik tłumaczenia](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na ekranie wyników tłumaczenia można:

- Naciśnij przycisk A i B, przewiń i wybierz język źródła.
- Naciśnij przycisk B, aby porozmawiać, zwolnij wysyłanie głosu i tłumaczenie tekstu

## <a name="how-it-works"></a>Jak to działa

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Rejestruje szkicu Arduino głosu następnie zapisuje żądania HTTP do usługi Azure Functions wyzwalacza. Środowisko Azure Functions wywołuje translator mowy kognitywnych usługi interfejsu API w celu tłumaczenia. Po usługi Azure Functions pobiera tekst tłumaczenia, wysyła komunikat C2D na urządzeniu. Następnie tłumaczenia jest wyświetlany na ekranie.

## <a name="change-device-id"></a>Zmień identyfikator urządzenia

Domyślny identyfikator urządzenia zarejestrowane w usłudze Azure IoT Hub jest **AZ3166**. Jeśli chcesz zmodyfikować go, wykonaj [instrukcje w tym miejscu](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub dotrzeć do nas z następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Teraz należy zestaw deweloperski IoT jako translator przy użyciu funkcji platformy Azure i usługi kognitywnych. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Visual Studio Code zadanie służy do automatyzowania przepisy chmury
> * Konfigurowanie parametrów połączenia urządzenia Azure IoT
> * Wdrażanie funkcji platformy Azure
> * Testowanie tłumaczenia wiadomości głosowych

Przejdź do innych samouczków, aby dowiedzieć się więcej:

> [!div class="nextstepaction"]
> [Nawiązać pakiet IoT Azure IoT zestaw deweloperski AZ3166 zdalnego monitorowania](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)