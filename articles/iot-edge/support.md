---
title: Obsługa Platform do usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Platformy obsługiwane przez usługi Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: adf017c57cf7c895a0da8fec70e89dda0a314693
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277923"
---
# <a name="azure-iot-edge-support"></a>Obsługa usługi Azure IoT Edge
Istnieje wiele sposobów, aby wyszukać pomocy technicznej dla produktu usługi Azure IoT Edge.

**Raportowania błędów** — większość programowania, który przechodzi do produktów Azure IoT Edge odbywa się w projekt open source usługi IoT Edge. Błędy mogą być zgłaszane na [strony problemy](https://github.com/azure/iotedge/issues) projektu. Poprawki należy szybko z projektu w drodze do aktualizacji produktów.

**Zespół obsługi klienta firmy Microsoft** — użytkowników, którzy mają [plan pomocy technicznej](https://azure.microsoft.com/support/plans/) zaangażować zespół obsługi klienta firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z [witryny Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Żądania funkcji** — produktu usługi Azure IoT Edge śledzi żądania funkcji za pomocą produktu [stronę z opiniami użytkowników](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Systemy operacyjne
Usługa Azure IoT Edge działa na większości systemów operacyjnych, które umożliwia uruchamianie kontenerów; Jednak wszystkie te nie są równie obsługiwane. Systemy operacyjne są podzielone na warstwy, które reprezentują poziom pomocy technicznej, które użytkownicy mogą oczekiwać.

### <a name="tier-1"></a>Warstwa 1
Warstwa 1 systemów można traktować jako oficjalnie obsługiwany. Oznacza to, że firmy Microsoft:
* jest tych systemów operacyjnych w testach automatycznych
* udostępnia pakiety instalacyjne dla nich

Ogólnie dostępna
| System operacyjny | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Raspbian stretch | Nie | Yes|
| Ubuntu Server 16.04 | Yes | Nie |
| Serwer Ubuntu 18.04 | Yes | Nie |

Publiczna wersja zapoznawcza
| System operacyjny | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core (aktualizacja z kwietnia 2018 r.) | Yes | Nie |
| Windows 10 IoT Enterprise (aktualizacja z kwietnia 2018 r.) | Yes | Nie |
| Serwer systemu Windows 10 1803 | Yes | Nie |

### <a name="tier-2"></a>Warstwa 2
Systemy warstwy 2 można traktować jako zgodna z usługą Azure IoT Edge i może służyć stosunkowo łatwe. Oznacza to, że:
* Wystąpiło ad hoc testowania na platformach firmy Microsoft lub zna partnerem, który został pomyślnie uruchomiony usługi Azure IoT Edge jest
* Pakiety instalacyjne dla innych platform może działać na tych platformach

| System operacyjny | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS w wersji 7.5 | Yes | Yes |
| Debian 8 | Yes | Yes |
| Debian 9 | Yes | Yes |
| RHEL W WERSJI 7.5 | Yes | Yes |
| Ubuntu 18.04 | Yes | Yes |
| Ubuntu 16.04 | Yes | Yes |
| Rzeka wiatru 8 | Yes | Nie |
| Yocto | Yes | Nie |

## <a name="container-engines"></a>Aparaty kontenera
Usługa Azure IoT Edge wymaga aparatu kontenera, można uruchomić moduły, niezależnie od systemu operacyjnego, na którym jest uruchomiony. Firma Microsoft udostępnia z kontenera aparatu moby aparatu, aby spełnić to wymaganie. Jest ona oparta na Moby projekt open source. Docker CE i Docker EE są innymi aparatami popularnych kontenera. One również są oparte na projekt źródłowy zostanie otwarta Moby i są zgodne z usługą Azure IoT Edge. Firma Microsoft oferuje optymalną obsługą systemów przy użyciu tych silników kontenera. Microsoft nie ma jednak możliwości na potrzeby wysłania poprawki dotyczące problemów w nich. Z tego powodu firma Microsoft zaleca używanie aparatu moby w systemach produkcyjnych.

