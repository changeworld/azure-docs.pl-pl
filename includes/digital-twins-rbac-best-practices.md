---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: a03f2b4e8d216db3764af03dc06b5188289ffc92
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50964207"
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