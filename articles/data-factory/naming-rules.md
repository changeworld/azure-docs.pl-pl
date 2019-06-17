---
title: Reguły nazewnictwa jednostki usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule opisano zasady nazewnictwa dla jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: c02a9393de72b827b7e38b52d06589f042d581b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787006"
---
# <a name="azure-data-factory---naming-rules"></a>Usługa Azure Data Factory — reguły nazewnictwa
Poniższa tabela zawiera zasady nazewnictwa artefaktów usługi Data Factory.

| Name (Nazwa) | Name Uniqueness | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Fabryka danych |Unikatowa w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter, czyli `MyDF` i `mydf` odnoszą się do tej samej usługi data factory. |<ul><li>Każda fabryka danych jest powiązany z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być natychmiast poprzedzony i następuje się literą lub cyfrą. Następujące po sobie kreski są niedozwolone w nazwach kontenerów.</li><li>Nazwa może być 3 do 63 znaków.</li></ul> |
| Połączone usługi/zestawy danych/potoków |Unikatowe za pomocą w fabryce danych. Nazwy jest rozróżniana wielkość liter. |<ul><li>Nazwy obiektów musi rozpoczynać się literą, liczbą lub znaku podkreślenia (_).</li><li>Nie może zawierać następujących znaków: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li><li>Łączniki ("-") nie są dozwolone w nazwach połączonych usług i tylko zestawów danych.</li></ul>  |
| Grupa zasobów |Unikatowa w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter. | Aby uzyskać więcej informacji, zobacz [Azure reguł i ograniczeń nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak tworzyć fabryki danych, postępując zgodnie z instrukcjami krok po kroku w [Szybki Start: tworzenie fabryki danych](quickstart-create-data-factory-powershell.md) artykułu. 
