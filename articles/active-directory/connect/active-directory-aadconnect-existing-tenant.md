---
title: 'Azure AD Connect: Jeśli już masz usługi Azure AD | Dokumentacja firmy Microsoft'
description: W tym temacie opisano sposób użycia Connect, jeśli masz istniejącą dzierżawę usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 726d8998d24a630808186eea417f236fdbfb565e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725211"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Jeśli masz dzierżawę istniejących
Większość tematy dotyczące sposobu korzystania z usługi Azure AD Connect zakłada rozpoczynać nowe usługi Azure AD dzierżawy i czy ma żadnych użytkowników lub istnieją inne obiekty. Ale jeśli została uruchomiona z dzierżawy usługi Azure AD wypełnić go użytkowników i innych obiektów, a teraz chcesz użyć połączenia, a następnie w tym temacie jest dla Ciebie.

## <a name="the-basics"></a>Podstawy
Obiekt w usłudze Azure AD albo jest zarządzany w chmurze (Azure AD) lub lokalnie. Dla jednego pojedynczego obiektu nie może zarządzać niektóre atrybuty lokalnymi i niektórych innych atrybutów w usłudze Azure AD. Każdy obiekt ma flagę wskazującą, gdy obiekt jest obsługiwany.

W chmurze, można zarządzać niektórych użytkowników lokalnych i inne. Typowy scenariusz dla tej konfiguracji jest organizacji z różnych kont pracowników i pracowników sprzedaży. Kont pracowników ma lokalne konto usługi AD, ale sprzedaży pracowników, czy nie, użytkownicy mają konta w usłudze Azure AD. Czy zarządzanie niektórych użytkowników lokalnych, a niektóre w usłudze Azure AD.

Jeśli do zarządzania użytkowników w usłudze Azure AD, które są również w lokalnej usłudze AD i później chcesz użyć Connect, a następnie istnieją pewne dodatkowe problemy, które należy wziąć pod uwagę.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizacja istniejących użytkowników w usłudze Azure AD
Po zainstalowaniu usługi Azure AD Connect i rozpoczęciem tej synchronizacji, usługi Azure AD sync (w usłudze Azure AD) sprawdza co nowego obiektu i próbuje znaleźć obiektu do dopasowania. Istnieją trzy atrybuty stosowane do tego procesu: **userPrincipalName**, **proxyAddresses**, i **sourceAnchor**/**nazwę immutableID** . Dopasowanie w **userPrincipalName** i **proxyAddresses** nosi nazwę **nietrwałego dopasowania**. Dopasowanie w **sourceAnchor** nosi nazwę **twardych dopasowania**. Aby uzyskać **proxyAddresses** tylko wartość z atrybutu **SMTP:**, która jest podstawowego adresu e-mail, jest używany do oceny.

Dopasowanie tylko jest sprawdzany pod kątem nowych obiektów pochodzących z Connect. Jeśli zmienisz istniejącego obiektu, jest on zgodne z żadnym z tych atrybutów, następnie zostanie wyświetlony błąd zamiast tego.

Jeśli usługi Azure AD odnajdzie obiekt, w którym wartości atrybutów są takie same dla obiekt pochodzące z Connect i który znajduje się już w usłudze Azure AD, obiektu w usłudze Azure AD są wykonywane przez połączenie. Obiekt wcześniej zarządzanymi w chmurze z flagą zarządzać lokalnymi. Wszystkie atrybuty w usłudze Azure AD z wartością w lokalnym programie AD zostaną zastąpione wartości lokalnej. Wyjątek stanowi po atrybucie **NULL** wartości lokalnej. W takim przypadku wartość pozostaje usługi Azure AD, ale można nadal zmienić tylko lokalne na inny.

> [!WARNING]
> Ponieważ wszystkie atrybuty w usłudze Azure AD mają zostać zastąpione przez wartości lokalnej, upewnij się, że masz dobrej danych w sieci lokalnej. Na przykład jeśli tylko udało adres e-mail w usłudze Office 365, a nie przechowywane zaktualizowane w lokalnych usług AD DS, utracisz wszystkie wartości w usłudze Azure AD/usługi Office 365 nie znajduje się w usługach AD DS, a następnie.

> [!IMPORTANT]
> Jeśli użyciu synchronizacji haseł, które jest używane przez ustawień ekspresowych, a następnie hasła w usłudze Azure AD jest zastępowany przy użyciu hasła w lokalnej usługi AD. Jeśli użytkownicy są używane do zarządzania różne hasła, należy poinformować, że powinny używać lokalnego hasła po zainstalowaniu Connect.

W procesie planowania należy rozważyć poprzedniej sekcji i ostrzeżenia. Jeśli wprowadzono wiele zmian w usłudze Azure AD nie zostaną uwzględnione w lokalnych usług AD DS, a następnie należy zaplanować jak wypełnić usług AD DS przez zaktualizowane wartości przed synchronizacją obiektów z programem Azure AD Connect.

Jeśli dopasowane obiektów z soft dopasowania, a następnie **sourceAnchor** są dodawane do obiektu w usłudze Azure AD, dzięki czemu można następnie używać twardych dopasowania.

### <a name="hard-match-vs-soft-match"></a>Twarde dopasowania vs Soft-match
Nowa instalacja programu Connect należy nie ma różnic praktyczne między soft - i twardych dopasowania. Jest to różnica w sytuacji odzyskiwania po awarii. Jeśli zgubisz serwera z usługą Azure AD Connect, można ponownie zainstalować nowego wystąpienia bez utraty danych. Obiekt o sourceAnchor są wysyłane do połączenia podczas początkowej instalacji. Następnie można ocenić dopasowania przez klienta (Azure AD Connect), które jest znacznie szybsze niż robiąc tego samego w usłudze Azure AD. Twarde dopasowanie jest oceniany zarówno połączenia, jak i przez usługę Azure AD. Elastyczne dopasowanie jest tylko obliczane przez usługę Azure AD.

### <a name="other-objects-than-users"></a>Innych obiektów niż użytkowników
Grupy obsługujące pocztę i kontakty możesz można soft-match proxyAddresses na podstawie. Dopasowanie twardych nie ma zastosowania, ponieważ można aktualizować tylko sourceAnchor/nazwę immutableID (przy użyciu programu PowerShell) na użytkowników tylko. Dla grup, które nie są z włączoną obsługą poczty obecnie nie jest obsługiwane dla soft-match lub twardych dopasowania.

## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Tworzenie nowej lokalnej usługi Active Directory na podstawie danych w usłudze Azure AD
Niektórzy klienci rozpoczynać się tylko na chmurze rozwiązanie z usługą Azure AD i nie mają lokalnego AD. Później, do którego chce używać zasobów lokalnych i chcesz utworzyć lokalną AD opartych na danych usługi Azure AD. Azure AD Connect nie może pomóc w tym scenariuszu. Nie tworzy użytkowników lokalnych i nie ma możliwość ustawiono hasło lokalne takie same jak Azure AD.

Jeśli tylko przyczyny, dlaczego zamierzasz dodać lokalnej usłudze AD służy do obsługi obiektów LOB (aplikacji biznesowych z), a następnie może warto użyć [usługi domenowe Azure AD](../../active-directory-domain-services/index.yml) zamiast tego.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
