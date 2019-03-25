---
title: Gen2 magazynu danych w usłudze Azure SQL obsługuje dolnej warstwy wystąpień obliczeniowych | Dokumentacja firmy Microsoft
description: Gen2 magazynu danych w usłudze Azure SQL obsługuje teraz dolnej warstwy wystąpień obliczeniowych
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 23617f3a7eca72e549bcf0e44805d21eef6ffa9b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400314"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Pomoc techniczna platformy Azure SQL Data Warehouse Gen2 dla dolnej warstwy wystąpień obliczeniowych

Firma Microsoft pomaga zwiększania klasy podstawowej koszt uruchamiania obsługi najbardziej wymagających zapytania, dodając dolnej warstwy wystąpień obliczeniowych dla niezwykle szybkie usługi Azure SQL Data magazynu Gen2 magazynu danych. Klienci mogą środowisko usługi Azure SQL Data Warehouse wydajność, elastyczność i bezpieczeństwo funkcjom począwszy od 100 cDWU (jednostek magazynu danych) i skalowanie do ponad 30 000 cDWU w ciągu kilku minut. Klienci mogą korzystać z Gen2 wydajność i elastyczność dzięki niższym warstwy wystąpień obliczeniowych. 

Upuszczając punkt wejścia dla magazynowania danych następnej generacji, Microsoft otwiera drzwi oparte na wartości klientom, którzy chcesz ocenić wszystkie zalety hurtowni danych bezpieczne i o wysokiej wydajności, bez zgadywania, które środowisko w wersji próbnej jest najlepsze dla nich.  Klienci będą można uruchomić możliwie jak 100 cDWU w dół od bieżącego punktu wejścia 500 cDWU.  Gen2 magazynu danych SQL w dalszym ciągu obsługują wstrzymywanie i wznawianie operacji i zbliża się poza po prostu elastyczność obliczeń.  Gen2 obsługuje również magazynu kolumn nieograniczoną pojemność magazynu oraz 2,5 raza więcej pamięci na zapytanie, nawet 128 zapytań jednoczesnych i buforowanie adaptacyjne w funkcji środowiska udostępnianiem średnio 5 razy większej wydajności w porównaniu do tej samej jednostki magazynu danych na Gen1 w tej samej cenie.  Geograficznie nadmiarowe kopie zapasowe są standardem Gen2 z ochroną danych gwarantowana wbudowane. Gen2 magazynu danych w usłudze Azure SQL jest gotowa do skalowania podczas pracy.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Wprowadzenie do usługi Azure SQL Data Warehouse Gen2 

Klienci mogą wybrać opcję wdrażania nowego wystąpienia Gen2 lub uaktualnienie istniejącego wystąpienia magazynu danych Gen1 występował, elastyczność i wydajność magazynowania następnej generacji danych. 

Spróbuj [zoptymalizowane Gen2 warstwa wystąpień obliczeniowych Azure usługa SQL Data Warehouse.](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
Uaktualnij [usługi Azure SQL Data magazynu obliczenia zoptymalizowane pod kątem Gen1 do Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) Obejrzyj Gen2 magazynu danych SQL Azure w akcji, w tym [film wideo Microsoft Mechanics.](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>Obsługiwane regiony dla dolnej warstwy wystąpień obliczeniowych

[W tabeli Dostępność obsługiwany region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](upgrade-to-latest-generation.md) o obliczenia zoptymalizowane pod kątem wydajności, uaktualniając SQL Data Warehouse. 
