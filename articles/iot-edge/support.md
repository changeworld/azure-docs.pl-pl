---
title: Obsługiwane systemy operacyjne, aparaty kontenerów — Usługa Azure IoT Edge
description: Dowiedz się, które systemy operacyjne mogą uruchamiać demon i środowisko wykonawcze usługi Azure IoT Edge oraz obsługiwane aparaty kontenerów dla urządzeń produkcyjnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536942"
---
# <a name="azure-iot-edge-supported-systems"></a>Obsługiwane systemy usługi Azure IoT Edge

Ten artykuł zawiera szczegółowe informacje o tym, które systemy i składniki są obsługiwane przez usługi IoT Edge, oficjalnie lub w wersji zapoznawczej.

Jeśli wystąpią problemy podczas korzystania z usługi Azure IoT Edge, istnieje kilka sposobów, aby uzyskać pomoc techniczną. Wypróbuj jeden z następujących kanałów pomocy technicznej:

**Zgłaszanie błędów** — większość rozwoju, który przechodzi do produktu usługi Azure IoT Edge dzieje się w projekcie open source usługi IoT Edge. Błędy można zgłaszać na [stronie problemów](https://github.com/azure/iotedge/issues) projektu. Poprawki szybko docierają z projektu do aktualizacji produktów.

**Zespół obsługi klienta firmy Microsoft** — użytkownicy, którzy mają plan pomocy [technicznej,](https://azure.microsoft.com/support/plans/) mogą zaangażować zespół działu obsługi klienta firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z [witryny Azure portal.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)

**Żądania funkcji** — produkt usługi Azure IoT Edge śledzi żądania funkcji za pośrednictwem [strony Głos użytkownika](https://feedback.azure.com/forums/907045-azure-iot-edge)produktu.

## <a name="container-engines"></a>Silniki kontenerowe

Moduły usługi Azure IoT Edge są implementowane jako kontenery, więc usługa IoT Edge potrzebuje aparatu kontenerów, aby je uruchomić. Firma Microsoft udostępnia aparat kontenerów, moby-engine, aby spełnić to wymaganie. Ten aparat kontenera jest oparty na projekcie open-source Moby. Docker CE i Docker EE to inne popularne silniki kontenerowe. Są one również oparte na projekcie open source Moby i są zgodne z usługą Azure IoT Edge. Firma Microsoft zapewnia najlepszą obsługę techniczną dla systemów korzystających z tych aparatów kontenerów; Jednak Microsoft nie może wysyłać rozwiązań problemów w nich. Z tego powodu firma Microsoft zaleca używanie aparatu moby w systemach produkcyjnych.

<br>
<center>

![Silnik Moby jako środowisko uruchomieniowe kontenera](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Systemy operacyjne

Usługa Azure IoT Edge działa w większości systemów operacyjnych, które mogą uruchamiać kontenery; jednak nie wszystkie te systemy są jednakowo obsługiwane. Systemy operacyjne są pogrupowane w warstwy, które reprezentują poziom pomocy technicznej, jakiego użytkownicy mogą oczekiwać.

* Obsługiwane są systemy Tier 1. W przypadku systemów warstwy 1 firma Microsoft:
  * ma ten system operacyjny w zautomatyzowanych testach
  * zapewnia dla nich pakiety instalacyjne
* Systemy warstwy 2 są zgodne z usługą Azure IoT Edge i mogą być używane stosunkowo łatwo. W przypadku systemów poziomu 2:
  * Firma Microsoft przeprowadziła nieformalne testy na platformach lub wie o partnerze, który pomyślnie uruchamia usługę Azure IoT Edge na platformie
  * Pakiety instalacyjne dla innych platform mogą działać na tych platformach

Rodzina systemu operacyjnego hosta musi zawsze odpowiadać rodzinie systemu operacyjnego gościa używanego w kontenerze modułu. Innymi słowy, można używać kontenerów systemu Linux tylko w kontenerach Systemu Linux i Windows w systemie Windows. Podczas korzystania z systemu Windows obsługiwane są tylko kontenery izolowane proces, a nie kontenery izolowane funkcji Hyper-V.  

<br>
<center>

![Host OS dopasowuje system operacyjny gościa](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Warstwa 1

Systemy wymienione w poniższej tabeli są obsługiwane przez firmę Microsoft, ogólnie dostępne lub w publicznej wersji zapoznawczej i są testowane przy każdej nowej wersji. 

| System operacyjny | Amd64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Serwer Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Serwer Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), budować 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), kompilacja 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), kompilacja 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), kompilacja 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Wymienione powyżej systemy operacyjne Windows są wymaganiami dla urządzeń z kontenerami systemu Windows w systemie Windows, który jest jedyną obsługiwaną konfiguracją w wersji produkcyjnej. Pakiety instalacyjne usługi Azure IoT Edge dla systemu Windows umożliwiają korzystanie z kontenerów systemu Linux w systemie Windows; jednak ta konfiguracja jest tylko do tworzenia i testowania. Aby uzyskać więcej informacji, zobacz [Uruchamianie kontenerów systemu Linux za pomocą usługi IoT Edge w systemie Windows](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Warstwa 2

Systemy wymienione w poniższej tabeli są uważane za zgodne z usługą Azure IoT Edge, ale nie są aktywnie testowane ani utrzymywane przez firmę Microsoft.

| System operacyjny | Amd64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Wbudowany Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Wbudowany Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Wbudowany System Operacyjny Linux Flex + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Wbudowany Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Wbudowany Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Wbudowany Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Wbudowany Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + RAMIĘ64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rzeka Wiatrowa 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Pogromca Raspbian <sup>1</sup> |  | ![Pogromca raspbian + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Pogromca raspbian + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Systemy Debiana 10, w tym Raspian Buster, używają wersji OpenSSL, której IoT Edge nie obsługuje. Użyj następującego polecenia, aby zainstalować wcześniejszą wersję przed zainstalowaniem usługi IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Wydania

Zasoby wydania usługi IoT Edge i informacje o wersji są dostępne na stronie [wydań azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Ta sekcja odzwierciedla informacje z tych informacji o wersji, aby ułatwić wizualizację składników każdej wersji.

Składniki IoT Edge można instalować lub aktualizować indywidualnie i są wstecznie kompatybilne ze składnikami ze starszych wersji. W poniższej tabeli wymieniono składniki zawarte w każdej wersji:

| Release   | Demon bezpieczeństwa  | Koncentrator krawędzi<br>Agent krawędzi | Libiothsm | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

Usługa IoT Edge używa sdk Microsoft.Azure.Device.Client. Aby uzyskać więcej informacji, zobacz [repozytorium GitHub usługi Azure IoT C#](https://github.com/Azure/azure-iot-sdk-csharp) lub [zestaw SDK platformy Azure dla zawartości referencyjnej .NET.](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) Na poniższej liście przedstawiono wersję sdk klienta, dla których każda wersja jest testowana:

* **IoT Edge 1.0.9**: SDK klienta 1.21.1
* **IoT Edge 1.0.8**: SDK klienta 1.20.3
* **IoT Edge 1.0.7**: SDK klienta 1.20.1
* **IoT Edge 1.0.6**: SDK klienta 1.17.1
* **IoT Edge 1.0.5**: SDK klienta 1.17.1

## <a name="virtual-machines"></a>Maszyny wirtualne

Usługa Azure IoT Edge można uruchomić na maszynach wirtualnych. Używanie maszyny wirtualnej jako urządzenia usługi IoT Edge jest powszechne, gdy klienci chcą rozszerzyć istniejącą infrastrukturę za pomocą analizy brzegowej. Rodzina hosta systemu operacyjnego maszyny Wirtualnej musi być zgodna z rodziną systemu operacyjnego gościa używanego w kontenerze modułu. To wymaganie jest taka sama, jak wtedy, gdy usługa Azure IoT Edge jest uruchamiana bezpośrednio na urządzeniu. Usługa Azure IoT Edge jest niezależną od podstawowej technologii wirtualizacji i działa na maszynach wirtualnych opartych na platformach takich jak Hyper-V i vSphere.

<br>
<center>

![Usługa Azure IoT Edge na maszynie wirtualnej](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimalne wymagania systemowe

Usługa Azure IoT Edge działa świetnie na urządzeniach tak małych, jak Raspberry Pi3 do sprzętu klasy serwerowej. Wybór odpowiedniego sprzętu dla scenariusza zależy od obciążeń, które chcesz uruchomić. Podjęcie ostatecznej decyzji o urządzeniu może być skomplikowane; Można jednak łatwo rozpocząć prototypowanie rozwiązania na tradycyjnych laptopach lub komputerach stacjonarnych.

Doświadczenie podczas tworzenia prototypów pomoże w wyborze ostatecznego urządzenia. Pytania, które należy wziąć pod uwagę, obejmują:

* Ile modułów jest w obciążeniu?
* Ile warstw mają udział kontenery modułów?
* W jakim języku są napisane moduły?
* Ile danych będą przetwarzać moduły?
* Czy twoje moduły potrzebują specjalistycznego sprzętu do przyspieszania obciążeń?
* Jakie są pożądane właściwości użytkowe rozwiązania?
* Jaki jest twój budżet sprzętowy?
