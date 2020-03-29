---
title: Porównywanie niestandardowych obrazów i formuł w devtest labs | Dokumenty firmy Microsoft
description: Dowiedz się więcej o różnicach między niestandardowymi obrazami i formułami jako bazami maszyn wirtualnych, aby zdecydować, który z nich najlepiej pasuje do Twojego środowiska.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: ae7556eda817b9eb7be84f9d4a23ea91d3d5440d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64680302"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Porównywanie niestandardowych obrazów i formuł w laboratorium DevTest
Zarówno [niestandardowe obrazy,](devtest-lab-create-template.md) jak i [formuły](devtest-lab-manage-formulas.md) mogą być używane jako podstawy dla [utworzonych nowych maszyn wirtualnych](devtest-lab-add-vm.md). Jednak kluczowe rozróżnienie między niestandardowymi obrazami i formułami polega na tym, że obraz niestandardowy jest po prostu obrazem opartym na dysku VHD, podczas gdy formuła jest obrazem opartym na dysku wirtualnym *oprócz* wstępnie skonfigurowanych ustawień , takich jak rozmiar maszyny Wirtualnej, sieć wirtualna, podsieć i artefakty. Te wstępnie skonfigurowane ustawienia są konfigurowane z wartościami domyślnymi, które można zastąpić w momencie tworzenia maszyny Wirtualnej. W tym artykule wyjaśniono niektóre zalety (plusy) i wady (wady) do korzystania z niestandardowych obrazów w porównaniu z używaniem formuł.

## <a name="custom-image-pros-and-cons"></a>Niestandardowe zalety i minusy obrazów
Obrazy niestandardowe zapewniają statyczny, niezmienny sposób tworzenia maszyn wirtualnych z żądanego środowiska. 

**Zalety**

* Inicjowanie obsługi administracyjnej maszyny Wirtualnej z obrazu niestandardowego jest szybkie, ponieważ nic się nie zmienia po wydzieleniu maszyny Wirtualnej z obrazu. Innymi słowy, nie ma żadnych ustawień do zastosowania, ponieważ obraz niestandardowy jest tylko obrazem bez ustawień. 
* Maszyny wirtualne utworzone na podstawie pojedynczego obrazu niestandardowego są identyczne.

**Wady**

* Jeśli chcesz zaktualizować jakiś aspekt obrazu niestandardowego, obraz musi zostać odtworzony.  

## <a name="formula-pros-and-cons"></a>Plusy i minusy formuły
Formuły zapewniają dynamiczny sposób tworzenia maszyn wirtualnych z żądanej konfiguracji/ustawień.

**Zalety**

* Zmiany w środowisku mogą być przechwytywane w locie za pomocą artefaktów. Na przykład jeśli chcesz, aby maszyna wirtualna była zainstalowana z najnowszymi bitami z potoku wydania lub zarejestrować najnowszy kod z repozytorium, możesz po prostu określić artefakt, który wdraża najnowsze bity lub rejestruje najnowszy kod w formule wraz z bazą docelową Obrazu. Za każdym razem, gdy ta formuła jest używana do tworzenia maszyn wirtualnych, najnowsze bity/kod są wdrażane/emjedzone na maszynie wirtualnej. 
* Formuły mogą definiować ustawienia domyślne, których obrazy niestandardowe nie mogą zapewnić — takie jak rozmiary maszyn wirtualnych i ustawienia sieci wirtualnej. 
* Ustawienia zapisane w formule są wyświetlane jako wartości domyślne, ale mogą być modyfikowane podczas tworzenia maszyny wirtualnej. 

**Wady**

* Tworzenie maszyny Wirtualnej z formuły może zająć więcej czasu niż utworzenie maszyny Wirtualnej na podstawie obrazu niestandardowego.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Podobne posty na blogu
* [Niestandardowe obrazy lub formuły?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Następne kroki
- [DevTest Labs – często zadawane pytania](devtest-lab-faq.md)