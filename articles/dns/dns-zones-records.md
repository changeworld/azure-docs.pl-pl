---
title: Przegląd stref i rekordów DNS — usłudze Azure DNS | Dokumentacja firmy Microsoft
description: Omówienie pomocy technicznej do hostowania strefy DNS i rekordów w usłudze Microsoft Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: 2b9c8f1bb7407dd36623fd8ad68f9489172a1caf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712220"
---
# <a name="overview-of-dns-zones-and-records"></a>Przegląd stref i rekordów DNS

Ta strona wyjaśniono kluczowe pojęcia związane z domen, stref DNS i rekordów DNS i zestawami rekordów i jak są one obsługiwane w usłudze Azure DNS.

## <a name="domain-names"></a>Nazwy domen

System nazw domen (DNS, Domain Name System) jest hierarchią domen. Hierarchia rozpoczyna się od domeny głównej, której nazwa to po prostu „ **.** ”.  Poniżej są domeny najwyższego poziomu, takie jak „com”, „net”, „org”, „uk” lub „jp”.  Pod nimi są domeny drugiego poziomu, takie jak „org.uk” lub „co.jp”. Domeny w hierarchii DNS są globalnie rozproszone, hostowane przez serwery DNS na całym świecie.

Rejestratora nazw domen jest organizacja, która umożliwia zakup nazwy domeny, taką jak "contoso.com".  Zakup nazwy domeny zapewnia prawo do kontrolowania hierarchię DNS w ramach tej samej nazwie, na przykład umożliwiając bezpośrednie nazwy "www.contoso.com" do witryny sieci web firmy. Rejestrator może hostowanie domeny w jego własnej serwery nazw w Twoim imieniu lub pozwalają na określenie alternatywnej nazwy serwerów.

Usługa Azure DNS zapewnia infrastruktury serwera nazwę globalnie dystrybuowana, wysokiej dostępności, którym można hostować swoją domenę. Hostowanie domen w usłudze Azure DNS, można zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi, rozliczeń i pomocy technicznej jako innych usług platformy Azure.

Usługa Azure DNS nie obsługuje aktualnie możliwości zakupu nazw domen. Zakup nazwy domeny, należy użyć Rejestratora nazw domen innej firmy. Rejestrator zwykle opłaty za niewielką opłatą roczną. Następnie domeny mogą być hostowane w usłudze Azure DNS do zarządzania rekordami DNS. Aby uzyskać szczegółowe informacje, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>Strefy DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Rekordy DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Czas wygaśnięcia

Czas wygaśnięcia (TTL) Określa jak długo każdy rekord jest buforowany przez klientów przed trwa ponowieniu. W powyższym przykładzie czas wygaśnięcia wynosi 3600 sekund lub 1 godzinę.

W usłudze Azure DNS czas wygaśnięcia jest określany dla zestawu rekordów, a nie dla każdego rekordu, dzięki czemu tę samą wartość jest używana dla wszystkich rekordów w danym zestawie rekordów.  Można określić wartość czasu wygaśnięcia z zakresu od 1 do 2 147 483 647 sekund.

### <a name="wildcard-records"></a>Symbol wieloznaczny rekordów

Usługa DNS platformy Azure obsługuje [rekordy z użyciem symboli wieloznacznych](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Symbol wieloznaczny rekordy są zwracane w odpowiedzi na dowolne zapytanie o takiej samej nazwie (chyba że istnieje lepsze dopasowanie pochodzące zestawu rekordów bez symboli wieloznacznych). Usługa DNS platformy Azure obsługuje zestawy rekordów z użyciem symboli wieloznacznych dla wszystkich typów rekordów z wyjątkiem NS i SOA.

Aby utworzyć zestaw rekordów symboli wieloznacznych, użyj nazwy zestawu rekordów "\*". Alternatywnie można również użyć nazwy z '\*"jako jego skrajnej lewej etykiecie, na przykład"\*.foo ".

### <a name="caa-records"></a>CAA records

Rekordów CAA umożliwia właścicielom domen Określanie, które certyfikatów urzędów certyfikacji będą upoważnione do wystawiania certyfikatów dla danej domeny. Dzięki temu urzędy certyfikacji uniknąć źle wystawianie certyfikatów w pewnych okolicznościach. Rekordami CAA można mieć trzy właściwości:
* **Flagi**: Jest to liczba całkowita między 0 a 255, używany do reprezentowania flagi stanu krytycznego, który ma specjalne znaczenie dla [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: ciąg ASCII, który może być jedną z następujących czynności:
    * **problem**: Użyj, jeżeli chcesz określić urzędów certyfikacji, które są dozwolone do wystawiania certyfikatów (wszystkie typy)
    * **issuewild**: Użyj, jeżeli chcesz określić urzędów certyfikacji, które są dozwolone do wystawiania certyfikatów (tylko w przypadku certyfikatów symboli wieloznacznych)
    * **iodef**: Określ adres e-mail lub nazwa hosta, do którego urzędów certyfikacji może generować powiadomienia dla żądań problem nieautoryzowanego certyfikatu
* **Wartość**: wartość dla konkretnego znacznika wybranego

### <a name="cname-records"></a>Rekordy CNAME

Zestawy rekordów CNAME nie mogą współistnieć z innymi zestawami rekordów o tej samej nazwie. Na przykład nie można utworzyć zestawu rekordów CNAME o nazwie względnej "www" oraz rekordu A o nazwie względnej "www" w tym samym czasie.

Ponieważ wierzchołek strefy (nazwa = "\@") zawsze zawiera zestawy, które zostały utworzone podczas tworzenia strefy rekordów NS i SOA, nie można utworzyć zestawu rekordów CNAME w wierzchołku strefy.

Te ograniczenia wynikają z standardy systemu DNS i nie są ograniczenia usługi DNS platformy Azure.

### <a name="ns-records"></a>Rekordy NS

NS zarejestrować zestaw w wierzchołku strefy (nazwa "\@") jest tworzony automatycznie w każdej strefie DNS i jest automatycznie usuwany po usunięciu strefy (nie można go usunąć oddzielnie).

Ten zestaw rekordów zawiera nazwy serwerów nazw usługi Azure DNS, które są przypisane do strefy. Możesz dodać dodatkowe serwery do tego rekordu NS skonfigurowane, do obsługi domeny hosting współpracujących z więcej niż jednego dostawcę DNS. Można również zmodyfikować czas wygaśnięcia i metadanych dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw usługi Azure DNS. 

Dotyczy to tylko zestaw w wierzchołku strefy rekordów NS. Inne zestawy rekordów NS w strefie (tak jak delegować stref podrzędnych) można utworzone, zmodyfikowane i usuwane bez ograniczeń.

### <a name="soa-records"></a>Rekordów SOA

Zestaw rekordów SOA jest tworzony automatycznie w wierzchołku każdej strefy (nazwa = "\@") i jest automatycznie usuwany po jej usunięciu.  Rekordów SOA nie można utworzyć ani usunąć oddzielnie.

Można zmodyfikować wszystkie właściwości o rekord SOA, z wyjątkiem właściwości "host", który jest wstępnie skonfigurowana do odwoływania się do nazwy serwera nazwy podstawowej, które są dostarczane przez usługę Azure DNS.

### <a name="spf-records"></a>Rekordy SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Rekordy SRV

[Rekordy SRV](https://en.wikipedia.org/wiki/SRV_record) są używane przez różne usługi, aby określić lokalizacje serwera. Podczas określania rekordów SRV w usłudze Azure DNS:

* *Usługi* i *protokołu* muszą być określone jako część nazwy zestawu rekordów, poprzedzony znakiem podkreślenia.  Na przykład "\_sip.\_ TCP.name ".  Rekord w wierzchołku strefy, nie ma potrzeby określić "\@"w nazwie rekordu, po prostu użyj usługa i protokół, na przykład"\_sip.\_ TCP ".
* *Priorytet*, *wagi*, *portu*, i *docelowej* są określane jako parametry każdego rekordu w zestawie rekordów.

### <a name="txt-records"></a>Rekordy TXT

Rekordy TXT są używane do mapowania nazw domen na dowolne ciągi znaków. Są one używane w wielu aplikacjach, w szczególności związane z konfiguracją poczty e-mail, takich jak [Framework zasad nadawcy (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) i [DomainKeys zidentyfikowane poczty (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Standardy systemu DNS zezwala na pojedynczy rekord TXT, który zawiera wiele ciągów, z których każdy może być maksymalnie 254 znaków. W przypadku wielu ciągów są łączone przez klientów i traktowany jako pojedynczy ciąg.

Podczas wywoływania interfejsu API REST usługi Azure DNS, należy określić każdy ciąg TXT oddzielnie.  Korzystając z witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia interfejsów należy określić pojedynczy ciąg na rekord, który automatycznie jest podzielony na segmenty 254 znaków, jeśli to konieczne.

Nie należy mylić wielu ciągów w rekordzie DNS z wielu rekordów TXT w zestawie rekordów TXT.  Zestaw rekordów TXT może zawierać wiele rekordów *każdy z nich* może zawierać wielu ciągów.  Usługa DNS platformy Azure obsługuje całkowita długość ciągu równa maksymalnie 1024 znaki każdego zestawu rekordów TXT (we wszystkich rekordach, połączone).

## <a name="tags-and-metadata"></a>Znaczniki i metadane

### <a name="tags"></a>`Tags`

Tagi są listą par nazwa wartość i są używane przez usługę Azure Resource Manager do oznaczania zasobów etykietami.  Usługa Azure Resource Manager używa tagów, aby umożliwić filtrowane widoki rachunku dotyczącym platformy Azure i umożliwia także ustawić zasady, na którym tagi są wymagane. Aby uzyskać więcej informacji na temat tagów, zobacz [Porządkowanie zasobów na platformie Azure za pomocą tagów](../azure-resource-manager/resource-group-using-tags.md).

Usługa DNS platformy Azure obsługuje za pomocą usługi Azure Resource Manager tagów zasobów strefy DNS.  Nie obsługuje ona tagów w zestawach rekordów DNS, mimo że jako alternatywę "metadata" jest obsługiwana na zestawów rekordów DNS, co zostało opisane poniżej.

### <a name="metadata"></a>Metadane

Jako alternatywę do zestawu rekordów tagów usługi Azure DNS obsługuje dodawanie adnotacji do zestawów rekordów przy użyciu "metadata".  Podobnie jak tagi, metadanych umożliwia kojarzenie pary nazwa wartość z każdego zestawu rekordów.  Może to być przydatne, na przykład rekord celem każdy zestaw rekordów.  W przeciwieństwie do tagów metadanych nie może służyć do zapewnienia widok filtrowany rachunku dotyczącym platformy Azure i nie można określić w zasadach usługi Azure Resource Manager.

## <a name="etags"></a>Elementy etag

Załóżmy, że dwie osoby lub dwóch procesów spróbuj zmodyfikować rekord DNS, w tym samym czasie. Który z nich wins? I będzie zwycięzca wiadomo, że zostały one zastąpione zmiany utworzone przez inną osobę?

Usługa Azure DNS korzysta z elementów etag bezpiecznie obsłużyć równoczesnych zmian do tego samego zasobu. Elementy etag są niezależne od [usługi Azure Resource Manager "Tagów"](#tags). Każdy zasób DNS (strefy lub zestawu rekordów) ma element Etag skojarzone z nią. Zawsze, gdy zasób jest pobierana, jego element Etag również jest pobierany. Podczas aktualizowania zasobu, można przesłać element Etag dzięki usłudze Azure DNS można sprawdzić, czy tag Etag na dopasowania serwera. Ponieważ każda aktualizacja do zasobu w wyniku element Etag, są ponownie generowane, element etag wskazuje, że nastąpiła zmiana współbieżnych. Elementy etag może również podczas tworzenia nowego zasobu upewnij się, że zasób nie istnieje.

Domyślnie Azure DNS PowerShell używa elementów etag zablokować równoczesnych zmian do stref, a także zestawy rekordów. Opcjonalny *-zastąpić* przełącznik może służyć do pomijania sprawdzania elementu Etag, w tym przypadku wszystkie równoczesnych zmian, które miały miejsce zostaną zastąpione.

Na poziomie interfejsu API REST usługi Azure DNS elementów etag są określane przy użyciu nagłówków HTTP.  Ich zachowanie znajduje się w poniższej tabeli:

| nagłówek | Zachowanie |
| --- | --- |
| Brak |Umieść zawsze powiedzie się (nie sprawdzeń element Etag) |
| If-match \<etag> |Umieść powiedzie się tylko, jeśli zasób istnieje i element Etag jest zgodny |
| IF-match * |Umieść powiedzie się tylko, jeśli istnieje zasób |
| IF-none-match * |Umieść powiedzie się tylko, jeśli zasób nie istnieje. |


## <a name="limits"></a>Limits

Następujące limity domyślne są stosowane podczas korzystania z usługi Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć korzystanie z usługi Azure DNS, Dowiedz się, jak [utworzyć strefę DNS](dns-getstarted-create-dnszone-portal.md) i [tworzenie rekordów DNS](dns-getstarted-create-recordset-portal.md).
* Aby przeprowadzić migrację istniejącej strefy DNS, Dowiedz się, jak [importować i eksportować plik strefy DNS](dns-import-export.md).
