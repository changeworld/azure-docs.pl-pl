---
title: 'Synchronizacja usługi Azure AD Connect: opis użytkowników, grup i kontaktów | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono użytkowników, grupy i kontakty w synchronizacji usługi Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245490"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Synchronizacja usługi Azure AD Connect: opis użytkowników, grup i kontaktów
Istnieje kilka różnych powodów, dla których ma istnieć wiele lasów usługi Active Directory i istnieje kilka różnych topologii wdrażania. Typowe modele obejmują wdrożenie zasobów konta i lasy synchronizacji GAL po fuzji & przejęcia. Ale nawet jeśli istnieją czyste modele, modele hybrydowe są również powszechne. Domyślna konfiguracja w synchronizacji usługi Azure AD Connect nie zakłada żadnego określonego modelu, ale w zależności od tego, jak dopasowywanie użytkowników zostało wybrane w podręczniku instalacji, można zaobserwować różne zachowania.

W tym temacie przejdziemy przez zachowanie domyślnej konfiguracji w niektórych topologiach. Przejdziemy przez konfigurację i Edytor reguł synchronizacji może służyć do przyjrzenia się konfiguracji.

Istnieje kilka ogólnych reguł, które zakłada konfiguracja:
* Niezależnie od tego, które zamówienie importujemy ze źródłowych usług Active Directories, wynik końcowy powinien być zawsze taki sam.
* Aktywne konto zawsze będzie współtworzyć informacje logowania, w tym **userPrincipalName** i **sourceAnchor**.
* Wyłączone konto przyczyni userPrincipalName i sourceAnchor, chyba że jest to połączona skrzynka pocztowa, jeśli nie ma aktywnego konta, które można znaleźć.
* Konto z połączonej skrzynki pocztowej nigdy nie będą używane dla userPrincipalName i sourceAnchor. Zakłada się, że aktywne konto zostanie znalezione później.
* Obiekt kontaktu może być aprowizny do usługi Azure AD jako kontakt lub jako użytkownik. Tak naprawdę nie wiesz, dopóki nie zostały przetworzone wszystkie lasy usługi Active Directory.

## <a name="groups"></a>Grupy
Ważne kwestie, o które należy pamiętać podczas synchronizowania grup z usługi Active Directory do usługi Azure AD:

* Usługa Azure AD Connect wyklucza wbudowane grupy zabezpieczeń z synchronizacji katalogów.

* Usługa Azure AD Connect nie obsługuje synchronizowania [członkostw grupy podstawowej z usługą](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) Azure AD.

* Usługa Azure AD Connect nie obsługuje synchronizowania [członkostw dynamicznej grupy dystrybucyjnej z usługą](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) Azure AD.

* Aby zsynchronizować grupę usługi Active Directory z usługą Azure AD jako grupę obsługującą pocztę:

    * Jeśli atrybut *proxyAddress* grupy jest pusty, jego atrybut *mail* musi mieć wartość

    * Jeśli atrybut *proxyAddress* grupy nie jest pusty, musi zawierać co najmniej jedną wartość adresu serwera proxy SMTP. Oto kilka przykładów:
    
      * Grupa usługi Active Directory, której atrybut proxyAddress ma wartość *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* nie będzie włączona poczta w usłudze Azure AD. Nie ma adresu SMTP.
      
      * Grupa usługi Active Directory, której atrybut proxyAddress ma wartości *\@{"X500:/0=contoso.com/ou=users/cn=testgroup",,"SMTP:johndoe contoso.com"}* będzie włączona poczta w usłudze Azure AD.
      
      * Grupa usługi Active Directory, której atrybut proxyAddress ma wartości *{"X500:/0=contoso.com/ou=users/cn=testgroup",\@"smtp:johndoe contoso.com"}* będzie również włączona poczta w usłudze Azure AD.

## <a name="contacts"></a>Kontakty
Kontakty reprezentujące użytkownika w innym lesie są powszechne po połączeniu & nabycia, w którym rozwiązanie GALSync pomostowa dwa lub więcej lasów giełdowych. Obiekt kontaktu zawsze łączy się z przestrzeni łącznika do metaverse przy użyciu atrybutu mail. Jeśli istnieje już obiekt kontaktu lub obiekt użytkownika o tym samym adresie pocztowym, obiekty są łączone ze sobą. Jest to skonfigurowane w regule **In from AD – Contact Join**. Istnieje również reguła o nazwie **W od AD — Kontakt wspólny** z przepływem atrybutu do atrybutu metaverse **sourceObjectType** ze stałym **kontaktem**. Ta reguła ma bardzo niski priorytet, więc jeśli dowolny obiekt użytkownika jest połączony z tym samym obiektem metaverse, wówczas reguła **In from AD — User Common** przyczyni się do wartości Użytkownika do tego atrybutu. Dzięki tej regule ten atrybut będzie miał wartość Kontakt, jeśli żaden użytkownik nie został przyłączony, a wartość Użytkownika, jeśli znaleziono co najmniej jednego użytkownika.

Do inicjowania obsługi administracyjnej obiektu usługi Azure AD, reguła **wychodząca Out to AAD — Sprzężenie kontaktu** utworzy obiekt kontaktu, jeśli atrybut metaverse **sourceObjectType** jest ustawiony na **Kontakt**. Jeśli ten atrybut jest ustawiony na **User**, a następnie reguły **Out to AAD — User Join** utworzy obiekt użytkownika zamiast.
Jest możliwe, że obiekt jest promowany z kontaktu do użytkownika, gdy więcej źródła active directories są importowane i synchronizowane.

Na przykład w topologii GALSync znajdziemy obiekty kontaktowe dla wszystkich w drugim lesie podczas importowania pierwszego lasu. Spowoduje to etap nowych obiektów kontaktu w łączniku AAD. Kiedy później zaimportujemy i zsynchronizujemy drugi las, znajdziemy prawdziwych użytkowników i dołączymy ich do istniejących obiektów metaverse. Następnie usuniemy obiekt kontaktu w UAD i zamiast tego utworzymy nowy obiekt użytkownika.

Jeśli masz topologię, w której użytkownicy są reprezentowani jako kontakty, upewnij się, że w przewodniku instalacji wybrano opcję dopasowania użytkowników do atrybutu poczty. Jeśli wybierzesz inną opcję, będziesz mieć konfigurację zależną od zamówienia. Obiekty kontaktu zawsze będą dołączać do atrybutu poczty, ale obiekty użytkownika będą dołączać do atrybutu mail tylko wtedy, gdy ta opcja została wybrana w przewodniku instalacyjnym. Następnie można skończyć z dwóch różnych obiektów w metaverse z tego samego atrybutu poczty, jeśli obiekt kontaktu został zaimportowany przed obiektem użytkownika. Podczas eksportowania do usługi Azure AD zostanie zgłoszony błąd. To zachowanie jest zgodnie z projektem i wskazuje złe dane lub że topologia nie została poprawnie zidentyfikowana podczas instalacji.

## <a name="disabled-accounts"></a>Konta wyłączone
Wyłączone konta są również synchronizowane z usługą Azure AD. Konta wyłączone są wspólne do reprezentowania zasobów w programie Exchange, na przykład w salach konferencyjnych. Wyjątkiem są użytkownicy z połączonym skrzynką pocztową; jak wcześniej wspomniano, nigdy nie będą aprowizować konto do usługi Azure AD.

Założenie jest, że jeśli zostanie znalezione wyłączone konto użytkownika, a następnie nie znajdzie innego aktywnego konta później i obiekt jest aprowizowana do usługi Azure AD z userPrincipalName i sourceAnchor znaleziono. W przypadku, gdy inne aktywne konto dołączy do tego samego obiektu metaverse, zostanie użyty jego userPrincipalName i sourceAnchor.

## <a name="changing-sourceanchor"></a>Zmiana źródłaAnchor
Gdy obiekt został wyeksportowany do usługi Azure AD, nie można już zmienić sourceAnchor. Gdy obiekt został wyeksportowany, atrybut metaverse **cloudSourceAnchor** jest ustawiany z **wartością sourceAnchor** akceptowaną przez usługę Azure AD. Jeśli **sourceAnchor** zostanie zmieniony i nie pasuje do **cloudSourceAnchor**, reguła **Out to AAD — sprzężenie użytkownika** spowoduje wrzucenie **atrybutu source błądAnchor został zmieniony.** W takim przypadku konfiguracja lub dane muszą zostać poprawione, więc ten sam sourceAnchor jest obecny w metaverse ponownie, zanim obiekt może być ponownie zsynchronizowany.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Usługa Azure AD Connect Sync: dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

