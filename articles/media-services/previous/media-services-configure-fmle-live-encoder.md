---
title: Konfigurowanie kodera FMLE wysłać strumień na żywo o pojedynczej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób konfigurowania kodera interfejsu Flash Media na żywo kodera (FMLE) do wysłania do kanałów AMS, które są włączone dla kodowanie na żywo o pojedynczej szybkości transmisji.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 055decd689e260a69651c5a3e1ce3f3f78b67340
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj koder FMLE, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Elemental na żywo](media-services-configure-elemental-live-encoder.md)
> * [tricaster](media-services-configure-tricaster-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

W tym artykule przedstawiono sposób konfigurowania [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) kodera (FMLE) do wysyłania strumienia pojedynczej szybkości transmisji bitów AMS kanałów, które są włączone kodowanie na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) za pomocą narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze z systemem Windows. Jeśli na Mac lub Linux, użyj portalu Azure do utworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

W tym samouczku opisano, przy użyciu AAC. FMLE nie obsługuje jednak AAC domyślnie. Musisz kupić wtyczki AAC kodowania przykład MainConcept: [AAC wtyczki](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Wymagania wstępne
* [Tworzenie konta usługi Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, brak punktu końcowego przesyłania strumieniowego uruchomiona. Aby uzyskać więcej informacji, zobacz [zarządzanie punktami końcowymi przesyłania strumieniowego w konta usługi Media Services](media-services-portal-manage-streaming-endpoints.md)
* Zainstaluj najnowszą wersję pakietu [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) narzędzia.
* Uruchom narzędzie i połącz się z kontem AMS.

## <a name="tips"></a>Porady
* Jeśli to możliwe, użyj połączenia internetowego hardwired.
* Regułą podczas określania wymaganiach odnośnie do przepustowości jest dwukrotnie przesyłania strumieniowego szybkości transmisji bitów. Chociaż nie jest to wymagane, ułatwia ograniczanie wpływu sieci przeciążona.
* Korzystając z koderów opartych na oprogramowaniu, zamknij wszystkie zbędne programy.

## <a name="create-a-channel"></a>Tworzenie kanału
1. W przy użyciu narzędzia AMSE, przejdź do **Live** , a następnie kliknij prawym przyciskiem myszy w obszarze kanału. Wybierz **Utwórz kanał...** z menu.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W ustawieniach kanału, wybierz **standardowe** dla opcji Live Encoding z protokołem wprowadzania ustawioną **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzyć kanał**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Należy pamiętać, że rozliczanie zaczyna się jak kanału przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stanów kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurowanie kodera FMLE
W tym samouczku są używane następujące ustawienia danych wyjściowych. Pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

**Wideo**:

* Koder-dekoder: H.264
* Profil: Wysoki (poziom 4.0)
* Szybkość transmisji bitów: 5000 KB/s
* Klatki kluczowej: 2 sekundy (60 sekund)
* Szybkość klatek: 30

**Audio**:

* Koder-dekoder: AAC (LC —)
* Szybkość transmisji bitów: 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji
1. Przejdź do interfejsu Flash Media na żywo kodera (FMLE) na maszynie używane.

    Interfejs jest jednej strony głównej ustawień. Zwróć uwagę na następujące ustawienia, aby zacząć korzystać z przesyłania strumieniowego przy użyciu FMLE zalecane.

   * Format: Szybkość klatek H.264: 30,00
   * Rozmiar wejściowe: 1280 x 720
   * Szybkość transmisji bitów: 5000 KB/s (może być określany na podstawie ograniczenia sieci)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Przy użyciu naprzemiennych źródeł, skontaktuj się z znacznikiem wyboru opcji "Opcji Usuń przeplot"
2. Wybierz ikoną klucza Format, należy te dodatkowe ustawienia:

   * Profil: Main
   * Poziom: 4.0
   * Częstotliwość klatki kluczowej: 2 sekundy.

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Ustawienie następujące istotne audio:

   * Format: AAC
   * Częstotliwość próbkowania: Hz 44100 b
   * Szybkość transmisji bitów: 192 kb/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Get kanału wejściowych adres URL, aby przypisać FMLE **punktu końcowego protokołu RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Gdy stan został zmieniony z **uruchamianie** do **systemem**, można uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiona, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do aktywowania przez **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowego adresu URL danych wejściowych**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Wklej te informacje w **FMS URL** pole sekcji danych wyjściowych i przypisz nazwę strumienia.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Nadmiarowości dodatkowe Powtórz te kroki do dodatkowej adresu URL danych wejściowych.
6. Wybierz przycisk **Połącz**.

> [!IMPORTANT]
> Przed kliknięciem przycisku **Connect**, możesz **musi** upewnij się, że kanał jest gotowy.
> Upewnij się również, nie należy pozostawiać kanału w stanie Gotowe bez wprowadzania wkład źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do przy użyciu narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania podglądu** i wybierz **z usługi Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Strumień jest widoczna w player, następnie koder został poprawnie skonfigurowany do nawiązania połączenia usługi AMS.

Jeśli błąd kanału powinna zostać zresetowana i dostosowane ustawienia kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykułu, aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Utwórz program
1. Po potwierdzeniu kanału odtwarzania, tworzenia programu. W obszarze **Live** w przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy w obszarze program i wybierz **utworzyć nowy Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Nazwa programu, a w razie potrzeby dostosuj **długość okna archiwum** (który domyślnie 4 godziny). Można także określić miejsce przechowywania lub pozostaw domyślnie.  
3. Sprawdź **teraz uruchomić Program** pole.
4. Kliknij przycisk **utworzyć Program**.  

    >[!NOTE]
    >Tworzenie programu zajmuje mniej czasu niż tworzenie kanału.
        
5. Po uruchomieniu programu potwierdzić odtwarzania przez kliknięcie prawym przyciskiem myszy program i przechodząc do **odtwarzania programach** , a następnie wybierając **z usługi Azure Media Player**.  
6. Po potwierdzeniu, ponownie kliknij prawym przyciskiem myszy program i wybierz **skopiuj dane wyjściowe adres URL do Schowka** (lub pobrać tych informacji z **programu informacji i ustawień** opcji z menu).

Strumień jest teraz gotowy do osadzonego w odtwarzacza lub dystrybuowane do odbiorców w celu wyświetlenia na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykułu, aby uzyskać wskazówki.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
