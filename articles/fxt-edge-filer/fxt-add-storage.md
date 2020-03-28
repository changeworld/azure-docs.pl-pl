---
title: 'Samouczek: Dodawanie magazynu do klastra plików usługi Azure FXT Edge'
description: Jak skonfigurować magazyn zaplecza i obszar pseudonamezowania skierowanego do klienta dla usługi Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239217"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Samouczek: Dodawanie magazynu zaplecza i konfigurowanie wirtualnej przestrzeni nazw 

W tym samouczku wyjaśniono, jak dodać magazyn zaplecza dla pamięci podręcznej i jak skonfigurować wirtualny system plików skierowanych do klienta. 

Klaster łączy się z zaplecza systemów magazynowania, aby uzyskać dostęp do danych klientów żądania i do przechowywania zmian trwale niż w pamięci podręcznej. 

Obszar nazw jest pseudosystemem plików skierowanym do klienta, który umożliwia wymianę magazynu zaplecza bez zmiany przepływów pracy po stronie klienta. 

Ten samouczek zawiera następujące informacje: 

> [!div class="checklist"]
> * Jak dodać magazyn zaplecza do klastra plików usługi Azure FXT Edge 
> * Jak zdefiniować ścieżkę skierowaną do klienta do magazynu

## <a name="about-back-end-storage"></a>Przechowywanie zaplecza — informacje

Klaster usługi Azure FXT Edge Filer używa podstawowej definicji *filera* do połączenia systemu magazynu zaplecza z klastrem FXT.

Usługa Azure FXT Edge Filer jest zgodna z kilkoma popularnymi systemami sprzętowymi NAS i może używać pustych kontenerów z obiektów Blob platformy Azure lub innego magazynu w chmurze. 

Kontenery magazynu w chmurze muszą być puste po dodaniu, aby system operacyjny FXT mógł całkowicie zarządzać wszystkimi danymi na woluminie magazynu w chmurze. Istniejące dane można przenieść do kontenera w chmurze po dodaniu kontenera do klastra jako podstawowy filer.

Użyj Panelu sterowania, aby dodać do systemu podstawowy filer.

> [!NOTE]
> 
> Jeśli chcesz korzystać z amazon AWS lub Google Cloud storage, musisz zainstalować licencję funkcji FlashCloud<sup>TM.</sup> Skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać klucz licencyjny, a następnie postępuj zgodnie z instrukcjami zawartymi w starszej konfiguracji [przewodnika po dodawaniu lub usuwaniu licencji funkcji](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Obsługa usługi Azure Blob storage jest uwzględniona w licencji oprogramowania Azure FXT Edge Filer. 

Aby uzyskać bardziej szczegółowe informacje na temat dodawania podstawowych filerów, zapoznaj się z tymi sekcjami przewodnika konfiguracji klastra:

* Aby uzyskać więcej informacji na temat wyboru i przygotowania do dodania głównego filera, przeczytaj [artykuł Praca z podstawowymi filerami](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Aby uzyskać szczegółowe wymagania wstępne i instrukcje krok po kroku, przeczytaj następujące artykuły:

  * [Dodawanie nowego pliku serwera NAS Core](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Dodawanie nowego filera o rdzeniu chmury](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Po dodaniu głównego filera można zaktualizować jego ustawienia na stronie Ustawienia szczegółów pliku podstawowego.

## <a name="add-a-core-filer"></a>Dodawanie głównego filera

Zdefiniuj podstawowy filer, klikając przycisk **Utwórz** na stronie Ustawienia core **filer** > **podstawowych filerów.**

![Kliknięcie przycisku Utwórz nad listą głównych filerów na stronie Manage Core Filers](media/fxt-cluster-config/create-core-filer-button.png)

Kreator **Add New Core Filer** przeprowadzi Cię przez proces tworzenia głównego filera, który łączy się z magazynem zaplecza. Przewodnik konfiguracji klastra zawiera opisy krok po kroku procesu, który różni się w przypadku pamięci masowej NFS/NAS i pamięci masowej w chmurze (łącza są powyżej). 

Podzadęcie obejmują:

* Określ typ głównego filera (NAS lub chmury)

  ![Pierwsza strona sprzętowego kreatora plików nas. Opcja "cloud core filer" jest wyłączona i wyświetla komunikat o błędzie dotyczący brakującej licencji.](media/fxt-cluster-config/new-nas-1.png)

* Ustaw nazwę głównego filera. Wybierz nazwę, która pomaga administratorom klastrów zrozumieć, który system magazynowania reprezentuje.

* W przypadku podstawowych filerów NAS podaj w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP. Nazwa FQDN jest zalecana dla wszystkich głównych filerów i wymagana do uzyskiwania dostępu do SMB.

* Wybierz zasadę pamięci podręcznej — druga strona kreatora zawiera listę dostępnych zasad pamięci podręcznej dla nowego głównego filera. Aby uzyskać szczegółowe informacje, przeczytaj [sekcję Zasad pamięci podręcznej przewodnika konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Druga strona sprzętowego kreatora plików serwerów PLIKÓW SERWERA NAS; Menu rozwijane Zasady pamięci podręcznej jest otwarte, z kilkoma wyłączonymi opcjami i trzema prawidłowymi opcjami zasad pamięci podręcznej (obejście, buforowanie odczytu i buforowanie odczytu/zapisu).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* W przypadku magazynu w chmurze należy określić usługi w chmurze i poświadczenia dostępu, wśród innych parametrów. Aby uzyskać szczegółowe informacje, przeczytaj usługę i protokół w [chmurze](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) w Przewodniku konfiguracji klastra.

  ![Informacje o rdzeniu filera w chmurze w kreatorze nowego rdzenia filera](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Jeśli dodano już poświadczenia dostępu do chmury dla tego klastra, są one wyświetlane na liście. Aktualizowanie i dodawanie poświadczeń na stronie Ustawienia**poświadczeń chmury klastra.** **Cluster** >  

Po wypełnieniu wszystkich wymaganych ustawień w kreatorze kliknij przycisk **Dodaj filera,** aby przesłać zmianę.

Po kilku chwilach system przechowywania pojawia się na liście filerów **rdzenia pulpitu nawigacyjnego** i jest dostępny za pośrednictwem podstawowych stron ustawień filera.

![core filer "Flurry-NAS" na stronie Zarządzanie ustawieniami core filers, z rozszerzonym widokiem szczegółów filera](media/fxt-cluster-config/core-filer-in-manage-page.png)

W głównym pliku na tym zrzucie ekranu brakuje serwera vserver. Należy połączyć core filer do serwera vserver i utworzyć skrzyżowanie, aby klienci mogli uzyskać dostęp do magazynu. Te kroki są opisane poniżej w [konfigurowanie obszaru nazw](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurowanie obszaru nazw

Klaster usługi Azure FXT Edge Filer tworzy wirtualny system plików o nazwie *obszar nazw klastra,* który upraszcza dostęp klienta do danych przechowywanych w różnych systemach zaplecza. Ponieważ klienci żądają plików przy użyciu ścieżki wirtualnej, systemy magazynowania można dodawać lub zastępować bez konieczności zmiany przepływu pracy klienta. 

Obszar nazw klastra umożliwia również prezentowanie systemów pamięci masowej w chmurze i serwera NAS w podobnej strukturze plików. 

Serwery vservers klastra utrzymują obszar nazw i obsługują klientów zawartość. Istnieją dwa kroki, aby utworzyć obszar nazw klastra: 

1. Tworzenie serwera vserver 
1. Konfigurowanie połączeń między systemami pamięci masowej zaplecza a ścieżkami systemu plików skierowanych do klienta 

### <a name="create-a-vserver"></a>Tworzenie serwera vserver

Serwery VServers to wirtualne serwery plików, które kontrolują przepływ danych między klientem a głównymi filerami klastra:

* VServers hostuje adresy IP skierowane do klienta
* Serwery VServers tworzą obszar nazw i definiują połączenia, które mapują strukturę katalogów wirtualnych skierowaną do klienta na eksport w magazynie zaplecza
* VServers wymuszają kontrolę dostępu do plików, w tym podstawowe zasady eksportu filer i systemy uwierzytelniania użytkowników
* VServers zapewniają infrastrukturę SMB

Przed rozpoczęciem konfigurowania serwera vserver klastra przeczytaj połączaną dokumentację i skonsultuj się z przedstawicielem firmy Microsoft, aby uzyskać pomoc w zrozumieniu obszaru nazw i serwerów vserver. Jeśli używasz sieci VLAN, [utwórz je](fxt-configure-network.md#adjust-network-settings) przed utworzeniem serwera vserver. 

Te sekcje Przewodnika konfiguracji klastra pomogą Ci zapoznać się z funkcjami serwera vserver FXT i globalnej przestrzeni nazw:

* [Tworzenie i praca z VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Korzystanie z globalnego obszaru nazw](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Tworzenie serwera VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Potrzebujesz co najmniej jednego serwera vserver dla klastra. 

Aby utworzyć nowy serwer vserver, potrzebne są następujące informacje:

* Nazwa ustawiona dla serwera vserver

* Zakres adresów IP skierowanych do klienta, które serwer vserver będzie obsługiwał

  Podczas tworzenia serwera vserver należy podać jeden zakres ciągłych adresów IP. Więcej adresów można dodać później, korzystając ze strony Ustawienia sieci z widokiem na **klienta.**

* Jeśli w sieci są sieci VLAN, które sieci VLAN będą używane w tym serwerze vserver

Użyj strony ustawień **VServer** > **Manage VServers,** aby utworzyć nowy serwer vserver. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Tworzenie serwera VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) w przewodniku konfiguracji klastra. 

![wyskakujące okno do tworzenia nowego serwera vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Tworzenie skrzyżowania

*Skrzyżowanie* mapuje ścieżkę magazynu zaplecza do obszaru nazw widocznego dla klienta.

Za pomocą tego systemu można uprościć ścieżkę używaną w punktach instalacji klienta i bezproblemowo skalować pojemność, ponieważ jedna ścieżka wirtualna może pomieścić magazyn z wielu głównych filerów.

![Dodawanie strony Kreatora nowego skrzyżowania z ustawieniami wypełnionymi](media/fxt-cluster-config/add-junction-full.png)

Szczegółowe informacje na temat tworzenia węzła obszar nazw programu [ **VServer** > ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) można znaleźć w przewodniku konfiguracji klastra.

![Strona ustawień obszaru nazw VServer > ze szczegółami połączenia](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurowanie reguł eksportu

Po tym, jak masz zarówno serwer vserver, jak i podstawowy filer, należy dostosować reguły eksportu i zasady eksportu, które kontrolują sposób, w jaki klienci mogą uzyskiwać dostęp do plików w eksporcie podstawowego filera.

Najpierw użyj strony**Reguły eksportu** **serwera VServer,** > aby dodać nowe reguły, zmodyfikować zasady domyślne lub utworzyć własne niestandardowe zasady eksportu.

Po drugie, użyj strony**Zasady eksportu** **serwera VServer,** > aby zastosować dostosowane zasady do eksportu głównego filera, gdy jest dostępny za pośrednictwem tego serwera vserver.

Przeczytaj artykuł Przewodnik konfiguracji klastra [Kontrolowanie dostępu do eksportu podstawowych filer, aby](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) uzyskać szczegółowe informacje.


## <a name="next-steps"></a>Następne kroki

Po dodaniu magazynu i skonfigurowaniu obszaru nazw skierowanego do klienta należy ukończyć początkową konfigurację klastra: 

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień sieci klastrów](fxt-configure-network.md)
