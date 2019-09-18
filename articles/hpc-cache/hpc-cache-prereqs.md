---
title: Wymagania wstępne dotyczące pamięci podręcznej platformy Azure HPC
description: Wymagania wstępne dotyczące korzystania z pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 50c60e38b58815be04cfb892c3622b9579529e67
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036870"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Wymagania wstępne dotyczące usługi Azure HPC cache

Przed rozpoczęciem korzystania z Azure Portal tworzenia nowej pamięci podręcznej platformy Azure HPC upewnij się, że środowisko spełnia te wymagania.

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Zalecana jest płatna subskrypcja.

> [!NOTE]
> W wersji zapoznawczej zespół pamięci podręcznej platformy Azure HPC musi dodać subskrypcję do listy dostępu, zanim będzie można jej użyć do utworzenia wystąpienia pamięci podręcznej. Ta procedura pomaga zapewnić, że każdy klient uzyskuje czas odpowiedzi o wysokiej jakości z ich pamięci podręcznej testów. Wypełnij [ten formularz](https://aka.ms/onboard-hpc-cache) , aby zażądać dostępu.

## <a name="network-infrastructure"></a>Infrastruktura sieciowa

Aby można było korzystać z pamięci podręcznej, należy skonfigurować dwie opcje związane z siecią:

* Dedykowana podsieć dla wystąpienia pamięci podręcznej platformy Azure HPC
* Obsługa systemu DNS, aby pamięć podręczna mogła uzyskać dostęp do magazynu i innych zasobów

### <a name="cache-subnet"></a>Podsieć pamięci podręcznej

Pamięć podręczna Azure HPC wymaga dedykowanej podsieci z następującymi jakościami:

* Podsieć musi mieć co najmniej 64 adresów IP.
* Podsieć nie może obsługiwać żadnych innych maszyn wirtualnych, nawet w przypadku powiązanych usług takich jak komputery klienckie.
* W przypadku korzystania z wielu wystąpień pamięci podręcznej każdy z nich musi mieć własną podsieć.

Najlepszym rozwiązaniem jest utworzenie nowej podsieci dla pamięci podręcznej. W ramach tworzenia pamięci podręcznej można utworzyć nową sieć wirtualną i podsieć.

### <a name="dns-access"></a>Dostęp do usługi DNS

Pamięć podręczna platformy Azure HPC wymaga systemu DNS, aby uzyskać dostęp do zasobów spoza sieci wirtualnej. W zależności od zasobów, z których korzystasz, może być konieczne skonfigurowanie niestandardowego serwera DNS i skonfigurowanie przekierowania między tym serwerem i serwerami Azure DNS: 

* Aby uzyskać dostęp do punktów końcowych usługi Azure Blob Storage i innych zasobów wewnętrznych, wymagany jest serwer DNS oparty na platformie Azure.
* Aby uzyskać dostęp do magazynu lokalnego, należy skonfigurować niestandardowy serwer DNS, który może rozpoznawać nazwy hostów magazynu.

Jeśli potrzebujesz dostępu do usługi BLOB Storage, możesz użyć domyślnego serwera DNS z systemem Azure dla pamięci podręcznej. Jeśli jednak potrzebujesz dostępu do innych zasobów, należy utworzyć niestandardowy serwer DNS i skonfigurować go tak, aby przesyłał do serwera Azure DNS wszystkie żądania rozpoznawania specyficzne dla platformy Azure. (Prosty serwer DNS może być również używany do równoważenia obciążenia połączeń klientów między wszystkimi dostępnymi punktami instalacji pamięci podręcznej).

Dowiedz się więcej na temat sieci wirtualnych platformy Azure i konfiguracji serwera DNS w temacie [rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Uprawnienia

Przed rozpoczęciem tworzenia pamięci podręcznej Sprawdź te wymagania wstępne związane z uprawnieniami.

* Pamięć podręczna Azure HPC musi mieć możliwość tworzenia wirtualnych interfejsów sieciowych (nic). Użytkownik tworzący pamięć podręczną musi mieć wystarczające uprawnienia w subskrypcji, aby można było tworzyć karty sieciowe.
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* W przypadku korzystania z magazynu obiektów BLOB wystąpienie pamięci podręcznej platformy Azure HPC wymaga autoryzacji dostępu do konta magazynu. Za pomocą kontroli dostępu opartej na rolach (RBAC) można zapewnić dostęp do pamięci podręcznej do magazynu obiektów BLOB. Wymagane są dwie role: Współautor konta magazynu oraz współautor danych obiektu blob magazynu. Postępuj zgodnie z instrukcjami w temacie [Dodawanie magazynu do pamięci podręcznej](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account).

## <a name="storage-infrastructure"></a>Infrastruktura magazynu

Pamięć podręczna obsługuje kontenery obiektów blob platformy Azure lub eksporty magazynu sprzętowego systemu plików NFS. Podczas tworzenia pamięci podręcznej można zdefiniować cele magazynu, ale można je również dodać później. 

### <a name="nfs-storage-requirements"></a>Wymagania dotyczące magazynu NFS

W przypadku korzystania z lokalnego magazynu sprzętu pamięć podręczna musi mieć dostęp sieciowy o wysokiej przepustowości do centrum danych z podsieci. Zalecany jest [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) lub podobny dostęp.

Magazyn zaplecza systemu plików NFS musi być zgodnym sprzętem/platformą oprogramowania. Aby uzyskać szczegółowe informacje, skontaktuj się z zespołem usługi Azure HPC cache.

### <a name="blob-storage-requirements"></a>Wymagania dotyczące magazynu obiektów BLOB

Jeśli chcesz używać usługi Azure Blob Storage z pamięcią podręczną Azure HPC, potrzebujesz zgodnego konta magazynu i pustego kontenera obiektów blob lub kontenera, który jest wypełniony danymi z pamięci podręcznej platformy Azure HPC, zgodnie z opisem w sekcji [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

Utwórz konto i kontener przed podjęciem próby dodania go jako miejsca docelowego magazynu.

Aby utworzyć zgodne konto magazynu, użyj następujących ustawień:

* Skuteczności **Standardowa**
* Rodzaj konta: **StorageV2 (ogólnego przeznaczenia w wersji 2)**
* Replikacji **Magazyn lokalnie nadmiarowy (LRS)**
* Warstwa dostępu (domyślna): **gorąca**

Dobrym sposobem jest użycie konta magazynu w tej samej lokalizacji co pamięć podręczna.

Należy również udzielić aplikacji pamięci podręcznej dostępu do konta usługi Azure Storage. Postępuj zgodnie z opisem w temacie [Dodawanie magazynu do pamięci podręcznej](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) , aby uzyskać pamięć podręczną współautora i magazyn danych obiektów BLOB konta magazynu ról dostępu. Jeśli nie jesteś właścicielem konta magazynu, jego właścicielem jest ten krok.

## <a name="next-steps"></a>Następne kroki

* [Utwórz wystąpienie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md) na podstawie Azure Portal
