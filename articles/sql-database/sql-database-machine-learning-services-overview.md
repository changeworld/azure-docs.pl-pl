---
title: Usługi uczenia maszynowego z R (wersja zapoznawcza)
description: W tym artykule opisano usługi Azure SQL Database Machine Learning Services (z R) i wyjaśniono, jak to działa.
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
ms.date: 11/20/2019
ms.openlocfilehash: 2a2cd4bfc3d393543b41eea776f02723d94054b1
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345826"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Usługi azure SQL Database Machine Learning services z r (wersja zapoznawcza)

Usługi uczenia maszynowego to funkcja usługi Azure SQL Database, używana do wykonywania skryptów R w bazie danych. Ta funkcja zawiera pakiety Microsoft R dla wysokowydajnych analiz predykcyjnych i uczenia maszynowego. Dane relacyjne mogą być używane w skryptach języka R za pośrednictwem procedur przechowywanych, skryptu T-SQL zawierającego instrukcje R lub kodu Języka R zawierającego T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Wersja zapoznawcza jest dostępna dla pojedynczych baz danych i pul elastycznych przy użyciu modelu zakupów opartego na językach wirtualnych w **warstwach** usług ogólnego przeznaczenia i **krytycznych dla firmy.** W tej początkowej wersji zapoznawczej warstwa usługi **hiperskali** i opcja wdrażania **wystąpienia zarządzanego** nie są obsługiwane. Obecnie język R jest jedynym obsługiwanym językiem. W tej chwili język Python nie jest obsługiwany.
>
> Wersja zapoznawcza jest obecnie dostępna w następujących regionach: Europa Zachodnia, Europa Północna, Zachodnie stany USA 2, Wschodnie stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Kanada Środkowa, Azja Południowo-Wschodnia, Indie Południowe i Australia Południowo-Wschodnia.

## <a name="what-you-can-do-with-r"></a>Co można zrobić z R

Korzystaj z możliwości języka R, aby dostarczać zaawansowaną analizę i uczenie maszynowe w bazie danych. W ten sposób obliczenia i przetwarzanie przenoszone są do miejsca przechowywania danych, co eliminuje konieczność ściągania danych przez sieć. Ponadto można wykorzystać możliwości pakietów R dla przedsiębiorstw, aby dostarczać zaawansowane analizy na dużą skalę.

Usługa Machine Learning Services obejmuje podstawową dystrybucję języka R z nakładką w postaci korporacyjnych pakietów języka R firmy Microsoft. Funkcje i algorytmy języka R firmy Microsoft zostały opracowane z myślą o skalowaniu i użyteczności, zapewniając analizę predykcyjną, modelowanie statystyczne, wizualizacje danych i najwyższej klasy algorytmy uczenia maszynowego.

### <a name="r-packages"></a>Pakiety R

Większość typowych pakietów języka R typu open source jest wstępnie zainstalowana w usługach uczenia maszynowego. Następujące pakiety R firmy Microsoft są również zawarte:

| Pakiet R | Opis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open to ulepszona dystrybucja R firmy Microsoft. Jest to kompletna platforma open source do analizy statystycznej i analizy danych. Jest on oparty na i 100% kompatybilny z R i zawiera dodatkowe możliwości dla lepszej wydajności i odtwarzalności. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR jest biblioteką podstawową dla skalowalnych R. Funkcje w tej bibliotece należą do najczęściej używanych. Przekształcenia i manipulacje danych, podsumowanie statystyczne, wizualizacja i wiele form modelowania i analiz znajdują się w tych bibliotekach. Ponadto funkcje w tych bibliotekach automatycznie rozdzielają obciążenia między dostępne rdzenie do przetwarzania równoległego, z możliwością pracy nad fragmentami danych, które są koordynowane i zarządzane przez aparat obliczeniowy. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML dodaje algorytmy uczenia maszynowego do tworzenia niestandardowych modeli do analizy tekstu, analizy obrazu i analizy tonacji. |

Oprócz wstępnie zainstalowanych pakietów można [zainstalować dodatkowe pakiety](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Zarejestruj się w wersji zapoznawczej (zamkniętej)

> [!IMPORTANT]
> Zarejestruj się w usłudze Azure SQL Database Machine Learning Services (wersja zapoznawcza) jest obecnie **zamknięta**.

## <a name="next-steps"></a>Następne kroki

- Zobacz [kluczowe różnice w usługach SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań o usługi Azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz [przewodnik Szybki start](sql-database-connect-query-r.md).
- Aby rozpocząć korzystanie z kilku prostych skryptów języka R, zobacz [Tworzenie i uruchamianie prostych skryptów języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md).
