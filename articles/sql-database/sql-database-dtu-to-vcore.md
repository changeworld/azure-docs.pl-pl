---
title: Migrowanie z modelu jednostek DTU do modelu rdzeni wirtualnych
description: Migracja z modelu DTU do modelu vCore. Migracja do klasy wirtualnej jest podobna do uaktualniania lub obniżania poziomu między warstwami standard i premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945398"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migracja z modelu opartego na uju do modelu opartego na wynikach wirtualnych

## <a name="migrate-a-database"></a>Migrowanie bazy danych

Migrowanie bazy danych z modelu zakupów opartego na usłudze DTU do modelu zakupów opartego na parć vCore jest podobne do uaktualniania lub obniżania poziomu między warstwami usług standardowych i premium w modelu zakupów opartym na usłudze DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrowanie baz danych korzystających z replikacji geograficznej

Migracja z modelu opartego na USŁUDZE DTU do modelu zakupów opartego na wynikach vCore jest podobna do uaktualniania lub obniżania poziomu relacji replikacji geograficznej między bazami danych w warstwach usług standardowych i premium. Podczas migracji nie trzeba zatrzymać replikacji geograficznej, ale należy przestrzegać następujących reguł sekwencjonowania:

- Podczas uaktualniania należy najpierw uaktualnić pomocniczą bazę danych, a następnie uaktualnić podstawową.
- Podczas obniżania poziomu, odwrócić kolejność: należy najpierw obniżyć podstawową bazę danych, a następnie obniżyć pomocniczą.

Podczas korzystania z replikacji geograficznej między dwoma pulami elastycznymi zaleca się wyznaczenie jednej puli jako głównej, a drugiej jako pomocniczej. W takim przypadku podczas migracji pul elastycznych należy użyć tych samych wskazówek dotyczących sekwencjonowania. Jeśli jednak masz pule elastyczne, które zawierają zarówno podstawowe, jak i pomocnicze bazy danych, należy traktować pulę z wyższym wykorzystaniem jako podstawowe i odpowiednio przestrzegać reguł sekwencjonowania.  

Poniższa tabela zawiera wskazówki dotyczące konkretnych scenariuszy migracji:

|Bieżąca warstwa usług|Docelowa warstwa usług|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa|Zastosowania ogólne|Boczne|Można migrować w dowolnej kolejności, ale trzeba zapewnić odpowiednie rozmiary vCore *|
|Premium|Krytyczne dla biznesu|Boczne|Można migrować w dowolnej kolejności, ale trzeba zapewnić odpowiednie rozmiary vCore *|
|Standardowa|Krytyczne dla biznesu|Uaktualnienie|Musi najpierw przeprowadzić migrację pomocniczą|
|Krytyczne dla biznesu|Standardowa|Zmiana na starszą lub mniej zaawansowaną wersję|Musi najpierw przeprowadzić migrację podstawową|
|Premium|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Musi najpierw przeprowadzić migrację podstawową|
|Zastosowania ogólne|Premium|Uaktualnienie|Musi najpierw przeprowadzić migrację pomocniczą|
|Krytyczne dla biznesu|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Musi najpierw przeprowadzić migrację podstawową|
|Zastosowania ogólne|Krytyczne dla biznesu|Uaktualnienie|Musi najpierw przeprowadzić migrację pomocniczą|
||||

\*Zgodnie z ogólną zasadą co 100 procesorów DTU w warstwie standardowej wymaga co najmniej 1 r., a każdy 125 procesorów DTU w warstwie premium wymaga co najmniej 1 podstawowych. Aby uzyskać więcej informacji, zobacz model zakupów oparty na technologii [vCore](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Migrowanie grup trybu failover

Migracja grup trybu failover z wieloma bazami danych wymaga indywidualnej migracji podstawowych i pomocniczych baz danych. Podczas tego procesu zastosowanie mają te same względy i reguły sekwencjonowania. Po przekonwertowaniu baz danych na model zakupów oparty na parcie wirtualnym grupa trybu failover pozostanie w mocy z tymi samymi ustawieniami zasad.

### <a name="create-a-geo-replication-secondary-database"></a>Tworzenie pomocniczej bazy danych replikacji geograficznej

Pomocniczą bazę danych replikacji geograficznej (pomocniczą) można utworzyć tylko przy użyciu tej samej warstwy usług, która została użyta w podstawowej bazie danych. W przypadku baz danych o wysokiej szybkości generowania dziennika zaleca się utworzenie geo-pomocniczego o tym samym rozmiarze obliczeń co podstawowy.

Jeśli tworzysz geo-pomocniczy w puli elastycznej dla pojedynczej podstawowej bazy danych, upewnij się, że `maxVCore` ustawienie dla puli jest zgodne z rozmiarem obliczeń podstawowej bazy danych. Jeśli tworzysz geo-pomocniczy dla podstawowego w innej puli elastycznej, zaleca `maxVCore` się, że pule mają te same ustawienia.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Konwertowanie bazy danych opartej na UJ na bazę danych opartą na parcie oparte na parcie bazy danych za pomocą bazy danych baz danych

Można skopiować dowolną bazę danych o rozmiarze obliczeniowym opartym na jednostce DTU do bazy danych o rozmiarze obliczeniowym opartym na wynikach wirtualnych bez ograniczeń lub specjalnego sekwencjonowania, o ile docelowy rozmiar obliczeń obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopia bazy danych tworzy migawkę danych w czasie rozpoczęcia operacji kopiowania i nie synchronizuje danych między źródłem a obiektem docelowym.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać określone rozmiary obliczeń i opcje rozmiaru magazynu dostępne dla pojedynczych baz danych, zobacz Limity zasobów opartych na polach [wirtualnych bazy danych SQL dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- Aby uzyskać określone rozmiary obliczeń i wybór rozmiaru magazynu dostępne dla pul elastycznych, zobacz [Limity zasobów opartych na](sql-database-vcore-resource-limits-elastic-pools.md)polach elastycznych bazy danych SQL .
