---
title: Pobieranie wiadomości Twitter z usługi Azure Functions | Dokumentacja firmy Microsoft
description: Czujnik umożliwia wykrywanie potrząsając i używania usługi Azure Functions można znaleźć losowe tweet z hasztagiem, który określisz
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: 5a4605a1668d25d5a90dc7d7873efa83ddc767ff
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752687"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Potrząsanie, potrząsanie dla Tweet — pobierania wiadomości Twitter z usługi Azure Functions!

W tym projekcie Dowiedz się jak używać czujnik wywołania zdarzenia przy użyciu usługi Azure Functions. Aplikacja pobiera losowe tweet z #hashtag skonfigurowanych w Twojej szkicu Arduino. Wyświetla tweet na ekranie zestaw deweloperski.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje zestaw deweloperski ma podłączony do sieci Wi-Fi.
* Przygotuj Środowisko deweloperskie.

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować za pomocą jednej z następujących metod:

* Aktywuj [bezpłatne 30-dniowej wersji próbnej konto Microsoft Azure](https://azure.microsoft.com/free/)
* Oświadczenie użytkownika [Azure środki](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) w przypadku subskrybentów MSDN lub Visual Studio

## <a name="open-the-project-folder"></a>Otwórz folder projektu

### <a name="start-vs-code"></a>Kod uruchomienia programu VS

- Upewnij się, że Twoje zestaw deweloperski jest podłączony do komputera.
- Uruchom kod programu VS.
- Zestaw deweloperski można połączyć się z komputerem.

> [!NOTE]
> Podczas uruchamiania kodu programu VS, może zostać wyświetlony komunikat o błędzie, który Arduino IDE lub pakietu pokrewne tablicy nie może zostać znalezione. Jeśli ten błąd występuje, zamknij kodzie VS, a następnie uruchom ponownie Arduino IDE. VS kod powinien teraz poprawnie zlokalizować ścieżkę Arduino IDE.

### <a name="open-arduino-examples-folder"></a>Otwórz folder Arduino przykłady

Po lewej stronie rozwiń **przykłady ARDUINO** przejdź do **przykłady MXCHIP AZ3166 > AzureIoT**i wybierz **ShakeShake**. Otwiera nowe okno programu VS kodu z folderu projektu w nim.  

> [!NOTE]
> Jeśli nie widzisz sekcji MXCHIP AZ3166, upewnij się, urządzenie jest poprawnie dołączone i uruchom ponownie program Visual Studio Code.  

![Mini solution — przykłady](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

> [!NOTE]
> Przykład można również otworzyć z palety polecenia. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) do otwarcia palety polecenia, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="provision-azure-services"></a>Udostępnianie usług platformy Azure

W oknie rozwiązania, uruchom zadanie za pośrednictwem `Ctrl+P` (macOS: `Cmd+P`), wprowadzając `task cloud-provision`.

W terminalu VS kodu interaktywnego wiersza polecenia prowadzi użytkownika przez Inicjowanie obsługi administracyjnej wymaganych usług Azure:

![Zainicjuj obsługę chmury](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Jeśli strony w stan ładowania, zawiesza się podczas próby logowanie do platformy Azure, zapoznaj się to [krok — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modyfikowanie #hashtag

Otwórz `ShakeShake.ino` i wyszukaj ten wiersz kodu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Zastąp ciąg `iot` w nawiasy klamrowe z Twojej preferowanych hasztagiem. Zestaw deweloperski pobiera później losowe tweet zawierający hasztagiem, które określisz w tym kroku.

## <a name="deploy-azure-functions"></a>Wdrażanie usługi Azure Functions

Użyj `Ctrl+P` (macOS: `Cmd+P`) do uruchamiania `task cloud-deploy` aby rozpocząć wdrażanie usługi Azure Functions kodu:

![Wdrażanie w chmurze](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Czasami funkcji platformy Azure mogą nie działać poprawnie. Aby rozwiązać ten problem, jeśli występuje on, sprawdź [krok — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload`.
2. Terminal wyświetli monit o wprowadzenie trybu konfiguracji. W tym celu:

   * Przytrzymaj naciśnięty przycisk A
   * Naciśnij i zwolnij przycisk reset.

3. Na ekranie zostanie wyświetlony identyfikator zestaw deweloperski i "Konfiguracja".
4. To ustawienie ciągu połączenia, które są pobierane z `task cloud-provision` kroku.
5. Kod programu VS, a następnie uruchamia Weryfikowanie i przekazywanie Arduino szkicu do Twojej zestaw deweloperski: ![przekazywania urządzenia](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

> [!NOTE]
> Mogą wystąpić "Błąd: AZ3166: Nieznany pakiet" komunikat o błędzie. Ten błąd występuje, gdy indeks tablicy pakietu nie jest odświeżany poprawnie. Aby rozwiązać ten problem, sprawdź [krok — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. Ustawić zestaw deweloperski trybu konfiguracji: przytrzymaj naciśnięty przycisk A, a następnie wypychania i zwolnij przycisk resetowania. Na ekranie "Konfiguracja".
2. Użyj `Cmd+P` do uruchomienia `task device-upload` można ustawić parametry połączenia, które są pobierane z `task cloud-provision` kroku.
3. Kod programu VS, a następnie uruchamia Weryfikowanie i przekazywanie Arduino szkicu do Twojej zestaw deweloperski: ![przekazywania urządzenia](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

> [!NOTE]
> Mogą wystąpić "Błąd: AZ3166: Nieznany pakiet" komunikat o błędzie. Ten błąd występuje, gdy indeks tablicy pakietu nie jest odświeżany poprawnie. Aby rozwiązać ten problem, sprawdź [krok — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Projekt testowy

Po zainicjowaniu aplikacji kliknij przycisk Zwolnij przycisk A, a następnie ostrożnie potrząsanie tablicy zestaw deweloperski. To działanie powoduje pobranie tweet losowych, zawierającą hasztagiem, określone wcześniej. W ciągu kilku sekund tweet wyświetlane na ekranie zestaw deweloperski:

### <a name="arduino-application-initializing"></a>Inicjowanie aplikacji Arduino...
![Arduino-application-initializing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Naciśnij klawisz A Aby potrząsanie...
![Naciśnij klawisz A-do-potrząsania powoduje](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Gotowy do potrząsanie...
![Gotowe do potrząsania powoduje](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Trwa przetwarzanie...
![Przetwarzanie](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Naciśnij klawisz B do odczytu...
![Naciśnij klawisz B do odczytu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Wyświetl losowe tweet...
![Wyświetl a losowe tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Naciśnij przycisk A ponownie, a następnie potrząsanie dla nowych tweet.
- Naciśnij przycisk B do przewijania reszty tweet.

## <a name="how-it-works"></a>Jak to działa

![Diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Rysunek Arduino wysyła zdarzenia do Centrum IoT Azure. To zdarzenie jest wyzwalane aplikacji usługi Azure Functions. Aplikacji funkcji Azure zawiera logikę do nawiązania połączenia interfejsu API w serwisie Twitter i pobierania tweet. Następnie zawija tekst tweet do C2D wiadomości (chmura urządzenie) i wysyła go z powrotem do urządzenia.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcjonalnie: Użyj własny token elementu nośnego usługi Twitter

Do celów testowych, ten przykładowy projekt używa wstępnie skonfigurowane token elementu nośnego usługi Twitter. Istnieje jednak [limit szybkości](https://dev.twitter.com/rest/reference/get/search/tweets) dla każdego konta usługi Twitter. Jeśli chcesz wziąć pod uwagę przy użyciu własnego tokenu, wykonaj następujące kroki:

1. Przejdź do [portalu dla deweloperów w usłudze Twitter](https://dev.twitter.com/) zarejestrować nową aplikację usługi Twitter.

2. [Pobierz klucz klienta i kluczy tajnych klienta](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) aplikacji.

3. Użyj [niektóre narzędzia](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) do wygenerowania tokena elementu nośnego usługi Twitter z tych dwóch kluczy.

4. W [portalu Azure](https://portal.azure.com/){: docelowy = "_blank"}, pobranie do **grupy zasobów** i Znajdź funkcji platformy Azure (typ: usługi aplikacji) dla projektu "Potrząsania powoduje potrząsanie". Nazwa zawiera zawsze "potrząsanie..." ciąg.
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Zaktualizuj kod `run.csx` w **Funkcje > shakeshake cs** z własnych tokenu:
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Zapisz plik i kliknij przycisk **Uruchom**.


## <a name="problems-and-feedback"></a>Problemy i opinie

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>Na ekranie "Nr Tweetów" podczas każdego kroku zostało uruchomione pomyślnie

Ta sytuacja zwykle odbywa się po raz pierwszy wdrożeniem i uruchomieniem przykładowej aplikacji funkcji wymaga dowolnym z kilku sekund na jak jedną minutę na zimny start aplikacji. Lub uruchamiając kod, są niektóre blips, które powodują ponowne uruchomienie aplikacji. Jeśli ten stan występuje, aplikacji urządzenia można uzyskać czasu pobierania tweet. W takim przypadku można spróbować jedną lub obie te metody rozwiązywania tego problemu:

1. Przycisk resetowania na zestaw deweloperski, aby ponownie uruchomić aplikację urządzenia.

2. W [portalu Azure](https://portal.azure.com/), odnaleźć aplikacji usługi Azure Functions, możesz utworzyć i uruchomić go ponownie: ![azure funkcja ponownego uruchamiania](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Opinia

Jeśli występują inne problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami z następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak zestaw deweloperski urządzenie podłączone do sieci Azure IoT zdalne monitorowanie akcelerator rozwiązań i pobrać tweet, Oto Sugerowane następne kroki:

* [Omówienie usługi Azure akceleratora rozwiązania IoT monitorowania zdalnego](https://docs.microsoft.com/azure/iot-suite/)
