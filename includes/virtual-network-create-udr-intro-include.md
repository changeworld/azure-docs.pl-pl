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
ms.openlocfilehash: 226dfd9add69e8d89a030b858c819691d7b20627
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
Chociaż korzystanie z tras systemowych automatycznie umożliwia ruch sieciowy we wdrożeniu, istnieją przypadki, w których użytkownik chce sterować przekazywaniem pakietów przez urządzenia wirtualne. Można to zrobić, tworząc trasy definiowane przez użytkownika, które wskazują kolejny krok na drodze pakietu do określonej podsieci, aby zamiast tego przeszły do urządzeń wirtualnych, i włączając funkcję przesyłania dalej IP dla maszyny wirtualnej uruchomionej jako urządzenie wirtualne.

Oto niektóre przypadków, w których można używać urządzeń wirtualnych:

* Monitorowanie ruchu przy użyciu systemu wykrywania nieautoryzowanego dostępu (ID)
* Kontrolowanie ruchu z zaporą

Aby uzyskać więcej informacji na temat przekazywania przez i adres IP, odwiedź stronę [trasy zdefiniowane przez użytkownika i przesyłania dalej protokołu IP](../articles/virtual-network/virtual-networks-udr-overview.md).

