---
title: Omówienie renderowania — usługa Azure Batch
description: Wprowadzenie korzystania z platformy Azure do renderowania i omówienie możliwości renderowania usługi Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60774033"
---
# <a name="rendering-using-azure"></a>Renderowanie przy użyciu platformy Azure

Renderowanie to proces robienia modeli 3D i przekształcania ich w obrazy 2D. Pliki scen 3D są tworzyć w aplikacjach takich jak Autodesk 3ds Max, Autodesk Maya i Blender.  Aplikacje renderowania, takie jak Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray i Blender Cycles, generują obrazy 2D.  Czasami pojedyncze obrazy są tworzone z plików sceny. Jednak jest to wspólne do modelowania i renderowania wielu obrazów, a następnie połączyć je w animacji.

Obciążenie renderowania jest intensywnie używane do efektów specjalnych (VFX) w branży mediów i rozrywki. Renderowanie jest również używane w wielu innych sektorach, takich jak reklama, produkcja, handel detaliczny oraz przemysł naftowy.

Proces renderowania jest intensywny obliczeniowo; może być wiele klatek / obrazów do wytworzenia, a renderowanie każdego obrazu może zająć wiele godzin.  Renderowanie jest zatem idealnym obciążeniem przetwarzania wsadowego, które może korzystać z platformy Azure i usługi Azure Batch do uruchamiania wielu renderów równolegle.

## <a name="why-use-azure-for-rendering"></a>Dlaczego warto używać platformy Azure do renderowania?

Z wielu powodów renderowanie jest obciążeniem idealnie nadaje się do platformy Azure i usługi Azure Batch:

* Zadania renderowania można podzielić na wiele części, które można uruchamiać równolegle przy użyciu wielu maszyn wirtualnych:
  * Animacje składają się z wielu klatek, a każda klatka może być renderowana równolegle.  Im więcej maszyn wirtualnych jest dostępnych do przetwarzania każdej klatki, tym szybciej można uzyskać wszystkie klatki i animację.
  * Niektóre programy renderowania umożliwiają pojedyncze klatki podzielone na wiele części, takich jak kafelki lub plasterki.  Każdy kawałek może być renderowany oddzielnie, a następnie połączony w ostateczny obraz po zakończeniu wszystkich elementów.  Im więcej maszyn wirtualnych jest dostępnych, tym szybciej można renderować ramkę.
* Projekty renderowania mogą wymagać ogromnej skali:
  * Poszczególne ramki mogą być złożone i wymagają wielu godzin renderowania, nawet na wysokiej klasy sprzęcie; animacje mogą składać się z setek tysięcy klatek.  Ogromna ilość obliczeń jest wymagana do renderowania wysokiej jakości animacji w rozsądnym czasie.  W niektórych przypadkach ponad 100 000 rdzeni zostało użytych do równoległego renderowania tysięcy ramek.
* Projekty renderowania są oparte na projektach i wymagają różnych ilości obliczeń:
  * Przydzielanie pojemności obliczeniowej i magazynu w razie potrzeby, skalowanie w górę lub w dół zgodnie z obciążeniem podczas projektu i usuwanie go po zakończeniu projektu.
  * Płać za pojemność po przydzieleniu, ale nie płać za nią, gdy nie ma obciążenia, na przykład między projektami.
  * Zaspokoić wybuchy z powodu nieoczekiwanych zmian; skalowanie wyższe, jeśli występują nieoczekiwane zmiany późno w projekcie i te zmiany muszą być przetwarzane zgodnie z napiętym harmonogramem.
* Wybieraj spośród szerokiego wyboru sprzętu w zależności od aplikacji, obciążenia i przedziału czasowego:
  * Istnieje szeroki wybór sprzętu dostępnego na platformie Azure, który można przydzielić i zarządzać za pomocą usługi Batch.
  * W zależności od projektu, wymóg może być dla najlepszej ceny / wydajności lub najlepszej ogólnej wydajności.  Różne sceny i/lub aplikacje renderowania będą miały różne wymagania dotyczące pamięci.  Niektóre aplikacje renderowania można wykorzystać procesory GPU dla najlepszej wydajności lub niektórych funkcji. 
* Maszyny wirtualne o niskim priorytecie obniżają koszty:
  * Maszyny wirtualne o niskim priorytecie są dostępne dla dużej zniżki w porównaniu do zwykłych maszyn wirtualnych na żądanie i są odpowiednie dla niektórych typów zadań.
  * Maszyny wirtualne o niskim priorytecie mogą być przydzielane przez usługę Azure Batch, a usługa Batch zapewnia elastyczność w zakresie sposobu ich wykorzystania w celu spełnienia szerokiego zestawu wymagań.  Pule wsadowe mogą składać się zarówno z dedykowanych, jak i niskiego priorytetu maszyn wirtualnych, z możliwością zmiany kombinacji typów maszyn wirtualnych w dowolnym momencie.

## <a name="options-for-rendering-on-azure"></a>Opcje renderowania na platformie Azure

Istnieje szereg możliwości platformy Azure, które mogą służyć do renderowania obciążeń.  Jakie możliwości użyć zależy od istniejącego środowiska i wymagań.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Istniejące lokalne środowisko renderowania przy użyciu aplikacji do zarządzania renderowaniem

Najczęstszym przypadkiem jest, aby istniała lokalna farma renderowania zarządzana przez aplikację do zarządzania renderowaniem, taką jak PipelineFX Qube, Royal Render lub Thinkbox Deadline.  Wymaganiem jest rozszerzenie pojemności farmy renderowania lokalnego przy użyciu maszyn wirtualnych platformy Azure.

Oprogramowanie do zarządzania renderowaniem ma wbudowaną obsługę platformy Azure lub udostępniamy wtyczki, które dodają obsługę platformy Azure. Aby uzyskać więcej informacji na temat obsługiwanych menedżerów renderowania i włączonych funkcji, zobacz artykuł dotyczący [korzystania z menedżerów renderowania](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Niestandardowy przepływ pracy renderowania

Wymaganie jest dla maszyn wirtualnych, aby rozszerzyć istniejącą farmę renderowania.  Pule usługi Azure Batch mogą przydzielać dużą liczbę maszyn wirtualnych, zezwalać na maszyny wirtualne o niskim priorytecie i dynamicznie skalowane automatycznie za pomocą pełnoceniowych maszyn wirtualnych oraz zapewniać licencjonowanie płatnego użytku dla popularnych aplikacji renderowania.

### <a name="no-existing-render-farm"></a>Brak istniejącej farmy renderowania

Klienckie stacje robocze mogą wykonywać renderowanie, ale obciążenie renderowania wzrasta i trwa zbyt długo, aby używać wyłącznie pojemności stacji roboczej.  Usługa Azure Batch może służyć zarówno do przydzielania obliczeń farmy renderowania na żądanie, jak i do planowania zadań renderowania w farmie renderowania platformy Azure.

## <a name="azure-batch-rendering-capabilities"></a>Możliwości renderowania usługi Azure Batch

Usługa Azure Batch umożliwia uruchamianie równoległych obciążeń na platformie Azure.  Umożliwia tworzenie i zarządzanie dużą liczbą maszyn wirtualnych, na których aplikacje są instalowane i uruchamiane.  Zapewnia również kompleksowe możliwości planowania zadań do uruchamiania wystąpień tych aplikacji, zapewniając przypisanie zadań do maszyn wirtualnych, kolejkowanie, monitorowanie aplikacji i tak dalej.

Usługa Azure Batch jest używana dla wielu obciążeń, ale dostępne są następujące funkcje, aby w szczególności ułatwić i przyspieszyć uruchamianie obciążeń renderowania.

* Obrazy maszyn wirtualnych z fabrycznie zainstalowaną grafiką i aplikacjami renderowania:
  * Dostępne są obrazy maszyn wirtualnych usługi Azure Marketplace, które zawierają popularne grafiki i aplikacje renderowania, unikając konieczności samodzielnego instalowania aplikacji lub tworzenia własnych obrazów niestandardowych z zainstalowanymi aplikacjami. 
* Licencjonowanie płatności za użycie dla aplikacji renderowania:
  * Można zapłacić za aplikacje na minutę, oprócz płacenia za maszyny wirtualne obliczeniowe, co pozwala uniknąć konieczności kupowania licencji i potencjalnie konfigurowania serwera licencji dla aplikacji.  Płacenie za użytkowanie oznacza również, że możliwe jest zaspokojenie różnego i nieoczekiwanego obciążenia, ponieważ nie ma stałej liczby licencji.
  * Możliwe jest również korzystanie z wstępnie zainstalowanych aplikacji z własnymi licencjami i nieużywanie licencji typu pay-per-use. W tym celu zazwyczaj należy zainstalować lokalny lub oparty na platformie Azure serwer licencji i użyć sieci wirtualnej platformy Azure do połączenia puli renderowania z serwerem licencji.
* Wtyczki do projektowania i modelowania klientów:
  * Wtyczki umożliwiają użytkownikom końcowym korzystanie z usługi Azure Batch bezpośrednio z aplikacji klienckiej, takiej jak Autodesk Maya, umożliwiając im tworzenie pul, przesyłanie zadań i korzystanie z większej pojemności obliczeniowej w celu szybszego renderowania.
* Integracja menedżera renderowania:
  * Usługa Azure Batch jest zintegrowana z aplikacjami do zarządzania renderowaniem lub wtyczki są dostępne w celu zapewnienia integracji usługi Azure Batch.

Istnieje kilka sposobów korzystania z usługi Azure Batch, z których wszystkie dotyczą również renderowania usługi Azure Batch.

* Interfejsy API:
  * Napisz kod przy użyciu [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch)lub innych obsługiwanych interfejsów API.  Deweloperzy mogą integrować możliwości usługi Azure Batch z istniejącymi aplikacjami lub przepływem pracy, zarówno w chmurze, jak i lokalnie.  Na przykład [autodesk Maya plug-in](https://github.com/Azure/azure-batch-maya) wykorzystuje interfejs API języka Python batch do wywoływania usługi Batch, tworzenie i zarządzanie pulami, przesyłanie zadań i zadań oraz stan monitorowania.
* Narzędzia wiersza polecenia:
  * Wiersz [polecenia platformy Azure](https://docs.microsoft.com/cli/azure/) lub programu Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview) może służyć do skryptu batch używać.
  * W szczególności obsługa szablonu interfejsu wiersza polecenia usługi Batch znacznie ułatwia tworzenie pul i przesyłanie zadań.
* Uis:
  * [Wykalorator partii](https://github.com/Azure/BatchExplorer) to narzędzie klienta między platformami, które umożliwia również zarządzanie kontami usługi Batch i ich monitorowanie, ale zapewnia jedne bogatsze możliwości w porównaniu z interfejsem użytkownika witryny azure portal.  Zestaw szablonów puli i zadań są dostarczane, które są dostosowane do każdej obsługiwanej aplikacji i mogą służyć do łatwego tworzenia pul i przesyłania zadań.
  * Witryna Azure portal może służyć do zarządzania i monitorowania usługi Azure Batch.
* Wtyczka aplikacji klienckiej:
  * Dostępne są wtyczki, które umożliwiają renderowanie usługi Batch do użycia bezpośrednio w aplikacjach do projektowania i modelowania klienta. Wtyczki głównie wywołać aplikację Eksploratora wsadowego z informacji kontekstowych o bieżącym modelu 3D.
  * Dostępne są następujące wtyczki:
    * [Usługa Azure Batch dla Mayi](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Wprowadzenie do renderowania usługi Azure Batch

Aby wypróbować renderowanie usługi Azure Batch, zobacz następujące samouczki wprowadzające:

* [Renderowanie sceny programu Blender za pomocą Eksploratora wsadowego](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Renderowanie sceny Programu Autodesk 3ds Max za pomocą interfejsu wiersza polecenia wsadowego](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Następne kroki

Określ listę aplikacji i wersji renderowania zawartych w obrazach maszyn wirtualnych w portalu Azure Marketplace w [tym artykule](https://docs.microsoft.com/azure/batch/batch-rendering-applications).