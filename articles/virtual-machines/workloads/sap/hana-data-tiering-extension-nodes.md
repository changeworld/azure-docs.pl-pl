---
title: Warstwowe i rozszerzenia danych dla sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Warstwy danych i węzły rozszerzeń dla sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3553ac9704ac26d0bdaae0f93b89f41a87ac716
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617161"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Korzystanie z węzłów warstwowych i rozszerzeń danych SAP HANA

SAP obsługuje model warstwowania danych dla SAP BW różnych wersji SAP NetWeaver i SAP BW/4HANA. Aby uzyskać więcej informacji na temat modelu warstwowania danych, zobacz dokument SAP [BW/4HANA i SAP BW na HANA z węzłami rozszerzenia SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
W przypadku wystąpienia hana large można użyć konfiguracji opcji 1 węzłów rozszerzeń SAP HANA, jak wyjaśniono w często zadawanych pytaniach i dokumentach blogu SAP. Konfiguracje opcji 2 można skonfigurować za pomocą następujących jednostek SKU dużych wystąpień HANA: S72m, S192, S192m, S384 i S384m. 

Gdy spojrzysz na dokumentację, zaleta może nie być natychmiast widoczna. Ale jeśli spojrzeć na wskazówki dotyczące rozmiaru SAP, można zobaczyć przewagę przy użyciu opcji 1 i option-2 SAP HANA węzłów rozszerzeń. Oto przykłady:

- Wytyczne sap HANA dotyczące rozmiaru zwykle wymagają podwójnej ilości woluminu danych jako pamięci. Po uruchomieniu wystąpienia SAP HANA z danymi gorąca, masz tylko 50 procent lub mniej pamięci wypełnione danymi. Pozostała część pamięci jest idealnie przechowywane dla SAP HANA wykonywania swojej pracy.
- Oznacza to, że w jednostce S192 dużych instancji HANA z 2 TB pamięci, z uruchomieniem bazy danych SAP BW, masz tylko 1 TB jako wolumin danych.
- Jeśli używasz dodatkowego węzła rozszerzenia SAP HANA opcji-1, również SKU dużych wystąpień HANA S192, zapewnia dodatkową pojemność 2 TB dla woluminu danych. W konfiguracji option-2 otrzymasz dodatkowe 4 TB dla ciepłego woluminu danych. W porównaniu do węzła gorącego, pełna pojemność pamięci "ciepłego" węzła rozszerzenia może służyć do przechowywania danych dla opcji 1. Podwójna pamięć może być używana do woluminu danych w konfiguracji węzła rozszerzenia SAP HANA option-2.
- Możesz skończyć z pojemnością 3 TB dla danych i stosunkiem 1:2 dla opcji 1. Masz 5 TB danych i stosunek 1:4 z konfiguracją węzła rozszerzenia option-2.

Im wyższy wolumin danych w porównaniu z pamięcią, tym większe szanse są, że ciepłe dane, o które prosisz, są przechowywane w magazynie dysku.

**Następne kroki**
- Odwoływanie się do [architektury SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)