---
title: Narzędzie sieci szkieletowej usługi Azure CLI- sfctl
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń i podgrup.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906222"
---
# <a name="sfctl"></a>sfctl
Polecenia do zarządzania klastrami sieci szkieletowej usług i encjami. Ta wersja jest zgodna ze środowiska uruchomieniowego usługi Service Fabric 7.0.

Polecenia podążają za wzorcem rzeczownik-czasownik. Zobacz podgrupy, aby uzyskać więcej informacji.

## <a name="subgroups"></a>Podgrup
|Podgrupy|Opis|
| --- | --- |
| [aplikacja](service-fabric-sfctl-application.md) | Tworzenie, usuwanie i zarządzanie aplikacjami i typami aplikacji. |
| [Chaos](service-fabric-sfctl-chaos.md) | Start, zatrzymaj i raport na temat usługi testu chaosu. |
| [Klastra](service-fabric-sfctl-cluster.md) | Wybierz klastry sieci szkieletowej usług, zarządzaj nimi i obsługa ich. |
| [Komponować](service-fabric-sfctl-compose.md) | Tworzenie, usuwanie i zarządzanie aplikacjami docker compose. |
| [Kontenera](service-fabric-sfctl-container.md) | Uruchamianie poleceń związanych z kontenerem w węźle klastra. |
| [Zdarzenia](service-fabric-sfctl-events.md) | Pobieranie zdarzeń z magazynu zdarzeń (jeśli usługa EventStore jest już zainstalowana). |
| [is](service-fabric-sfctl-is.md) | Wysyłaj kwerendy i wysyłaj polecenia do usługi infrastruktury. |
| [siatka](service-fabric-sfctl-mesh.md) | Usuwanie aplikacji sieci szkieletowej sieci szkieletowej usług i zarządzanie nimi. |
| [Węzła](service-fabric-sfctl-node.md) | Zarządzanie węzłami tworzącymi klaster. |
| [Partycji](service-fabric-sfctl-partition.md) | Kwerenda i zarządzanie partycjami dla dowolnej usługi. |
| [Właściwość](service-fabric-sfctl-property.md) | Przechowuj i wysyłaj zapytania właściwości w obszarze Nazwy sieci szkieletowej usług. |
| [Repliki](service-fabric-sfctl-replica.md) | Zarządzanie replikami należącymi do partycji usługi. |
| [Rpm](service-fabric-sfctl-rpm.md) | Wysyłaj kwerendy i wysyłaj polecenia do usługi menedżera napraw. |
| [sa-klaster](service-fabric-sfctl-sa-cluster.md) | Zarządzanie autonomicznymi klastrami sieci szkieletowej usług. |
| [Usługi](service-fabric-sfctl-service.md) | Tworzenie, usuwanie i zarządzanie usługami, typami usług i pakietami usług. |
| [ustawienia](service-fabric-sfctl-settings.md) | Skonfiguruj ustawienia lokalne do tego wystąpienia sfctl. |
| [sklep](service-fabric-sfctl-store.md) | Wykonywanie podstawowych operacji na poziomie pliku w magazynie obrazów klastra. |

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)