---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: d25f6f0d787b7343b93025ff2dd2bd5bf4f0f6d8
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948942"
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