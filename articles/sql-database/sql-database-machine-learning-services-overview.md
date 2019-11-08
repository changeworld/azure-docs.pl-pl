---
title: Machine Learning Services z językiem R (wersja zapoznawcza)
description: W tym artykule opisano Azure SQL Database Machine Learning Services (z językiem R) i wyjaśniono, jak działa.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827430"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services z językiem R (wersja zapoznawcza)

Machine Learning Services to funkcja Azure SQL Database używana do wykonywania skryptów języka R w bazie danych. Ta funkcja obejmuje pakiety Microsoft R na potrzeby analizy predykcyjnej i uczenia maszynowego o wysokiej wydajności. Dane relacyjne mogą być używane w skryptach języka R za pomocą procedur składowanych, skryptu T-SQL zawierającego instrukcje języka R lub kodu języka R zawierającego język T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (w języku R) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Publiczna wersja zapoznawcza jest dostępna dla pojedynczych baz danych i pul elastycznych przy użyciu modelu zakupu opartego na rdzeń wirtualny w warstwach usług **ogólnych** i **krytycznych dla działalności biznesowej** . W tej początkowej publicznej wersji zapoznawczej **warstwa usługi i** wdrożenie **wystąpienia zarządzanego** nie są obsługiwane. Obecnie język R jest jedynym obsługiwanym językiem. W tej chwili język Python nie jest obsługiwany.
>
> Wersja zapoznawcza jest obecnie dostępna w następujących regionach: Europa Zachodnia, Europa Północna, zachodnie stany USA 2, Wschodnie stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Kanada środkowa, Azja Południowo-Wschodnia, Indie Południowe i Australia Południowo-Wschodnia.
>
> [Utwórz konto w wersji zapoznawczej](#signup) poniżej.

## <a name="what-you-can-do-with-r"></a>Co możesz zrobić przy użyciu języka R

Korzystaj z możliwości języka R, aby dostarczać zaawansowaną analizę i uczenie maszynowe w bazie danych. W ten sposób obliczenia i przetwarzanie przenoszone są do miejsca przechowywania danych, co eliminuje konieczność ściągania danych przez sieć. Ponadto możesz wykorzystać możliwości pakietów języka R dla przedsiębiorstw, aby zapewnić zaawansowaną analizę na dużą skalę.

Usługa Machine Learning Services obejmuje podstawową dystrybucję języka R z nakładką w postaci korporacyjnych pakietów języka R firmy Microsoft. Funkcje i algorytmy języka R firmy Microsoft zostały opracowane z myślą o skalowaniu i użyteczności, zapewniając analizę predykcyjną, modelowanie statystyczne, wizualizacje danych i najwyższej klasy algorytmy uczenia maszynowego.

### <a name="r-packages"></a>Pakiety języka R

Najpopularniejsze pakiety języka R Open Source są wstępnie zainstalowane w Machine Learning Services. Dostępne są również następujące pakiety języka R firmy Microsoft:

| Pakiet języka R | Opis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open to ulepszona dystrybucja języka R od firmy Microsoft. Jest to kompletna platforma typu "open source" umożliwiająca analizę statystyczną i naukę danych. Jest on oparty na i 100% zgodny z językiem R i oferuje dodatkowe możliwości w celu zwiększenia wydajności i odtwarzalności. |
| [Kolekcję funkcji revoscaler](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | Kolekcję funkcji revoscaler jest podstawową biblioteką skalowaln R. funkcje w tej bibliotece są między najczęściej używanymi. Przekształceń i manipulowanie danymi, podsumowanie statystyczne, Wizualizacja i wiele form modelowania i analiz można znaleźć w tych bibliotekach. Ponadto funkcje w tych bibliotekach automatycznie dystrybuują obciążenia na dostępne rdzenie do przetwarzania równoległego, dzięki czemu można korzystać z fragmentów danych, które są skoordynowane i zarządzane przez aparat obliczeń. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML dodaje algorytmy uczenia maszynowego do tworzenia niestandardowych modeli na potrzeby analizy tekstu, analizy obrazów i analizy tonacji. |

Oprócz wstępnie zainstalowanych pakietów można [zainstalować dodatkowe pakiety](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Tworzenie konta na potrzeby korzystania z wersji zapoznawczej

Aby zarejestrować się w publicznej wersji zapoznawczej, wykonaj następujące kroki:

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

2. Wyślij wiadomość e-mail do firmy Microsoft na adres [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com), aby utworzyć konto na potrzeby korzystania z publicznej wersji zapoznawczej. Publiczna wersja zapoznawcza usługi Machine Learning (z językiem R) w usłudze SQL Database nie jest włączona domyślnie.

Po zarejestrowaniu się w programie firma Microsoft przeprowadzi Cię do publicznej wersji zapoznawczej i włączy język R dla istniejącej lub nowej bazy danych.

Machine Learning Services przy użyciu języka R nie jest zalecane w przypadku obciążeń produkcyjnych w publicznej wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z kluczowymi różnicami w Machine Learning Services SQL Server](sql-database-machine-learning-services-differences.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań w Azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz [Przewodnik Szybki Start](sql-database-connect-query-r.md).
- Aby zacząć korzystać z niektórych prostych skryptów języka R, zobacz [Tworzenie i Uruchamianie prostych skryptów języka r w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md).
