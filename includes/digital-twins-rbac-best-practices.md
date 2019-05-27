---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/28/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: e81b8fb06240d566e46ca0b45a0910e014dee329
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119028"
---
Kontrola dostępu oparta na rolach jest strategia zabezpieczeń opartych na dziedziczenie zarządzania dostępu, uprawnień i ról. Role podrzędny dziedziczą uprawnienia z nadrzędnego ról. Ponadto można przypisać uprawnienia nie są dziedziczone z roli nadrzędnej. Mogą również można przypisać do roli dostosowywać, odpowiednio do potrzeb.

Na przykład Administrator miejsca może być konieczne globalny dostęp do uruchomienia wszystkie operacje dla określonego obszaru. Uwzględniane są wszystkie węzły poniżej lub w obrębie przestrzeni. Instalator urządzenia może być konieczne tylko *odczytu* i *aktualizacji* uprawnienia dla urządzeń i czujników.

W każdym przypadku rolom nadaje się *dokładnie i nie więcej niż wymagany dostęp do* do zrealizowania ich zadań na zasadzie najmniejszych uprawnień. Zgodnie z tą zasadą otrzymuje tożsamość *tylko*:

* Ilość dostępu wymagane do wykonania swojego zadania.
* Rola, odpowiedni i ograniczone do wykonywania swoich zadań.

>[!IMPORTANT]
> Zawsze postępuj zgodnie z zasadą najniższych uprawnień.

Dwie inne ważne opartej na rolach kontroli dostępu w praktyki do naśladowania:

> [!div class="checklist"]
> * Przeprowadzaj okresowe inspekcje przypisań ról, aby sprawdzić, czy każda rola ma odpowiednie uprawnienia.
> * Wyczyść ról i przypisań użytkowników indywidualnych zmiany ról lub przypisania.