---
title: Mapowanie transformacji wiersza polecenia Alter przepływu danych w usłudze Azure Data Factory
description: Jak zaktualizować docelowej bazy danych przy użyciu Azure danych fabryki mapowanie przepływu Alter wiersz przekształcania danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: f0ac5bb36079983b10e4d86cc776bd4e5ee6817d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520145"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Przekształcenie wiersza polecenia Alter fabryki danych platformy Azure

Przekształcenie wiersza polecenia Alter umożliwia ustawianie zasad insert, usuwanie, aktualizacja i upsert w wierszach. Można dodać warunki jeden do wielu jako wyrażenia. Każda z tych warunków może spowodować w wiersz lub wiersze wstawione, zaktualizowane, usunięty lub upsert. Wiersz polecenia ALTER może tworzyć akcje zarówno DDL i DML względem bazy danych.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Zmień ustawienia wiersza](media/data-flow/alter-row1.png "zmienić ustawienia wiersza")

> [!NOTE]
> Przekształcenia wiersza polecenia ALTER będzie działać tylko w odniesieniu do bazy danych ujścia przepływu danych. Akcje, które można przypisać do wierszy (insert, update, delete, upsert) nie występuje podczas sesji debugowania. Należy dodać zadanie wykonania przepływu danych do potoku i wprowadź w życie alter zasady wierszy w tabelach bazy danych za pomocą potoku debugowania lub wyzwalaczy.

## <a name="view-policies"></a>Wyświetl zasady

Przełącz tryb debugowania przepływu danych do włączone, a następnie Wyświetl wyniki zasad wiersza polecenia alter w okienku podglądu danych. Wykonywanie wiersza polecenia alter w trybie debugowania przepływu danych nie przyniesie DDL i DML akcji względem urządzenie docelowe. Aby uzyskać te akcje, które występują, należy wykonać przepływ danych wewnątrz działania wykonywania przepływu danych w potoku.

![Zmiany zasad wiersz](media/data-flow/alter-row3.png "zmienić zasady wiersza")

Pozwoli to sprawdzić i wyświetlić stan każdego wiersza w oparciu o warunkach. Istnieją ikona reprezentuje dla każdego wstawiania, aktualizowania, usuwania i upsert akcję, która odbędzie się przepływ danych, wskazujący akcję, która będzie miała miejsce podczas wykonywania przepływu danych w potoku.

## <a name="sink-settings"></a>Ustawienia ujścia

Musisz mieć bazę danych będący ujściem typu dla wiersza polecenia Alter do pracy. Ustawienia ujścia musi ustawić każdej akcji, które mają być dozwolone.

![ALTER ujścia wiersz](media/data-flow/alter-row2.png "Alter ujścia wiersza")

Domyślne zachowanie w przepływ danych ADF bazy danych ujścia jest aby wstawić wiersze. Jeśli chcesz zezwolić na aktualizacje, wykonuje operację UPSERT i usuwa także, możesz również sprawdzić te pola w ujściu, aby zezwolić na akcje.

> [!NOTE]
> Jeśli Twoje wstawiania, aktualizacji lub wykonuje operację UPSERT zmodyfikować schematu tabeli docelowej w ujściu, przepływ danych zakończy się niepowodzeniem. Aby zmodyfikować schemat docelowy w bazie danych, należy wybrać opcję "Utwórz tabelę" w ujściu. To spowoduje drop i ponowne utworzenie tabeli za pomocą nowych definicji schematu.

## <a name="next-steps"></a>Kolejne kroki

Po przekształceniu wiersza polecenia Alter, możesz chcieć [ujście danych do docelowego magazynu danych](data-flow-sink.md).
