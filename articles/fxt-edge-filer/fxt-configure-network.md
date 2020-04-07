---
title: 'Samouczek: Konfigurowanie sieci w klastrze plików usługi Azure FXT Edge'
description: Jak dostosować ustawienia sieciowe po utworzeniu klastra plików usługi Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 9b0154889544e0054e309cc5f43851b73b4396b4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754696"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Samouczek: Konfigurowanie ustawień sieciowych klastra

Przed użyciem nowo utworzonego klastra plików usługi Azure FXT Edge należy sprawdzić i dostosować kilka ustawień sieciowych dla przepływu pracy. 

W tym samouczku wyjaśniono ustawienia sieciowe, które mogą być konieczne do dostosowania do nowego klastra. 

Dowiesz się: 

> [!div class="checklist"]
> * Jakie ustawienia sieciowe mogą wymagać aktualizacji po utworzeniu klastra
> * Które przypadki użycia usługi Azure FXT Edge wymagają serwera usługi AD lub serwera DNS 
> * Jak skonfigurować system DNS (ROUND-robin DNS) do automatycznego równoważenia żądań klientów do klastra FXT

Czas potrzebny na wykonanie tych kroków zależy od liczby potrzebnych zmian konfiguracji w systemie:

* Jeśli wystarczy przeczytać samouczek i sprawdzić kilka ustawień, powinno to zająć od 10 do 15 minut. 
* Jeśli chcesz skonfigurować dns okrężne, to zadanie może potrwać godzinę lub dłużej.

## <a name="adjust-network-settings"></a>Dostosowywanie ustawień sieciowych

Kilka zadań związanych z siecią są częścią konfigurowania nowego klastra plików usługi Azure FXT Edge Filer. Sprawdź tę listę i zdecyduj, które z nich mają zastosowanie do Twojego systemu.

Aby dowiedzieć się więcej o ustawieniach sieci dla klastra, zobacz [Konfigurowanie usług sieciowych](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) w Przewodniku konfiguracji klastra.

* Konfigurowanie systemu DNS okrężnego dla sieci skierowanej do klienta (opcjonalnie)

  Ruch klastra równoważenia obciążenia przez skonfigurowanie systemu DNS zgodnie z opisem w [temacie Konfigurowanie systemu DNS dla klastra fxt edge filer](#configure-dns-for-load-balancing).

* Weryfikowanie ustawień NTP

* Konfigurowanie pobierania usługi Active Directory i nazwy użytkownika/grupy (w razie potrzeby)

  Jeśli hosty sieciowe korzystają z usługi Active Directory lub innego rodzaju zewnętrznej usługi katalogowej, należy zmodyfikować konfigurację usług katalogowych klastra, aby skonfigurować sposób pobierania przez klaster informacji o nazwie użytkownika i grupie. Szczegółowe informacje można znaleźć w przewodniku konfiguracji klastra w**usługach katalogowych** **klastrów.** > 

  Serwer usługi AD jest wymagany, jeśli chcesz obsługiwać SMB. Skonfiguruj ad przed rozpoczęciem konfigurowania SMB.

* Definiowanie sieci VLAN (opcjonalnie)
  
  Skonfiguruj dodatkowe sieci VLAN przed zdefiniowaniem serwerów vservers i globalnej przestrzeni nazw klastra. Przeczytaj [artykuł Praca z sieciami VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) w Przewodniku konfiguracji klastra, aby dowiedzieć się więcej.

* Konfigurowanie serwerów proxy (w razie potrzeby)

  Jeśli klaster używa serwera proxy do docierania do adresów zewnętrznych, wykonaj następujące kroki, aby go skonfigurować:

  1. Definiowanie serwera proxy na stronie Ustawienia **konfiguracji serwera proxy**
  1. Zastosuj konfigurację serwera proxy na stronie**Konfiguracja ogólna** **klastra** > lub na stronie **Podstawowe szczegóły filera.**
  
  Aby uzyskać więcej informacji, zobacz [Korzystanie z serwerów proxy sieci Web](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) w Przewodniku konfiguracji klastra.

* Przekazywanie [certyfikatów szyfrowania](#encryption-certificates) dla klastra do użycia (opcjonalnie)

### <a name="encryption-certificates"></a>Certyfikaty szyfrowania

Klaster FXT Edge Filer używa certyfikatów X.509 dla następujących funkcji:

* Aby zaszyfrować ruch administracyjny klastra

* Uwierzytelniać w imieniu klienta na serwerach KMIP innych firm

* Do weryfikacji certyfikatów serwerów dostawców usług w chmurze

Jeśli chcesz przekazać certyfikaty do klastra, użyj strony Ustawienia > **certyfikatów** **klastra.** Szczegóły znajdują się na stronie [Certyfikaty > klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) w Przewodniku konfiguracji klastra.

Aby zaszyfrować komunikację zarządzania klastrem, użyj strony Ustawienia ogólne **klastra,** > **General Setup** aby wybrać certyfikat, który ma być używany do administracyjnego protokołu TLS.

> [!Note] 
> Klucze dostępu do usługi w chmurze są przechowywane przy użyciu strony konfiguracji **poświadczenia chmury.** W powyższej sekcji [Dodaj podstawowy filer](fxt-add-storage.md#add-a-core-filer) przedstawiono przykład; przeczytaj sekcję [Poświadczeń chmury](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) przewodnik konfiguracji klastra, aby uzyskać szczegółowe informacje. 

## <a name="configure-dns-for-load-balancing"></a>Konfigurowanie systemu DNS do równoważenia obciążenia

W tej sekcji opisano podstawy konfigurowania systemu DNS (RRDNS) okrężnego systemu do dystrybucji obciążenia klienta między wszystkie adresy IP skierowane do klienta w klastrze FXT Edge Filer. 

### <a name="decide-whether-or-not-to-use-dns"></a>Zdecyduj, czy używać systemu DNS

Równoważenie obciążenia jest zawsze zalecane, ale nie trzeba zawsze używać dns. Na przykład w przypadku niektórych typów przepływów pracy klienta może być bardziej sensowne użycie skryptu do równomiernego przypisywania adresów IP klastra między klientami podczas instalowania klastra. Niektóre metody są opisane w [Mount klastra](fxt-mount-clients.md). 

Przy podejmowaniu decyzji o używaniu serwera DNS należy pamiętać o następujących kwestiach: 

* Jeśli dostęp do systemu mają tylko klienci systemu NFS, system DNS nie jest wymagany. Możliwe jest określenie wszystkich adresów sieciowych przy użyciu numerycznych adresów IP. 

* Jeśli system obsługuje dostęp SMB (CIFS), system DNS jest wymagany, ponieważ należy określić domenę DNS dla serwera usługi Active Directory.

* System DNS jest wymagany, jeśli chcesz używać uwierzytelniania Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Szczegóły konfiguracji dns okrężne

Gdy klienci uzyskują dostęp do klastra, RRDNS automatycznie równoważy ich żądania między wszystkimi dostępnymi interfejsami.

Aby uzyskać optymalną wydajność, skonfiguruj serwer DNS do obsługi adresów klastra skierowanych do klienta, jak pokazano na poniższym diagramie.

Serwer vserver klastra jest wyświetlany po lewej stronie, a adresy IP są wyświetlane w środku i po prawej stronie. Skonfiguruj każdy punkt dostępu klienta za pomocą rekordów I wskaźników A, jak pokazano na ilustracji.

![Diagram DNS okrężny klastra](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
— szczegółowy link tekstowy alternatywny następuje po[szczegółowym opisie tekstowym](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Każdy adres IP skierowany do klienta musi mieć unikatową nazwę do użytku wewnętrznego przez klaster. (Na tym diagramie adresy IP klienta są nazywane vs1-client-IP-* dla jasności, ale w produkcji prawdopodobnie należy użyć czegoś bardziej zwięzłego, takiego jak klient*.)

Klienci instalują klaster przy użyciu nazwy serwera vserver jako argumentu serwera. 

Zmodyfikuj ``named.conf`` plik serwera DNS, aby ustawić cykliczną kolejność zapytań do serwera vserver. Ta opcja gwarantuje, że wszystkie dostępne wartości są przełączane przez. Dodaj następującą instrukcję:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Następujące ``nsupdate`` polecenia zawierają przykład poprawnego konfigurowania systemu DNS:

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

### <a name="enable-dns-in-the-cluster"></a>Włączanie usługi DNS w klastrze 

Określ serwer DNS używany przez klaster na stronie Ustawienia**sieci administracyjnej** **klastra.** >  Ustawienia na tej stronie obejmują:

* Adres serwera DNS
* Nazwa domeny DNS
* Domeny wyszukiwania DNS

Aby uzyskać więcej informacji, przeczytaj artykuł [Ustawienia DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) w Przewodniku konfiguracji klastra.

## <a name="next-steps"></a>Następne kroki

Jest to ostatni podstawowy krok konfiguracji dla klastra plików usługi Azure FXT Edge. 

* Dowiedz się więcej o diodach LED systemu i innych wskaźnikach w [monitorze stanu sprzętu](fxt-monitor.md).
* Dowiedz się więcej o tym, jak klienci powinni montować klaster FXT Edge Filer w [instalacji klastra](fxt-mount-clients.md). 
* Aby uzyskać więcej informacji na temat obsługi klastra FXT Edge Filer i zarządzania nim, zobacz [Przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 