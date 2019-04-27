---
title: Tworzenie fabryki danych obrazu w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć fabrykę danych obrazu niestandardowego w usłudze Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1bb31614c04d6073bc40c510fc43b2f8e4e189
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622645"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Utwórz fabrykę obrazu niestandardowego w usłudze Azure DevTest Labs
W tym artykule pokazano, jak skonfigurować fabrycznie obrazu niestandardowego przy użyciu dostępnych w przykładowych skryptach [repozytorium Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Co to jest fabrykę obrazu?
Fabryki obrazu jest rozwiązaniem konfiguracji jako kodu, które produkcją i dystrybucją obrazy automatycznie w regularnych odstępach czasu za pomocą odpowiednie konfiguracje. Obrazy w fabryce obrazu są zawsze aktualne, a rutynowej konserwacji jest bliski zeru po całego procesu jest zautomatyzowane. Ponadto wszystkie wymagane konfiguracje znajdują się już w obrazie, dlatego oszczędza czas z ręcznego konfigurowania system po utworzeniu maszyny Wirtualnej przy użyciu podstawowego systemu operacyjnego.

Znaczące akceleratora można pobrać pulpitu dla deweloperów, do stanu gotowości w usłudze DevTest Labs korzysta z obrazów niestandardowych. Wadą obrazów niestandardowych jest coś, co jest dodatkowe do zachowania w środowisku laboratoryjnym. Na przykład wersje próbne produktów wygasają wraz z upływem czasu (lub) nie są stosowane nowo wydane aktualizacje zabezpieczeń, które wymusić nam okresowego odświeżania obrazu niestandardowego. Za pomocą fabryki obraz masz definicję obrazu zaewidencjonowany kontrolą kodu źródłowego i zautomatyzowanego procesu w celu utworzenia niestandardowych obrazów na podstawie definicji.

To rozwiązanie umożliwia tworzenie maszyn wirtualnych z obrazów niestandardowych eliminuje koszty konserwacji dodatkowe szybkości. Dzięki temu rozwiązaniu można automatycznie Tworzenie niestandardowych obrazów, rozsyłaj je do innych DevTest Labs i wycofać starych obrazów. W poniższym klipie wideo informacje o fabrycznie obrazu i jak jest implementowane za pomocą usługi DevTest Labs.  Wszystkie skrypty programu Azure Powershell są łatwo dostępne i znajduje się tutaj: [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Ogólny widok rozwiązania
To rozwiązanie umożliwia tworzenie maszyn wirtualnych z obrazów niestandardowych eliminuje koszty konserwacji dodatkowe szybkości. Dzięki temu rozwiązaniu umożliwia automatyczne tworzenie niestandardowych obrazów i przekazać je do innych DevTest Labs. DevOps platformy Azure (dawniej Visual Studio Team Services) są używane jako aparatu aranżacji do automatyzacji wszystkich operacji w usłudze DevTest Labs.

![Ogólny widok rozwiązania](./media/create-image-factory/high-level-view-of-solution.png)

Brak [rozszerzenia usługi VSTS na potrzeby usługi DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) to pozwala wykonać te poszczególne kroki:

- Tworzenie obrazu niestandardowego
- Tworzenie maszyny wirtualnej
- Usuwanie maszyny Wirtualnej
- Tworzenie środowiska
- Usuwanie środowiska
- Wypełnij środowiska

Przy użyciu rozszerzenia usługi DevTest Labs to prosty sposób rozpocząć pracę z automatyczne tworzenie niestandardowych obrazów w usłudze DevTest Labs.

Brak implementacji alternatywnych przy użyciu skryptu programu PowerShell dla scenariusza bardziej złożone. Przy użyciu programu PowerShell, można w pełni zautomatyzować fabrykę obrazu, oparte na usłudze DevTest Labs, używanym w ciągłej integracji i ciągłego dostarczania (CI/CD) łańcucha narzędzi. Są zasady, a następnie w tym rozwiązaniu alternatywny:

- Typowe aktualizacje powinny wymagać żadnych zmian do fabryki obrazu. (np. dodanie nowego typu niestandardowego obrazu, automatyczne wycofanie starych obrazów, dodanie nowego "punktu końcowego" DevTest Labs umożliwia otrzymywanie niestandardowych obrazów i tak dalej).
- Wspólne zmiany są objęte kontrolą kodu źródłowego (infrastruktura jako kod)
- Odbieranie obrazów niestandardowych w usłudze DevTest Labs może nie być w tej samej subskrypcji platformy Azure (labs span subskrypcje)
- Skrypty programu PowerShell musi być wielokrotnego użytku, dzięki czemu firma Microsoft może zwiększać dodatkowe fabryki, zgodnie z potrzebami

## <a name="next-steps"></a>Kolejne kroki
Przejdź do następnego artykułu, w tej sekcji: [Uruchom fabrykę obrazu z DevOps platformy Azure](image-factory-set-up-devops-lab.md)
