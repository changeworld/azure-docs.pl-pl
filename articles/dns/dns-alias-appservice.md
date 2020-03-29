---
title: Hostuj aplikacje internetowe platformy Azure z równoważenia obciążenia w strefie wierzchołka strefy
description: Używanie rekordu aliasu DNS platformy Azure do hosta aplikacji sieci web z równoważenia obciążenia w wierzchołku strefy
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937367"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hostuj aplikacje internetowe platformy Azure z równoważenia obciążenia w strefie wierzchołka strefy

Protokół DNS uniemożliwia przypisanie niczego innego niż rekord A lub AAAA w wierzchołku strefy. Przykładowy wierzchołek strefy jest contoso.com. To ograniczenie stanowi problem dla właścicieli aplikacji, którzy mają aplikacje z równoważeniem obciążenia za Usługą Traffic Manager. Nie można wskakiwać profilu usługi Traffic Manager z rekordu wierzchołka strefy. W rezultacie właściciele aplikacji muszą używać obejścia problemu. Przekierowanie w warstwie aplikacji musi zostać przekierowane z wierzchołka strefy do innej domeny. Przykładem jest przekierowanie z contoso.com\.do contoso.com www. Ten układ przedstawia pojedynczy punkt awarii dla funkcji przekierowania.

W rekordach aliasu ten problem już nie istnieje. Teraz właściciele aplikacji można skierować ich rekord apex strefy do profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe. Właściciele aplikacji mogą wskazywać ten sam profil usługi Traffic Manager, który jest używany dla dowolnej innej domeny w strefie DNS.

Na przykład contoso.com i www\.contoso.com mogą wskazywać ten sam profil usługi Traffic Manager. Dzieje się tak długo, jak profil usługi Traffic Manager ma skonfigurowane tylko zewnętrzne punkty końcowe.

W tym artykule dowiesz się, jak utworzyć rekord aliasu dla wierzchołka domeny i skonfigurować punkty końcowe profilu usługi Traffic Manager dla aplikacji sieci Web.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje dotyczące hostowania własnej domeny w usłudze Azure DNS, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

Przykładowa domena używana w tym samouczku to contoso.com, ale skorzystaj z własnej nazwy domeny.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, aby pomieścić wszystkie zasoby użyte w tym artykule.

## <a name="create-app-service-plans"></a>Tworzenie planów usługi app service

Utwórz dwa plany usługi aplikacji sieci Web w grupie zasobów, korzystając z poniższej tabeli informacji o konfiguracji. Aby uzyskać więcej informacji na temat tworzenia planu usługi app service, zobacz [Zarządzanie planem usługi app service na platformie Azure](../app-service/app-service-plan-manage.md).


|Nazwa  |System operacyjny  |Lokalizacja  |Warstwa cenowa  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Wschodnie stany USA|Dev/Test D1-Shared|
|ASP-02     |Windows|Środkowe stany USA|Dev/Test D1-Shared|

## <a name="create-app-services"></a>Tworzenie usług aplikacji

Utwórz dwie aplikacje internetowe, po jednej w każdym planie usługi app service.

1. W lewym górnym rogu strony portalu Azure wybierz pozycję **Utwórz zasób**.
2. Wpisz **aplikację sieci Web** na pasku wyszukiwania i naciśnij klawisz Enter.
3. Wybierz **aplikację sieci Web**.
4. Wybierz **pozycję Utwórz**.
5. Zaakceptuj ustawienia domyślne i użyj poniższej tabeli, aby skonfigurować dwie aplikacje internetowe:

   |Nazwa<br>(musi być unikatowa w ciągu .azurewebsites.net)|Grupa zasobów |Stos środowiska uruchomieniowego|Region|Plan/lokalizacja usługi aplikacji
   |---------|---------|-|-|-------|
   |Aplikacja-01|Użyj istniejącego<br>Wybieranie grupy zasobów|.NET Core 2.2|Wschodnie stany USA|ASP-01(D1)|
   |Aplikacja-02|Użyj istniejącego<br>Wybieranie grupy zasobów|.NET Core 2.2|Środkowe stany USA|ASP-02(D1)|

### <a name="gather-some-details"></a>Zbierz kilka szczegółów

Teraz musisz zanotować adres IP i nazwę hosta dla aplikacji internetowych.

1. Otwórz grupę zasobów i wybierz pierwszą aplikację internetową **(App-01** w tym przykładzie).
2. W lewej kolumnie wybierz pozycję **Właściwości**.
3. Zanotuj adres w obszarze **ADRES URL**i w obszarze **Wychodzące adresy IP** zanotuj pierwszy adres IP na liście. Te informacje będą używane później podczas konfigurowania punktów końcowych usługi Traffic Manager.
4. Powtórz dla **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager w grupie zasobów. Użyj ustawień domyślnych i wpisz unikatową nazwę w trafficmanager.net przestrzeni nazw.

Aby uzyskać informacje dotyczące tworzenia profilu usługi Traffic Manager, zobacz [Szybki start: Tworzenie profilu usługi Traffic Manager dla aplikacji sieci Web o wysokiej dostępności](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Tworzenie punktów końcowych

Teraz można utworzyć punkty końcowe dla dwóch aplikacji sieci web.

1. Otwórz grupę zasobów i wybierz profil Usługi Traffic Manager.
2. W lewej kolumnie wybierz pozycję **Punkty końcowe**.
3. Wybierz pozycję **Dodaj**.
4. Aby skonfigurować punkty końcowe, użyj poniższej tabeli:

   |Typ  |Nazwa  |Środowisko docelowe  |Lokalizacja  |Niestandardowe ustawienia nagłówka|
   |---------|---------|---------|---------|---------|
   |Zewnętrzny punkt końcowy     |Koniec 01|Adres IP zarejestrowany dla aplikacji App-01|Wschodnie stany USA|host:\<adres URL zarejestrowany dla aplikacji-01\><br>Przykład: **host:01.azurewebsites.net aplikacji**|
   |Zewnętrzny punkt końcowy     |Koniec 02|Adres IP zarejestrowany dla app-02|Środkowe stany USA|host:\<adres URL zarejestrowany dla aplikacji-02\><br>Przykład: **host:02.azurewebsites.net aplikacji**

## <a name="create-dns-zone"></a>Tworzenie strefy DNS

Do testowania można użyć istniejącej strefy DNS lub utworzyć nową strefę. Aby utworzyć i delegować nową strefę DNS na platformie Azure, zobacz [Samouczek: Hostuj swoją domenę w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Dodawanie rekordu TXT do sprawdzania poprawności domeny niestandardowej

Po dodaniu niestandardowej nazwy hosta do aplikacji internetowych zostanie wyszukany określony rekord TXT, aby zweryfikować domenę.

1. Otwórz grupę zasobów i wybierz strefę DNS.
2. Wybierz pozycję **Zestaw rekordów**.
3. Dodaj zestaw rekordów, korzystając z poniższej tabeli. Dla tej wartości użyj rzeczywistego adresu URL aplikacji internetowej, który został wcześniej zarejestrowany:

   |Nazwa  |Typ  |Wartość|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Dodawanie domeny niestandardowej

Dodaj domenę niestandardową dla obu aplikacji internetowych.

1. Otwórz grupę zasobów i wybierz pierwszą aplikację internetową.
2. W lewej kolumnie wybierz pozycję **Domeny niestandardowe**.
3. W obszarze **Domeny niestandardowe**wybierz pozycję **Dodaj domenę niestandardową**.
4. W obszarze **Domena niestandardowa**wpisz swoją niestandardową nazwę domeny. Na przykład contoso.com.
5. Wybierz przycisk **Weryfikuj**.

   Domena powinna przejść weryfikację i wyświetlić zielone znaczniki wyboru obok **pozycji Dostępność nazwy hosta** i **Własność domeny**.
5. Wybierz **pozycję Dodaj domenę niestandardową**.
6. Aby wyświetlić nową nazwa hosta w obszarze **Nazwy hostów przypisane do witryny,** odśwież przeglądarkę. Odświeżanie na stronie nie zawsze pokazuje zmiany od razu.
7. Powtórz tę procedurę dla drugiej aplikacji internetowej.

## <a name="add-the-alias-record-set"></a>Dodawanie zestawu rekordów aliasu

Teraz dodaj rekord aliasu dla wierzchołka strefy.

1. Otwórz grupę zasobów i wybierz strefę DNS.
2. Wybierz pozycję **Zestaw rekordów**.
3. Dodaj zestaw rekordów, korzystając z poniższej tabeli:

   |Nazwa  |Typ  |Zestaw rekordów aliasu  |Typ aliasu  |Zasób platformy Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Tak|Zasób platformy Azure|Traffic Manager - Twój profil|


## <a name="test-your-web-apps"></a>Testowanie aplikacji internetowych

Teraz możesz przetestować, aby upewnić się, że możesz dotrzeć do aplikacji sieci web i że jest on równoważenia obciążenia.

1. Otwórz przeglądarkę internetową i przejdź do swojej domeny. Na przykład contoso.com. Powinna zostać wyświetlona domyślna strona aplikacji sieci Web.
2. Zatrzymaj swoją pierwszą aplikację internetową.
3. Zamknij przeglądarkę internetową i odczekaj kilka minut.
4. Uruchom przeglądarkę internetową i przejdź do domeny. Domyślna strona aplikacji sieci Web nadal powinna być widoczna.
5. Zatrzymaj drugą aplikację internetową.
6. Zamknij przeglądarkę internetową i odczekaj kilka minut.
7. Uruchom przeglądarkę internetową i przejdź do domeny. Powinien zostać wyświetlony błąd 403, wskazujący, że aplikacja internetowa jest zatrzymana.
8. Uruchom drugą aplikację internetową.
9. Zamknij przeglądarkę internetową i odczekaj kilka minut.
10. Uruchom przeglądarkę internetową i przejdź do domeny. Domyślna strona aplikacji sieci Web powinna zostać ponownie wyświetlona.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rekordach aliasu, zobacz następujące artykuły:

- [Samouczek: Konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure](tutorial-alias-pip.md)
- [Samouczek: konfigurowanie rekordu aliasu w celu obsługi nazw domen wierzchołkowych przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Aby dowiedzieć się, jak przeprowadzić migrację aktywnej nazwy DNS, zobacz [Migrowanie aktywnej nazwy DNS do usługi Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
