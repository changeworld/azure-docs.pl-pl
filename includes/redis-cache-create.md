---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: f059f23031c2cdd74daaa856213d7e06f87dc27c
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273918"
---
1. Aby utworzyć pamięć podręczną, najpierw zaloguj się w [witrynie Azure Portal](https://portal.azure.com). Następnie wybierz pozycję **Utwórz zasób** > **Bazy danych** > **Azure Cache for Redis**.

    ![Nowa pamięć podręczna Azure dla menu Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. W polu **Nowa pamięć podręczna Azure Cache for Redis** skonfiguruj ustawienia nowej pamięci podręcznej.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa DNS** | Nazwa unikatowa w skali globalnej | Nazwa pamięci podręcznej. Musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery oraz znak `-`. Na początku ani na końcu nazwy pamięci podręcznej nie może występować znak `-`, a następujące po sobie znaki `-` nie są prawidłowe.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzone nowe wystąpienie pamięci podręcznej Azure Cache for Redis. | 
    | **Grupa zasobów** |  *TestResources* | Nazwa nowej grupy zasobów, w której ma zostać utworzona pamięć podręczna. Dzięki wprowadzeniu wszystkich zasobów dla aplikacji do grupy można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów skojarzonych z aplikacją. | 
    | **Location** | East US | Wybierz [region](https://azure.microsoft.com/regions/) w pobliżu innych usług, które będą korzystać z pamięci podręcznej. |
    | **[Warstwa cenowa](https://azure.microsoft.com/pricing/details/cache/)** |  Podstawowa C0 (250 MB pamięci podręcznej) |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |
    | **Przypnij do pulpitu nawigacyjnego** |  Wybrane | Przypnij nową pamięć podręczną do pulpitu nawigacyjnego, aby było łatwo ją znaleźć. |

    ![Tworzenie pamięci podręcznej platformy Azure dla usługi Redis](media/redis-cache-create/redis-cache-cache-create.png) 

3. Po skonfigurowaniu nowych ustawień pamięci podręcznej wybierz pozycję **Utwórz**. 

    Tworzenie pamięci podręcznej może zająć kilka minut. Stan operacji można sprawdzić, monitorując postęp na pulpicie nawigacyjnym. Po utworzeniu pamięci podręcznej ma ona stan **Uruchomiona** i jest gotowa do użycia.

    ![Utworzono pamięć podręczną platformy Azure dla Redis](media/redis-cache-create/redis-cache-cache-created.png)

