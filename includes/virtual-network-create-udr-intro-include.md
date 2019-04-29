---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 482241deb1081ac8a5265a076eabbdc3fb6d659e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743395"
---
Chociaż korzystanie z tras systemowych automatycznie umożliwia ruch sieciowy we wdrożeniu, istnieją przypadki, w których użytkownik chce sterować przekazywaniem pakietów przez urządzenia wirtualne. Można to zrobić, tworząc trasy definiowane przez użytkownika, które wskazują kolejny krok na drodze pakietu do określonej podsieci, aby zamiast tego przeszły do urządzeń wirtualnych, i włączając funkcję przesyłania dalej IP dla maszyny wirtualnej uruchomionej jako urządzenie wirtualne.

Oto niektóre z przypadków użycia wirtualnych urządzeń sieciowych:

* Monitorowanie ruchu przy użyciu systemu wykrywania nieautoryzowanego dostępu (ID)
* Kontrolowanie ruchu za pomocą zapory

Aby uzyskać więcej informacji na temat przekazywania trasy zdefiniowanej przez użytkownika i adres IP, odwiedź stronę [tras zdefiniowanych przez użytkownika i przekazywanie adresów IP](../articles/virtual-network/virtual-networks-udr-overview.md).