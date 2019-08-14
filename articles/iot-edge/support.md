---
title: Obsługiwane systemy operacyjne, aparaty kontenera — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, które systemy operacyjne można uruchomić demona usługi Azure IoT Edge i środowisko uruchomieniowe i aparatów obsługiwane kontenerów urządzeń z systemem produkcyjnym
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: fdecc34b49b853c34624cabf4688e0387c303a16
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985813"
---
# <a name="azure-iot-edge-supported-systems"></a>Usługa Azure IoT Edge obsługiwanych systemów

Ten artykuł zawiera szczegółowe informacje o systemach i składnikach obsługiwanych przez IoT Edge, niezależnie od tego, czy są one oficjalne czy w wersji zapoznawczej. 

Jeśli wystąpią problemy podczas korzystania z usługi Azure IoT Edge, istnieje kilka sposobów poszukiwania pomocy technicznej. Wypróbuj jeden z następujących kanałów, aby uzyskać pomoc techniczną:

**Raportowania błędów** — większość programowania, który przechodzi do produktów Azure IoT Edge odbywa się w projekt open source usługi IoT Edge. Błędy mogą być zgłaszane na [strony problemy](https://github.com/azure/iotedge/issues) projektu. Poprawki należy szybko z projektu w drodze do aktualizacji produktów.

**Zespół obsługi klienta firmy Microsoft** — użytkowników, którzy mają [plan pomocy technicznej](https://azure.microsoft.com/support/plans/) zaangażować zespół obsługi klienta firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z [witryny Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Żądania funkcji** — produktu usługi Azure IoT Edge śledzi żądania funkcji za pomocą produktu [stronę z opiniami użytkowników](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Aparaty kontenera

Moduły Azure IoT Edge są implementowane jako kontenery, dlatego IoT Edge potrzebuje aparatu kontenera, aby je uruchomić. Firma Microsoft udostępnia z kontenera aparatu moby aparatu, aby spełnić to wymaganie. Ten aparat kontenera jest oparty na projekcie Moby Open Source. Docker CE i Docker EE są innymi aparatami popularnych kontenera. Są one również oparte na projekcie Moby Open Source i są zgodne z Azure IoT Edge. Firma Microsoft zapewnia najlepszą pomoc techniczną dla systemów korzystających z tych aparatów kontenerów; Jednak firma Microsoft nie może wydać w nich poprawek. Z tego powodu firma Microsoft zaleca używanie aparatu moby w systemach produkcyjnych.

<br>
<center>

![Moby jako środowisko uruchomieniowe kontenera](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Systemy operacyjne
Azure IoT Edge działa w większości systemów operacyjnych, które mogą uruchamiać kontenery; Jednak wszystkie te systemy nie są jednakowo obsługiwane. Systemy operacyjne są podzielone na warstwy, które reprezentują poziom pomocy technicznej, które użytkownicy mogą oczekiwać.
* Obsługiwane są systemy warstwy 1. W przypadku systemów warstwy 1 Firma Microsoft:
    * Czy ten system operacyjny jest w zautomatyzowanych testach
    * udostępnia pakiety instalacyjne dla nich
* Systemy warstwy 2 są zgodne z Azure IoT Edge i mogą być używane stosunkowo łatwo. W przypadku systemów warstwy 2:
    * Firma Microsoft zakończyła testowanie ad hoc na platformach lub wie, że partner pomyślnie uruchomił Azure IoT Edge na platformie
    * Pakiety instalacyjne dla innych platform może działać na tych platformach
    
Rodzina systemu operacyjnego hosta musi być zawsze zgodna z rodziną systemu operacyjnego gościa używanego wewnątrz kontenera modułu. Innymi słowy, można używać tylko kontenerów systemu Linux w kontenerach systemów Linux i Windows w systemie Windows. W przypadku korzystania z systemu Windows obsługiwane są tylko przetwórz izolowane kontenery, a nie kontenery izolowane funkcji Hyper-V.  

<br>
<center>

![System operacyjny hosta jest zgodny z systemem operacyjnym gościa](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Warstwa 1

Systemy wymienione w poniższej tabeli są obsługiwane przez firmę Microsoft, ogólnie dostępne lub w publicznej wersji zapoznawczej, i są testowane z każdą nową wersją. 

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian stretch |  | ![Raspbian Rozciągaj i ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza  |
| Serwer Ubuntu 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza |
| Windows 10 IoT Enterprise, kompilacja 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019, build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019, kompilacja 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core, kompilacja 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


Wymienione powyżej systemy operacyjne Windows są wymaganiami dotyczącymi urządzeń z kontenerami systemu Windows w systemie Windows. Ta konfiguracja jest jedyną obsługiwaną konfiguracją dla środowiska produkcyjnego. Pakiety instalacyjne Azure IoT Edge dla systemu Windows umożliwiają używanie kontenerów z systemem Linux w systemie Windows; Jednak ta konfiguracja służy tylko do tworzenia i testowania. Korzystanie z kontenerów systemu Linux w systemie Windows nie jest obsługiwaną konfiguracją dla środowiska produkcyjnego. W tym scenariuszu deweloperskim można użyć dowolnej wersji systemu Windows 10 Build 14393 lub nowszej oraz systemu Windows Server 2016 lub nowszego.

### <a name="tier-2"></a>Warstwa 2

Systemy wymienione w poniższej tabeli są uważane za zgodne z Azure IoT Edge, ale nie są aktywnie przetestowane ani konserwowane. 

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| CentOS w wersji 7.5 | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 8 | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 9 | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 10<sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| RHEL W WERSJI 7.5 | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 16.04 | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 18.04 | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Rzeka wiatru 8 | ![Rzeka wiatr 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Yocto | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster<sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 systemów, w tym Raspian Buster, użyj wersji OpenSSL, która nie jest obsługiwana przez IoT Edge. Przed zainstalowaniem IoT Edge Użyj następującego polecenia: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Maszyny wirtualne
Azure IoT Edge można uruchamiać na maszynach wirtualnych. Użycie maszyny wirtualnej jako urządzenia IoT Edge jest powszechne, gdy klienci chcą rozszerzyć istniejącą infrastrukturę za pomocą funkcji analizy brzegowej. Rodzina systemu operacyjnego hosta maszyny wirtualnej musi być zgodna z rodziną systemu operacyjnego gościa używanego wewnątrz kontenera modułu. To wymaganie jest takie samo, jak w przypadku uruchamiania Azure IoT Edge bezpośrednio na urządzeniu. Azure IoT Edge jest niezależny od podstawowej technologii wirtualizacji i działa na maszynach wirtualnych opartych na platformach takich jak Hyper-V i vSphere.

<br>
<center>

![Azure IoT Edge w maszynie wirtualnej](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimalne wymagania systemowe
Azure IoT Edge działa doskonale na urządzeniach jako Raspberry PI3 na sprzęcie klasy serwerów. Wybór odpowiedniego sprzętu dla danego scenariusza zależy od obciążeń, które chcesz uruchomić. Podejmowanie ostatecznej decyzji o urządzeniu może być skomplikowane; można jednak łatwo zacząć tworzyć prototypy rozwiązania na tradycyjnych laptopach lub komputerach stacjonarnych.

Środowisko pracy podczas tworzenia prototypów ułatwi wybór ostatecznego urządzenia. Należy wziąć pod uwagę następujące pytania: 

* Ile modułów znajduje się w obciążeniu?
* Ile warstw ma udział w kontenerach modułów?
* W jakim języku są zapisywane Twoje moduły? 
* Ile danych będzie przetwarzanych przez moduły?
* Czy moduły wymagają dowolnego wyspecjalizowanego sprzętu w celu przyspieszenia obciążeń?
* Jakie są odpowiednie charakterystyki wydajności rozwiązania?
* Jaki jest Twój budżet sprzętu?
