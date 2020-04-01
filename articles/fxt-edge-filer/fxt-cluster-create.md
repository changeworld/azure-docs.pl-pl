---
title: 'Samouczek: Tworzenie klastra pamięci podręcznej programu Azure FXT Edge Filer'
description: Jak utworzyć klaster pamięci podręcznej magazynu hybrydowego za pomocą narzędzia Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bfe1d1aeeac55039acf0c7eb295001277be9cd2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239210"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Samouczek: Tworzenie klastra plików usługi Azure FXT Edge

Po zainstalowaniu i zainicjowaniu węzłów sprzętowych usługi Azure FXT Edge Filer dla pamięci podręcznej użyj oprogramowania klastra FXT do utworzenia klastra pamięci podręcznej. 

W tym samouczku otrzymasz od użytkownika kroki konfigurowania węzłów sprzętowych jako klastra. 

Z tego samouczka dowiesz się: 

> [!div class="checklist"]
> * Jakie informacje są potrzebne przed rozpoczęciem tworzenia klastra
> * Różnica między siecią zarządzania klastra, siecią klastra a siecią skierowaną do klienta
> * Jak połączyć się z węzłem klastra 
> * Jak utworzyć klaster początkowy przy użyciu jednego węzła azure FXT Edge Filer
> * Jak zalogować się do panelu sterowania klastra, aby skonfigurować ustawienia klastra

Ta procedura trwa od 15 do 45 minut, w zależności od tego, ile badań należy wykonać, aby zidentyfikować adresy IP i zasoby sieciowe.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj następujące wymagania wstępne:

* Zainstaluj swoje systemy sprzętowe Azure FXT Edge Filer w centrum danych 

  Do utworzenia klastra potrzebny jest tylko jeden węzeł, ale przed skonfigurowaniem klastra i przygotowaniem go do użycia należy [dodać co najmniej dwa kolejne węzły.](fxt-add-nodes.md) 

* Podłącz odpowiednie kable zasilające i sieciowe do systemu  
* Włącz co najmniej jeden węzeł filera usługi Azure FXT Edge i [ustaw jego hasło główne](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Zbieranie informacji dla klastra 

Do utworzenia klastra plików usługi Azure FXT Edge potrzebne są następujące informacje:

* Nazwa klastra

* Hasło administracyjne ustawione dla klastra

* Adresy IP:

  * Jeden adres IP do zarządzania klastrem oraz maska sieci i router do użycia w sieci zarządzania
  * Pierwszy i ostatni adres IP w ciągłym zakresie adresów IP do komunikacji klastra (węzeł do węzła). Szczegółowe informacje można znaleźć w [dystrybucji adresów IP](#ip-address-distribution)poniżej. 
  * (Adresy IP skierowane do klienta są ustawiane po utworzeniu klastra).

* Informacje o infrastrukturze sieciowej:

  * Adres IP serwera DNS dla klastra
  * Nazwa domeny DNS klastra
  * Nazwa lub adres IP serwerów NTP klastra (jednego lub trzech lub więcej) 
  * Określa, czy chcesz włączyć agregację łączy IEEE 802.1AX-2008 w interfejsach klastra
  * Jeśli włączysz agregację łączy, niezależnie od tego, czy chcesz używać agregacji dynamicznej IEEE 802.3ad (LACP)

Te elementy infrastruktury sieciowej można skonfigurować po utworzeniu klastra, ale lepiej jest to zrobić w czasie tworzenia. 

### <a name="ip-address-distribution"></a>Dystrybucja adresów IP

Klaster pamięci podręcznej magazynu hybrydowego usługi Azure FXT Edge Filer używa adresów IP w trzech kategoriach:

* Zarządzanie IP: jeden adres IP do zarządzania klastrem

  Ten adres służy jako punkt wejścia do uzyskiwania dostępu do narzędzi konfiguracji klastra (internetowego panelu sterowania lub interfejsu API XML-RPC). Ten adres jest automatycznie przypisywany do węzła podstawowego w klastrze i jest przenoszony automatycznie w przypadku zmiany węzła podstawowego.

  Inne adresy IP mogą być używane do uzyskiwania dostępu do panelu sterowania, ale adres IP zarządzania jest przeznaczony do zapewnienia dostępu, nawet jeśli poszczególne węzły w pracy awaryjnej.

* Sieć klastrów: zakres adresów IP do komunikacji klastra

  Sieć klastra służy do komunikacji między węzłami klastra i do pobierania plików z magazynu zaplecza (core filers).

  **Najlepsze praktyki:** Przydziel jeden adres IP na port fizyczny używany do komunikacji klastra w każdym węźle azure FXT Edge Filer. Klaster automatycznie przypisuje adresy w określonym zakresie do poszczególnych węzłów.

* Sieć skierowana do klienta: zakres adresów IP używanych przez klientów do żądania i zapisu plików

  Adresy sieciowe klienta są używane przez klientów do uzyskiwania dostępu do podstawowych danych filera za pośrednictwem klastra. Na przykład klient NFS może zainstalować jeden z tych adresów.

  **Najlepsze praktyki:** Przydziel jeden adres IP na port fizyczny używany do komunikacji z klientem w każdym węźle serii FXT.

  Klaster dystrybuuje adresy IP skierowane do klienta w swoich węzłach składowych tak równomiernie, jak to możliwe.

  Dla uproszczenia wielu administratorów konfiguruje jedną nazwę DNS z konfiguracją DNS (RRDNS), aby ułatwić dystrybucję żądań klientów w zakresie adresów. Ta konfiguracja umożliwia również wszystkim klientom korzystanie z tego samego polecenia instalacji w celu uzyskania dostępu do klastra. Przeczytaj [pozycję Konfiguruj usługę DNS,](fxt-configure-network.md#configure-dns-for-load-balancing) aby uzyskać więcej informacji.

Aby utworzyć nowy klaster, należy określić adres IP zarządzania i zakres adresów sieciowych klastra. Adresy skierowane do klienta są określane po utworzeniu klastra.

## <a name="connect-to-the-first-node"></a>Łączenie się z pierwszym węzłem

Można połączyć się z dowolnym z zainstalowanych węzłów FXT i użyć jego oprogramowania systemu operacyjnego, aby skonfigurować klaster.

Jeśli jeszcze tego nie zrobiono, włącz co najmniej jeden z węzłów FXT dla klastra i upewnij się, że ma połączenie sieciowe i adres IP. Aby aktywować węzeł, należy ustawić nowe hasło główne, więc wykonaj czynności opisane w [obszarze Ustaw hasła sprzętowe,](fxt-node-password.md) jeśli jeszcze tego nie zrobiono.

Aby sprawdzić połączenie sieciowe, upewnij się, że diody LED łącza sieciowego węzła są podświetlone (oraz, jeśli to konieczne, wskaźniki na przełączniku sieciowym, do którego jest podłączony). Diody LED wskaźników są opisane w [monitorze stanu sprzętu usługi Monitor Azure FXT Edge Filer](fxt-monitor.md).

Po uruchomieniu węzła zażąda adresu IP. Jeśli jest połączony z serwerem DHCP, akceptuje adres IP podany przez usługę DHCP. (Ten adres IP jest tymczasowy. Zmieni się podczas tworzenia klastra.)

Jeśli nie jest połączony z serwerem DHCP lub nie otrzyma odpowiedzi, węzeł użyje oprogramowania Bonjour do ustawienia przypisanego przez siebie adresu IP w formularzu 169.254. \*. \*. Należy jednak ustawić tymczasowy statyczny adres IP na jednej z kart sieciowych węzła przed użyciem go do utworzenia klastra. Instrukcje znajdują się w tym starszym dokumencie; skontaktuj się z usługą i pomocą techniczną firmy Microsoft w celu uzyskania zaktualizowanych informacji: [Dodatek A: Ustawianie statycznego adresu IP w węźle FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Znajdź adres IP

Połącz się z węzłem Azure FXT Edge Filer, aby znaleźć jego adres IP. Można użyć kabla szeregowego, bezpośredniego połączenia z portami USB i VGA lub podłączyć za pomocą przełącznika KVM. (Aby uzyskać szczegółowe informacje o połączeniu z portem, zobacz [Ustawianie haseł początkowych).](fxt-node-password.md)

Po nawiązaniu połączenia zaloguj `root` się przy użyciu nazwy użytkownika i hasła ustawionego podczas uruchamiania węzła po raz pierwszy.  

Po zalogowaniu się należy określić adres IP węzła.

Użyj polecenia, `ifconfig` aby wyświetlić adresy przypisane do tego systemu.

Na przykład polecenie `ifconfig | grep -B5 inet` wyszukuje porty z adresami internetowymi i podaje pięć wierszy kontekstu, aby wyświetlić identyfikator portu.

Zapisz dowolny adres IP wyświetlany w raporcie ifconfig. Adresy wymienione z nazwami portów, takimi jak e0a lub e0b, są dobrymi opcjami. Nie należy używać żadnych adresów IP wymienionych z nazwami e7*, ponieważ nazwy te są używane tylko dla portów usług iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Ładowanie kreatora konfiguracji klastra

Użyj narzędzia konfiguracji klastra opartego na przeglądarce, aby utworzyć klaster. 

Wprowadź adres IP węzła w przeglądarce internetowej. Jeśli przeglądarka podaje komunikat o niezaufanej witrynie, i tak przejdź do witryny. (Poszczególne węzły narzędzia Azure FXT Edge Filer nie mają certyfikatów zabezpieczeń dostarczonych przez urząd certyfikacji).

![Strona logowania panelu sterowania w oknie przeglądarki](media/fxt-cluster-create/unconfigured-node-gui.png)

Pozostaw pola **Nazwa użytkownika** i **Hasło** puste. Kliknij **przycisk Zaloguj,** aby załadować stronę tworzenia klastra.

![Początkowy ekran konfiguracji nieskonfigurowanego węzła w panelu sterowania graficznym oparty na przeglądarce. Pokazuje opcje aktualizacji oprogramowania, ręcznego konfigurowania klastra lub konfigurowania klastra z pliku instalacyjnego.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Tworzenie klastra

Narzędzie konfiguracji klastra prowadzi użytkownika przez zestaw ekranów w celu utworzenia klastra Azure FXT Edge Filer. Przed rozpoczęciem upewnij się, że masz gotowe [wymagane informacje.](#gather-information-for-the-cluster) 

### <a name="creation-options"></a>Opcje tworzenia

Pierwszy ekran daje trzy opcje. Użyj opcji konfiguracji ręcznej, chyba że masz specjalne instrukcje od personelu pomocy technicznej.

Kliknij **pozycję I skonfiguruj klaster ręcznie,** aby załadować nowy ekran opcji konfiguracji klastra. 

Inne opcje są rzadko używane:

* "Aktualizacja obrazu systemu" monituje o zainstalowanie nowego oprogramowania systemu operacyjnego przed utworzeniem klastra. (Aktualnie zainstalowana wersja oprogramowania znajduje się w górnej części ekranu). Musisz podać plik pakietu oprogramowania - adres URL i nazwę użytkownika/ hasło lub przesyłając plik z komputera. 

* Opcja pliku instalatora klastra jest czasami używana przez dział obsługi klienta i pomocy technicznej firmy Microsoft. 

## <a name="cluster-options"></a>Opcje klastra

Na następnym ekranie zostanie wyświetlony monit o skonfigurowanie opcji dla nowego klastra.

Strona jest podzielona na dwie główne sekcje: **Konfiguracja podstawowa** i **Konfiguracja sieci.** Sekcja konfiguracji sieci zawiera również podsekcje: jedną dla sieci **zarządzania** i jedną dla sieci **klastra.**

### <a name="basic-configuration"></a>Konfiguracja podstawowa

W górnej sekcji wprowadź podstawowe informacje dotyczące nowego klastra.

![Szczegóły sekcji "Konfiguracja podstawowa" na stronie graficznego przeglądarki. Pokazuje trzy pola (nazwa klastra, hasło administratora, potwierdź hasło)](media/fxt-cluster-create/basic-configuration.png) 

* **Nazwa klastra** — wprowadź unikatową nazwę klastra.

  Nazwa klastra musi spełniać następujące kryteria:
  
  * Długość od 1 do 16 znaków
  * Może zawierać litery, cyfry oraz znaki kreski (-) i podkreślenia (_) 
  * Nie może zawierać innych znaków interpunkcyjnych ani znaków specjalnych
  
  Tę nazwę można zmienić później na stronie konfiguracji**konfiguracji konfiguracji ogólne klastra.** **Cluster** >  (Aby uzyskać więcej informacji na temat ustawień [klastra, przeczytaj Przewodnik konfiguracji klastra,](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)który nie jest częścią tego zestawu dokumentacji).

  > [!NOTE] 
  > Nazwa klastra służy do identyfikowania informacji o systemie przekazanych do obsługi monitorowania lub rozwiązywania problemów, dlatego warto podać nazwę firmy.

* **Hasło administratora** — ustawianie hasła `admin`dla domyślnego użytkownika administracyjnego , .
  
  Należy skonfigurować indywidualne konta użytkowników dla każdej osoby, która administruje klastrem, ale nie można usunąć użytkownika `admin`. Zaloguj się `admin` tak, jakby trzeba było utworzyć dodatkowych użytkowników.
 
  Hasło można zmienić `admin` na stronie Ustawienia**użytkowników** **administracyjnych** > w Panelu sterowania klastra. Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją **użytkowników** w [Przewodniku konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Konfiguracja sieci

Sekcja **Sieć monituje** o określenie infrastruktury sieciowej, z której będzie korzystać klaster. 

Istnieją dwie oddzielne sieci do skonfigurowania:

* *Sieć zarządzania* zapewnia administratorowi dostęp do klastra w celu konfiguracji i monitorowania. Podany w tym miejscu adres IP jest używany podczas łączenia się z Panelem sterowania lub w celu uzyskania dostępu do protokołu SSH. 

  Większość klastrów używa tylko jednego adresu IP zarządzania, ale jeśli chcesz dodać interfejsy, możesz to zrobić po utworzeniu klastra.

* *Sieć klastra* jest używana do komunikacji między węzłami klastra oraz między węzłami klastra a magazynem zaplecza (core filers).

Sieć skierowana do klienta jest konfigurowana później, po utworzeniu klastra.

Ta sekcja zawiera również konfigurację serwerów DNS i NTP, które są używane przez obie sieci.

### <a name="configure-the-management-network"></a>Konfigurowanie sieci zarządzania

Ustawienia w sekcji **Zarządzanie** są dla sieci, która zapewnia administratorowi dostęp do klastra.

![szczegóły sekcji "Zarządzanie", z polami dla 5 opcji i polem wyboru dla sieci zarządzania 1Gb](media/fxt-cluster-create/management-network-filled.png)

* **Adres IP zarządzania** — umożliwia określenie adresu IP używanego do uzyskiwania dostępu do Panelu sterowania klastra. Ten adres zostanie zgłoszony przez węzeł podstawowy klastra, ale automatycznie zostanie przeniesiony do w dobrej kondycji, jeśli oryginalny węzeł podstawowy stanie się niedostępny.

  Większość klastrów używa tylko jednego adresu IP zarządzania. Jeśli chcesz więcej niż jeden, można dodać je po utworzeniu klastra przy użyciu **klastra** > **administracyjne ustawienia sieci** strony. Więcej informacji można przeczytać w [Przewodniku konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Maska sieci** — umożliwia określenie maski sieciowej dla sieci zarządzania.

* **Router** — umożliwia wprowadzenie domyślnego adresu bramy używanego przez sieć zarządzania.

* **Tag sieci VLAN (opcjonalnie)** — jeśli klaster używa tagów sieci VLAN, określ znacznik sieci zarządzania.

  Dodatkowe ustawienia sieci VLAN są konfigurowane na stronie ustawień sieci**VLAN** **klastra.** >  Przeczytaj [artykuł Praca z sieciami VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) i > sieci [VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) w Przewodniku konfiguracji klastra, aby dowiedzieć się więcej.

* **Mtu** - W razie potrzeby dostosuj maksymalną jednostkę transmisji (MTU) dla sieci zarządzania klastra.

* **Użyj sieci mgmt 1 Gb** — zaznacz to pole wyboru, jeśli chcesz przypisać dwa porty sieciowe 1GbE w węzłach FXT tylko do sieci zarządzania. (Musisz mieć porty 25GbE/ 10GbE dostępne dla wszystkich innych ruchów.) Jeśli to pole wyboru nie jest zaznaczone, sieć zarządzania korzysta z portu o najwyższej szybkości. 

### <a name="configure-the-cluster-network"></a>Konfigurowanie sieci klastra 

Ustawienia sieci klastra mają zastosowanie do ruchu między węzłami klastra oraz między węzłami klastra a głównymi filerami.

![szczegóły sekcji "Klaster", z polami do wprowadzenia sześciu wartości](media/fxt-cluster-create/cluster-network-filled.png)

* **Pierwszy adres IP** i **ostatni adres IP** — wprowadź adresy IP definiujące zakres używany do komunikacji klastra wewnętrznego. Adresy IP używane w tym miejscu muszą być ciągłe i nie są przypisywane przez protokół DHCP.

  Po utworzeniu klastra można dodać więcej adresów IP. Użyj strony Ustawienia sieci**klastrów** **klastrów** > [(dokumentacja Przewodnika konfiguracji klastra).](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)

  Wartość **liczba ip w zakresie** jest obliczana i wyświetlana automatycznie.

* **Maska sieciowa niemakowa (opcjonalnie)** — umożliwia określenie maski sieciowej dla sieci klastra. 

  System automatycznie sugeruje wartość maski sieciowej wprowadzonej dla sieci zarządzania; w razie potrzeby go zmienić.

* **Router klastra (opcjonalnie)** — umożliwia określenie domyślnego adresu bramy używanego przez sieć klastra. 

  System automatycznie sugeruje ten sam adres bramy, który został podany dla sieci zarządzania.

* **Tag VLAN klastra (opcjonalnie)** — jeśli klaster używa znaczników sieci VLAN, określ znacznik sieci klastra.

* **Jednostka MTU niezwiązane z mgmt (opcjonalnie)** — w razie potrzeby dostosuj maksymalną jednostkę transmisji (MTU) dla sieci klastra.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurowanie systemu DNS i NTP klastra 

Poniżej sekcji **Klaster** znajdują się pola do określania serwerów DNS i NTP oraz włączania agregacji łączy. Te ustawienia dotyczą wszystkich sieci używanych przez klaster.

![Szczegóły sekcji konfiguracji DNS/NTP z trzema polami dla serwerów DNS, polami dla domeny DNS i wyszukiwania DNS, trzema polami serwerów NTP i menu rozwijanym dla opcji agregacji łączy](media/fxt-cluster-create/dns-ntp-filled.png)

* **Serwery DNS** — wprowadź adres IP co najmniej jednego serwera dns (domain name system).

  System DNS jest zalecany dla wszystkich klastrów i wymagany, jeśli chcesz używać protokołu SMB, AD lub Protokołu Kerberos. 
  
  Aby uzyskać optymalną wydajność, skonfiguruj serwer DNS klastra pod kątem równoważenia obciążenia okrężnego zgodnie z opisem w [temacie Konfigurowanie systemu DNS dla klastra plików usługi Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domena DNS** — wprowadź nazwę domeny sieciowej, której będzie używał klaster.

* **Wyszukiwanie DNS** — opcjonalnie wprowadź dodatkowe nazwy domen, które system powinien przeszukiwać w celu rozpoznawania zapytań DNS. Można dodać maksymalnie sześć nazw domen, oddzielonych spacjami.

* **Serwery NTP** — określ jeden lub trzy serwery protokołu czasu sieciowego (NTP) w dostarczonych polach. Można użyć nazwy hosta lub adresy IP.

* **Agregacja łączy** — agregacja łączy umożliwia dostosowanie sposobu, w jaki porty Ethernet w węzłach FXT klastra obsługują różne typy ruchu. Aby dowiedzieć się więcej, przeczytaj [przewodnik agregacji łączy](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) w przewodniku konfiguracji klastra.

### <a name="click-the-create-button"></a>Kliknij przycisk Utwórz

Po podaszeniu wszystkich wymaganych ustawień w formularzu kliknij przycisk **Utwórz klaster.**

![dolnej części wypełnionego formularza z kursorem nad przyciskiem create w prawym dolnym rogu](media/fxt-cluster-create/create-cluster.png)

System wyświetla komunikat podczas tworzenia klastra.

![komunikat o stanie konfiguracji klastra w przeglądarce: "Węzeł FXT tworzy teraz klaster. Zajmie to kilka minut. Po utworzeniu klastra odwiedź to łącze, aby ukończyć konfigurację." z hiperłączem na "odwiedź ten link"](media/fxt-cluster-create/creating-message.png)

Po kilku chwilach możesz kliknąć łącze w wiadomości, aby przejść do Panelu sterowania klastra. (To łącze prowadzi do adresu IP określonego w **adresie IP zarządzania).** Trwa od 15 sekund do jednej minuty, aby link stał się aktywny po kliknięciu przycisku Utwórz. Jeśli interfejs internetowy nie zostanie załadowany, odczekaj jeszcze kilka sekund, a następnie kliknij ponownie łącze. 

Tworzenie klastra zajmuje minutę lub więcej, ale można zalogować się do Panelu sterowania, gdy proces trwa. Normalne jest, że strona pulpitu nawigacyjnego panelu sterowania wyświetla ostrzeżenia do czasu zakończenia procesu tworzenia klastra. 

## <a name="open-the-settings-pages"></a>Otwieranie stron Ustawienia 

Po utworzeniu klastra należy dostosować jego konfigurację dla sieci i przepływu pracy. 

Użyj interfejsu internetowego Panelu sterowania, aby skonfigurować nowy klaster. Skorzystaj z łącza z ekranu stanu tworzenia klastra lub przejdź do zarządzania adresem IP ustawionym w klastrze.

Zaloguj się do interfejsu internetowego przy użyciu nazwy użytkownika `admin` i hasła ustawionego podczas tworzenia klastra.

![przeglądarka internetowa z polami logowania w panelu sterowania](media/fxt-cluster-create/admin-login.png)

Panel sterowania zostanie otwarty i pokazuje stronę **pulpitu nawigacyjnego.** Po zakończeniu tworzenia klastra wszystkie komunikaty ostrzegawcze powinny być usuwane z ekranu.

Kliknij kartę **Ustawienia,** aby skonfigurować klaster.

Na karcie **Ustawienia** na lewym pasku bocznym jest wyświetlane menu stron konfiguracyjnych. Strony są uporządkowane według kategorii. Kliknij formant + lub - u góry nazwy kategorii, aby rozwinąć lub ukryć poszczególne strony.

![Karta Ustawienia panelu sterowania (w przeglądarce) z załadowaną stroną Ustawienia ogólne klastra >](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Kroki konfiguracji klastra

W tym momencie procesu istnieje klaster, ale ma tylko jeden węzeł, nie ma adresów IP skierowanych do klienta i nie ma magazynu zaplecza. Dodatkowe kroki konfiguracji są potrzebne, aby przejść z nowo utworzonego klastra do systemu pamięci podręcznej, który jest gotowy do obsługi przepływu pracy.

### <a name="required-configuration"></a>Wymagana konfiguracja

Te kroki są potrzebne dla większości lub wszystkich klastrów. 

* Dodawanie węzłów do klastra 

  Trzy węzły są standardem, ale wiele klastrów produkcyjnych ma więcej — maksymalnie 24 węzły.

  Przeczytaj [dodaj węzły klastra,](fxt-add-nodes.md) aby dowiedzieć się, jak dodać inne jednostki filera usługi Azure FXT Edge do klastra i włączyć wysoką dostępność.

* Określanie magazynu zaplecza

  Dodaj podstawowe definicje *filer* dla każdego systemu magazynu zaplecza, który będzie używany przez klaster. Przeczytaj [dodaj magazyn zaplecza i skonfiguruj wirtualną przestrzeń nazw,](fxt-add-storage.md#about-back-end-storage) aby dowiedzieć się więcej.

* Konfigurowanie dostępu klienta i wirtualnego obszaru nazw 

  Utwórz co najmniej jeden serwer wirtualny (vserver) i przypisz mu zakres adresów IP dla komputerów klienckich do użycia. Należy również skonfigurować obszar nazw klastra (czasami nazywany globalnym obszarem nazw lub GNS), wirtualną funkcję systemu plików, która umożliwia mapowanie eksportu magazynu zaplecza do ścieżek wirtualnych. Obszar nazw klastra zapewnia klientom spójną i dostępną strukturę systemu plików, nawet jeśli przełączysz nośniki pamięci masowej zaplecza. Obszar nazw może również zapewnić przyjazną dla użytkownika hierarchię magazynu wirtualnego dla kontenerów obiektów blob platformy Azure lub innego obsługiwanego magazynu obiektów w chmurze.

  Przeczytaj [pozycję Konfigurowanie obszaru nazw, aby](fxt-add-storage.md#configure-the-namespace) uzyskać szczegółowe informacje. Ten krok obejmuje:
  * Tworzenie serwerów vservers
  * Konfigurowanie skrzyżowań między widokiem sieci klienta a pamięcią zaplecza 
  * Definiowanie adresów IP klienta obsługiwanych przez każdy serwer vserver

  > [!Note] 
  > Przed rozpoczęciem konfigurowania GNS klastra zaleca się znaczące planowanie. Przeczytaj [sekcje Korzystanie z globalnego obszaru nazw](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) oraz Tworzenie i praca z [serwerami VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) w Przewodniku konfiguracji klastra, aby uzyskać pomoc.

* [Dostosowywanie ustawień sieciowych](fxt-configure-network.md)

  Istnieje kilka ustawień związanych z siecią, które powinny być weryfikowane lub dostosowane dla nowego klastra. Przeczytaj [artykuł Dopasowywanie ustawień sieciowych,](fxt-configure-network.md) aby uzyskać szczegółowe informacje o następujących elementach:

  * Weryfikowanie konfiguracji DNS i NTP 
  * Konfigurowanie usług katalogowych w razie potrzeby 
  * Konfigurowanie sieci VLAN
  * Konfigurowanie serwerów proxy
  * Dodawanie adresów IP do sieci klastra
  * Przechowywanie certyfikatów szyfrowania

* [Konfigurowanie monitorowania pomocy technicznej](#enable-support)

  Musisz zaakceptować zasady zachowania poufności informacji dla narzędzia konfiguracyjnego i skonfigurować ustawienia przekazywania pomocy technicznej w tym samym czasie.

  Klaster może automatycznie przekazywać dane dotyczące rozwiązywania problemów dotyczące klastra, w tym statystyki i pliki debugowania. Te przesłane pliki umożliwiają działowi obsługi klienta i pomocy technicznej firmy Microsoft najlepszą możliwą obsługę. Można dostosować to, co jest monitorowane, i opcjonalnie włączyć proaktywną pomoc techniczną i usługę zdalnego rozwiązywania problemów.  

### <a name="optional-configuration"></a>Konfiguracja opcjonalna

Te kroki nie są wymagane dla wszystkich klastrów. Są one potrzebne dla niektórych typów przepływów pracy lub dla niektórych stylów zarządzania klastrem. 

* Dostosowywanie ustawień węzła

  Nazwy węzłów można ustawić i skonfigurować porty IPMI węzła na poziomie całego klastra lub indywidualnie. Jeśli skonfigurujesz te ustawienia przed dodaniem węzłów do klastra, nowe węzły mogą automatycznie odbierać ustawienia po dołączeniu. Opcje są opisane w starszym dokumencie tworzenia klastra [Dostosowywanie ustawień węzła](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Niektóre dokumenty dotyczące tego produktu nie są jeszcze dostępne w witrynie dokumentacji platformy Microsoft Azure. Łącza do [przewodnika konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) i starszej wersji [Przewodnika tworzenia klastrów](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) przeprowadzą Cię do oddzielnej witryny internetowej hostowanego przez github. 

* Konfigurowanie SMB

  Aby zezwolić SMB na dostęp do klastra, a także systemu plików NFS, należy skonfigurować SMB i włączyć ją. SMB (czasami nazywany CIFS) jest zwykle używany do obsługi klientów systemu Microsoft Windows.

  Planowanie i konfigurowanie SMB wymaga więcej niż kliknięcia kilku przycisków w Panelu sterowania. W zależności od wymagań systemu, SMB może mieć wpływ na sposób definiowania podstawowych filerów, liczbę utworzonych serwerów vserver, sposób konfigurowania skrzyżowań i przestrzeni nazw, uprawnienia dostępu i inne ustawienia.

  Aby uzyskać więcej informacji, przeczytaj sekcję Przewodnik konfiguracji [klastra Konfigurowanie dostępu SMB.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html)

* Instalowanie dodatkowych licencji

  Jeśli chcesz używać magazynu w chmurze innego niż Azure Blob, należy zainstalować dodatkową licencję funkcji. Aby uzyskać szczegółowe informacje na temat zakupu licencji flashcloud<sup>TM,</sup> skontaktuj się z przedstawicielem firmy Microsoft. Szczegóły są wyjaśnione w [Dodaj magazyn zaplecza i skonfiguruj wirtualną przestrzeń nazw](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Włącz obsługę

Klaster usługi Azure FXT Edge Filer może automatycznie przekazywać dane pomocy technicznej dotyczące klastra. Te przesłane pliki pozwalają pracownikom zapewnić najlepszą możliwą obsługę klienta.

Wykonaj następujące kroki, aby skonfigurować przekazywanie pomocy technicznej.

1. Przejdź do strony Ustawienia**obsługi** **klastrów.** >  Zaakceptuj politykę prywatności. 

   ![Zrzut ekranu przedstawiający Panel sterowania i wyskakujące okno z przyciskiem Potwierdź, aby zaakceptować zasady zachowania poufności informacji](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Kliknij trójkąt po lewej stronie **informacji o kliencie,** aby rozwinąć sekcję.
1. Kliknij przycisk **Ponownie przywdziaj informacje o przekazywaniu.**
1. Ustaw nazwę pomocy technicznej klastra w **unikatowej nazwie klastra** — upewnij się, że jednoznacznie identyfikuje klaster do obsługi personelu.
1. Zaznacz pola wyboru **Monitorowanie statystyk,** **Przesyłanie informacji ogólnych**i **Przekazywanie informacji o awariach**.
1. Kliknij **przycisk Prześlij**.  

   ![Zrzut ekranu zawierający ukończoną sekcję informacji o kliencie na stronie ustawień pomocy technicznej](media/fxt-cluster-create/fxt-support-info.png)

1. Kliknij trójkąt po lewej stronie **bezpiecznej pomocy proaktywnej (SPS),** aby rozwinąć sekcję.
1. Zaznacz pole wyboru **Włącz łącze SPS**.
1. Kliknij **przycisk Prześlij**.

   ![Zrzut ekranu zawierający ukończoną sekcję Bezpieczne wsparcie proaktywne na stronie ustawień pomocy technicznej](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu klastra podstawowego i zaakceptowaniu zasad ochrony prywatności dodaj pozostałe węzły klastra. 

> [!div class="nextstepaction"]
> [Dodawanie węzłów klastra](fxt-add-nodes.md)
