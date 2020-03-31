---
title: Montowanie avere vFXT — Azure
description: Jak zainstalować klientów za pomocą avere vFXT na platformie Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: b8486b5a33226b1faa5e3874144129dbe7a1a2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153415"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Instalowanie klastra Avere vFXT

Wykonaj następujące kroki, aby połączyć komputery klienckie z klastrem vFXT.

1. Zdecyduj, jak zrównoważyć ruch klienta między węzłami klastra. Przeczytaj [informacje o obciążeniu klienta salda](#balance-client-load)poniżej.
1. Zidentyfikuj adres IP i ścieżkę połączenia do zainstalowania.
1. Wystaw [polecenie mount](#mount-command-arguments)z odpowiednimi argumentami.

## <a name="balance-client-load"></a>Bilans obciążenia klienta

Aby ułatwić równoważenie żądań klientów między wszystkimi węzłami w klastrze, należy zainstalować klientów do pełnego zakresu adresów IP skierowanych do klienta. Istnieje kilka prostych sposobów automatyzacji tego zadania.

> [!TIP]
> Inne metody równoważenia obciążenia mogą być odpowiednie dla dużych lub skomplikowanych systemów; [otwórz bilet pomocy technicznej).](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)
>
> Jeśli wolisz używać serwera DNS do automatycznego równoważenia obciążenia po stronie serwera, musisz skonfigurować własny serwer DNS na platformie Azure i zarządzać nim. W takim przypadku można skonfigurować system DNS okrężny dla klastra vFXT zgodnie z tym [dokumentem: Konfiguracja DNS klastra Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Przykładowy skrypt montażowy zrównoważonego klienta

W tym przykładzie kodu użyto adresów IP klienta jako elementu randomizującego do dystrybucji klientów do wszystkich dostępnych adresów IP klastra vFXT.

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
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

Powyższa funkcja jest częścią przykładu Batch dostępnego w witrynie [przykładów Avere vFXT.](https://github.com/Azure/Avere#tutorials)

## <a name="create-the-mount-command"></a>Tworzenie polecenia mount

> [!NOTE]
> Jeśli podczas tworzenia klastra VFXT programu Avere nie utworzyno nowego kontenera obiektów Blob, przed podjęciem próby zainstalowania klientów należy dodać systemy magazynowania zgodnie z opisem w temacie [Konfigurowanie magazynu.](avere-vfxt-add-storage.md)

Z klienta ``mount`` polecenie mapuje serwer wirtualny (vserver) w klastrze vFXT na ścieżkę w lokalnym systemie plików. Format jest``mount <vFXT path> <local path> {options}``

Polecenie mount ma trzy elementy:

* Ścieżka vFXT - połączenie adresu IP i ścieżki połączenia obszaru nazw w klastrze 9 opisane poniżej)
* ścieżka lokalna - ścieżka na kliencie
* mount command options - wymienione w [argumentach polecenia Mount](#mount-command-arguments)

### <a name="junction-and-ip"></a>Złącze i adres IP

Ścieżka serwera vserver jest kombinacją jego *adresu IP* oraz ścieżki do skrzyżowania *obszaru nazw*. Węzeł obszaru nazw jest ścieżką wirtualną, która została zdefiniowana podczas dodawania systemu magazynu.

Jeśli klaster został utworzony z magazynem obiektów Blob, ścieżka obszaru nazw do tego kontenera jest`/msazure`

Przykład: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Jeśli po utworzeniu klastra dodano magazyn, ścieżka połączenia obszaru nazw jest wartością ustawioną w **ścieżce obszaru nazw** podczas tworzenia skrzyżowania. Na przykład jeśli ``/avere/files`` użyto jako ścieżki obszaru nazw, klienci będą instalować *IP_address*:/avere/files do lokalnego punktu instalacji.

![Okno dialogowe "Dodaj nowe skrzyżowanie" z /avere/files w polu ścieżki obszaru nazw](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

Adres IP jest jednym z adresów IP przeznaczonych dla klienta zdefiniowanych dla serwera vserver. Zakres adresów IP skierowanych do klienta można znaleźć w dwóch miejscach w Panelu sterowania Avere:

* **Tabela VServers** (karta Pulpit nawigacyjny) -

  ![Karta Pulpit nawigacyjny w Panelu sterowania Avere z wybraną kartą VServer w tabeli danych poniżej wykresu i zakreśloną sekcją adresu IP](media/avere-vfxt-ip-addresses-dashboard.png)

* Strona ustawień **sieciowych z widokiem na klienta** -

  ![Ustawienia > VServer > strona konfiguracji sieci z okręgu wokół sekcji Zakres adresów w tabeli dla określonego serwera vserver](media/avere-vfxt-ip-addresses-settings.png)

Oprócz ścieżek należy uwzględnić [argumenty polecenia Mount](#mount-command-arguments) opisane poniżej podczas montażu każdego klienta.

### <a name="mount-command-arguments"></a>Argumenty polecenia Montowanie

Aby zapewnić bezproblemowe instalowanie klienta, należy przekazać następujące ustawienia i argumenty w poleceniu instalacji:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Wymagane ustawienia | |
--- | ---
``hard`` | Miękkie instalacje do klastra vFXT są skojarzone z awariami aplikacji i możliwością utraty danych.
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieciowych dla operacji instalacji.
``retry=n`` | Ustaw, ``retry=30`` aby uniknąć przejściowych błędów instalacji. (W instalacjach pierwszego planu zalecana jest inna wartość).

## <a name="next-steps"></a>Następne kroki

Po zamontowaniu klientów można ich użyć do skopiowania danych do nowego kontenera magazynu obiektów Blob w klastrze. Jeśli nie musisz wypełniać nowego magazynu, przeczytaj inne łącza, aby dowiedzieć się więcej o dodatkowych zadaniach konfiguracyjnych:

* [Przenoszenie danych do głównego filera klastra](avere-vfxt-data-ingest.md) — jak efektywnie przesyłać dane do nowego głównego filera
* [Dostosowywanie dostrajania klastra](avere-vfxt-tuning.md) — dostosowywanie ustawień klastra do obciążenia
* [Zarządzanie klastrem](avere-vfxt-manage-cluster.md) — jak uruchomić lub zatrzymać klaster i zarządzać węzłami
