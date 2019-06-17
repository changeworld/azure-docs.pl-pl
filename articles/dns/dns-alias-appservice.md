---
title: Hostowanie aplikacji internetowych platformy Azure ze zrównoważonym obciążeniem w wierzchołku strefy
description: Użyj usługi Azure DNS alias rekord hostująca aplikacje internetowe ze zrównoważonym obciążeniem w wierzchołku strefy
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 11/3/2018
ms.author: victorh
ms.openlocfilehash: b08eae072c2fbe420401424baf97a25b4cbbe87b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790746"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hostowanie aplikacji internetowych platformy Azure ze zrównoważonym obciążeniem w wierzchołku strefy

Protokół DNS zapobiega przypisanie coś innego niż rekord A lub AAAA w wierzchołku strefy. Przykład wierzchołku strefy to contoso.com. Ograniczenie to stanowi problem dla właścicieli aplikacji, mających równoważenia obciążenia aplikacji za zaporą usługi Traffic Manager. Nie jest możliwe wskazywała na profil usługi Traffic Manager z rekordu wierzchołku strefy. W wyniku właścicieli aplikacji należy użyć, aby uzyskać obejście tego problemu. Przekierowanie w warstwie aplikacji musi przekierowywać domenę w wierzchołku strefy do innej domeny. Przykładem jest przekierowanie z contoso.com www\.contoso.com. To rozwiązanie przedstawia informacje o pojedynczym punktem awarii dla funkcji przekierowania.

Przy użyciu rekordów aliasów ten problem już nie istnieje. Teraz właścicieli aplikacji można wskazać ich rekordów w wierzchołku strefy profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe. Właściciele aplikacji może wskazywać tego samego profilu usługi Traffic Manager, używanego do innej domeny w swojej strefie DNS.

Na przykład contoso.com i www\.contoso.com może wskazywać tego samego profilu usługi Traffic Manager. Jest to możliwe tak długo, jak profil usługi Traffic Manager ma tylko zewnętrzne punkty końcowe skonfigurowane.

W tym artykule dowiesz się, jak utworzyć rekord aliasu dla języka apex Twojej domeny i skonfigurować punkty końcowe profilu usługi Traffic Manager, tak dla aplikacji sieci web.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje, Hostuj swoją domenę, w usłudze Azure DNS, zobacz [samouczka: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

Przykładowa domena używana w tym samouczku to contoso.com, ale skorzystaj z własnej nazwy domeny.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów zawierającą wszystkie zasoby, które są używane w tym artykule.

## <a name="create-app-service-plans"></a>Tworzenie planów usługi App Service

Utwórz dwa plany usługi aplikacji sieci Web w swojej grupie zasobów, aby uzyskać informacje o konfiguracji przy użyciu poniższej tabeli. Aby uzyskać więcej informacji na temat tworzenia planu usługi App Service, zobacz [Zarządzanie plan usługi App Service na platformie Azure](../app-service/app-service-plan-manage.md).


|Name (Nazwa)  |System operacyjny  |Lokalizacja  |Warstwa cenowa  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Wschodnie stany USA|Tworzenie i testowanie udostępnione D1|
|ASP-02     |Windows|Środkowe stany USA|Tworzenie i testowanie udostępnione D1|

## <a name="create-app-services"></a>Tworzenie usługi App Services

Utworzyć dwie aplikacje internetowe, po jednym w każdym planie usługi App Service.

1. W lewym górnym rogu strony Azure portal, kliknij polecenie **Utwórz zasób**.
2. Typ **aplikacji sieci Web** w pasku wyszukiwania i naciśnij klawisz Enter.
3. Kliknij przycisk **aplikacja sieci Web**.
4. Kliknij pozycję **Utwórz**.
5. Zaakceptuj ustawienia domyślne, a następnie skorzystaj z poniższej tabeli, aby skonfigurować dwóch aplikacji sieci web:

   |Name (Nazwa)<br>(musi być unikatowa w obrębie. azurewebsites.net)|Grupa zasobów |Plan usługi App Service/lokalizacja
   |---------|---------|---------|
   |App-01|Użyj istniejącej<br>Wybieranie grupy zasobów|ASP-01(East US)|
   |App-02|Użyj istniejącej<br>Wybieranie grupy zasobów|ASP 02(Central US)|

### <a name="gather-some-details"></a>Zbierz niektóre szczegóły

Teraz należy pamiętać, nazwa hosta i adres IP dla aplikacji.

1. Otwórz grupę zasobów, a następnie kliknij przycisk swoją pierwszą aplikację (**App-01** w tym przykładzie).
2. W lewej kolumnie kliknij **właściwości**.
3. Zanotuj adres w ramach **adresu URL**, a następnie w obszarze **wychodzące adresy IP** należy zwrócić uwagę, pierwszego adresu IP na liście. Użyjesz tych informacji później podczas konfigurowania punktów końcowych usługi Traffic Manager.
4. Powtórz tę procedurę dla **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager w grupie zasobów. Użyj ustawień domyślnych, a następnie wpisz unikatową nazwę obszaru nazw trafficmanager.net.

Aby uzyskać informacje dotyczące tworzenia profilu usługi Traffic Manager, zobacz [Szybki Start: Tworzenie profilu usługi Traffic Manager dla aplikacji sieci web o wysokiej dostępności](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Tworzenie punktów końcowych

Teraz możesz utworzyć punkty końcowe dla dwóch aplikacji sieci web.

1. Otwórz grupę zasobów i kliknij swój profil usługi Traffic Manager.
2. W lewej kolumnie kliknij **punktów końcowych**.
3. Kliknij pozycję **Add** (Dodaj).
4. Skorzystaj z poniższej tabeli, aby skonfigurować punkty końcowe:

   |Typ  |Name (Nazwa)  |Cel  |Lokalizacja  |Ustawienia niestandardowego nagłówka|
   |---------|---------|---------|---------|---------|
   |Jest zewnętrzny punkt końcowy     |End-01|Adres IP, który jest zarejestrowany dla programu App-01|Wschodnie stany USA|Host:\<adres URL dla programu App-01\><br>Przykład: **hosta: aplikacja-01.azurewebsites.net**|
   |Jest zewnętrzny punkt końcowy     |End-02|Adres IP, który jest zarejestrowany dla programu App-02|Środkowe stany USA|Host:\<adres URL dla programu App-02\><br>Przykład: **hosta: aplikacja-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Tworzenie strefy DNS

Możesz użyć istniejącej strefy DNS dla badania, lub można utworzyć nowej strefy. Aby utworzyć i przekazać nowej strefy DNS na platformie Azure, zobacz [samouczka: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Dodaj zestaw rekordów aliasów

Gdy strefy DNS jest gotowy, możesz dodać rekord aliasu dla wierzchołku strefy.

1. Otwórz grupę zasobów, a następnie kliknij strefę DNS.
2. Kliknij pozycję **Zestaw rekordów**.
3. Dodaj rekord, można ustawić przy użyciu poniższej tabeli:

   |Name (Nazwa)  |Typ  |Alias record set  |Typ aliasu  |Zasób platformy Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Yes|Zasób platformy Azure|Traffic Manager — swój profil|

## <a name="add-custom-hostnames"></a>Dodaj niestandardowe nazwy hostów

Dodaj niestandardową nazwę hosta dla obu aplikacji sieci web.

1. Otwórz grupę zasobów i kliknij swoją pierwszą aplikację sieci web.
2. W lewej kolumnie kliknij **domen niestandardowych**.
3. Kliknij przycisk **Dodaj nazwę hosta**.
4. W obszarze nazwy hosta wpisz nazwę domeny. Na przykład "contoso.com".

   Domenę należy przekazać sprawdzania poprawności i Pokaż zielone znaczniki obok **dostępność nazwy hosta** i **własność domeny**.
5. Kliknij przycisk **Dodaj nazwę hosta**.
6. Aby wyświetlić nowe nazwy hosta w obszarze **nazwy hostów przypisane do lokacji**, Odśwież przeglądarkę. Odświeżanie na stronie nie zawsze są wyświetlane zmiany natychmiast.
7. Powtórz tę procedurę dla drugiej aplikacji sieci web.

## <a name="test-your-web-apps"></a>Testowanie aplikacji sieci web

Teraz możesz sprawdzić, upewnij się, że możesz połączyć aplikację sieci web i że jego obciążenia zrównoważone.

1. Otwórz przeglądarkę internetową i przejdź do domeny. Na przykład "contoso.com". Powinna zostać wyświetlona strona aplikacji sieci web domyślne.
2. Zatrzymaj swoją pierwszą aplikację sieci web.
3. Zamknij przeglądarkę sieci web, a następnie zaczekaj kilka minut.
4. Uruchom przeglądarkę sieci web i przejdź do domeny. Zobaczysz powinny nadal domyślną stronę aplikacji sieci web.
5. Zatrzymaj drugiej aplikacji sieci web.
6. Zamknij przeglądarkę sieci web, a następnie zaczekaj kilka minut.
7. Uruchom przeglądarkę sieci web i przejdź do domeny. Powinien zostać wyświetlony błąd 403, wskazujący, że aplikacja sieci web została zatrzymana.
8. Uruchom aplikację sieci web w drugim.
9. Zamknij przeglądarkę sieci web, a następnie zaczekaj kilka minut.
10. Uruchom przeglądarkę sieci web i przejdź do domeny. Należy ponownie wyświetlić domyślną stronę aplikacji sieci web.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rekordów aliasów, zobacz następujące artykuły:

- [Samouczek: Konfigurowanie rekordu aliasu do odwoływania się do platformy Azure, publiczny adres IP](tutorial-alias-pip.md)
- [Samouczek: Konfigurowanie rekordu aliasu do obsługi języka apex nazw domen przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
