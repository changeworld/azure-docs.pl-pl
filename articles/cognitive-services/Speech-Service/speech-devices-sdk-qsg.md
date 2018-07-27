---
title: Wprowadzenie do zestawu Speech Devices SDK
description: Wymagania wstępne i instrukcje dla klientów zaczynających się od zestawu Speech Devices SDK.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 266315a731eec8a2c0ab0a880ce9e1db58331184
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283140"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Wprowadzenie do zestawu Speech Devices SDK

W tym artykule opisano sposób konfigurowania komputerze i usługi mowy urządzenia zestaw development kit dla tworzenia urządzenia obsługujące rozpoznawanie mowy, przy użyciu zestawu Speech Devices SDK. Następnie utworzysz i wdrożysz przykładową aplikację na urządzeniu. 

Kod źródłowy przykładowej aplikacji jest dołączony do zestawu Speech Devices SDK i jest również [dostępne w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tworzenia przy użyciu zestawu Speech Devices SDK, zbieranie informacji i oprogramowania, które będą potrzebne.

* Uzyskaj zestaw deweloperski [z Roobo](http://ddk.roobo.com/). Zestawy są dostępne w usłudze konfiguracji tablicy liniowego lub cykliczne mikrofonu. Wybierz właściwy dla potrzeb.

    |Tworzenie konfiguracji zestawu|Lokalizacja osoby mówiącej|
    |-----------------------------|------------|
    |Cykliczne|Dowolnym kierunku z urządzenia|
    |Liniowe|Przed urządzenia|

* Uzyskaj najnowszą wersję zestawu Speech Devices SDK, w tym dla systemu Android przykładową aplikację, z zestawu Speech Devices SDK [witryny pobierania plików](https://shares.datatransfer.microsoft.com/). Wyodrębnij plik ZIP do folderu lokalnego (takie jak `C:\SDSDK`).

* Zainstaluj [programu Android Studio](https://developer.android.com/studio/) i [Vysor](http://vysor.io/download/) na komputerze.

* Uzyskiwanie usługi mowy [klucz subskrypcji](get-started.md). Możesz uzyskać bezpłatnej 30-dniowej wersji próbnej lub uzyskać klucz z pulpitu nawigacyjnego platformy Azure.

* Jeśli chcesz użyć funkcja rozpoznawania celu usługi rozpoznawania mowy, Subskrybuj [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) i [uzyskać klucz subskrypcji](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Użytkownik może [Tworzenie prostego modelu usługi LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) lub użyj przykładu modelu usługi LUIS `LUIS-example.json`, która jest dostępna z zestawu Speech Devices SDK [witryny pobierania plików](https://shares.datatransfer.microsoft.com/). Przekaż swój model plik JSON do [portal usługi LUIS](https://www.luis.ai/home) , klikając **importowania Nowa aplikacja** i wybierając plik JSON.

## <a name="set-up-the-development-kit"></a>Skonfiguruj zestaw development kit

1. Podłącz zasilacz zestaw development kit. Wskaźnik zielono powinien dostarczone w górnym tablicy.

1. Zestaw development kit nawiązać połączenia z komputerem za pomocą mini kabla USB.

    ![Łączenie zestawu dla deweloperów](media/speech-devices-sdk/qsg-1.jpg)

1. Orientacja odpowiednio z zestawu SDK.

    |Tworzenie konfiguracji zestawu|Orientacja|
    |-----------------------------|------------|
    |Cykliczne|Prawidłowa z mikrofon połączonego z limitu|
    |Liniowe|Stronie z mikrofon przodu (pokazana poniżej)|

    ![Orientacja kit liniowej dev](media/speech-devices-sdk/qsg-2.jpg)

1. Zainstaluj certyfikaty i plik tabeli wznawiania programu word (słowo kluczowe), a następnie ustaw uprawnienia urządzenia dźwiękowego. Wpisz następujące polecenia w oknie poleceń.

    > [!NOTE]
    > Te polecenia korzystają z mostka debugowania systemu Android, `adb.exe`, który jest częścią instalacji programu Android Studio. To narzędzie można znaleźć w `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Ten katalog można dodać do swojej ścieżki, aby był bardziej wygodne do wywołania `adb`. W przeciwnym razie należy określić pełną ścieżkę do instalacji programu `adb.exe` w każdego polecenia, który wywołuje `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Wyciszenia mikrofonu i głośników komputera PC. Dzięki temu można mieć się, że pracujesz z mikrofon zestaw development kit i przypadkowo nie spowoduje wyzwolenia urządzenia przy użyciu dźwięku z komputera.
    
1.  Uruchom Vysor na tym komputerze.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Urządzenia muszą być wymienione w obszarze "Wybierz urządzenie". Kliknij przycisk **widoku** przycisk obok niej. 
 
1.  Łączenie się z siecią bezprzewodową, klikając **ustawienia**, następnie **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>Uruchamianie przykładowej aplikacji

Aby uruchomić testy Roobo i weryfikowanie konfiguracji development kit, tworzenie i instalowanie przykładowej aplikacji.

1.  Uruchom program Android Studio.

1.  Wybierz otworzyć istniejący projekt Android Studio.

    ![Android studio — Otwórz istniejący projekt](media/speech-devices-sdk/qsg-5.png)
 
1.  Przejdź do `C:\SDSDK\Android-Sample-Release\example`, następnie kliknij przycisk **OK** otworzyć projektu w przykładzie.
 
1.  Dodaj klucz subskrypcji mowy do kodu źródłowego. Jeśli chcesz wypróbować rozpoznawanie intencji również dodać swoje [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klucz subskrypcji i aplikacji identyfikatora. 

    Klucze i informacje o aplikacji znajduje się w następujące wiersze w pliku źródłowym `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Aktywujące domyślne (słowo kluczowe) jest "Computer".  Jeśli chcesz, może być Wypróbuj jedną z inne podany wake słowa "Maszyna" i "Asystent". Pliki zasobów dla tych alternatywnych słów znajdują się w zestawu Speech Devices SDK w folderze "— słowo kluczowe". Na przykład `C:\SDSDK\Android-Sample-Release\keyword\Computer` zawiera pliki używane dla "Computer".

    Możesz również [utworzyć niestandardowe słowo aktywujące](speech-devices-sdk-create-kws.md).

    Aby zainstalować odpowiednią aktywujące:
 
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
    
    * Odwoływać się do tych plików w przykładowej aplikacji. Znajdź następujące wiersze w `MainActivity.java`. Upewnij się, że określone słowo kluczowe jest używane są i że ścieżka wskazuje `kws.table` pliku, który jest przypisany do urządzenia.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > We własnym kodzie, można użyć `kws.table` pliku, aby utworzyć wystąpienie modelu — słowo kluczowe i uruchomić rozpoznawanie w następujący sposób.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Zaktualizuj następujące wiersze, zawierający ustawienia geometrii tablicy mikrofonu.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Zmienna|Znaczenie|Dostępne wartości|
    |--------|-------|----------------|
    |`DeviceGeometry`|Konfiguracji fizycznej mic|`Circular6+1` Aby uzyskać zestaw deweloperski cykliczne|
    ||| `Linear4` Aby uzyskać deweloperski liniowego|
    |`SelectedGeometry`|Konfiguracja kontroli integralności uprawnień oprogramowania|`Circular6+1` Aby uzyskać zestaw deweloperski cykliczne przy użyciu mikrofonu wszystkie|
    |||`Circular3+1` Aby uzyskać zestaw deweloperski cykliczne przy użyciu czterech mikrofonu|
    |||`Linear4` Aby liniowej zestaw deweloperski za pomocą wszystkich mikrofonu|
    |||`Linear2` Aby liniowej zestaw deweloperski za pomocą dwóch mikrofonu|


1.  Kompiluj aplikację, wybierając **Uruchom "aplikację"** menu wykonywania. Pojawi się okno dialogowe Wybierz cel wdrożenia. Wybierz swoje urządzenie, a następnie kliknij przycisk **OK** wdrożyć aplikację na urządzeniu.

    ![Wybierz cel wdrożenia](media/speech-devices-sdk/qsg-7.png)
 
1.  Przykładowa aplikacja zestawu Speech Devices SDK rozpocznie, wyświetlanie opcji wymienionych w tym miejscu.

    ![przykładowej aplikacji urządzenia mowy](media/speech-devices-sdk/qsg-8.png)

1. Poeksperymentuj z nim!

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli otrzymujesz błędy certyfikatów podczas korzystania z usługi rozpoznawania mowy, upewnij się, że urządzenie ma poprawną datę i godzinę.

Aby uzyskać więcej informacji rozwoju, zobacz firmy Roobo [Podręcznik programowania](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo udostępnia narzędzia, która przechwytuje wszystkie audio w pamięci, które może pomóc w rozwiązywaniu problemów audio flash. Wersja narzędzia towarzyszy konfiguracjami development kit. Wybierz swoje urządzenie w [witryny Roobo](http://ddk.roobo.com/), następnie kliknij przycisk **narzędzia ROOBO** widocznego u dołu strony.
