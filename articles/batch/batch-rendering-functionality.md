---
title: Renderowanie możliwości — w usłudze Azure Batch
description: Możliwości renderowania określonych w usłudze Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: be6c0f9a8874507433606903bcbd58c7723d6a8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118691"
---
# <a name="azure-batch-rendering-capabilities"></a>Możliwości renderowania w usłudze Azure Batch

Funkcje wersji standardowej usługi Azure Batch są używane do uruchamiania obciążenia renderowania i aplikacji. Usługa Batch obejmuje również określone funkcje do obsługi obciążenia renderowania.

Aby uzyskać omówienie pojęcia usługi Batch, łącznie z pul, zadań i zadań, zobacz [w tym artykule](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Pule usługi Batch

### <a name="rendering-application-installation"></a>Renderowanie instalacji aplikacji

Renderowanie obrazu maszyny Wirtualnej portalu Azure Marketplace można określić w konfiguracji puli, jeśli tylko wstępnie zainstalowanych aplikacji trzeba było używać.

Brak obrazu Windows 2016 i obrazu systemu CentOS.  W [portalu Azure Marketplace](https://azuremarketplace.microsoft.com), obrazy maszyn wirtualnych można znaleźć, wyszukując pozycję "usługi batch rendering".

Aby uzyskać przykładową konfigurację puli, zobacz [renderowania wiersza polecenia platformy Azure w ramach samouczka](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Witryna Azure portal i programu Batch Explorer zapewnia narzędzia graficznego interfejsu użytkownika do renderowania obrazu maszyny Wirtualnej należy wybrać podczas tworzenia puli.  Jeśli za pomocą interfejsu API usługi Batch, a następnie określ następujące wartości właściwości dla [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) podczas tworzenia puli:

| Wydawca | Oferta | SKU | Version |
|---------|---------|---------|--------|
| partia | rendering-centos73 | Renderowanie | najnowsza |
| partia | rendering-windows2016 | Renderowanie | najnowsza |

Inne opcje są dostępne, jeśli są wymagane dodatkowe aplikacje, w puli maszyn wirtualnych:

* Obraz niestandardowy na podstawie standardowego obrazu portalu Marketplace:
  * Przy użyciu tej opcji możesz skonfigurować swoją maszynę wirtualną z odpowiednimi aplikacjami w określonych wersjach, których potrzebujesz. Aby uzyskać więcej informacji, zobacz [użyć obrazu niestandardowego do tworzenia puli maszyn wirtualnych](https://docs.microsoft.com/azure/batch/batch-custom-images). Autodesk i Chaos Group zmodyfikowano Arnold i V-Ray, odpowiednio, na potrzeby sprawdzania poprawności usługi licencjonowania usługi Azure Batch. Upewnij się, że masz wersje tych aplikacji, dzięki temu, w przeciwnym razie Licencjonowanie płatność za użycie nie będą działać. Bieżące wersje aplikacji Maya lub 3ds Max nie wymagają serwera licencji podczas uruchamiania bezobsługowego (w trybie wsadowym/wiersza polecenia). Jeśli nie masz pewności, jak korzystać z tej opcji, skontaktuj się z działem pomocy technicznej platformy Azure.
* [Pakiety aplikacji](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Pliki aplikacji przy użyciu co najmniej jednego pliku ZIP pakietu, Przekaż za pośrednictwem witryny Azure portal i określ pakiet, w konfiguracji puli. Podczas tworzenia puli maszyn wirtualnych są pobierane pliki ZIP i wyodrębnić pliki.
* Pliki zasobów:
  * Pliki aplikacji są przekazywane do usługi Azure blob storage i określ odwołań do pliku w [zadanie podrzędne uruchamiania puli](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Podczas tworzenia puli maszyn wirtualnych, pliki zasobów zostaną pobrane na każdej maszynie Wirtualnej.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Płatność dla użycie licencji na program preinstalowanymi aplikacji

Aplikacje, które będą używane i mieć opłacie licencyjnej muszą być określone w konfiguracji puli.

* Określ `applicationLicenses` właściwości podczas [tworzenia puli](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Można określić następujące wartości w tablicy ciągów — "vray", "arnold", "3dsmax", "maya".
* Po określeniu jednej lub więcej aplikacji, koszt tych aplikacji jest dodawany do kosztu maszyn wirtualnych.  Ceny aplikacji są wyświetlane na [usługi Azure Batch stronę z cennikiem](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Jeśli zamiast tego możesz połączyć się z serwera licencji na korzystanie z aplikacji renderowanie, nie należy określać `applicationLicenses` właściwości.

Aby wybrać aplikacje i wyświetlić ceny aplikacji, można użyć witryny Azure portal lub programu Batch Explorer.

Jeśli zostanie podjęta próba na korzystanie z aplikacji, ale aplikacja nie została określona w `applicationLicenses` właściwość konfiguracji puli lub czy nie zasięg serwera licencji, a następnie wykonywania aplikacji nie powiedzie się z licencjonowania błędów oraz kod zakończenia różny od zera.

### <a name="environment-variables-for-pre-installed-applications"></a>Zmienne środowiskowe dla optymalizacji wstępnie zainstalowanych aplikacji

Aby można było utworzyć wiersz polecenia dla zadań podrzędnych renderowania, należy określić lokalizację instalacji plików wykonywalnych aplikacji renderowania.  Systemowe zmienne środowiskowe zostały utworzone na obrazy maszyny Wirtualnej portalu Azure Marketplace, które mogą być używane zamiast konieczności określania rzeczywiste ścieżki.  Te zmienne środowiskowe stanowią uzupełnienie [standardowa zmiennych środowiskowych usługi Batch](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) tworzony dla każdego zadania.

|Aplikacja|Plik wykonywalny aplikacji|Zmienna środowiskowa|
|---------|---------|---------|
|Autodesk 3ds Max 2018 r.|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray autonomiczny|vray.exe|VRAY_3.60.4_EXEC|
Wiersz polecenia programu Arnold 2017 r.|kick.exe|ARNOLD_2017_EXEC|
|Wiersz polecenia programu Arnold 2018 r.|kick.exe|ARNOLD_2018_EXEC|
|Mikser|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Rodzin maszyn wirtualnych platformy Azure

Podobnie jak w przypadku innych obciążeń, wymagania systemowe aplikacji renderowania różnią się i wymagań dotyczących wydajności zależy dla projektów i zadań.  Wielu różnych rodzin maszyn wirtualnych są dostępne w Azure, w zależności od wymagań — najniższy koszt, najlepsze ceny do wydajności, najlepsze wydajności i tak dalej.
Niektóre aplikacje renderowania, takie jak Arnold, są oparte na procesor CPU; inne, takie jak V-Ray i cykle Blender można używać procesorów CPU lub GPU.
Aby uzyskać opis dostępnych rodzin maszyn wirtualnych i rozmiarach maszyn wirtualnych [Zobacz typy maszyn wirtualnych i rozmiarach](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Maszyny wirtualne o niskim priorytecie

Podobnie jak w przypadku innych obciążeń maszyn wirtualnych o niskim priorytecie może być wykorzystywany w pulach usługi Batch do renderowania.  Maszyny wirtualne o niskim priorytecie wykonać takie same jak regularne dedykowanych maszyn wirtualnych, ale korzystanie z nadmiarowej wydajności platformy Azure i są dostępne dla dużą zniżką.  Kompromisem za ułatwienie przy użyciu maszyn wirtualnych o niskim priorytecie jest, że te maszyny wirtualne mogą być niedostępne do alokacji lub mogą być przerywane, w dowolnym momencie, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie nie mają być odpowiednie dla wszystkich zadań renderowania. Na przykład jeśli obrazy zająć wiele godzin do renderowania, a następnie jest prawdopodobne, posiadające renderowania tych obrazów, przerwane i ponownie uruchomione z powodu maszyny wirtualne są przerywane byłoby dopuszczalne.

Aby uzyskać więcej informacji na temat maszyn wirtualnych o niskim priorytecie i różne sposoby, aby je skonfigurować za pomocą usługi Batch, zobacz [używanie maszyn wirtualnych o niskim priorytecie z usługą Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Zadania i podzadania

Brak obsługi specyficznych dla renderowania jest wymagana dla zadań i zadań podrzędnych.  Element konfiguracji głównego jest wiersz polecenia zadania, który musi odwoływać się do wymaganej aplikacji.
Gdy używane są obrazy maszyny Wirtualnej portalu Azure Marketplace, najlepszym rozwiązaniem jest używać zmiennych środowiskowych, aby określić ścieżkę i plik wykonywalny aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Przykłady usługi Batch renderowania Wypróbuj samouczki dwa:

* [Renderowanie przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderowanie przy użyciu programu Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
