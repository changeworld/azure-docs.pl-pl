---
title: Dane techniczne sprzętu zestawu Azure Kinect DK
description: Informacje o składnikach, danych technicznych i możliwościach zestawu Azure Kinect DK.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specs, hardware, DK, capabilities, depth, color, RGB, IMU, microphone, array, depth
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: dbe4cac64e324c1188ec5a01b4da3935a5d5b479
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371293"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Dane techniczne sprzętu zestawu Azure Kinect DK

Ten artykuł zawiera szczegółowe informacje na temat sposobu, w jaki sprzęt Azure Kinect integruje najnowszą technologię czujników firmy Microsoft w jednym akcesorium podłączonym przez USB.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Warunki

W tym artykule są używane poniższe skróty terminów.

- NFOV (narrow field-of-view depth mode) — tryb głębi ostrości wąskiego pola widzenia
- WFOV (wide field-of-view depth mode) — tryb głębi ostrości szerokiego pola widzenia
- FOV (field-of-view) — pole widzenia
- FPS (frames-per-second) — klatki na sekundę
- IMU (Inertial Measurement Unit) — moduł IMU
- FoI (field of interest) — pole zainteresowania

## <a name="product-dimensions-and-weight"></a>Wymiary i waga produktu

Poniżej podano wagę i wymiary urządzenia zestawu Azure Kinect.

- **Wymiary**: 103 x 39 x 126 mm
- **Waga**: 440 g

![Wymiary zestawu Azure Kinect DK](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Środowisko pracy

Zestaw Azure Kinect DK jest przeznaczony dla deweloperów i firm komercyjnych pracujących w następujących warunkach otoczenia:

- **Temperatura**: 10–25⁰C
- **Wilgotność**: Wilgotność względna 8–90% (bez kondensacji)

> [!NOTE]
> Użytkowanie w warunkach otoczenia wykraczających poza te zakresy może spowodować awarię i/lub nieprawidłowe działanie urządzenia. Te warunki otoczenia dotyczą otoczenia bezpośrednio wokół urządzenia we wszystkich warunkach eksploatacyjnych. W przypadku korzystania z zewnętrznej obudowy zalecana jest aktywna kontrola temperatury i/lub inne rozwiązania chłodzące, aby zapewnić utrzymanie warunków otoczenia urządzenia w tych zakresach. Konstrukcja urządzenia zawiera kanał chłodzący między sekcją przednią a tylną tuleją. Po zaimplementowania urządzenia należy się upewnić, że ten kanał chłodzący jest drożny.

Zapoznaj się z [informacjami dotyczącymi bezpieczeństwa](https://support.microsoft.com/help/4023454/safety-information) produktu.

## <a name="depth-camera-supported-operating-modes"></a>Obsługiwane tryby pracy kamery z pomiarem głębi

Zestaw Azure Kinect DK zawiera zaprojektowaną przez firmę Microsoft 1-megapikselową kamerę z pomiarem głębi w technologii Time-of-Flight (ToF) korzystającą z [czujnika obrazu przedstawionego w dokumencie ISSCC 2018](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018). Kamera z pomiarem głębi obsługuje tryby wskazane poniżej:

 | Tryb            | Rozwiązanie | FoI       | FPS                | Zakres roboczy* | Czas ekspozycji |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV bez łączenia pikseli   | 640x576    | 75°x65°   | 0, 5, 15, 30       | 0,5–3,86 m       | 12,8 ms        |
| NFOV z łączeniem pikseli 2x2 (SW) | 320x288    | 75°x65°   | 0, 5, 15, 30       | 0,5–5,46 m       | 12,8 ms        |
| WFOV z łączeniem pikseli 2x2 | 512x512    | 120°x120° | 0, 5, 15, 30       | 0,25–2,88 m      | 12,8 ms        |
| WFOV bez łączenia pikseli   | 1024x1024  | 120°x120° | 0, 5, 15           | 0,25–2,21 m      | 20,3 ms        |
| Pasywny czujnik podczerwieni      | 1024x1024  | Nie dotyczy       | 0, 5, 15, 30       | Nie dotyczy              | 1,6 ms         |

\*współczynnik odbicia 15%–95% przy 850nm, 2,2 μW/cm<sup>2</sup>/nm, odchylenie standardowe błędów losowych ≤ 17 mm, typowy błąd systematyczny < 11 mm + 0,1% odległości bez zakłóceń wielościeżkowych. Głębia podana poza wskazanym zakresem w zależności od współczynnika odbicia obiektu.

## <a name="color-camera-supported-operating-modes"></a>Obsługiwane tryby operacyjne kamery kolorowej

Zestaw Azure Kinect DK zawiera czujnik efektu migawki czujnika obrazu CMOS o rozdzielczości 12 megapikseli OV12A10. Poniżej wymieniono natywne tryby pracy:

|             Rozdzielczość kamery RGB (HxV)  |          Współczynnik proporcji  |          Opcje formatu   |          Liczba klatek na sekundę (FPS)  |          Nominalne FOV (HxV) (po przetworzeniu)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840x2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       2560x1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1920x1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1280x720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90°x59°                              |
|       4096x3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90°x74,3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90°x74,3°                            |

Kamera RGB jest zgodna ze standardem USB Video Class i może być używana bez zestawu Sensor SDK. Przestrzeń barw kamery RGB: BT.601 — pełny zakres [0..255]. 

> [!NOTE]
> Zestaw Sensor SDK może dostarczać obrazy kolorowe w formacie piksela BGRA. To nie jest tryb natywny obsługiwany przez urządzenie i jego użycie powoduje dodatkowe obciążenie procesora CPU. Procesor CPU hosta jest używany do konwertowania z obrazów MJPEG odebranych z urządzenia.

## <a name="rgb-camera-exposure-time-values"></a>Wartości czasu ekspozycji kamery RGB

Poniżej znajduje się mapowanie akceptowalnych ręcznych wartości ekspozycji kamery RGB:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10 000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Czas surowych danych czujnika głębi

Tryb głębi | IR <br>Impulsy | Szerokość <br>impulsów  | Okresy <br>bezczynności| Czas bezczynności | Ekspozycja <br> Time
-|-|-|-|-|-
NFOV bez łączenia pikseli <br>  NFOV z łączeniem pikseli 2xx <br> WFOV z łączeniem pikseli 2x2 | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV bez łączenia pikseli                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Pole widzenia kamery

Następny obraz przedstawia pole widzenia kamery z pomiarem głębi i RGB — kąty, które czujniki „widzą”. Ten diagram przedstawia kamerę RGB w trybie 4:3.

![FOV (pole widzenia) kamery](./media/resources/hardware-specs-media/camera-fov.png)

Ten obraz pokazuje pole widzenia kamery widziane od przodu z odległości 2000 mm.

![FOV (pole widzenia) kamery — widok z przodu](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Gdy głębia jest w trybie NFOV, kamera RGB ma lepszy stopień nakładania się pikseli w rozdzielczości 4:3 niż w rozdzielczości 16:9.

## <a name="motion-sensor-imu"></a>Czujnik ruchu (IMU)

Osadzony czujnik IMU jest modułem LSM6DSMUS, który zawiera zarówno przyspieszeniomierz, jak i żyroskop. Przyspieszeniomierz i żyroskop są próbkowane jednocześnie z częstotliwością 1,6 kHz. Próbki są raportowane do hosta z częstotliwością 208 Hz.

## <a name="microphone-array"></a>Macierz mikrofonów

Zestaw Azure Kinect DK ma osadzoną wysokiej jakości kołową macierz siedmiu mikrofonów, która jest identyfikowana jako standardowe urządzenie USB klasy 2.0. Można uzyskać dostęp do wszystkich 7 kanałów. Specyfikacje wydajności:

- Czułość:-22 dBFS (94 dB SPL, 1 kHz)
- Stosunek sygnału do szumu > 65 dB
- Punkt przeciążenia akustycznego: 116 dB

![Mikrofon](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Zestaw Azure Kinect DK to złożone urządzenie USB3, które udostępnia następujące punkty końcowe sprzętu dla systemu operacyjnego:

Identyfikator dostawcy: 0x045E (Microsoft), tabelę identyfikatora produktu podano poniżej:

|    Interfejs USB        |    PNP IP    |     Uwagi            |
|-------------------------|--------------|----------------------|
|    Koncentrator USB 3.1 pierwszej generacji    |    0x097A    |    Główny koncentrator    |
|    Koncentrator USB 2.0         |    0x097B    |    HS   USB          |
|    Kamera z pomiarem głębi       |    0x097C    |    USB 3.0            |
|    Kamera kolorowa       |    0x097D    |    USB 3.0            |
|    Mikrofony        |    0x097E    |    HS   USB          |

## <a name="indicators"></a>Wskaźniki

Z przodu urządzenia znajduje się wskaźnik przesyłania strumieniowego kamery, który można wyłączyć programowo przy użyciu zestawu Sensor SDK.

Dioda LED stanu znajdująca się z tyłu urządzenia wskazuje stan urządzenia:

| Światło lampki wskaźnika     | Oznacza, że                                                   |
|-----------------------|------------------------------------------------------------|
| Ciągłe białe           | Urządzenie jest włączone i działa prawidłowo.                         |
| Pulsujące białe        | Urządzenie jest włączone, ale nie ma połączenia danych przez USB 3.0.   |
| Pulsujące bursztynowe        | Urządzenie nie ma wystarczającego zasilania, aby pracować.               |
| Bursztynowe, następnie pulsujące białe  | Trwa aktualizacja oprogramowania układowego lub odzyskiwanie                    |

## <a name="power-device"></a>Zasilanie urządzenia

Urządzenie może być zasilane na dwa sposoby:

1. Przy użyciu wbudowanego zasilacza. Połączenie danych jest zapewniane przez oddzielny kabel USB typu C do USB typu A.
2. Przy użyciu kabla USB typu C dla zasilania i danych.

Kabel USB typu C nie jest dołączony do zestawu Azure Kinect DK.

> [!NOTE]
> - Dostarczony przewód zasilający ma z jednej strony wtyczkę USB typu A, a z drugiej strony pojedynczy wtyk okrągły. Użyj dostarczonego zasilacza ściennego z tym kablem. Urządzenie jest w stanie pobierać więcej mocy niż mogą dostarczyć dwa standardowe porty USB typu A.
> - Kable USB mają znaczenie, dlatego zalecamy korzystanie z kabli wysokiej jakości i sprawdzenie funkcjonalności przed zdalnym wdrożeniem jednostki.

> [!TIP]
> Jak wybrać dobry kabel USB typu C:
> - [Certyfikowany kabel USB](https://www.usb.org/products) musi obsługiwać zarówno zasilanie, jak i połączenie danych.
> - Kabel pasywny powinien być krótszy niż 1,5 m. Jeśli jest dłuższy, użyj kabla aktywnego. 
> - Kabel musi obsługiwać > 1,5 A. W przeciwnym razie należy połączyć zasilacz zewnętrzny.

Sprawdź kabel:

- Podłącz urządzenie za pomocą kabla do komputera hosta.
- Sprawdź, czy wszystkie urządzenia są prawidłowo wyliczane w Menedżerze urządzeń systemu Windows. Kamera z pomiarem głębi i RGB powinny być wyświetlane jak pokazano w poniższym przykładzie.

  ![Zestaw Azure Kinect DK w Menedżerze urządzeń](./media/resources/hardware-specs-media/device-manager.png)

- Sprawdź, czy kabel może zapewnić niezawodne przesyłanie strumieniowe dla wszystkich czujników w narzędziu Azure Kinect Viewer, używając następujących ustawień:

  - Kamera z pomiarem głębi: NFOV bez łączenia pikseli
  - Kamera RGB: 2160p
  - Mikrofony i czujnik IMU włączone

## <a name="what-does-the-light-mean"></a>Co oznacza stan lampki wskaźnika zasilania?

Wskaźnik zasilania to dioda LED znajdująca się z tyłu obudowy zestawu Azure Kinect DK. Kolor diody LED zmienia się w zależności od stanu urządzenia.

![Ilustracja przedstawia tył obudowy zestawu Azure Kinect DK. Dołączono trzy numerowane objaśnienia: jeden dla diody LED wskaźnika, a poniżej dwa dla kabli.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Na tym rysunku oznaczono następujące elementy:

1. Wskaźnik zasilania
1. Kabel zasilający (podłączony do źródła zasilania)
1. Kabel danych USB typu C (podłączony do komputera)

Upewnij się, że kable są podłączone tak, jak pokazano. Następnie zapoznaj się z poniższą tabelą, aby dowiedzieć się, co oznaczają różne stany lampki wskaźnika zasilania.

|Światło lampki wskaźnika: |Oznacza, że: |Zalecane czynności: |
| ---| --- | --- |
|Ciągłe białe |Urządzenie jest włączone i działa poprawnie. |Korzystaj z urządzenia. |
|Nie świeci |Urządzenie nie jest podłączone do komputera. |Upewnij się, że kabel przewodu zasilającego z okrągłym wtykiem jest podłączony do urządzenia i do zasilacza USB.<br /><br />Upewnij się, że kabel USB typu C jest podłączony do urządzenia i do komputera PC. |
|Pulsujące białe |Urządzenie jest włączone, ale nie ma połączenia danych przez port USB 3.0. |Upewnij się, że kabel przewodu zasilającego z okrągłym wtykiem jest podłączony do urządzenia i do zasilacza USB.<br /><br />Upewnij się, że kabel USB typu C jest podłączony do urządzenia i do portu USB 3.0 na komputerze.<br /><br />Podłącz urządzenie do innego portu USB 3.0 na komputerze.<br /><br />Na komputerze otwórz Menedżer urządzeń (**Start** > **Panel sterowania** > **Menedżer urządzeń**) i sprawdź, czy komputer ma obsługiwany kontroler hosta USB 3.0. |
|Pulsujące bursztynowe |Urządzenie nie ma wystarczającego zasilania, aby pracować. |Upewnij się, że kabel przewodu zasilającego z okrągłym wtykiem jest podłączony do urządzenia i do zasilacza USB.<br /><br />Upewnij się, że kabel USB typu C jest podłączony do urządzenia i do komputera PC. |
|Bursztynowe, a następnie pulsujące białe |Urządzenie jest włączone i otrzymuje aktualizację oprogramowania układowego lub przywraca ustawienia fabryczne. |Poczekaj, aż światło wskaźnika zasilania zmieni się na ciągłe białe. Aby uzyskać więcej informacji, zobacz [Resetowanie zestawu Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Pobór mocy

Zestaw Azure Kinect DK pobiera do 5,9 W; rzeczywiste zużycie energii zależy od przypadku użycia.

## <a name="calibration"></a>Kalibracja

Zestaw Azure Kinect DK jest kalibrowany w fabryce. Zapytania o parametry kalibracji czujników wizualnych i bezwładnościowych mogą być wykonywane programowo przy użyciu zestawu Sensor SDK.

## <a name="device-recovery"></a>Odzyskiwanie urządzenia

Oprogramowanie układowe urządzenia można zresetować do oryginalnego oprogramowania układowego przy użyciu przycisku znajdującego się pod bolcem blokady.

![Przycisk odzyskiwania zestawu Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Aby odzyskać urządzenie, zobacz [instrukcje podane w tym miejscu](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Następne kroki

- [Korzystanie z zestawu Azure Kinect Sensor SDK](about-sensor-sdk.md)
- [Konfigurowanie sprzętu](set-up-azure-kinect-dk.md)
