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
ms.openlocfilehash: 2c1a4a1931bc2e38b0bee5f90518b01fdf4767a1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "30196830"
---
Jeśli pracujesz z modelu wdrażania usługi Resource Manager, można zmienić na nowej jednostki SKU bramy. Po zmianie ze starszej wersji jednostki SKU bramy do nowej wersji produktu, usuń istniejącą bramę sieci VPN i Utwórz nową bramę sieci VPN.

Przepływu pracy:

1. Usuń wszystkie połączenia z bramą sieci wirtualnej.
2. Usuń starą bramę sieci VPN.
3. Utwórz nową bramę sieci VPN.
4. Zaktualizuj lokalne urządzenia sieci VPN do nowego adresu IP bramy sieci VPN (dla połączeń typu lokacja-lokacja).
5. Zaktualizuj wartość adresu IP bramy dla wszystkich bram sieci lokalnej połączeń między sieciami wirtualnymi, które będą łączyć się z tą bramą.
6. Pobierz nowe pakiety konfiguracji sieci VPN klienta dla klientów połączeń punkt-lokacja nawiązujących połączenie z siecią wirtualną przez tę bramę sieci VPN.
7. Odtwórz połączenia z bramą sieci wirtualnej.

Kwestie do rozważenia:

* Aby przejść do nowej wersji produktu, bramy sieci VPN musi być w modelu wdrażania usługi Resource Manager.
* Jeśli masz klasycznego bramy sieci VPN, należy nadal przy użyciu starszej starszej wersji jednostki SKU dla tej bramy, jednak można zmienić rozmiar między starszej wersji jednostki SKU. Nie można zmienić nowe jednostki SKU.
* Konieczne będzie przestoju łączności po zmianie ze starszej wersji produktu do nowej wersji.
* W przypadku zmiany do nowej bramy jednostka SKU, zmieni się publiczny adres IP dla bramy sieci VPN. Dzieje się tak nawet w przypadku określenia tej samej publicznego obiektu adres IP używany wcześniej.