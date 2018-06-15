---
title: Wyjaśniający obliczeniowe jednostki w bazie danych systemu Azure dla programu MySQL | Dokumentacja firmy Microsoft
description: 'Bazę danych systemu Azure dla programu MySQL: w tym artykule opisano pojęcia obliczeniowe jednostki i co się dzieje, gdy obciążenie osiągnie maksymalną liczbę jednostek obliczeniowe.'
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678448"
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Wyjaśniający jednostki obliczeń w bazie danych systemu Azure dla programu MySQL
Ten temat wyjaśnia pojęcie jednostki obliczeniowe i co się dzieje, gdy obciążenie osiągnie maksymalną liczbę jednostek obliczeniowe.

## <a name="what-are-compute-units"></a>Co to są jednostki obliczeniowe?
Obliczenia bazy danych jednostki to miara przepływności przetwarzania procesora CPU, który może być dostępne dla pojedynczej bazy danych Azure dla serwera MySQL. Do obliczenia jest mieszanych pomiarach procesora CPU i zasobów pamięci. Ogólnie rzecz biorąc 50 jednostek obliczeniowe są równoważne połowy rdzenia. 100 jednostek obliczeniowe są równoważne jednego rdzenia. 2000 jednostki obliczeniowe są równoważne 20 rdzenie przepływności gwarantowane przetwarzanie dostępne na serwerze.

Ilość pamięci na jednostkę obliczeniowe jest zoptymalizowana pod kątem Basic i Standard warstw cenowych. Podwojenie jednostki obliczeniowe po podniesieniu poziomu wydajności jest równa podwojenie zestaw zasobów dostępne dla tego pojedynczej bazy danych Azure dla programu MySQL.

Umożliwia na przykład standardowe jednostki obliczeniowe 800 x 8 więcej przepływności procesora CPU i pamięci niż konfiguracji jednostek Standard obliczeniowe 100. Jednak podczas standardowych 100 jednostek obliczeniowe Podaj samej przepływności procesora CPU w porównaniu do podstawowej jednostki obliczeniowe 100, ilość pamięci, wstępnie skonfigurowanego w warstwie cenowej standardowa jest double ilość pamięci skonfigurowanej dla podstawowego warstwy cenowej. W związku z tym warstwa cenowa standardowa zapewnia lepszą wydajność obciążeń i mniejsze opóźnienia transakcji niż podstawowa warstwa cenowa z tej samej jednostki obliczeniowe wybrane.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Jak ustalić liczbę jednostek obliczeniowe potrzebne do mojego obciążenie?
Jeśli chcesz przeprowadzić migrację istniejącego serwera MySQL uruchamiane lokalnie lub na maszynie wirtualnej można określić liczbę jednostek obliczeniowe szacowaną liczbę rdzeni przepływności przetwarzania wymaga obciążenie. 

Jeśli z istniejącym lokalnym lub serwerze maszyny wirtualnej jest obecnie używane 4 rdzenie (bez zliczanie hiperwątkowości Procesora), uruchom konfigurując 400 jednostek obliczeniowe bazy danych Azure, aby serwer MySQL. Jednostki obliczeniowe można skalować w górę lub w dół dynamicznie w zależności od potrzeb obciążenia i praktycznie bez przestojów aplikacji. 

Monitorowanie wykres metryki w portalu Azure lub tworzenia poleceń wiersza polecenia platformy Azure do mierzenia obliczeniowe jednostki. Odpowiednich metryk do monitorowania są procent obliczeniowe jednostki i limit obliczeniowe jednostki.

>[!IMPORTANT]
> Odnalezienie magazynu, który IOPS nie mogą być pełni wykorzystane do maksymalnej należy rozważyć monitorowanie w wykorzystaniu obliczeniowe jednostki. Wywoływanie jednostki obliczeniowe mogą zezwalać na wyższej przepustowości we/wy przez zmniejszenie wąskie gardło wynikające z ograniczoną procesora CPU lub pamięci.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Co się stanie po naciśnięciu I Moje maksymalną liczbę jednostek obliczeniowe?
Poziomy wydajności są kalibrować i zarządzane w celu zapewnienia zasobów, aby uruchomić obciążenie bazy danych w granicach maksymalnej dla wybranych cenową warstwę i poziom wydajności. 

Jeśli obciążenie osiągnie maksymalną wartością albo obliczeniowe jednostek lub elastycznie limity liczby, można nadal korzystać z zasobów na maksymalny dozwolony poziom, ale Twoje zapytania nie są prawdopodobnie mogą wystąpić opóźnienia zwiększona. Te limity spowodować spowolnienie obciążenia zamiast błędów, chyba że spowolnienie staje się tak poważne, który odpytuje upłynął limit czasu. 

Jeśli obciążenie osiągnie limity maksymalną liczbę połączeń, pojawienia się jawne błędy. Aby uzyskać więcej informacji na ograniczenia zasobów, zobacz [ograniczenia w bazie danych Azure dla programu MySQL](concepts-limits.md).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o cenach warstw, zobacz [bazy danych Azure dla programu MySQL warstw cenowych](./concepts-service-tiers.md).
