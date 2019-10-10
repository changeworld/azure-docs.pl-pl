---
title: Przegląd wdrożenia — avere vFXT dla platformy Azure
description: Omówienie wdrażania avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255426"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure — omówienie wdrożenia

Ten artykuł zawiera omówienie kroków niezbędnych do uzyskania avere vFXT dla klastra platformy Azure.

Przed utworzeniem klastra vFXT z poziomu portalu Azure Marketplace potrzebne są kilka zadań. Wyraźne zrozumienie procesu rozpoczęcia do zakończenia pomoże Ci określić zakres potrzebnego nakładu pracy. 

## <a name="deployment-steps"></a>Kroki wdrażania

Po [zaplanowaniu systemu](avere-vfxt-deploy-plan.md)możesz rozpocząć tworzenie klastra avere vFXT. 

Szablon Azure Resource Manager w portalu Azure Marketplace zbiera niezbędne informacje i automatycznie wdraża cały klaster. 

Po uruchomieniu klastra vFXT i dowiesz się, jak połączyć klientów z nim i w razie potrzeby jak przenieść dane do nowego kontenera magazynu obiektów BLOB.  

Poniżej przedstawiono przegląd wszystkich kroków.

1. Konfigurowanie wymagań wstępnych 

   Przed utworzeniem maszyny wirtualnej należy utworzyć nową subskrypcję dla projektu avere vFXT, skonfigurować własność subskrypcji, sprawdzić przydziały i zażądać zwiększenia w razie potrzeby, a następnie zaakceptować warunki korzystania z oprogramowania vFXT avere. Aby uzyskać szczegółowe instrukcje, zapoznaj [się z artykułem przygotowywanie do utworzenia avere vFXT](avere-vfxt-prereqs.md) .

1. Tworzenie klastra avere vFXT 

   Skorzystaj z portalu Azure Marketplace, aby utworzyć avere vFXT dla klastra platformy Azure. Szablon zbiera wymagane informacje i wykonuje skrypty w celu utworzenia produktu końcowego.

   Tworzenie klastra obejmuje te kroki, które są wykonywane przez szablon Marketplace: 

   * Tworzenie nowej infrastruktury sieciowej i grup zasobów, w razie konieczności
   * Tworzenie *kontrolera klastra*  

     Kontroler klastra jest prostą MASZYNą wirtualną, która znajduje się w tej samej sieci wirtualnej co klaster avere vFXT i ma niestandardowe oprogramowanie, które jest konieczne do utworzenia klastra i zarządzania nim. Kontroler tworzy węzły vFXT i tworzą klaster, a także udostępnia interfejs wiersza polecenia do zarządzania klastrem w trakcie jego istnienia.

     Jeśli podczas wdrażania zostanie utworzona nowa sieć wirtualna, kontroler będzie miał publiczny adres IP. Oznacza to, że kontroler może działać jako host skoku do łączenia się z klastrem spoza sieci wirtualnej.

   * Tworzenie maszyn wirtualnych węzłów klastra

   * Konfigurowanie maszyn wirtualnych węzłów klastra w celu utworzenia klastra

   * Opcjonalnie można utworzyć nowy kontener obiektów blob i skonfigurować go jako magazyn zaplecza dla klastra

1. Konfigurowanie klastra 

   Połącz się z interfejsem avere vFXT Configuration Interface (avere Panel sterowania), aby dostosować ustawienia klastra. Zezwól na monitorowanie pomocy technicznej i Dodaj system magazynu, jeśli używasz lokalnego centrum danych.

   * [Uzyskiwanie dostępu do klastra vFXT](avere-vfxt-cluster-gui.md)
   * [Włącz obsługę](avere-vfxt-enable-support.md)
   * [Konfigurowanie magazynu](avere-vfxt-add-storage.md) (w razie konieczności)

1. Instalowanie klientów

   Postępuj zgodnie z wytycznymi w temacie [Instalowanie klastra avere vFXT](avere-vfxt-mount-clients.md) , aby dowiedzieć się więcej na temat równoważenia obciążenia i sposobu instalowania klastra przez komputery klienckie.

1. Dodaj dane (w razie konieczności)

   Ponieważ avere vFXT to skalowalna pamięć podręczna z obsługą kilku klientów, najlepszym sposobem przenoszenia danych do nowego kontenera magazynu zaplecza jest wielowątkowa strategia kopiowania plików wielowątkowych. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [dotyczący przeniesienia danych do klastra vFXT](avere-vfxt-data-ingest.md) .

## <a name="next-steps"></a>Następne kroki

Kontynuuj [Przygotowywanie do utworzenia avere vFXT](avere-vfxt-prereqs.md) , aby wykonać wstępne zadania wdrożenia vFXT avere dla platformy Azure. 