---
title: Wyjaśniający obliczeniowe jednostki w bazie danych systemu Azure dla PostgreSQL | Dokumentacja firmy Microsoft
description: 'Bazę danych systemu Azure dla PostgreSQL: w tym artykule opisano pojęcia obliczeniowe jednostki i co się dzieje, gdy obciążenie osiągnie maksymalną liczbę jednostek obliczeniowe.'
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: dbb9f733455fa0492358b24b178c8c637ff08c71
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678420"
---
# <a name="explaining-compute-units-in-azure-database-for-postgresql"></a>Wyjaśniający dla PostgreSQL jednostki obliczeń w bazie danych Azure
Ten temat wyjaśnia pojęcie jednostki obliczeniowe i co się dzieje, gdy obciążenie osiągnie maksymalny poziom obliczeniowe jednostki.

## <a name="what-are-compute-units"></a>Co to są jednostki obliczeniowe?
Obliczenia bazy danych jednostki to miara przepływności przetwarzania procesora CPU, który może być dostępne dla pojedynczej bazy danych Azure PostgreSQL serwera. Do obliczenia jest mieszanych pomiarach procesora CPU i zasobów pamięci. Ogólnie rzecz biorąc 50 jednostek obliczeniowe są równoważne połowy rdzenia. 100 jednostek obliczeniowe są równoważne jednego rdzenia. 2000 jednostki obliczeniowe są równoważne 20 rdzenie przepływności gwarantowane przetwarzanie dostępne na serwerze.

Ilość pamięci na jednostkę obliczeniowe jest zoptymalizowana pod kątem Basic i Standard warstw cenowych. Podwojenie jednostki obliczeniowe po podniesieniu poziomu wydajności jest równa podwojenie ilości zasobów Procesora i pamięci do tego pojedynczej bazy danych Azure dla PostgreSQL.

Na przykład standardowe jednostki obliczeniowe 800 obejmuje 8 x więcej przepływności procesora CPU i pamięci niż konfiguracji jednostek Standard obliczeniowe 100. Jednak gdy jednostek Standard obliczeniowe 100 Podaj samej przepływności procesora CPU, tak jak w przypadku podstawowej jednostki obliczeniowe 100, ilość pamięci, wstępnie skonfigurowanego w warstwie cenowej standardowa jest double ilość pamięci skonfigurowanej dla podstawowego warstwy cenowej. W związku z tym warstwa cenowa standardowa zapewnia lepszą wydajność obciążeń i mniejsze opóźnienia transakcji niż podstawowa warstwa cenowa z tej samej jednostki obliczeniowe wybrane.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Jak ustalić liczbę jednostek obliczeniowe potrzebne do mojego obciążenie?
Jeśli chcesz przeprowadzić migrację istniejącego serwera PostgreSQL uruchamiane lokalnie lub na maszynie wirtualnej można określić liczbę jednostek obliczeniowe szacowaną liczbę rdzeni przepływności przetwarzania obciążenie musi. 

Jeśli z istniejącym lokalnym lub serwerze maszyny wirtualnej jest obecnie przy użyciu 4 rdzenie (bez zliczanie hiperwątkowości Procesora), należy uruchomić konfigurując 400 jednostek obliczeniowe PostgreSQL serwera bazy danych Azure. Jednostki obliczeniowe można dynamicznie skalować w górę lub w dół w zależności od potrzeb obciążenia praktycznie bez przestojów aplikacji. 

Monitorowanie wykres metryki w portalu Azure lub tworzenia poleceń wiersza polecenia platformy Azure do mierzenia obliczeniowe jednostki. Odpowiednich metryk do monitorowania są procent obliczeniowe jednostki i limit obliczeniowe jednostki.

>[!IMPORTANT]
> Odnalezienie magazynu, który IOPS nie mogą być pełni wykorzystane do maksymalnej należy rozważyć monitorowanie w wykorzystaniu obliczeniowe jednostki. Wywoływanie jednostki obliczeniowe mogą zezwalać na wyższej przepustowości we/wy przez zmniejszenie wąskie gardło z powodu ograniczonych procesora CPU lub pamięci.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Co się stanie po naciśnięciu I Moje maksymalną liczbę jednostek obliczeniowe?
Poziomy wydajności są kalibrować i zarządzane w celu zapewnienia zasobów, aby uruchomić obciążenie bazy danych w granicach maksymalnej dla wybranych cenową warstwę i poziom wydajności. 

Jeśli obciążenie osiągnie maksymalną wartością obliczeniowe jednostki lub elastycznie IOPS, można nadal korzystać z zasobów na maksymalny dozwolony poziom, ale prawdopodobnie zapytań zobaczyć zwiększenie opóźnienia. Te limity nie powodują błędy, ale raczej spowolnienie w obciążeniu, chyba że spowolnienie staje się tak poważne, który odpytuje upłynął limit czasu. 

Jeśli obciążenie osiągnie limity maksymalną liczbę połączeń, pojawienia się jawne błędy. Aby uzyskać więcej informacji na ograniczenia zasobów, zobacz [ograniczeń w bazie danych Azure PostgreSQL](concepts-limits.md).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o cenach warstw, zobacz [bazą danych Azure dla PostgreSQL warstw cenowych](./concepts-service-tiers.md).
