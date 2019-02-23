---
title: DNS w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Przy użyciu systemu DNS w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 3a4efb6282a0aca8173403009fd58e9154a91b2b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728954"
---
# <a name="using-dns-in-azure-stack"></a>Przy użyciu systemu DNS w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack obsługuje następujące funkcje systemu nazw domen (DNS, Domain Name System):

* Rozpoznawanie nazw hostów DNS
* Tworzenie i Zarządzanie strefami DNS i rekordów przy użyciu interfejsu API

## <a name="support-for-dns-hostname-resolution"></a>Obsługa rozpoznawanie nazwy hosta DNS

Można określić etykietę nazwy domeny DNS dla publicznych adresów IP zasobów. Używa usługi Azure Stack **domainnamelabel.location.cloudapp.azurestack.external** nazwę etykiety i mapy do publicznego adresu IP w usłudze Azure Stack zarządzane serwery DNS.

Na przykład, jeśli utworzysz publiczny zasób adresu IP za pomocą **contoso** jako etykietę nazwy domeny w lokalizacji lokalnej usługi Azure Stack [w pełni kwalifikowaną nazwę domeny (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)  **contoso.local.cloudapp.azurestack.external** rozpoznawany jako publiczny adres IP zasobu. Aby utworzyć niestandardową domenę rekord CNAME, który wskazuje na publiczny adres IP w usłudze Azure Stack, można użyć tej nazwy FQDN.

Aby dowiedzieć się więcej na temat rozpoznawania nazw, zobacz [rozpoznawania nazw DNS](../../dns/dns-for-azure-services.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) artykułu.

> [!IMPORTANT]
> Każda etykieta nazwy domeny, tworzonych muszą być unikatowe w lokalizacji usługi Azure Stack.

Poniższy zrzut ekranu przedstawia **tworzenie publicznego adresu IP** okno dialogowe tworzenia publicznego adresu IP za pomocą portalu:

![Tworzenie publicznego adresu IP](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Przykładowy scenariusz

Masz modułu równoważenia obciążenia przetwarzaniem żądań z aplikacji sieci web. Za obciążenia równoważenia jest witryny sieci web uruchomionej na co najmniej jednej maszyny wirtualnej. Możesz uzyskać dostęp do witryny sieci web z równoważeniem obciążenia przy użyciu nazwy DNS zamiast adresu IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Tworzenie i Zarządzanie strefami DNS i rekordów przy użyciu interfejsu API

Można tworzyć i zarządzać strefami DNS i rekordów w usłudze Azure Stack.

Usługa Azure Stack zapewnia podobne do platformy Azure, usługi DNS przy użyciu interfejsów API, które są zgodne z interfejsów API usługi Azure DNS.  Hostowanie domen w usłudze Azure Stack DNS, pozwala na zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API i narzędzi. Można również użyć tego samego rozliczeń i pomocy technicznej co w innych usług platformy Azure.

Infrastruktura usługi system DNS Azure Stack jest mniejszych niż platformy Azure. Rozmiar i położenie wdrożenia usługi Azure Stack ma wpływ na zakres, skalowalność i wydajność DNS. Oznacza to, że wydajności, dostępności, globalną dystrybucję i wysoka dostępność może się różnić wdrożenia wdrożenia.

## <a name="comparison-with-azure-dns"></a>Porównanie z usługą Azure DNS

DNS w usłudze Azure Stack jest podobny do serwera DNS na platformie Azure, ale istnieje kilka wyjątków ważne:

* **Nie obsługuje rekordów AAAA**: Usługa Azure Stack nie obsługuje rekordów AAAA, ponieważ usługi Azure Stack nie obsługuje adresów IPv6. Jest to główna różnica między DNS na platformie Azure i usługi Azure Stack.

* **Nie jest wielodostępne**: Usługa DNS w usłudze Azure Stack nie jest wielodostępne. Poszczególni dzierżawcy nie można utworzyć tej samej strefie DNS. Tylko pierwszy subskrypcję, która podejmuje próbę utworzenia strefy zakończy się powodzeniem, a każde kolejne wywołanie się nie powieść. Jest to Najważniejszą różnicą między platformą Azure i usługi system DNS Azure Stack.

* **Znaczniki, metadane i elementów etag**: Występują niewielkie różnice w jak Azure Stack obsługuje tagi, metadane, elementów etag i limitów.

Aby dowiedzieć się więcej o usłudze Azure DNS, zobacz [strefy i rekordy DNS](../../dns/dns-zones-records.md).

### <a name="tags"></a>Tagi

Usługa system DNS Azure Stack obsługuje, za pomocą usługi Azure Resource Manager tagów zasobów strefy DNS. Nie obsługuje tagów w zestawach rekordów DNS, mimo że alternatywnym **metadanych** jest obsługiwana w zestawach rekordów DNS, zgodnie z opisem w następnej sekcji.

### <a name="metadata"></a>Metadane

Jako alternatywę do zestawu rekordów tagów obsługuje dodawanie adnotacji do zestawów rekordów przy użyciu usługi Azure Stack DNS *metadanych*. Podobnie jak tagi, metadanych umożliwia kojarzenie pary nazwa wartość z każdego zestawu rekordów. Na przykład może to być przydatne do rejestrowania przeznaczenie każdy zestaw rekordów. W przeciwieństwie do tagów metadanych nie można użyć, aby zapewnić widok filtrowany rachunku dotyczącym platformy Azure, a metadane nie można określić w zasadach usługi Azure Resource Manager.

### <a name="etags"></a>Elementy etag

Załóżmy, że dwie osoby lub dwóch procesów spróbuj zmodyfikować rekord DNS, w tym samym czasie. Który z nich wins? I będzie zwycięzca wiadomo, że zostały one zastąpione zmiany utworzone przez inną osobę?

Usługa system DNS Azure Stack używa *elementów etag* bezpiecznie obsłużyć równoczesnych zmian do tego samego zasobu. Elementy etag różnią się od usługi Azure Resource Manager *tagi*. Każdy zasób DNS (strefy lub zestawu rekordów) ma element Etag skojarzone z nią. Po pobraniu zasobu jego element Etag również są pobierane. Podczas aktualizowania zasobu, można przesłać element Etag dzięki usłudze Azure Stack DNS można sprawdzić, czy tag Etag na dopasowania serwera. Ponieważ każda aktualizacja do zasobu w wyniku element Etag, są ponownie generowane, element etag wskazuje, że nastąpiła zmiana współbieżnych. Elementy etag może również podczas tworzenia nowego zasobu upewnij się, że zasób nie istnieje.

Domyślnie polecenia cmdlet programu PowerShell do programu Azure Stack DNS za pomocą elementów etag blokować współbieżnych zmian do stref i zestawy rekordów. Możesz użyć opcjonalnego `-Overwrite` przełączyć się do pomijania sprawdzania elementu Etag, co powoduje, że równoczesnych zmian, które miały miejsce zostaną zastąpione.

Na poziomie interfejsu API REST usługi Azure Stack DNS elementów etag są określane przy użyciu nagłówków HTTP. Ich zachowanie jest opisane w poniższej tabeli:

| Nagłówek | Zachowanie|
|--------|---------|
| Brak   | Umieść zawsze powiedzie się (nie sprawdzeń element Etag)|
| If-match| Umieść powiedzie się tylko, jeśli zasób istnieje i element Etag jest zgodny|
| IF-match *| Umieść powiedzie się tylko, jeśli istnieje zasób|
| IF-none-match *| Umieść powiedzie się tylko, jeśli zasób nie istnieje.|

### <a name="limits"></a>Limity

Następujące limity domyślne są stosowane podczas korzystania z usługi Azure Stack DNS:

| Zasób| Limit domyślny|
|---------|--------------|
| Strefy na subskrypcję| 100|
| Zestawy rekordów dla strefy| 5000|
| Rekordów na zestawu rekordów| 20|

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do międzynarodowych nazw domen dla usługi Azure Stack](azure-stack-understanding-dns.md)
