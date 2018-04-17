---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 09c6871fc5243296da2f2defd594afb80c62ac95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scenariusz
Aby lepiej zilustrować tworzenie grup NSG, w niniejszym dokumencie użyto następujący scenariusz:

![Scenariusz sieci wirtualnych](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

W tym scenariuszu utworzysz grupy NSG dla każdej podsieci w **TestVNet** sieci wirtualnej, w następujący sposób: 

* **Grupa NSG frontonu**. Frontonu grupa NSG jest stosowana do *frontonu* podsieci i zawiera dwie reguły:    
  * **Reguła RDP**. Zezwala na ruch RDP do *frontonu* podsieci.
  * **Reguła sieci Web**. Zezwala na ruch HTTP do *frontonu* podsieci.
* **Grupa NSG zaplecza**. Grupa NSG zaplecza jest stosowana do *zaplecza* podsieci i zawiera dwie reguły:    
  * **Reguła SQL**. Zezwala na ruch SQL tylko z *frontonu* podsieci.
  * **Reguła sieci Web**. Nie zezwala na wszystkie internet powiązany ruch z *zaplecza* podsieci.

Kombinacja tych reguł tworzenie scenariusza przypominającej DMZ, gdzie podsieci zaplecza może odbierać ruchu przychodzącego dla serwera SQL z podsieci frontonu i nie ma dostępu do Internetu, gdy podsieci frontonu komunikacji z Internetem i otrzymywać przychodzące żądania HTTP tylko.

