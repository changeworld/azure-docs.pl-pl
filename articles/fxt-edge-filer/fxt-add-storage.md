---
title: Dodawanie magazynu zaplecza do klastra systemu Microsoft Azure FXT krawędzi filtr
description: Jak skonfigurować Magazyn zaplecza i pseudonamespace ukierunkowane na klienta dla filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0a16e654ff92c450438ac91c590b42d22201d015
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450457"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Samouczek: Dodawanie magazynu zaplecza i konfigurowanie wirtualnej przestrzeni nazw 

W tym samouczku wyjaśniono, jak dodać magazyn back-edge dla pamięci podręcznej i sposób konfigurowania wirtualnego systemu plików ukierunkowane na klienta. 

Klaster nawiązanie połączenia z systemów magazynowania zaplecza, dostęp do danych żądania klientów i przechowywanie zmian trwałe niż w pamięci podręcznej. 

Przestrzeń nazw jest system plików pseudo ukierunkowane na klienta, która pozwala wymienić magazynu zaplecza bez zmieniania klienta przepływów pracy. 

Ten samouczek zawiera następujące informacje: 

> [!div class="checklist"]
> * Jak dodać magazynu zaplecza w klastrze filtr Edge FXT platformy Azure 
> * Jak zdefiniować ścieżkę ukierunkowane na klienta magazynu

## <a name="about-back-end-storage"></a>Temat magazynu zaplecza

Klaster filtr Edge FXT Azure używa *podstawowe filtr* definicji, aby połączyć system magazynu zaplecza w klastrze FXT.

Filtr krawędzi w usłudze Azure FXT jest zgodny z kilku popularnych systemów sprzętowych NAS i używać pusty kontenerów obiektów Blob platformy Azure lub innym magazynie w chmurze. 

Kontenery magazynu w chmurze może być pusty, po dodaniu tak, aby system operacyjny FXT całkowicie zarządzać wszystkie dane na woluminie magazynu w chmurze. Po dodaniu kontenera do klastra jako filtr core, można przenieść istniejące dane do kontenera w chmurze.

Aby dodać filtr core do systemu za pomocą Panelu sterowania.

> [!NOTE]
> 
> Jeśli chcesz korzystać z usługi Amazon AWS lub Google Cloud storage, należy zainstalować FlashCloud<sup>TM</sup> funkcji licencji. Skontaktuj się z przedstawicielem handlowym firmy Microsoft, aby klucz licencji, a następnie postępuj zgodnie z instrukcjami w przewodniku starszą konfigurację, aby uzyskać [dodanie lub usunięcie licencji funkcji](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Obsługa usługi Azure Blob storage znajduje się w licencji na oprogramowanie filtr Edge FXT platformy Azure. 

Aby uzyskać szczegółowe informacje o dodawaniu filtrach core, przeczytaj te sekcje Przewodnik po konfiguracji klastra:

* Aby uzyskać więcej informacji o Wybieranie i przygotowywanie dodać filtr core, przeczytaj [wygląda Praca z podstawowych](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Aby uzyskać szczegółowe wymagania wstępne i instrukcje krok po kroku, przeczytaj następujące artykuły:

  * [Dodawanie nowych filtr Core NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Dodawanie nowych filtr Core chmury](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Po dodaniu filtr core, możesz zaktualizować jego ustawienia na stronie szczegółów filtr podstawowych ustawień.

## <a name="add-a-core-filer"></a>Dodaj filtr core

Zdefiniować filtr core, klikając **Utwórz** znajdujący się na **filtr Core** > **wygląda podstawowe zarządzanie** strony ustawień.

![Kliknięcie przycisku Utwórz powyżej listy core filtrach na stronie Zarządzanie filtrach Core](media/fxt-cluster-config/create-core-filer-button.png)

**Dodaj nowy filtr Core** Kreator przeprowadzi Cię przez proces tworzenia filtr core prowadzący do wewnętrznej bazy danych magazynu. Przewodnik po konfiguracji klastra zawiera opisy krok po kroku procesu, który różni się dla systemu plików NFS/NAS magazynu i Magazyn w chmurze (linki znajdują się powyżej). 

Podzadania obejmują:

* Określ typ filtr core (NAS lub w chmurze)

  ![Pierwszej stronie Kreatora sprzętu NAS do filtr nowych podstawowych. Opcja "chmura core filtr" jest wyłączona, a także pokazuje komunikat o błędzie dotyczący brakującej licencji.](media/fxt-cluster-config/new-nas-1.png)

* Ustaw nazwę filtr core. Wybierz nazwę, która pomaga administratorom klastra zrozumieć system magazynu, który reprezentuje.

* Dla NAS core filtrach Podaj w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP. Nazwa FQDN jest zalecana dla wszystkich wygląda podstawowe i wymagane do dostępu do protokołu SMB.

* Wybierz zasady pamięci podręcznej — druga strona kreatora listę zasad pamięci podręcznej dostępnych dla nowy filtr core. Aby uzyskać więcej informacji, przeczytaj [zasady części przewodnika konfiguracji klastra w pamięci podręcznej](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Drugiej stronie sprzętu NAS core Filtr Kreatora nowego; menu rozwijane zasad pamięci podręcznej jest otwarty, wskazuje, że kilka wyłączone opcje i trzy prawidłową pamięć podręczną zasad (obejście odczytu pamięci podręcznej i buforowania odczytu/zapisu).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Dla magazynu w chmurze należy określić cloud service i poświadczenia dostępu, wśród innych parametrów. Aby uzyskać więcej informacji, przeczytaj [w chmurze usługa i protokół](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) w przewodniku po konfiguracji klastra.

  ![Informacje o filtr core chmury w Kreatorze nowego filtr Core](media/fxt-cluster-config/new-core-filer-cloud3.png) <!-- xxx get an Azure version of this screenshot xxx -->
  
  Jeśli już dodano poświadczenia dostępu do chmury dla tego klastra, zostaną one wyświetlone na liście. Aktualizowanie i dodawanie poświadczenia w **klastra** > **poświadczenia chmury** strony ustawień. 

Po wypełnieniu wszystkich wymaganych ustawień kreatora, kliknij przycisk **Dodaj filtr** przycisk, aby przesłać zmiany.

Po kilku chwilach system magazynu jest wyświetlana na **pulpit nawigacyjny** podstawowe filtrach listy i jest możliwy za pośrednictwem strony ustawień filtr core.

![rozszerzony filtr Core "Dziesiątki-NAS" na stronie ustawień zarządzania filtrach Core przy użyciu widoku szczegółów filtr](media/fxt-cluster-config/core-filer-in-manage-page.png)

Brak vserver filtr core, w tym zrzucie ekranu. Należy połączyć filtr core vserver i utworzyć połączenie, dzięki czemu klienci mają dostęp do magazynu. Te kroki są opisane poniżej w [Konfigurowanie przestrzeni nazw](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurowanie przestrzeni nazw

Klaster filtr Edge FXT Azure tworzy wirtualny system plików o nazwie *klastra przestrzeni nazw* upraszczające proces klienta dostępu do danych przechowywanych w różnych systemów zaplecza. Ponieważ klienci żądają pliki przy użyciu ścieżki wirtualnej, systemów pamięci masowej można dodać lub bez konieczności zmiany przepływu pracy klienta. 

Przestrzeń nazw klastra umożliwia także prezentować chmury i systemów magazynowych NAS w podobną strukturę pliku. 

Vservers klastra Obsługa przestrzeni nazw i obsługujących zawartość dla klientów. Istnieją dwa kroki, aby utworzyć przestrzeń nazw klastra: 

1. Utwórz vserver 
1. Konfigurowanie punktach transferu między systemami magazynowania zaplecza i ścieżki ukierunkowane na klienta systemu plików 

### <a name="create-a-vserver"></a>Utwórz vserver

VServers to serwery plików wirtualnego, które kontrolują sposób przepływu danych między klientem i filtrach podstawowe klastra:

* Adresy IP ukierunkowane na klienta hosta VServers
* VServers utworzyć przestrzeń nazw, a także zdefiniować punktach transferu mapowane strukturę katalogu wirtualnego ukierunkowane na klienta eksporty magazynu zaplecza
* VServers wymuszanie kontroli dostępu do pliku, w tym core Filtr eksportu zasad i systemami uwierzytelniania użytkownika
* VServers zapewnia infrastrukturę protokołu SMB

Przed rozpoczęciem konfigurowania vserver klastra, zapoznaj się z dokumentacją połączone i zapoznaj się z przedstawicielem firmy Microsoft dla przestrzeni nazw Opis pomocy i vservers. Jeśli przy użyciu sieci VLAN, [je utworzyć](fxt-configure-network.md#adjust-network-settings) przed utworzeniem vserver. 

Te sekcje Przewodnik po konfiguracji klastra pomoże zapoznać się z FXT vserver i funkcje globalnej przestrzeni nazw:

* [Tworzenie i Praca z VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Za pomocą globalnego Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Tworzenie VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Należy co najmniej jeden vserver dla klastra. 

Aby utworzyć nowy vserver, potrzebne są następujące informacje:

* Nazwę można ustawić dla vserver

* Zakres adresów IP ukierunkowane na klienta, który będzie obsługiwać vserver

  Po utworzeniu vserver, należy podać jeden zakres sąsiadujących adresów IP. Możesz dodać więcej adresów później za pomocą **sieciowych połączonego z klienta** strony ustawień.

* Jeżeli w sieci VLAN, które sieci VLAN na potrzeby tego vserver

Użyj **VServer** > **Zarządzanie VServers** ustawienia strony, aby utworzyć nowy vserver. Aby uzyskać szczegółowe informacje, przeczytaj [tworzenia VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) w przewodniku po konfiguracji klastra. 

![Okno podręczne do tworzenia nowych vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Utwórz połączenie

A *Rozgałęzienie* mapuje ścieżka magazynu zaplecza do klienta widoczne przestrzeni nazw.

Można użyć tego systemu, aby uprościć ścieżki punktów instalacji klienta i pozwala bezproblemowo skalować wydajność, ponieważ jedną ścieżkę wirtualną może pomieścić usługi storage z poziomu wielu filtrach core.

![Dodaj stronę kreatora nowych połączeń z ustawieniami wypełnione](media/fxt-cluster-config/add-junction-full.png)

Zapoznaj się [ **VServer** > **Namespace** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) w przewodniku po konfiguracji klastra, aby uzyskać szczegółowe informacje o tworzeniu połączeń przestrzeni nazw.

![VServer > Strona Ustawienia Namespace przedstawiający szczegóły połączenie](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Skonfiguruj reguły eksportu

Po utworzeniu vserver i filtr core możesz dostosować reguły eksportu i wyeksportować zasady określające, jak klienci mogą uzyskiwać dostęp plików na polecenie eksportuje filtr core.

Najpierw za pomocą **VServer** > **Eksportuj reguły** stronę, aby dodać nowe reguły, aby zmodyfikować domyślne zasady lub Utwórz własne zasady Eksport niestandardowy.

Po drugie, użyć **VServer** > **Eksportuj zasady** strony, aby zastosować zasady dostosowane do eksportu filtr swoje podstawowe podczas uzyskiwania dostępu do za pośrednictwem tego vserver.

Zapoznaj się z artykułami przewodnik konfiguracji klastra [kontrolowanie dostępu do podstawowych filtr eksporty](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) Aby uzyskać szczegółowe informacje.


## <a name="next-steps"></a>Kolejne kroki

Po dodaniu magazynu i konfigurowanie przestrzeni nazw ukierunkowane na klienta, należy zakończyć początkowej konfiguracji klastra: 

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień sieciowych klastra](fxt-configure-network.md)
