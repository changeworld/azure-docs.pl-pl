---
title: Utwórz w usłudze translator mxchip IoT DevKit za pomocą usługi Azure Functions i Cognitive Services | Dokumentacja firmy Microsoft
description: Korzystać z mikrofonu na mxchip IoT DevKit, aby otrzymywać wiadomość głosową, a następnie użyj usług Azure Cognitive Services do przetworzenia na przetłumaczonego tekstu w języku angielskim
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: acfff95afacfa1e5c75a799ba84d64cfa0579f66
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592095"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Używaj DevKit az3166 usługi IoT z usługi Azure Functions i Cognitive Services umożliwia języka w usłudze translator

W tym artykule dowiesz się, jak mxchip IoT DevKit jako translator języka przy użyciu [usług Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). On rejestruje swój głos i tłumaczy je na widocznych na ekranie Mxchip tekstu w języku angielskim.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym Arduino zgodne z czujników i urządzeń peryferyjnych sformatowanego. Możesz tworzyć w ramach [rozszerzenie programu Visual Studio Code dla Arduino](https://aka.ms/arduino). I pochodzi ona z rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) przeprowadzenie Cię przez proces prototypu rozwiązania Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [przewodnik wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje Mxchip nawiązano połączenie z sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować się przy użyciu jednej z tych dwóch metod:

* Aktywuj [bezpłatnej 30-dniowej wersji próbnej konta systemu Microsoft Azure](https://azure.microsoft.com/free/)
* Oświadczenie użytkownika [platformy Azure w wysokości](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Jeśli jesteś subskrybentem MSDN lub Visual Studio

## <a name="open-the-project-folder"></a>Otwórz folder projektu

Najpierw otwórz folder projektu. 

### <a name="start-vs-code"></a>Uruchom program VS Code

- Upewnij się, że Twoje Mxchip jest podłączony do Twojego komputera.

- Uruchom program VS Code.

- Podłącz Mxchip do komputera.

### <a name="open-the-arduino-examples-folder"></a>Otwórz folder przykłady Arduino

Po lewej stronie rozwiń **przykłady ARDUINO > przykłady zestawu DEWELOPERSKIEGO az3166 usługi > AzureIoT**i wybierz **DevKitTranslator**. Zostanie otwarte nowe okno programu VS Code wyświetlanie folderu projektu. Jeśli nie widać przykłady dla sekcji az3166 usługi zestawu DEWELOPERSKIEGO, upewnij się, urządzenie jest prawidłowo podłączony i uruchom ponownie program VS Code.  

![Przykłady mxchip IoT DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

Przykład można również otworzyć z palety poleceń. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="provision-azure-services"></a>Aprowizacja usług platformy Azure

W oknie rozwiązania wpisz `Ctrl+P` (z systemem macOS: `Cmd+P`) i wprowadź `task cloud-provision`.

W programie VS Code terminala interaktywne wiersza polecenia przeprowadzi inicjowania obsługi wszystkich niezbędnych usług platformy Azure:

![Zadanie aprowizacji chmury](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="deploy-the-azure-function"></a>Wdrażanie funkcji platformy Azure

Użyj `Ctrl+P` (z systemem macOS: `Cmd+P`) do uruchamiania `task cloud-deploy` umożliwia wdrażanie kodu w usłudze Azure Functions. Ten proces trwa zwykle 2 do 5 minut.

![Zadanie wdrażania w chmurze](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Po pomyślnym wdrożeniu funkcji platformy Azure należy wypełnić plik azure_config.h nazwą aplikacji funkcji. Możesz przejść do [witryny Azure portal](https://portal.azure.com/) go znaleźć:

![Znajdź nazwę aplikacji funkcji platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Jeśli funkcja platformy Azure nie działa prawidłowo, sprawdź [strony "Błąd kompilacji dla funkcji platformy Azure" w często zadawanych PYTAŃ IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) go rozwiązać.

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

1. Użyj `Ctrl+P` (z systemem macOS: `Cmd+P`) do uruchamiania `task config-device-connection`.

2. Naciśnij w terminalu zapyta, czy chcesz użyć parametrów połączenia, które pobiera z `task cloud-provision` kroku. Można również wpisać własne parametry połączenia urządzenia, wybierając **"Utwórz nową..."**

3. Naciśnij w terminalu wyświetli monit o wprowadzenie tryb konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony identyfikator Mxchip i "Konfiguracja".

   ![Weryfikacja i przekazywania szkic Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Po `task config-device-connection` gotowe, kliknij przycisk `F1` ładować poleceń programu VS Code i wybierz `Arduino: Upload`, następnie uruchamia program VS Code, weryfikowanie i przekazywanie Arduino szkic.

   ![Weryfikacja i przekazywania szkic Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

Mxchip rozruchu i uruchamiania kodu.

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

![Mini-Solution-Voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Rekordy szkic Arduino swój głos publikuje następnie HTTP żądania wyzwolenia funkcji platformy Azure. Funkcja Azure wywołuje translatorze interfejsu API tłumaczenia mowy usługi cognitive Services. Po funkcji platformy Azure pobiera tekst tłumaczenia, wysyła komunikat (z chmury do urządzenia) C2D do urządzenia. Następnie tłumaczenie jest wyświetlany na ekranie.

## <a name="change-device-id"></a>Zmiana Identyfikatora urządzenia

Identyfikator urządzenia domyślne, które są zarejestrowane w usłudze Azure IoT Hub jest **az3166 usługi**. Aby zmodyfikować identyfikator urządzenia, zobacz instrukcje [dostosować identyfikator urządzenia IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [często zadawane pytania IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktowanie się z nami za pomocą następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Ma przedstawiono sposób używania mxchip IoT DevKit jako translator, za pomocą usługi Azure Functions i Cognitive Services. W tym instruktażu przedstawiono sposób:

> [!div class="checklist"]
> * Zadanie programu Visual Studio Code służy do automatyzowania przepisów chmury
> * Konfigurowanie parametrów połączenia urządzenia Azure IoT
> * Wdrażanie funkcji platformy Azure
> * Testowanie tłumaczenie wiadomości głosowych

Przejdź do samouczków, aby dowiedzieć się więcej:

> [!div class="nextstepaction"]
> [Połącz DevKit az3166 usługi IoT do monitorowania zdalnego IoT Azure akceleratora rozwiązań](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
