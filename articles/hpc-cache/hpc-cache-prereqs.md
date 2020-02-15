---
title: Wymagania wstępne dotyczące pamięci podręcznej platformy Azure HPC
description: Wymagania wstępne dotyczące korzystania z pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rohogue
ms.openlocfilehash: 135c231f84d95ea2418fab4647d715473378e41c
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251961"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Wymagania wstępne dotyczące usługi Azure HPC cache

Przed rozpoczęciem korzystania z Azure Portal tworzenia nowej pamięci podręcznej platformy Azure HPC upewnij się, że środowisko spełnia te wymagania.

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Zalecana jest płatna subskrypcja.

> [!NOTE]
> W ciągu kilku pierwszych miesięcy zespołu pamięci podręcznej platformy Azure HPC należy dodać subskrypcję do listy dostępu, zanim będzie można jej użyć do utworzenia wystąpienia pamięci podręcznej. Ta procedura pomaga zapewnić, że każdy klient uzyskuje wysoką jakość odpowiedzi z pamięci podręcznej. Wypełnij [ten formularz](https://aka.ms/onboard-hpc-cache) , aby zażądać dostępu.

## <a name="network-infrastructure"></a>Infrastruktura sieciowa

Aby można było korzystać z pamięci podręcznej, należy skonfigurować dwa wymagania wstępne związane z siecią:

* Dedykowana podsieć dla wystąpienia pamięci podręcznej platformy Azure HPC
* Obsługa systemu DNS, aby pamięć podręczna mogła uzyskać dostęp do magazynu i innych zasobów

### <a name="cache-subnet"></a>Podsieć pamięci podręcznej

Pamięć podręczna Azure HPC wymaga dedykowanej podsieci z następującymi jakościami:

* Podsieć musi mieć co najmniej 64 adresów IP.
* Podsieć nie może obsługiwać żadnych innych maszyn wirtualnych, nawet w przypadku powiązanych usług takich jak komputery klienckie.
* W przypadku korzystania z wielu wystąpień pamięci podręcznej platformy Azure HPC każda z nich musi mieć własną podsieć.

Najlepszym rozwiązaniem jest utworzenie nowej podsieci dla każdej pamięci podręcznej. W ramach tworzenia pamięci podręcznej można utworzyć nową sieć wirtualną i podsieć.

### <a name="dns-access"></a>Dostęp do usługi DNS

Pamięć podręczna potrzebuje systemu DNS, aby uzyskać dostęp do zasobów poza jego siecią wirtualną. W zależności od zasobów, z których korzystasz, może być konieczne skonfigurowanie niestandardowego serwera DNS i skonfigurowanie przekierowania między tym serwerem i serwerami Azure DNS:

* Aby uzyskać dostęp do punktów końcowych usługi Azure Blob Storage i innych zasobów wewnętrznych, wymagany jest serwer DNS oparty na platformie Azure.
* Aby uzyskać dostęp do magazynu lokalnego, należy skonfigurować niestandardowy serwer DNS, który może rozpoznawać nazwy hostów magazynu.

Jeśli potrzebujesz dostępu do usługi BLOB Storage, możesz użyć domyślnego serwera DNS z systemem Azure dla pamięci podręcznej. Jeśli jednak potrzebujesz dostępu do innych zasobów, należy utworzyć niestandardowy serwer DNS i skonfigurować go tak, aby przesyłał do serwera Azure DNS wszystkie żądania rozpoznawania specyficzne dla platformy Azure.

Prosty serwer DNS może być również używany do równoważenia obciążenia połączeń klientów między wszystkimi dostępnymi punktami instalacji pamięci podręcznej.

Dowiedz się więcej na temat sieci wirtualnych platformy Azure i konfiguracji serwera DNS w temacie [rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Uprawnienia

Przed rozpoczęciem tworzenia pamięci podręcznej Sprawdź te wymagania wstępne związane z uprawnieniami.

* Wystąpienie pamięci podręcznej musi być w stanie tworzyć wirtualne interfejsy sieciowe (nic). Użytkownik tworzący pamięć podręczną musi mieć wystarczające uprawnienia w subskrypcji, aby można było tworzyć karty sieciowe.

* W przypadku korzystania z usługi BLOB Storage pamięć podręczna Azure HPC wymaga autoryzacji dostępu do konta magazynu. Aby zapewnić dostęp do pamięci podręcznej do magazynu obiektów blob, użyj kontroli dostępu opartej na rolach (RBAC). Wymagane są dwie role: współautor konta magazynu i współautor danych obiektu blob magazynu.

  Postępuj zgodnie z instrukcjami w temacie [Dodawanie elementów docelowych magazynu](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) , aby dodać role.

## <a name="storage-infrastructure"></a>Infrastruktura magazynu

Pamięć podręczna obsługuje kontenery obiektów blob platformy Azure lub eksporty magazynu sprzętowego systemu plików NFS. Dodaj elementy docelowe magazynu po utworzeniu pamięci podręcznej.

Każdy typ magazynu ma określone wymagania wstępne.

### <a name="blob-storage-requirements"></a>Wymagania dotyczące magazynu obiektów BLOB

Jeśli chcesz używać usługi Azure Blob Storage z pamięcią podręczną, potrzebujesz zgodnego konta magazynu i pustego kontenera obiektów blob lub kontenera, który jest wypełniony danymi z pamięci podręcznej platformy Azure HPC, zgodnie z opisem w sekcji [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

Utwórz konto przed podjęciem próby dodania miejsca docelowego magazynu. Nowy kontener można utworzyć po dodaniu obiektu docelowego.

Aby utworzyć zgodne konto magazynu, użyj następujących ustawień:

* Wydajność: **standardowa**
* Rodzaj konta: **StorageV2 (ogólnego przeznaczenia w wersji 2)**
* Replikacja: **Magazyn lokalnie nadmiarowy (LRS)**
* Warstwa dostępu (domyślna): **gorąca**

Dobrym sposobem jest użycie konta magazynu w tej samej lokalizacji co pamięć podręczna.
<!-- clarify location - same region or same resource group or same virtual network? -->

Należy również nadać aplikacji pamięci podręcznej dostęp do konta usługi Azure [Storage, jak](#permissions)wspomniano powyżej. Postępuj zgodnie z procedurą w temacie [Dodawanie miejsc docelowych](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) w celu nadania pamięci podręcznej wymaganych ról dostępu. Jeśli nie jesteś właścicielem konta magazynu, jego właścicielem jest ten krok.

### <a name="nfs-storage-requirements"></a>Wymagania dotyczące magazynu NFS

W przypadku korzystania z systemu magazynu NFS (na przykład lokalnego sprzętowego serwera NAS) Upewnij się, że spełnia on te wymagania. Aby sprawdzić te ustawienia, może być konieczne skontaktowanie się z administratorami sieci lub menedżerami zapory dla systemu magazynu (lub centrum danych).

> [!NOTE]
> Tworzenie miejsca docelowego magazynu zakończy się niepowodzeniem, jeśli pamięć podręczna ma niewystarczający dostęp do systemu magazynu NFS.

* **Łączność sieciowa:** Pamięć podręczna Azure HPC wymaga dostępu do sieci o wysokiej przepustowości między podsiecią pamięci podręcznej a centrum danych systemu plików NFS. Zalecany jest [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) lub podobny dostęp. W przypadku korzystania z sieci VPN może być konieczne skonfigurowanie jej w celu zablokowania protokołu TCP o rozmiarze 1350, aby upewnić się, że duże pakiety nie są blokowane.

* **Dostęp do portów:** Pamięć podręczna musi mieć dostęp do określonych portów TCP/UDP w systemie magazynu. Różne typy magazynów mają różne wymagania dotyczące portów.

  Aby sprawdzić ustawienia systemu magazynu, wykonaj poniższą procedurę.

  * Wydaj polecenie `rpcinfo` w systemie magazynu, aby sprawdzić, czy są konieczne. Poniższe polecenie wyświetla listę portów i formatuje odpowiednie wyniki w tabeli. (Użyj adresu IP systemu zamiast *< storage_IP >* termin).

    To polecenie można wydać z dowolnego klienta systemu Linux z zainstalowaną infrastrukturą NFS. Jeśli używasz klienta w podsieci klastra, może on również pomóc w sprawdzeniu łączności między podsiecią a systemem magazynu.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  * Oprócz portów zwracanych przez polecenie `rpcinfo` upewnij się, że te najczęściej używane porty zezwalają na ruch przychodzący i wychodzący:

    | Protokół | Port  | NDES  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | Instalacja   |
    | TCP/UDP  | 4047  | stan   |

  * Sprawdź ustawienia zapory, aby upewnić się, że zezwalają na ruch na wszystkich wymaganych portach. Pamiętaj, aby sprawdzić zapory używane na platformie Azure oraz zapory lokalne w centrum danych.

* **Dostęp do katalogu:** Włącz `showmount` polecenie w systemie magazynu. Pamięć podręczna Azure HPC korzysta z tego polecenia, aby sprawdzić, czy konfiguracja docelowa magazynu wskazuje na prawidłowy eksport, oraz upewnić się, że wiele instalacji nie uzyskuje dostępu do tych samych podkatalogów (co wiąże się z ryzykiem konfliktów plików).

  > [!NOTE]
  > Jeśli system magazynu NFS używa systemu operacyjnego NetApp ONTAP 9,2, nie należy **włączać `showmount`** . [Skontaktuj się z pomocą techniczną firmy Microsoft,](hpc-cache-support-ticket.md) Aby uzyskać pomoc.

* **Dostęp do katalogu głównego:** Pamięć podręczna łączy się z systemem zaplecza jako identyfikator użytkownika 0. Sprawdź te ustawienia w systemie magazynu:
  
  * Włącz `no_root_squash`. Ta opcja zapewnia, że zdalny użytkownik główny może uzyskać dostęp do plików należących do katalogu głównego.

  * Zaznacz pole wyboru Eksportuj zasady, aby upewnić się, że nie obejmują one ograniczeń dotyczących dostępu głównego z podsieci pamięci podręcznej.

* Magazyn zaplecza systemu plików NFS musi być zgodnym sprzętem/platformą oprogramowania. Aby uzyskać szczegółowe informacje, skontaktuj się z zespołem usługi Azure HPC cache.

## <a name="next-steps"></a>Następne kroki

* [Utwórz wystąpienie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md) na podstawie Azure Portal
