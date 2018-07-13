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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38941720"
---
Otwieranie portu lub utworzenie punktu końcowego, na maszynę wirtualną (VM) na platformie Azure przez utworzenie filtru sieciowego w podsieci lub interfejsu sieciowego maszyny Wirtualnej. Te filtry, które kontroli ruchu przychodzącego i wychodzącego, możesz umieścić na sieciowej grupy zabezpieczeń, który jest dołączony do zasobu, który odbiera ruch.

Użyjmy typowym przykładem ruchu w sieci web na porcie 80. Po utworzeniu maszyny Wirtualnej, który jest skonfigurowany do obsługi żądań sieci web protokołu TCP standardowego portu 80 (Pamiętaj, aby uruchomić odpowiednie usługi i otworzyć odpowiednie reguły zapory systemu operacyjnego na maszynie Wirtualnej również), możesz:

1. Utwórz sieciową grupę zabezpieczeń.
2. Utwórz regułę ruchu przychodzącego, która zezwala na ruch:
   * zakres portów docelowych "80"
   * źródłowy zakres portów elementu "*" (umożliwiając, dowolnego portu źródłowego)
   * wartość priorytetu mniej 65,500 (w celu wyższy priorytet niż przechwytującą cały domyślne można odmówić reguły dla ruchu przychodzącego)
3. Kojarzenie sieciowej grupy zabezpieczeń przy użyciu interfejsu sieciowego maszyny Wirtualnej lub podsieci.

Można tworzyć konfiguracje sieci złożone, aby zabezpieczyć środowisku przy użyciu sieciowych grup zabezpieczeń i reguł. Przedstawiony przykład używa tylko jedną lub dwie reguły, które zezwalają na ruch protokołu HTTP lub zdalnego zarządzania. Aby uzyskać więcej informacji, zobacz ["Więcej informacji"](#more-information-on-network-security-groups) sekcji lub [co to jest sieciowa grupa zabezpieczeń?](../articles/virtual-network/security-overview.md)

