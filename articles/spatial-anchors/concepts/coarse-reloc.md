---
title: Zgrowawienie
description: Dowiedz się więcej o korzystaniu z przelokalizacji grubej, aby znaleźć kotwice w pobliżu.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844398"
---
# <a name="coarse-relocalization"></a>Zgrubna ponowna lokalizacja

Gruba relokalizacja to funkcja, która zapewnia wstępną odpowiedź na pytanie: *Gdzie jest teraz moje urządzenie / Jakie treści należy obserwować?* Odpowiedź nie jest precyzyjna, ale zamiast tego jest w formie: *Jesteś blisko tych kotwic; spróbuj zlokalizować jedną z nich*.

Grubozwiązkowa relokalizacja polega na kojarzeniu różnych odczytów czujników na urządzeniu zarówno z tworzeniem, jak i wykonywaniem zapytań o kotwice. W scenariuszach zewnętrznych dane z czujników są zazwyczaj położeniem urządzenia GPS (Global Positioning System). Jeśli gps nie jest dostępny lub zawodne (np. w pomieszczeniach), dane czujnika składają się z punktów dostępu WiFi i sygnałów nawigacyjnych Bluetooth w zasięgu. Wszystkie zebrane dane z czujników przyczyniają się do utrzymania indeksu przestrzennego, który jest używany przez kotwice przestrzenne platformy Azure do szybkiego określenia zakotwiczeń, które znajdują się w odległości około 100 metrów od urządzenia.

Szybkie wyszukiwanie kotew dzięki zgęstnej relokalizacji upraszcza rozwój aplikacji wspieranych przez światowe kolekcje (powiedzmy milionów geo-rozproszonych) kotew. Złożoność zarządzania kotwicą jest ukryta, co pozwala skupić się bardziej na niesamowitej logice aplikacji. Wszystkie kotwicy podnoszenia ciężkich jest wykonywana za kulisami przez Azure Spatial Anchors.

## <a name="collected-sensor-data"></a>Zebrane dane z czujników

Dane czujnika, które można wysłać do usługi kotwiczenia, są jedną z następujących czynności:

* Pozycja GPS: szerokość geograficzna, długość geograficzna, wysokość.
* Siła sygnału punktów dostępu WiFi w zasięgu.
* Siła sygnału sygnału sygnału Bluetooth w zasięgu.

Ogólnie rzecz biorąc, aplikacja będzie musiała uzyskać uprawnienia specyficzne dla urządzenia, aby uzyskać dostęp do danych GPS, Wi-Fi lub BLE. Ponadto niektóre z powyższych danych czujników nie są dostępne zgodnie z projektem na niektórych platformach. Aby uwzględnić te sytuacje, zbieranie danych z czujników jest opcjonalne i jest domyślnie wyłączone.

## <a name="set-up-the-sensor-data-collection"></a>Konfigurowanie zbierania danych czujników

Zacznijmy od utworzenia dostawcy odcisków palców czujnika i uświadomienia sesji:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Następnie musisz zdecydować, które czujniki chcesz użyć do grubej relokalizacji. Ta decyzja jest specyficzna dla aplikacji, którą opracowujesz, ale zalecenia w poniższej tabeli powinny dać dobry punkt wyjścia:


|             | Pomieszczeniach | Zewnątrz |
|-------------|---------|----------|
| Gps         | Wyłączone | Włączone |
| Sieć Wi-Fi        | Włączone | Wł.(opcjonalnie) |
| Sygnalizatory BLE | Włączone (opcjonalnie z zastrzeżeniami, patrz poniżej) | Wyłączone |


### <a name="enabling-gps"></a>Włączanie gps

Zakładając, że aplikacja ma już uprawnienia dostępu do pozycji GPS urządzenia, można skonfigurować azure przestrzenne kotwice, aby go używać:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Podczas korzystania z GPS w aplikacji należy pamiętać, że odczyty dostarczane przez sprzęt są zazwyczaj:

* asynchronicznego i niskiej częstotliwości (poniżej 1 Hz).
* zawodne / hałaśliwe (średnio 7-m odchylenie standardowe).

Ogólnie rzecz biorąc, zarówno system operacyjny urządzenia, jak i kotwice przestrzenne platformy Azure wykonają filtrowanie i ekstrapolację na nieprzetworzonym sygnale GPS, próbując ograniczyć te problemy. To dodatkowe przetwarzanie wymaga dodatkowego czasu na zbieżność, więc aby uzyskać najlepsze wyniki, należy spróbować:

* utworzyć jeden dostawca odcisków palców czujnika jak najwcześniej w aplikacji
* utrzymywanie przy życiu dostawcy odcisków palców czujnika między wieloma sesjami
* udostępnianie dostawcy odcisków palców czujnika między wieloma sesjami

Jeśli planujesz używać dostawcy odcisków palców czujnika poza sesją kotwicy, upewnij się, że uruchomisz go przed żądaniem oszacowania czujnika. Na przykład poniższy kod zajmie się aktualizowaniem pozycji urządzenia na mapie w czasie rzeczywistym:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Włączanie sieci Wi-Fi

Zakładając, że aplikacja ma już uprawnienia dostępu do stanu WiFi urządzenia, można skonfigurować zakotwiczenia przestrzenne platformy Azure, aby z niej korzystać:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Podczas korzystania z sieci Wi-Fi w aplikacji należy pamiętać, że odczyty dostarczane przez sprzęt są zazwyczaj:

* asynchronicznego i niskiej częstotliwości (poniżej 0,1 Hz).
* potencjalnie ograniczona na poziomie systemu operacyjnego.
* zawodne / hałaśliwe (średnio odchylenie standardowe 3-dBm).

Zakotwiczenia przestrzenne platformy Azure spróbuje utworzyć mapę siły sygnału sieci Wi-Fi podczas sesji, próbując ograniczyć te problemy. Aby uzyskać najlepsze wyniki, należy spróbować:

* utworzyć sesję na długo przed umieszczeniem pierwszej kotwicy.
* utrzymać sesję przy życiu tak długo, jak to możliwe (to jest, utworzyć wszystkie kotwice i kwerendy w jednej sesji).

### <a name="enabling-bluetooth-beacons"></a>Włączanie sygnałów nawigacyjnych Bluetooth

Zakładając, że aplikacja ma już uprawnienia dostępu do stanu Bluetooth urządzenia, można skonfigurować zakotwiczenia przestrzenne platformy Azure, aby z niej korzystać:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Sygnalizatory są zazwyczaj wszechstronnymi urządzeniami, w których można skonfigurować wszystko — w tym identyfikatory UUID i adresy MAC. Ta elastyczność może być problematyczne dla azure przestrzenne kotwice, ponieważ uważa, że sygnały nawigacyjne są jednoznacznie identyfikowane przez ich identyfikatory UUID. Niezapewnienie tej wyjątkowości najprawdopodobniej spowoduje przestrzenne tunele czasoprzestrzenniowe. Aby uzyskać najlepsze wyniki, należy:

* przypisać unikatowe identyfikatory UUD do sygnałów nawigacyjnych.
* wdrożyć je — zazwyczaj w regularnych wzorców, takich jak siatka.
* przekazać listę unikalnych identyfikatorów UUI DO dostawcy odcisków palców czujnika:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Zakotwiczenia przestrzenne platformy Azure będą śledzić tylko sygnały nawigacyjne Bluetooth, które znajdują się na liście znanych identyfikatorów UUID zbliżeniową sygnału sygnalizatora. Złośliwe sygnały nawigacyjne zaprogramowane tak, aby zezwalały na identyfikatory UUID, mogą jednak negatywnie wpłynąć na jakość usługi. Z tego powodu należy używać sygnalizatorów tylko w wyselekcjonowanych przestrzeniach, w których można kontrolować ich wdrożenie.

## <a name="querying-with-sensor-data"></a>Wykonywanie zapytań z danymi z czujnika

Po utworzeniu kotwic ze skojarzonymi danymi z czujników można rozpocząć ich pobieranie za pomocą odczytów czujników zgłoszonych przez urządzenie. Nie musisz już dostarczać usługi listy znanych kotwic, które oczekujesz od niego , zamiast tego po prostu poinformuj usługę o lokalizacji urządzenia zgłoszonej przez wbudowane czujniki. Kotwice przestrzenne platformy Azure następnie dowiedzieć się zestaw zakotwiczeń w pobliżu urządzenia i spróbuj wizualnie dopasować je.

Aby zapytania korzystały z danych z czujnika, zacznij od utworzenia kryteriów "w pobliżu urządzenia":

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Parametr `DistanceInMeters` określa, jak daleko będziemy eksplorować wykres zakotwiczenia, aby pobrać zawartość. Załóżmy na przykład, że zapełniłeś trochę miejsca kotwicami o stałej gęstości 2 co metr. Ponadto kamera w urządzeniu obserwuje pojedynczą kotwicę, a usługa pomyślnie ją zlokalizowała. Najprawdopodobniej chcesz pobrać wszystkie kotwice, które umieściłeś w pobliżu, a nie pojedynczą kotwicę, którą aktualnie obserwujesz. Zakładając, że umieszczone kotwice są połączone na wykresie, usługa może pobrać wszystkie pobliskie kotwice, podążając za krawędziami wykresu. Ilość wykresu przechodzi przez przechodzenie `DistanceInMeters`wykonane jest kontrolowana przez ; otrzymasz wszystkie kotwice podłączone do tej, którą znajdujesz, `DistanceInMeters`które są bliżej niż .

Należy pamiętać, że `MaxResultCount` duże wartości dla mogą negatywnie wpłynąć na wydajność. Ustaw go na rozsądną wartość dla aplikacji.

Na koniec musisz poinformować sesję, aby użyć wyszukiwania opartego na czujnikach:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Oczekiwane wyniki

Urządzenia GPS klasy konsumenckiej są zazwyczaj dość nieprecyzyjne. Badanie przeprowadzone przez [Zandenbergen i Barbeau (2011)][6] raportuje medianą dokładności telefonów komórkowych z wspomaganym GPS (A-GPS) na około 7 metrów - dość dużą wartość do zignorowania! Aby uwzględnić te błędy pomiarowe, usługa traktuje kotwice jako rozkład prawdopodobieństwa w przestrzeni GPS. Jako takie, kotwica jest teraz obszar przestrzeni, który najprawdopodobniej (czyli z ponad 95% zaufania) zawiera swoją prawdziwą, nieznaną pozycję GPS.

To samo rozumowanie jest stosowane podczas wykonywania zapytań z GPS. Urządzenie jest reprezentowane jako kolejny przestrzenny obszar zaufania wokół swojej prawdziwej, nieznanej pozycji GPS. Odkrywanie pobliskich kotwic przekłada się na zwykłe znajdowanie kotwic z obszarami zaufania *wystarczająco blisko* regionu zaufania urządzenia, jak pokazano na poniższym obrazku:

![Wybór kandydatów kotwicy z GPS](media/coarse-reloc-gps-separation-distance.png)

Dokładność sygnału GPS, zarówno podczas tworzenia kotwicy, jak i podczas zapytań, ma duży wpływ na zestaw zwróconych kotwic. W przeciwieństwie do zapytań opartych na WiFi / beacons rozważy wszystkie kotwice, które mają co najmniej jeden punkt dostępu / beacon wspólnego z zapytaniem. W tym sensie wynik zapytania opartego na WiFi / beacons jest w większości określany przez fizyczny zasięg punktów dostępu / sygnalizatorów i przeszkód środowiskowych.

Poniższa tabela szacuje oczekiwaną przestrzeń wyszukiwania dla każdego typu czujnika:

| Czujnik      | Promień przestrzeni wyszukiwania (ok.) | Szczegóły |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | Określa niepewność GPS wśród innych czynników. Podane liczby są szacowane na średnią dokładność GPS telefonów komórkowych z A-GPS, czyli 7 metrów. |
| Sieć Wi-Fi        | 50 m - 100 m | Zależy od zasięgu punktów dostępu bezprzewodowego. Zależy od częstotliwości, siły nadajnika, przeszkód fizycznych, zakłóceń i tak dalej. |
| Sygnalizatory BLE |  70 m | Określa zasięg sygnału sygnalizatora. Zależy od częstotliwości, siły transmisji, przeszkód fizycznych, zakłóceń i tak dalej. |

## <a name="per-platform-support"></a>Obsługa platformy

W poniższej tabeli podsumowano dane z czujników zebrane na każdej z obsługiwanych platform, wraz z wszelkimi zastrzeżeniami specyficznymi dla platformy:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | Nie dotyczy | Obsługiwane za pośrednictwem interfejsów API [LocationManager][3] (zarówno GPS, jak i NETWORK) | Obsługiwane za pośrednictwem interfejsów API [programu CLLocationManager][4] |
| Sieć Wi-Fi        | Obsługiwane z szybkością około jednego skanowania co 3 sekundy | Obsługiwane. Począwszy od poziomu API 28, skanowanie WiFi jest ograniczane do 4 połączeń co 2 minuty. Z systemu Android 10 ograniczanie przepustowości można wyłączyć w menu Ustawienia dewelopera. Aby uzyskać więcej informacji, zobacz [dokumentację systemu Android][5]. | Nie dotyczy - brak publicznego interfejsu API |
| Sygnalizatory BLE | Ograniczone do [Eddystone][1] i [iBeacon][2] | Ograniczone do [Eddystone][1] i [iBeacon][2] | Ograniczone do [Eddystone][1] i [iBeacon][2] |

## <a name="next-steps"></a>Następne kroki

Użyj grubozległej relokalizacji w aplikacji.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Cel C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
