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
ms.openlocfilehash: 6a96bd7c3d7d02f181a7d7513edb3bb39881274f
ms.sourcegitcommit: 7de1432648c4ff3bcd09530c079418477d9f4d00
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35719434"
---
1. Aby utworzyć pamięć podręczną, najpierw zaloguj się w [witrynie Azure Portal](https://portal.azure.com). Następnie wybierz pozycje **Utwórz zasób** > **Bazy danych** > **Redis Cache**.

    ![Nowa pamięć podręczna](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. Skonfiguruj ustawienia nowej pamięci podręcznej w polu **Nowa pamięć podręczna Redis**.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa DNS** | Nazwa unikatowa w skali globalnej | Nazwa pamięci podręcznej. Musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery oraz znak `-`. Na początku ani na końcu nazwy pamięci podręcznej nie może występować znak `-`, a następujące po sobie znaki `-` nie są prawidłowe.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzone nowe wystąpienie pamięci podręcznej Azure Redis Cache. | 
    | **Grupa zasobów** |  *TestResources* | Nazwa nowej grupy zasobów, w której ma zostać utworzona pamięć podręczna. Dzięki wprowadzeniu wszystkich zasobów dla aplikacji do grupy można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów skojarzonych z aplikacją. | 
    | **Lokalizacja** | Wschodnie stany USA | Wybierz [region](https://azure.microsoft.com/regions/) w pobliżu innych usług, które będą korzystać z pamięci podręcznej. |
    | **[Warstwa cenowa](https://azure.microsoft.com/pricing/details/cache/)** |  Podstawowa C0 (250 MB pamięci podręcznej) |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Redis Cache](../articles/redis-cache/cache-overview.md). |
    | **Przypnij do pulpitu nawigacyjnego** |  Wybrano | Przypnij nową pamięć podręczną do pulpitu nawigacyjnego, aby było łatwo ją znaleźć. |

    ![Tworzenie pamięci podręcznej](media/redis-cache-create/redis-cache-cache-create.png) 

3. Po skonfigurowaniu nowych ustawień pamięci podręcznej wybierz pozycję **Utwórz**. 

    Tworzenie pamięci podręcznej może zająć kilka minut. Stan operacji można sprawdzić, monitorując postęp na pulpicie nawigacyjnym. Po utworzeniu pamięci podręcznej ma ona stan **Uruchomiona** i jest gotowa do użycia.

    ![Utworzono pamięć podręczną](media/redis-cache-create/redis-cache-cache-created.png)

