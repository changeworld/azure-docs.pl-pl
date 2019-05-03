---
title: Limity usługi Azure Blockchain
description: Omówienie usługi i ograniczenia funkcjonalności w usłudze Azure Service łańcucha bloków
services: azure-blockchain
keywords: łańcuch bloków
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028173"
---
# <a name="limits-in-azure-blockchain-service"></a>Limity usługi Azure Blockchain

Usługa Azure Blockchain ma usługi i funkcjonalności limity, takie jak liczba węzłów, jaką może mieć elementu członkowskiego, konsorcjum ograniczenia i ilości magazynu.

## <a name="pricing-tier"></a>Warstwa cenowa

Maksymalny limit dla transakcji i węzły modułu sprawdzania poprawności zależą od tego, czy aprowizowanie usługi Azure Service łańcucha bloków w warstwie podstawowa lub standardowa warstw cenowych.

| Warstwa cenowa | Maksymalna liczba transakcji węzłów | Maks. węzłów modułu sprawdzania poprawności |
|:---|:---:|:---:|
| Podstawowa | 10 | 1 |
| Standardowa (Standard) | 10 | 2 |

Zmiana warstwy cenowej między podstawowa i standardowa, po utworzeniu elementu członkowskiego nie jest obsługiwana.

## <a name="storage-capacity"></a>Pojemność magazynu

Maksymalna ilość pamięci masowej, który może służyć za węzeł księgi danych i dzienników to 1 terabajt.

Zmniejsza rozmiar magazynu rejestr i dziennika nie jest obsługiwane.

## <a name="consortium-limits"></a>Limity konsorcjum

* **Konsorcjum i elementów członkowskich nazwy muszą być unikatowe** od nazw innych konsorcjum i elementów członkowskich w usłudze Azure Service łańcucha bloków.

* **Nie można zmienić nazwy elementu członkowskiego i konsorcjum**

* **Wszystkie elementy członkowskie do konsorcjum musi należeć do tej samej warstwie cenowej**

* **Wszystkie elementy członkowskie, które uczestniczą w konsorcjum musi znajdować się w tym samym regionie**

    Pierwszy element członkowski, utworzone w konsorcjum decyduje o regionie. Zaproszonych członków do konsorcjum musi znajdować się w tym samym regionie jako pierwszego elementu członkowskiego. Ograniczanie wszystkie elementy członkowskie do tego samego regionu pomaga upewnić się, że consensus sieci nie ma negatywnego wpływu na.

* **Konsorcjum musi mieć co najmniej jednego administratora**

    Jeśli istnieje tylko jeden administrator konsorcjum, nie został usunięty z konsorcjum, lub usuń ich element członkowski, aż inny administrator jest dodawany lub promowany w konsorcjum.

* **Nie można ponownie dodać elementy członkowskie usunięte z konsorcjum**

    Zamiast ich musi można go ponownie zaprosić do dołączania konsorcjum, a następnie utwórz nowy element członkowski. Ich istniejącego zasobu elementu członkowskiego nie są usuwane do zachowania transakcji historycznych.

* **Wszystkie elementy członkowskie do konsorcjum muszą używać tej samej wersji księgi**

    Aby uzyskać więcej informacji na temat poprawek, aktualizacji i księgi wersje dostępne w usłudze Azure Service łańcucha bloków, zobacz [stosowanie poprawek, aktualizacji i wersje](ledger-versions.md).

## <a name="next-steps"></a>Kolejne kroki

* [Poprawki, aktualizacje i wersje](ledger-versions.md)
