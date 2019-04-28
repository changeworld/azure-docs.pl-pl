---
title: Przewodnik rozwiązywania problemów z usługi Azure DNS | Dokumentacja firmy Microsoft
description: Jak rozwiązywać typowe problemy z usługą Azure DNS
services: dns
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
ms.assetid: 95b01dc3-ee69-4575-a259-4227131e4f9c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/20/2017
ms.author: genli
ms.openlocfilehash: 535e7604915555f32a7636b739c49f72cb0220c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823899"
---
# <a name="azure-dns-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z usługi Azure DNS

Ta strona zawiera informacje dotyczące rozwiązywania problemów dla często zadawane pytania w usłudze Azure DNS.

Jeśli te kroki nie rozwiążą problemu, możesz również wyszukać lub Opublikuj swój problem w naszym [forum pomocy technicznej społeczności w witrynie MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Alternatywnie Otwórz żądanie pomocy technicznej platformy Azure.


## <a name="i-cant-create-a-dns-zone"></a>Nie można utworzyć strefy DNS

Aby rozwiązać typowe problemy, spróbuj wykonać następujące kroki:

1.  Przejrzyj dzienniki inspekcji usługi Azure DNS, aby ustalić przyczynę błędu.
2.  Każda nazwa strefy DNS musi być unikatowa w obrębie swojej grupy zasobów. To znaczy, że dwie strefy DNS o takiej samej nazwie nie mogą współużytkować grupy zasobów. Spróbuj użyć innej nazwy strefy lub innej grupy zasobów.
3.  Możesz zobaczyć błąd „Osiągnięto lub przekroczono maksymalną liczbę stref w subskrypcji {identyfikator subskrypcji}”. Użyj innej subskrypcji platformy Azure, usuń kilka stref lub skontaktuj się z pomocą techniczną platformy Azure w celu podniesienia limitu dla subskrypcji.
4.  Możesz zobaczyć błąd „Strefa {nazwa strefy} jest niedostępna”. Ten błąd oznacza, że usługa DNS platformy Azure nie mogła przydzielić serwerów nazw dla tej strefy DNS. Spróbuj użyć innej nazwy strefy. Jeśli jesteś właścicielem nazwy domeny, możesz zamiast tego skontaktować się z działem pomocy technicznej platformy Azure, który może przydzielić odpowiednie serwery nazw.


### <a name="recommended-documents"></a>**Zalecane dokumenty**

[Strefy i rekordy DNS](dns-zones-records.md)
<br>
[Tworzenie strefy DNS](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nie można utworzyć nowego rekordu DNS

Aby rozwiązać typowe problemy, spróbuj wykonać następujące kroki:

1.  Przejrzyj dzienniki inspekcji usługi Azure DNS, aby ustalić przyczynę błędu.
2.  Czy zestaw rekordów już istnieje?  Usługa DNS platformy Azure zarządza rekordami za pomocą *zestawów*, czyli kolekcji rekordów o tej samej nazwie i typie. Jeśli już istnieje rekord o tej danej nazwie i typie, to w celu dodania kolejnego takiego rekordu trzeba poddać edycji istniejący zestaw rekordów.
3.  Czy próbujesz utworzyć rekord w wierzchołku strefy DNS (w „katalogu głównym” strefy)? Jeśli tak, to konwencja systemu DNS wymaga użycia znaku „@” jako nazwy rekordu. Standardy systemu DNS nie umożliwiają także używania rekordów CNAME w wierzchołku strefy.
4.  Czy występuje konflikt dotyczący rekordu CNAME?  Standardy systemu DNS nie umożliwiają używania rekordu CNAME o takiej samej nazwie jak nazwa rekordu innego typu. Jeśli istnieje już rekord CNAME, utworzenie innego typu rekordu o takiej samej nazwie zakończy się niepowodzeniem.  Analogicznie, utworzenie rekordu CNAME zakończy się niepowodzeniem, jeśli jego nazwa jest taka sama jak nazwa istniejącego rekordu innego typu. Rozwiąż ten konflikt, usuwając drugi rekord lub wybierając inną nazwę rekordu.
5.  Czy osiągnięto limit liczby zestawów rekordów w strefie DNS? Bieżąca liczba zestawów rekordów oraz najwyższa możliwa liczba zestawów rekordów są pokazane w witrynie Azure Portal w obszarze „Właściwości” danej strefy. Jeśli osiągnięto ten limit, usuń niektóre zestawy rekordów lub skontaktuj się z działem pomocy technicznej platformy Azure, aby zwiększyć limit liczby zestawów rekordów dla tej strefy. Następnie spróbuj ponownie. 


### <a name="recommended-documents"></a>**Zalecane dokumenty**

[Strefy i rekordy DNS](dns-zones-records.md)
<br>
[Tworzenie strefy DNS](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Nie mogę rozpoznać mojego rekordu DNS

Rozpoznawanie nazw DNS to wieloetapowy proces, który może się nie powieść z wielu powodów. Następujące kroki zawierają informacje pomocne w badaniu przyczyn nieudanego rozpoznawania nazw DNS w przypadku rekordu DNS w strefie hostowanej w usłudze DNS platformy Azure.

1.  Sprawdź, czy rekordy DNS zostały prawidłowo skonfigurowane w usłudze DNS platformy Azure. Przejrzyj rekordy DNS w witrynie Azure Portal, sprawdzając, czy nazwa strefy, nazwa rekordu i typ rekordu są prawidłowe.
2.  Sprawdź, czy rekordy DNS są rozpoznawane prawidłowo na serwerach nazw usługi DNS platformy Azure.
    - Jeśli zapytania DNS są wysyłane z komputera lokalnego, mogą być wyświetlane wyniki z pamięci podręcznej. Nie odzwierciedlają one aktualnego stanu serwerów nazw.  Ponadto w sieciach firmowych często są używane serwery proxy usługi DNS, które uniemożliwiają kierowanie zapytań DNS do określonych serwerów nazw.  Aby uniknąć tych problemów, skorzystaj z usługi rozpoznawania nazw opartej na sieci Web, np. [digwebinterface](https://digwebinterface.com).
    - Pamiętaj, aby określić poprawne serwery nazw dla strefy DNS zgodnie z informacjami w witrynie Azure Portal.
    - Sprawdź, czy nazwa DNS jest poprawna (należy określić w pełni kwalifikowaną nazwę, z uwzględnieniem nazwy strefy) i czy typ rekordu jest poprawny
3.  Zobaczy, czy nazwa domeny DNS została prawidłowo [oddelegowana na serwery nazw usługi DNS platformy Azure](dns-domain-delegation.md). Istnieje [wiele witryn innych firm umożliwiających weryfikowanie delegowania DNS](https://www.bing.com/search?q=dns+check+tool). Ten test jest testem delegowania *strefy*, więc należy podać tylko nazwę strefy DNS, a nie w pełni kwalifikowaną nazwę rekordu.
4.  Po ukończeniu powyższych działań rozpoznawanie rekordu DNS powinno już przebiegać prawidłowo. Do weryfikacji można ponownie skorzystać z usługi [digwebinterface](https://digwebinterface.com), tym razem stosując domyślne ustawienia serwera nazw.


### <a name="recommended-documents"></a>**Zalecane dokumenty**

[Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Jak określić „usługę” i „protokół” dla rekordu SRV?

Usługa DNS platformy Azure zarządza rekordami DBS za pomocą zestawów, czyli kolekcji rekordów o tej samej nazwie i typie. W przypadku zestawu rekordów SRV „usługa” i „protokół” muszą być określone jako część nazwy zestawu rekordów. Inne parametry rekordów SRV („priorytet”, „waga”, „port” i „miejsce docelowe”) są określane osobno dla każdego rekordu w zestawie rekordów.

Przykładowe nazwy rekordów SRV (nazwa usługi „sip”, protokół „tcp”):

- \_sip.\_tcp (tworzy zestaw rekordów w wierzchołku strefy)
- \_sip.\_tcp.sipservice (tworzy zestaw rekordów o nazwie „sipservice”)

### <a name="recommended-documents"></a>**Zalecane dokumenty**

[Strefy i rekordy DNS](dns-zones-records.md)
<br>
[Create DNS record sets and records by using the Azure portal (Tworzenie zestawów rekordów i rekordów DNS przy użyciu witryny Azure Portal)](dns-getstarted-create-recordset-portal.md)
<br>
[Typ rekordu SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [strefy i rekordy DNS platformy Azure](dns-zones-records.md)
* Aby rozpocząć korzystanie z usługi Azure DNS, Dowiedz się, jak [utworzyć strefę DNS](dns-getstarted-create-dnszone-portal.md) i [tworzenie rekordów DNS](dns-getstarted-create-recordset-portal.md).
* Aby przeprowadzić migrację istniejącej strefy DNS, Dowiedz się, jak [importować i eksportować plik strefy DNS](dns-import-export.md).

