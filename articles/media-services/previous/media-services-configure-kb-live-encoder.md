---
title: "Konfigurowanie kodera Haivision KB wysłać strumień na żywo o pojedynczej szybkości transmisji bitów na platformie Azure | Dokumentacja firmy Microsoft"
description: "W tym temacie przedstawiono sposób konfigurowania kodera na żywo Haivision KB do wysłania do kanałów AMS, które są włączone dla kodowanie na żywo o pojedynczej szybkości transmisji."
services: media-services
documentationcenter: 
author: dbgeorge
manager: vsood
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera na żywo Haivision KB, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów
> [!div class="op_single_selector"]
> * [Elemental na żywo](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

W tym temacie przedstawiono sposób konfigurowania [kodera na żywo Havision KB](https://www.haivision.com/products/kb-series/) kodera do wysyłania strumienia pojedynczej szybkości transmisji bitów AMS kanałów, które są włączone kodowanie na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) za pomocą narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze z systemem Windows. Jeśli na Mac lub Linux, użyj portalu Azure do utworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Wymagania wstępne
*   Dostęp do koder Haivision KB systemem SW w wersji 5.01 lub nowszej.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Określ nazwę kanału pole opisu jest opcjonalne. W ustawieniach kanału, wybierz **standardowe** dla opcji Live Encoding z protokołem wprowadzania ustawioną **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest. Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.
3. Kliknij przycisk **utworzyć kanał**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurowanie kodera Haivision KB
W tym samouczku są używane następujące ustawienia danych wyjściowych. Pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

Wideo:
-   Koder-dekoder: H.264
-   Profil: Wysoki (poziom 4.0)
-   Szybkość transmisji bitów: 5000 KB/s
-   Klatki kluczowej: 2 sekundy (60 sekund)
-   Szybkość klatek: 30

Audio:
-   Koder-dekoder: AAC (LC —)
-   Szybkość transmisji bitów: 192 kb/s
-   Częstotliwość próbkowania: 44,1 kHz

## <a name="configuration-steps"></a>Kroki konfiguracji
1.  Zaloguj się interfejsie użytkownika Haivision KB.
2.  Polecenie **przycisk Menu** w centrum sterowania kanału i wybierz **Dodaj kanał**  
    ![Zrzut 2017-08-14 na 9.15.09 ekranu AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Typ **Nazwa kanału** w nazwie pola i kliknij przycisk Dalej.  
    ![Zrzut 2017-08-14 na 9.19.07 ekranu AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Wybierz **źródła danych wejściowych kanału** z **źródła danych wejściowych** listy rozwijanej i kliknij przycisk Dalej.
    ![Zrzut 2017-08-14 na 9.20.44 ekranu AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z **szablonu koder** listy rozwijanej wybierz **H264-720-AAC-192** i kliknij przycisk Dalej.
    ![Zrzut 2017-08-14 na 9.23.15 ekranu AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Z **wybierz nowe dane wyjściowe** listy rozwijanej wybierz **RTMP** i kliknij przycisk Dalej.  
    ![Zrzut 2017-08-14 na 9.27.51 ekranu AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Z **kanał wyjściowy** okna, wypełnij informacje strumienia Azure. Wklej **RTMP** łącza z konfiguracji początkowej kanału w **serwera** obszaru. W **nazwy wyjściowego** obszaru wpisz nazwę kanału. W obszarze strumienia nazwę szablonu nazwy strumienia użyć szablonu RTMPStreamName_ % video_bitrate %.
    ![Zrzut 2017-08-14 na 9.33.17 ekranu AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kliknij przycisk Dalej, a następnie kliknij pozycję gotowe.
9.  Kliknij przycisk **przycisk Odtwórz** zainicjował kanału kodera.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Podczas odtwarzania testu
Przejdź do przy użyciu narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu Umieść kursor nad odtwarzania podglądu, a następnie wybierz z usługi Azure Media Player.

Strumień jest widoczna w player, następnie koder został poprawnie skonfigurowany do nawiązania połączenia usługi AMS.

Jeśli błąd kanału powinna zostać zresetowana i dostosowane ustawienia kodera. Zobacz artykuł dotyczący rozwiązywania problemów, aby uzyskać wskazówki.

## <a name="create-a-program"></a>Utwórz program
1.  Po potwierdzeniu kanału odtwarzania, tworzenia programu. Na karcie na żywo za pomocą narzędzia AMSE, kliknij prawym przyciskiem myszy w obszarze program i wybierz opcję Utwórz nowy Program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nazwa programu, a w razie potrzeby Dostosuj długość okna archiwum (który domyślnie 4 godziny). Można także określić miejsce przechowywania lub pozostaw domyślnie.
2.  Pole teraz Program początek wyboru.
3.  Kliknij przycisk Utwórz Program.
4.  Po uruchomieniu programu Potwierdź odtwarzania, klikając prawym przyciskiem myszy program i nawigowania do odtwarzania programy i wybierając z usługi Azure Media Player.
5.  Po potwierdzeniu, ponownie kliknij prawym przyciskiem myszy program i wybierz polecenie Kopiuj dane wyjściowe adres URL do Schowka lub pobrać te informacje z informacje o programie i ustawienia opcji z menu.

Strumień jest teraz gotowy do osadzonego w odtwarzacza lub dystrybuowane do odbiorców w celu wyświetlenia na żywo.

> [!NOTE]
> Tworzenie programu zajmuje mniej czasu niż tworzenie kanału.