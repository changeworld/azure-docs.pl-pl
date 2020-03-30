---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157226"
---
Opcje nadmiarowości dla konta magazynu obejmują:

* Lokalnie nadmiarowa pamięć masowa (LRS): prosta, tania strategia nadmiarowości. Dane są kopiowane synchronicznie trzy razy w regionie podstawowym.
* Magazyn strefowy (ZRS): nadmiarowość w scenariuszach wymagających wysokiej dostępności. Dane są kopiowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym.
* Magazyn geograficznie nadmiarowy (GRS): Nadmiarowość międzyregionalna w celu ochrony przed regionalnymi awariami. Dane są kopiowane synchronicznie trzy razy w regionie podstawowym, a następnie kopiowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu danych w regionie pomocniczym, włącz magazyn geograficzny dostępu do odczytu (RA-GRS).
* Magazyn nadmiarowy ze strefy geograficznej (GZRS) (wersja zapoznawcza): nadmiarowość w scenariuszach wymagających zarówno wysokiej dostępności, jak i maksymalnej trwałości. Dane są kopiowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym, a następnie kopiowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu danych w regionie pomocniczym, włącz magazyn geograficzny dostępu do strefy odczytu (RA-GZRS).

Aby uzyskać więcej informacji na temat opcji nadmiarowości w usłudze Azure Storage, zobacz [Nadmiarowość usługi Azure Storage](../articles/storage/common/storage-redundancy.md).
