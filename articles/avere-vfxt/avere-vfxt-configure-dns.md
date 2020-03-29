---
title: Avere vFXT DNS — platforma Azure
description: Konfigurowanie serwera DNS do równoważenia obciążenia okrężnego za pomocą avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153789"
---
# <a name="avere-cluster-dns-configuration"></a>Konfiguracja DNS klastra Avere

W tej sekcji opisano podstawy konfigurowania systemu DNS do równoważenia obciążenia klastra VFXT Avere.

Ten dokument *nie zawiera* instrukcji konfigurowania serwera DNS i zarządzania nim w środowisku platformy Azure.

Zamiast używać okrężnego dns do równoważenia obciążenia klastra vFXT na platformie Azure, należy rozważyć użycie metod ręcznych, aby przypisać adresy IP równomiernie wśród klientów, gdy są one zainstalowane. Kilka metod są opisane w [Mount avere klastra](avere-vfxt-mount-clients.md).

Przy podejmowaniu decyzji o używaniu serwera DNS należy pamiętać o następujących kwestiach:

* Jeśli dostęp do systemu mają tylko klienci systemu plików NFS, użycie systemu DNS nie jest wymagane — możliwe jest określenie wszystkich adresów sieciowych przy użyciu numerycznych adresów IP.

* Jeśli system obsługuje dostęp SMB (CIFS), system DNS jest wymagany, ponieważ należy określić domenę DNS dla serwera usługi Active Directory.

* System DNS jest wymagany, jeśli chcesz używać uwierzytelniania Kerberos.

## <a name="load-balancing"></a>Równoważenie obciążenia

Aby rozpowszechniać ogólne obciążenie, skonfiguruj domenę DNS tak, aby używała dystrybucji obciążenia okrężnego dla adresów IP skierowanych do klienta.

## <a name="configuration-details"></a>Szczegóły konfiguracji

Gdy klienci uzyskują dostęp do klastra, RRDNS automatycznie równoważy ich żądania między wszystkimi dostępnymi interfejsami.

Aby uzyskać optymalną wydajność, skonfiguruj serwer DNS do obsługi adresów klastra skierowanych do klienta, jak pokazano na poniższym diagramie.

Serwer vserver klastra jest wyświetlany po lewej stronie, a adresy IP są wyświetlane w środku i po prawej stronie. Skonfiguruj każdy punkt dostępu klienta za pomocą rekordów I wskaźników A, jak pokazano na ilustracji.

![Diagram DNS okrężny klastra Avere](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

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

## <a name="cluster-dns-settings"></a>Ustawienia DNS klastra

Określ serwer DNS używany przez klaster vFXT na stronie Ustawienia**sieci administracyjnej** **klastra.** >  Ustawienia na tej stronie obejmują:

* Adres serwera DNS
* Nazwa domeny DNS
* Domeny wyszukiwania DNS

Przeczytaj [artykuł Ustawienia DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) w Przewodniku konfiguracji klastra Avere, aby uzyskać więcej informacji na temat korzystania z tej strony.
