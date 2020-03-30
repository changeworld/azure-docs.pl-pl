---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183111"
---
Jeśli pracujesz z modelem wdrażania Menedżera zasobów, możesz zmienić na nowe jednostki SKU bramy. Po zmianie z starszej jednostki SKU bramy na nową jednostkę SKU należy usunąć istniejącą bramę sieci VPN i utworzyć nową bramę sieci VPN.

Przepływu pracy:

1. Usuń wszystkie połączenia z bramą sieci wirtualnej.
2. Usuń starą bramę sieci VPN.
3. Utwórz nową bramę sieci VPN.
4. Zaktualizuj lokalne urządzenia sieci VPN do nowego adresu IP bramy sieci VPN (dla połączeń typu lokacja-lokacja).
5. Zaktualizuj wartość adresu IP bramy dla wszystkich bram sieci lokalnej połączeń między sieciami wirtualnymi, które będą łączyć się z tą bramą.
6. Pobierz nowe pakiety konfiguracji sieci VPN klienta dla klientów połączeń punkt-lokacja nawiązujących połączenie z siecią wirtualną przez tę bramę sieci VPN.
7. Odtwórz połączenia z bramą sieci wirtualnej.

Zagadnienia do rozważenia:

* Aby przejść do nowych jednostek SKU, brama sieci VPN musi znajdować się w modelu wdrażania Menedżera zasobów.
* Jeśli masz klasyczną bramę sieci VPN, musisz nadal używać starszych starszych jednostek SKU dla tej bramy, jednak można zmienić rozmiar między starszymi jednostkami SKU. Nie można zmienić na nowe jednostki SKU.
* Będziesz mieć przestoje łączności po zmianie z starszej jednostki SKU na nową jednostkę SKU.
* Po zmianie na nową jednostkę SKU bramy zmieni się publiczny adres IP bramy sieci VPN. Dzieje się tak, nawet jeśli określisz ten sam publiczny obiekt adresu IP, który był wcześniej używany.