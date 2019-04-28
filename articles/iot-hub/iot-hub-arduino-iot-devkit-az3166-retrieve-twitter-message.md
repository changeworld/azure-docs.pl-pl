---
title: Pobieranie wiadomości z usługi Twitter za pomocą usługi Azure Functions | Dokumentacja firmy Microsoft
description: Czujnik umożliwiają wykrywanie potrząsając i Znajdź losowe tweet z hasztagiem, który określisz za pomocą usługi Azure Functions
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60779839"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Potrząśnij, wstrząsnąć dla Tweet — pobieranie wiadomości z usługi Twitter za pomocą usługi Azure Functions

W tym projekcie dowiesz się, jak za pomocą czujnik wyzwalać zdarzenia za pomocą usługi Azure Functions. Aplikacja pobiera losowe tweet z #hashtag skonfigurowanych w swojej szkic Arduino. Tweet jest wyświetlany na ekranie Mxchip.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [przewodnik wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje Mxchip nawiązano połączenie z sieci Wi-Fi.
* Przygotowywanie środowiska deweloperskiego.

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować się za pomocą jednej z następujących metod:

* Aktywuj [bezpłatnej 30-dniowej wersji próbnej konta systemu Microsoft Azure](https://azure.microsoft.com/free/)
* Oświadczenie użytkownika [platformy Azure w wysokości](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Jeśli jesteś subskrybentem MSDN lub Visual Studio

## <a name="open-the-project-folder"></a>Otwórz folder projektu

Uruchom po otwarciu folderu projektu. 

### <a name="start-vs-code"></a>Uruchom program VS Code

* Upewnij się, że Twoje Mxchip jest podłączony do komputera.

* Uruchom program VS Code.

* Podłącz Mxchip do komputera.

   > [!NOTE]
   > Podczas uruchamiania programu VS Code, może zostać wyświetlony komunikat o błędzie, który środowisku IDE Arduino lub pakietu powiązane tablicy nie może zostać znalezione. Jeśli ten błąd występuje, zamknij program VS Code i ponownie uruchom środowisku IDE Arduino. Program VS Code powinna teraz poprawnie zlokalizować ścieżkę środowisku IDE Arduino.

### <a name="open-the-arduino-examples-folder"></a>Otwórz folder przykłady Arduino

Po lewej stronie rozwiń **przykłady ARDUINO** sekcji, przejdź do **przykłady zestawu DEWELOPERSKIEGO az3166 usługi > AzureIoT**i wybierz **ShakeShake**. Zostanie otwarte nowe okno programu VS Code wyświetlanie folderu projektu. Jeśli nie są wyświetlane w sekcji az3166 usługi zestawu DEWELOPERSKIEGO, upewnij się, urządzenie jest prawidłowo podłączony i ponownie uruchom Visual Studio Code.  
![mini solution przykłady](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Możesz również otworzyć przykładowy projekt z palety poleceń. Kliknij przycisk `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

## <a name="provision-azure-services"></a>Aprowizacja usług platformy Azure

W oknie rozwiązania należy uruchomić zadanie przy użyciu `Ctrl+P` (z systemem macOS: `Cmd+P`), wprowadzając `task cloud-provision`.

W terminalu programu VS Code interaktywne wiersza polecenia poprowadzi Cię przez Inicjowanie obsługi administracyjnej wymaganych usług platformy Azure:

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Jeśli strony stanu ładowania, zawiesza się podczas próby logowanie do platformy Azure, zapoznaj się ["zawiesza się strony logowania" krok z często zadawanymi Pytaniami IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modyfikowanie #hashtag

Otwórz `ShakeShake.ino` i wyszukaj ten wiersz kodu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Zastąp ciąg `iot` w nawiasy klamrowe hasztaga preferowany. Mxchip pobiera później losowe tweet zawierający hasztag, którą określisz w tym kroku.

## <a name="deploy-azure-functions"></a>Wdrażanie usługi Azure Functions

Użyj `Ctrl+P` (z systemem macOS: `Cmd+P`) do uruchamiania `task cloud-deploy` rozpocząć wdrażanie kodu funkcji platformy Azure:

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Od czasu do czasu funkcji platformy Azure może nie działać prawidłowo. Aby rozwiązać ten problem, jeśli występuje, sprawdź ["Błąd kompilacji" sekcji często zadawanych pytań IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

Następnie Skompiluj i przekazania kodu urządzenia.

### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload`.

2. Naciśnij w terminalu wyświetli monit o wprowadzenie tryb konfiguracji. W tym celu:

   * Naciśnij i przytrzymaj przycisk A

   * Naciśnij i zwolnij przycisk reset.

3. Na ekranie zostanie wyświetlony identyfikator Mxchip i "Konfiguracja".

### <a name="macos"></a>macOS

1. Umieść Mxchip tryb konfiguracji:

   Przytrzymaj naciśnięty przycisk A, a następnie Wypchnij, a następnie zwolnij przycisk resetowania. Na ekranie są wyświetlane "Konfiguracja".

2. Użyj `Cmd+P` do uruchomienia `task device-upload` można ustawić parametry połączenia, które są pobierane z `task cloud-provision` kroku.

### <a name="verify-upload-and-run"></a>Sprawdź, Przekaż i uruchom

Po ustawieniu parametrów połączenia, weryfikuje i przekazuje ją następnie uruchamia go. 

1. Weryfikowanie i przekazywanie szkic Arduino do Twojej Mxchip, rozpoczyna się programu VS Code:

   ![przekazywanie urządzeń](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. Mxchip rozruchu i uruchamiania kodu.

Możesz uzyskać "Błąd: AZ3166: Nieznany pakiet"komunikat o błędzie. Ten błąd występuje, gdy indeks pakietów tablicy nie jest odświeżany poprawnie. Aby rozwiązać ten problem, zapoznaj się z ["Nieznany pakiet" Błąd w często zadawanych PYTAŃ IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Projekt testowy

Po zainicjowaniu aplikacji kliknij release A przycisk, a następnie delikatnie wstrząsnąć tablicy Mxchip. To działanie powoduje pobranie losowe tweet zawierający hasztag, określony wcześniej. W ciągu kilku sekund tweet jest wyświetlane na ekranie Mxchip:

### <a name="arduino-application-initializing"></a>Trwa inicjowanie Arduino aplikacji...

![Arduino-application-initializing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Naciśnij klawisz na potrząsanie...

![Naciśnij klawisz A do wstrząsnąć](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Gotowy do potrząsania...

![Gotowe do wstrząsnąć](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Trwa przetwarzanie...

![Przetwarzanie](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Naciśnij klawisz B do odczytu...

![Naciśnij klawisz B do odczytu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Wyświetl losowe tweet...

![Wyświetlanie a losowe — tweetu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Naciśnij przycisk, A ponownie, a następnie wstrząsnąć dla nowego tweetu.
- Naciśnij przycisk B do przewijania reszty tweetu.

## <a name="how-it-works"></a>Jak to działa

![diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Szkic Arduino wysyła zdarzenie do usługi Azure IoT Hub. To zdarzenie jest wyzwalane aplikacji usługi Azure Functions. Aplikacja usługi Azure Functions zawiera logikę umożliwiającą połączenie interfejsu API w usłudze Twitter i pobranie tweet. Go następnie opakowywany tekst tweetu C2D komunikat (z chmury do urządzenia) i wysyła je z powrotem do urządzenia.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcjonalnie: Użyj własnych token elementu nośnego usługi Twitter

Do celów testowych, ten przykładowy projekt używa wstępnie skonfigurowane token elementu nośnego usługi Twitter. Istnieje jednak [ograniczania liczby wywołań](https://dev.twitter.com/rest/reference/get/search/tweets) dla każdego konta usługi Twitter. Jeśli chcesz wziąć pod uwagę przy użyciu własnych tokenu, wykonaj następujące czynności:

1. Przejdź do [portalu deweloperów w usłudze Twitter](https://dev.twitter.com/) zarejestrować nową aplikację usługi Twitter.

2. [Pobierz klucz klienta i wpisów tajnych konsumenta](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) aplikacji.

3. Użyj [niektóre narzędzia](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) do generowania tokenu elementu nośnego usługi Twitter z te dwa klucze.

4. W [witryny Azure portal](https://portal.azure.com/){: target = "_blank"}, zagłębieniem **grupy zasobów** i Znajdź funkcję platformy Azure (typ: Usługa App Service) dla projektu "Wstrząsnąć potrząsanie". Nazwa zawiera zawsze "potrząsania..." ciągu.

   ![Funkcja platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Zaktualizuj kod `run.csx` w ramach **Funkcje > shakeshake cs** przy użyciu własnych tokenu:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Zapisz plik, a następnie kliknij przycisk **Uruchom**.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jak rozwiązać problemy lub Wyraź swoją opinię. 

### <a name="problems"></a>Problemy

Jednym z problemów można uzyskać, jeśli na ekranie są wyświetlane "No Tweetami" podczas każdego kroku zostało uruchomione pomyślnie. Ten warunek zwykle ma to miejsce podczas pierwszego wdrażania i uruchom aplikację przykładową, ponieważ aplikacja funkcji wymaga dowolne miejsce z kilku sekund na możliwie jak jedną minutę na zimno aplikacji. 

Lub uruchamiając kod, istnieją pewne blips, które powodują ponowne uruchomienie aplikacji. Jeśli ten stan występuje, aplikacji urządzenia można uzyskać limitu czasu dla pobierania tweetu. W takim przypadku można spróbować jedną lub obie te metody, aby rozwiązać ten problem:

1. Kliknij przycisk Resetuj na Mxchip ponowne uruchomienie aplikacji urządzenia.

2. W [witryny Azure portal](https://portal.azure.com/)Znajdź utworzoną aplikację usługi Azure Functions i uruchom go ponownie:

   ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Opinia

Jeśli występują inne problemy, zapoznaj się [często zadawane pytania IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć urządzenie Mxchip Twój akcelerator rozwiązań Azure IoT zdalne monitorowanie i pobierać tweet, Oto zalecane kolejne kroki:

* [Omówienie usługi Azure akcelerator rozwiązań IoT zdalnego monitorowania](https://docs.microsoft.com/azure/iot-suite/)