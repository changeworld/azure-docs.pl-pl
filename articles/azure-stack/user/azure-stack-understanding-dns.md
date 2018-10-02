---
title: Opis międzynarodowych nazw domen w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Opis funkcji międzynarodowych nazw domen i możliwości w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-09/28/2018started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 783262a5b55bd645ae3b85c1f00434648d7ee35f
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584971"
---
# <a name="introducing-idns-for-azure-stack"></a>Wprowadzenie do międzynarodowych nazw domen dla usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

międzynarodowych nazw domen jest funkcją sieci usługi Azure Stack, która umożliwia rozpoznawanie nazw DNS zewnętrzne (na przykład http://www.bing.com.) umożliwia również zarejestrować nazwy wewnętrznej sieci wirtualnej. W ten sposób można naprawić maszyny wirtualne na tej samej sieci wirtualnej, nazwy, a nie adresu IP. To podejście usuwa potrzebę zapewnienia niestandardowe wpisami serwera DNS. Aby uzyskać więcej informacji na temat systemu DNS, zobacz [Omówienie usługi Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Do czego służy międzynarodowych nazw domen.

Bez konieczności określania niestandardowych wpisami serwera DNS, z międzynarodowych nazw domen w usłudze Azure Stack, pobierz następujące możliwości:

- Udostępnione usługi rozpoznawania nazw DNS dla obciążenia dzierżaw.
- Autorytatywną usługę DNS dla rozpoznawania nazw i rejestrację serwera DNS w sieci wirtualnej dzierżawcy.
- Usługa DNS do rekursywnego rozpoznawania nazw internetowych z maszynami wirtualnymi dzierżawy. Dzierżawy nie są już potrzebne określić niestandardowe wpisy DNS do rozpoznawania nazw Internet (na przykład www.bing.com.)

Nadal możesz przenieść własną usługą DNS i korzystania z niestandardowych serwerów DNS. Jednak za pomocą międzynarodowych nazw domen, można rozpoznać nazwy DNS w sieci Internet i nawiązać połączenie z maszynami wirtualnymi w tej samej sieci wirtualnej, nie trzeba tworzyć niestandardowe wpisy DNS.

## <a name="what-doesnt-idns-do"></a>Czego nie międzynarodowych nazw domen?

Jakie międzynarodowych nazw domen nie zezwala na wykonywanie, jest tworzenie rekordu DNS dla nazwy, które mogą być rozwiązane z spoza sieci wirtualnej.

Na platformie Azure masz możliwość wybrania etykiety nazwy DNS, który jest skojarzony z publicznym adresem IP. Można wybrać etykiety (prefiks), ale Azure wybiera sufiks, który zależy od regionu, w którym można utworzyć publiczny adres IP.

![Przykład etykiety nazwy DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Jak pokazano na poprzedniej ilustracji, platforma Azure będzie utworzyć rekord "A" w systemie DNS dla etykiety nazwy DNS w strefie **westus.cloudapp.azure.com**. Prefiks i sufiks są łączone w celu tworzenia [w pełni kwalifikowaną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), można rozwiązać z dowolnego miejsca w publicznym Internecie.

Usługa Azure Stack obsługuje tylko międzynarodowych nazw domen do rejestracji nazw wewnętrznych, więc nie można wykonać następujące czynności:

- Tworzenie rekordu DNS w istniejącej strefy DNS hostowanej (na przykład local.azurestack.external.)
- Tworzenie strefy DNS (np. Contoso.com).
- Utwórz rekord w ramach własnego niestandardowego strefy DNS.
- Obsługuje zakupu nazw domen.

## <a name="next-steps"></a>Kolejne kroki

[Przy użyciu systemu DNS w usłudze Azure Stack](azure-stack-dns.md)
