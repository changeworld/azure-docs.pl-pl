---
title: Reguły nadawania nazw jednostkom usługi Azure Data Factory
description: W tym artykule opisano reguły nazewnictwa dla jednostek usługi Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8fa1340b586434bf98d51437d4dc6b08594f0afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931886"
---
# <a name="azure-data-factory---naming-rules"></a>Usługa Azure Data Factory — reguły nazewnictwa
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [reguły nazewnictwa w ustawieniach Data Factory](../naming-rules.md).

Poniższa tabela zawiera reguły nazewnictwa artefaktów fabryki danych.

| Nazwa | Unikatowość nazwy | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Fabryka danych |Unikatowy na platformie Microsoft Azure. Nazwy są bez uwzględniania wielkości liter, `MyDF` `mydf` to znaczy i odnoszą się do tej samej fabryki danych. |<ul><li>Każda fabryka danych jest powiązana z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się od litery lub liczby i mogą zawierać tylko litery, cyfry i znak myślnika (-).</li><li>Każdy znak myślnika (-) musi być bezpośrednio poprzedzony i następuje litera lub cyfra. Kolejne kreski nie są dozwolone w nazwach kontenerów.</li><li>Nazwa może mieć długości od 3 do 63 znaków.</li></ul> |
| Połączone usługi/tabele/potoki |Unikatowy z w fabryce danych. Nazwy są bez uwzględniania wielkości liter. |<ul><li>Maksymalna liczba znaków w nazwie tabeli: 260.</li><li>Nazwy obiektów muszą zaczynać się od litery, liczby lub podkreślenia (_).</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">","","%","&,"":","\\</li></ul> |
| Grupa zasobów |Unikatowy na platformie Microsoft Azure. Nazwy są bez uwzględniania wielkości liter. |<ul><li>Maksymalna liczba znaków: 1000.</li><li>Nazwa może zawierać litery, cyfry i następujące znaki: "-", "_", "" i "".</li></ul> |

