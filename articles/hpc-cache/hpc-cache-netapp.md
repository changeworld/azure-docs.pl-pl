---
title: Korzystanie z pamięci podręcznej HPC usługi Azure i plików NetApp platformy Azure
description: Jak korzystać z pamięci podręcznej HPC usługi Azure w celu poprawy dostępu do danych przechowywanych za pomocą plików NetApp usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238678"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Korzystanie z pamięci podręcznej HPC usługi Azure za pomocą plików NetApp platformy Azure

Usługi [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) można używać jako miejsca docelowego magazynu dla pamięci podręcznej HPC platformy Azure. W tym artykule wyjaśniono, jak obie usługi mogą współpracować ze sobą i zawiera wskazówki dotyczące konfigurowania ich.

Usługa Azure NetApp Files łączy ich system operacyjny ONTAP ze skalowalnością i szybkością platformy Microsoft Azure. Ta kombinacja umożliwia użytkownikom przesunięcie ustalonych przepływów pracy do chmury bez przepisywania kodu.

Dodanie składnika usługi Azure HPC Cache może poprawić dostęp do plików, przedstawiając wiele woluminów usługi Azure NetApp Files w jednym zagregowanym obszarze nazw. Może zapewnić buforowanie krawędzi dla woluminów znajdujących się w innym regionie usługi. Może również zwiększyć wydajność na żądanie woluminów, które zostały utworzone na niższych poziomach usług, aby zaoszczędzić koszty.

## <a name="overview"></a>Omówienie

Aby użyć systemu Azure NetApp Files jako magazynu zaplecza z pamięcią podręczną HPC usługi Azure, wykonaj ten proces.

1. Utwórz system i woluminy Usługi Azure NetApp Files zgodnie ze wskazówkami zawartymi w [zaplanuj swój system poniżej](#plan-your-azure-netapp-files-system).
1. Utwórz pamięć podręczną HPC platformy Azure w regionie, w którym potrzebujesz dostępu do plików. (Użyj instrukcji w tworzenie pamięci [podręcznej HPC platformy Azure](hpc-cache-create.md).)
1. [Zdefiniuj obiekty docelowe magazynu](#create-storage-targets-in-the-cache) w pamięci podręcznej, które wskazują woluminy plików NetApp platformy Azure. Utwórz jeden cel magazynu pamięci podręcznej dla każdego unikatowego adresu IP używanego do uzyskiwania dostępu do woluminów.
1. Niech klienci [zainstalują pamięć podręczną HPC platformy Azure](#mount-storage-targets) zamiast bezpośrednio montować woluminy plików NetApp platformy Azure.

## <a name="plan-your-azure-netapp-files-system"></a>Planowanie systemu plików NetApp platformy Azure

Planując system plików NetApp Azure, zwróć uwagę na elementy w tej sekcji, aby upewnić się, że można go bezproblemowo zintegrować z pamięcią podręczną HPC usługi Azure.

Przeczytaj także [dokumentację usługi Azure NetApp Files](../azure-netapp-files/index.yml) przed utworzeniem woluminów do użycia z pamięcią podręczną HPC usługi Azure.

### <a name="nfs-client-access-only"></a>Tylko dostęp do klienta NFS

Usługa Azure HPC Cache obsługuje obecnie tylko dostęp do usługi NFS. Nie można go używać z woluminami bitowymi W TRYBIE SMB ACL lub POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Ekskluzywna podsieć dla plików NetApp platformy Azure

Usługa Azure NetApp Files używa pojedynczej podsieci delegowanej dla swoich woluminów. Żadne inne zasoby nie mogą korzystać z tej podsieci. Ponadto tylko jedna podsieć w sieci wirtualnej może służyć do usługi Azure NetApp Files. Dowiedz się więcej [w wskazówki dotyczące planowania sieci usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Rozmiar podsieci delegowanej

Podczas tworzenia systemu Azure NetApp Files do użycia z pamięcią podręczną HPC usługi Azure należy użyć minimalnego rozmiaru dla podsieci delegowanej.

Minimalny rozmiar, który jest określony za pomocą netmask /28, zawiera 16 adresów IP. W praktyce usługa Azure NetApp Files używa tylko trzech z tych dostępnych adresów IP do dostępu do woluminu. Oznacza to, że wystarczy utworzyć tylko trzy obiekty docelowe magazynu w pamięci podręcznej HPC platformy Azure, aby objąć wszystkie woluminy.

Jeśli podsieć delegowane jest zbyt duża, jest możliwe dla woluminów plików NetApp platformy Azure do użycia więcej adresów IP niż jedno wystąpienie pamięci podręcznej HPC platformy Azure może obsłużyć. Pojedyncza pamięć podręczna może mieć co najwyżej dziesięć obiektów docelowych magazynu.

Przykład przewodnika Szybki start w dokumentacji usługi Azure NetApp Files używa 10.7.0.0/16 dla podsieci delegowanej, co daje podsieci, która jest zbyt duża.

### <a name="capacity-pool-service-level"></a>Poziom usług puli pojemności

Wybierając poziom usług dla puli pojemności, należy wziąć pod uwagę przepływ pracy. Jeśli często zapisujesz dane z powrotem do woluminu usługi Azure NetApp Files, wydajność pamięci podręcznej może być ograniczona, jeśli czas stornia jest powolny. Wybierz wysoki poziom usług dla woluminów, które będą miały częste zapisy.

Woluminy o niskim poziomie usług również mogą wykazywać pewne opóźnienia na początku zadania, podczas gdy pamięć podręczna wstępnie wypełnia zawartość. Po uruchomieniu pamięci podręcznej z dobrym zestawem roboczym plików opóźnienie powinno stać się niezauważalne.

Ważne jest, aby zaplanować poziom usług puli pojemności z wyprzedzeniem, ponieważ nie można go zmienić po utworzeniu. Nowy wolumin musiałby zostać utworzony w innej puli pojemności, a dane skopiowane.

Należy zauważyć, że można zmienić przydział magazynu woluminu i rozmiar puli pojemności bez zakłócania dostępu.

## <a name="create-storage-targets-in-the-cache"></a>Tworzenie obiektów docelowych magazynu w pamięci podręcznej

Po skonfigurowaniu systemu plików NetApp platformy Azure i utworzeniu pamięci podręcznej HPC platformy Azure należy zdefiniować obiekty docelowe magazynu w pamięci podręcznej, które wskazują woluminy systemu plików.

Utwórz jeden cel magazynu dla każdego adresu IP używanego przez woluminy plików NetApp azure. Adres IP jest wymieniony na stronie instrukcji montażu woluminu.

Jeśli wiele woluminów współużytkuje ten sam adres IP, można użyć jednego miejsca docelowego magazynu dla wszystkich z nich.  

Postępuj zgodnie z [instrukcjami instalacji w dokumentacji usługi Azure NetApp Files,](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) aby znaleźć adresy IP do użycia.

Można również znaleźć adresy IP za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Nazwy eksportu w systemie Azure NetApp Files mają składnik pojedynczej ścieżki. Nie próbuj tworzyć miejsca docelowego magazynu ``/`` dla eksportu głównego w usłudze Azure NetApp Files, ponieważ ten eksport nie zapewnia dostępu do plików.

Nie ma żadnych specjalnych ograniczeń dotyczących ścieżek wirtualnego obszaru nazw dla tych obiektów docelowych magazynu.

## <a name="mount-storage-targets"></a>Narzędzia pamięci masowej montowania

Komputery klienckie należy zainstalować pamięci podręcznej zamiast instalowania woluminów usługi Azure NetApp Files bezpośrednio. Postępuj zgodnie z instrukcjami w [instalacji pamięci podręcznej HPC platformy Azure](hpc-cache-mount.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o konfigurowaniu i używaniu [plików NetApp usługi Azure](../azure-netapp-files/index.yml)
* Aby uzyskać pomoc w planowaniu i konfigurowaniu systemu pamięci podręcznej HPC usługi Azure do korzystania z plików NetApp azure, [skontaktuj się z pomocą techniczną](hpc-cache-support-ticket.md).
