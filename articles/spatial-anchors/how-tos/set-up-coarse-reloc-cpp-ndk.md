---
title: Duże relokalizacja w C++/NDK
description: Szczegółowe wyjaśnienie sposobu tworzenia i lokalizowania kotwic przy użyciu czujników na urządzeniach w C++/NDK.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e505e6a8a3c9f28905c1b1fc3d90dbeee959a793
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277245"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-cndk"></a>Jak utworzyć i zlokalizować kotwice przy użyciu czujników na urządzeniach w C++/NDK

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [System Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Kotwice przestrzenne platformy Azure mogą kojarzyć dane z czujników na urządzeniu, umieszczając w nich kotwice. Te dane mogą również służyć do szybkiego ustalenia, czy w pobliżu urządzenia znajdują się jakieś kotwice. Aby uzyskać więcej informacji, zobacz artykuł o [niedużej lokalizacji](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, upewnij się, że masz:

- Podstawowa wiedza na C++ temat i <a href="https://developer.android.com/ndk/" target="_blank">zestaw Android Native Development Kit</a>.
- Zapoznaj się z [omówieniem zakotwiczenia przestrzennego platformy Azure](../overview.md).
- Ukończono jedno z [5-minutowych przewodników szybki start](../index.yml).
- Zapoznaj się z [tematem tworzenie i lokalizowanie kotwic](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Allow GPS
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);

// Allow WiFi scanning
sensors->WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors->BluetoothEnabled(true);
sensors->KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);

// Configure the near-device criteria
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();
nearDeviceCriteria->DistanceInMeters(5.0f);
nearDeviceCriteria->MaxResultCount(25);

// Set the session's locate criteria
auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]