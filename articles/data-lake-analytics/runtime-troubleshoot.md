---
title: Jak rozwiązywać problemy z błędami środowiska uruchomieniowego usługi Azure Data Lake Analytics U-SQL
description: Dowiedz się, jak rozwiązywać problemy z awariami środowiska uruchomieniowego U-SQL.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648456"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Dowiedz się, jak rozwiązywać problemy z awariami środowiska uruchomieniowego U-SQL spowodowanymi zmianami w czasie wykonywania

Środowisko uruchomieniowe Usługi Azure Data Lake U-SQL, w tym kompilator, optymalizator i menedżer zadań, jest tym, co przetwarza kod U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Wybieranie wersji środowiska wykonawczego U-SQL

Po przesłaniu zadań U-SQL z programu Visual Studio, ADL SDK lub portalu Usługi Azure Data Lake Analytics zadanie użyje aktualnie dostępnego domyślnego środowiska uruchomieniowego. Nowe wersje środowiska wykonawczego U-SQL są wydawane regularnie i zawierają zarówno drobne aktualizacje, jak i poprawki zabezpieczeń.

Można również wybrać niestandardową wersję środowiska uruchomieniowego; ponieważ chcesz wypróbować nową aktualizację, należy pozostać w starszej wersji środowiska wykonawczego lub zostały dostarczone z poprawką dla zgłoszonego problemu, w którym nie można czekać na regularne nowej aktualizacji.

> [!CAUTION]
> Wybranie środowiska wykonawczego, który różni się od wartości domyślnej ma potencjał, aby przerwać zadania U-SQL. Użyj tych innych wersji tylko do testowania.

W rzadkich przypadkach pomoc techniczna firmy Microsoft może przypiąć inną wersję środowiska wykonawczego jako domyślną dla twojego konta. Upewnij się, że ten pin zostanie jak najszybciej przywrócą. Jeśli pozostaniesz przypięty do tej wersji, wygaśnie ona w późniejszym terminie.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorowanie zadań U-SQL wersja środowiska uruchomieniowego

Możesz zobaczyć historię, której wersji środowiska uruchomieniego zostały użyte w historii zadań twojego konta za pośrednictwem przeglądarki zadań programu Visual Studio lub historii zadań witryny Azure portal.

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Wybierz **pozycję Wyświetl wszystkie zadania**. Pojawi się lista wszystkich aktywnych i ostatnio zakończonych zadań na koncie.
3. Opcjonalnie kliknij przycisk **Filtruj,** aby ułatwić znajdowanie zadań według **przedziału czasu,** **nazwy zlecenia**i wartości **autora.**
4. Można zobaczyć środowisko wykonawcze używane w ukończonych zadaniach.

![Wyświetlanie wersji środowiska wykonawczego zadania z przeszłości](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Dostępne wersje środowiska wykonawczego zmieniają się wraz z czasem. Domyślne środowisko uruchomieniowe jest zawsze nazywane "domyślnym" i przechowujemy co najmniej poprzednie środowisko wykonawcze dostępne przez pewien czas, a także udostępnia specjalne środowiska wykonawcze z różnych powodów. Jawnie nazwane środowiska wykonawcze zazwyczaj są zgodne z następującym formatem (kursywa jest używana dla części zmiennych i [] wskazuje opcjonalne części):

release_YYYYMMDD_adl_buildno _modifier

Na przykład oznacza release_20190318_adl_3394512_2 druga wersja kompilacji 3394512 wersji wykonawczej z marca 18 2019 i release_20190318_adl_3394512_private oznacza prywatną kompilację tej samej wersji. Uwaga: Data jest związana z tym, kiedy dokonano ostatniego zameldowania w tej wersji, a niekoniecznie z oficjalną datą premiery.

Poniżej przedstawiono aktualnie dostępne wersje środowiska uruchomieniowego.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 bieżącą wartość domyślną
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Rozwiązywanie problemów z wersją środowiska wykonawczego U-SQL

Istnieją dwa możliwe problemy z wersją środowiska uruchomieniowego, które mogą wystąpić:

1. Skrypt lub niektóre kodu użytkownika zmienia zachowanie z jednej wersji do następnej. Takie przełomowe zmiany są zwykle przekazywane z wyprzedzeniem wraz z publikacją informacji o wydaniu. Jeśli napotkasz taką przełomową zmianę, skontaktuj się z pomocą techniczną firmy Microsoft, aby zgłosić to zachowanie przerywania (w przypadku, gdy nie zostało jeszcze udokumentowane) i przesłać zadania względem starszej wersji środowiska uruchomieniowego.

2. Używasz środowiska uruchomieniowego innego niż domyślne jawnie lub niejawnie, gdy został przypięty do konta, a środowisko uruchomieniowe zostało usunięte po pewnym czasie. Jeśli wystąpi brakujące środowiska wykonawcze, uaktualnij skrypty, aby działały z bieżącym domyślnym czasem wykonywania. Jeśli potrzebujesz dodatkowego czasu, skontaktuj się z pomocą techniczną firmy Microsoft

## <a name="see-also"></a>Zobacz też

- [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-manage-use-portal.md)
- [Monitorowanie zadań w usłudze Azure Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
