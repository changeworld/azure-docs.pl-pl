---
title: Obsługiwane systemy operacyjne, aparaty kontenera — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, które systemy operacyjne można uruchomić demona usługi Azure IoT Edge i środowisko uruchomieniowe i aparatów obsługiwane kontenerów urządzeń z systemem produkcyjnym
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d8059ac4965ce5582b899ebc0d765e00ae9deb35
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892800"
---
# <a name="azure-iot-edge-supported-systems"></a>Usługa Azure IoT Edge obsługiwanych systemów

Istnieje wiele sposobów, aby wyszukać pomocy technicznej dla produktu usługi Azure IoT Edge.

**Raportowania błędów** — większość programowania, który przechodzi do produktów Azure IoT Edge odbywa się w projekt open source usługi IoT Edge. Błędy mogą być zgłaszane na [strony problemy](https://github.com/azure/iotedge/issues) projektu. Poprawki należy szybko z projektu w drodze do aktualizacji produktów.

**Zespół obsługi klienta firmy Microsoft** — użytkowników, którzy mają [plan pomocy technicznej](https://azure.microsoft.com/support/plans/) zaangażować zespół obsługi klienta firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z [witryny Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Żądania funkcji** — produktu usługi Azure IoT Edge śledzi żądania funkcji za pomocą produktu [stronę z opiniami użytkowników](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Aparaty kontenera
Usługa Azure IoT Edge wymaga aparatu kontenera, aby uruchomić modułów, ponieważ są one zaimplementowane jako kontenery. Firma Microsoft udostępnia z kontenera aparatu moby aparatu, aby spełnić to wymaganie. Jest ona oparta na Moby projekt open source. Docker CE i Docker EE są innymi aparatami popularnych kontenera. One również są oparte na Moby projektu open-source i są zgodne z usługą Azure IoT Edge. Firma Microsoft oferuje optymalną obsługą systemów przy użyciu tych silników kontenera. Microsoft nie ma jednak możliwości na potrzeby wysłania poprawki dotyczące problemów w nich. Z tego powodu firma Microsoft zaleca używanie aparatu moby w systemach produkcyjnych.

<br>
<center>
![Moby jako kontener środowiska uruchomieniowego](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Systemy operacyjne
Usługa Azure IoT Edge działa na większości systemów operacyjnych, które umożliwia uruchamianie kontenerów; Jednak wszystkie te nie są równie obsługiwane. Systemy operacyjne są podzielone na warstwy, które reprezentują poziom pomocy technicznej, które użytkownicy mogą oczekiwać.
* Warstwa 1 systemów można traktować jako oficjalnie obsługiwany. Oznacza to, że firmy Microsoft:
    * jest to system operacyjny w testach automatycznych
    * udostępnia pakiety instalacyjne dla nich
* Systemy warstwy 2 można traktować jako zgodna z usługą Azure IoT Edge i może służyć stosunkowo łatwe. Oznacza to, że:
    * Wystąpiło ad hoc testowania na platformach firmy Microsoft lub zna partnerem, który został pomyślnie uruchomiony usługi Azure IoT Edge jest
    * Pakiety instalacyjne dla innych platform może działać na tych platformach
    
Rodzina systemu operacyjnego hosta musi zawsze odpowiadać rodziny używane w kontenerze modułu systemu operacyjnego gościa. Innymi słowy tylko umożliwia kontenerów systemu Linux w systemie Linux i kontenerów Windows na Windows. Korzystając z Windows, proces izolowany kontenerów są obsługiwane, nie funkcji hyper-v kontenerach izolowanych.  

<br>
<center>
![Pasuje do systemu operacyjnego hosta systemu operacyjnego gościa](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Warstwa 1
Ogólnie dostępna
| System operacyjny | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian stretch | Nie | Yes|
| Ubuntu Server 16.04 | Yes | Nie |
| Serwer Ubuntu 18.04 | Yes | Nie |

Publiczna wersja zapoznawcza
| System operacyjny | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core build 17763 | Yes | Nie |
| Windows 10 IoT Enterprise build 17763 | Yes | Nie |
| Windows Server 2019 | Yes | Nie |

Systemy operacyjne Windows powyżej przedstawiono wymagania dotyczące urządzeń, których uruchamianie kontenerów Windows na Windows. Jest to jedyna obsługiwana konfiguracja dla środowiska produkcyjnego. Pakiety instalacyjne usługi Azure IoT Edge dla Windows umożliwia korzystanie z kontenerów systemu Linux na Windows; Jednak to tworzenia i testowania tylko. Korzystanie z kontenerów systemu Linux na Windows nie jest obsługiwaną konfiguracją w środowisku produkcyjnym. Dowolna wersja systemu Windows 10 kompilacji 14393 lub nowszym i Windows Server 2016 lub nowszym może służyć w tym scenariuszu rozwoju.

### <a name="tier-2"></a>Warstwa 2

| System operacyjny | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS w wersji 7.5 | Yes | Yes |
| Debian 8 | Yes | Yes |
| Debian 9 | Yes | Yes |
| RHEL W WERSJI 7.5 | Yes | Yes |
| Ubuntu 18.04 | Yes | Yes |
| Ubuntu 16.04 | Yes | Yes |
| Rzeka wiatru 8 | Yes | Nie |
| Yocto | Yes | Nie |


## <a name="virtual-machines"></a>Maszyny wirtualne
Usługa Azure IoT Edge można uruchomić na maszynach wirtualnych. Jest to typowe w przypadku, gdy klienci chcą rozszerzyć istniejącą infrastrukturę dzięki inteligencji krawędziowej. Rodzina systemu operacyjnego maszyny Wirtualnej hosta muszą być zgodne rodziny używane w kontenerze modułu systemu operacyjnego gościa. Jest to ten sam wymóg, gdy usługi Azure IoT Edge jest uruchamiane bezpośrednio na urządzeniu. Usługa Azure IoT Edge jest niezależny od podstawowych technologii wirtualizacji i działa na maszynach wirtualnych obsługiwane przez platform, takich jak funkcji Hyper-V i vSphere.

<br>
<center>
![Usługa Azure IoT Edge na maszynie wirtualnej](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimalne wymagania systemowe
Usługa Azure IoT Edge działa doskonale na urządzeniach tak małej, jak Raspberry Pi3 serwer klasy korporacyjnej sprzęt. Wybieranie odpowiedniego sprzętu dla danego scenariusza jest bardzo zależy od obciążenia, które chcesz uruchomić. Podejmowania decyzji końcowy urządzenia może być skomplikowane; jednak łatwo zacząć tworzenie prototypów rozwiązania na tradycyjnych komputery przenośne lub komputery stacjonarne.

Środowisko podczas tworzenia prototypów pomoże przewodnik wyboru końcowego urządzenia. Pytania, które należy wziąć pod uwagę: jak wiele modułów składają się obciążenie, ile czy warstwy udział kontenery współpracę modułów, w jakim języku są moduły zapisanych, jak dużo danych będzie moduły przetwarzać, wykonaj wszelkie specjalne potrzeby modułów sprzęt przyspieszenia ich obciążeń, co to są żądane właściwości działania rozwiązania, co to jest budżetu sprzętu?
