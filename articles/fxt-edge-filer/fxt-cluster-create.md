---
title: Tworzenie klastra systemu Microsoft Azure FXT krawędzi filtr
description: Jak utworzyć klaster hybrydowy magazyn pamięci podręcznej za pomocą filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 1bfe8f0efce0a844263fc65df0ad927114886769
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450541"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Samouczek: Tworzenie klastra filtr Edge FXT platformy Azure

Po zainstalowaniu i zainicjować węzłami sprzętowymi filtr Edge FXT platformy Azure dla swojej pamięci podręcznej, należy korzystać z oprogramowania klastra FXT, aby utworzyć klaster pamięci podręcznej. 

Ten samouczek przeprowadzi Cię przez kroki konfigurowania sprzętu węzłów klastra. 

Z tego samouczka dowiesz się: 

> [!div class="checklist"]
> * Jakie informacje są potrzebne przed rozpoczęciem tworzenia klastra
> * Różnica między sieć zarządzania klastrem, sieci klastra i sieci ukierunkowane na klienta
> * Jak połączyć się z węzłem klastra 
> * Tworzenie klastra przy użyciu jednego węzła filtr Edge FXT platformy Azure
> * Jak zalogować się do klastra w Panelu sterowania, aby skonfigurować ustawienia klastra

Ta procedura zajmuje od 15 45 minut, w zależności od ilości badań, które należy wykonać, aby zidentyfikować IP adresów i zasobów sieciowych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy spełnić następujące wymagania wstępne:

* Zainstaluj co najmniej trzech systemów sprzętowych filtr Edge FXT platformy Azure w centrum danych 
* Połącz odpowiednie przewodów zasilania i sieci w systemie  
* Włącz co najmniej jeden węzeł filtr Edge FXT platformy Azure i [ustaw jego hasło główne](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Zbierz informacje dotyczące klastra 

Potrzebne do utworzenia klastra filtr Edge FXT Azure następujące informacje:

* Nazwa klastra

* Hasło administracyjne można ustawić dla klastra

* Adresy IP:

  * Pojedynczy adres IP dla zarządzania klastrem, netmask i routera, aby używać dla sieci zarządzania
  * Pierwsze i ostatnie adresy IP w ciągły zakres adresów IP do komunikacji klastra (węzeł na węzeł). Zobacz [dystrybucji adresów IP](#ip-address-distribution)poniżej, aby uzyskać szczegółowe informacje. 
  * (Adresy IP ukierunkowane na klienta są ustawione po utworzeniu klastra).

* Informacje o infrastruktury sieci:

  * Adres IP serwera DNS dla klastra
  * Nazwa domeny DNS dla klastra
  * Nazwa lub adres IP dla klastra serwerów NTP (jeden serwer lub trzech lub więcej) 
  * Czy chcesz włączyć IEEE 802.1ax-2008 link agregacji w interfejsach klastra
  * Po włączeniu agregacji link umożliwia określenie, czy użyć IEEE 802.3ad (LACP) dynamiczne agregacji

Te elementy infrastruktury sieci można skonfigurować po utworzeniu klastra, ale lepiej to zrobić w czasie jego tworzenia. 

### <a name="ip-address-distribution"></a>Dystrybucja adresów IP

Filtr Edge FXT Azure hybrydowego magazynu pamięci podręcznej klastra używa adresów IP w trzy kategorie:

* Zarządzanie IP: Pojedynczy adres IP do zarządzania klastrem

  Ten adres służy jako punkt wejścia do dostępu do narzędzi konfiguracji klastra (panel sterowania opartego na sieci web lub interfejsu API XML-RPC). Ten adres jest automatycznie przypisywana do podstawowego węzła w klastrze i automatycznie przenosi Jeśli zmieni się węzła podstawowego.

  Inne adresy IP mogą być używane dostęp do panelu sterowania, ale adres IP zarządzania zaprojektowano w celu zapewnienia dostępu, nawet wtedy, gdy poszczególne węzły w trybie Failover.

* Sieć klastra: Zakres adresów IP do komunikacji klastra

  Sieć klastra jest używany do komunikacji między węzłami klastra i pobieranie plików z magazynu zaplecza (filtrach core).

  **Najlepszym rozwiązaniem jest:** Przydziel jeden adres IP na fizyczny port umożliwiający komunikację klastra w każdym węźle filtr Edge FXT platformy Azure. Klaster automatycznie przypisuje poszczególne węzły adresów w określonym zakresie.

* Sieć ukierunkowane na klienta: Zakres adresów IP klientów używających do żądania i zapis plików

  Adresy sieciowe klienta są używane przez klientów dostępu do podstawowych filtr danych za pośrednictwem klastra. Na przykład klient systemu plików NFS może zainstalować jednego z tych adresów.

  **Najlepszym rozwiązaniem jest:** Przydziel jeden adres IP na fizyczny port używany do komunikacji z klientem w każdym węźle FXT serii.

  Klaster możliwie najbardziej równomiernie rozkłada ukierunkowane na klienta, adresy IP na jego składowych węzłów.

  Dla uproszczenia wielu administratorów, należy skonfigurować jedną nazwą DNS przy użyciu działania okrężnego konfiguracji DNS (RRDNS) ułatwiają rozkładania żądań klientów na zakres adresów. Ta konfiguracja umożliwia także wszyscy klienci używają tego samego polecenia instalacji dostęp do klastra. Odczyt [Konfigurowanie serwera DNS](fxt-configure-network.md#configure-dns-for-load-balancing) Aby uzyskać więcej informacji.

Do utworzenia nowego klastra należy określić adres IP zarządzania i zakres adresów sieci klastra. Po utworzeniu klastra są określone adresy ukierunkowane na klienta.

## <a name="connect-to-the-first-node"></a>Połącz się z pierwszym węzłem

Możesz nawiązać połączenie z dowolnego z węzłów FXT zainstalowanych i skonfigurować klaster za pomocą swojego oprogramowania systemu operacyjnego.

Jeśli jeszcze nie zrobiono, Włącz co najmniej jeden z węzłów FXT klastra i upewnij się, że ma ona połączenie sieciowe i adresy IP. Należy ustawić nowe hasło główne do aktywowania węzła, dlatego należy wykonać czynności opisane w [Ustawianie haseł sprzętu](fxt-node-password.md) Jeśli nie zostały już wykonane.

Aby sprawdzić połączenie sieciowe, upewnij się, że podświetlonych połączenia sieciowego węzła diod LED (i, jeśli to konieczne, wskaźniki w sieci Przełącz się do której jest dołączona). Diod LED wskaźnika są opisane w [filtr Edge FXT Azure Monitor stanu sprzętu](fxt-monitor.md).

Podczas rozruchu węzła zażąda adresu IP. Jeśli jest podłączona do serwera DHCP, akceptuje adres IP udostępnianemu przez protokół DHCP. (Ten adres IP jest tymczasowe. Zostanie on zmieniony po utworzeniu klastra.)

Jeśli nie jest podłączony do serwera DHCP lub nie otrzyma odpowiedzi, węzeł użyje Bonjour oprogramowania można ustawić własnym przypisanego adresu IP w postaci 169.254. \*. \*. Jednak należy ustawić statycznego adresu IP na jedną z kart sieciowych węzła przed jego użyciem w celu utworzenia klastra. Instrukcje znajdują się w tym dokumencie starszej wersji; Aby uzyskać najnowsze informacje, skontaktuj się z Microsoft Service i pomocy technicznej: [Dodatek A: Ustawianie statycznego adresu IP na węźle FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Znajdowanie adresu IP

Nawiązać węzeł filtr Edge FXT platformy Azure, aby znaleźć adres IP. Można użyć kabel szeregowy bezpośrednie połączenie do portów USB i VGA lub nawiązać połączenie za pośrednictwem przełącznika KVM. (Port połączenia szczegółów, zobacz [hasła początkowego](fxt-node-password.md).)

Po nawiązaniu połączenia, zaloguj się przy użyciu nazwy użytkownika `root` i hasło, które można ustawić, gdy węzeł zostanie uruchomiony po raz pierwszy.  

Po zalogowaniu się musisz określić adres IP węzła.

Użyj polecenia `ifconfig` Aby wyświetlić adresy przypisane do tego systemu.

Na przykład polecenie `ifconfig | grep -B5 inet` wyszukuje portów przy użyciu adresów internetowych oraz zapewnia pięć wierszy kontekstu do wyświetlenia identyfikatora portu.

Zanotuj dowolnego adresu IP w raporcie ifconfig. Adresy na liście za pomocą nazwy portu, takich jak e0a lub e0b opcje są dobre. Nie należy używać dowolnych adresów IP wymienionych e7 * nazwą, ponieważ te nazwy są używane tylko w przypadku portów IPMI porty sieciowe nie regularne.  

## <a name="load-the-cluster-configuration-wizard"></a>Obciążenia Kreator konfiguracji klastra

Aby utworzyć klaster, należy użyć narzędzia konfiguracji klastra opartego na przeglądarce. 

Wprowadź adres IP węzła w przeglądarce sieci web. Jeśli przeglądarka daje komunikat o witrynie jest zaufany, przejdź do witryny mimo to. (Węzły poszczególnych filtr Edge FXT Azure nie mają certyfikaty zabezpieczeń dostarczone do urzędu certyfikacji).

![Strona logowania w Panelu sterowania w oknie przeglądarki](media/fxt-cluster-create/unconfigured-node-gui.png)

Pozostaw **Username** i **hasło** pola puste. Kliknij przycisk **logowania** do załadowania strony tworzenia klastra.

![Ekran konfiguracji początkowej dla nieskonfigurowany węzeł, w Panelu sterowania graficznego interfejsu użytkownika opartego na przeglądarce. Pokazuje opcje aktualizacji oprogramowania, ręcznie skonfigurować klaster lub skonfigurować klaster z pliku konfiguracji.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Tworzenie klastra

Narzędzie konfiguracji klastra przeprowadzi Cię przez zestaw ekranów w celu utworzenia klastra filtr Edge FXT platformy Azure. Upewnij się, że masz [wymaganych informacji](#gather-information-for-the-cluster) gotowe przed uruchomieniem. 

### <a name="creation-options"></a>Opcje tworzenia

Pierwszy ekran zawiera trzy opcje. Jeśli nie masz specjalnych instrukcji z pracownikami działu pomocy technicznej, należy użyć opcji ręcznej konfiguracji.

Kliknij przycisk **klastra będzie ręcznie skonfigurować** załadować nowy ekran opcje konfiguracji klastra. 

Inne opcje są rzadko używane:

* "Aktualizuj obraz systemu" wyświetli monit o zainstalowanie nowego oprogramowania systemu operacyjnego przed utworzeniem klastra. (Aktualnie zainstalowana wersja oprogramowania znajduje się w górnej części ekranu). Należy podać plik pakietu oprogramowania — albo, adres URL i nazwę użytkownika/hasło lub przekazanie pliku z komputera. 

* Opcja pliku konfiguracji klastra jest czasami używane przez dział obsługi klienta firmy Microsoft i pomocy technicznej. 

## <a name="cluster-options"></a>Opcje klastra

Następny ekran jest wyświetlany monit o Konfigurowanie opcji dla nowego klastra.

Strony jest podzielony na dwie główne sekcje **podstawową konfigurację** i **konfiguracja sieci**. Sekcja konfiguracji sieci ma również podsekcje: jeden dla **zarządzania** sieci, a drugi dla **klastra** sieci.

### <a name="basic-configuration"></a>Konfiguracja podstawowa

W górnej sekcji podaj podstawowe informacje dla nowego klastra.

![Szczegółowe informacje na temat sekcji "Konfiguracja podstawowa" na stronie przeglądarki graficznego interfejsu użytkownika. Pokazuje trzy pola (nazwa klastra i hasło administratora, potwierdź hasło)](media/fxt-cluster-create/basic-configuration.png) 

* **Nazwa klastra** — wprowadź unikatową nazwę dla klastra.

  Nazwa klastra musi spełniać następujące kryteria:
  
  * 1 do 16 znaków
  * Może zawierać litery, cyfry i kreski (-) i znaki podkreślenia (_) 
  * Nie może zawierać innych znaków interpunkcyjnych ani znaków specjalnych
  
  Tę nazwę można zmienić później **klastra** > **ustawienia głównej** strona konfiguracji. (Aby uzyskać więcej informacji na temat ustawień klastra, przeczytaj [przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), który nie jest częścią tego zestawu dokumentacji.)

  > [!NOTE] 
  > Nazwa klastra służy do identyfikowania informacji o systemie przekazany do obsługi w przypadku monitorowania i rozwiązywania problemów, więc warto uwzględnić nazwę swojej firmy.

* **Hasło administratora** — Ustaw hasło dla użytkownika administracyjnego domyślne `admin`.
  
  Należy skonfigurować indywidualne konta użytkowników, dla każdej osoby, która zarządza klastrem, ale nie można usunąć użytkownika `admin`. Zaloguj się jako `admin` Jeśli musisz utworzyć dodatkowych użytkowników.
 
  Można zmienić hasła dla `admin` w **administracji** > **użytkowników** strony ustawień w klastrze Panelu sterowania. Aby uzyskać więcej informacji, przeczytaj **użytkowników** w dokumentacji [przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Konfiguracja sieci

**Sieć** sekcji monituje o określenie infrastruktury sieci, które będą używane przez klaster. 

Istnieją dwa odrębne sieci można skonfigurować:

* *Sieć zarządzania* zapewnia dostęp administratora do klastra do konfiguracji i monitorowania. Adres IP podany w tym miejscu jest używany podczas nawiązywania połączenia z Panelu sterowania lub dostępu SSH. 

  Większość klastrów używać tylko jeden adres IP, ale jeśli chcesz dodać interfejsy możesz to zrobić po utworzeniu klastra.

* *Sieci klastrów* jest używany do komunikacji między węzłami klastra oraz między węzłami klastra i magazynu zaplecza (filtrach core).

Ukierunkowane na klienta sieć jest skonfigurowana później, po utworzeniu klastra.

Ta sekcja zawiera również konfigurację dla serwerów DNS i NTP, które są używane przez obie sieci.

### <a name="configure-the-management-network"></a>Skonfiguruj sieć zarządzania

Ustawienia w **zarządzania** sekcji służą do sieci, która zapewnia dostęp administratora do klastra.

![szczegółowe informacje na temat sekcji "Zarządzanie", z polami 5 opcji i pola wyboru dla sieci zarządzania 1Gb](media/fxt-cluster-create/management-network-filled.png)

* **Zarządzanie IP** — Określ adres IP, który będzie uzyskiwać dostęp do klastra Panel sterowania do. Ten adres będzie wymagana przez węzła podstawowego klaster, ale automatycznie przenosi do prawidłowego węzła Jeśli oryginalna węzeł podstawowy stanie się niedostępny.

  Większość klastrów Użyj tylko jeden adres IP zarządzania. Jeśli chcesz więcej niż jeden, można dodać je po utworzeniu klastra przy użyciu **klastra** > **administracyjnych sieci** strony ustawień. Dowiedz się więcej w [przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Maska sieci** -określić maskę sieci dla sieci zarządzania.

* **Router** — wprowadź adres bramy domyślne używane przez sieć zarządzania.

* **(Opcjonalnie) tagu sieci VLAN** — Jeśli klaster używa znaczników sieci VLAN, określ znacznik, dla sieci zarządzania.

  Dodatkowe ustawienia sieci VLAN są konfigurowane w **klastra** > **sieci VLAN** strony ustawień. Odczyt [pracy z sieciami VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) i [klastra > sieć VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) w przewodniku po konfiguracji klastra, aby dowiedzieć się więcej.

* **Rozmiar jednostki MTU** — w razie potrzeby dostosuj maksymalna jednostka transmisji (MTU) dla klastra sieć zarządzania.

* **Sieć zarządzania 1Gb użycia** — zaznacz to pole wyboru, jeśli chcesz przypisać sieci 1GbE dwa porty węzłów FXT z siecią zarządzania. Jeśli nie zaznaczysz to pole sieć zarządzania korzysta z najwyższy dostępny port szybkość. 

### <a name="configure-the-cluster-network"></a>Skonfigurowanie sieci klastrów 

Ustawienia sieci klastra stosowane do ruchu między węzłami klastra oraz między węzłami klastra i filtrach core.

![szczegółowe informacje na temat sekcji "Klastra", przy użyciu pól, aby wprowadzić wartości sześciu](media/fxt-cluster-create/cluster-network-filled.png)

* **Pierwszy adres IP** i **ostatnim IP** -wprowadź adresy IP, które definiują zakres używany do komunikacji wewnątrz klastra. Adresy IP używane w tym miejscu musi być ciągłe i nie przypisano przez protokół DHCP.

  Po utworzeniu klastra można dodać więcej adresów IP. Użyj **klastra** > **sieci klastrów** strony ustawień ([dokumentacji przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Wartość w **liczbę adresów IP w zakresie** jest obliczane i wyświetlane automatycznie.

* **(Opcjonalnie) netmask non-mgmt** -określić maskę sieci sieci klastra. 

  System automatycznie sugeruje wartością maski podsieci, ta wprowadzona dla sieci zarządzania; Zmień go, jeśli to konieczne.

* **Router klastra (opcjonalnie)** -Określ domyślny adres bramy używanej przez sieć klastra. 

  System automatycznie sugeruje ten sam adres bramy, które dostarczone dla sieci zarządzania.

* **Klaster tagu sieci VLAN (opcjonalnie)** — Jeśli klaster używa znaczników sieci VLAN, określ znacznik, sieci klastra.

* **(Opcjonalnie) MTU non-mgmt** — w razie potrzeby dostosuj maksymalna jednostka transmisji (MTU) dla sieci klastra.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurowanie klastra, DNS i NTP 

Poniżej **klastra** sekcji są polami określenie serwerów DNS i NTP i włączanie Agregacja łączy. Te ustawienia mają zastosowanie do wszystkich sieci, używanych przez klaster.

![Szczegółowe informacje na temat sekcji konfiguracji DNS/NTP z trzech pól dla serwerów DNS, pola dla domeny DNS i wyszukiwania DNS, trzy pola dla serwerów NTP i listy rozwijanej dla opcji agregacji łącza](media/fxt-cluster-create/dns-ntp-filled.png)

* **Serwery DNS** — wprowadź adres IP jednego lub więcej domen (DNS) serwery nazw system.

  DNS jest zalecana dla wszystkich klastrów i wymagane, jeśli chcesz użyć protokołu SMB, AD, lub Kerberos. 
  
  Aby uzyskać optymalną wydajność, należy skonfigurować serwer DNS klastra równoważenia obciążenia działania okrężnego, zgodnie z opisem w [Konfigurowanie serwera DNS dla klastra filtr Edge FXT Azure](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domena DNS** — wprowadź nazwę domeny sieciowe będą używane przez klaster.

* **Wyszukiwanie DNS** — Opcjonalnie wprowadź nazwy domen dodatkowe, które system powinien Wyszukaj, aby rozpoznać zapytania DNS. Możesz dodać maksymalnie sześć nazw domen, rozdzielone spacjami.

* **Serwery NTP** — Określ co najmniej trzy serwery protokołu (NTP) czasu sieci w odpowiednich polach. Można użyć nazwy hostów lub adresów IP.

* **Agregacja łącz** -Agregacja łączy pozwala dostosować sposób porty ethernet w węzłach klastra FXT obsługi różnych typów ruchu. Aby dowiedzieć się więcej, przeczytaj [Agregacja łączy](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) w przewodniku po konfiguracji klastra.

### <a name="click-the-create-button"></a>Kliknij przycisk Utwórz

Po wprowadzeniu wszystkich wymaganych ustawień w formularzu, kliknij przycisk **tworzenia klastrów** przycisku.

![dolnej części wypełnionego formularza za pomocą kursor na przycisku Utwórz w prawym dolnym rogu](media/fxt-cluster-create/create-cluster.png)

System wyświetli komunikat podczas tworzenia klastra.

![Komunikat stanu konfiguracji klastra w przeglądarce: "Węzeł FXT jest teraz tworzenia klastra. To może potrwać kilka minut. Po utworzeniu klastra można znaleźć ten link, aby zakończyć konfigurację." za pomocą hiperlinków na "można znaleźć ten link"](media/fxt-cluster-create/creating-message.png)

Po kilku chwilach możesz kliknąć link w wiadomości, aby przejść do klastra Panelu sterowania. (Ten link umożliwia przejście do adresu IP, który określiłeś w **IP zarządzania**.) Zajmuje 15 sekund na jedną minutę łącza staje się aktywny po kliknięciu przycisku Utwórz. Jeśli interfejs sieci web nie można załadować Poczekaj kilka sekund, a następnie ponownie kliknij link. 

Utworzenie klastra trwa minutę lub więcej, ale można zalogować się do panelu sterowania podczas procesu się dzieje. Jest to normalny strony pulpitu nawigacyjnego Panelu sterowania wyświetlić ostrzeżenia, dopóki nie zakończy się proces tworzenia klastra. 

## <a name="open-the-settings-pages"></a>Otwórz ustawienia strony 

Po utworzeniu klastra, należy dostosować konfigurację sieci i przepływ pracy. 

Konfigurowanie nowego klastra za pomocą interfejsu sieci web Panelu sterowania. Kliknij link z ekranu stan tworzenia klastra lub przejdź do adresu IP zarządzania, który został ustawiony w klastrze.

Zaloguj się do interfejsu sieci web przy użyciu nazwy użytkownika `admin` i hasło, które są ustawiane podczas tworzenia klastra.

![przeglądarki sieci Web, wyświetlane pola logowania w Panelu sterowania](media/fxt-cluster-config/admin-login.png)

Panel sterowania otwiera się i pokazuje **pulpit nawigacyjny** strony. Po zakończeniu tworzenia klastra, wyświetlając wyczyścić wszystkie komunikaty ostrzegawcze.

Kliknij przycisk **ustawienia** kartę, aby skonfigurować klaster.

Na **ustawienia** karcie lewym pasku bocznym pokazuje menu strony konfiguracji. Strony są zorganizowane według kategorii. Kliknij pozycję + lub - u góry nazwę kategorii, aby rozwinąć lub ukryć poszczególne strony.

![Karta Ustawienia Panelu sterowania (w przeglądarce) z klastrem > strony główne ustawienia załadowany](media/fxt-cluster-config/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Kroki instalacji klastra

Na tym etapie procesu, klaster istnieje, ale ma tylko jeden węzeł, Brak adresów IP ukierunkowane na klienta i Brak magazynu zaplecza. Dodatkowe ustawienia są potrzebne, można przejść od nowo utworzonego klastra do systemu pamięci podręcznej, który jest gotowy do obsługi przepływu pracy.

### <a name="required-configuration"></a>Wymaganej konfiguracji

Te kroki są wymagane w przypadku większości lub wszystkich klastrów. 

* Dodaj węzły do klastra 

  Trzy węzły jest standardowa, ale wiele klastrów produkcyjnych mają więcej możliwości — maksymalnie 24 węzłów.

  Odczyt [Dodawanie węzłów klastra](fxt-add-nodes.md) dowiesz się, jak dodać inne jednostki filtr Edge FXT platformy Azure do klastra, a także aby włączyć wysoką dostępność.

* Określić wielkość magazynu zaplecza

  Dodaj *podstawowe filtr* definicji dla każdego systemu magazynu zaplecza, które będzie używane przez klaster. Odczyt [Dodawanie magazynu zaplecza i konfigurowanie wirtualnej przestrzeni nazw](fxt-add-storage.md#about-back-end-storage) Aby dowiedzieć się więcej.

* Konfigurowanie dostępu klienta i wirtualnej przestrzeni nazw 

  Utwórz co najmniej jednym serwerem wirtualnym (vserver) i przypisz jej zakres adresów IP dla komputerów klienckich do użycia. Należy również skonfigurować przestrzeń nazw klastrów (nazywane czasem Namespace globalne lub GNS), funkcja wirtualnego systemu plików, która umożliwia mapowanie eksporty magazynu zaplecza do ścieżek wirtualnych. Przestrzeń nazw klastra zapewnia klientów struktury systemu plików spójne i jest dostępny nawet wtedy, gdy przełączasz się nośników magazynowania zaplecza. Przestrzeń nazw również podać hierarchii przyjazny dla użytkownika magazynu wirtualnego dla kontenerów obiektów Blob platformy Azure lub innych magazyn obiektów w chmurze obsługiwana.

  Odczyt [Konfigurowanie przestrzeni nazw](fxt-add-storage.md#configure-the-namespace) Aby uzyskać szczegółowe informacje. Ten krok obejmuje:
  * Tworzenie vservers
  * Konfigurowanie żeby połączenia między magazynu widoku i zaplecze oparte na sieci klienta 
  * Definiowanie których adres IP klienta adresy są obsługiwane przez każdego vserver

  > [!Note] 
  > Przed rozpoczęciem konfigurowania GNS klastra zaleca się planowanie znaczące. Odczyt [przy użyciu globalne Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) i [tworzenie i Praca z VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) sekcje w przewodniku po konfiguracji klastra w celu uzyskania pomocy.

* [Dostosuj ustawienia sieci](fxt-configure-network.md)

  Istnieje kilka ustawień związanych z siecią, które powinny być zweryfikowane lub dostosowany do nowego klastra. Odczyt [dostosować ustawienia sieciowe](fxt-configure-network.md) szczegółowe informacje na temat tych elementów:

  * Trwa weryfikowanie konfiguracji DNS i NTP 
  * Konfigurowanie usług katalogowych, jeśli to konieczne 
  * Konfigurowanie sieci VLAN
  * Konfigurowanie serwerów proxy
  * Dodawanie adresów IP do sieci klastra
  * Przechowywanie certyfikatów szyfrowania

* [Konfigurowanie monitorowania pomocy technicznej](#enable-support)

  Musisz zaakceptować zasady zachowania poufności informacji o narzędziu konfiguracji, a następnie należy skonfigurować ustawienia przekazywania pomocy technicznej, w tym samym czasie.

  Klaster automatycznie przekazać dane dotyczące rozwiązywania problemów dotyczące klastra, w tym statystyk i debugowania plików. Przekazywanie tych Poinformuj dział obsługi klienta firmy Microsoft i pomocy technicznej zapewniają najlepsze możliwe usługi. Można dostosować, co jest monitorowana, a opcjonalnie możesz włączyć aktywne pomocy technicznej i rozwiązywania problemów zdalny.  

### <a name="optional-configuration"></a>Konfiguracja opcjonalna

Te kroki nie są wymagane dla wszystkich klastrów. Są one potrzebne dla niektórych rodzajów przepływy pracy, lub dla niektórych style zarządzania klastrem. 

* Dostosuj ustawienia węzła

  Można ustawić nazwy węzła i skonfiguruj porty IPMI węzła na całego klastra, poziomu lub osobno. Jeśli skonfigurujesz te ustawienia przed dodaniem węzłów w klastrze, nowe węzły mogą wybierać ustawienia automatycznie w celu dołączenia. Opcje te są opisane w dokumencie tworzenia klastra w starszej wersji [Dostosowywanie ustawienia węzła](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Dokumentacji dla tego produktu nie jest jeszcze dostępna w witrynie dokumentacji firmy Microsoft Azure. Linki do [przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) i starszych wersji [Przewodnik tworzenia klastra](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) spowoduje przejście do oddzielnych witryny internetowej hostowanej w usłudze GitHub. 

* Skonfiguruj usługę SMB

  Jeśli chcesz zezwolić na dostęp protokołu SMB do Twojego klastra, a także systemu plików NFS, należy skonfigurować protokół SMB i włącz ją. SMB (czasami nazywany CIFS) zwykle jest używana do obsługi klientów firmy Microsoft Windows.

  Planowanie i konfigurowanie protokołu SMB obejmuje więcej niż przycisków kilka w Panelu sterowania. W zależności od wymagań systemu SMB może mieć wpływ na sposób definiowania core filtrach, ile vservers tworzenia, jak skonfigurować usługi punktach transferu i przestrzeni nazw, uprawnienia dostępu i inne ustawienia.

  Aby uzyskać więcej informacji, zapoznaj się z przewodnikiem konfiguracji klastra [Konfigurowanie dostęp przez protokół SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) sekcji.

* Zainstaluj dodatkowe licencje

  Jeśli chcesz wykorzystywać magazyn w chmurze niż usługi Azure Blob, należy zainstalować licencję dodatkowych funkcji. Skontaktuj się z przedstawicielem handlowym firmy Microsoft, aby uzyskać szczegółowe informacje dotyczące zakupu FlashCloud<sup>TM</sup> licencji. Szczegółowe informacje są wyjaśnione w [Dodawanie magazynu zaplecza i konfigurowanie wirtualnej przestrzeni nazw](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Włącz obsługę

Klaster filtr Edge FXT Azure automatycznie przekazać dane o pomocy technicznej dotyczące klastra. Przekazywanie tych Poinformuj personel zapewniają najlepsze możliwe działem.

Wykonaj następujące kroki, aby skonfigurować przekazywanie pomocy technicznej.

1. Przejdź do **klastra** > **pomocy technicznej** strony ustawień. Zaakceptuj zasady ochrony prywatności. 

   ![Zrzut ekranu pokazujący Panel sterowania i wyskakujące okienko ze przycisk Potwierdź, aby zaakceptować zasady zachowania poufności informacji](media/fxt-cluster-config/fxt-privacy-policy.png)

1. Kliknij przycisk trójkąta z lewej strony **informacje o kliencie** aby rozwinąć sekcję.
1. Kliknij przycisk **Revalidate przekazywania informacji** przycisku.
1. Ustaw nazwa_klastra pomocy technicznej w **unikatową nazwę klastra** — upewnij się, unikatowo identyfikuje klaster do personelu obsługi.
1. Zaznacz pola wyboru dla **monitorowania statystyk**, **przekazać ogólne informacje o**, i **przekazywanie informacji o awariach**.
1. Kliknij przycisk **Submit** (Prześlij).  

   ![Zrzut ekranu zawierającego klienta ukończone sekcji informacji o pomocy technicznej, strony ustawień](media/fxt-cluster-config/fxt-support-info.png)

1. Kliknij przycisk trójkąta z lewej strony **Secure aktywne pomocy technicznej (SPS)** aby rozwinąć sekcję.
1. Pole wyboru dla **Włącz łącze dodatki Service Pack**.
1. Kliknij przycisk **Submit** (Prześlij).

   ![Zrzut ekranu, zawierający ukończone sekcję Secure aktywne pomocy technicznej na stronie Ustawienia pomocy technicznej](media/fxt-cluster-config/fxt-support-sps.png)

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu klastra podstawowego i zaakceptować zasady zachowania poufności informacji, Dodaj pozostałe węzły klastra. 

> [!div class="nextstepaction"]
> [Dodawanie węzłów klastra](fxt-add-nodes.md)
