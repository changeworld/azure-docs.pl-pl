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
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: acb8b262256031ae8615180e0f55c98cb56b538d
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054385"
---
# <a name="using-dns-in-azure-stack"></a>Przy użyciu systemu DNS w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack obsługuje następujące funkcje systemu nazw domen (DNS, Domain Name System):

* Rozpoznawanie nazw hostów DNS
* Tworzenie i Zarządzanie strefami DNS i rekordów przy użyciu interfejsu API

## <a name="support-for-dns-hostname-resolution"></a>Obsługa rozpoznawanie nazwy hosta DNS

Można określić etykietę nazwy domeny DNS dla publicznych adresów IP zasobów. Używa usługi Azure Stack *domainnamelabel.location*. cloudapp.azurestack.external nazwę etykiety i mapy do publicznego adresu IP adres w usłudze Azure Stack zarządzane serwery DNS.

Na przykład, jeśli utworzysz publiczny zasób adresu IP za pomocą **contoso** jako etykietę nazwy domeny w lokalizacji lokalnej usługi Azure Stack [w pełni kwalifikowaną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **contoso.local.cloudapp.azurestack.external** rozpoznawany jako publiczny adres IP zasobu. Aby utworzyć niestandardową domenę rekord CNAME, który wskazuje na publiczny adres IP w usłudze Azure Stack, można użyć tej nazwy FQDN.

Aby dowiedzieć się więcej na temat rozpoznawania nazw, zobacz [rozpoznawania nazw DNS](https://docs.microsoft.com/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) artykułu.

> [!IMPORTANT]
> Każda etykieta nazwy domeny, tworzonych muszą być unikatowe w lokalizacji usługi Azure Stack.

Następny ekran przechwytywania pokazuje **tworzenie publicznego adresu IP** okno dialogowe tworzenia publicznego adresu IP przy użyciu portalu.

![Tworzenie publicznego adresu IP](media/azure-stack-whats-new-dns/image01.png)

**Przykładowy scenariusz**

Masz modułu równoważenia obciążenia przetwarzaniem żądań z aplikacji sieci web. Za obciążenia równoważenia jest witryny sieci web uruchomionej na co najmniej jednej maszyny wirtualnej. Możesz uzyskać dostęp do witryny sieci web z równoważeniem obciążenia przy użyciu nazwy DNS zamiast adresu IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Tworzenie i Zarządzanie strefami DNS i rekordów przy użyciu interfejsu API

Można tworzyć i zarządzać strefami DNS i rekordów w usłudze Azure Stack.

Usługa Azure Stack zapewnia usługi DNS, takich jak Azure, za pomocą interfejsów API, które są zgodne z interfejsami API DNS platformy Azure.  Hostowanie domen w usłudze Azure Stack DNS, pozwala na zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API i narzędzi. Można również użyć tego samego rozliczeń i pomocy technicznej co w innych usług platformy Azure.

Infrastruktura usługi system DNS Azure Stack jest mniejszych niż platformy Azure. Rozmiar i położenie wdrożenia usługi Azure Stack wpłynie na zakres, skalowalność i wydajność DNS. Oznacza to, że wydajności, dostępności, globalnej dystrybucji i wysokiej dostępności może się różnić wdrożenia wdrożenia.

## <a name="comparison-with-azure-dns"></a>Porównanie z usługą Azure DNS

DNS w usłudze Azure Stack jest podobny do serwera DNS na platformie Azure, ale istnieją wyjątki głównych, które należy zrozumieć.

* **Nie obsługuje rekordów AAAA**

    Usługa Azure Stack nie obsługuje rekordów AAAA, ponieważ usługi Azure Stack nie obsługuje adresów IPv6.  Jest to główna różnica między DNS na platformie Azure i usługi Azure Stack.
* **Nie jest wielodostępnych**

    Usługa DNS w usłudze Azure Stack nie jest wielodostępne. Poszczególni dzierżawcy nie można utworzyć tej samej strefie DNS. Tylko pierwszy subskrypcję, która podejmuje próbę utworzenia strefy zakończy się powodzeniem, a każde kolejne wywołanie się nie powieść.  Jest to znany problem i Najważniejszą różnicą między platformą Azure i usługi system DNS Azure Stack. Ten problem zostanie rozwiązany w przyszłej wersji.
* **Znaczniki, metadane i elementów etag**

    Występują niewielkie różnice w jak Azure Stack obsługuje tagi, metadane, elementów etag i limitów.

Aby dowiedzieć się więcej o usłudze Azure DNS, zobacz [strefy i rekordy DNS](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>Znaczniki, metadane i elementów etag

**Tagi**

Usługa system DNS Azure Stack obsługuje, za pomocą usługi Azure Resource Manager tagów zasobów strefy DNS. Nie obsługuje ona tagów w zestawach rekordów DNS, mimo że jako alternatywę "metadata" jest obsługiwana na zestawów rekordów DNS, jak wyjaśniono dalej.

**Metadata**

Jako alternatywę do zestawu rekordów tagów usługi Azure Stack DNS obsługuje dodawanie adnotacji do zestawów rekordów przy użyciu "metadata". Podobnie jak tagi, metadanych umożliwia kojarzenie pary nazwa wartość z każdego zestawu rekordów. Na przykład może to być przydatne do rejestrowania przeznaczenie każdy zestaw rekordów. W przeciwieństwie do tagów metadanych nie może służyć do zapewnienia widok filtrowany rachunku dotyczącym platformy Azure i nie można określić w zasadach usługi Azure Resource Manager.

**Elementy etag**

Załóżmy, że dwie osoby lub dwóch procesów spróbuj zmodyfikować rekord DNS, w tym samym czasie. Który z nich wins? I będzie zwycięzca wiadomo, że zostały one zastąpione zmiany utworzone przez inną osobę?

Usługa system DNS Azure Stack używa elementów etag bezpiecznie obsłużyć równoczesnych zmian do tego samego zasobu. Elementy etag są niezależne od usługi Azure Resource Manager "Tagów". Każdy zasób DNS (strefy lub zestawu rekordów) ma element Etag skojarzone z nią. Zawsze, gdy zasób jest pobierana, jego element Etag również jest pobierany. Podczas aktualizowania zasobu, można przesłać element Etag dzięki usłudze Azure Stack DNS można sprawdzić, czy tag Etag na dopasowania serwera. Ponieważ każda aktualizacja do zasobu w wyniku element Etag, są ponownie generowane, element etag wskazuje, że nastąpiła zmiana współbieżnych. Elementy etag może również podczas tworzenia nowego zasobu upewnij się, że zasób nie istnieje.

Domyślnie program Azure Stack DNS PowerShell używa elementów etag zablokować równoczesnych zmian do stref, a także zestawy rekordów. Opcjonalny *-zastąpić* przełącznik może służyć do pomijania sprawdzania elementu Etag, w tym przypadku wszystkie równoczesnych zmian, które miały miejsce zostaną zastąpione.

Na poziomie interfejsu API REST usługi Azure Stack DNS elementów etag są określane przy użyciu nagłówków HTTP. Ich zachowanie znajduje się w poniższej tabeli:

| Nagłówek | Zachowanie|
|--------|---------|
| Brak   | Umieść zawsze powiedzie się (nie sprawdzeń element Etag)|
| IF-match| Umieść powiedzie się tylko, jeśli zasób istnieje i element Etag jest zgodny|
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

[Wprowadzenie do międzynarodowych nazw domen dla usługi Azure Stack](azure-stack-understanding-dns.md)
