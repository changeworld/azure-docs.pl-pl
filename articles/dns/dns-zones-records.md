---
title: Omówienie stref i rekordów DNS — usługa Azure DNS
description: Omówienie obsługi obsługi stref i rekordów DNS w usłudze Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 19189af6424960b8e20be686af745b10f2d8578b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265157"
---
# <a name="overview-of-dns-zones-and-records"></a>Omówienie stref i rekordów DNS

Na tej stronie opisano kluczowe pojęcia dotyczące domen, stref DNS oraz rekordów i zestawów rekordów DNS oraz sposobu ich obsługi w usłudze Azure DNS.

## <a name="domain-names"></a>Nazwy domen

System nazw domen (DNS, Domain Name System) jest hierarchią domen. Hierarchia rozpoczyna się od domeny głównej, której nazwa to po prostu „**.**”.  Poniżej są domeny najwyższego poziomu, takie jak „com”, „net”, „org”, „uk” lub „jp”.  Pod nimi są domeny drugiego poziomu, takie jak „org.uk” lub „co.jp”. Domeny w hierarchii DNS są dystrybuowane globalnie, hostowane przez serwery nazw DNS na całym świecie.

Rejestrator nazw domen to organizacja, która umożliwia zakup nazwy domeny, `contoso.com`takiej jak .  Zakup nazwy domeny daje prawo do kontrolowania hierarchii DNS pod tą nazwą, `www.contoso.com` na przykład umożliwiając kierowanie nazwy do witryny sieci Web firmy. Rejestrator może hostować domenę we własnych serwerach nazw w Twoim imieniu lub umożliwia określenie alternatywnych serwerów nazw.

Usługa Azure DNS zapewnia globalnie rozproszoną infrastrukturę serwera nazw o wysokiej dostępności, której można używać do hosta domeny. Hostując domeny w usłudze Azure DNS, możesz zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi, rozliczeń i pomocy technicznej, co inne usługi platformy Azure.

Usługa Azure DNS nie obsługuje obecnie zakupu nazw domen. Jeśli chcesz kupić nazwę domeny, musisz użyć zewnętrznego rejestratora nazw domen. Rejestrator zazwyczaj pobiera niewielką opłatę roczną. Domeny mogą być następnie hostowane w usłudze Azure DNS do zarządzania rekordami DNS. Aby uzyskać szczegółowe informacje, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>Strefy DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Rekordy DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Czas na żywo

Czas wygaśnięcia lub czasu wygaśnięcia określa, jak długo każdy rekord jest buforowany przez klientów przed ponownym podszerceniem. W powyższym przykładzie czas wygaśnięcia wynosi 3600 sekund lub 1 godzinę.

W usłudze Azure DNS czas wygaśnięcia jest określony dla zestawu rekordów, a nie dla każdego rekordu, więc ta sama wartość jest używana dla wszystkich rekordów w tym zestawie rekordów.  Można określić dowolną wartość TTL z 1 do 2 147 483 647 sekund.

### <a name="wildcard-records"></a>Rekordy symboli wieloznacznych

Usługa DNS platformy Azure obsługuje [rekordy z użyciem symboli wieloznacznych](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Rekordy symboli wieloznacznych są zwracane w odpowiedzi na każde zapytanie o pasującej nazwie (chyba że istnieje bliższe dopasowanie z zestawu rekordów innych niż symbol wieloznaczny). Usługa Azure DNS obsługuje zestawy rekordów wieloznacznych dla wszystkich typów rekordów z wyjątkiem ns i soa.

Aby utworzyć zestaw rekordów wieloznacznych,\*użyj nazwy zestawu rekordów ' ' . Alternatywnie można również użyć nazwy\*z ' ' jako jej lewej\*etykiety, na przykład " .foo'.

### <a name="caa-records"></a>Rekordy CAA

Rekordy CAA umożliwiają właścicielom domen określenie, które urzędy certyfikacji są autoryzowane do wystawiania certyfikatów dla swojej domeny. Dzięki temu urząd certyfikacji uniknąć błędnego wystawiania certyfikatów w pewnych okolicznościach. Rekordy CAA mają trzy właściwości:
* **Flagi:** Jest to liczba całkowita z 0 do 255, używana do reprezentowania flagi krytycznej, która ma specjalne znaczenie dla [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: ciąg ASCII, który może być jedną z następujących czynności:
    * **issue**: użyj tego, jeśli chcesz określić jednostki certyfikacji, które mogą wystawiać certyfikaty (wszystkie typy)
    * **issuewild**: użyj tego, jeśli chcesz określić jednostki certyfikacji, które mogą wystawiać certyfikaty (tylko certyfikaty z symbolami wieloznaczymi)
    * **iodef**: określić adres e-mail lub nazwę hosta, na które jednostki certyfikacji mogą powiadamiać o nieautoryzowanych żądaniach wydania certyfikatu
* **Wartość:** wartość wybranego znacznika

### <a name="cname-records"></a>Rekordy CNAME

Zestawy rekordów CNAME nie mogą współistnieć z innymi zestawami rekordów o tej samej nazwie. Na przykład nie można utworzyć zestawu rekordów CNAME o względnej nazwie "www" i rekordu A o względnej nazwie "www" w tym samym czasie.

Ponieważ wierzchołek\@strefy (nazwa = ' ') zawsze zawiera zestawy rekordów NS i SOA, które zostały utworzone podczas tworzenia strefy, nie można utworzyć rekordu CNAME ustawionego w wierzchołku strefy.

Ograniczenia te wynikają ze standardów DNS i nie są ograniczeniami usługi Azure DNS.

### <a name="ns-records"></a>Rekordy NS

Rekord NS ustawiony na wierzchołku strefy (nazwa '\@') jest tworzony automatycznie dla każdej strefy DNS i jest usuwany automatycznie po usunięciu strefy (nie można jej usunąć oddzielnie).

Ten zestaw rekordów zawiera nazwy serwerów nazw DNS platformy Azure przypisanych do strefy. Do tego zestawu rekordów NS można dodać dodatkowe serwery nazw, aby obsługiwać domeny współ hostingowe z więcej niż jednym dostawcą DNS. Można również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw DNS platformy Azure. 

Dotyczy to tylko rekordu NS ustawionego w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) mogą być tworzone, modyfikowane i usuwane bez ograniczeń.

### <a name="soa-records"></a>Rekordy SOA

Zestaw rekordów SOA jest tworzony automatycznie na wierzchołku każdej strefy (nazwa = '\@') i jest usuwany automatycznie po usunięciu strefy.  Rekordy SOA nie mogą być tworzone ani usuwane oddzielnie.

Można zmodyfikować wszystkie właściwości rekordu SOA z wyjątkiem właściwości "host", która jest wstępnie skonfigurowana do odwoływania się do nazwy serwera nazw podstawowych dostarczonej przez usługę Azure DNS.

Numer seryjny strefy w rekordzie SOA nie jest aktualizowany automatycznie po wprowadzeniu zmian w rekordach w strefie. W razie potrzeby można go zaktualizować ręcznie, edytując rekord SOA.

### <a name="spf-records"></a>Rekordy SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Rekordy SRV

[Rekordy SRV](https://en.wikipedia.org/wiki/SRV_record) są używane przez różne usługi do określania lokalizacji serwerów. Podczas określania rekordu SRV w usłudze Azure DNS:

* *Usługa* i *protokół* muszą być określone jako część nazwy zestawu rekordów, poprzedzone podkreśleniami.  Na przykład\_, ' łyk. \_tcp.name".  Dla rekordu w wierzchołku strefy,\@nie ma potrzeby, aby określić ' '\_w nazwie rekordu, po prostu użyj usługi i protokołu, na przykład ' sip. \_tcp".
* *Priorytet*, *waga,* *port*i *cel* są określone jako parametry każdego rekordu w zestawie rekordów.

### <a name="txt-records"></a>Rekordy TXT

Rekordy TXT są używane do mapowania nazw domen na dowolne ciągi tekstowe. Są one używane w wielu aplikacjach, w szczególności związanych z konfiguracją poczty e-mail, takich jak [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) i [DomainKeys Identified Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Standardy DNS zezwalają na to, aby pojedynczy rekord TXT zawierał wiele ciągów, z których każdy może mieć długość do 254 znaków. Gdzie wiele ciągów są używane, są one łączone przez klientów i traktowane jako pojedynczy ciąg.

Podczas wywoływania interfejsu API REST usługi Azure DNS należy określić każdy ciąg TXT oddzielnie.  Podczas korzystania z witryny Azure portal, powershell lub interfejsów wiersza polecenia należy określić jeden ciąg na rekord, który jest automatycznie podzielony na 254-znakowe segmenty, jeśli to konieczne.

Wielu ciągów w rekordzie DNS nie należy mylić z wieloma rekordami TXT w zestawie rekordów TXT.  Zestaw rekordów TXT może zawierać wiele rekordów, *z których każdy* może zawierać wiele ciągów.  Usługa Azure DNS obsługuje łączną długość ciągu do 1024 znaków w każdym zestawie rekordów TXT (we wszystkich połączonych rekordach).

## <a name="tags-and-metadata"></a>Tagi i metadane

### <a name="tags"></a>Tagi

Tagi są listą par nazwa-wartość i są używane przez usługę Azure Resource Manager do etykietowania zasobów.  Usługa Azure Resource Manager używa tagów, aby włączyć filtrowane widoki rachunku platformy Azure, a także umożliwia ustawienie zasad, na których są wymagane tagi. Aby uzyskać więcej informacji na temat tagów, zobacz [Using tags to organize your Azure resources](../azure-resource-manager/management/tag-resources.md) (Porządkowanie zasobów na platformie Azure za pomocą tagów).

Usługa Azure DNS obsługuje używanie tagów usługi Azure Resource Manager w zasobach strefy DNS.  Nie obsługuje tagów w zestawach rekordów DNS, chociaż jako alternatywne "metadane" są obsługiwane w zestawach rekordów DNS, jak wyjaśniono poniżej.

### <a name="metadata"></a>Metadane

Jako alternatywa dla znaczników zestawu rekordów usługa Azure DNS obsługuje dodawanie adnotacji do zestawów rekordów przy użyciu "metadanych".  Podobnie jak w tagach metadane umożliwiają skojarzenie par nazw i wartości z każdym zestawem rekordów.  Może to być przydatne, na przykład do rejestrowania celu każdego zestawu rekordów.  W przeciwieństwie do tagów metadane nie mogą być używane do zapewnienia filtrowanego widoku rachunku platformy Azure i nie można ich określić w zasadach usługi Azure Resource Manager.

## <a name="etags"></a>Etagi

Załóżmy, że dwie osoby lub dwa procesy próbują zmodyfikować rekord DNS w tym samym czasie. Który z nich wygra? A czy zwycięzca wie, że nadpisywał zmiany stworzone przez kogoś innego?

Usługa Azure DNS używa tagów Etags do bezpiecznego obsługi równoczesnych zmian w tym samym zasobie. Znaczniki Etags są oddzielone od ["Tagów" usługi Azure Resource Manager](#tags). Każdy zasób DNS (strefa lub zestaw rekordów) ma etag skojarzony z nim. Za każdym razem, gdy zasób jest pobierany, jego Etag jest również pobierany. Podczas aktualizowania zasobu, można wybrać przekazać z powrotem Etag więc usługa Azure DNS można sprawdzić, czy etag na serwerze jest zgodny. Ponieważ każda aktualizacja do zasobu powoduje etag jest generowany ponownie, niezgodność Etag wskazuje, że wystąpiła zmiana równoczesnych. Etags można również użyć podczas tworzenia nowego zasobu, aby upewnić się, że zasób jeszcze nie istnieje.

Domyślnie usługa Azure DNS PowerShell używa tagów Etags do blokowania równoczesnych zmian w strefach i zestawach rekordów. Opcjonalny przełącznik *-Overwrite* może służyć do pomijania kontroli Etag, w którym to przypadku wszystkie zmiany współbieżne, które wystąpiły są zastępowane.

Na poziomie interfejsu API DNS REST platformy Azure tagi Etags są określane przy użyciu nagłówków HTTP.  Ich zachowanie znajduje się w poniższej tabeli:

| Nagłówek | Zachowanie |
| --- | --- |
| Brak |PUT zawsze się powiedzie (bez kontroli Etag) |
| If-match \<etag> |Put powiedzie się tylko wtedy, gdy istnieje zasób i Etag pasuje |
| Jeśli mecz * |PUT działa pomyślnie tylko wtedy, gdy istnieje zasób |
| Jeśli-brak-mecz * |Put działa pomyślnie tylko wtedy, gdy zasób nie istnieje |


## <a name="limits"></a>Limity

Podczas korzystania z usługi Azure DNS obowiązują następujące limity domyślne:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć korzystanie z usługi Azure DNS, dowiedz się, jak [utworzyć strefę DNS](dns-getstarted-create-dnszone-portal.md) i [utworzyć rekordy DNS](dns-getstarted-create-recordset-portal.md).
* Aby przeprowadzić migrację istniejącej strefy DNS, dowiedz się, jak [zaimportować i wyeksportować plik strefy DNS](dns-import-export.md).
