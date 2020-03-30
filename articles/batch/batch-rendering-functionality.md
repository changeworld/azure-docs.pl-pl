---
title: Możliwości renderowania — usługa Azure Batch
description: Standardowe funkcje usługi Azure Batch są używane do uruchamiania obciążeń renderowania i aplikacji. Usługa Batch zawiera określone funkcje do obsługi obciążeń renderowania.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 697e2640b7215e0bbb9202c672f936535831eb99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449724"
---
# <a name="azure-batch-rendering-capabilities"></a>Możliwości renderowania usługi Azure Batch

Standardowe funkcje usługi Azure Batch są używane do uruchamiania obciążeń renderowania i aplikacji. Usługa Batch zawiera również określone funkcje do obsługi obciążeń renderowania.

Aby zapoznać się z omówieniem pojęć usługi Batch, w tym pule, zadania i zadania, zobacz [ten artykuł](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Pule wsadowe

### <a name="rendering-application-installation"></a>Instalacja aplikacji renderowania

Obraz maszyny Wirtualnej renderowania w portalu Azure Marketplace można określić w konfiguracji puli, jeśli muszą być używane tylko wstępnie zainstalowane aplikacje.

Jest obraz systemu Windows 2016 i obraz CentOS.  W [portalu Azure Marketplace](https://azuremarketplace.microsoft.com)obrazy maszyn wirtualnych można znaleźć, wyszukując "renderowanie wsadowe".

Aby zapoznać się z przykładową konfiguracją puli, zobacz [samouczek renderowania interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Portal Azure i Eksplorator partii zapewniają narzędzia graficzne do wybierania obrazu maszyny wirtualnej renderowania podczas tworzenia puli.  Jeśli używasz interfejsu API usługi Batch, należy określić następujące wartości właściwości [dla ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) podczas tworzenia puli:

| Wydawca | Oferta | SKU | Wersja |
|---------|---------|---------|--------|
| partia | renderowanie-centos73 | Renderowania | najnowsza |
| partia | rendering-windows2016 | Renderowania | najnowsza |

Inne opcje są dostępne, jeśli dodatkowe aplikacje są wymagane na maszynach wirtualnych puli:

* Obraz niestandardowy z Galerii obrazów udostępnionych:
  * Przy użyciu tej opcji możesz skonfigurować swoją maszynę wirtualną z odpowiednimi aplikacjami w określonych wersjach, których potrzebujesz. Aby uzyskać więcej informacji, zobacz [Tworzenie puli za pomocą Galerii obrazów udostępnionych](batch-sig-images.md). Autodesk i Chaos Group zmodyfikowały odpowiednio Arnold i V-Ray, aby sprawdzić poprawność w ramach usługi licencjonowania usługi Azure Batch. Upewnij się, że masz wersje tych aplikacji z tej pomocy technicznej, w przeciwnym razie licencjonowanie pay-per-use nie będzie działać. Bieżące wersje Programu Maya lub 3ds Max nie wymagają serwera licencji przy uruchamianiu bezgłowy (w trybie wsadowym/wiersza polecenia). Jeśli nie wiesz, jak kontynuować tę opcję, skontaktuj się z pomocą techniczną platformy Azure.
* [Pakiety aplikacyjne](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Pakuj pliki aplikacji przy użyciu jednego lub więcej plików ZIP, przekaż za pośrednictwem witryny Azure portal i określ pakiet w konfiguracji puli. Podczas tworzenia maszyn wirtualnych puli pliki ZIP są pobierane, a pliki wyodrębniane.
* Pliki zasobów:
  * Pliki aplikacji są przekazywane do magazynu obiektów blob platformy Azure i określić odwołania do plików w [zadaniu uruchamiania puli](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Podczas tworzenia maszyn wirtualnych puli pliki zasobów są pobierane na każdą maszynę wirtualną.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licencjonowanie płatności za użytkowanie dla wstępnie zainstalowanych aplikacji

Aplikacje, które będą używane i mają opłatę licencyjną, muszą być określone w konfiguracji puli.

* Określ `applicationLicenses` właściwość podczas [tworzenia puli](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Następujące wartości można określić w tablicy ciągów - "vray", "arnold", "3dsmax", "maya".
* Po określeniu co najmniej jednej aplikacji koszt tych aplikacji jest dodawany do kosztu maszyn wirtualnych.  Ceny aplikacji są podane na [stronie cennik azure batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Jeśli zamiast tego łączysz się z serwerem licencji, aby `applicationLicenses` korzystać z aplikacji renderowania, nie należy określać właściwości.

Za pomocą portalu Azure lub Eksploratora wsadowego można wybrać aplikacje i wyświetlić ceny aplikacji.

Jeśli zostanie podjęta próba użycia aplikacji, ale aplikacja nie `applicationLicenses` została określona we właściwości konfiguracji puli lub nie dociera do serwera licencji, wykonanie aplikacji kończy się niepowodzeniem z błędem licencjonowania i kodem zakończenia niezerowego.

### <a name="environment-variables-for-pre-installed-applications"></a>Zmienne środowiskowe dla wstępnie zainstalowanych aplikacji

Aby można było utworzyć wiersz polecenia do renderowania zadań, należy określić lokalizację instalacji plików wykonywalnych aplikacji renderowania.  Zmienne środowiska systemowego zostały utworzone na obrazach maszyn wirtualnych usługi Azure Marketplace, które mogą być używane zamiast określania rzeczywistych ścieżek.  Te zmienne środowiskowe są dodatkiem do [standardowych zmiennych środowiskowych partii utworzonych](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) dla każdego zadania.

|Aplikacja|Plik wykonywalny aplikacji|Zmienna środowiskowa|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|plik render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|plik render.exe|MAYA_2018_EXEC|
|Grupa Chaos V-Ray Standalone|plik vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 wiersz polecenia|plik kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 wiersz polecenia|plik kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Rodziny maszyn wirtualnych platformy Azure

Podobnie jak w przypadku innych obciążeń, wymagania systemowe aplikacji renderowania różnią się, a wymagania dotyczące wydajności różnią się dla zadań i projektów.  Wiele różnych rodzin maszyn wirtualnych są dostępne na platformie Azure w zależności od wymagań — najniższy koszt, najlepsza cena/wydajność, najlepsza wydajność i tak dalej.
Niektóre aplikacje renderowania, takie jak Arnold, są oparte na procesorze; inne, takie jak V-Ray i Blender Cycles mogą używać procesorów i/lub procesorów graficznych.
Aby zapoznać się z opisem dostępnych rodzin maszyn wirtualnych i rozmiarów maszyn wirtualnych, [zobacz typy i rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Maszyny wirtualne o niskim priorytecie

Podobnie jak w przypadku innych obciążeń, maszyny wirtualne o niskim priorytecie mogą być używane w pulach wsadowych do renderowania.  Maszyny wirtualne o niskim priorytecie działają tak samo jak zwykłe dedykowane maszyny wirtualne, ale wykorzystują nadwyżkę pojemności platformy Azure i są dostępne dla dużego rabatu.  Kompromisem przy użyciu maszyn wirtualnych o niskim priorytecie jest to, że te maszyny wirtualne mogą nie być dostępne do przydzielenia lub mogą być wywłaszczone w dowolnym momencie, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie nie będą odpowiednie dla wszystkich zadań renderowania. Na przykład jeśli renderowanie obrazów trwa wiele godzin, prawdopodobnie przerwanie renderowania tych obrazów i ponowne uruchomienie ich z powodu wywłaszczenia maszyn wirtualnych nie byłoby dopuszczalne.

Aby uzyskać więcej informacji na temat cech maszyn wirtualnych o niskim priorytecie i różnych sposobów konfigurowania ich przy użyciu usługi Batch, zobacz [Używanie maszyn wirtualnych o niskim priorytecie z programem Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Zadania i zadania

Dla zadań i zadań nie jest wymagana żadna obsługa renderowania.  Głównym elementem konfiguracji jest wiersz polecenia zadania, który musi odwoływać się do wymaganej aplikacji.
Gdy są używane obrazy maszyn wirtualnych w portalu Azure Marketplace, najlepszym rozwiązaniem jest użycie zmiennych środowiskowych do określenia ścieżki i pliku wykonywalnego aplikacji.

## <a name="next-steps"></a>Następne kroki

Na przykład renderowania batch wypróbować dwa samouczki:

* [Renderowanie przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderowanie przy użyciu programu Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
