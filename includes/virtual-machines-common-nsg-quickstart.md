---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34684986"
---
Otwarcie portu lub utworzyć punkt końcowy z maszyną wirtualną (VM) na platformie Azure, tworząc filtr sieci w podsieci lub interfejsu sieciowego maszyny Wirtualnej. Te filtry, które kontrolują ruchu przychodzącego i wychodzącego, można umieścić na grupę zabezpieczeń sieci dołączony do tego zasobu, który odbiera ruch.

Użyjmy typowym przykładem ruchu w sieci web na porcie 80. Po utworzeniu maszyny Wirtualnej, który jest skonfigurowany do obsługi żądań sieci web na standardowe TCP port 80 (Pamiętaj, aby uruchomić odpowiednie usługi i otworzyć reguł zapory systemu operacyjnego na maszynie Wirtualnej, a także), możesz:

1. Utwórz grupę zabezpieczeń sieci.
2. Utwórz regułę ruchu przychodzącego zezwala na ruch z:
   * zakres portów docelowych "80"
   * zakres portów źródłowych z "*" (dzięki czemu dowolnego portu źródłowego)
   * wartość priorytetu mniej 65,500 (aby wyższy priorytet niż catch wszystkie domyślne można odmówić reguły dla ruchu przychodzącego)
3. Skojarzenia sieciowej grupy zabezpieczeń z interfejsu sieciowego maszyny Wirtualnej lub podsieci.

Można utworzyć konfiguracji sieci złożonych zabezpieczeniu środowiska przy użyciu grup zabezpieczeń sieci i reguł. Przedstawiony przykład używa tylko jedna lub dwie reguły zezwalające na ruch HTTP lub do zdalnego zarządzania. Aby uzyskać więcej informacji, zobacz następujące tematy ["Więcej informacji"](#more-information-on-network-security-groups) sekcji lub [co to jest grupa zabezpieczeń sieci?](../articles/virtual-network/security-overview.md)

