---
title: Renderowanie Przegląd - usługi Azure Batch
description: Wprowadzenie do renderowania i przeglądu możliwości renderowania usługi Azure Batch przy użyciu platformy Azure
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774033"
---
# <a name="rendering-using-azure"></a>Renderowanie przy użyciu platformy Azure

Renderowanie to proces pobierania modele 3D i konwertowania ich na obrazów 2D. Sceny 3D, pliki są tworzone w aplikacji, takich jak Autodesk 3ds Max, programy Autodesk Maya i Blender.  Renderowanie aplikacji, takich jak Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray i Blender cykle tworzenia obrazów 2D.  Czasami pojedynczego obrazy są tworzone na podstawie pliki sceny. Jednak jest wspólne dla modelu i przetworzyć wiele obrazów, a następnie łączyć je w animacji.

Obciążenia renderowania jest intensywnie używana do efekty specjalne (filmowych efektów specjalnych) w branży multimedia i rozrywka. Renderowanie jest również używane w wielu branżach, takich jak reklam, handlu detalicznego, ropa naftowa i gaz i produkcji.

Proces renderowania jest wymaga dużej mocy obliczeniowej; może istnieć wiele ramek/obrazów do produkcji i każdego obrazu może zająć wiele godzin do renderowania.  Renderowanie w związku z tym jest obciążenie przetwarzania doskonałe przetwarzania wsadowego, które mogą korzystać z platformy Azure i usługi Azure Batch do równoległego uruchamiania wielu renderów.

## <a name="why-use-azure-for-rendering"></a>Dlaczego warto używać platformy Azure w celu renderowania?

Z wielu powodów renderowanie jest obciążenie doskonale nadaje się do platformy Azure i usługi Azure Batch:

* Renderowania zadania może zostać podzielony na wiele elementów, które mogą być uruchamiane równolegle za pomocą wielu maszyn wirtualnych:
  * Animacje składają się z wieloma ramkami i każdej ramki mogą być renderowane w sposób równoległy.  Więcej maszyny wirtualne dostępne dla procesu, który można tworzyć każdej klatce, tym szybciej wszystkich ramek i animacji.
  * Niektóre programy renderowania umożliwia jednej klatki, aby być dzielone na wiele części, takie jak kafelki lub wycinki.  Każdy element może można renderowane osobno, a następnie łączone w obrazie końcowym, po zakończeniu wszystkich elementów.  Więcej maszyn wirtualnych, które są dostępne, tym szybciej ramki mogą być renderowane.
* Renderowanie projekty mogą wymagać ogromnej skali:
  * Poszczególnych klatek mogą być skomplikowane i wymagają wiele godzin do renderowania, nawet w przypadku sprzętu wysokiej klasy; animacje może składać się z setkami tysięcy ramek.  Ogromna ilość zasobów obliczeniowych jest wymagane do renderowania animacji wysokiej jakości w rozsądnym czasie.  W niektórych przypadkach ponad 100 000 rdzeni wtedy były używane do renderowania tysiące ramek równolegle.
* Renderowanie projekty są oparte na projekt i wymagają różnej ilości mocy obliczeniowej:
  * Przydzielanie pojemności obliczeniowej i magazynowej, gdy jest to wymagane, skalować ją w górę lub w dół zgodnie z obciążeniem, podczas projektu i usunąć go po zakończeniu projektu.
  * Płać pojemności, gdy przydzielone, ale nie płać za jej, gdy istnieje bez obciążenia, takich jak między projektami.
  * O pracownikach wzmożeniach ruchu z powodu nieoczekiwanych zmian; większe, jeśli występują nieoczekiwane opóźnienia w projekcie i te zmiany skali muszą być przetwarzane zgodnie z harmonogramem ścisłej.
* Wybieraj spośród szerokiego sprzętu zgodnie z aplikacji, obciążeń i przedziału czasu:
  * Brak dostępnej szeroką gamę sprzętu platformy Azure, które mogą być przydzielny i zarządzane przy użyciu usługi Batch.
  * W zależności od projektu wymóg może być najlepsze ceny i wydajności lub najlepsze ogólną wydajność.  Wymagania dotyczące pamięci różnych mają różnych scen i/lub aplikacjami do renderowania.  Niektóre aplikacje renderowania można wykorzystać procesorach GPU znajdujących się pod kątem optymalnej wydajności lub niektórych funkcji. 
* Maszyny wirtualne o niskim priorytecie ograniczenie kosztów:
  * Maszyny wirtualne o niskim priorytecie są dostępne dla dużą zniżką w porównaniu ze zwykłymi maszynami wirtualnymi na żądanie i nadają się dla niektórych typów zadań.
  * Maszyny wirtualne o niskim priorytecie mogą zostać przydzieleni przez usługi Azure Batch przy użyciu usługi Batch, co zapewnia elastyczność w sposób są one używane do zaspokojenia szeroką gamę wymagania.  Pule usługi Batch może obejmować zarówno w przypadku dedykowanego, jak i o niskim priorytecie maszyn wirtualnych z możliwości w dowolnym momencie zmienić różnych typów maszyn wirtualnych.

## <a name="options-for-rendering-on-azure"></a>Opcje renderowania na platformie Azure

Brak możliwości platformy Azure, które mogą służyć do obciążenia renderowania.  Które możliwości do użycia zależy od tego, wszystkie istniejące środowiska i wymagań.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Istniejące lokalne środowisko renderowania przy użyciu aplikacji do zarządzania renderowania

Najbardziej typowe przypadek jest ma być istniejącą lokalną renderowania farmy są zarządzane przez aplikacji do zarządzania renderowania, takich jak PipelineFX Qube, Royal renderowania lub Thinkbox terminu.  Wymagane jest rozszerzenie lokalnego pojemności farmy renderowania przy użyciu maszyn wirtualnych platformy Azure.

Oprogramowanie do zarządzania renderowania ma wbudowane pomocy technicznej platformy Azure lub udostępnimy dostępnych wtyczek, które dodać pomocy technicznej platformy Azure. Aby więcej informacji na temat obsługiwanych renderować menedżerów i włączoną funkcją, zobacz artykuł [przy użyciu renderowania menedżerów](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Niestandardowe renderowanie przepływu pracy

Wymagane jest dla maszyn wirtualnych w celu rozszerzenia istniejącej farmy renderowania.  Pule usługi Azure Batch może przydzielić dużej liczby maszyn wirtualnych, umożliwić maszynom wirtualnym o niskim priorytecie może być używany i dynamicznie automatycznie skalowanych ceny pełnej maszyn wirtualnych i zapewniają płatności dla użycie licencji na program renderowania popularnych aplikacji.

### <a name="no-existing-render-farm"></a>Nie istniejących farmą renderowania

Klienckich stacji roboczych może przeprowadzać renderowania, ale zwiększa się obciążenie renderowania i on zbyt długiego czasu do użycia wyłącznie pojemności stacji roboczej.  Usługa Azure Batch może służyć do zarówno przydzielić renderowania farmie obliczeniowej na żądanie oraz planowanie zadań renderowania do farmy renderowania platformy Azure.

## <a name="azure-batch-rendering-capabilities"></a>Możliwości renderowania w usłudze Azure Batch

Usługa Azure Batch umożliwia równoległych obciążeń do uruchamiania na platformie Azure.  Umożliwia tworzenie i zarządzanie dużą liczbę maszyn wirtualnych, na których aplikacje są instalowane i uruchamiane.  Udostępnia również kompleksowe możliwości do uruchamiania wystąpień tych aplikacji, zapewniając przypisywanie zadań do maszyn wirtualnych, kolejkowanie, monitorowanie aplikacji i tak dalej do planowania zadań.

Usługa Azure Batch jest używany w przypadku wielu obciążeń, ale następujące funkcje są dostępne do specjalnie umożliwiają łatwiejsze i szybsze uruchamianie obciążeń renderowania.

* Obrazy maszyn wirtualnych ze wstępnie zainstalowanymi aplikacjami graficznymi i:
  * Dostępne są obrazy maszyny Wirtualnej w portalu Marketplace platformy Azure zawierające popularne aplikacje graficzne i renderujące, unikając konieczności samodzielnego instalowania aplikacji lub utworzyć własne niestandardowe obrazy z zainstalowanymi aplikacjami. 
* Płatność za użycie licencji na aplikacje renderowania:
  * Można płacić dla aplikacji w systemie minutowym, oprócz płacić za zasoby obliczeniowe maszyn wirtualnych, co pozwala uniknąć konieczności kupowanie licencji i potencjalnie konfigurowania serwera licencji dla aplikacji.  Również płacenia za użycie oznacza, że jest możliwe w celu zaspokojenia zmiennych i nieoczekiwane obciążenia się nie stałą liczbę licencji.
  * Użytkownik może również używać wstępnie zainstalowanymi aplikacjami z własnej licencji oraz nie licencjonowania płatność za użycie. Aby to zrobić, zazwyczaj należy zainstalować na lokalnych lub opartych na platformie Azure licencji serwera, a siecią wirtualną platformy Azure umożliwia puli renderowania nawiązać połączenie z serwerem licencji.
* Dodatki dla projektu klienta i aplikacjach do modelowania:
  * Dodatki plug-in zezwolić użytkownikom końcowym na korzystanie z usługi Azure Batch bezpośrednio z aplikacji klienckiej, takich jak Autodesk Maya, co pozwala na tworzenie pul, przesyłanie zadań i upewnij użytkowania więcej obliczeń przygotowanie wydajności niezbędnej do wykonania renderuje szybciej.
* Renderowanie manager integration:
  * Usługa Azure Batch jest zintegrowany z aplikacjami do renderowania zarządzania lub dodatki plug-in są dostępne nad umożliwieniem integracji usługi Azure Batch.

Istnieje kilka sposobów korzystania z usługi Azure Batch, które mają zastosowanie również do usługi Azure Batch rendering.

* APIs:
  * Pisz kod, używając [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch), lub inne obsługiwane interfejsy API.  Deweloperzy mogą integrować możliwości usługi Azure Batch istniejącej aplikacji lub przepływu pracy, czy chmura lub oparte na środowisku lokalnym.  Na przykład [Autodesk Maya wtyczki](https://github.com/Azure/azure-batch-maya) korzysta z interfejsu API języka Python usługi Batch do wywołania usługi Batch, tworzenie i Zarządzanie pulami, przesyłanie zadań i zadań podrzędnych oraz monitorowanie stanu.
* Narzędzia wiersza polecenia:
  * [Wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/) lub [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) może służyć do skryptu Użyj usługi Batch.
  * W szczególności Obsługa szablonów interfejsu wiersza polecenia usługi Batch sprawia, że znacznie łatwiej tworzyć pule oraz przesyłania zadań.
* Interfejsów użytkownika:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) to narzędzie klienta dla wielu platform, które umożliwia także kont usługi Batch, zarządzane i monitorowane, ale zawiera niektóre bardziej zaawansowane możliwości w porównaniu do interfejsu użytkownika witryny Azure portal.  Zestaw szablonów puli i zadania pod warunkiem, że są dostosowane do poszczególnych obsługiwanych aplikacji i może służyć do łatwego tworzenia pul i przesyłać zadania.
  * Witryna Azure portal może służyć do monitorowania usługi Azure Batch i zarządzania nimi.
* Klient aplikacji — dodatek firmy:
  * Dostępne są wtyczki umożliwiające renderowania usługi Batch można używać bezpośrednio z poziomu projektu klienta i aplikacjach do modelowania. Dodatki plug-in wywołać głównie aplikacji Batch Explorer informacje kontekstowe dotyczące modelu 3D.
  * Dostępne są następujące dodatki plug-in:
    * [Usługa Azure Batch dla programu Maya](https://github.com/Azure/azure-batch-maya)
    * [3DS Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Wprowadzenie do usługi Azure Batch rendering

Zobacz następujące samouczki wprowadzające do wypróbowania usługi Azure Batch rendering:

* [Renderowanie sceny Blender za pomocą Eksploratora usługi Batch](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Umożliwia renderowanie Autodesk sceny programu 3ds Max Batch interfejsu wiersza polecenia](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Kolejne kroki

Określić listę aplikacji renderowania i zawarte w obrazach maszyn wirtualnych platformy Azure Marketplace w wersji [w tym artykule](https://docs.microsoft.com/azure/batch/batch-rendering-applications).