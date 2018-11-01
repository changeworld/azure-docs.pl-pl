---
title: Omówienie wdrażania — vFXT Avere dla platformy Azure
description: Omówienie wdrażania Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634154"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>VFXT Avere dla platformy Azure — Omówienie wdrożenia

Ten artykuł zawiera omówienie kroki niezbędne do uzyskania vFXT Avere klastra platformy Azure i uruchomione.

Podczas pierwszego wdrażania Avere vFXT systemu, można zauważyć obejmuje więcej czynności niż wdrażanie większości innych narzędzi platformy Azure. Wyczyść sensie rozpoczęcia do zakończenia procesu o pomoże Ci nakład pracy wymagany zakres. Po skonfigurowaniu i uruchomieniu systemu jego możliwości, aby przyspieszyć zadania mocą obliczeniową opartą na chmurze, spowoduje to warte włożonej pracy.

## <a name="deployment-steps"></a>Kroki wdrażania

Po [planowania systemu](avere-vfxt-deploy-plan.md), możesz rozpocząć tworzenie Avere vFXT klastra. 

Rozpocznij od utworzenia kontrolera klastra maszyny Wirtualnej, który jest używany do utworzenia klastra vFXT.

Po skonfigurowaniu i uruchomieniu klastra vFXT warto wiedzieć, jak połączyć klientów i, jeśli to konieczne, jak przenieść dane do nowego kontenera magazynu obiektów Blob.  

Poniżej przedstawiono omówienie wszystkich kroków.

1. Konfigurowanie wymagań wstępnych 

   Przed utworzeniem maszyny Wirtualnej, należy utworzyć nową subskrypcję dla projektu vFXT Avere, skonfiguruj własności subskrypcji, sprawdź limity przydziału i poprosić o zwiększenie, jeśli potrzebne i zaakceptuj postanowienia dotyczące korzystania z oprogramowania vFXT Avere. Odczyt [przed rozpoczęciem tworzenia Avere vFXT](avere-vfxt-prereqs.md) szczegółowe informacje.

1. Tworzenie kontrolera klastra

   *Klastra kontroler* jest prostą maszynę Wirtualną, która znajduje się w tej samej sieci wirtualnej jako klaster vFXT Avere. Kontroler tworzy vFXT węzłów i formularze klastra i zapewnia również interfejs wiersza polecenia do zarządzania klastrem jego okres istnienia.

   Jeśli skonfigurujesz kontroler z publicznym adresem IP również służył jako host skok do łączenia się z klastrem z spoza sieci wirtualnej.

   Całe oprogramowanie wymagane do utworzenia klastra vFXT i zarządzania jego węzłów jest preinstalowany na kontrolerze klastra.

   Odczyt [tworzenie maszyny Wirtualnej kontrolera klastra](avere-vfxt-deploy.md#create-the-cluster-controller-vm) Aby uzyskać szczegółowe informacje.

1. Tworzenie roli środowiska wykonawczego dla węzłów klastra 

   Usługa Azure korzysta [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC), aby autoryzować węzła klastra maszyny wirtualne do wykonywania określonych zadań. Na przykład węzły klastra muszą mieć możliwość przypisywania lub ponowne przypisywanie adresów IP do innych węzłów klastra. Przed utworzeniem klastra, należy zdefiniować rolę, która umożliwi im odpowiednie uprawnienia.

   Wstępnie zainstalowane oprogramowanie kontrolera klastra zawiera rolę prototypu, można go dostosować. Odczyt [tworzenie roli dostęp do węzła klastra](avere-vfxt-deploy.md#create-the-cluster-node-access-role) instrukcje.

1. Tworzenie klastra vFXT Avere 

   Na kontrolerze Przeprowadź edycję skryptu tworzenia odpowiedniego klastra, a następnie uruchomić go do utworzenia klastra. [Edytuj skrypt wdrażania](avere-vfxt-deploy.md#edit-the-deployment-script) zawiera szczegółowe instrukcje. 

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