---
title: Reguły nazewnictwa jednostek fabryki danych Azure | Dokumentacja firmy Microsoft
description: Opisuje reguły nazewnictwa dla jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: e9d2140edc64daca4df0463dbfdcd5b06f072012
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620412"
---
# <a name="azure-data-factory---naming-rules"></a>Fabryka danych Azure - reguły nazewnictwa
W poniższej tabeli przedstawiono reguły nazewnictwa artefaktów fabryki danych.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [reguły w fabryce danych version1 nazewnictwa](v1/data-factory-naming-rules.md).

| Name (Nazwa) | Name Uniqueness | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Fabryka danych |Unikatowe w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter, czyli `MyDF` i `mydf` odwoływać się do tej samej fabryki danych. |<ul><li>Każdej fabryki danych jest powiązany dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być od razu poprzedzone i następuje literą lub cyfrą. Następujących po sobie kresek nie są dozwolone w nazwach kontenerów.</li><li>Nazwa może być 3 do 63 znaków.</li></ul> |
| Połączonej usługi/tabel/potoki |Unikatowy o w fabryce danych. Nazwy jest rozróżniana wielkość liter. |<ul><li>Maksymalna liczba znaków w nazwie tabeli: 260.</li><li>Nazwa obiektu musi rozpoczynać się od litery, liczby lub znaku podkreślenia (_).</li><li>Następujące znaki nie są dozwolone: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li><li>Łączniki ("-") nie są dozwolone w nazwach połączone usługi, a tylko zestawów danych.</li></ul>  |
| Grupa zasobów |Unikatowe w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter. | Aby uzyskać więcej informacji, zobacz [ograniczenia i reguły nazewnictwa Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak można utworzyć fabryki danych, wykonując następujące instrukcje krok po kroku w [Szybki Start: tworzenie fabryki danych](quickstart-create-data-factory-powershell.md) artykułu. 
