---
title: Typy punktów końcowych usługi Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano różne typy punktów końcowych, które mogą być używane z usługą Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: dc76f56b6c05f22a380ff33715fe22e8c72e4891
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508436"
---
# <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager
Microsoft Azure Traffic Manager umożliwia kontrolowanie sposobu dystrybucji ruchu sieciowego do wdrożenia aplikacji działające w różnych centrach danych. Skonfiguruj każde wdrożenie aplikacji jako punktu końcowego w usłudze Traffic Manager. Gdy usługa Traffic Manager odbiera żądanie DNS, wybiera dostępnego punktu końcowego do zwrócenia w odpowiedzi DNS. Usługa Traffic manager Określa wybór na bieżący stan punktu końcowego i metody routingu ruchu. Aby uzyskać więcej informacji, zobacz [jak działa usługa Traffic Manager](traffic-manager-how-it-works.md).

Istnieją trzy typy obsługiwanych przez usługę Traffic Manager punktu końcowego:
* **Punkty końcowe platformy Azure** używane w przypadku usług hostowanych na platformie Azure.
* **Zewnętrzne punkty końcowe** są używane dla adresów IPv4 i IPv6, nazwy FQDN, lub dla usług hostowanych poza systemem Azure, która może być lokalne lub u innego dostawcy usług hostingowych.
* **Zagnieżdżone punktów końcowych** są używane do łączenia profilów usługi Traffic Manager, aby utworzyć bardziej elastyczne systemy kierowania ruchu do obsługi wymagań większych i bardziej złożonych wdrożeń.

Nie ma żadnych ograniczeń, w jaki sposób punktów końcowych o różnych typach są łączone w jeden profil usługi Traffic Manager. Każdy profil może zawierać żadnych różne typy punktów końcowych.

W poniższych sekcjach opisano każdy typ punktu końcowego w dokładniej.

## <a name="azure-endpoints"></a>Punkty końcowe platformy Azure

Punkty końcowe platformy Azure używane w przypadku usług opartych na platformie Azure w usłudze Traffic Manager. Obsługiwane są następujące typy zasobów platformy Azure:

* Usługi PaaS w chmurze.
* Aplikacje internetowe
* Miejsc aplikacji sieci Web
* Zasoby publicznego adresu IP (które mogą być podłączone do maszyn wirtualnych bezpośrednio lub za pośrednictwem usługi Azure Load Balancer). Publiczny adres IP musi mieć nazwę DNS, przypisany do użycia w profilu usługi Traffic Manager.

Publiczny adres IP zasoby są zasobami usługi Azure Resource Manager. Nie istnieją w klasycznym modelu wdrażania. Tak więc są tylko obsługiwane na forach usługi Traffic Manager w usłudze Azure Resource Manager środowisk. Inne typy punktów końcowych są obsługiwane przy użyciu usługi Resource Manager oraz klasycznego modelu wdrażania.

Korzystając z punkty końcowe platformy Azure, usługa Traffic Manager wykrywa "Classic" maszyny Wirtualnej IaaS, usługi w chmurze lub aplikacji sieci Web jest zatrzymana i uruchomiona. Ten stan jest widoczny w stan punktu końcowego. Zobacz [monitorowania punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) Aby uzyskać szczegółowe informacje. Po zatrzymaniu podstawowej usługi Traffic Manager wykonuje kontrole kondycji punktów końcowych lub bezpośrednie kierowanie ruchu do punktu końcowego. Brak zdarzeń rozliczeń usługi Traffic Manager miejsce w przypadku zatrzymania. Po ponownym uruchomieniu usługi, wznawia rozliczeń i punkt końcowy jest uprawniona do odbierania ruchu. Wykrywanie nie ma zastosowania do publicznego adresu IP punktów końcowych.

## <a name="external-endpoints"></a>Zewnętrzne punkty końcowe

Zewnętrzne punkty końcowe są używane dla obu adresów IPv4 i IPv6, nazwy FQDN, lub usługi spoza platformy Azure. Korzystanie z punktami końcowymi adresów IPv4 i IPv6 zezwala na ruch Menedżera do sprawdzenia kondycji punktów końcowych bez nazwy DNS dla nich. Co w efekcie usługi Traffic Manager może odpowiadać na zapytania o rekordy A i AAAA, gdy zwracany jest ten punkt końcowy w odpowiedzi. Usługi spoza platformy Azure mogą obejmować usługi hostowanej lokalnie lub u innego dostawcy. Zewnętrzne punkty końcowe można użyć pojedynczo lub w połączeniu z punktami końcowymi usługi Azure w tym samym profilu usługi Traffic Manager, z wyjątkiem punktów końcowych, określony jako adresów IPv4 lub IPv6, które mogą być tylko zewnętrzne punkty końcowe. Łącząc punkty końcowe platformy Azure przy użyciu zewnętrzne punkty końcowe umożliwia różne scenariusze:

* Obejmij zwiększenia nadmiarowości istniejącej aplikacji w środowisku lokalnym albo model trybu failover active-active lub active-passive przy użyciu platformy Azure. 
* Kierowanie ruchu do punktów końcowych, które nie mają skojarzonych z nimi nazwy DNS. Ponadto Zmniejsz ogólny czas oczekiwania wyszukiwania DNS, usuwając potrzeba wykonania drugiej zapytanie DNS w celu uzyskania adresu IP, nazwy DNS, zwracane. 
* Zmniejszenie opóźnień aplikacji dla użytkowników na całym świecie, Rozszerz istniejącą aplikację w środowisku lokalnym, do dodatkowych lokalizacjach geograficznych platformy Azure. Aby uzyskać więcej informacji, zobacz [usługi Traffic Manager "Wydajność" routing ruchu](traffic-manager-routing-methods.md#performance).
* Podaj dodatkowej pojemności dla istniejącej lokalnej aplikacji, ciągle lub jako "dużego ruchu do chmury" rozwiązania spełniającego wzrost popytu korzystanie z platformy Azure.

W niektórych przypadkach warto użyć zewnętrzne punkty końcowe można odwoływać się do usług platformy Azure (przykłady można znaleźć [— często zadawane pytania](traffic-manager-faqs.md#traffic-manager-endpoints)). W tym przypadku kontroli kondycji są rozliczane według stawki punkty końcowe platformy Azure, nie współczynnik zewnętrzne punkty końcowe. Jednak w przeciwieństwie do punktów końcowych platformy Azure, Jeśli zatrzymasz lub usuniesz podstawowej usługi kondycji Sprawdź rozliczeń kontynuowany do momentu wyłączenia lub usunięcia punktu końcowego w usłudze Traffic Manager.

## <a name="nested-endpoints"></a>Zagnieżdżone punktów końcowych

Punkty końcowe zagnieżdżonych łączyć wiele profilów usługi Traffic Manager, aby tworzyć elastyczne schematy routing ruchu i potrzeb większych i złożonych wdrożeń. Dzięki punktom końcowym zagnieżdżone profilu "podrzędne" zostanie dodany jako punkt końcowy do profilu "parent". Profile nadrzędnym a podrzędnym mogą zawierać inne punkty końcowe dowolnego typu, łącznie z innych profilów zagnieżdżonych. Aby uzyskać więcej informacji, zobacz [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Usługa Web Apps jako punkty końcowe

Dodatkowe zagadnienia do rozważenia podczas konfigurowania aplikacji sieci Web jako punktów końcowych w usłudze Traffic Manager:

1. Tylko aplikacje sieci Web w wersji "Standard" lub nowszym są uprawnione do używania z usługą Traffic Manager. Aby dodać aplikację sieci Web z niższym poziomem jednostki SKU zakończy się niepowodzeniem. Obniżenie poziomu jednostki SKU istniejącej aplikacji sieci Web wyników w usłudze Traffic Manager nie będzie już wysyłać ruchu do tej aplikacji sieci Web. Aby uzyskać więcej informacji na temat obsługiwanych planów zobacz [planów usługi App Service](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Gdy punkt końcowy odbierze żądanie HTTP, używa nagłówek "host" w żądaniu można określić, która aplikacja sieci Web powinien obsługiwać żądania. Nagłówek hosta zawiera nazwę DNS używane, aby zainicjować żądanie, na przykład "contosoapp.azurewebsites.net". Aby użyć innej nazwy DNS z aplikacją sieci Web, nazwę DNS musi być zarejestrowana w ramach niestandardowej nazwy domeny dla aplikacji. Podczas dodawania punktu końcowego aplikacji sieci Web jako punkt końcowy platformy Azure, nazwę DNS profilu usługi Traffic Manager jest automatycznie rejestrowane dla aplikacji. Rejestracja zostanie automatycznie usunięta po usunięciu punktu końcowego.
3. Każdy profil usługi Traffic Manager może mieć co najwyżej jeden końcowy aplikacji sieci Web z każdego regionu platformy Azure. Aby obejść to ograniczenie, można skonfigurować aplikację sieci Web jako zewnętrzny punkt końcowy. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Włączanie i wyłączanie punktów końcowych

Wyłączenie punktu końcowego w usłudze Traffic Manager może być przydatna do tymczasowego usuwania ruch punktu końcowego, który znajduje się w trybie konserwacji lub ponownie wdrażany. Gdy punkt końcowy zostanie ponownie uruchomiona, można ją ponownie włączyć.

Punkty końcowe włączone i wyłączone w portalu usługi Traffic Manager, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST.

> [!NOTE]
> Wyłączenie punktu końcowego platformy Azure nie ma nic wspólnego z jego stanem wdrożenia na platformie Azure. Usługi platformy Azure (takie jak aplikacją sieci Web lub maszyna wirtualna pozostaje uruchomiony i będzie mógł odbierać ruch nawet po wyłączeniu w usłudze Traffic Manager. Ruch może zostać zlikwidowane bezpośrednio do wystąpienia usługi, a nie za pomocą nazwy DNS profilu usługi Traffic Manager. Aby uzyskać więcej informacji, zobacz [jak działa usługa Traffic Manager](traffic-manager-how-it-works.md).

Bieżący uprawnień każdy punkt końcowy do odbierania ruchu zależy od następujących czynników:

* Stan profilu (włączone/wyłączone)
* Stan punktu końcowego (włączone/wyłączone)
* Wyniki kondycji sprawdza, czy dla tego punktu końcowego

Aby uzyskać więcej informacji, zobacz [monitorowania punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Ponieważ usługa Traffic Manager działa na poziomie usługi DNS, nie może mieć wpływ na istniejące połączenia do dowolnego punktu końcowego. Gdy punkt końcowy jest niedostępny, usługa Traffic Manager kieruje nowe połączenia do innego dostępnego punktu końcowego. Jednak hosta za wyłączony lub w złej kondycji punktu końcowego może nadal odbierać ruchu za pośrednictwem istniejących połączeń, do czasu jej zakończenia są tymi sesjami. Aplikacje, należy ograniczyć czas trwania sesji, aby zezwolić na ruch opróżnić z istniejących połączeń.

Jeśli wszystkie punkty końcowe w profilu są wyłączone lub profil, który sam jest wyłączona, usługa Traffic Manager wysyła odpowiedź "NXDOMAIN" do nowej kwerendy DNS.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [jak działa usługa Traffic Manager](traffic-manager-how-it-works.md).
* Dowiedz się więcej o usłudze Traffic Manager [punktu końcowego monitorowaniu i automatycznemu trybu failover](traffic-manager-monitoring.md).
* Dowiedz się więcej o usłudze Traffic Manager [metody routingu ruchu](traffic-manager-routing-methods.md).
