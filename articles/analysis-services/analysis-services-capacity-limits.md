---
title: Azure Analysis Services limity zasobów i obiektów | Microsoft Docs
description: Opisuje Azure Analysis Services limity zasobów i obiektów.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fe43602c66af72357e16822ee9d4b5a741d3f86
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298705"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services limity zasobów i obiektów

W tym artykule opisano limity zasobów i obiektów modelu.

## <a name="tier-limits"></a>Limity warstwy

Aby uzyskać QPU i limity pamięci dla warstw deweloperskich, podstawowa i standardowa, zobacz [stronę z cennikiem Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limity obiektów

Te limity są teoretyczne. Wydajność zostanie obniżona przy mniejszej liczbie.

|Obiekt|Maksymalne rozmiary/liczby|  
|------------|----------------------------|  
|Bazy danych w wystąpieniu|16 000|  
|Łączna liczba tabel i kolumn w bazie danych|16 000|  
|Wiersze w tabeli|Bez ograniczeń<br /><br /> **Ostrzeżenie:** Ograniczenie, że żadna Pojedyncza kolumna w tabeli nie może mieć więcej niż 1 999 999 997 odrębnych wartości.|  
|Hierarchie w tabeli|15 999|  
|Poziomy w hierarchii|15 999|  
|Relacje|8000|  
|Kolumny kluczy we wszystkich tabelach|15 999|  
|Miary w tabelach|2 ^ 31-1 = 2 147 483 647|  
|Komórki zwrócone przez zapytanie|2 ^ 31-1 = 2 147 483 647|  
|Rozmiar rekordu zapytania źródłowego|64 K|  
|Długość nazw obiektów|512 znaków|  


