---
title: Limity zasobów i obiektów usług Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano limity zasobów i obiektów dla serwera usług Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573209"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limity zasobów i obiektów usług Analysis Services

W tym artykule opisano limity zasobów i obiektów modelu.

## <a name="tier-limits"></a>Limity poziomów

W przypadku limitów QPU i pamięci dla warstw deweloperskich, podstawowych i standardowych zobacz [stronę cennika usług Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limity obiektów

Limity te są teoretyczne. Wydajność będzie zmniejszana przy niższych liczbach.

|Obiekt|Maksymalne rozmiary/liczby|  
|------------|----------------------------|  
|Bazy danych w wystąpieniu|16 000|  
|Łączna liczba tabel i kolumn w bazie danych|16 000|  
|Wiersze w tabeli|Unlimited (nieograniczony)<br /><br /> **Ostrzeżenie:** Z ograniczeniem, że żadna pojedyncza kolumna w tabeli może mieć więcej niż 1,999,999,997 różne wartości.|  
|Hierarchie w tabeli|15,999|  
|Poziomy w hierarchii|15,999|  
|Relacje|8000|  
|Kolumny kluczy we wszystkich tabelach|15,999|  
|Miary w tabelach|2^31-1 = 2 147 483 647|  
|Komórki zwracane przez kwerendę|2^31-1 = 2 147 483 647|  
|Rozmiar rekordu kwerendy źródłowej|64 k.|  
|Długość nazw obiektów|512 znaków|  


