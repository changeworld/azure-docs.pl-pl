---
title: Omówienie wdrażania — vFXT Avere dla platformy Azure
description: Omówienie wdrażania Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 1be11fff7139b250e85fe15cec9082a2c85cf857
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298538"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>VFXT Avere dla platformy Azure — Omówienie wdrożenia

Ten artykuł zawiera omówienie kroki niezbędne do uzyskania vFXT Avere klastra platformy Azure i uruchomione.

Niektóre zadania są wymagane, przed i po utworzeniu klastra vFXT w portalu Azure Marketplace. Wyczyść sensie rozpoczęcia do zakończenia procesu o pomoże Ci nakład pracy wymagany zakres. 

## <a name="deployment-steps"></a>Kroki wdrażania

Po [planowania systemu](avere-vfxt-deploy-plan.md), możesz rozpocząć tworzenie Avere vFXT klastra. 

Szablon usługi Azure Resource Manager w witrynie Azure Marketplace zbiera niezbędne informacje i automatycznie wdraża całego klastra. 

Po skonfigurowaniu i uruchomieniu klastra vFXT warto wiedzieć, jak połączyć klientów i, jeśli to konieczne, jak przenieść dane do nowego kontenera magazynu obiektów Blob.  

Poniżej przedstawiono omówienie wszystkich kroków.

1. Konfigurowanie wymagań wstępnych 

   Przed utworzeniem maszyny Wirtualnej, należy utworzyć nową subskrypcję dla projektu vFXT Avere, skonfiguruj własności subskrypcji, sprawdź limity przydziału i poprosić o zwiększenie, jeśli potrzebne i zaakceptuj postanowienia dotyczące korzystania z oprogramowania vFXT Avere. Odczyt [przed rozpoczęciem tworzenia Avere vFXT](avere-vfxt-prereqs.md) szczegółowe informacje.

1. Utworzenie roli dostępu w węzłach klastra

   Usługa Azure korzysta [kontroli dostępu opartej na rolach](../role-based-access-control/index.yml) (RBAC), aby autoryzować węzła klastra maszyny wirtualne do wykonywania określonych zadań. Na przykład węzły klastra muszą mieć możliwość przypisywania lub ponowne przypisywanie adresów IP do innych węzłów klastra. Przed utworzeniem klastra, należy zdefiniować rolę, która umożliwi im odpowiednie uprawnienia.

   Odczyt [tworzenie roli dostęp do węzła klastra](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) instrukcje.

   Kontroler klastra używa również rola dostępu, ale możesz zaakceptować domyślna rola właściciela, zamiast tworzyć własne. Jeśli chcesz utworzyć niestandardową rolę kontrolera klastra, zapoznaj się z [roli dostęp do kontrolera dostosowany](avere-vfxt-controller-role.md). 

1. Tworzenie klastra vFXT Avere 

   Użyj portalu Azure Marketplace, aby utworzyć vFXT Avere klastra platformy Azure. Szablon służy do zbierania wymaganych informacji i wykonuje skrypty w celu utworzenia produktu końcowego.

   Tworzenie klastra obejmuje następujące kroki, które są wszystkie wykonywane tylko przez szablon witryny marketplace: 

   * Tworzenie nowej sieci infrastruktury i grup zasobów, jeśli to konieczne
   * Tworzenie *kontrolera klastra*  

     Kontroler klastra jest prostą maszynę Wirtualną, która znajduje się w tej samej sieci wirtualnej jako klaster vFXT Avere i ma niestandardowe oprogramowanie wymagane do tworzenia i zarządzania klastrem. Kontroler tworzy vFXT węzłów i formularze klastra i zapewnia również interfejs wiersza polecenia do zarządzania klastrem jego okres istnienia.

     Jeśli skonfigurujesz kontroler z publicznym adresem IP również służył jako host skok do łączenia się z klastrem z spoza sieci wirtualnej.

   * Tworzenie klastra maszyn wirtualnych węzła
   * Konfigurowanie maszyn wirtualnych węzłów klastra jako klastra
   * Opcjonalnie, tworzeniu nowego kontenera obiektów Blob i skonfigurowania go jako magazynu zaplecza dla klastra

1. Konfigurowanie klastra 

   Nawiązać Avere vFXT konfiguracji interfejsu (Panel sterowania Avere) aby dostosować ustawienia klastra. Zgoda na obsługę monitorowania i Dodaj systemie magazynu, korzystając z centrum danych w środowisku lokalnym.

   * [Dostęp do klastra vFXT](avere-vfxt-cluster-gui.md)
   * [Włącz obsługę](avere-vfxt-enable-support.md)
   * [Konfigurowanie magazynu](avere-vfxt-add-storage.md) (jeśli jest to konieczne)

1. Instalowanie klientów

   Postępuj zgodnie z wytycznymi podanymi w [instalacji klastra vFXT Avere](avere-vfxt-mount-clients.md) informacje na temat równoważenia obciążenia oraz jak komputerów klienckich należy zainstalować klastra.

1. Dodawanie danych (jeśli jest to konieczne)

   Ponieważ Avere vFXT jest skalowalna pamięć podręczna wielu klientów, najlepszym sposobem, aby przenieść dane do nowego kontenera magazynu zaplecza jest ze strategią kopiowanie wielu klientów, wielowątkowy plik. Odczyt [przenoszenia danych do klastra vFXT](avere-vfxt-data-ingest.md) Aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki

W dalszym ciągu [przed rozpoczęciem tworzenia Avere vFXT](avere-vfxt-prereqs.md) do wykonania zadań wstępne dotyczące wdrażania Avere vFXT dla platformy Azure. 