---
title: Rozpoczynanie pracy z zestawem SDK urządzenia mowy | Dokumentacja firmy Microsoft
description: Wymagania wstępne i instrukcje na temat rozpoczynania pracy z zestawem SDK urządzenia mowy.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 6cc5ff1c532d67c48beac1a2a10d034f5d9d7501
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349720"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Rozpoczynanie pracy z zestawem SDK urządzenia mowy

W tym artykule opisano sposób konfigurowania komputerze i Twojej mowy urządzenia development kit opracowywanie urządzeń z obsługą mowy przy użyciu zestawu SDK urządzenia mowy. Następnie zostanie tworzenie i wdrażanie przykładowej aplikacji na urządzeniu. 

Kod źródłowy dla przykładowej aplikacji jest zawarta w zestawie SDK urządzenia mowy oraz [dostępne w witrynie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem programowanie z zestawem SDK urządzenia mowy, zbieranie informacji i oprogramowania, które będą potrzebne.

* Uzyskaj zestawu deweloperskiego [z Roobo](http://ddk.roobo.com/). Zestawy są dostępne z konfiguracjami tablicy liniowego lub cykliczne mikrofon; Wybierz właściwy dla potrzeb.

    |Projektowanie konfiguracji zestawu|Osoby mówiącej lokalizacji|
    |-----------------------------|------------|
    |Cykliczne|Dowolnym kierunku z urządzenia|
    |Liniowe|Przed urządzenia|

* Uzyskać najnowszą wersję zestawu SDK urządzenia mowy, wraz z Android przykładowej aplikacji, z zestawu SDK urządzenia mowy [pobrania](https://shares.datatransfer.microsoft.com/). Wyodrębnij plik ZIP do folderu lokalnego (takie jak `C:\SDSDK`).

* Zainstaluj [Android Studio](https://developer.android.com/studio/) i [Vysor](http://vysor.io/download/) na komputerze.

* Uzyskaj usługi mowy [klucza subskrypcji](get-started.md). Uzyskaj bezpłatną 30-dniową wersją próbną lub Uzyskaj klucz z pulpitu nawigacyjnego platformy Azure.

* Jeśli chcesz użyć konwersji rozpoznawania mowy usługi subskrybuje [usługi języka opis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) i [uzyskać klucz subskrypcji](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Możesz [utworzyć prosty model LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) lub użyj przykładu modelu LUIS `LUIS-example.json`, dostępne z zestawu SDK urządzenia mowy [pobrania](https://shares.datatransfer.microsoft.com/). Przekaż plik JSON modelu do [LUIS portal](https://www.lui.ai/applications) , klikając **importu nową aplikację** i wybierając plik JSON.

## <a name="set-up-the-development-kit"></a>Skonfiguruj zestaw deweloperski

1. Podłącz zasilacz zestaw deweloperski. Wskaźnik zielono powinien podświetlony w górnym tablicy.

1. Zestaw deweloperski nawiązać połączenia z komputerem za pomocą mini kabla USB.

    ![Łączenie z zestawem deweloperów](media/speech-devices-sdk/qsg-1.jpg)

1. Orientacja odpowiednio z zestawu deweloperskiego.

    |Projektowanie konfiguracji zestawu|Orientacja|
    |-----------------------------|------------|
    |Cykliczne|Pionowo z mikrofonów ukierunkowane limitu|
    |Liniowe|Po z mikrofonów przodu (pokazana poniżej)|

    ![Orientacja zestawu liniowej deweloperów](media/speech-devices-sdk/qsg-2.jpg)

1. Instalowanie certyfikatów i plik tabeli wznawiania word (— słowo kluczowe), a także Ustaw uprawnienia urządzenia dźwiękowego. Wpisz następujące polecenia w oknie poleceń.

    > [!NOTE]
    > Te polecenia korzystają z systemu Android mostka debugowania, `adb.exe`, który jest częścią instalacji programu Android Studio. To narzędzie można znaleźć w `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Można dodać ten katalog do ścieżki dokonanie wygodniejsze do wywołania `adb`. W przeciwnym razie należy określić pełną ścieżkę do instalacji `adb.exe` w każdym poleceniu wywołująca `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Wyciszanie mikrofonu i osoby mówiącej Twojego komputera. W ten sposób można się, że korzystasz z mikrofonów zestaw deweloperski i przypadkowo nie spowoduje wyzwolenia urządzenia w usłudze audio z komputera.
    
1.  Uruchom Vysor na tym komputerze.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Urządzenia powinien być wyświetlany w obszarze "Wybierz urządzenie". Kliknij przycisk **widoku** przycisk obok niej. 
 
1.  Połącz się z siecią bezprzewodową, klikając **ustawienia**, następnie **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>Uruchom przykładową aplikację

Aby uruchomić testy Roobo i sprawdź poprawność ustawień development kit, kompilacji i zainstalować przykładową aplikację.

1.  Uruchom program Android Studio.

1.  Wybierz, aby otworzyć istniejący projekt Android Studio.

    ![Android studio — Otwórz istniejący projekt](media/speech-devices-sdk/qsg-5.png)
 
1.  Przejdź do `C:\SDSDK\Android-Sample-Release\example`, następnie kliknij przycisk **OK** otworzyć przykładowy projekt.
 
1.  Dodaj klucz subskrypcji mowy do kodu źródłowego. Jeśli chcesz wypróbować konwersji rozpoznawania również dodać Twojego [języka opis usługi](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) identyfikatora klucza subskrypcji i aplikacji 

    Klucze i informacje o aplikacji zawiera następujące wiersze w pliku źródłowym `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Word wznawiania domyślne (— słowo kluczowe) jest "Komputer".  Jeśli chcesz, możesz jednego z innych podane spróbować wake wyrazy "Maszyna" i "Asystenta". Pliki zasobów dla tych alternatywnych słów można znaleźć w zestawie SDK urządzenia mowy w folderze "— słowo kluczowe". Na przykład `C:\SDSDK\Android-Sample-Release\keyword\Computer` zawiera pliki używane dla "Komputer".

    Możesz także [utworzyć słowo niestandardowego](speech-devices-sdk-create-kws.md).

    Aby zainstalować word żądaną wznawiania:
 
    * Utwórz `keyword` folderu w folderze \data\ na urządzeniu, uruchamiając następujące polecenia w oknie polecenia.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Skopiuj pliki `kws.table`, `kws_g.fst`, `kws_k.fst`, i `words_kw.txt`) do folderu \data\keyword\ urządzenia. Uruchom następujące polecenia w oknie polecenia o.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Odwoływać się do tych plików w przykładowej aplikacji. Znajdź następujące wiersze do `MainActivity.java`. Upewnij się, że określone słowo kluczowe jest używane są i że ścieżka wskazuje `kws.table` pliku, który jest przypisany do urządzenia.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > W swoim własnym kodem, można użyć `kws.table` plik, aby utworzyć wystąpienie modelu — słowo kluczowe i uruchomić następujący rozpoznawania.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Zaktualizuj następujące wiersze zawierające ustawienia geometrii tablicy mikrofon.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Zmienna|Znaczenie|Dostępne wartości|
    |--------|-------|----------------|
    |`DeviceGeometry`|Konfiguracji fizycznej micznych|`Circular6+1` dla deweloperów cykliczne zestawu|
    ||| `Linear4` dla deweloperów liniową zestawu|
    |`SelectedGeometry`|Konfiguracja micznych oprogramowania|`Circular6+1` dla deweloperów cykliczne zestawu przy użyciu wszystkich mikrofony|
    |||`Circular3+1` dla deweloperów cykliczne zestawu przy użyciu czterech mikrofony|
    |||`Linear4` dla deweloperów liniową zestawu przy użyciu wszystkich mikrofony|
    |||`Linear2` dla deweloperów liniową zestawu przy użyciu dwóch mikrofony|


1.  Tworzenie aplikacji, wybierając **Uruchom "aplikację"** z menu wykonywania. Zostanie wyświetlone okno dialogowe Wybierz cel wdrożenia. Wybierz urządzenie, a następnie kliknij przycisk **OK** do wdrożenia aplikacji na urządzeniu.

    ![Wybierz cel wdrożenia](media/speech-devices-sdk/qsg-7.png)
 
1.  Rozpoczyna się SDK urządzenia mowy przykładowej aplikacji wyświetlanie opcji wymienionych w tym miejscu.

    ![Przykładowa aplikacja urządzenia mowy](media/speech-devices-sdk/qsg-8.png)

1. Odtwórz z nim!

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli certyfikat błędy podczas korzystania z usługi rozpoznawania mowy, upewnij się, że urządzenie ma prawidłową datę i godzinę.

Więcej programowanie informacji można uzyskać w Roobo [Podręcznik programowania](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo udostępnia narzędzia, która przechwytuje wszystkie audio w pamięci, które mogą ułatwić rozwiązywanie problemów z dźwiękiem flash. Wersja narzędzia jest dostępna dla każdej development kit konfiguracji. Wybierz swoje urządzenie w [lokacji Roobo](http://ddk.roobo.com/), następnie kliknij przycisk **ROOBO narzędzia** łącze umieszczone u dołu strony.
