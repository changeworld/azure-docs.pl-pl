---
title: Zainstaluj Avere vFXT - Azure
description: Jak zainstalować klientów przy użyciu Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409385"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Instalowanie klastra Avere vFXT  

Wykonaj następujące kroki, aby połączyć komputery klienckie z klastrem vFXT.

1. Zdecyduj, jak Równoważenie obciążenia ruchu klientów między węzły klastra. Odczyt [Równoważenie obciążenia klienta](#balance-client-load)poniżej, aby uzyskać szczegółowe informacje. 
1. Zidentyfikuj adresów IP i Rozgałęzienie ścieżek do zainstalowania.
1. Problem [polecenie instalacji](#mount-command-arguments), z odpowiednimi argumentami.

## <a name="balance-client-load"></a>Równoważenie obciążenia klienta

Aby ułatwić równoważenie żądań klientów między wszystkie węzły w klastrze, należy zainstalować klientów do pełnego zakresu adresów IP ukierunkowane na klienta. Istnieje kilka prostych sposobów automatyzacji tego zadania.

> [!TIP] 
> Inne metody równoważenia obciążenia może być odpowiednie dla dużych lub złożonych systemów; [Otwórz bilet pomocy technicznej](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) celu uzyskania pomocy.)
> 
> Jeśli wolisz używać serwera DNS dla równoważenia obciążenia usługi automatycznego po stronie serwera, musisz skonfigurować i zarządzać swoim własnym serwerze DNS w systemie Azure. W takim przypadku można skonfigurować DNS okrężnego vFXT klastra zgodnie z tym dokumencie: [Konfiguracja DNS klastra Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Przykładowy klient o zrównoważonym obciążeniu instalowania skryptu

Ten przykład kodu wykorzystuje adresów IP klientów jako randomizing element do dystrybucji klientów dla wszystkich dostępnych adresów IP klastra vFXT.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

Funkcja powyżej znajduje się przykład usługi Batch dostępnych w [Avere vFXT przykłady](https://github.com/Azure/Avere#tutorials) lokacji.

## <a name="create-the-mount-command"></a>Utwórz polecenie instalacji 

> [!NOTE]
> Jeśli nie utworzono nowy kontener obiektów Blob podczas tworzenia klastra vFXT Avere, wykonaj kroki opisane w [skonfigurować magazyn](avere-vfxt-add-storage.md) przed przystąpieniem do instalacji klientów.

Z komputera klienckiego z ``mount`` polecenie mapuje serwera wirtualnego (vserver) w klastrze vFXT na ścieżkę w lokalnym systemie plików. Format jest ``mount <vFXT path> <local path> {options}``

Istnieją trzy elementy do polecenia instalacji: 

* Ścieżka vFXT - (kombinację adresów IP i nazw Rozgałęzienie ścieżek opisanych poniżej)
* Ścieżka lokalna — ścieżka na komputerze klienckim 
* Opcje polecenia - instalacji (wymienione w [argumenty wiersza polecenia instalacji](#mount-command-arguments))

### <a name="junction-and-ip"></a>Połączenia i adres IP

Ścieżka vserver składa się z jego *adresu IP* oraz ścieżkę do *przestrzeni nazw połączeń*. Połączenie przestrzeni nazw jest ścieżką wirtualną, która została zdefiniowana, gdy system magazynowania została dodana.

Jeśli klaster został utworzony za pomocą magazynu obiektów Blob, ścieżka obszaru nazw jest `/msazure`

Przykład: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Jeśli po utworzeniu klastra został dodany magazyn, ścieżki Rozgałęzienie przestrzeni nazw odpowiada wartość ustawiona w **ścieżki Namespace** podczas tworzenia połączenia. Na przykład, jeśli użyto ``/avere/files`` jako ścieżki obszaru nazw, instalująca klientów *adres_IP*: / avere/pliki do ich punktu instalacji lokalnej.

!["Dodaj nowe połączenie" okno dialogowe z plikami/avere/w polu Ścieżka przestrzeni nazw](media/avere-vfxt-create-junction-example.png)


Adres IP jest jeden z adresów IP ukierunkowane na klienta, które są zdefiniowane dla vserver. Można znaleźć w zakresie ukierunkowane na klienta adresów IP w dwóch miejscach w Panelu sterowania Avere:

* **VServers** tabeli (karta pulpitu nawigacyjnego) — 

  ![Karta pulpitu nawigacyjnego w Panelu sterowania Avere kartą VServer wybrane w tabeli danych poniżej wykresu i sekcja adresu IP w kółkach](media/avere-vfxt-ip-addresses-dashboard.png)

* **Klient sieci skierowany** strony Ustawienia - 

  ![Ustawienia > VServer > strony konfiguracji klienta sieci skierowany w kółku sekcji zakres adresów w tabeli dla konkretnego vserver](media/avere-vfxt-ip-addresses-settings.png)

Oprócz ścieżek, obejmują [argumenty wiersza polecenia instalacji](#mount-command-arguments) opisane poniżej, w przypadku instalowania poszczególnych klientów.

### <a name="mount-command-arguments"></a>Argumenty wiersza polecenia instalacji

W celu zapewnienia instalacji klienta bezproblemowe, należy przekazać te ustawienia i argumenty polecenia instalacji: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Wymagane ustawienia | |
--- | --- 
``hard`` | Instalacji miękkich klastrowi vFXT są skojarzone z błędów aplikacji i możliwej utracie danych. 
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieciowych systemu plików NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci dla operacji instalacji.
``retry=n`` | Ustaw ``retry=30`` w celu uniknięcia błędów przejściowych instalacji. (Inną wartość jest zalecane w instaluje pierwszego planu).

| Preferowanych ustawień  | |
--- | --- 
``nointr``            | Opcja "nointr" jest preferowane dla klientów przy użyciu starszego jądra (przed kwietnia 2008), które obsługują tę opcję. Należy pamiętać, że opcja "Grupa" jest ustawieniem domyślnym.


## <a name="next-steps"></a>Kolejne kroki

Masz zainstalowany klientów, można używane do wypełniania wewnętrznej bazy danych magazynu danych (filtr core). Odwoływać się do tych dokumentów, aby dowiedzieć się więcej o zadaniach dodatkowe ustawienia:

* [Przenoszenie danych do klastra filtr core](avere-vfxt-data-ingest.md) — jak wydajnie przekazywanie danych za pomocą wielu klientów i wątków
* [Dostosowywanie klastra dostrajania](avere-vfxt-tuning.md) — Dostosowywanie ustawień klastra do własnych obciążenia
* [Zarządzanie klastrem](avere-vfxt-manage-cluster.md) — jak uruchamianie lub zatrzymywanie klastra i zarządzanie węzłami
