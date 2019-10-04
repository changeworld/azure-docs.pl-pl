---
title: plik dołączany
description: plik dołączany
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1c8237ce4e8b758395567e939c1ed4bda1f297ff
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827670"
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