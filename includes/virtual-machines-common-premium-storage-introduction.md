---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74005589"
---
Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Można użyć instrukcji zawartych w tym dokumencie w połączeniu z najlepszymi praktykami dotyczącymi wydajności mającymi zastosowanie do technologii używanych przez aplikację. Aby zilustrować wskazówki, użyliśmy SQL Server uruchomiony w magazynie w jakości Premium jako przykład w tym dokumencie.

Podczas gdy zajmujemy się scenariuszami wydajności dla warstwy magazynowania w tym artykule, należy zoptymalizować warstwę aplikacji. Na przykład jeśli hostujesz farmę programu SharePoint w usłudze Azure Premium Storage, możesz użyć przykładów programu SQL Server z tego artykułu, aby zoptymalizować serwer bazy danych. Ponadto należy zoptymalizować serwer sieci Web i serwer aplikacji usługi SharePoint Farm, aby uzyskać jak największą wydajność.

Ten artykuł pomoże odpowiedzieć na poniższe typowe pytania dotyczące optymalizacji wydajności aplikacji w usłudze Azure Premium Storage,

* Jak mierzyć wydajność aplikacji?  
* Dlaczego nie widzisz oczekiwanej wysokiej wydajności?  
* Jakie czynniki wpływają na wydajność aplikacji w magazynie w wersji Premium?  
* Jak te czynniki wpływają na wydajność aplikacji w magazynie w wersji Premium?  
* Jak można zoptymalizować dla IOPS, przepustowość i opóźnienie?  

Udostępniliśmy te wytyczne specjalnie dla magazynu w wersji Premium, ponieważ obciążenia działające w magazynie w wersji Premium są bardzo wrażliwe na wydajność. W stosownych przypadkach podaliśmy przykłady. Niektóre z tych wskazówek można również zastosować do aplikacji uruchomionych na maszynach wirtualnych IaaS z dyskami magazynu standardowego.