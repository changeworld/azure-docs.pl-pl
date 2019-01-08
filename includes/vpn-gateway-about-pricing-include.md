---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ae36adc78d8c87d85c64fd61cb3a50dfcae60b85
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995752"
---
Płatność dotyczy dwóch składników: kosztu godzin obliczeniowych bramy sieci wirtualnej i transferu danych wychodzących z bramy sieci wirtualnej. Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

**Koszty obliczeniowe bramy sieci wirtualnej**<br>Każda brama sieci wirtualnej ma koszt godziny obliczeniowej. Cena zależy od jednostki SKU bramy, która została określona podczas tworzenia bramy sieci wirtualnej. Koszt dotyczy samej bramy i jest dodawany do kosztu transferu danych, który przepływa przez bramę. Koszt konfiguracji aktywna-aktywna jest taka sam jak konfiguracji aktywna-pasywna.

**Koszty transferu danych**<br>Koszty transferu danych są obliczane na podstawie ruchu wychodzącego ze źródłowej bramy sieci wirtualnej.

* Jeśli ruch jest wysyłany do lokalnego urządzenia sieci VPN, zostanie naliczona stawka za transfer danych wychodzących do sieci Internet.
* Jeśli ruch jest wysyłany między sieciami wirtualnymi w różnych regionach, cena zależy od regionu.
* Jeśli ruch jest wysyłany tylko między sieciami wirtualnymi w tym samym regionie, nie ma żadnych kosztów danych. Ruch między sieciami wirtualnymi w tym samym regionie jest bezpłatny.