---
title: Obsługa platformy Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Platformy obsługiwane przez krawędź IoT Azure
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 05a571c4491122ec5c7c35f6bccc4b8c332a4be2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130713"
---
# <a name="azure-iot-edge-support"></a>Obsługa krawędzi IoT Azure
Istnieją różne sposoby poszukuje produktu Azure IoT krawędzi.

**Raportowanie błędów** — większość programowanie, który jest przesyłany do produktu Azure IoT krawędzi odbywa się w projekt open source IoT krawędzi. Błędy mogą być zgłaszane na [strony problemy](https://github.com/azure/iotedge/issues) projektu. Poprawki należy szybko z projektu w drodze do aktualizacji produktu.

**Zespół obsługi klienta firmy Microsoft** — użytkownicy, którzy mają [plan pomocy technicznej](https://azure.microsoft.com/support/plans/) można Uwzględnij zespół obsługi klienta firmy Microsoft przez tworzenie biletu pomocy technicznej bezpośrednio z [portalu Azure]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funkcja żądań** — produktu Azure IoT krawędzi śledzi żądania funkcji za pomocą produktu [User Voice strony](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Systemy operacyjne
Krawędź IoT Azure działa na większości systemów operacyjnych, które można uruchomić kontenery; Jednak wszystkie te są równie nieobsługiwane. Systemy operacyjne są pogrupowane w warstw, które reprezentują poziomu obsługi, które użytkownicy mogą oczekiwać.

### <a name="tier-1"></a>Warstwa 1
Systemy warstwy 1 można traktować jako oficjalnie obsługiwana. Oznacza to, że firmy Microsoft:
* jest tych systemów operacyjnych w testów automatycznych
* zawiera pakiety instalacyjne dla nich

Ogólnie dostępna
* Ubuntu Server 18.04
* Ubuntu Server 16.04
* Raspbian stretch

W publicznej wersji zapoznawczej
* Serwer systemu Windows 10 1803
* Windows 10 IoT Enterprise (z kwietnia 2018 aktualizacji)
* Windows 10 IoT Core (z kwietnia 2018 aktualizacji)

### <a name="tier-2"></a>Warstwa 2
Systemy warstwy 2 można traktować jako zgodna z Azure IoT Edge i względnie łatwo można użyć. Oznacza to, że:
* Wystąpiło ad hoc testowania na platformach firmy Microsoft lub zna partnera pomyślnie uruchomione na platformie Azure IoT krawędzi
* Pakiety instalacyjne dla innych platform może działać na tych platformach

Ubuntu 18.04

Ubuntu 16.04

Rzeka knie 8

Yocto

Debian

Mac

## <a name="container-engines"></a>Aparaty kontenera
Azure IoT krawędzi wymaga aparatu kontenera, można uruchomić modułów, niezależnie od systemu operacyjnego, na którym jest uruchomiony. Firma Microsoft udostępnia z kontenera aparatu moby aparatu, aby spełnić to wymaganie. Jest on oparty na Moby projekt open source. Docker CE i Docker EE są aparatów innych popularnych kontenera. One również są oparte na projektu źródłowego otwiera Moby i są zgodne z Azure IoT krawędzi. Firma Microsoft udostępnia najlepsze nakładu obsługę systemów przy użyciu tych silników kontenera. Microsoft nie ma jednak możliwości na potrzeby wysłania poprawki dotyczące problemów w tych obiektach. Dlatego firma Microsoft zaleca używanie aparatu moby w systemach produkcyjnych.


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 
