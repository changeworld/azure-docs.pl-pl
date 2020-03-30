---
title: 'Usługa Azure AD Connect: gdy masz już usługę Azure AD | Dokumenty firmy Microsoft'
description: W tym temacie opisano sposób korzystania z connect, gdy masz istniejącą dzierżawę usługi Azure AD.
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
ms.openlocfilehash: 3636b88b14cf7e76e4fb023434316e7ee31ded04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71336820"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Usługa Azure AD Connect: gdy masz istniejącą dzierżawę
Większość tematów dotyczących korzystania z usługi Azure AD Connect zakłada, że zaczynasz od nowej dzierżawy usługi Azure AD i że nie ma tam żadnych użytkowników ani innych obiektów. Ale jeśli rozpoczęto od dzierżawy usługi Azure AD, wypełniłeś ją użytkownikami i innymi obiektami, a teraz chcesz użyć connect, ten temat jest dla Ciebie.

## <a name="the-basics"></a>Podstawy
Obiekt w usłudze Azure AD jest opanowany w chmurze (Azure AD) lub lokalnie. W przypadku jednego obiektu nie można zarządzać niektórymi atrybutami lokalnymi i innymi atrybutami w usłudze Azure AD. Każdy obiekt ma flagę wskazującą, gdzie obiekt jest zarządzany.

Możesz zarządzać niektórymi użytkownikami lokalnie i innymi w chmurze. Typowym scenariuszem dla tej konfiguracji jest organizacja z mieszanką pracowników księgowych i pracowników sprzedaży. Pracownicy księgowi mają lokalne konto usługi AD, ale pracownicy sprzedaży nie mają konta w usłudze Azure AD. Można zarządzać niektórymi użytkownikami lokalnie, a niektóre w usłudze Azure AD.

Jeśli rozpoczęto zarządzanie użytkownikami w usłudze Azure AD, którzy są również w lokalnej usłudze AD, a później chcesz używać connect, istnieją pewne dodatkowe problemy, które należy wziąć pod uwagę.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizacja z istniejącymi użytkownikami w usłudze Azure AD
Po zainstalowaniu usługi Azure AD Connect i rozpoczęciu synchronizacji usługa synchronizacji usługi Azure AD (w usłudze Azure AD) sprawdza każdy nowy obiekt i próbuje znaleźć istniejący obiekt, który będzie zgodny. Istnieją trzy atrybuty używane dla tego procesu: **userPrincipalName**, **proxyAddresses**i **sourceAnchor**/**immutableID**. Dopasowanie **na userPrincipalName** i **proxyAddresses** jest znany jako **miękkie dopasowanie**. Mecz na **sourceAnchor** jest znany jako **mecz twardy**. Dla **atrybutu proxyAddresses** tylko wartość z **SMTP:**, czyli podstawowy adres e-mail, jest używany do oceny.

Dopasowanie jest oceniane tylko dla nowych obiektów pochodzących z Connect. Jeśli zmienisz istniejący obiekt, tak aby pasował do któregokolwiek z tych atrybutów, zamiast tego zostanie wyświetlony błąd.

Jeśli usługa Azure AD znajdzie obiekt, w którym wartości atrybutów są takie same dla obiektu pochodzącego z connect i że jest już obecny w usłudze Azure AD, obiekt w usłudze Azure AD jest przejęty przez Connect. Wcześniej zarządzany obiekt w chmurze jest oflagowany jako zarządzany lokalnie. Wszystkie atrybuty w usłudze Azure AD z wartością w lokalnej usłudze AD są zastępowane wartością lokalną. Wyjątek występuje, gdy atrybut ma wartość **NULL** w środowisku lokalnym. W takim przypadku wartość w usłudze Azure AD pozostaje, ale nadal można zmienić tylko lokalnie na coś innego.

> [!WARNING]
> Ponieważ wszystkie atrybuty w usłudze Azure AD zostaną zastąpione przez wartość lokalną, upewnij się, że masz dobre dane lokalnie. Na przykład jeśli tylko zarządzasz adresem e-mail w usłudze Office 365 i nie był aktualizowany w lokalnych usługach AD DS, utracisz wszystkie wartości w usłudze Azure AD/Office 365, które nie są obecne w usługach AD DS.

> [!IMPORTANT]
> Jeśli używasz synchronizacji hasła, która jest zawsze używana przez ustawienia ekspresowe, hasło w usłudze Azure AD jest zastępowane hasłem w lokalnej usłudze AD. Jeśli użytkownicy są przyzwyczajeni do zarządzania różnymi hasłami, należy poinformować ich, że należy użyć hasła lokalnego po zainstalowaniu Connect.

Poprzednia sekcja i ostrzeżenie muszą być uwzględnione w planowaniu. Jeśli wprowadzono wiele zmian w usłudze Azure AD, które nie zostały odzwierciedlone w lokalnych usługach AD DS, przed synchronizacją obiektów z usługą Azure AD Connect należy zaplanować sposób wypełniania usług AD DS zaktualizowanymi wartościami.

Jeśli dopasowywane obiekty z soft-match, a następnie **sourceAnchor** jest dodawany do obiektu w usłudze Azure AD, więc trudne dopasowanie może być używane później.

>[!IMPORTANT]
> Firma Microsoft zdecydowanie zaleca, aby nie synchronizować kont lokalnych z istniejącymi wcześniej kontami administracyjnymi w usłudze Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Hard-match vs Soft-match
W przypadku nowej instalacji Connect nie ma praktycznej różnicy między miękkim a twardym dopasowaniem. Różnica jest w sytuacji odzyskiwania po awarii. Jeśli serwer został utracony za pomocą usługi Azure AD Connect, można ponownie zainstalować nowe wystąpienie bez utraty żadnych danych. Obiekt z źródłemAnchor jest wysyłany do Connect podczas instalacji początkowej. Dopasowanie może być następnie oceniane przez klienta (Usługi Azure AD Connect), który jest znacznie szybszy niż robi to samo w usłudze Azure AD. Dopasowanie jest oceniane zarówno przez Connect, jak i przez usługę Azure AD. Dopasowanie nietremne jest oceniane tylko przez usługę Azure AD.

### <a name="other-objects-than-users"></a>Inne obiekty niż użytkownicy
W przypadku grup i kontaktów z włączoną obsługą poczty można dopasować program dobierania programów w sposób miękki na podstawie adresów proxy. Dopasowanie na skróty nie ma zastosowania, ponieważ można zaktualizować tylko sourceAnchor/immutableID (przy użyciu programu PowerShell) tylko dla użytkowników. W przypadku grup, które nie są włączone pocztą, obecnie nie ma obsługi meczu miękkiego lub meczu twardego.

### <a name="admin-role-considerations"></a>Zagadnienia dotyczące roli administratora
Aby uniemożliwić niezaufanym użytkownikom lokalnym dopasowywanie się do użytkownika w chmurze, który ma dowolną rolę administratora, usługa Azure AD Connect nie będzie dopasowywać lokalnych obiektów użytkownika do obiektów, które mają rolę administratora. Jest to domyślnie. Aby obejść to zachowanie, można wykonać następujące czynności:

1.  Usuń role katalogu z obiektu użytkownika tylko w chmurze.
2.  Jeśli nie powiodła się próba synchronizacji użytkownika, usunięcie obiektu poddanego kwarantannie w chmurze.
3.  Wyzwalanie synchronizacji.
4.  Opcjonalnie dodaj role katalogu z powrotem do obiektu użytkownika w chmurze po wystąpieniu dopasowania.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Tworzenie nowej lokalnej usługi Active Directory na podstawie danych w usłudze Azure AD
Niektórzy klienci zaczynają od rozwiązania tylko w chmurze z usługą Azure AD i nie mają lokalnej usługi AD. Później chcą korzystać z zasobów lokalnych i chcesz utworzyć lokalną usługę AD na podstawie danych usługi Azure AD. Usługa Azure AD Connect nie może pomóc w tym scenariuszu. Nie tworzy użytkowników lokalnie i nie ma żadnej możliwości, aby ustawić hasło lokalnie na takie same jak w usłudze Azure AD.

Jeśli jedynym powodem, dla którego planujesz dodać lokalną usługę AD, jest obsługa lokalnych obiektów AD (aplikacje typu Line-of-Business), być może należy rozważyć użycie [usług domeny usługi Azure AD.](../../active-directory-domain-services/index.yml)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
