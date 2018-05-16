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
ms.openlocfilehash: 55bc2bd4e065c301f11a5fc4d3b58aa443b83e2d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
Aby utworzyć pamięć podręczną, najpierw zaloguj się w witrynie [Azure Portal](https://portal.azure.com), a następnie kliknij kolejno opcje **Utwórz zasób** > **Bazy danych** > **Redis Cache**.

![Nowa pamięć podręczna](media/redis-cache-create/redis-cache-new-cache-menu.png)

Skonfiguruj ustawienia nowej pamięci podręcznej w polu **Nowa pamięć podręczna Redis**.

| Ustawienie      | Sugerowana wartość  | Opis |
| ------------ |  ------- | -------------------------------------------------- |
| **Nazwa DNS** | Nazwa unikatowa w skali globalnej | Nazwa pamięci podręcznej musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery i znak `-`. Na początku ani na końcu nazwy pamięci podręcznej nie może występować znak `-`, a następujące po sobie znaki `-` nie są prawidłowe.  | 
| **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona nowa pamięć podręczna Azure Redis Cache. | 
| **Grupa zasobów** |  *TestResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona pamięć podręczna. Dzięki wprowadzeniu wszystkich zasobów dla aplikacji do grupy można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów spowodowałoby usunięcie wszystkich zasobów skojarzonych z aplikacją. | 
| **Lokalizacja** | Wschodnie stany USA | Wybierz [region](https://azure.microsoft.com/regions/) w pobliżu innych usług, które będą korzystać z pamięci podręcznej. |
| **[Warstwa cenowa](https://azure.microsoft.com/pricing/details/cache/)** |  Podstawowa C0 (250 MB pamięci podręcznej) |  Warstwa cenowa określa rozmiar, wydajność i funkcje dostępne dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Redis Cache](../articles/redis-cache/cache-overview.md). |
| **Przypnij do pulpitu nawigacyjnego** |  Wybrano | Kliknij przycisk Przypnij nową pamięć podręczną do pulpitu nawigacyjnego, aby ułatwić sobie jej znajdowanie. |

![Tworzenie pamięci podręcznej](media/redis-cache-create/redis-cache-cache-create.png) 

Po skonfigurowaniu nowych ustawień pamięci podręcznej kliknij przycisk **Utwórz**. 

Tworzenie pamięci podręcznej może zająć kilka minut. Stan operacji można sprawdzić, monitorując postęp na pulpicie nawigacyjnym. Po utworzeniu pamięci podręcznej nowa pamięć podręczna ma stan **Uruchomiona** i jest gotowa do użycia.

![Utworzono pamięć podręczną](media/redis-cache-create/redis-cache-cache-created.png)

