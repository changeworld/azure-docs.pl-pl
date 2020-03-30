---
title: Pobieranie wiadomości na Twitterze za pomocą usługi Azure Functions | Dokumenty firmy Microsoft
description: Użyj czujnika ruchu, aby wykryć potrząsanie i użyj usługi Azure Functions, aby znaleźć losowy tweet z hashtagiem, który określisz
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60779839"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Potrząśnij, potrząśnij tweetem - pobierz wiadomość z Twittera za pomocą funkcji platformy Azure

W tym projekcie dowiesz się, jak używać czujnika ruchu do wyzwalania zdarzenia przy użyciu usługi Azure Functions. Aplikacja pobiera losowy tweet z #hashtag, które konfigurujesz w szkicu Arduino. Tweet jest wyświetlany na ekranie DevKit.

## <a name="what-you-need"></a>Co jest potrzebne

Ukończ [przewodnik Wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Niech DevKit będzie połączony z siecią Wi-Fi.
* Przygotuj środowisko programistyczne.

Aktywna subskrypcja platformy Azure. Jeśli go nie masz, możesz zarejestrować się za pomocą jednej z następujących metod:

* Aktywowanie [bezpłatnego 30-dniowego konta microsoft azure podczas próby](https://azure.microsoft.com/free/)
* Ubiegaj się o [kredyt na platformę Azure,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jeśli jesteś subskrybentem usługi MSDN lub Visual Studio

## <a name="open-the-project-folder"></a>Otwieranie folderu projektu

Zacznij od otwarcia folderu projektu. 

### <a name="start-vs-code"></a>Uruchom kod VS

* Upewnij się, że devkit jest podłączony do komputera.

* Uruchom program VS Code.

* Podłącz devkit do komputera.

   > [!NOTE]
   > Podczas uruchamiania programu VS Code może pojawić się komunikat o błędzie, którego nie można odnaleźć arduino IDE lub powiązanego pakietu tablicy. Jeśli wystąpi ten błąd, zamknij kod VS i uruchom Arduino IDE ponownie. VS Kod powinien teraz poprawnie zlokalizować ścieżkę Arduino IDE.

### <a name="open-the-arduino-examples-folder"></a>Otwórz folder Przykłady Arduino

Rozwiń sekcję **ARDUINO EXAMPLES** po lewej stronie, przejdź do **przykładów dla programu MXCHIP AZ3166 > AzureIoT**i wybierz **opcję ShakeShake**. Zostanie otwarte nowe okno programu VS Code z folderem projektu. Jeśli nie widzisz sekcji MXCHIP AZ3166, upewnij się, że urządzenie jest poprawnie podłączone i uruchom ponownie program Visual Studio Code.  
przykłady ![mini-rozwiązania](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Przykładowy projekt można również otworzyć z palety poleceń. Kliknij `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

## <a name="provision-azure-services"></a>Aprowizuj usługi platformy Azure

W oknie rozwiązania uruchom zadanie `Ctrl+P` za pośrednictwem `Cmd+P`(macOS: ) wprowadzając `task cloud-provision`program .

W terminalu kodu VS interaktywna linia poleceń prowadzi użytkownika przez inicjowanie obsługi administracyjnej wymaganych usług platformy Azure:

![udostępnianie w chmurze](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Jeśli strona zawiesza się w stanie ładowania podczas próby zalogowania się na platformie Azure, zapoznaj się z [krokiem "strona logowania zawiesza się" w często zadawanych pytaniach ioT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modyfikowanie #hashtag

Otwórz `ShakeShake.ino` i poszukaj tego wiersza kodu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Zastąp ciąg `iot` w nawiasach klamrowych preferowanym hasztagiem. DevKit później pobiera losowy tweet, który zawiera hashtag, który określisz w tym kroku.

## <a name="deploy-azure-functions"></a>Wdrażanie usługi Azure Functions

Użyj `Ctrl+P` (macOS: `Cmd+P`), `task cloud-deploy` aby uruchomić, aby rozpocząć wdrażanie kodu usługi Azure Functions:

![wdrażanie w chmurze](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Od czasu do czasu funkcja platformy Azure może nie działać poprawnie. Aby rozwiązać ten problem w momencie jego wystąpienia, sprawdź [sekcję "błąd kompilacji" w często zadawanych pytaniach dotyczących zestawu IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przesyłanie kodu urządzenia

Następnie skompiluj i przekaż kod urządzenia.

### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do `task device-upload`uruchomienia .

2. Terminal wyświetli monit o przełączenie do trybu konfiguracji. W tym celu:

   * Przytrzymaj przycisk A

   * Naciśnij i zwolnij przycisk resetowania.

3. Na ekranie są wyświetlane identyfikatory DevKit i "Konfiguracja".

### <a name="macos"></a>macOS

1. Przełoń DevKit w tryb konfiguracji:

   Przytrzymaj przycisk A, a następnie naciśnij i zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony komunikat "Konfiguracja".

2. Służy `Cmd+P` do `task device-upload` uruchamiania, aby ustawić ciąg `task cloud-provision` połączenia, który jest pobierany z kroku.

### <a name="verify-upload-and-run"></a>Weryfikowanie, przekazywanie i uruchamianie

Teraz ciąg połączenia jest ustawiony, weryfikuje i przekazuje aplikację, a następnie uruchamia go. 

1. Vs Code rozpoczyna weryfikację i przesłanie szkicu Arduino do DevKit:

   ![przesyłanie urządzenia](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit uruchamia się ponownie i uruchamia kod.

Może pojawić się komunikat o błędzie "Błąd: AZ3166: Nieznany pakiet". Ten błąd występuje, gdy indeks pakietu płyty nie jest odświeżany poprawnie. Aby rozwiązać ten problem, sprawdź [błąd "nieznany pakiet" w często zadawanych pytaniach ioT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testowanie projektu

Po inicjacji aplikacji kliknij i zwolnij przycisk A, a następnie delikatnie potrząśnij tablicą DevKit. Ta akcja pobiera losowy tweet, który zawiera hashtag, który został określony wcześniej. W ciągu kilku sekund na ekranie DevKit zostanie wyświetlony tweet:

### <a name="arduino-application-initializing"></a>Arduino aplikacja inicjowania ...

![Inicjowanie aplikacji Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Naciśnij klawisz A, aby potrząsnąć...

![Naciśnij przycisk A-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Gotowy do potrząsania...

![Gotowy do potrząsania](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Przetwarzania...

![Przetwarzanie](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Naciśnij klawisz B, aby przeczytać...

![Naciśnij klawiszE-B-to-read](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Wyświetl losowy tweet...

![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Naciśnij przycisk A ponownie, a następnie potrząśnij nowym tweetem.
- Naciśnij przycisk B, aby przewinąć resztę tweeta.

## <a name="how-it-works"></a>Jak to działa

![diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Szkic Arduino wysyła zdarzenie do usługi Azure IoT Hub. To zdarzenie wyzwala aplikację Usługi Azure Functions. Aplikacja Usługi Azure zawiera logikę, aby połączyć się z interfejsem API twittera i pobrać tweet. Następnie zawija tekst tweeta do wiadomości C2D (Cloud-to-device) i wysyła go z powrotem do urządzenia.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcjonalnie: Użyj własnego tokenu okaziciela Twittera

Do celów testowych ten przykładowy projekt używa wstępnie skonfigurowanego tokenu okaziciela Twittera. Istnieje jednak [limit stawek](https://dev.twitter.com/rest/reference/get/search/tweets) dla każdego konta na Twitterze. Jeśli chcesz rozważyć użycie własnego tokenu, wykonaj następujące kroki:

1. Przejdź do [portalu Twitter Developer,](https://dev.twitter.com/) aby zarejestrować nową aplikację Twitter.

2. [Pobierz klucz konsumencki i tajemnice konsumenckie](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) swojej aplikacji.

3. Użyj [narzędzia](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) do generowania tokenu okaziciela Twittera z tych dwóch kluczy.

4. W [portalu Azure](https://portal.azure.com/){:target="_blank"}, dostać się do **grupy zasobów** i znaleźć funkcję platformy Azure (typ: usługa aplikacji) dla projektu "Shake, Shake". Nazwa zawsze zawiera "shake...". Ciąg.

   ![azure-funkcja](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Zaktualizuj kod w `run.csx` ramach funkcji > **shakeshake-cs** za pomocą własnego tokenu:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Zapisz plik i kliknij przycisk **Uruchom**.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jak rozwiązywać problemy lub przekazywać opinie. 

### <a name="problems"></a>Problemy

Jeden problem można zobaczyć, czy na ekranie wyświetla "Nie Tweets", podczas gdy każdy krok został pomyślnie uruchomiony. Ten warunek zwykle dzieje się przy pierwszym wdrożeniu i uruchomieniu próbki, ponieważ aplikacja funkcji wymaga dowolnego miejsca od kilku sekund do nawet jednej minuty do zimnego uruchomienia aplikacji. 

Lub podczas uruchamiania kodu, istnieją pewne blips, które powodują ponowne uruchomienie aplikacji. W takim stanie aplikacja urządzenia może uzyskać limit czasu na pobranie tweeta. W takim przypadku można wypróbować jedną lub obie te metody, aby rozwiązać problem:

1. Kliknij przycisk resetowania na DevKit, aby ponownie uruchomić aplikację urządzenia.

2. W [witrynie Azure portal](https://portal.azure.com/)znajdź utworzoną aplikację Usługi Azure Functions i uruchom ją ponownie:

   ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Opinia

Jeśli wystąpią inne problemy, zapoznaj się z [często zadawanymi pytaniami dotyczącymi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz instrukcje, jak podłączyć urządzenie DevKit do akceleratora rozwiązania zdalnego monitorowania usługi Azure IoT i pobrać tweet, oto sugerowane następne kroki:

* [Omówienie akceleratora rozwiązań do zdalnego monitorowania usługi Azure IoT](https://docs.microsoft.com/azure/iot-suite/)