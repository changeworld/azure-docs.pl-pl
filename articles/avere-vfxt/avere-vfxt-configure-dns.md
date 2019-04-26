---
title: Avere vFXT DNS - Azure
description: Konfigurowanie serwera DNS dla działanie okrężne Równoważenie obciążenia za pomocą Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 9fd9eaf1e62d063026e0e656346baaaade87064f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410337"
---
# <a name="avere-cluster-dns-configuration"></a>Konfiguracja DNS klastra Avere

W tej sekcji opisano podstawowe informacje dotyczące konfigurowania systemu DNS dla swojej Avere vFXT klastra równoważenia obciążenia. 

W tym dokumencie *nie obejmuje* instrukcje dotyczące konfigurowania i zarządzania serwerem DNS w środowisku platformy Azure. 

Zamiast przy użyciu działania okrężnego systemu DNS, równoważenia obciążenia w klastrze vFXT na platformie Azure, należy wziąć pod uwagę przy użyciu metod ręcznych do przypisywania adresów IP równomiernie między klientami w przypadku, gdy są zainstalowane. Opisano kilka metod w [instalacji klastra Avere](avere-vfxt-mount-clients.md). 

Zachowaj te rzeczy, należy pamiętać, przy podejmowaniu decyzji, czy należy użyć serwera DNS: 

* Jeśli system jest uzyskiwać dostęp tylko klientów systemu plików NFS, przy użyciu systemu DNS nie jest wymagane — istnieje możliwość określić wszystkie adresy sieciowe przy użyciu numeryczne adresy IP. 

* Jeśli system obsługuje dostęp protokołu SMB (CIFS), DNS jest wymagane, ponieważ należy określić domenę DNS dla serwera usługi Active Directory.

* Serwer DNS jest wymagany, jeśli chcesz korzystać z uwierzytelniania Kerberos.

## <a name="load-balancing"></a>Równoważenie obciążenia

Rozłożenie obciążenia ogólną, należy skonfigurować swoją domenę DNS na potrzeby dystrybucji obciążenia z działaniem okrężnym adresów IP ukierunkowane na klienta.

## <a name="configuration-details"></a>Szczegóły konfiguracji

Gdy klienci uzyskują dostęp do klastra, RRDNS automatycznie równoważy ich żądania między wszystkie dostępne interfejsy.

Aby uzyskać optymalną wydajność należy skonfigurować serwer DNS do obsługi adresów klastra ukierunkowane na klienta, jak pokazano na poniższym diagramie.

Vserver klastra jest wyświetlany po lewej stronie, a adresy IP są wyświetlane w Centrum i po prawej stronie. Skonfiguruj każdy punkt dostępu klienta, a rekordy i wskaźniki, jak pokazano.

![Diagram DNS okrężnego klastra Avere](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

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

Następujące polecenia nsupdate podać przykład poprawnie konfigurowania systemu DNS:

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

Określ serwer DNS, który korzysta z klastra vFXT w **klastra** > **administracyjnych sieci** strony ustawień. Ustawienia na tej stronie, obejmują:

* Adres serwera DNS
* Nazwa domeny DNS
* Domeny wyszukiwania DNS

Odczyt [ustawienia DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) w przewodniku po konfiguracji klastra Avere Aby uzyskać więcej informacji o korzystaniu z tej strony.


