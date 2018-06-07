---
title: Opis międzynarodowych nazw domen w stosie Azure | Dokumentacja firmy Microsoft
description: Opis międzynarodowych nazw domen funkcje i możliwości w stosie Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 9123160f42adea57c28dff265bd5b5dbbcbb7918
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724262"
---
# <a name="introducing-idns-for-azure-stack"></a>Introducing Azure stosu międzynarodowych nazw domen.

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

międzynarodowych nazw domen jest funkcją sieci stosu Azure, która umożliwia rozpoznawania nazw DNS zewnętrznych (na przykład http://www.bing.com.) można też zarejestrować nazwy wewnętrznej sieci wirtualnej. W ten sposób można rozwiązać maszyn wirtualnych w tej samej sieci wirtualnej, nazwy, a nie adres IP. Ta metoda usuwa, należy określić niestandardowe wpisów serwera DNS. Aby uzyskać więcej informacji dotyczących usługi DNS, zobacz [Omówienie usługi Azure DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Do czego służy międzynarodowych nazw domen.

Z międzynarodowych nazw domen w stosie Azure bez konieczności określania niestandardowych wpisów serwera DNS można uzyskać następujące możliwości:

- Udostępnione usługi rozpoznawania nazw DNS dla obciążeń dzierżawców.
- Autorytatywny usługi DNS dla rozpoznawania nazw i rejestrację DNS w sieci wirtualnej dzierżawcy.
- Usługa DNS cykliczne do rozpoznawania nazw internetowych dzierżawy maszyn wirtualnych. Dzierżawcy nie są już potrzebne określić niestandardowe wpisy DNS do rozpoznawania nazw internetowych (na przykład www.bing.com.)

Nadal można przełączyć własne DNS i używać niestandardowych serwerów DNS. Jednak przy użyciu międzynarodowych nazw domen, można rozpoznawania nazw DNS w Internecie i nawiązać połączenia z innych maszyn wirtualnych w tej samej sieci wirtualnej, nie trzeba tworzyć niestandardowe wpisy DNS.

## <a name="what-doesnt-idns-do"></a>Czego nie międzynarodowych nazw domen?

Jakie międzynarodowych nazw domen nie pozwala celu jest tworzenie rekordu DNS dla nazwy, która może zostać rozwiązany z spoza sieci wirtualnej.

Na platformie Azure masz możliwość określenia etykieta nazwy DNS, który jest skojarzony z publicznym adresem IP. Możesz wybrać etykietę (prefiks), ale Azure wybiera sufiks, opartym na regionie, w którym można utworzyć publiczny adres IP.

![Przykład etykieta nazwy DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Jak pokazano na poprzedniej ilustracji, Azure utworzy rekord "A" w systemie DNS w przypadku etykieta nazwy DNS w strefie **westus.cloudapp.azure.com**. Prefiksu i sufiksu są łączone utworzenie [pełną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) który można rozwiązać ten problem z dowolnego miejsca w publicznej sieci Internet.

Stos Azure obsługuje tylko międzynarodowych nazw domen rejestracji nazwy wewnętrznej, więc nie można wykonać następujące czynności:

- Utwórz rekord DNS w istniejącej hostowanej strefy DNS (na przykład local.azurestack.external.)
- Tworzenie strefy DNS (np. Contoso.com).
- Utwórz rekord w obszarze własne niestandardowe strefy DNS.
- Obsługuje zakupów nazw domen.

## <a name="next-steps"></a>Kolejne kroki

[W stosie Azure przy użyciu systemu DNS](azure-stack-dns.md)
