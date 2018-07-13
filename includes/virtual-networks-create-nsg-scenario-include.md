---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38760364"
---
## <a name="scenario"></a>Scenariusz
Aby lepiej zilustrować tworzenie sieciowych grup zabezpieczeń, w tym dokumencie używa następujący scenariusz:

![Scenariusz sieci wirtualnych](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

W tym scenariuszu, Utwórz sieciową grupę zabezpieczeń dla każdej podsieci w **TestVNet** sieci wirtualnej, w następujący sposób: 

* **Sieciowa grupa zabezpieczeń frontonu**. Sieciowa grupa zabezpieczeń frontonu jest stosowany do *frontonu* podsieci i zawiera dwie reguły:    
  * **Reguła RDP**. Zezwala na ruch RDP do *frontonu* podsieci.
  * **Reguła sieci Web**. Zezwala na ruch HTTP do *frontonu* podsieci.
* **Sieciowa grupa zabezpieczeń zaplecza**. Serwer zaplecza w sieciowej grupie zabezpieczeń są stosowane do *zaplecza* podsieci i zawiera dwie reguły:    
  * **Reguła SQL**. Zezwala na ruch SQL tylko z *frontonu* podsieci.
  * **Reguła sieci Web**. Nie zezwala na cały internet powiązany ruch z *zaplecza* podsieci.

Kombinacja tych reguł tworzenie scenariusza DMZ podobne, gdzie podsieci zaplecza może odbierać ruchu przychodzącego dla programu SQL z podsieci frontonu i nie ma dostępu do Internetu, natomiast podsieci frontonu mogą komunikować się z Internetem i odbierania przychodzące żądania HTTP tylko.

