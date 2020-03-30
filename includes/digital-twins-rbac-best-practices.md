---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044925"
---
Kontrola dostępu oparta na rolach to oparta na dziedziczeniu strategia zabezpieczeń do zarządzania dostępem, uprawnieniami i rolami. Role potomne dziedziczą uprawnienia z ról nadrzędnych. Uprawnienia można również przypisać bez dziedziczenia z roli nadrzędnej. Można je również przypisać, aby dostosować rolę w razie potrzeby.

Na przykład administrator obszaru może potrzebować dostępu globalnego, aby uruchomić wszystkie operacje dla określonego miejsca. Dostęp obejmuje wszystkie węzły pod lub w obrębie przestrzeni. Instalator urządzeń może wymagać tylko uprawnień *do odczytu* i *aktualizacji* urządzeń i czujników.

W każdym przypadku role są przyznawane *dokładnie i nie więcej niż dostęp wymagany* do wykonania swoich zadań zgodnie z zasadą najmniejszych uprawnień. Zgodnie z tą zasadą tożsamość jest przyznawana *tylko:*

* Ilość dostępu potrzebnego do ukończenia zadania.
* Rola właściwa i ograniczona do wykonywania swojej pracy.

>[!IMPORTANT]
> Zawsze postępuj zgodnie z zasadą najmniejszych przywilejów.

Dwie inne ważne praktyki kontroli dostępu oparte na rolach do naśladowania:

> [!div class="checklist"]
> * Okresowo inspekcji przypisań ról, aby sprawdzić, czy każda rola ma poprawne uprawnienia.
> * Oczyść role i przypisania, gdy poszczególne osoby zmieniają role lub przypisania.