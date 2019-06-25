---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: f42a97cdd74d360bc047ef561cbe626d526f9e4a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183810"
---
System nazw domen (DNS) jest używana do lokalizowania rzeczy w Internecie. Na przykład podczas wprowadzania adresu w przeglądarce lub kliknij łącze na stronie sieci web używa DNS, tłumaczenie domenę na adres IP. Adres IP jest swoistego, takie jak adres ulicy, ale nie jest bardzo ludzi przyjazna. Na przykład jest znacznie łatwiejsze do zapamiętania nazwę DNS, takich jak **contoso.com** niż do zapamiętania adresu IP, takich jak 192.168.1.88 lub 2001:0:4137:1f67:24a2:3888:9cce:fea3.

DNS system jest oparty na *rekordów*. Rejestruje skojarzenia określonego *nazwa*, takich jak **contoso.com**, za pomocą adresu IP lub nazwa DNS innej. W przypadku aplikacji, takich jak przeglądarki sieci web, wyszukuje nazwy w systemie DNS, znajduje rekord i używa, niezależnie od rodzaju wskazuje jako adres. Jeśli wartość, która wskazuje jest adres IP, Przeglądarka użyje tej wartości. Jeśli wskazuje on na inną nazwę DNS, aplikacja ma robić rozdzielczości. Ostatecznie rozpoznawanie nazw zakończy się adresu IP.

Kiedy tworzysz witrynę sieci Web platformy Azure, nazwa DNS jest automatycznie przypisywany do lokacji. Ta nazwa mają postać  **&lt;yoursitename&gt;. azurewebsites.net**. Po dodaniu witryny sieci Web jako punkt końcowy usługi Azure Traffic Manager, witryny sieci Web jest dostępna za pośrednictwem  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domeny.

> [!NOTE]
> Gdy witryny sieci Web jest skonfigurowany jako punkt końcowy usługi Traffic Manager, użyje **. trafficmanager.net** adresów, podczas tworzenia rekordów DNS.
> 
> Można używać tylko rekordy CNAME z usługą Traffic Manager
> 
> 

Są również wiele typów rekordów, każdy z nich swoje własne funkcje i ograniczenia, ale dla witryn sieci Web skonfigurowanych do jako punkty końcowe usługi Traffic Manager, tylko Dbamy o jeden; *CNAME* rekordów.

### <a name="cname-or-alias-record"></a>Rekord CNAME, Alias lub
Mapuje rekord CNAME *określonych* nazwy DNS, takich jak **mail.contoso.com** lub **www\.contoso.com**, do innej nazwy domeny (canonical). W przypadku usługi Azure Websites przy użyciu usługi Traffic Manager jest nazwa domeny canonical  **&lt;MojaAplikacja >. trafficmanager.net** nazwę domeny profilu usługi Traffic Manager. Po utworzeniu tego rekordu CNAME tworzy alias dla  **&lt;MojaAplikacja >. trafficmanager.net** nazwy domeny. Wpis CNAME zostanie rozpoznana jako adres IP Twojego  **&lt;MojaAplikacja >. trafficmanager.net** nazwę domeny na automatycznie, więc jeśli zmieni adres IP witryny sieci Web, nie trzeba podejmować żadnych działań.

Po odebraniu na usługi Traffic Manager ruchu następnie kieruje ruch do witryny sieci Web przy użyciu metody, który jest skonfigurowany do równoważenia obciążenia. Jest to całkowicie niewidoczne odwiedzający witrynę sieci Web. Użytkownicy będą widzieć tylko niestandardowej nazwy domeny w swojej przeglądarce.

> [!NOTE]
> Mapowanie poddomeny przy użyciu rekordu CNAME, takich jak Zezwalaj tylko niektóre rejestratorów domeny **www\.contoso.com**i nie główne nazwy, takie jak **contoso.com**. Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz temat w dokumentacji dostarczonej przez rejestrator, <a href="https://en.wikipedia.org/wiki/CNAME_record">Wikipedia wpis rekordu CNAME</a>, lub <a href="https://tools.ietf.org/html/rfc1035">nazw domen IETF — implementacja i specyfikacja</a> dokumentu.

