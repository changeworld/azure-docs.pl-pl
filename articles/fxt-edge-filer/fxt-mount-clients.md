---
title: Zainstaluj klientów w klastrze filtr Edge FXT Azure firmy Microsoft
description: Jak komputery klienckie systemu plików NFS można zainstalować filtr Edge FXT Azure storage hybrydowe z pamięci podręcznej
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: dd74b82d4edad2b0176a3724176d924c2387f6fe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450324"
---
# <a name="tutorial-mount-the-cluster"></a>Samouczek: Zainstaluj klaster

Tym samouczku pokazano, jak zainstalować klientów systemu plików NFS w klastrze filtr Edge FXT platformy Azure. Klienci Zainstaluj ścieżki wirtualnej przestrzeni nazw, które przypisane po dodaniu magazynu zaplecza. 

W tym samouczku pokazano: 

> [!div class="checklist"]
> * Strategie klientów równoważenia obciążenia z zakresu adresów IP ukierunkowane na klienta
> * Jak utworzyć ścieżkę instalacji z Rozgałęzienie ukierunkowane na klienta IP adres i przestrzeni nazw
> * Argumenty, które do użycia w poleceniu instalacji

Ten samouczek zajmuje około 45 minut.

## <a name="steps-to-mount-the-cluster"></a>Instrukcje instalacji klastra

Wykonaj następujące kroki, aby połączyć z komputerów klienckich do klastra usługi Azure FXT krawędzi filtr.

1. Zdecyduj, jak Równoważenie obciążenia ruchu klientów między węzły klastra. Odczyt [Równoważenie obciążenia klienta](#balance-client-load)poniżej, aby uzyskać szczegółowe informacje. 
1. Zidentyfikuj klastra adresów IP i Rozgałęzienie ścieżek do zainstalowania.
1. Określ ścieżkę ukierunkowane na klienta do instalacji.
1. Problem [polecenie instalacji](#use-recommended-mount-command-options), z odpowiednimi argumentami.

## <a name="balance-client-load"></a>Równoważenie obciążenia klienta

Aby ułatwić równoważenie żądań klientów między wszystkie węzły w klastrze, należy zainstalować klientów do pełnego zakresu adresów IP ukierunkowane na klienta. Istnieje kilka sposobów, aby zautomatyzować to zadanie.

Aby uzyskać informacje dotyczące działania okrężnego DNS równoważenia obciążenia dla klastra, przeczytaj [Konfigurowanie serwera DNS dla klastra filtr Edge FXT Azure](fxt-configure-network.md#configure-dns-for-load-balancing). Aby użyć tej metody, musisz utrzymywać serwer DNS, które nie zostały wyjaśnione w tych artykułach.

Prostszą metodę niż w przypadku małych instalacji jest używanie skryptu do przypisywania adresów IP w całym zakresie w momencie instalacji klienta. 

Inne metody równoważenia obciążenia może być odpowiednie dla dużych lub złożonych systemów. Zapoznaj się z przedstawicielem handlowym firmy Microsoft lub Otwórz żądanie pomocy technicznej w celu uzyskania pomocy. (Równoważenie obciążenia azure jest obecnie *nieobsługiwane* z filtr Edge FXT platformy Azure.)

## <a name="create-the-mount-command"></a>Utwórz polecenie instalacji 

Z komputera klienckiego z ``mount`` polecenie mapuje serwera wirtualnego (vserver) w klastrze Azure FXT krawędzi filtr do ścieżki w lokalnym systemie plików. 

Format jest ``mount <FXT cluster path> <local path> {options}``

Istnieją trzy elementy do polecenia instalacji: 

* Ścieżka klastra — kombinacją adresów IP i nazw Rozgałęzienie ścieżek opisanych poniżej
* Ścieżka lokalna — ścieżka na komputerze klienckim 
* Opcje polecenia - instalacji (wymienione w [zaleca użycie opcji polecenia instalacji](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Tworzenie ścieżki klastra

Ścieżka klastra jest kombinacją vserver *adresu IP* oraz ścieżkę do *przestrzeni nazw połączeń*. Połączenie przestrzeni nazw jest ścieżką wirtualną zdefiniowane kiedy użytkownik [dodano system magazynowania](fxt-add-storage.md#create-a-junction).

Na przykład, jeśli użyto ``/avere/files`` jako ścieżki obszaru nazw, instalująca klientów *adres_IP*: / avere/pliki do ich punktu instalacji lokalnej. 

<!-- to do: update screenshot to use fxt/files instead of avere/files -->

!["Dodaj nowe połączenie" okno dialogowe z plikami/avere/w polu Ścieżka przestrzeni nazw](media/fxt-mount/fxt-create-junction-example.png)

Adres IP jest jeden z adresów IP ukierunkowane na klienta, które są zdefiniowane dla vserver. Można znaleźć w zakresie ukierunkowane na klienta adresów IP w dwóch miejscach w klastrze Panelu sterowania:

* **VServers** tabeli (karta pulpitu nawigacyjnego) — 

  ![Karta pulpitu nawigacyjnego z wybraną w tabeli danych pod wykresem kartą VServer i sekcja adresu IP w kółkach w Panelu sterowania](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Klient sieci skierowany** strony Ustawienia - 

  ![Ustawienia > VServer > strony konfiguracji klienta sieci skierowany w kółku sekcji zakres adresów w tabeli dla konkretnego vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Połącz, adres IP i ścieżki przestrzeni nazw w celu utworzenia klastra ścieżkę dla polecenia instalowania. 

Przykładowe polecenie instalacji klienta: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Utwórz ścieżkę lokalną

Ścieżka lokalna dla polecenia instalowania zależy od użytkownika. Można ustawić dowolną strukturę ścieżki, które mają w ramach wirtualnej przestrzeni nazw. Projektowanie przestrzeni nazw i ścieżkę lokalną, odpowiednim dla przepływu pracy klienta. 

Aby uzyskać więcej informacji na temat nazw ukierunkowane na klienta, zapoznaj się z przewodnikiem konfiguracji klastra [Przegląd przestrzeni nazw](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html).

Oprócz ścieżek, obejmują [opcje polecenia instalacji](#use-recommended-mount-command-options) opisane poniżej, w przypadku instalowania poszczególnych klientów.

### <a name="use-recommended-mount-command-options"></a>Użyj opcji polecenia instalacji zalecanych

W celu zapewnienia instalacji klienta bezproblemowe, należy przekazać te ustawienia i argumenty polecenia instalacji: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Wymagane ustawienia | |
--- | --- 
``hard`` | Instalacji miękkich klastrowi filtr Edge FXT platformy Azure są skojarzone z błędów aplikacji i możliwej utracie danych. 
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieciowych systemu plików NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci dla operacji instalacji.
``retry=n`` | Ustaw ``retry=30`` w celu uniknięcia błędów przejściowych instalacji. (Inną wartość jest zalecane w instaluje pierwszego planu).

| Preferowanych ustawień  | |
--- | --- 
``nointr``            | Jeśli klienci korzystają z starszych jądra systemu operacyjnego (przed kwietnia 2008), które obsługują tę opcję, można go użyć. Opcja "Grupa" jest ustawieniem domyślnym.

## <a name="next-steps"></a>Kolejne kroki

Po zainstalowano klientów, można przetestować przepływ pracy i rozpoczynanie pracy z klastrem.

Jeśli musisz przenieść dane do nowego filtr core chmury, uwzględniające pozyskiwać zaletą struktury pamięci podręcznej przy użyciu danych równoległych. Niektóre strategie zostały opisane w [przenoszenia danych do klastra vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (VFXT Avere dla platformy Azure jest oparte na chmurze produktu, korzystającej z technologii buforowania w bardzo podobny do filtr Edge FXT platformy Azure).

Odczyt [filtr Edge FXT Azure Monitor stanu sprzętu](fxt-monitor.md) w przypadku konieczności rozwiązywania wszelkich problemów sprzętowych. 
