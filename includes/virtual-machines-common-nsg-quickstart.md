---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888561"
---
Otwieranie portu lub utworzenie punktu końcowego, na maszynę wirtualną (VM) na platformie Azure przez utworzenie filtru sieci dla podsieci lub interfejsu sieciowego maszyny Wirtualnej. Te filtry, które kontroli ruchu przychodzącego i wychodzącego, możesz umieścić na sieciowej grupy zabezpieczeń, który jest dołączony do zasobu, który odbiera ruch.

W przykładzie w tym artykule pokazano, jak utworzyć filtr sieci, który używa standardowego portu TCP 80 (zakłada się już do odpowiednich usług i otworzyć odpowiednie reguły zapory systemu operacyjnego na maszynie Wirtualnej).

Po utworzeniu maszyny Wirtualnej, który jest skonfigurowany do obsługi żądań sieci web na standardowy port TCP 80, możesz wykonywać następujące czynności:

1. Utwórz sieciową grupę zabezpieczeń.

2. Tworzenie reguły zabezpieczeń dla ruchu przychodzącego zezwalającej na ruch i przypisać wartości do następujących ustawień:

   - **Docelowe zakresy portów**: 80

   - **Źródłowe zakresy portów**: * (umożliwia dowolnego portu źródłowego)

   - **Wartość priorytetu**: wprowadź wartość, która jest mniejsza niż 65,500 i wyższy priorytet niż domyślna wychwytywania Odmów reguły dla ruchu przychodzącego.

3. Kojarzenie sieciowej grupy zabezpieczeń przy użyciu interfejsu sieciowego maszyny Wirtualnej lub podsieci.

Mimo że w tym przykładzie używa prostej reguły, aby zezwolić na ruch HTTP, umożliwia także sieciowych grup zabezpieczeń i reguł do utworzenia bardziej złożonych konfiguracji sieci. 




