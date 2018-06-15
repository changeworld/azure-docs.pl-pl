---
title: Porównywanie niestandardowych obrazów i formuły w usłudze DevTest Labs | Dokumentacja firmy Microsoft
description: Więcej informacji na temat różnic między niestandardowych obrazów i formuły jako baz maszyn wirtualnych może zdecydować, który najlepiej odpowiada środowisku.
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
ms.openlocfilehash: 37288fd4a9c7558d05728b8ce03df505117e0232
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787444"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Porównywanie niestandardowych obrazów i formuły w usłudze DevTest Labs
Zarówno [niestandardowych obrazów](devtest-lab-create-template.md) i [formuły](devtest-lab-manage-formulas.md) mogą być używane jako podstawy [utworzone nowe maszyny wirtualne](devtest-lab-add-vm.md). Jednak klucza rozróżnianie niestandardowych obrazów i formuły jest obraz niestandardowy po prostu obraz oparty na dysku VHD, gdy formuła jest obraz oparty na dysku VHD *oprócz* wstępnie skonfigurowane ustawienia — takich jak rozmiar maszyny Wirtualnej, sieci wirtualnej, podsieci i artefaktów. Te wstępnie skonfigurowane ustawienia są skonfigurowane z wartościami domyślnymi, które mogą zostać zastąpione w czasie tworzenia maszyny Wirtualnej. W tym artykule omówiono (specjalistów) zalety i wady (wad) przy użyciu niestandardowych obrazów w porównaniu z użyciem formuły.

## <a name="custom-image-pros-and-cons"></a>Obraz niestandardowy zalet i wad
Niestandardowe obrazy umożliwiają statyczne, niezmienne do tworzenia maszyn wirtualnych z wymagane środowisko. 

**Specjaliści**

* Obsługę maszyny Wirtualnej z obrazu niestandardowego jest szybkie, ponieważ nic nie zmienia się po maszyny Wirtualnej jest uruchomione z obrazu. Innymi słowy braku ustawień do zastosowania jako obraz niestandardowy jest tylko obraz bez ustawień. 
* Maszyny wirtualne utworzone za pomocą pojedynczego obrazu niestandardowego są identyczne.

**Cons**

* Jeśli musisz zaktualizować niektóre aspekty obraz niestandardowy obraz muszą zostać ponownie utworzone.  

## <a name="formula-pros-and-cons"></a>Formuły zalet i wad
Formuły umożliwiają dynamiczne tworzenie maszyn wirtualnych z odpowiednie ustawienia.

**Specjaliści**

* Zmiany w środowisku, można przechwycić w locie za pośrednictwem artefaktów. Na przykład jeśli mają zainstalowane najnowsze bitów z potoku sieci wersji maszyny Wirtualnej lub zarejestrować najnowsze kodu z repozytorium, po prostu określić artefaktu, który wdraża najnowsze bitów lub rejestruje najnowsze kodu w formule wraz z obrazu podstawowego docelowego. Zawsze, gdy w tej formule jest używany do tworzenia maszyn wirtualnych, najnowsze bity/kodu są wdrożone/zarejestrowany do maszyny Wirtualnej. 
* Formuły można zdefiniować ustawienia domyślne, których nie może dostarczyć niestandardowe obrazy — takich jak ustawienia sieci wirtualnej i rozmiarów maszyn wirtualnych. 
* Ustawienia zapisane w formule są wyświetlane jako wartości domyślne, ale można zmodyfikować po utworzeniu maszyny Wirtualnej. 

**Cons**

* Tworzenie maszyny Wirtualnej na podstawie formuły może zająć więcej czasu niż tworzenia maszyny Wirtualnej z obrazu niestandardowego.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne
* [Niestandardowe obrazy lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Kolejne kroki
- [DevTest Labs — często zadawane pytania](devtest-lab-faq.md)