---
title: Konfigurowanie kodera NewTek TriCaster wysłać strumień na żywo o pojedynczej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób konfigurowania kodera na żywo Tricaster do wysłania do kanałów AMS, które są włączone dla kodowanie na żywo o pojedynczej szybkości transmisji.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 8084f32ac8cc2184d93796468ad66fb73398e876
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788914"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj koder NewTek TriCaster, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów
> [!div class="op_single_selector"]
> * [tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental na żywo](media-services-configure-elemental-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

W tym artykule przedstawiono sposób konfigurowania [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) kodera na żywo do wysyłania strumienia pojedynczej szybkości transmisji bitów AMS kanałów, które są włączone kodowanie na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) za pomocą narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze z systemem Windows. Jeśli na Mac lub Linux, użyj portalu Azure do utworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Korzystając Tricaster wysyłanie wkład do kanałów AMS, które są włączone kodowanie na żywo, może istnieć błędami występującymi audio/wideo w zdarzenia na żywo w przypadku określonych funkcji Tricaster, takie jak szybka Wycinanie między źródła danych lub przełączanie z typu. Zespół AMS działa w rozwiązaniu tych problemów, do tego czasu, nie zaleca się korzystanie z tych funkcji.
>
>

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

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W ustawieniach kanału, wybierz **standardowe** dla opcji Live Encoding z protokołem wprowadzania ustawioną **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzyć kanał**.

   ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Rozliczanie zaczyna się, jak kanału przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stanów kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_tricaster_rtmp></a>Konfigurowanie kodera NewTek TriCaster
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
1. Utwórz nową **NewTek TriCaster** projektu w zależności od tego, jakie wideo źródło danych wejściowych jest używany.
2. Raz w ramach tego projektu należy znaleźć **strumienia** przycisk, a następnie kliknij koło zębate ikonę obok niej dostęp do menu konfiguracji strumienia.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Po otwarciu menu, kliknij przycisk **nowy** pozycji połączenia. Po wyświetleniu monitu dla typu połączenia, wybierz **Adobe Flash**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kliknij przycisk **OK**.
5. Teraz można zaimportować profil FMLE, klikając strzałkę listy rozwijanej w obszarze **przesyłania strumieniowego profilu** i przechodząc do **Przeglądaj**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Przejdź do gdzie skonfigurowanego profilu FMLE został zapisany.
7. Wybierz go, a następnie naciśnij klawisz **OK**.

    Po przekazaniu profilu, przejdź do następnego kroku.
8. Get kanału wejściowych adres URL, aby przypisać Tricaster **punktu końcowego protokołu RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Gdy stan został zmieniony z **uruchamianie** do **systemem**, można uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiona, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do aktywowania przez **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowego adresu URL danych wejściowych**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Wklej te informacje w **lokalizacji** pole w obszarze **Flash Server** w projekcie Tricaster. Również przypisać nazwę strumienia w **Identyfikator strumienia** pola.

    Jeśli informacje strumień został dodany do profilu FMLE, jego można również zaimportować do tej sekcji, klikając **importowanie ustawień**, przechodząc do zapisanego profilu FMLE i klikając pozycję **OK**. Odpowiednie pola Flash serwera należy wypełnić o informacje z FMLE.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Gdy skończysz, kliknij przycisk **OK** w dolnej części ekranu. Po zakończeniu dane wejściowe audio i wideo w Tricaster rozpoczęcia przesyłania strumieniowego AMS, klikając **strumienia** przycisku.

     ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Przed kliknięciem przycisku **strumienia**, możesz **musi** upewnij się, że kanał jest gotowy.
> Upewnij się również, nie należy pozostawiać kanału w stanie Gotowe bez wprowadzania wkład źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu
Przejdź do przy użyciu narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania podglądu** i wybierz **z usługi Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Strumień jest widoczna w player, następnie koder został poprawnie skonfigurowany do nawiązania połączenia usługi AMS.

Jeśli błąd kanału należy zresetować i dostosowane ustawienia kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykułu, aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Utwórz program
1. Po potwierdzeniu kanału odtwarzania, tworzenia programu. W obszarze **Live** w przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy w obszarze program i wybierz **utworzyć nowy Program**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
