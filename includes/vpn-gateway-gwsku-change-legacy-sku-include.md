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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183111"
---
Jeśli pracujesz z modelu wdrażania usługi Resource Manager, można zmienić do nowej jednostki SKU bramy. Po zmianie ze starszej wersji jednostki SKU bramy do nowej jednostki SKU, usuń istniejącą bramę sieci VPN i utworzyć nową bramę sieci VPN.

Przepływ pracy:

1. Usuń wszystkie połączenia z bramą sieci wirtualnej.
2. Usuń starą bramę sieci VPN.
3. Utwórz nową bramę sieci VPN.
4. Zaktualizuj lokalne urządzenia sieci VPN do nowego adresu IP bramy sieci VPN (dla połączeń typu lokacja-lokacja).
5. Zaktualizuj wartość adresu IP bramy dla wszystkich bram sieci lokalnej połączeń między sieciami wirtualnymi, które będą łączyć się z tą bramą.
6. Pobierz nowe pakiety konfiguracji sieci VPN klienta dla klientów połączeń punkt-lokacja nawiązujących połączenie z siecią wirtualną przez tę bramę sieci VPN.
7. Odtwórz połączenia z bramą sieci wirtualnej.

Zagadnienia do rozważenia:

* Aby przejść do nowych jednostek SKU, bramy sieci VPN musi być w modelu wdrażania usługi Resource Manager.
* W przypadku klasycznego bramy sieci VPN należy kontynuować, przy użyciu starszych starszych jednostek SKU dla tej bramy, jednak można zmienić rozmiar starszych jednostek SKU. Nie można zmienić nowych jednostek SKU.
* Będziesz mieć czas przestoju łączności, gdy zmienią się ze starszej wersji jednostki SKU nowej jednostki SKU.
* Podczas zmiany do nowej jednostki SKU bramy, publiczny adres IP dla bramy sieci VPN ulegnie zmianie. Dzieje się tak, nawet w przypadku określenia tego samego obiektu publicznego adresu IP, co użyte wcześniej.