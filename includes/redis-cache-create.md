---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73719098"
---
1. Aby utworzyć pamięć podręczną, zaloguj się do [witryny Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**. 
   
   ![Wybieranie opcji Utwórz zasób](media/redis-cache-create/create-a-resource.png)
   
1. Na stronie **Nowy** wybierz pozycję **Bazy danych,** a następnie wybierz pozycję **Azure Cache for Redis**.
   
   ![Wybierz pamięć podręczną platformy Azure dla redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Na stronie **Nowa pamięć podręczna Redis** skonfiguruj ustawienia nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać kolejnych łączników. *Nazwa hosta* wystąpienia pamięci podręcznej będzie * \<nazwą DNS>.redis.cache.windows.net*. | 
   | **Subskrypcja** | Rozwijana i wybierz subskrypcję. | Subskrypcja, w ramach której można utworzyć tę nową usługę Azure Cache dla wystąpienia Redis. | 
   | **Grupa zasobów** | Rozwijana i wybierz grupę zasobów lub wybierz pozycję **Utwórz nowe** i wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma być utworzona pamięć podręczna i inne zasoby. Umieszczając wszystkie zasoby aplikacji w jednej grupie zasobów, można łatwo zarządzać nimi lub usuwać je razem. | 
   | **Lokalizacja** | Rozwijana i wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) w pobliżu innych usług, które będą używać pamięci podręcznej. |
   | **Warstwa cenowa** | Rozwijana i wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Wybierz **pozycję Utwórz**. 
   
   ![ Tworzenie pamięci podręcznej Azure Cache for Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Trwa trochę czasu dla pamięci podręcznej do utworzenia. Można monitorować postęp na **stronie** Przegląd usługi Azure Cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony,** pamięć podręczna jest gotowa do użycia.
   
   ![Utworzona usługa Azure Cache for Redis](media/redis-cache-create/redis-cache-cache-created.png)

