---
title: Strumienie urządzeń usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Omówienie strumieni urządzeń usługi Azure IoT Hub, które ułatwiają bezpieczne dwukierunkowe tunele TCP dla różnych scenariuszy komunikacji między chmurą a urządzeniem.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890469"
---
# <a name="iot-hub-device-streams-preview"></a>Strumienie urządzeń usługi IoT Hub (wersja zapoznawcza)

*Strumienie urządzeń* usługi Azure IoT Hub ułatwiają tworzenie bezpiecznych dwukierunkowych tuneli TCP dla różnych scenariuszy komunikacji między chmurą a urządzeniem. Strumień urządzenia jest obsługiwany przez punkt *końcowy przesyłania strumieniowego* usługi IoT Hub, który działa jako serwer proxy między urządzeniem a punktami końcowymi usługi. Ta konfiguracja, przedstawiona na poniższym diagramie, jest szczególnie przydatna, gdy urządzenia znajdują się za zaporą sieciową lub znajdują się wewnątrz sieci prywatnej. W związku z tym strumienie urządzeń usługi IoT Hub pomagają zaspokoić potrzeby klientów w zakresie docierania do urządzeń IoT w sposób przyjazny dla zapory i bez konieczności szeroko otwierania portów zapory sieci przychodzącej lub wychodzącej.

!["Omówienie strumieni urządzeń usługi IoT Hub"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Korzystając ze strumieni urządzeń usługi IoT Hub, urządzenia pozostają bezpieczne i będą musiały tylko otworzyć wychodzące połączenia TCP do punktu końcowego przesyłania strumieniowego centrum IoT przez port 443. Po ustanowieniu strumienia aplikacje po stronie usługi i po stronie urządzenia będą miały programowy dostęp do obiektu klienta WebSocket do wysyłania i odbierania nieprzetworzonych bajtów do siebie. Niezawodność i gwarancje zamawiania zapewniane przez ten tunel jest na równi z TCP.

## <a name="benefits"></a>Korzyści

Strumienie urządzeń usługi IoT Hub zapewniają następujące korzyści:

* **Bezpieczna łączność przyjazna dla zapory:** Urządzenia IoT można uzyskać z punktów końcowych usługi bez otwierania portu zapory przychodzącej na urządzeniu lub obwodzie sieci (tylko łączność wychodząca z IoT Hub jest potrzebna przez port 443).

* **Uwierzytelnianie:** Zarówno urządzenia i strony usługi tunelu trzeba uwierzytelnić za pomocą usługi IoT Hub przy użyciu odpowiednich poświadczeń.

* **Szyfrowanie:** Domyślnie strumienie urządzeń Usługi IoT Hub używają połączeń z obsługą protokołu TLS. Gwarantuje to, że ruch jest zawsze szyfrowany niezależnie od tego, czy aplikacja używa szyfrowania, czy nie.

* **Prostota łączności:** W wielu przypadkach użycie strumieni urządzeń eliminuje potrzebę skomplikowanej konfiguracji wirtualnych sieci prywatnych w celu umożliwienia łączności z urządzeniami IoT.

* **Zgodność ze stosem TCP/IP:** Strumienie urządzeń usługi IoT Hub mogą pomieścić ruch aplikacji TCP/IP. Oznacza to, że szeroki zakres zastrzeżonych, jak również opartych na standardach protokołów może korzystać z tej funkcji.

* **Łatwość użycia w konfiguracjach sieci prywatnych:** Usługa może komunikować się z urządzeniem, odwołując się do jego identyfikatora urządzenia, a nie adresu IP urządzenia. Jest to przydatne w sytuacjach, gdy urządzenie znajduje się wewnątrz sieci prywatnej i ma prywatny adres IP lub jego adres IP jest przypisywany dynamicznie i jest nieznany po stronie usługi.

## <a name="device-stream-workflows"></a>Przepływy pracy strumienia urządzeń

Strumień urządzenia jest inicjowany, gdy usługa żąda połączenia z urządzeniem, podając jego identyfikator urządzenia. Ten przepływ pracy w szczególności pasuje do modelu komunikacji klient/serwer, w tym SSH i RDP, gdzie użytkownik zamierza zdalnie połączyć się z serwerem SSH lub RDP uruchomionym na urządzeniu przy użyciu programu klienckiego SSH lub RDP.

Proces tworzenia strumienia urządzenia obejmuje negocjacje między urządzeniem, usługą, głównymi punktami końcowymi i punktami końcowymi przesyłania strumieniowego. Podczas gdy główny punkt końcowy usługi IoT hub organizuje tworzenie strumienia urządzenia, punkt końcowy przesyłania strumieniowego obsługuje ruch przepływa, który przepływa między usługą a urządzeniem.

### <a name="device-stream-creation-flow"></a>Przepływ tworzenia strumienia urządzenia

Programowe tworzenie strumienia urządzenia przy użyciu sdk obejmuje następujące kroki, które są również przedstawione na rysunku poniżej:

!["Proces uzgadniania strumienia urządzenia"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Aplikacja urządzenia rejestruje wywołanie zwrotne z wyprzedzeniem, aby otrzymywać powiadomienia o tym, kiedy do urządzenia jest inicjowany nowy strumień urządzenia. Ten krok zazwyczaj ma miejsce, gdy urządzenie uruchamia się i łączy się z Centrum IoT Hub.

2. Program po stronie usługi inicjuje strumień urządzenia w razie potrzeby, podając identyfikator urządzenia (_nie_ adres IP).

3. Centrum IoT powiadamia program po stronie urządzenia, wywołując wywołanie zwrotne zarejestrowane w kroku 1. Urządzenie może zaakceptować lub odrzucić żądanie inicjacji strumienia. Ta logika może być specyficzne dla scenariusza aplikacji. Jeśli żądanie strumienia zostanie odrzucone przez urządzenie, Usługa IoT Hub odpowiednio informuje o tym usługę; w przeciwnym razie wykonaj poniższe czynności.

4. Urządzenie tworzy bezpieczne wychodzące połączenie TCP z punktem końcowym przesyłania strumieniowego za pośrednictwem portu 443 i uaktualnia połączenie do websocket. Adres URL punktu końcowego przesyłania strumieniowego, a także poświadczenia używane do uwierzytelniania są dostarczane do urządzenia przez Centrum IoT w ramach żądania wysłanego w kroku 3.

5. Usługa jest powiadamiany o wyniku urządzenia akceptującego strumień i przechodzi do tworzenia własnego klienta WebSocket do punktu końcowego przesyłania strumieniowego. Podobnie odbiera adres URL punktu końcowego przesyłania strumieniowego i informacje uwierzytelniania z Usługi IoT Hub.

W procesie uzgadniania powyżej:

* Proces uzgadniania musi zakończyć się w ciągu 60 sekund (krok od 2 do 5), w przeciwnym razie uzgadnianie zakończy się niepowodzeniem z limitem czasu i usługa zostanie odpowiednio powiadomiona.

* Po zakończeniu tworzenia strumienia powyżej punkt końcowy przesyłania strumieniowego będzie działać jako serwer proxy i przeniesie ruch między usługą a urządzeniem za pośrednictwem odpowiednich websockets.

* Zarówno urządzenie, jak i usługa wymagają połączenia wychodzącego z głównym punktem końcowym usługi IoT Hub, a także punktem końcowym przesyłania strumieniowego za portem 443. Adres URL tych punktów końcowych jest dostępny na karcie *Przegląd* w portalu Usługi IoT Hub.

* Niezawodność i gwarancje zamawiania ustalonego strumienia jest na równi z TCP.

* Wszystkie połączenia z centrum IoT Hub i punktu końcowego przesyłania strumieniowego używać protokołu TLS i są szyfrowane.

### <a name="termination-flow"></a>Przepływ zakończenia

Ustalony strumień kończy się, gdy jedno z połączeń TCP z bramą zostanie rozłączone (przez usługę lub urządzenie). Może to nastąpić dobrowolnie, zamykając WebSocket na urządzeniu lub programach usługowych lub mimowolnie w przypadku limitu czasu połączenia sieciowego lub awarii procesu. Po zakończeniu połączenia urządzenia lub usługi z punktem końcowym przesyłania strumieniowego inne połączenie TCP zostanie również (na siłę) zakończone, a usługa i urządzenie są odpowiedzialne za ponowne utworzenie strumienia, jeśli to konieczne.

## <a name="connectivity-requirements"></a>Wymagania dotyczące łączności

Zarówno urządzenia, jak i strony usługi strumienia urządzenia musi być w stanie ustanowić połączenia z obsługą protokołu TLS do usługi IoT Hub i jego punktu końcowego przesyłania strumieniowego. Wymaga to łączności wychodzącej za port 443 do tych punktów końcowych. Nazwa hosta skojarzona z tymi punktami końcowymi znajduje się na karcie *Przegląd* usługi IoT Hub, jak pokazano na poniższym rysunku:

!["Punkty końcowe strumienia urządzeń"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternatywnie informacje o punktach końcowych można pobrać przy użyciu interfejsu wiersza polecenia `property.hostname` platformy `property.deviceStreams` Azure w sekcji właściwości centrum, w szczególności i kluczy.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Dane wyjściowe jest obiekt JSON wszystkich punktów końcowych, które centrum urządzenia i usługi może być konieczne do połączenia w celu ustanowienia strumienia urządzenia.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Upewnij się, że zainstalowano platformę Azure CLI w wersji 2.0.57 lub nowszej. Najnowszą wersję można pobrać ze strony [Zainstaluj interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Zezwalaj na łączność wychodzącą z punktami końcowymi przesyłania strumieniowego urządzenia

Jak wspomniano na początku tego artykułu, urządzenie tworzy połączenie wychodzące z punktem końcowym przesyłania strumieniowego usługi IoT Hub podczas procesu inicjowania strumieni urządzeń. Zapory na urządzeniu lub w jego sieci muszą zezwalać na łączność wychodzącą z bramą przesyłania strumieniowego za pośrednictwem portu 443 (należy pamiętać, że komunikacja odbywa się za pośrednictwem połączenia WebSocket, które jest szyfrowane przy użyciu protokołu TLS).

Nazwa hosta punktu końcowego przesyłania strumieniowego urządzenia można znaleźć w portalu ![usługi Azure IoT Hub w zakładce Przegląd.](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternatywnie można znaleźć te informacje przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Upewnij się, że zainstalowano platformę Azure CLI w wersji 2.0.57 lub nowszej. Najnowszą wersję można pobrać ze strony [Zainstaluj interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Rozwiązywanie problemów za pomocą dzienników aktywności strumieni urządzeń

Dzienniki usługi Azure Monitor można skonfigurować w celu zbierania dziennika aktywności strumieni urządzeń w centrum IoT Hub. Może to być bardzo pomocne w rozwiązywaniu problemów scenariuszy.

Wykonaj poniższe czynności, aby skonfigurować dzienniki usługi Azure Monitor dla działań strumienia urządzeń usługi IoT Hub:

1. Przejdź do karty *Ustawienia diagnostyczne* w centrum IoT Hub i kliknij łącze *Włącz diagnostykę.*

   !["Włączanie dzienników diagnostycznych"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Podaj nazwę ustawień diagnostycznych i wybierz opcję *Wyślij do usługi Log Analytics.* Zostaniesz poprowadzony, aby wybrać istniejący zasób obszaru roboczego usługi Log Analytics lub utworzyć nowy. Ponadto sprawdź *DeviceStreams* z listy.

    !["Włącz dzienniki strumieni urządzeń"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Teraz możesz uzyskać dostęp do dzienników strumieni urządzeń na karcie *Dzienniki* w portalu usługi IoT Hub. Dzienniki aktywności strumienia urządzeń `AzureDiagnostics` pojawią `Category=DeviceStreams`się w tabeli i mają .

   Jak pokazano poniżej, tożsamość urządzenia docelowego i wynik operacji jest również dostępna w dziennikach.

   !["Dostęp do dzienników strumienia urządzeń"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Dostępność regionalna

Podczas publicznej wersji zapoznawczej strumienie urządzeń usługi IoT Hub są dostępne w regionach Central US, Central US EUAP, North Europe i Southeast Asia. Upewnij się, że utworzysz centrum w jednym z tych regionów.

## <a name="sdk-availability"></a>Dostępność SDK

Dwie strony każdego strumienia (po stronie urządzenia i usługi) używają SDK usługi IoT Hub do ustanowienia tunelu. Podczas publicznej wersji zapoznawczej klienci mogą wybierać spośród następujących języków SDK:

* Urządzenia obsługujące sdk języka C i C# strumieniuje po stronie urządzenia.

* NodeJS i C# SDK obsługuje strumienie urządzeń po stronie usługi.

## <a name="iot-hub-device-stream-samples"></a>Próbki strumienia urządzeń usługi IoT Hub

Istnieją dwa [przykłady szybkiego startu](/azure/iot-hub) dostępne na stronie Centrum IoT. Pokazują one użycie strumieni urządzeń przez aplikacje.

* Przykład *echa* pokazuje programowe użycie strumieni urządzeń (wywołując bezpośrednio interfejs SDK API).

* *Przykład lokalnego serwera proxy* pokazuje tunelowanie gotowego ruchu aplikacji klient/serwer (na przykład SSH, RDP lub web) za pośrednictwem strumieni urządzeń.

Próbki te są opisane bardziej szczegółowo poniżej.

### <a name="echo-sample"></a>Próbka echa

Przykład echa pokazuje programowe wykorzystanie strumieni urządzeń do wysyłania i odbierania bajtów między aplikacjami usługi i urządzenia. Należy pamiętać, że można używać programów usługowych i urządzeń w różnych językach. Na przykład można użyć programu urządzenia C z programem usługi C#.

Oto próbki echa:

* [Program usług i usług języka C#](quickstart-device-streams-echo-csharp.md)

* [Program usługi Node.js](quickstart-device-streams-echo-nodejs.md)

* [Program urządzenia C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Przykład lokalnego serwera proxy (dla protokołu SSH lub RDP)

Przykład lokalnego serwera proxy pokazuje sposób, aby włączyć tunelowanie ruchu istniejącej aplikacji, który obejmuje komunikację między klientem a programem serwera. Ta konfiguracja działa w przypadku protokołów klient/serwer, takich jak SSH i RDP, gdzie po stronie usługi działa jako klient (z uruchomionymi programami klienckimi SSH lub RDP), a po stronie urządzenia działa jako serwer (z uruchomionym demonem SSH lub programami serwera RDP).

W tej sekcji opisano użycie strumieni urządzeń, aby umożliwić użytkownikowi SSH do urządzenia za pośrednictwem strumieni urządzeń (w przypadku protokołu RDP lub innej aplikacji klient/serwer są podobne przy użyciu odpowiedniego portu protokołu).

Konfiguracja wykorzystuje dwa lokalne programy *proxy* pokazane na poniższym rysunku, a mianowicie *device-local proxy* i *service-local proxy*. Lokalne programy proxy są odpowiedzialne za wykonywanie [uzgadniania inicjowania strumienia urządzenia](#device-stream-creation-flow) za pomocą usługi IoT Hub i interakcję z klientem SSH i demonem SSH przy użyciu zwykłych gniazd klienta/serwera.

!["Konfiguracja serwera proxy strumienia urządzeń dla protokołu SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Użytkownik uruchamia lokalny serwer proxy usługi, aby zainicjować strumień urządzenia do urządzenia.

2. Serwer proxy lokalny dla urządzeń akceptuje żądanie inicjacji strumienia i tunel jest ustanawiany do punktu końcowego przesyłania strumieniowego usługi IoT Hub (jak wspomniano powyżej).

3. Serwer proxy lokalny dla urządzeń łączy się z punktem końcowym demona SSH nasłuchiwanie na porcie 22 na urządzeniu.

4. Serwer proxy lokalny usługi nasłuchuje na wyznaczonym porcie oczekującym na nowe połączenia SSH od użytkownika (port 2222 używany w przykładzie, ale można go skonfigurować do dowolnego innego dostępnego portu). Użytkownik wskazuje klienta SSH do portu serwera proxy lokalnego usługi na localhost.

### <a name="notes"></a>Uwagi

* Powyższe kroki kończą tunel end-to-end między klientem SSH (po prawej) do demona SSH (po lewej). Część tej kompleksowej łączności obejmuje wysyłanie ruchu za pomocą strumienia urządzenia do usługi IoT Hub.

* Strzałki na rysunku wskazują kierunek, w którym połączenia są ustanawiane między punktami końcowymi. W szczególności należy pamiętać, że nie ma żadnych połączeń przychodzących będzie do urządzenia (często jest to blokowane przez zaporę).

* Wybór użycia portu 2222 na lokalnym serwerze proxy usługi jest dowolnym wyborem. Serwer proxy można skonfigurować tak, aby używał dowolnego innego dostępnego portu.

* Wybór portu 22 jest zależny od protokołu i specyficzne dla SSH w tym przypadku. W przypadku PROW należy stosować port 3389. Można to skonfigurować w dostarczonych przykładowych programach.

Skorzystaj z poniższych linków, aby uzyskać instrukcje dotyczące uruchamiania lokalnych programów proxy w wybranym języku. Podobnie jak [w przykładzie echa,](#echo-sample)można uruchamiać lokalne programy proxy w różnych językach, ponieważ są one w pełni interoperacyjne.

* [Program usług i usług języka C#](quickstart-device-streams-proxy-csharp.md)

* [Program usługi Node.js](quickstart-device-streams-proxy-nodejs.md)

* [Program urządzenia C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o strumieniach urządzeń.

> [!div class="nextstepaction"]
> [Strumienie urządzeń na pokazie IoT (kanał 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
