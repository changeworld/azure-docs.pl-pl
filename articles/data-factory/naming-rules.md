---
title: Reguły nazewnictwa Azure Data Factory jednostek | Microsoft Docs
description: Opisuje reguły nazewnictwa dla jednostek Data Factory.
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
ms.openlocfilehash: 4397f7efdb9ff797375584f5a43af2833a6a2989
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140991"
---
# <a name="azure-data-factory---naming-rules"></a>Reguły nazewnictwa Azure Data Factory
W poniższej tabeli przedstawiono reguły nazewnictwa Data Factory artefaktów.

| Name | Name Uniqueness | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Fabryka danych |Unikatowy dla Microsoft Azure. W nazwach nie jest rozróżniana wielkość liter, `MyDF` oznacza `mydf` to, że i zapoznaj się z tą samą fabryką danych. |<ul><li>Każda Fabryka danych jest powiązana z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i mogą zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być bezpośrednio poprzedzony literą lub cyfrą. Kolejne kreski są niedozwolone w nazwach kontenerów.</li><li>Nazwa może składać się z 3-63 znaków.</li></ul> |
| Połączone usługi/zestawy danych/potoki |Unikatowy w usłudze Fabryka danych. W nazwach nie jest rozróżniana wielkość liter. |<ul><li>Nazwy obiektów muszą zaczynać się literą, cyfrą lub znakiem podkreślenia (_).</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":",\\""</li><li>Łączniki ("-") są niedozwolone w nazwach połączonych usług i tylko dla zestawów danych.</li></ul>  |
| Grupa zasobów |Unikatowy dla Microsoft Azure. W nazwach nie jest rozróżniana wielkość liter. | Aby uzyskać więcej informacji, zobacz [reguły i ograniczenia nazewnictwa platformy Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak utworzyć fabrykę danych, wykonując instrukcje krok po kroku w [przewodniku szybki start: Tworzenie artykułu dla fabryki danych](quickstart-create-data-factory-powershell.md) . 
