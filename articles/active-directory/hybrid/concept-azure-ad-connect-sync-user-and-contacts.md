---
title: 'Synchronizacja programu Azure AD Connect: Opis użytkowników, grup i kontakty | Dokumentacja firmy Microsoft'
description: Wyjaśnia użytkownikom, grupom i kontakty w usłudze Azure AD Connect sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661747754369c17ca98ae69d477e04124b6a2942
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245490"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Synchronizacja programu Azure AD Connect: Opis użytkowników, grup i kontaktów
Istnieje kilka przyczyn, dlaczego może mieć wiele lasów usługi Active Directory i istnieje kilka topologii rozmieszczania. Typowe modeli obejmują wdrożenia zasobów konta usługi i lasów sync'ed GAL po połączeniu & pozyskiwania. Ale nawet w przypadku modeli czystego, modele hybrydowe są również wspólne. Domyślna konfiguracja przedstawiona w synchronizacji programu Azure AD Connect nie przyjmuje żadnych określonego modelu, ale w zależności od tego, jak dopasowanie użytkownika został wybrany w podręczniku instalacji, można zaobserwować inne zachowania.

W tym temacie opiszemy jak domyślna konfiguracja zachowuje się w niektórych topologii. Opiszemy konfiguracji i narzędzia Synchronization Rules Editor może służyć do wzięcia pod konfiguracji.

Istnieje kilka ogólnych reguł, których konfiguracja zakłada:
* Niezależnie od tego, kolejność zaimportujemy ze źródła Active Directory wynik końcowy powinien zawsze być taka sama.
* Aktywne konto zawsze przyczyni się informacje o rejestracji, w tym **userPrincipalName** i **sourceAnchor**.
* Wyłączone konto współtworzą userPrincipalName i sourceAnchor, o ile nie jest połączoną skrzynkę pocztową, jeśli nie ma aktywnego konta ma zostać odnaleziona.
* Konta z połączoną skrzynkę pocztową, nigdy nie będzie służyć do userPrincipalName i sourceAnchor. Zakłada się, później znaleźć aktywnego konta.
* Obiektem kontaktu może być przygotowana do usługi Azure AD jako kontakt lub użytkownik. Nie bardzo wiesz, dopóki nie zostaną przetworzone wszystkie lasy usługi Active Directory źródła.

## <a name="groups"></a>Grupy
Ważne punkty, które należy zwrócić uwagę podczas synchronizowania grup usługi Active Directory do usługi Azure AD:

* Program Azure AD Connect nie obejmuje grup wbudowanych rozwiązań zabezpieczeń z synchronizacji katalogów.

* Program Azure AD Connect nie obsługuje synchronizacji [członkostwa grupy podstawowej](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) do usługi Azure AD.

* Program Azure AD Connect nie obsługuje synchronizacji [członkowstwa w grupach dynamicznych dystrybucji](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) do usługi Azure AD.

* Aby zsynchronizować grupy usługi Active Directory do usługi Azure AD jako grupę z włączoną obsługą poczty:

    * Jeśli grupy *proxyAddress* atrybut jest pusty, jego *poczty* atrybut musi mieć wartość

    * Jeśli grupy *proxyAddress* atrybut jest pusta, musi zawierać co najmniej jedną wartość adresu serwera proxy SMTP. Oto kilka przykładów:
    
      * Grupy usługi Active Directory, w których atrybut proxyAddress ma wartość *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* nie będzie włączoną obsługą poczty w usłudze Azure AD. Go nie ma adresu SMTP.
      
      * Grupy usługi Active Directory, w których atrybut proxyAddress zawiera wartości *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe\@contoso.com"}* będzie włączoną obsługą poczty w usłudze Azure AD.
      
      * Grupy usługi Active Directory, w których atrybut proxyAddress zawiera wartości *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe\@contoso.com"}* będzie także włączoną obsługą poczty w usłudze Azure AD.

## <a name="contacts"></a>Kontakty
Kontakty reprezentujący użytkownika w innym lesie jest wspólne po połączeniu & nabycia gdzie rozwiązanie GALSync jest mostkowanie dwie lub większą liczbą lasów programu Exchange. Skontaktuj się z pomocą obiekt zawsze dołącza do przestrzeni łącznika do środowiska metaverse, za pomocą atrybutu poczty. Jeśli istnieje już obiekt kontaktu lub użytkownika za pomocą tego samego adresu poczty, obiekty są łączone ze sobą. To ustawienie jest konfigurowane w regule **w z usługi AD — skontaktuj się z Dołącz**. Dostępna jest również reguły o nazwie **w z usługi AD — skontaktuj się z wspólnej** przy użyciu przepływu atrybutu, aby atrybut metaverse **sourceObjectType** przy użyciu stałej **skontaktuj się z pomocą**. Ta reguła ma bardzo niskim priorytecie tak, jeśli dowolny obiekt użytkownika jest dołączony do tego samego obiektu metaverse, a następnie reguła **w z usługi AD — typowe użytkownika** współtworzą wartość tego atrybutu użytkownika. Z tą regułą tego atrybutu będzie mieć wartość kontaktu, jeśli żaden użytkownik nie został dołączony i wartość użytkownika, jeśli znaleziono co najmniej jednego użytkownika.

Do obsługi administracyjnej obiektu do usługi Azure AD, reguły ruchu wychodzącego **Out do usługi AAD — skontaktuj się z Dołącz** utworzy obiekt kontaktu, jeśli atrybut metaverse **sourceObjectType** jest ustawiona na **skontaktuj się z pomocą**. Jeśli ten atrybut jest ustawiony na **użytkownika**, następnie reguła **Out do usługi AAD — użytkownik przyłączyć** zamiast tego utworzyć obiekt użytkownika.
Istnieje możliwość, że obiekt jest promowany z kontaktu do użytkownika w przypadku katalogów Active źródła zostały zaimportowane i zsynchronizowane.

Na przykład w topologii usługi GALSync będzie znaleźliśmy skontaktuj się z pomocą obiektów dla każdego drugiego lasu podczas importowania pierwszy las. Spowoduje to etap nowe obiekty kontaktu w łączniku usługi AAD. Gdy firma Microsoft później Importuj i Synchronizuj drugiego lasu, firma Microsoft znajdzie rzeczywistych użytkowników i dołącz je do istniejących obiektów w magazynie metaverse. Firma Microsoft będzie następnie usuń obiektem kontaktu w usłudze AAD i zamiast tego utworzyć nowego obiektu użytkownika.

W przypadku topologii, w których użytkownicy są reprezentowane jako kontakty upewnij się, że wybrano, aby dopasować użytkowników na atrybut poczty w podręczniku instalacji. Jeśli wybierzesz inną opcję, będzie mieć konfiguracji usługi zależne od kolejności. Skontaktuj się z pomocą obiektów zawsze dołączy na atrybut poczty, ale na atrybut poczty tylko dołączy obiekty użytkownika, jeśli ta opcja została wybrana w podręczniku instalacji. Można następnie na końcu dwóch różnych obiektów w magazynie metaverse przy użyciu tego samego atrybutu poczty w przypadku obiektem kontaktu zostało zaimportowane przed obiektu użytkownika. Podczas eksportowania do usługi Azure AD zostanie zgłoszony błąd. To zachowanie jest celowe i wskazuje nieprawidłowe dane lub że topologia nie został prawidłowo zidentyfikowany podczas instalacji.

## <a name="disabled-accounts"></a>Wyłączone konta
Wyłączone konta są synchronizowane również do usługi Azure AD. Wyłączone konta są wspólne dla reprezentują zasoby w programie Exchange, na przykład salach konferencyjnych. Wyjątek stanowi użytkowników z połączoną skrzynkę pocztową; jak wspomniano wcześniej przydzieli te nigdy nie konta z usługą Azure AD.

Zakłada się, że jeśli zostanie znaleziony wyłączonego konta użytkownika, to firma Microsoft nie będą dostępne inne aktywne konto później i obiekt jest przygotowany do usługi Azure AD za pomocą userPrincipalName i sourceAnchor znaleziono. W przypadku, gdy inny aktywnego konta zostaną dołączone do tego samego obiektu metaverse, jego userPrincipalName i sourceAnchor zostaną użyte.

## <a name="changing-sourceanchor"></a>Zmiana sourceAnchor
Gdy obiekt została wyeksportowana do usługi Azure AD, a następnie nie mogą już zmienić sourceAnchor. Jeśli obiekt został wyeksportowany atrybut metaverse **cloudSourceAnchor** została ustawiona za pomocą **sourceAnchor** wartość zaakceptowane przez usługę Azure AD. Jeśli **sourceAnchor** został zmieniony, a nie odpowiada **cloudSourceAnchor**, reguła **Out do usługi AAD — Dołącz do użytkownika** zgłosi błąd **ma atrybut sourceAnchor zmienione**. W tym przypadku konfiguracji lub danych muszą zostać poprawione więc ten sam sourceAnchor jest obecne w magazynie metaverse programu ponownie, zanim obiekt można ponownie zsynchronizować.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizacja programu Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

