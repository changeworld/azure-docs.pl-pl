---
title: Rozwiń rozwiązanie Azure VMware według chmury cloudsimple private cloud
description: W tym artykule opisano sposób rozwijania istniejącej chmury prywatnej CloudSimple w celu zwiększenia pojemności w istniejącym lub nowym klastrze
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025303"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Rozwiń chmurę prywatną CloudSimple

CloudSimple zapewnia elastyczność dynamicznego rozszerzania chmury prywatnej. Można rozpocząć od mniejszej konfiguracji, a następnie rozwinąć, ponieważ potrzebujesz większej pojemności. Możesz też utworzyć chmurę prywatną na podstawie bieżących potrzeb, a następnie rozwinąć ją wraz ze wzrostem zużycia.

Chmura prywatna składa się z jednego lub więcej klastrów vSphere. Każdy klaster może mieć od 3 do 16 węzłów.  Podczas rozwijania chmury prywatnej należy dodać węzły do istniejącego klastra lub utworzyć nowy klaster. Aby rozwinąć istniejący klaster, dodatkowe węzły muszą być tego samego typu (SKU) co istniejące węzły. Do tworzenia nowego klastra, węzły mogą być innego typu. Aby uzyskać więcej informacji na temat limitów chmury prywatnej, zobacz sekcję limitów w [cloudSimple chmura prywatna omówienie](cloudsimple-private-cloud.md) artykułu.

Chmura prywatna jest tworzona z domyślnym **centrum danych** w vCenter.  Każde centrum danych służy jako jednostka zarządzania najwyższego poziomu.  W przypadku nowego klastra CloudSimple umożliwia dodawanie do istniejącego centrum danych lub tworzenie nowego centrum danych.

W ramach nowej konfiguracji klastra CloudSimple konfiguruje infrastrukturę VMware.  Ustawienia obejmują ustawienia magazynu dla grup dysków vSAN, wysokiej dostępności VMware i harmonogramu zasobów rozproszonych (DRS).

Chmura prywatna może być rozszerzana wiele razy. Rozszerzeń można wykonać tylko wtedy, gdy pozostaniesz w ogólnych granicach węzła. Za każdym razem, gdy rozwijasz chmurę prywatną, którą dodajesz do istniejącego klastra lub tworzysz nowy.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Węzły muszą być aprowizowane, zanim będzie można rozwinąć chmurę prywatną.  Aby uzyskać więcej informacji na temat inicjowania obsługi administracyjnej węzłów, zobacz [inicjowanie obsługi administracyjnej węzłów rozwiązania VMware przez CloudSimple —](create-nodes.md) artykuł platformy Azure.  Do utworzenia nowego klastra musi mieć co najmniej trzy dostępne węzły tej samej jednostki SKU.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Rozszerzanie chmury prywatnej

1. [Uzyskaj dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **Zasoby** i wybierz chmurę prywatną, dla której chcesz się rozwinąć.

3. W sekcji Podsumowanie kliknij pozycję **Rozwiń**.

    ![Rozwiń chmurę prywatną](media/resources-expand-private-cloud.png)

4. Wybierz, czy chcesz rozwinąć istniejący klaster, czy utworzyć nowy klaster vSphere. Po wprowadzaniu zmian informacje podsumowujące na stronie są aktualizowane.

    * Aby rozwinąć istniejący klaster, kliknij pozycję **Rozwiń istniejący klaster**. Wybierz klaster, który chcesz rozwinąć, i wprowadź liczbę węzłów do dodania. Każdy klaster może mieć maksymalnie 16 węzłów.
    * Aby dodać nowy klaster, kliknij pozycję **Utwórz nowy klaster**. Wprowadź nazwę klastra. Wybierz istniejące centrum danych lub wprowadź nazwę, aby utworzyć nowe centrum danych. Wybierz typ węzła. Podczas tworzenia nowego klastra vSphere można wybrać inny typ węzła, ale nie podczas rozszerzania istniejącego klastra vSphere. Wybierz liczbę węzłów. Każdy nowy klaster musi mieć co najmniej trzy węzły.

    ![Rozwiń chmurę prywatną - dodawanie węzłów](media/resources-expand-private-cloud-add-nodes.png)

5. Kliknij **przycisk Prześlij,** aby rozwinąć chmurę prywatną.

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)