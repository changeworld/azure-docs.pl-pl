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
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: daf0b1c12ab10230690a62eb5dc772417d8b92f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824239"
---
# <a name="azure-data-factory---naming-rules"></a>Usługa Azure Data Factory — reguły nazewnictwa
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [reguły w usłudze Data Factory nazewnictwa](../naming-rules.md).

Poniższa tabela zawiera zasady nazewnictwa artefaktów usługi Data Factory.

| Name (Nazwa) | Name Uniqueness | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Fabryka danych |Unikatowa w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter, czyli `MyDF` i `mydf` odnoszą się do tej samej usługi data factory. |<ul><li>Każda fabryka danych jest powiązany z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być natychmiast poprzedzony i następuje się literą lub cyfrą. Następujące po sobie kreski są niedozwolone w nazwach kontenerów.</li><li>Nazwa może być 3 do 63 znaków.</li></ul> |
| Połączonych usług/tabel/potoków |Unikatowe za pomocą w fabryce danych. Nazwy jest rozróżniana wielkość liter. |<ul><li>Maksymalna liczba znaków w nazwie tabeli: 260.</li><li>Nazwy obiektów musi rozpoczynać się literą, liczbą lub znaku podkreślenia (_).</li><li>Nie może zawierać następujących znaków: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Grupa zasobów |Unikatowa w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter. |<ul><li>Maksymalna liczba znaków: 1000.</li><li>Nazwa może zawierać litery, cyfry i następujące znaki: "-", "_",","i"."</li></ul> |

