---
title: Węzły warstw i rozszerzenia danych dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Obsługa warstw i rozszerzenia węzłów danych dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5868aaf29d6b57ea608763840a08f1991a982e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628595"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Używanie węzłów warstw i rozszerzenie danych SAP HANA

SAP obsługuje model warstw danych SAP BW różne wersje oprogramowania SAP NetWeaver i SAP BW/4HANA. Więcej informacji na temat modelu warstw danych znajduje się w dokumencie SAP [SAP BW/4HANA i SAP BW on HANA, SAP HANA rozszerzenia węzłów](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Przy użyciu dużych wystąpień HANA można użyć konfiguracji opcja 1 węzłów rozszerzenia platformy SAP HANA, jak wyjaśniono w dokumentach blogu — często zadawane pytania i SAP. Opcja 2 konfiguracje można skonfigurować za pomocą następujących HANA dużych wystąpień jednostek SKU: S72m, platformie S192, platformie S192m, S384 i S384m. 

Kiedy wyświetlasz dokumentację korzyści może nie być widoczne od razu. Ale jeśli przyjrzymy się wytyczne dotyczące określania rozmiaru SAP, możesz zobaczyć zaletą przy użyciu opcji-1 i 2 opcji węzłów rozszerzenia platformy SAP HANA. Poniżej przedstawiono przykłady:

- Wytyczne dotyczące określania rozmiaru platformy SAP HANA zwykle wymagają double ilość ilość danych jako pamięć. Po uruchomieniu wystąpienia platformy SAP HANA z gorących danych mają tylko 50% lub mniej pamięci wypełniany danymi. W pozostałej części pamięci najlepiej jest przechowywany dla oprogramowania SAP HANA, wykonując pracę.
- Oznacza to w jednostce platformie HANA duże wystąpienie S192 o pojemności 2 TB pamięci, uruchomienie bazy danych SAP BW, wystarczy tylko 1 TB jako wolumin danych.
- Jeśli używasz dodatkowego węzła Rozszerzenia platformy SAP HANA — opcja 1, również platformie S192 HANA duże wystąpienie jednostki SKU, daje dodatkowej pojemności 2 TB dla woluminów danych. W konfiguracji opcji 2 dodatkowe 4 TB uzyskać ilości danych bez wyłączania zasilania. W porównaniu do węzła gorąca, pojemności pamięci pełną węzła "ciepłych" rozszerzenia może służyć do przechowywania danych, dla opcji-1. Double pamięci może służyć do ilości danych w konfiguracji węzła Rozszerzenia platformy SAP HANA — opcja 2.
- Na końcu 3 TB pojemności dla danych i współczynnika hot na ciepło 1:2 dla opcji-1. Masz 5 TB danych i współczynnika 1:4 z konfiguracją węzła Rozszerzenia opcja 2.

Im większa ilość danych w porównaniu do pamięci, tym większe prawdopodobieństwo to, że ciepło dane, które są monitowania o są przechowywane na magazyn dyskowy.

**Następne kroki**
- Zapoznaj się [architektura SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)