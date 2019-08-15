---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: fa88bf7f8db9b4fea63429dbfd18f84ee84ccda1
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012231"
---
Kontrola dostępu oparta na rolach jest strategią zabezpieczeń opartą na dziedziczeniu służącą do zarządzania dostępem, uprawnieniami i rolami. Role elementów potomnych dziedziczą uprawnienia z ról nadrzędnych. Uprawnienia można także przypisać bez dziedziczenia z roli nadrzędnej. Mogą być również przypisane do dostosowywania roli w razie potrzeby.

Na przykład administrator przestrzeni może potrzebować globalnego dostępu do uruchamiania wszystkich operacji w określonym miejscu. Dostęp obejmuje wszystkie węzły znajdujące się poniżej lub wewnątrz obszaru. Instalator urządzenia może potrzebować tylko uprawnień *odczytu* i *aktualizacji* dla urządzeń i czujników.

W każdym przypadku role są przyznawane *dokładnie i nie są wymagane* do realizacji zadań zgodnie z zasadą najniższych uprawnień. Zgodnie z tą zasadą tożsamość jest udzielana *tylko*:

* Ilość dostępu wymagana do ukończenia zadania.
* Rola odpowiednia i ograniczona do realizacji zadania.

>[!IMPORTANT]
> Zawsze stosuj zasadę najniższych uprawnień.

Dwie inne ważne praktyki kontroli dostępu opartej na rolach, które należy wykonać:

> [!div class="checklist"]
> * Okresowe inspekcje przypisań ról w celu sprawdzenia, czy każda rola ma odpowiednie uprawnienia.
> * Wyczyść role i przypisania, gdy użytkownicy zmieniają role lub przypisania.