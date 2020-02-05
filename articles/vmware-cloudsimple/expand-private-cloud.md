---
title: Rozwiń chmurę prywatną Azure VMware Solutions (Automatyczna synchronizacja)
description: Zawiera opis sposobu rozszerzania istniejącej chmury prywatnej do automatycznej synchronizacji w celu dodania pojemności w istniejącym lub nowym klastrze.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025303"
---
# <a name="expand-an-avs-private-cloud"></a>Rozwiń chmurę prywatną w wersji zaautomatycznej

Funkcja automatycznej synchronizacji zapewnia elastyczność umożliwiającą dynamiczne rozszerzanie chmury prywatnej o automatycznej synchronizacji. Możesz zacząć od mniejszej konfiguracji, a następnie rozwijać ją, gdy potrzebujesz większej pojemności. Możesz też utworzyć chmurę prywatną do automatycznej synchronizacji na podstawie bieżących potrzeb, a następnie rozwijać ją w miarę wzrostu zużycia.

Chmura prywatna do automatycznej synchronizacji składa się z co najmniej jednego klastra vSphere. Każdy klaster może zawierać od 3 do 16 węzłów. Podczas rozszerzania chmury prywatnej o automatycznej synchronizacji należy dodać węzły do istniejącego klastra lub utworzyć nowy klaster. Aby rozszerzyć istniejący klaster, dodatkowe węzły muszą być tego samego typu (SKU) jak istniejące węzły. W przypadku tworzenia nowego klastra węzły mogą być innego typu. Aby uzyskać więcej informacji na temat synchronizacji limitów chmur prywatnych, zobacz sekcję limity w artykule [Omówienie automatycznej synchronizacji chmury prywatnej](cloudsimple-private-cloud.md) .

Chmura prywatna w wersji zaautomatycznej jest tworzona przy użyciu domyślnego **centrum** danych w programie vCenter. Każde centrum danych służy jako jednostka zarządzania najwyższego poziomu. W przypadku nowego klastra narzędzie do automatycznej synchronizacji oferuje możliwość dodania do istniejącego centrum danych lub utworzenia nowego centrum danych.

W ramach nowej konfiguracji klastra automatyczna konfiguracja systemu pozwala skonfigurować infrastrukturę VMware. Ustawienia obejmują ustawienia magazynu dla grup dysków sieci vSAN, wysokiej dostępności VMware i Distributed Resource Scheduler (DRS).

Chmurę prywatną do automatycznej synchronizacji można rozszerzyć wiele razy. Rozszerzanie można wykonać tylko wtedy, gdy użytkownik pozostaje w ogólnym limicie węzłów. Za każdym razem, gdy rozszerzasz chmurę prywatną, którą chcesz dodać do istniejącego klastra, lub Utwórz nową.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było rozszerzyć chmurę prywatną automatycznej synchronizacji, węzły muszą być obsługiwane. Aby uzyskać więcej informacji o węzłach aprowizacji, zobacz temat [Inicjowanie obsługi węzłów dla rozwiązań VMware przez narzędzie do automatycznej synchronizacji — artykuł platformy Azure](create-nodes.md) . Do utworzenia nowego klastra potrzebne są co najmniej trzy dostępne węzły tej samej jednostki SKU.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Rozwiń chmurę prywatną w wersji zaautomatycznej

1. [Uzyskaj dostęp do portalu automatycznej synchronizacji](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** i wybierz chmurę prywatną do automatycznej synchronizacji, dla której chcesz rozwinąć.

3. W sekcji Podsumowanie kliknij przycisk **Rozwiń**.

    ![Rozwiń listę automatyczna synchronizacja chmurę prywatną](media/resources-expand-private-cloud.png)

4. Wybierz, czy chcesz rozszerzyć istniejący klaster, czy utworzyć nowy klaster vSphere. Podczas wprowadzania zmian informacje podsumowujące na stronie zostaną zaktualizowane.

    * Aby rozwinąć istniejący klaster, kliknij przycisk **Rozwiń istniejący klaster**. Wybierz klaster, który chcesz rozwinąć, a następnie wprowadź liczbę węzłów do dodania. Każdy klaster może zawierać maksymalnie 16 węzłów.
    * Aby dodać nowy klaster, kliknij przycisk **Utwórz nowy klaster**. Wprowadź nazwę klastra. Wybierz istniejące centrum danych lub wprowadź nazwę, aby utworzyć nowe centrum danych. Wybierz typ węzła. Możesz wybrać inny typ węzła podczas tworzenia nowego klastra vSphere, ale nie podczas rozszerzania istniejącego klastra vSphere. Wybierz liczbę węzłów. Każdy nowy klaster musi mieć co najmniej trzy węzły.

    ![Rozwiń listę automatyczna synchronizacja chmur prywatnych — Dodaj węzły](media/resources-expand-private-cloud-add-nodes.png)

5. Kliknij pozycję **Prześlij** , aby rozwinąć chmurę prywatną do automatycznej synchronizacji.

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [automatycznej synchronizacji chmur prywatnych](cloudsimple-private-cloud.md)