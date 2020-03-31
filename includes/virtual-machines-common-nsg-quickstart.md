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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183201"
---
Otwórz port lub utworzyć punkt końcowy na maszynie wirtualnej (VM) na platformie Azure, tworząc filtr sieciowy w podsieci lub interfejsie sieciowym maszyny wirtualnej. Te filtry, które kontrolują ruch przychodzący i wychodzący, umieszcza się w sieciowej grupie zabezpieczeń dołączonej do zasobu odbieranego przez ruch.

W tym przykładzie w tym artykule pokazano, jak utworzyć filtr sieciowy, który używa standardowego portu TCP 80 (zakłada się, że już uruchomiono odpowiednie usługi i otwarto wszystkie reguły zapory systemu operacyjnego na maszynie Wirtualnej).

Po utworzeniu maszyny Wirtualnej skonfigurowanego do obsługi żądań sieci web na standardowym porcie TCP 80 można:

1. Utwórz sieciową grupę zabezpieczeń.

2. Utwórz regułę zabezpieczeń ruchu przychodzącego zezwalającą na ruch i przypisz wartości do następujących ustawień:

   - **Zakresy portów docelowych:** 80

   - **Zakresy portów źródłowych**: * (umożliwia dowolny port źródłowy)

   - **Wartość priorytetu:** Wprowadź wartość o priorytecie mniejszej niż 65 500 i wyższą niż domyślna reguła odmowy przychodzących.

3. Skojarz grupę zabezpieczeń sieci z interfejsem sieciowym lub podsiecią maszyny Wirtualnej.

Chociaż w tym przykładzie użyto prostej reguły zezwalania na ruch HTTP, można również użyć grup zabezpieczeń sieciowych i reguł do tworzenia bardziej złożonych konfiguracji sieci. 




