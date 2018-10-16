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
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324175"
---
Kontrola dostępu oparta na rolach jest strategia zabezpieczeń opartych na dziedziczenie zarządzania dostępu, uprawnień i ról. Role podrzędny dziedziczą uprawnienia z nadrzędnego ról. Może również przypisane uprawnienia nie są dziedziczone z roli nadrzędnej. Mogą one również przypisane do roli dostosowywać, odpowiednio do potrzeb.

Na przykład **administratora miejsca** może wymagać globalny dostęp do uruchomienia wszystkie operacje dla określonego odstęp (w tym wszystkie węzły poniżej lub znajdujący się w nim), natomiast **Instalator urządzenia** tylko może być konieczne *odczytu* i *aktualizacji* uprawnienia dla urządzeń i czujników.

W każdym przypadku może być przyznany role **dokładnie i nie więcej niż wymagany dostęp do** do zrealizowania ich zadań na **zasadę najmniejszych uprawnień**, przyznać tożsamości *tylko*:

* Ilość dostępu wymagane do wykonania swojego zadania.
* Rola, odpowiedni i ograniczone do wykonywania swoich zadań.

>[!IMPORTANT]
> **Zawsze postępuj zgodnie z zasadą najniższych uprawnień**.

Dwie inne ważne kontroli dostępu opartej na rolach praktyki do naśladowania:

> [!div class="checklist"]
> * Przeprowadzaj okresowe inspekcje przypisań ról, aby sprawdzić, czy każda rola ma odpowiednie uprawnienia.
> * Ról i przypisań powinny zostać oczyszczony po zmianie użytkowników indywidualnych, ról lub przypisania.