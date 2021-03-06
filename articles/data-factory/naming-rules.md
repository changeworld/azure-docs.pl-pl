---
title: Reguły nadawania nazw jednostkom usługi Azure Data Factory
description: W tym artykule opisano reguły nazewnictwa dla jednostek usługi Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 59cddf04493333b441dcf130d1d99d4fa946748c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837846"
---
# <a name="azure-data-factory---naming-rules"></a>Usługa Azure Data Factory — reguły nazewnictwa
Poniższa tabela zawiera reguły nazewnictwa artefaktów fabryki danych.

| Nazwa | Unikatowość nazwy | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Fabryka danych |Unikatowy na platformie Microsoft Azure. Nazwy są bez uwzględniania wielkości liter, `MyDF` `mydf` to znaczy i odnoszą się do tej samej fabryki danych. |<ul><li>Każda fabryka danych jest powiązana z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się od litery lub liczby i mogą zawierać tylko litery, cyfry i znak myślnika (-).</li><li>Każdy znak myślnika (-) musi być bezpośrednio poprzedzony i następuje litera lub cyfra. Kolejne kreski nie są dozwolone w nazwach kontenerów.</li><li>Nazwa może mieć długości od 3 do 63 znaków.</li></ul> |
| Połączone usługi/zestawy danych/potoki |Unikatowy z w fabryce danych. Nazwy są bez uwzględniania wielkości liter. |<ul><li>Nazwy obiektów muszą zaczynać się od litery, liczby lub podkreślenia (_).</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">","","%","&,"":","\\</li><li>Myślniki ("-") nie są dozwolone tylko w nazwach połączonych usług i tylko zestawów danych.</li></ul>  |
| Grupa zasobów |Unikatowy na platformie Microsoft Azure. Nazwy są bez uwzględniania wielkości liter. | Aby uzyskać więcej informacji, zobacz [Reguły i ograniczenia nazewnictwa platformy Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak tworzyć fabryki danych, wykonując instrukcje krok po kroku w [przewodniku Szybki start: utwórz](quickstart-create-data-factory-powershell.md) artykuł fabryki danych. 
