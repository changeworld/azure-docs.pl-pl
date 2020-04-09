---
title: Tworzenie strumienia na żywo usługi Azure Media Services
description: Dowiedz się, jak utworzyć transmisję na żywo usługi Azure Media Services przy użyciu portalu i wirecastu
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: 459f11844f873a911b3e5702e8c768b1cd22e504
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985001"
---
# <a name="create-an-azure-media-services-live-stream"></a>Tworzenie strumienia na żywo usługi Azure Media Services

Ten przewodnik Szybki start pomoże Ci utworzyć transmisję na żywo usługi Azure Media Services przy użyciu portalu Azure i usługi Telestream Wirecast. Przyjęto założenie, że masz subskrypcję platformy Azure i utworzono konto usługi Media Services.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową i przejdź do [portalu Microsoft Azure](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

W tym przewodniku Szybki start omówimy:

- Konfigurowanie kodera lokalnego z bezpłatną próbą telestream Wirecast.
- Konfigurowanie transmisji na żywo.
- Konfigurowanie wyjść strumienia na żywo.
- Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego.
- Za pomocą programu Azure Media Player, aby wyświetlić strumień na żywo i dane wyjściowe na żądanie.

Aby zachować prostotę, użyjemy predefiniowanych kodowania dla usługi Azure Media Services w wirecast, kodowanie chmury przekazywanej i RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Konfigurowanie kodera lokalnego przy użyciu funkcji Wirecast

1. Pobierz i zainstaluj Wirecast dla swojego systemu operacyjnego na [stronie internetowej Telestream](https://www.telestream.net).
1. Uruchom aplikację i użyj ulubionego adresu e-mail, aby zarejestrować produkt. Zachowaj otwartą aplikację.
1. W otrzymanej wiadomości e-mail zweryfikuj swój adres e-mail. Następnie aplikacja rozpocznie bezpłatną wersję próbną.
1. Zalecane: Obejrzyj film instruktażowy na ekranie aplikacji otwierającej.

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurowanie strumienia na żywo usługi Azure Media Services

1. Przejdź do konta usługi Azure Media Services w portalu, a następnie wybierz pozycję **Przesyłanie strumieniowe na żywo** z listy **usługi Media Services.**

   ![Link do transmisji na żywo](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Wybierz **pozycję Dodaj wydarzenie na żywo,** aby utworzyć nowe wydarzenie transmisji strumieniowej na żywo.

   ![Ikona Dodaj wydarzenie na żywo](media/live-events-wirecast-quickstart/add-live-event.png)
1. Wprowadź nazwę nowego wydarzenia, taką jak *TestLiveEvent,* w polu **Nazwa wydarzenia na żywo.**

   ![Pole nazwa wydarzenia na żywo](media/live-events-wirecast-quickstart/live-event-name.png)
1. Wprowadź opcjonalny opis zdarzenia w polu **Opis.**
1. Wybierz opcję Przekazywanie — brak opcji **kodowania w chmurze.**

   ![Opcja kodowania w chmurze](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Wybierz opcję **RTMP.**
1. Upewnij się, że opcja **Brak** jest zaznaczona dla **startu na żywo zdarzenia**, aby uniknąć naliczania opłat za wydarzenie na żywo, zanim będzie gotowe. (Rozliczenia rozpoczną się po rozpoczęciu wydarzenia na żywo).

   ![Rozpocznij opcję wydarzenia na żywo](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Wybierz przycisk **Przejrzyj + utwórz,** aby przejrzeć ustawienia.
1. Wybierz przycisk **Utwórz,** aby utworzyć wydarzenie na żywo. Następnie wrócisz do aukcji wydarzeń na żywo.
1. Wybierz łącze do właśnie utworzonego wydarzenia na żywo. Należy zauważyć, że zdarzenie zostało zatrzymane.
1. Zachowaj tę stronę otwartą w przeglądarce. Wrócimy do niego później.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Konfigurowanie transmisji na żywo przy użyciu programu Wirecast Studio

1. W aplikacji Wirecast wybierz polecenie **Utwórz pusty dokument** z menu głównego, a następnie wybierz pozycję **Kontynuuj**.

   ![Ekran startowy wirecast](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Umieść wskaźnik myszy na pierwszej warstwie w obszarze **Warstwy Wirecast.**  Wybierz wyświetloną ikonę **Dodaj** i wybierz dane wejściowe wideo, które chcesz przesyłać strumieniowo.

   ![Wirecast dodaj ikonę](media/live-events-wirecast-quickstart/add-icon.png)

   Zostanie otwarte okno dialogowe **Warstwa wzorcowa 1.**
1. Z menu **wybierz polecenie Przechwytywanie wideo,** a następnie wybierz aparat, którego chcesz użyć.

   ![Obszar podglądu do przechwytywania wideo](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Widok z kamery pojawi się w obszarze podglądu.
1. Umieść wskaźnik myszy na drugiej warstwie w obszarze **Warstwy Wirecast.** Wybierz wyświetloną ikonę **Dodaj** i wybierz dane wejściowe audio, które chcesz przesyłać strumieniowo. Zostanie otwarte okno dialogowe **Warstwa wzorcowa 2.**
1. Z menu **wybierz polecenie Przechwytywanie dźwięku,** a następnie wybierz wejście audio, którego chcesz użyć.

   ![Wejścia do przechwytywania dźwięku](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Z menu głównego wybierz pozycję **Ustawienia wyjściowe**. Zostanie wyświetlone okno dialogowe **Wybieranie miejsca docelowego wyjścia.**
1. Wybierz **usługę Azure Media Services** z listy rozwijanej Miejsce **docelowe.** Ustawienie danych wyjściowych dla usługi Azure Media Services automatycznie wypełnia *większość* ustawień danych wyjściowych.

   ![Ekran ustawień wyjścia wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)


W następnej procedurze powrócisz do usługi Azure Media Services w przeglądarce, aby skopiować wejściowy adres URL, aby wprowadzić go w ustawieniach danych wyjściowych:

1. Na stronie usługi Azure Media Services w portalu wybierz pozycję **Start,** aby rozpocząć zdarzenie transmisji na żywo. (Rozliczenia rozpoczynają się teraz).

   ![Ikona Start](media/live-events-wirecast-quickstart/start.png)
2. Ustaw przełącznik **Bezpieczne/Niezabezpieczenie** na **Niezabezpieczony**. Ten krok ustawia protokół na RTMP zamiast RTMPS.
3. W polu **Wejściowy adres URL** skopiuj adres URL do schowka.

   ![Wejściowy adres URL](media/live-events-wirecast-quickstart/input-url.png)
4. Przełącz się do aplikacji Wirecast i wklej **wejściowy adres URL** w polu **Adres** w ustawieniach danych wyjściowych.

   ![Adres URL danych wejściowych wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Kliknij przycisk **OK**.

## <a name="set-up-outputs"></a>Konfigurowanie wyjść

Ta część skonfiguruje swoje wyjścia i pozwoli Ci zapisać nagranie transmisji na żywo.  

> [!NOTE]
> Aby można było przesyłać strumieniowo to dane wyjściowe, punkt końcowy przesyłania strumieniowego musi być uruchomiony. Zobacz później [Uruchom domyślną sekcję punktu końcowego przesyłania strumieniowego.](#run-the-default-streaming-endpoint)

1. Wybierz **łącze Utwórz wyjścia** poniżej przeglądarki **wideo Wyjścia.**
1. Jeśli chcesz, edytuj nazwę danych wyjściowych w polu **Nazwa** na coś bardziej przyjaznego dla użytkownika, aby można było je łatwo znaleźć później.
   
   ![Pole Nazwa wyjściowa](media/live-events-wirecast-quickstart/output-name.png)
1. Zostaw na razie całą resztę pudełek w spokoju.
1. Wybierz **przycisk Dalej,** aby dodać lokalizator przesyłania strumieniowego.
1. Zmień nazwę lokalizatora na coś bardziej przyjaznego dla użytkownika, jeśli chcesz.
   
   ![Pole nazwa lokalizatora](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Zostaw wszystko inne na tym ekranie w spokoju na razie.
1. Wybierz **pozycję Utwórz**.

## <a name="start-the-broadcast"></a>Rozpoczynanie transmisji

1. W programie Wirecast wybierz z menu głównego pozycję **Start** > **/Stop Broadcasting** > **Start Azure Media Services: Azure Media Services.**

   ![Elementy menu programu Start](media/live-events-wirecast-quickstart/start-broadcast.png)

   Gdy strumień został wysłany do zdarzenia na żywo, okno **na żywo** w wirecast pojawia się w odtwarzaczu wideo na stronie zdarzenia na żywo w usłudze Azure Media Services.

1. Wybierz przycisk **Przejdź** pod oknem podglądu, aby rozpocząć nadawanie wideo i dźwięku wybranych dla warstw Wirecast.

   ![Przycisk Wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Jeśli wystąpi błąd, spróbuj przeładować gracza, wybierając link **Przeładuj odtwarzacz** nad odtwarzaczem.

## <a name="run-the-default-streaming-endpoint"></a>Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego

1. Wybierz **punkty końcowe przesyłania strumieniowego** na liście usługi Media Services.

   ![Element menu punktów końcowych przesyłania strumieniowego](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Jeśli domyślny stan punktu końcowego przesyłania strumieniowego zostanie zatrzymany, wybierz go. Ten krok prowadzi do strony dla tego punktu końcowego.
1. Wybierz pozycję **Uruchom**.
   
   ![Przycisk Start dla punktu końcowego przesyłania strumieniowego](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Odtwarzanie emisji wyjściowej przy użyciu programu Azure Media Player

1. Skopiuj adres URL przesyłania strumieniowego w **odtwarzaczu wideo Wyjściowe.**
1. W przeglądarce sieci Web otwórz [demo programu Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Wklej adres URL przesyłania strumieniowego do pola **adresu URL** programu Azure Media Player.
1. Wybierz przycisk **Aktualizuj odtwarzacz.**
1. Wybierz ikonę **Odtwórz** w filmie, aby zobaczyć transmisję na żywo.

## <a name="stop-the-broadcast"></a>Zatrzymywanie transmisji

Jeśli uważasz, że przesyłałeś wystarczającą ilość zawartości, zatrzymaj emisję.

1. W obszarze Wirecast wybierz przycisk **Emisja.** Ten krok zatrzymuje emisję z Wirecast.
1. W portalu wybierz pozycję **Zatrzymaj**. Następnie pojawi się komunikat ostrzegawczy, że transmisja na żywo zostanie zatrzymana, ale dane wyjściowe staną się teraz zasobem na żądanie.
1. Wybierz **zatrzymaj** w komunikacie ostrzegawczym. Usługa Azure Media Player wyświetla teraz błąd, ponieważ strumień na żywo nie jest już dostępny.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Odtwarzanie danych wyjściowych na żądanie przy użyciu programu Azure Media Player

Dane wyjściowe, które zostały utworzone jest teraz dostępna do przesyłania strumieniowego na żądanie, tak długo, jak punkt końcowy przesyłania strumieniowego jest uruchomiony.

1. Przejdź do listy usługi Media Services i wybierz pozycję **Zasoby**.
1. Znajdź dane wyjściowe zdarzenia utworzone wcześniej i wybierz łącze do zasobu. Zostanie otwarta strona wyjściowa zasobu.
1. Skopiuj adres URL przesyłania strumieniowego pod odtwarzaczem wideo dla zasobu.
1. Wróć do programu Azure Media Player w przeglądarce i wklej adres URL przesyłania strumieniowego do pola adresu URL.
1. Wybierz **pozycję Aktualizuj odtwarzacz**.
1. Wybierz ikonę **Odtwórz** w filmie, aby wyświetlić zasób na żądanie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!IMPORTANT]
> Zatrzymaj usługi! Po wykonaniu kroków w tym przewodniku Szybki start należy zatrzymać zdarzenie na żywo i punkt końcowy przesyłania strumieniowego lub naliczane będą naliczane za czas, w którym pozostają uruchomione. Aby zatrzymać wydarzenie na żywo, zobacz Procedurę [Zatrzymania emisji,](#stop-the-broadcast) kroki 2 i 3.

Aby zatrzymać punkt końcowy przesyłania strumieniowego:

1. Na liście usługi Media Services wybierz pozycję **Punkty końcowe przesyłania strumieniowego**.
2. Wybierz domyślny punkt końcowy przesyłania strumieniowego, który został uruchomiony wcześniej. Ten krok otwiera stronę punktu końcowego.
3. Wybierz **pozycję Zatrzymaj**.

> [!TIP]
> Jeśli nie chcesz, aby zasoby były przechowywane z tego zdarzenia, usuń je, aby nie były naliczane naliczane płatności za przechowywanie.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Wydarzenia na żywo i wyniki na żywo w umiań Media Services](./live-events-outputs-concept.md)
