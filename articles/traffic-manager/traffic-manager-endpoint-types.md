---
title: Typy punktów końcowych usługi Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano różne typy punktów końcowych, które mogą być używane z usługą Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: 792712e3e529d77ff20a7603b5fbf028ca60f8c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877517"
---
# <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager
Menedżer ruchu Microsoft Azure umożliwia kierowanie ruchu sieciowego do wdrożeń aplikacji uruchomionych w różnych centrach danych. Skonfiguruj każdego wdrożenia aplikacji jako punktu końcowego w usłudze Traffic Manager. Menedżer ruchu odebrania żądania DNS wybiera dostępnego punktu końcowego, aby powrócić do odpowiedzi DNS. Menedżer ruchu Określa wybór na bieżący stan punktu końcowego i metody routingu ruchu. Aby uzyskać więcej informacji, zobacz [sposób działania usługi Traffic Manager](traffic-manager-how-traffic-manager-works.md).

Istnieją trzy typy punktu końcowego obsługiwane przez usługę Traffic Manager:
* **Punkty końcowe systemu Azure** są używane dla usługi hostowanej na platformie Azure.
* **Zewnętrzne punkty końcowe** są używane poza Azure, lokalnie hostowanych usług lub z innym dostawcą hostingu.
* **Zagnieżdżone punkty końcowe** są używane do łączenia profilów usługi Traffic Manager do tworzenia bardziej elastyczne systemy routingu ruchu do obsługi na potrzeby wdrożeń większych i bardziej złożonych.

Żadne ograniczenia nie są w sposób punkty końcowe o różnych typach są połączone w jeden profil Menedżera ruchu. Każdy profil może zawierać żadnych mieszane typy punktu końcowego.

W poniższych sekcjach opisano każdy typ punktu końcowego szczegółowo.

## <a name="azure-endpoints"></a>Punkty końcowe systemu Azure

Punkty końcowe systemu Azure są używane dla usług opartych na platformie Azure w usłudze Traffic Manager. Obsługiwane są następujące typy zasobów platformy Azure:

* Usługi w chmurze "Klasycznym" maszynom wirtualnym IaaS i PaaS.
* Web Apps
* Zasoby publicznego adresu IP (które mogą być podłączone do maszyn wirtualnych bezpośrednio lub za pośrednictwem usługi równoważenia obciążenia Azure). Publicznego adresu IP musi mieć nazwę DNS przypisane do użycia w profilu usługi Traffic Manager.

Zasoby publicznego adresu IP są zasoby usługi Azure Resource Manager. Nie istnieją w klasycznym modelu wdrażania. W związku z tym są tylko obsługiwane w ruchu Menedżera środowiska usługi Azure Resource Manager. Inne typy punktu końcowego są obsługiwane za pośrednictwem zarówno Resource Manager i klasycznym modelu wdrażania.

Używając punkty końcowe systemu Azure Traffic Manager wykrywa maszyn wirtualnych IaaS 'Klasycznym', usługa w chmurze lub aplikacji sieci Web jest zatrzymana i uruchomiona. Ten stan jest widoczny w stan punktu końcowego. Zobacz [monitorowania punktów końcowych usługi Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) szczegółowe informacje. Po zatrzymaniu podległej usłudze Traffic Manager wykonuje kontroli kondycji punktu końcowego lub bezpośredniego ruchu do punktu końcowego. Brak rozliczeń zdarzenia występują dla zatrzymanego wystąpienia menedżera ruchu. Po ponownym uruchomieniu usługi, wznawia rozliczeń i punktu końcowego nie kwalifikuje się do odbierania ruchu. Wykrywanie nie ma zastosowania do punktów końcowych publicznego adresu IP.

## <a name="external-endpoints"></a>Zewnętrzne punkty końcowe

Zewnętrzne punkty końcowe są używane w przypadku usług poza platformą Azure. Na przykład usługi hostowane lokalnie lub za pomocą innego dostawcy. Zewnętrzne punkty końcowe można użyć pojedynczo lub łączyć się z punktami końcowymi Azure w ten sam profil Menedżera ruchu. Łączenie punkty końcowe systemu Azure z zewnętrzne punkty końcowe umożliwia różne scenariusze:

* W obu aktywny aktywny lub aktywny / pasywny model umożliwia uzyskanie zwiększenia nadmiarowości dla istniejącej lokalnej aplikacji Azure.
* Aby zmniejszyć opóźnienia aplikacji dla użytkowników na całym świecie, Rozszerz istniejącą aplikację lokalną do dodatkowej lokalizacji geograficznych na platformie Azure. Aby uzyskać więcej informacji, zobacz [Menedżera ruchu "Performance" routingu ruchu](traffic-manager-routing-methods.md#performance).
* Użyj Azure, aby zapewnić jej większą pojemność dla istniejącej lokalnej aplikacji, stale lub jako rozwiązanie "serii chmurą" Aby spełnić kolekcji w żądanie.

W niektórych przypadkach warto użyć zewnętrzne punkty końcowe usług Azure odwołanie do (przykłady można znaleźć [— często zadawane pytania](traffic-manager-faqs.md#traffic-manager-endpoints)). W takim przypadku kontroli kondycji są rozliczane według stawki punkty końcowe systemu Azure, nie szybkość zewnętrzne punkty końcowe. Jednak w odróżnieniu od punkty końcowe systemu Azure, jeśli zatrzymać lub usunąć podległej usłudze kondycji Sprawdź rozliczeń kontynuowany do momentu wyłączenia lub usunąć punktu końcowego w usłudze Traffic Manager.

## <a name="nested-endpoints"></a>Zagnieżdżone punkty końcowe

Punkty końcowe zagnieżdżonych łączyć wiele profilów usługi Traffic Manager do tworzenia elastycznych systemów routingu ruchu i obsługiwać potrzeby większych i złożone wdrożenia. Z punktami końcowymi zagnieżdżone profilu 'child' jest dodawana jako punkt końcowy do profilu "parent". Profile zarówno nadrzędnym a podrzędnym może zawierać innych punktów końcowych dowolnego typu, łącznie z innych zagnieżdżonych profilów. Aby uzyskać więcej informacji, zobacz [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps jako punkty końcowe

Dodatkowe zagadnienia do rozważenia podczas konfigurowania aplikacji sieci Web jako punkty końcowe w usłudze Traffic Manager:

1. Tylko aplikacje sieci Web w wersji "Standard" lub nowszym kwalifikują się do użycia z usługą Traffic Manager. Próby dodania aplikacji sieci Web z niższym jednostki SKU zakończyć się niepowodzeniem. Zmiana wersji na starszą jednostki SKU istniejącej aplikacji sieci Web powoduje w usłudze Traffic Manager nie będzie wysyłać ruch do tej aplikacji sieci Web.
2. Gdy punkt końcowy otrzymuje żądania HTTP, używa nagłówka "host" w żądaniu ustalenie powinny zrealizować żądania, która aplikacja sieci Web. Nagłówek hosta zawiera nazwę DNS używane do inicjowania żądania, na przykład "contosoapp.azurewebsites.net". Aby użyć innej nazwy DNS z aplikacji sieci Web, nazwa DNS musi być zarejestrowany jako niestandardowej nazwy domeny dla aplikacji. Dodając punkt końcowy aplikacji sieci Web jako punktu końcowego platformy Azure, nazwa DNS profilu Menedżera ruchu jest automatycznie rejestrowane dla aplikacji. Rejestracja zostanie automatycznie usunięta po usunięciu punktu końcowego.
3. Każdy profil Menedżera ruchu może mieć co najwyżej jeden końcowy aplikacji sieci Web w każdym regionie Azure. Aby obejść to ograniczenie, można skonfigurować aplikacji sieci Web jako zewnętrznego punktu końcowego. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Włączanie i wyłączanie punktów końcowych

Wyłączenie punktu końcowego w usłudze Traffic Manager mogą być przydatne do tymczasowo usunąć ruchu z punktu końcowego, który jest w trybie konserwacji lub ponownie wdrażany. Gdy punkt końcowy jest uruchomiona ponownie, można ją ponownie włączyć.

Punkty końcowe może być włączony i wyłączyć za pomocą portalu usługi Traffic Manager, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST, które są obsługiwane w klasycznym modelu wdrażania i Menedżera zasobów.

> [!NOTE]
> Wyłączenie punktu końcowego platformy Azure nie ma nic wspólnego z jego stanem wdrożenia na platformie Azure. Usługa Azure (takich jak maszyny Wirtualnej lub aplikacji sieci Web pozostaje uruchomiona i może odbierać ruch nawet po wyłączeniu w usłudze Traffic Manager. Ruch może zostać zlikwidowane bezpośrednio do wystąpienia usługi, a nie za pomocą nazwy DNS profilu Menedżera ruchu. Aby uzyskać więcej informacji, zobacz [działania Menedżera ruchu](traffic-manager-how-traffic-manager-works.md).

Bieżący kwalifikuje się każdego punktu końcowego na odbieranie ruchu zależy od następujących czynników:

* Stan profilu (włączone/wyłączone)
* Stan punktu końcowego (włączone/wyłączone)
* Sprawdza, czy wyniki kondycję tego punktu końcowego

Aby uzyskać więcej informacji, zobacz [monitorowania punktów końcowych usługi Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Ponieważ Traffic Manager działa na poziomie DNS, nie może mieć wpływ istniejących połączeń z dowolnego punktu końcowego. Gdy punkt końcowy jest niedostępny, usługi Traffic Manager kieruje nowych połączeń z innym punktem końcowym dostępne. Jednak hosta za wyłączone lub nieprawidłowości punktem końcowym mogą w dalszym ciągu odbieranie ruchu za pośrednictwem istniejących połączeń, dopóki nie kończą się tymi sesjami. Aplikacje należy ograniczyć czas trwania sesji, aby zezwolić na ruch do opróżnienia z istniejących połączeń.

Wyłączenie wszystkich punktów końcowych w profilu lub jest wyłączona na profil, Traffic Manager wysyła odpowiedź "NXDOMAIN" do nowego zapytania DNS.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się [działania Menedżera ruchu](traffic-manager-how-traffic-manager-works.md).
* Dowiedz się więcej o Menedżerze ruchu [punkt końcowy monitorowania i automatycznej pracy awaryjnej](traffic-manager-monitoring.md).
* Dowiedz się więcej o Menedżerze ruchu [metody routingu ruchu](traffic-manager-routing-methods.md).
