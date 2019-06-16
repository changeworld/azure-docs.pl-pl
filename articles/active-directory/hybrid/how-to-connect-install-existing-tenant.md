---
title: 'Program Azure AD Connect: Jeśli masz już usługę Azure AD | Dokumentacja firmy Microsoft'
description: W tym temacie opisano sposób użycia Connect, jeśli masz istniejącą dzierżawę usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1495c14ae4c588661452aa3696019da00be47548
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571378"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Program Azure AD Connect: Jeśli masz dzierżawę celowe
Większość tematów w zakresie używania usługi Azure AD Connect przyjęto założenie, rozpoczynać się nowej usługi Azure AD dzierżawy i się, że żadni użytkownicy ani innych obiektów. Ale jeśli rozpoczęto za pomocą dzierżawy usługi Azure AD wypełnić go użytkowników i innych obiektów, a teraz chcesz użyć Connect, a następnie w tym temacie jest dla Ciebie.

## <a name="the-basics"></a>Podstawy
Obiekt w usłudze Azure AD jest albo zarządzanych lub w chmurze (Azure AD) w środowisku lokalnym. Jeden pojedynczy obiekt nie możesz zarządzać niektóre atrybuty w środowisku lokalnym i niektóre inne atrybuty w usłudze Azure AD. Każdy obiekt ma flagę wskazującą, w którym odbywa się obiekt.

W chmurze, możesz zarządzać niektórych użytkowników lokalnych i innych. Typowy scenariusz w przypadku tej konfiguracji to organizacji z różnych kont pracowników i pracowników sprzedaży. Kont pracowników ma lokalne konto usługi AD, ale pracownicy sprzedaży nie, użytkownicy mają konta w usłudze Azure AD. Może zarządzać niektórych użytkowników w środowisku lokalnym, a niektóre w usłudze Azure AD.

Jeśli rozpoczęto do zarządzania użytkownikami w usłudze Azure AD, które są również w lokalnej usługi AD i później chcesz użyć Connect, istnieją pewne dodatkowe kwestie, które należy wziąć pod uwagę.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizacja istniejących użytkowników w usłudze Azure AD
Podczas instalowania programu Azure AD Connect i rozpoczęciem tej synchronizacji, usługa synchronizacji Azure AD (w usłudze Azure AD) sprawdza każdy nowy obiekt i spróbuj znaleźć istniejącego obiektu do dopasowania. Istnieją trzy atrybuty używane dla tego procesu: **userPrincipalName**, **proxyAddresses**, i **sourceAnchor**/**immutableID** . Dopasowanie w **userPrincipalName** i **proxyAddresses** jest znany jako **dopasowanie słabe**. Dopasowanie w **sourceAnchor** jest znany jako **twardych dopasowanie**. Dla **proxyAddresses** tylko wartość za pomocą atrybutu **SMTP:** , która jest podstawowym adresem e-mail, jest używana do oceny.

Dopasowanie jest oceniane tylko dla nowych obiektów pochodzące z witryny Connect. Jeśli zmienisz istniejącego obiektu, dzięki czemu jest on zgodny te atrybuty, następnie zostanie wyświetlony błąd zamiast tego.

Jeśli usługa Azure AD wykryje obiekt, w którym wartości atrybutów są takie same dla obiektu pochodzące z witryny Connect i który znajduje się już w usłudze Azure AD, następnie obiektu w usłudze Azure AD zostanie przejęty przez połączenie. Obiekt wcześniej zarządzane w chmurze jest oznaczone jako zarządzane lokalnie. Wszystkie atrybuty w usłudze Azure AD z wartością w lokalnym programie AD zostaną zastąpione wartością w środowisku lokalnym. Wyjątek występuje podczas ma atrybut **NULL** wartość w środowisku lokalnym. W tym przypadku wartość pozostaje w usłudze Azure AD, ale można nadal zmienić tylko w środowisku lokalnym na inny.

> [!WARNING]
> Ponieważ wszystkie atrybuty w usłudze Azure AD mają zostać zastąpione przez wartość w środowisku lokalnym, upewnij się, że dysponujesz aktualnymi danymi w środowisku lokalnym. Na przykład jeśli można tylko zarządzane adres e-mail w usłudze Office 365, a nie przechowywane zaktualizowane w lokalnych usług AD DS, a następnie utracisz wszystkie wartości w usłudze Azure AD/usługi Office 365 nie znajduje się w usługach AD DS.

> [!IMPORTANT]
> Jeśli używasz synchronizacji haseł, które jest używane przez ustawienia ekspresowe, a następnie hasła w usłudze Azure AD jest zastępowany przy użyciu hasła w lokalnym programie AD. Jeśli użytkownicy są używane do zarządzania różne hasła, należy z informacją o tym, że powinny używać lokalnego hasła po zainstalowaniu Connect.

W procesie planowania należy rozważyć poprzedniej sekcji i ostrzeżenia. Jeśli wprowadzono wiele zmian w usłudze Azure AD, nie zostaną uwzględnione w lokalnych usługach AD DS, należy planowanie sposobu wypełniania usług AD DS z zaktualizowanymi wartościami przed zsynchronizowaniem obiektów za pomocą usługi Azure AD Connect.

Jeśli spełnione obiektów przy użyciu opcji soft-match, a następnie **sourceAnchor** są dodawane do obiektu w usłudze Azure AD, dzięki czemu można następnie używać twardych dopasowania.

>[!IMPORTANT]
> Firma Microsoft zaleca się przed synchronizowanie lokalnych kont za pomocą istniejących kont administracyjnych w usłudze Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Hard-match vs Soft-match
Do nowej instalacji programu Connect nie ma praktyczne różnic między soft - i twardych dopasowania. Różnica polega na w sytuacji odzyskiwania po awarii. W przypadku utraty serwera z usługą Azure AD Connect, można ponownie zainstalować nowe wystąpienie bez utraty danych. Obiekt o sourceAnchor są wysyłane do programu Connect podczas początkowej instalacji. Dopasowanie można następnie oceniany przez klienta (Azure AD Connect), który jest znacznie szybsze niż ten sam w usłudze Azure AD. Twarde dopasowanie jest oceniany zarówno przy użyciu Connect, jak i przez usługę Azure AD. Dopasowanie słabe jest oceniane tylko przez usługę Azure AD.

### <a name="other-objects-than-users"></a>Innych obiektów niż użytkownicy
Grupy obsługujące pocztę i kontakty możesz można soft-match na podstawie proxyAddresses. Twarde dopasowanie nie ma zastosowania, ponieważ można aktualizować tylko sourceAnchor/wartość immutableID (przy użyciu programu PowerShell) dotyczące użytkowników tylko. Dla grup, które nie są z włączoną obsługą poczty obecnie nie jest obsługiwane dla miękkiego lub twardych dopasowania.

### <a name="admin-role-considerations"></a>Zagadnienia dotyczące roli administratora
Aby uniemożliwić niezaufanym lokalnych użytkowników pasującej do użytkownika chmury, który ma dowolnej roli administratora, program Azure AD Connect nie będą zgodne obiekty użytkowników lokalnych z obiektami, które ma rolę administratora. Jest to domyślnie. Aby obejść ten problem, możesz skorzystać z poniższej procedury:

1.  Usuwanie ról w katalogu z obiektu użytkowników tylko w chmurze
2.  Wyzwalanie synchronizacji
3.  Opcjonalnie Dodaj role katalogu do obiektu użytkownika w chmurze po wystąpiło dopasowanie.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Tworzenie nowej lokalnej usługi Active Directory z danych w usłudze Azure AD
Niektórzy klienci rozpoczynać się tylko na chmurze rozwiązania z usługą Azure AD i nie mają lokalnej usługi AD. Później chcą używać zasobów lokalnych i chcesz utworzyć lokalną AD oparte na danych usługi Azure AD. Program Azure AD Connect nie może pomóc w przypadku tego scenariusza. Nie powoduje utworzenia użytkowników w środowisku lokalnym i nie ma żadnych możliwość ustawiania hasła lokalnego do tej samej, tak jak w usłudze Azure AD.

Jeśli Jedyny przypadek, dlaczego, do którego planujesz dodać lokalnej usługi AD służy do obsługi obiektów LOB (aplikacje Line-of-Business), a następnie może warto użyć [usługi domenowe Azure AD](../../active-directory-domain-services/index.yml) zamiast tego.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
