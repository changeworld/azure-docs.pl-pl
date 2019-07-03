---
title: Dostosuj ustawienia sieci dla klastra systemu Microsoft Azure FXT krawędzi filtr
description: Jak dostosować ustawienia sieciowe po utworzeniu klastra filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 36ed354304cb1c88e48088f4b36c1ad0350af0dc
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543025"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Samouczek: Konfigurowanie ustawień sieciowych klastra 

Przed skorzystaniem z nowo utworzonego klastra filtr Edge FXT platformy Azure, należy sprawdzić i dostosować kilka ustawień sieci dla przepływu pracy. 

W tym samouczku opisano ustawienia sieci, które mogą wymagać, aby dostosować ją do nowego klastra. 

Dowiesz się: 

> [!div class="checklist"]
> * Ustawienia sieci, które może być konieczna aktualizacja po utworzeniu klastra
> * Które przypadki użycia filtr Edge FXT Azure wymagają serwera AD lub serwer DNS 
> * Jak skonfigurować działanie okrężne DNS (RRDNS), aby automatycznie równoważyć obciążenie klienta żądaniami klastrowi FXT

Ilość czasu potrzebnego do wykonania tych kroków, zależy od tego, jak wiele zmian w konfiguracji są wymagane w systemie:

* Jeśli wymagane jest tylko do odczytu za pomocą tego samouczka i sprawdź ustawienia kilka, powinno zająć 10 do 15 minut. 
* Jeśli musisz skonfigurować usługę DNS okrężnego tego zadania może potrwać godzinę lub dłużej.

## <a name="adjust-network-settings"></a>Dostosuj ustawienia sieci

Kilka zadań związanych z siecią są częścią konfigurowania nowego klastra filtr Edge FXT platformy Azure. Ta lista kontrolna i zdecydować, które dotyczą systemu.

Aby dowiedzieć się więcej na temat ustawień sieci dla klastra, przeczytaj [skonfigurowanie usług sieciowych](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) w przewodniku po konfiguracji klastra.

* Konfigurowanie działania okrężnego systemu DNS w sieci ukierunkowane na klienta (opcjonalnie)

  Równoważenie obciążenia klastra ruchem poprzez skonfigurowanie systemu DNS, zgodnie z opisem w [Konfigurowanie serwera DNS dla klastra filtr Edge FXT](#configure-dns-for-load-balancing).

* Sprawdź ustawienia NTP

* Konfigurowanie usługi Active Directory i nazwa nazwy użytkownika/grupy plików do pobrania (jeśli jest to konieczne)

  Jeśli hosty w sieci za pomocą usługi Active Directory lub innego rodzaju zewnętrznej usługi katalogowej, należy zmodyfikować konfigurację usługi katalogu klastra, aby skonfigurować sposób klastra pobiera informacje o nazwę użytkownika i grupy. Odczyt **klastra** > **usługa katalogowa** w przewodniku po konfiguracji klastra, aby uzyskać szczegółowe informacje.

  Jeśli chcesz, aby obsługa protokołu SMB, wymagany jest serwer usługi AD. Skonfiguruj AD przed rozpoczęciem konfigurowania protokołu SMB.

* Definiowanie sieci VLAN (opcjonalnie)
  
  Skonfiguruj wszystkie dodatkowe sieci VLAN, przed przystąpieniem do definiowania vservers klastra i globalnej przestrzeni nazw. Odczyt [pracy z sieciami VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) w przewodniku po konfiguracji klastra, aby dowiedzieć się więcej.

* Skonfiguruj serwery proxy (jeśli jest to konieczne)

  Jeśli klaster używa serwera proxy do osiągnięcia zewnętrzne adresy, wykonaj następujące kroki, aby je skonfigurować:

  1. Zdefiniuj serwer proxy w **konfigurację serwera Proxy** strona Ustawienia
  1. Zastosuj konfigurację serwera proxy z **klastra** > **ustawienia głównej** strony lub **szczegóły filtr Core** strony.
  
  Aby uzyskać więcej informacji, przeczytaj [przy użyciu internetowego serwera proxy](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) w przewodniku po konfiguracji klastra.

* Przekaż [certyfikaty szyfrowania](#encryption-certificates) klastra do użycia (opcjonalnie)

### <a name="encryption-certificates"></a>Certyfikaty szyfrowania

Filtr Edge FXT klastra przy użyciu certyfikatów X.509 dla tych funkcji:

* Do szyfrowania ruchu administracyjnej klastra

* Aby wykonać uwierzytelnienie w imieniu klienta na serwerach KMIP innych firm

* W celu sprawdzenia certyfikaty serwera dostawców chmury

Jeśli musisz przekazać certyfikatów do klastra, użyj **klastra** > **certyfikaty** strony ustawień. Szczegółowe informacje na temat [klastra > Certyfikaty](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) strona Podręcznika konfiguracji klastra.

Do szyfrowania komunikacji zarządzania klastrem, użyj **klastra** > **ustawienia głównej** ustawienia strony, aby wybrać certyfikat protokołu SSL administracyjne.

> [!Note] 
> Klucze dostępu usługi w chmurze są przechowywane przy użyciu **poświadczenia chmury** strona konfiguracji. [Dodaj filtr core](fxt-add-storage.md#add-a-core-filer) sekcji powyżej przedstawiono przykład; zapoznaj się z przewodnikiem konfiguracji klastra [poświadczenia chmury](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) sekcji, aby uzyskać szczegółowe informacje. 

## <a name="configure-dns-for-load-balancing"></a>Konfigurowanie systemu DNS dla równoważenia obciążenia

W tej sekcji opisano podstawowe informacje dotyczące konfigurowania działania okrężnego systemu DNS (RRDNS), do dystrybucji obciążenia klienta między wszystkie adresy IP ukierunkowane na klienta w klastrze filtr Edge FXT. 

### <a name="decide-whether-or-not-to-use-dns"></a>Zdecydować, czy do korzystania z usługi DNS

Równoważenie obciążenia sieciowego jest zawsze zalecane, ale nie musisz zawsze używaj DNS. Na przykład w niektórych typach przepływy pracy klienta sensowne więcej można przypisywać adresy IP klastra równomiernie wśród klientów podczas ich instalowania klastra za pomocą skryptu. Niektóre metody są opisane w [instalacji klastra](fxt-mount-clients.md). 

Zachowaj te rzeczy, należy pamiętać, przy podejmowaniu decyzji, czy należy użyć serwera DNS: 

* Jeśli system jest uzyskiwać dostęp tylko klientów systemu plików NFS, serwer DNS nie jest wymagane. Istnieje możliwość określić wszystkie adresy sieciowe przy użyciu numeryczne adresy IP. 

* Jeśli system obsługuje dostęp protokołu SMB (CIFS), DNS jest wymagane, ponieważ należy określić domenę DNS dla serwera usługi Active Directory.

* Serwer DNS jest wymagany, jeśli chcesz korzystać z uwierzytelniania Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Szczegóły konfiguracji DNS okrężnego

Gdy klienci uzyskują dostęp do klastra, RRDNS automatycznie równoważy ich żądania między wszystkie dostępne interfejsy.

Aby uzyskać optymalną wydajność należy skonfigurować serwer DNS do obsługi adresów klastra ukierunkowane na klienta, jak pokazano na poniższym diagramie.

Vserver klastra jest wyświetlany po lewej stronie, a adresy IP są wyświetlane w Centrum i po prawej stronie. Skonfiguruj każdy punkt dostępu klienta, a rekordy i wskaźniki, jak pokazano.

![Diagram DNS okrężnego klastra — następujące łącze szczegółowe tekst alternatywny obrazu](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[szczegółowy opis tekstowy](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Każdy klient dostępnego adresu IP musi mieć unikatową nazwę do użytku wewnętrznego przez klaster. (W tym diagramie adresów IP klienta są nazywane vs1 — klient - IP-* w celu uściślenia, ale w środowisku produkcyjnym należy prawdopodobnie używać coś bardziej zwięzły, takich jak klient *.)

Klienci instalacji klastra przy użyciu nazwy vserver jako argument serwera. 

Modyfikowanie serwera DNS ``named.conf`` plik, aby ustawić kolejność cyklicznych zapytań do usługi vserver. Ta opcja zapewnia wszystkie dostępne wartości to cyklom za pośrednictwem. Dodaj instrukcję, jak pokazano poniżej:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Następujące ``nsupdate`` polecenia podać przykład poprawnie konfigurowania systemu DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>Włączanie systemu DNS w klastrze 

Określenie serwera DNS używanego przez klaster w **klastra** > **administracyjnych sieci** strony ustawień. Ustawienia na tej stronie, obejmują:

* Adres serwera DNS
* Nazwa domeny DNS
* Domeny wyszukiwania DNS

Aby uzyskać więcej informacji, przeczytaj [ustawienia DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) w przewodniku po konfiguracji klastra.

## <a name="next-steps"></a>Kolejne kroki

Jest to ostatni krok konfiguracji podstawowej dla klastra filtr Edge FXT platformy Azure. 

* Dowiedz się więcej o diod LED systemu i inne wskaźniki w [monitorować stan sprzętu](fxt-monitor.md).
* Dowiedz się więcej na temat sposobu klientów należy zainstalować filtr Edge FXT klastra w systemie [instalacji klastra](fxt-mount-clients.md). 
* Aby uzyskać więcej informacji na temat świadczenia i zarządzania klastrem filtr Edge FXT zobacz [przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 