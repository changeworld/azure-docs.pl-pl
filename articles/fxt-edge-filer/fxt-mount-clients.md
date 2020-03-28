---
title: Instalowanie klientów w klastrze Microsoft Azure FXT Edge Filer
description: Jak maszyny klienckie NFS mogą montować hybrydową pamięć podręczną magazynu usługi Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 43223db298e4ad170ea6d0687a342b3aee35500e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130766"
---
# <a name="tutorial-mount-the-cluster"></a>Samouczek: Montowanie klastra

W tym samouczku opisano, jak zainstalować klientów systemu plików NFS w klastrze azure FXT Edge Filer. Klienci montują ścieżki wirtualnego obszaru nazw przypisane podczas dodawania magazynu zaplecza.

Ten poradnik uczy:

> [!div class="checklist"]
> * Strategie równoważenia obciążenia klientów w zakresie adresów IP skierowanych do klienta
> * Jak skonstruować ścieżkę instalacji z adresu IP skierowanego do klienta i połączenia obszaru nazw
> * Jakie argumenty mają być używane w poleceniu instalacji

Ten samouczek trwa około 45 minut.

## <a name="steps-to-mount-the-cluster"></a>Kroki do montażu klastra

Wykonaj następujące kroki, aby połączyć maszyny klienckie z klastrem plików usługi Azure FXT Edge.

1. Zdecyduj, jak zrównoważyć ruch klienta między węzłami klastra. Przeczytaj [informacje o obciążeniu klienta salda](#balance-client-load)poniżej.
1. Zidentyfikuj adres IP klastra i ścieżkę połączenia do zainstalowania.
1. Określ ścieżkę skierowaną do klienta dla instalacji.
1. Wystaw [polecenie mount](#use-recommended-mount-command-options)z odpowiednimi argumentami.

## <a name="balance-client-load"></a>Bilans obciążenia klienta

Aby ułatwić równoważenie żądań klientów między wszystkimi węzłami w klastrze, należy zainstalować klientów do pełnego zakresu adresów IP skierowanych do klienta. Istnieje kilka sposobów automatyzacji tego zadania.

Aby dowiedzieć się więcej o równoważeniu obciążenia DNS typu round-robin dla klastra, przeczytaj artykuł [Konfigurowanie systemu DNS dla klastra plików usługi Azure FXT Edge](fxt-configure-network.md#configure-dns-for-load-balancing). Aby użyć tej metody, należy zachować serwer DNS, co nie jest wyjaśnione w tych artykułach.

Prostszą metodą dla małych instalacji jest użycie skryptu do przypisywania adresów IP w całym zakresie w czasie instalacji klienta.

Inne metody równoważenia obciążenia mogą być odpowiednie dla dużych lub skomplikowanych systemów. Skonsultuj się z przedstawicielem firmy Microsoft lub otwórz [prośbę o pomoc dotyczącą pomocy.](fxt-support-ticket.md) (Moduł równoważenia obciążenia platformy Azure nie jest obecnie *obsługiwany za pomocą* narzędzia Azure FXT Edge Filer).

## <a name="create-the-mount-command"></a>Tworzenie polecenia mount

Z klienta ``mount`` polecenie mapuje serwer wirtualny (vserver) w klastrze Azure FXT Edge Filer do ścieżki w lokalnym systemie plików.

Format jest``mount <FXT cluster path> <local path> {options}``

Istnieją trzy elementy polecenia mount:

* ścieżka klastra - kombinacja adresu IP i ścieżki połączenia obszaru nazw opisana poniżej
* ścieżka lokalna - ścieżka na kliencie
* mount command options - (wymienione w [Użyj zalecanych opcji polecenia montowania)](#use-recommended-mount-command-options)

### <a name="create-the-cluster-path"></a>Tworzenie ścieżki klastra

Ścieżka klastra jest kombinacją adresu IP serwera *vserver* oraz ścieżki do *skrzyżowania obszaru nazw*. Połączenie obszaru nazw jest ścieżką wirtualną zdefiniowaną po [dodaniu systemu pamięci masowej](fxt-add-storage.md#create-a-junction).

Na przykład, jeśli ``/fxt/files`` użyto jako ścieżki obszaru nazw, klienci będą montować *IP_address*:/fxt/files do lokalnego punktu instalacji.

![Okno dialogowe "Dodaj nowe skrzyżowanie" z /avere/files w polu ścieżki obszaru nazw](media/fxt-mount/fxt-junction-example.png)

Adres IP jest jednym z adresów IP przeznaczonych dla klienta zdefiniowanych dla serwera vserver. Zakres adresów IP skierowanych do klienta można znaleźć w dwóch miejscach w Panelu sterowania klastra:

* **Tabela VServers** (karta Pulpit nawigacyjny) -

  ![Karta Pulpit nawigacyjny w Panelu sterowania z kartą VServer wybraną w tabeli danych poniżej wykresu i zakreśloną sekcją adres IP](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Strona ustawień **sieciowych z widokiem na klienta** -

  ![Ustawienia > VServer > strona konfiguracji sieci z okręgu wokół sekcji Zakres adresów w tabeli dla określonego serwera vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Połącz adres IP i ścieżkę obszaru nazw, tworząc ścieżkę klastra dla polecenia instalacji.

Przykładowe polecenie instalacji klienta:``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Tworzenie ścieżki lokalnej

Ścieżka lokalna dla polecenia mount zależy od Ciebie. Można ustawić dowolną strukturę ścieżki jako część wirtualnej przestrzeni nazw. Zaprojektuj obszar nazw i ścieżkę lokalną, która jest wygodna dla przepływu pracy klienta.

Aby uzyskać więcej informacji na temat obszaru nazw skierowanego do klienta, przeczytaj [omówienie obszaru nazw](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)przewodnika po konfiguracji klastra .

Oprócz ścieżek należy uwzględnić [opcje polecenia mount](#use-recommended-mount-command-options) opisane poniżej podczas montażu każdego klienta.

### <a name="use-recommended-mount-command-options"></a>Używanie zalecanych opcji polecenia montowania

Aby zapewnić bezproblemowe instalowanie klienta, należy przekazać następujące ustawienia i argumenty w poleceniu instalacji:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Wymagane ustawienia | |
--- | ---
``hard`` | Miękkie instalacje do klastra plików usługi Azure FXT Edge są skojarzone z błędami aplikacji i możliwością utraty danych.
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieciowych dla operacji instalacji.
``retry=n`` | Ustaw, ``retry=30`` aby uniknąć przejściowych błędów instalacji. (W instalacjach pierwszego planu zalecana jest inna wartość).

| Preferowane ustawienia  | |
--- | ---
``nointr``            | Jeśli klienci używają starszych jąder systemu operacyjnego (przed kwietniem 2008 r.), które obsługują tę opcję, użyj jej. Opcja "intr" jest opcją domyślną.

## <a name="next-steps"></a>Następne kroki

Po zamontowaniu klientów można przetestować przepływ pracy i rozpocząć pracę z klastrem.

Jeśli chcesz przenieść dane do nowego filera rdzenia chmury, skorzystaj ze struktury pamięci podręcznej przy użyciu równoległego pozyskiwania danych. Niektóre strategie są opisane w [przeniesieniu danych do klastra vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT for Azure to produkt oparty na chmurze, który używa technologii buforowania bardzo podobnej do usługi Azure FXT Edge Filer).

Przeczytaj [monitora Usługi Azure FXT Edge Filer stan sprzętu,](fxt-monitor.md) jeśli chcesz rozwiązać wszelkie problemy ze sprzętem.
