---
title: 'Synchronizacja programu Azure AD Connect: opis użytkowników, grup i kontakty | Dokumentacja firmy Microsoft'
description: Wyjaśniono, użytkowników, grup i kontakty synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: markvi;andkjell
ms.openlocfilehash: 2e249fbf63adc459a29aa3e39e22f97b5769586a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593337"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Synchronizacja programu Azure AD Connect: opis użytkowników, grup i kontaktów
Istnieje kilka przyczyn dlaczego może mieć wiele lasów usługi Active Directory i istnieje kilka topologii rozmieszczania. Typowe modele zawierają wdrażania konta zasobów i lasów sync'ed usługi GAL po połączeniu & nabycia. Ale nawet w przypadku modeli czystego, modele hybrydowe wspólnej również. Domyślna konfiguracja synchronizacji Azure AD Connect nie przyjmuje żadnego określonego modelu, ale w zależności od tego, jak dopasowywaniu użytkowników zostało wybrane w podręczniku instalacji, można zaobserwować różne zachowania.

W tym temacie firma Microsoft będzie przejście przez zachowanie domyślnej konfiguracji niektóre topologiami. Firma Microsoft będzie przejście przez konfigurację i Edytor reguł synchronizacji może służyć do wyszukiwania w konfiguracji.

Istnieje kilka ogólne zasady, których konfiguracja zakłada:
* Niezależnie od tego, w jakiej kolejności możemy zaimportować ze źródła usług Active Directory w rezultacie zawsze powinna być taka sama.
* Aktywne konto zawsze przyczyniają się informacji logowania, w tym **userPrincipalName** i **sourceAnchor**.
* Wyłączonych kont przyczyniają userPrincipalName i sourceAnchor, o ile nie jest połączoną skrzynkę pocztową, jeśli nie aktywne konto ma zostać odnaleziona.
* Konta z połączoną skrzynkę pocztową, nigdy nie będzie służyć do userPrincipalName i sourceAnchor. Zakłada się później odnaleźć aktywnego konta.
* Obiekt kontaktu może być przygotowana do usługi Azure AD jako kontakt lub użytkownik. Nie można ustalić naprawdę, dopóki nie zostaną przetworzone wszystkie lasów usługi Active Directory źródła.

## <a name="groups"></a>Grupy
Ważne informacje, które należy zwrócić uwagę podczas synchronizowania grup z usługi Active Directory do usługi Azure AD:

* Azure AD Connect nie obejmuje grup zabezpieczeń z synchronizacji katalogów.

* Azure AD Connect nie obsługuje synchronizacji [członkostwa grupy podstawowej](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) do usługi Azure AD.

* Azure AD Connect nie obsługuje synchronizacji [członkostwa w grupach dystrybucyjnych dynamiczne](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) do usługi Azure AD.

* Aby zsynchronizować grupy usługi Active Directory do usługi Azure AD jako grupę z włączoną obsługą poczty:

    * Jeśli grupy *proxyAddress* atrybut jest pusty, jego *poczty* atrybut musi mieć wartość

    * Jeśli grupy *proxyAddress* atrybut jest pusty, musi zawierać co najmniej jedną wartość adres serwera proxy SMTP. Oto kilka przykładów:
    
      * Grupy usługi Active Directory, w których atrybut proxyAddress ma wartość *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* nie będzie włączoną obsługą poczty w usłudze Azure AD. Nie ma adresu SMTP.
      
      * Grupy usługi Active Directory, w których atrybut proxyAddress zawiera wartości *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe@contoso.com"}* będzie można z włączoną obsługą poczty w usłudze Azure AD.
      
      * Grupy usługi Active Directory, w których atrybut proxyAddress zawiera wartości *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe@contoso.com"}* będzie także włączoną obsługą poczty w usłudze Azure AD.

## <a name="contacts"></a>Kontakty
Kontakty reprezentujący użytkownika w innym lesie jest posiadanie wspólnej po połączeniu & nabycia gdzie rozwiązania usługi GALSync jest mostkowanie co najmniej dwa lasy usługi programu Exchange. Skontaktuj się z pomocą obiektów jest zawsze przyłączania z obszaru łącznika do metaverse przy użyciu atrybutu poczty. Jeśli istnieje już kontaktu obiektu lub użytkownika z tym samym adresem poczty, obiekty są połączone ze sobą. Te ustawienia zostaną skonfigurowane w regule **w z usługi Active Directory — kontakt Join**. Istnieje również reguły o nazwie **w z usługi Active Directory — skontaktuj się z wspólnej** przepływu atrybutu z atrybutem metaverse **sourceObjectType** o stałej **skontaktuj się z**. Ta reguła ma pierwszeństwo bardzo niskich tak więc jeśli dowolny obiekt użytkownika jest dołączony do tego samego obiektu metaverse, a następnie reguły **w z usługi Active Directory — typowe użytkownika** przyczyniają się wartość użytkownika z tym atrybutem. Z tą regułą tego atrybutu będzie mieć wartość kontaktu, jeśli żaden użytkownik nie został dołączony i wartość użytkownika, jeśli znaleziono co najmniej jednego użytkownika.

Do obsługi obiektu do usługi Azure AD, wychodzącą regułę **Out do usługi AAD — skontaktuj się z Join** spowoduje utworzenie obiektu kontaktu, jeśli atrybut metaverse **sourceObjectType** ma ustawioną wartość **skontaktuj się z**. Jeśli ten atrybut ma ustawioną **użytkownika**, następnie reguły **Out do usługi AAD — użytkownik przyłączyć** zamiast tego utworzyć obiektu user.
Istnieje możliwość, że obiekt jest awansowana z skontaktuj się z użytkownika podczas źródła więcej usług Active Directory zostały zaimportowane i zsynchronizowane.

Na przykład w topologii usługi GALSync zostanie znaleźliśmy skontaktuj się z pomocą obiektów dla każdego z drugiego lasu możemy importowania pierwszy las. Spowoduje to etap nowe obiekty kontaktu w łączniku usługi AAD. Gdy firma Microsoft później importuje i synchronizuje drugiego lasu, możemy znajdzie rzeczywistych użytkowników i dołącz je do istniejących obiektów metaverse. Firma Microsoft będzie następnie usuwanie kontaktów obiektu w usłudze AAD i zamiast tego utwórz nowy obiekt użytkownika.

Jeśli masz topologii, w których użytkownicy są reprezentowane jako kontakty, upewnij się, że wybierzesz, aby dopasować użytkowników na atrybut poczty w podręczniku instalacji. Wybranie innej opcji będzie mieć kolejność zależne od konfiguracji. Skontaktuj się z pomocą obiektów zawsze spowoduje dołączenie na atrybut poczty, ale na atrybut poczty tylko dołączy obiekty użytkownika, jeśli ta opcja została wybrana w podręczniku instalacji. Można następnie na końcu dwóch różnych obiektów w magazynie metaverse atrybutem poczty Jeśli obiektu kontaktu została zaimportowana przed obiektu user. Podczas eksportowania do usługi Azure AD zostanie zgłoszony błąd. To zachowanie jest celowe i wskazuje nieprawidłowe dane lub że topologia nie została poprawnie zidentyfikowane podczas instalacji.

## <a name="disabled-accounts"></a>Wyłączone konta
Wyłączonych kont są również synchronizowane z usługą Azure AD. Wyłączone konta są często używane do reprezentowania zasobów w programie Exchange, na przykład sal konferencyjnych. Wyjątek stanowi użytkownikom połączoną skrzynkę pocztową; jak wcześniej wspomniano te będą nigdy nie zainicjować obsługę administracyjną konta do usługi Azure AD.

Zakłada się, że jeśli zostanie znaleziony wyłączonego konta użytkownika, to firma Microsoft nie będzie zawierał inne aktywne konto później i obiekt jest przygotowana do usługi Azure AD z userPrincipalName i znaleziono sourceAnchor. W przypadku, gdy inne aktywne konto dołączy do tego samego obiektu metaverse, następnie jego userPrincipalName i sourceAnchor będą używane.

## <a name="changing-sourceanchor"></a>Zmiana sourceAnchor
Gdy obiekt została wyeksportowana do usługi Azure AD, a następnie nie można już zmienić sourceAnchor. Jeśli obiekt został wyeksportowany atrybut metaverse **cloudSourceAnchor** ustawiono **sourceAnchor** wartość zaakceptowane przez usługę Azure AD. Jeśli **sourceAnchor** zostało zmienione i nie odpowiada **cloudSourceAnchor**, reguła **Out do usługi AAD — użytkownik przyłączyć** zgłosi błąd **zmienił atrybutu sourceAnchor**. W takim przypadku konfiguracji lub dane muszą zostać poprawione, tym samym sourceAnchor jest obecne w magazynie metaverse ponownie obiekt można ponownie zsynchronizować.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Azure AD Connect Sync: Dostosowywanie opcji synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)

