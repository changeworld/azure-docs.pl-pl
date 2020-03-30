---
title: Opis przydziałów i ograniczania przepustowości usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — opis przydziałów, które mają zastosowanie do Usługi IoT Hub i oczekiwanego zachowania ograniczania przepustowości.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284696"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Odwołanie — przydziały i ograniczanie przepustowości usługi IoT Hub

W tym artykule opisano przydziały dla Centrum IoT i zawiera informacje ułatwiające zrozumienie, jak działa ograniczanie przepustowości.

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności

Każda subskrypcja platformy Azure może mieć co najwyżej 50 centrów IoT i co najwyżej 1 bezpłatne centrum.

Każde centrum IoT jest aprowizowane za pomocą określonej liczby jednostek w określonej warstwie. Warstwa i liczba jednostek określają maksymalny dzienny przydział wiadomości, które można wysyłać. Rozmiar wiadomości używany do obliczania dziennego przydziału wynosi 0,5 KB dla centrum warstwy bezpłatnej i 4 KB dla wszystkich innych warstw. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

Warstwa określa również limity ograniczania przepustowości, które Wymusza centrum IoT hub we wszystkich operacjach.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

Podczas publicznej wersji zapoznawczej urządzenia IoT Plug and Play będą wysyłać oddzielne wiadomości na interfejs, co może zwiększyć liczbę wiadomości wliczonych do przydziału wiadomości.

## <a name="operation-throttles"></a>Przepustnice operacyjne

Dławienia operacji są ograniczenia szybkości, które są stosowane w zakresach minut i mają na celu zapobieganie nadużyciom. Są one również przedmiotem [kształtowania ruchu](#traffic-shaping).

W poniższej tabeli przedstawiono wymuszone ograniczenia. Wartości odnoszą się do pojedynczego koncentratora.

| Ograniczenie | Bezpłatne, B1 i S1 | B2 i S2 | B3 i S3 | 
| -------- | ------- | ------- | ------- |
| [Operacje rejestru tożsamości](#identity-registry-operations-throttle) (tworzenie, pobieranie, lista, aktualizowanie, usuwanie) | 1,67/s/jednostka (100/min/jednostka) | 1,67/s/jednostka (100/min/jednostka) | 83,33/s/jednostka (5000/min/jednostka) |
| [Nowe połączenia urządzeń](#device-connections-throttle) (ten limit dotyczy szybkości _nowych połączeń,_ a nie całkowitej liczby połączeń) | Wyższa o 100/s lub 12/s/jednostka <br/> Na przykład dwie jednostki S1 to 2\*12 = 24 nowe połączenia/s, ale masz co najmniej 100 nowych połączeń/s w jednostkach. Dzięki dziewięciu jednostkom S1 masz 108 nowych połączeń/s (9\*12) w swoich jednostkach. | 120 nowych połączeń/s/jednostka | 6000 nowych połączeń/s/jednostki |
| Liczba elementów wysłanych z urządzenia do chmury | Wyższa liczba operacji wysyłania 100/s lub 12 operacji wysyłania/s/jednostka <br/> Na przykład dwie jednostki S1 to 2\*12 = 24/s, ale masz co najmniej 100 operacji wysyłania/s w jednostkach. Z dziewięcioma jednostkami S1 masz 108 operacji\*wysyłania/s (9 12) w swoich jednostkach. | 120 operacji wysyłania/s/jednostka | 6000 operacji wysyłania/s/jednostka |
| Z chmury do urządzenia wysyła<sup>1</sup> | 1,67 operacji wysyłania/s/jednostki (100 wiadomości/min/jednostka) | 1,67 operacji wysyłania/s/jednostki (100 operacji wysyłania/min/jednostka) | 83.33 operacje wysyłania/s/jednostka (5000 operacji wysyłania/min/jednostka) |
| Odbierz<sup>1</sup> z chmury do urządzenia <br/> (tylko wtedy, gdy urządzenie używa protokołu HTTPS)| 16,67 operacji odbioru/s/jednostki (1000 operacji odbioru/min/jednostka) | 16,67 operacji odbioru/s/jednostki (1000 operacji odbioru/min/jednostka) | 833.33 odbierania operacji/s/jednostki (50 000 operacji odbioru/min/jednostka) |
| Przekazywanie plików | 1,67 inicjacji przesyłania plików/s/jednostka (100/min/jednostka) | 1,67 inicjacji przesyłania plików/s/jednostka (100/min/jednostka) | 83.33 inicjowania przekazywania plików/s/jednostka (5000/min/jednostka) |
| Metody bezpośrednie<sup>1</sup> | 160 KB/s/jednostka<sup>2</sup> | 480 KB/s/jednostka<sup>2</sup> | 24 MB/s/jednostka<sup>2</sup> | 
| Zapytania | 20/min/jednostka | 20/min/jednostka | 1000/min/jednostka |
| Twin (urządzenie i moduł) odczytuje<sup>1</sup> | 100/s | Wyższa o 100/s lub 10/s/jednostka | 500/s/jednostka |
| Podwójne aktualizacje (urządzenie i moduł)<sup>1</sup> | 50/s | Wyższa o 50/s lub 5/s/jednostka | 250/s/jednostka |
| Operacje pracy<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 1,67/s/jednostka (100/min/jednostka) | 1,67/s/jednostka (100/min/jednostka) | 83,33/s/jednostka (5000/min/jednostka) |
| Zadania operacyjne urządzenia<sup>1</sup> <br/> (aktualizacja bliźniaczej reprezentacji, wywołać metodę bezpośrednią) | 10/s | Wyższa o 10/s lub 1/s/jednostka | 50/s/jednostka |
| Konfiguracje i wdrożenia brzegowe<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 0,33/s/jednostka (20/min/jednostka) | 0,33/s/jednostka (20/min/jednostka) | 0,33/s/jednostka (20/min/jednostka) |
| Szybkość inicjowania strumienia urządzenia<sup>1</sup> | 5 nowych strumieni/s | 5 nowych strumieni/s | 5 nowych strumieni/s |
| Maksymalna liczba jednocześnie podłączonych strumieni urządzeń<sup>1</sup> | 50 | 50 | 50 |
| Maksymalny transfer danych strumienia urządzenia<sup>1</sup> (łączna objętość dziennie) | 300 MB | 300 MB | 300 MB |

<sup>1.</sup> Ta funkcja nie jest dostępna w podstawowej warstwie Usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [Jak wybrać odpowiednie Centrum IoT .](iot-hub-scaling.md) <br/><sup>2.</sup> Rozmiar miernika ograniczania wynosi 4 KB.

### <a name="throttling-details"></a>Szczegóły ograniczania przepustowości

* Rozmiar licznika określa, w jakich przyrostach limit ograniczania przepustowości jest zużywany. Jeśli ładunek połączenia bezpośredniego wynosi od 0 do 4 KB, jest liczony jako 4 KB. Przed osiągnięciem limitu 160 KB/s/jednostki można wykonać maksymalnie 40 połączeń na sekundę na jednostkę.

   Podobnie, jeśli ładunek wynosi od 4 KB do 8 KB, każde wywołanie odpowiada za 8 KB i można wykonać maksymalnie 20 wywołań na sekundę na jednostkę przed osiągnięciem maksymalnego limitu.

   Na koniec, jeśli rozmiar ładunku wynosi od 156 KB do 160 KB, będziesz mógł wykonać tylko 1 wywołanie na sekundę na jednostkę w koncentratorze przed osiągnięciem limitu 160 KB/s/jednostki.

*  W *przypadku operacji urządzeń zadań (aktualizacja bliźniaczej reprezentacji, wywołaj metodę bezpośrednią)* dla warstwy S2, 50/s/jednostka ma zastosowanie tylko podczas wywoływania metod przy użyciu zadań. Jeśli wywołasz metody bezpośrednie bezpośrednio, stosuje się oryginalny limit ograniczania przepustowości wynoszący 24 MB/s/jednostkę (dla S2).

*  **Przydział** to łączna liczba wiadomości, które można wysyłać w centrum *dziennie.* Limit przydziału centrum można znaleźć w kolumnie **Całkowita liczba wiadomości /dzień** na [stronie cennik Centrum IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Przepustnice między chmurami i urządzenia do chmury określają *maksymalną szybkość* wysyłania wiadomości — liczbę wiadomości, niezależnie od fragmentów 4 KB. Każda wiadomość może mieć maksymalnie 256 KB, co jest [maksymalnym rozmiarem wiadomości.](iot-hub-devguide-quotas-throttling.md#other-limits)

*  Jest to dobra praktyka, aby ograniczyć połączenia, tak aby nie trafić / przekroczyć limity przepustnicy. Jeśli nie zostanie osiągnięty limit, Usługa IoT Hub odpowiada kodem błędu 429 i klient powinien wykonać i ponowić próbę. Te limity są na koncentrator (lub w niektórych przypadkach na koncentrator/jednostkę). Aby uzyskać więcej informacji, zobacz [Zarządzanie łącznością i niezawodnymi wzorcami obsługi wiadomości/ponawianie prób](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Kształtowanie ruchu

Aby uwzględnić ruch seryjny, Usługa IoT Hub akceptuje żądania nad przepustnicą przez ograniczony czas. Pierwsze kilka z tych żądań są przetwarzane natychmiast. Jednak jeśli liczba żądań nadal narusza przepustnicy, IoT Hub rozpoczyna umieszczanie żądań w kolejce i przetwarzane z szybkością limitu. Efekt ten nazywany jest *kształtowaniem ruchu*. Ponadto rozmiar tej kolejki jest ograniczony. Jeśli naruszenie przepustnicy będzie kontynuowane, kolejka zostanie zapełniona, a centrum `429 ThrottlingException`IoT Hub rozpocznie odrzucanie żądań za pomocą programu .

Na przykład za pomocą symulowanego urządzenia można wysyłać 200 komunikatów z urządzenia do chmury na sekundę do centrum IoT hub s1 (który ma limit 100/s D2C wysyła). W pierwszej lub dwóch minutach wiadomości są przetwarzane natychmiast. Jednak ponieważ urządzenie nadal wysyłać więcej wiadomości niż limit przepustnicy, Centrum IoT zaczyna przetwarzać tylko 100 wiadomości na sekundę i umieszcza resztę w kolejce. Zaczynasz zauważyć zwiększone opóźnienie. Po pewnym czasie `429 ThrottlingException` rozpoczniesz uzyskiwanie w miarę wypełniania kolejki, a "liczba błędów przepustnicy" w [metrykach usługi IoT Hub](iot-hub-metrics.md) zaczyna rosnąć.

### <a name="identity-registry-operations-throttle"></a>Przepustnicy operacji rejestru tożsamości

Operacje rejestru tożsamości urządzenia są przeznaczone do użycia w czasie wykonywania w scenariuszach zarządzania urządzeniami i inicjowania obsługi administracyjnej. Odczytywanie lub aktualizowanie dużej liczby tożsamości urządzeń jest obsługiwane przez [zadania importu i eksportu](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Przepustnica połączeń urządzeń

Przepustnica *połączeń urządzeń* reguluje szybkość, z jaką można nawiązać nowe połączenia urządzeń za pomocą koncentratora IoT hub. Przepustnica *połączeń urządzenia* nie reguluje maksymalnej liczby jednocześnie podłączonych urządzeń. Szybkość *przepustnicy połączeń urządzeń* zależy od liczby jednostek, które są aprowizacji dla IoT hub.

Na przykład, jeśli kupisz jedną jednostkę S1, otrzymasz przepustnicę 100 połączeń na sekundę. W związku z tym aby podłączyć 100 000 urządzeń, trwa co najmniej 1000 sekund (około 16 minut). Jednak możesz mieć tyle jednocześnie podłączonych urządzeń, ile masz urządzeń zarejestrowanych w rejestrze tożsamości.

## <a name="other-limits"></a>Inne limity

Usługa IoT Hub wymusza inne limity operacyjne:

| Operacja | Limit |
| --------- | ----- |
| Urządzenia | Całkowita liczba urządzeń plus moduły, które mogą być zarejestrowane w jednym centrum IoT jest ograniczona do 1 000 000. Jedynym sposobem na zwiększenie tego limitu jest skontaktowanie się z [pomocą techniczną firmy Microsoft.](https://azure.microsoft.com/support/options/)|
| Operacje przekazywania plików | 10 równoczesnych plików przesłanych na urządzenie. |
| Oferty pracy<sup>1</sup> | Maksymalna liczba równoczesnych zadań to 1 (dla wolnych i S1), 5 (dla S2) i 10 (dla S3). Jednak maksymalna równoczesna [liczba zadań importowania/eksportowania urządzeń](iot-hub-bulk-identity-mgmt.md) wynosi 1 dla wszystkich warstw. <br/>Historia zadań jest zachowywana do 30 dni. |
| Dodatkowe punkty końcowe | Płatne centra jednostek SKU mogą mieć 10 dodatkowych punktów końcowych. Wolne koncentratory jednostek SKU mogą mieć jeden dodatkowy punkt końcowy. |
| Zapytania routingu wiadomości | Płatne centra jednostek SKU mogą mieć 100 zapytań routingu. Wolne koncentratory jednostek SKU mogą mieć pięć zapytań routingu. |
| Wzbogacenia wiadomości | Płatne centra jednostek SKU mogą mieć maksymalnie 10 wzbogacenia wiadomości. Bezpłatne koncentratory jednostek SKU mogą mieć maksymalnie 2 wzbogacenia wiadomości.|
| Wiadomości między urządzeniami a chmurą | Maksymalny rozmiar wiadomości 256 KB |
| Wiadomości między chmurami a urządzeniem<sup>1</sup> | Maksymalny rozmiar wiadomości 64 KB. Maksymalna liczba oczekujących wiadomości do dostarczenia wynosi 50 na urządzenie. |
| Metoda bezpośrednia<sup>1</sup> | Maksymalny rozmiar ładunku metody bezpośredniej wynosi 128 KB. |
| Automatyczne konfiguracje urządzeń i modułów<sup>1</sup> | 100 konfiguracji na płatne centrum SKU. 20 konfiguracji na bezpłatny koncentrator SKU. |
| Automatyczne wdrożenia usługi IoT Edge<sup>1</sup> | 50 modułów na wdrożenie. 100 wdrożeń (w tym wdrożeń warstwowych) na płatne centrum SKU. 10 wdrożeń na bezpłatne centrum SKU. |
| Bliźnięta<sup>1</sup> | Maksymalny rozmiar żądanych właściwości i zgłoszonych sekcji właściwości wynosi 32 KB każda. Maksymalny rozmiar sekcji znaczników to 8 KB. |
| Zasady dostępu współużytkowanego | Maksymalna liczba zasad dostępu współdzielonego to 16 |

<sup>1.</sup> Ta funkcja nie jest dostępna w podstawowej warstwie Usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [Jak wybrać odpowiednie Centrum IoT .](iot-hub-scaling.md)

## <a name="increasing-the-quota-or-throttle-limit"></a>Zwiększanie limitu przydziału lub przepustnicy

W dowolnym momencie można zwiększyć przydziały lub limity przepustnicy, [zwiększając liczbę aprowizowanych jednostek w centrum IoT](iot-hub-upgrade.md)hub .

## <a name="latency"></a>Opóźnienie

Centrum IoT hub stara się zapewnić małe opóźnienia dla wszystkich operacji. Jednak ze względu na warunki sieciowe i inne nieprzewidywalne czynniki nie może zagwarantować pewnego opóźnienia. Podczas projektowania rozwiązania należy:

* Należy unikać żadnych założeń dotyczących maksymalnego opóźnienia dowolnej operacji Usługi IoT Hub.
* Aprowizuj centrum IoT hub w regionie platformy Azure najbliżej twoich urządzeń.
* Należy rozważyć użycie usługi Azure IoT Edge do wykonywania operacji z uwzględnieniem opóźnień na urządzeniu lub na bramie w pobliżu urządzenia.

Wiele jednostek Usługi IoT Hub wpływa na ograniczanie przepustowości zgodnie z wcześniejszym opisem, ale nie zapewniają żadnych dodatkowych korzyści z opóźnienia lub gwarancji.

Jeśli widzisz nieoczekiwany wzrost opóźnienia operacji, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z dogłębną dyskusją na temat zachowania ograniczania przepustowości usługi IoT Hub, zobacz wpis w blogu [IoT Hub ograniczania i Ciebie](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md)
