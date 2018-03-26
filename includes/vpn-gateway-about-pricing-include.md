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
ms.openlocfilehash: 205c67377e4bff66c02e3ee508c0f6f4e2823608
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
Płatność dotyczy dwóch składników: kosztu godzin obliczeniowych bramy sieci wirtualnej i transferu danych wychodzących z bramy sieci wirtualnej. Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

**Koszty obliczeniowe bramy sieci wirtualnej**<br>Każda brama sieci wirtualnej ma koszt godziny obliczeniowej. Cena zależy od jednostki SKU bramy, która została określona podczas tworzenia bramy sieci wirtualnej. Koszt dotyczy samej bramy i jest dodawany do kosztu transferu danych, który przepływa przez bramę.

**Koszty transferu danych**<br>Koszty transferu danych są obliczane na podstawie ruchu wychodzącego ze źródłowej bramy sieci wirtualnej.

* Jeśli ruch jest wysyłany do lokalnego urządzenia sieci VPN, zostanie naliczona stawka za transfer danych wychodzących do sieci Internet.
* Jeśli ruch jest wysyłany między sieciami wirtualnymi w różnych regionach, cena zależy od regionu.
* Jeśli ruch jest wysyłany tylko między sieciami wirtualnymi w tym samym regionie, nie ma żadnych kosztów danych. Ruch między sieciami wirtualnymi w tym samym regionie jest bezpłatny.