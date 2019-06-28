---
title: Rozwiń rozwiązanie VMware na platformie Azure, Chmura prywatna CloudSimple
description: Zawiera opis sposobu rozszerzania istniejącej chmurze prywatnej CloudSimple na dodanie pojemności w istniejącym lub nowym klastrze
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332672"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Rozwiń CloudSimple chmury prywatnej

CloudSimple zapewnia elastyczność rozszerzania dynamicznie chmury prywatnej. Może zaczynać się od konfiguracji o mniejszej, a następnie rozwiń potrzebujesz większej pojemności. Lub można utworzyć chmurę prywatną, oparte na bieżących potrzeb, a następnie rozwiń węzeł wzrostem zużycia.

Chmura prywatna składa się z co najmniej jeden klaster vSphere. Każdy klaster może mieć węzłów 3-16.  Rozszerzając chmury prywatnej, możesz dodać węzły do istniejącego klastra lub Utwórz nowy klaster. Aby rozszerzyć istniejący klaster, dodatkowe węzły muszą być tego samego typu (SKU), co istniejące węzły. Do tworzenia nowego klastra, węzłów może być innego typu. Aby uzyskać więcej informacji o limitach chmurę prywatną, zobacz ogranicza sekcji [omówienie chmury prywatnej CloudSimple](cloudsimple-private-cloud.md) artykułu.

Chmura prywatna jest tworzony z domyślną **Datacenter** programie vcenter.  Każde centrum danych służy jako jednostka zarządzania najwyższego poziomu.  Nowy klaster CloudSimple zapewnia wybór dodanie do istniejącego centrum danych lub tworzenie nowych centrów danych.

W ramach nową konfigurację klastra CloudSimple służy do konfigurowania infrastruktury VMware.  Ustawienia obejmują ustawienia magazynu dla grupy dysków sieci vSAN, VMware o wysokiej dostępności i harmonogram rozproszonych zasobów (DRS).

Chmurę prywatną można rozszerzyć, wiele razy. Rozszerzenie jest możliwe tylko wtedy, gdy pozostają w granicach węzeł ogólny. Każdorazowo rozwiń chmury prywatnej, Dodaj do istniejącego klastra lub Utwórz nową.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musi być obsługiwana węzłów, aby rozszerzyć chmury prywatnej.  Aby uzyskać więcej informacji na temat inicjowania obsługi administracyjnej węzłów, zobacz [Inicjowanie obsługi administracyjnej węzłów dla oprogramowania VMware Solution by CloudSimple - Azure](create-nodes.md) artykułu.  Do tworzenia nowego klastra, musisz mieć co najmniej trzy węzły dostępnych w tej samej jednostki SKU.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Rozwiń chmury prywatnej

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz **zasobów** strony i wybierz chmurę prywatną, dla którego chcesz rozwinąć.

3. W sekcji podsumowania kliknij **rozwiń**.

    ![Rozwiń chmury prywatnej](media/resources-expand-private-cloud.png)

4. Wybierz, czy rozwiń istniejącego klastra lub Utwórz nowy klaster vSphere. Po wprowadzeniu dowolnych zmian, podsumowanie informacji na stronie jest aktualizowana.

    * Aby rozszerzyć istniejący klaster, kliknij przycisk **rozszerzyć istniejący klaster**. Wybierz klaster, który chcesz rozwinąć, a następnie wprowadź liczbę węzłów do dodania. Każdy klaster może mieć maksymalnie 16 węzłach.
    * Aby dodać nowy klaster, kliknij **Utwórz nowy klaster**. Wprowadź nazwę klastra. Wybierz istniejącego centrum danych, lub wprowadź nazwę, aby utworzyć nowe centrum danych. Wybierz typ węzła. Można wybrać typ innego węzła, podczas tworzenia nowego klastra vSphere, ale nie w przypadku, gdy rozwinięcie w istniejącym klastrze vSphere. Wybierz liczbę węzłów. Każdy nowy klaster musi mieć co najmniej trzy węzły.

    ![Rozwiń chmury prywatnej — Dodawanie węzłów](media/resources-expand-private-cloud-add-nodes.png)

5. Kliknij przycisk **przesyłania** rozszerzania chmury prywatnej.

## <a name="next-steps"></a>Kolejne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [Chmurami prywatnymi](cloudsimple-private-cloud.md)