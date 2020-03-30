---
title: Jednostki SKU dla obrazu kontenerów platformy Azure | Azure Marketplace
description: Konfigurowanie jednostek SKU dla kontenera platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: adbe3d4c498c8f4e4968ca903f78c34aedca9a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280001"
---
# <a name="container-skus-tab"></a>Karta Jednostki SKU kontenera

Karta **Jednostki SKU** na stronie **Nowa oferta** umożliwia utworzenie co najmniej jednej jednostki SKU i skojarzenie ich z nową ofertą.  Można użyć różnych jednostek SKU do rozróżniania rozwiązania według zestawów funkcji, modeli rozliczeń lub innych cech.

## <a name="sku-settings"></a>Ustawienia jednostki SKU

Po rozpoczęciu tworzenia nowej oferty nie ma żadnych jednostek SKU skojarzonych z ofertą. Aby utworzyć nową jednostkę SKU, wykonaj następujące kroki:

1. Na karcie Jednostki SKU wybierz pozycję **Nowa jednostka SKU**

   ![Nowy monit sku](./media/containers-sku-settings.png)

2. Podaj wymagane informacje o jednostce SKU i kontenerze. Każda jednostka SKU odpowiada obrazowi kontenera. Istnieją dwie części do jednostki SKU:

    -   Metadane jednostki SKU
    -   Metadane kontenera


### <a name="sku-metadata"></a>Metadane jednostki SKU

Metadane jednostki SKU zawiera informacje wyświetlane w witrynie sklepu dla listy kontenerów.

![Metadane jednostki SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadane kontenera

Metadane kontenera zawiera informacje referencyjne szczegółów repozytorium obrazów wewnątrz rejestru kontenerów platformy Azure (ACR). Usługa Azure Marketplace kopiuje ten obraz do rejestru publicznego specyficznego dla portalu Marketplace, a następnie udostępnia obraz klientom po certyfikacji. Wszystkie żądania od użytkownika platformy Azure do korzystania z obrazu kontenera portalu Azure Marketplace są obsługiwane z rejestru publicznego portalu Marketplace, a nie z usługi ACR.

![Metadane kontenera](./media/containers-image-repository.png)
    
**Szczegóły repozytorium obrazów** w poprzednim przechwycie ekranu zawiera następujące pola.  Wymagane pola są oskarżane gwiazdką (*).

-   **Identyfikator\* subskrypcji** — identyfikator subskrypcji platformy Azure, w którym znajduje się usługa ACR.
-   **Nazwa\* grupy zasobów** — nazwa grupy zasobów usługi ACR.
-   **Nazwa\* rejestru** — nazwa ACR.
-   **Nazwa\* repozytorium** — nazwa repozytorium. Po ustawieniu tej nazwy tej wartości nie można zmienić. Użyj unikatowej nazwy, aby uniknąć konfliktu z innymi ofertami na koncie.
-   **Nazwa\* użytkownika** — nazwa użytkownika (nazwa użytkownika administratora) skojarzona z obrazem ACR.
-   **Hasło\* ** — hasło skojarzone z obrazem ACR.

    >[!NOTE]
    >Nazwa użytkownika i hasło są wymagane, aby upewnić się, że partnerzy mają dostęp do acr wymienionych w procesie publikowania.


### <a name="image-version"></a>Wersja obrazu

Podczas publikowania obrazu kontenera można podać jeden lub więcej znaczników obrazu i skróty SHA.

**Znacznik\* lub skrót obrazu**
 
- Ten tag lub skrót `latest` musi zawierać znacznik i tag `xx.xx.xx-` wersji (na przykład, począwszy od tego, gdzie xx jest liczbą). Powinny one być [manifest tagi](https://github.com/estesp/manifest-tool) do kierowania na wiele platform. Wszystkie tagi, do których odwołuje się znacznik manifestu, muszą również zostać dodane, abyśmy mogli je przesłać. 
- Można dodać kilka wersji kontenera za pomocą tagów. Wszystkie znaczniki manifestu (z wyjątkiem) `latest`muszą zaczynać się od jednego `X.Y-` lub `X.Y.Z-` miejsca, w którym X, Y, Z są liczby całkowite. <br/> Na przykład, `latest` jeśli znacznik `1.0.1-linux-x64` `1.0.1-linux-arm32`wskazuje `1.0.1-windows-arm32`, i , te tagi muszą być dodane w tym miejscu.

>[!NOTE]
>Pamiętaj, aby dodać **znacznik testowy** do obrazu, aby można było zidentyfikować obraz podczas testowania.


## <a name="next-steps"></a>Następne kroki

Użyj [karty Marketplace,](./cpp-marketplace-tab.md) aby utworzyć opis witryny dla swojej oferty. 
