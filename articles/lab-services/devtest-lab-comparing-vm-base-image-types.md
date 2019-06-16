---
title: Porównanie niestandardowych obrazów i formuł w usłudze DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnicach między niestandardowych obrazów i formuł jako podstawy maszyny Wirtualnej, podejmiesz decyzję, który z nich najlepiej pasujące do środowiska.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680302"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Porównanie niestandardowych obrazów i formuł w usłudze DevTest Labs
Zarówno [obrazów niestandardowych](devtest-lab-create-template.md) i [formuły](devtest-lab-manage-formulas.md) mogą być używane jako podstawy dla [utworzone nowe maszyny wirtualne](devtest-lab-add-vm.md). Jednak jest to po prostu obrazu opartego na dysku VHD formułę trakcie obrazu opartego na dysku VHD obrazu niestandardowego klucza rozróżnienie między niestandardowych obrazów i formuł *oprócz* wstępnie skonfigurowane ustawienia — takie jak rozmiar maszyny Wirtualnej, sieci wirtualnej podsieci i artefaktów. Te wstępnie skonfigurowane ustawienia są konfigurowane przy użyciu wartości domyślnych, które mogą zostać zastąpione w czasie tworzenia maszyny Wirtualnej. W tym artykule omówiono (specjalistów) wady i (wad) do korzystania z niestandardowych obrazów i przy użyciu formuły.

## <a name="custom-image-pros-and-cons"></a>Niestandardowy obraz zalety i wady
Obrazy niestandardowe zapewniają statyczne i niezmienne sposób tworzenia maszyn wirtualnych z wymagane środowisko. 

**Specjaliści**

* Aprowizacji za pomocą niestandardowego obrazu maszyny Wirtualnej jest szybkie ponieważ nic nie zmienia się po zostanie rozszerzona maszynę Wirtualną z obrazu. Innymi słowy nie ma żadnych ustawień, do zastosowania jako obraz niestandardowy jest po prostu obraz bez ustawień. 
* Maszyny wirtualne tworzone za pomocą pojedynczego obrazu niestandardowego są identyczne.

**Wady**

* Jeśli musisz zaktualizować niektóre aspekty obraz niestandardowy obraz, który musi zostać ponownie utworzona.  

## <a name="formula-pros-and-cons"></a>Formuły zalety i wady
Formuły umożliwiają dynamiczne tworzenie maszyn wirtualnych z żądaną/ustawienia konfiguracji.

**Specjaliści**

* Zmiany w środowisku mogą być przechwytywane w locie za pośrednictwem artefaktów. Na przykład jeśli mają zainstalowane z najnowsze elementy z potokiem wersji maszyny Wirtualnej lub zarejestrować najnowszy kod ze swojego repozytorium, po prostu użytkownika artefakt, który współdziała z najnowszym kodem w formule wraz z podstawowej docelowej lub wdrażany najnowsze elementy obraz. Zawsze, gdy ta formuła jest używany do tworzenia maszyn wirtualnych, najnowszego bity/kodu są wdrożone/zarejestrowany na maszynie wirtualnej. 
* Formuły można zdefiniować ustawienia domyślne, których nie może dostarczyć niestandardowe obrazy — takie jak ustawienia sieci wirtualnej i rozmiarów maszyn wirtualnych. 
* Zapisano w formule ustawienia są wyświetlane jako wartości domyślne, ale można go modyfikować po utworzeniu maszyny Wirtualnej. 

**Wady**

* Tworzenie maszyny Wirtualnej na podstawie formuły może zająć więcej czasu niż tworzenie maszyny Wirtualnej na podstawie niestandardowego obrazu.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne
* [Obrazy niestandardowe lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Kolejne kroki
- [Usługa DevTest Labs — często zadawane pytania](devtest-lab-faq.md)