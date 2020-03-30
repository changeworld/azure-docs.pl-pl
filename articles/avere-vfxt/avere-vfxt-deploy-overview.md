---
title: Omówienie wdrażania — Avere vFXT for Azure
description: Omówienie wdrażania avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153687"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure — omówienie wdrażania

W tym artykule przedstawiono omówienie kroków potrzebnych do uruchomienia klastra Avere vFXT for Azure.

Przed utworzeniem klastra vFXT i po jego utworzeniu z portalu Azure Marketplace jest potrzebnych kilka zadań. Posiadanie jasnego poczucia procesu od początku do końca pomoże Ci w zakresie wysiłku potrzebnego.

## <a name="deployment-steps"></a>Kroki wdrażania

Po [zaplanowaniu systemu](avere-vfxt-deploy-plan.md)można rozpocząć tworzenie klastra Avere vFXT.

Szablon usługi Azure Resource Manager w portalu Azure Marketplace zbiera niezbędne informacje i automatycznie wdraża cały klaster.

Po uruchomieniu klastra vFXT przed jego użyciem nadal istnieją pewne kroki konfiguracji. Jeśli utworzono nowy kontener magazynu obiektów Blob, należy przenieść dane do niego. Jeśli używasz systemu pamięci masowej NAS, należy go dodać po utworzeniu klastra. Należy połączyć klientów z klastrem.

Oto przegląd wszystkich kroków.

1. Konfigurowanie wymagań wstępnych

   Przed utworzeniem maszyny Wirtualnej należy utworzyć nową subskrypcję dla projektu VFXT Avere, skonfigurować własność subskrypcji, sprawdzić przydziały i zażądać zwiększenia w razie potrzeby oraz zaakceptować warunki korzystania z oprogramowania VFXT Avere. Przeczytaj [instrukcje Przygotuj się do utworzenia avere vFXT,](avere-vfxt-prereqs.md) aby uzyskać szczegółowe instrukcje.

1. Tworzenie klastra VFXT Avere

   Użyj portalu Azure Marketplace, aby utworzyć klaster Avere vFXT dla platformy Azure. Szablon zbiera wymagane informacje i wykonuje skrypty w celu utworzenia produktu końcowego.

   Tworzenie klastra obejmuje następujące kroki, które są wykonywane przez szablon portalu marketplace:

   * W razie potrzeby tworzenie nowej infrastruktury sieciowej i grup zasobów
   * Tworzenie kontrolera klastra

     Kontroler klastra jest prostą maszyną wirtualną, która znajduje się w tej samej sieci wirtualnej co klaster VFXT Avere i ma niestandardowe oprogramowanie potrzebne do tworzenia klastra i zarządzania nim. Kontroler tworzy węzły vFXT i tworzy klaster, a także udostępnia interfejs wiersza polecenia do zarządzania klastrem w okresie jego istnienia.

     Jeśli utworzysz nową sieć wirtualną lub podsieć podczas wdrażania, kontroler będzie miał publiczny adres IP. Oznacza to, że kontroler może służyć jako host przeskoku do łączenia się z klastrem spoza sieci wirtualnej.

   * Tworzenie maszyn wirtualnych węzła klastra

   * Tworzenie klastra z poszczególnych węzłów

   * Opcjonalnie należy utworzyć nowy kontener obiektów Blob i skonfigurować go jako magazyn zaplecza dla klastra

   Tworzenie klastra jest szczegółowo opisane w [udrożeniu klastra vFXT](avere-vfxt-deploy.md).

1. Konfigurowanie klastra

   Połącz się z interfejsem konfiguracyjnym Avere vFXT (Avere Control Panel), aby dostosować ustawienia klastra. Zapisz się do monitorowania pomocy technicznej i dodaj system magazynu, jeśli używasz magazynu sprzętowego lub dodatkowych kontenerów obiektów blob.

   * [Uzyskiwanie dostępu do klastra vFXT](avere-vfxt-cluster-gui.md)
   * [Włącz obsługę](avere-vfxt-enable-support.md)
   * [Konfigurowanie pamięci masowej](avere-vfxt-add-storage.md) (w razie potrzeby)

1. Instalowanie klientów

   Postępuj zgodnie z wytycznymi w [instalacji klastra VFXT Avere,](avere-vfxt-mount-clients.md) aby dowiedzieć się więcej o równoważeniu obciążenia i jak komputery klienckie powinny montować klaster.

1. Dodawanie danych (w razie potrzeby)

   Ponieważ Avere vFXT jest skalowalną pamięcią podręczną wielu klientów, najlepszym sposobem przenoszenia danych do nowego kontenera magazynu zaplecza jest strategia kopiowania plików wielowątkowych z wieloma klientami.

   Jeśli chcesz przenieść dane zestawu roboczego do nowego kontenera obiektów Blob lub innego systemu magazynu zaplecza, postępuj zgodnie z instrukcjami w [: Przenoszenie danych do klastra vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Następne kroki

Kontynuuj [przygotowywanie do utworzenia avere vFXT,](avere-vfxt-prereqs.md) aby wykonać zadania wymagane.
