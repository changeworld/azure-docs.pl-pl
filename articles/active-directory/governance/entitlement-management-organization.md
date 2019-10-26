---
title: Dodawanie połączonej organizacji w usłudze Azure AD uprawnień do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak zezwolić osobom spoza organizacji na żądanie pakietów dostępu, dzięki czemu można współpracować nad projektami.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d45db1f62b370a2692ed932572e16c247b8903
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952487"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management-preview"></a>Dodawanie połączonej organizacji w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza)

Zarządzanie prawami w usłudze Azure AD umożliwia współpracę z osobami spoza organizacji. Jeśli często pracujesz z użytkownikami w zewnętrznym katalogu lub domenie usługi Azure AD, możesz je dodać jako podłączoną organizację. W tym artykule opisano sposób dodawania połączonej organizacji w taki sposób, aby umożliwić użytkownikom spoza organizacji żądanie zasobów w katalogu.

## <a name="what-is-a-connected-organization"></a>Co to jest połączona organizacja?

Połączona organizacja to zewnętrzny katalog usługi Azure AD lub domena, z którą istnieje relacja.

Załóżmy na przykład, że Pracujesz w banku Woodgrove i chcesz współpracować z dwiema organizacjami zewnętrznymi: Instytut projektowania grafiki i contoso. Użytkownik skontaktował się ze swoim kontaktem w Instytucie projektu graficznego, że używa usługi Azure AD, a użytkownicy tego Instytutu grafiki tego pracownika mają główną nazwę użytkownika kończącą się `graphicdesigninstitute.com`. Po skontaktowaniu się z kontaktem w firmie Contoso nie korzystali jeszcze z usługi Azure AD, ale użytkownicy firmy Contoso mają główną nazwę użytkownika kończącą się `contoso.com`.

Można skonfigurować dwie połączone organizacje — jeden dla Instytutu projektowania grafiki z domeną `graphicdesigninstitute.com`i jeden dla firmy Contoso z `contoso.com`domeny. Jeśli następnie dodasz te dwie połączone organizacje do zasad, użytkownicy z każdej organizacji, którzy mają główną nazwę użytkownika zgodną z zasadami, będą mogli żądać dostępu do pakietów. Ponadto, ponieważ Instytut projektowania grafiki został zidentyfikowany jako używany do korzystania z usługi Azure AD, a następnie, jeśli Instytut projektowania grafiki ma w przyszłości domeny podrzędne, takie jak `graphicdesigninstitute.example`, użytkownicy z tą główną nazwą użytkownika będą mogli również żądać pakietów dostępu przy użyciu te same zasady.

![Przykład połączonej organizacji](./media/entitlement-management-organization/connected-organization-example.png)

Sposób uwierzytelniania użytkowników z katalogu lub domeny usługi Azure AD zależy od typu uwierzytelniania. Typy uwierzytelniania dla połączonych organizacji są następujące:

| Typ uwierzytelniania | Stan |
| --- | --- |
| Azure AD | Obsługiwane |
| [Federacja bezpośrednia](../b2b/direct-federation.md) | Wersja zapoznawcza |
| [Jednorazowy kod dostępu](../b2b/one-time-passcode.md) (domena) | Wersja zapoznawcza |

## <a name="add-a-connected-organization"></a>Dodawanie połączonej organizacji

Wykonaj następujące kroki, aby dodać zewnętrzny katalog lub domenę usługi Azure AD jako podłączoną organizację.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika lub zapraszający Gości

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **połączone organizacje** , a następnie kliknij pozycję **Dodaj połączoną organizację**.

    ![Zarządzanie tożsamościami — połączone organizacje — Dodawanie połączonej organizacji](./media/entitlement-management-organization/connected-organization.png)

1. Na karcie **podstawowe** wprowadź nazwę wyświetlaną i opis organizacji.

    ![Dodaj podłączoną organizację — podstawowe karty](./media/entitlement-management-organization/organization-basics.png)

1. Na karcie **katalog + domena** kliknij pozycję **Dodaj katalog i domenę** , aby otworzyć okienko wybieranie katalogów i domen.

1. Wpisz nazwę domeny, aby wyszukać katalog lub domenę usługi Azure AD. Należy wpisać całą nazwę domeny.

1. Sprawdź, czy jest to poprawna organizacja według podanej nazwy i typu uwierzytelniania. Sposób logowania użytkowników zależy od typu uwierzytelniania.

    ![Dodawanie połączonej organizacji — wybieranie katalogów i domen](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Kliknij przycisk **Dodaj** , aby dodać katalog lub domenę usługi Azure AD. Obecnie można dodać tylko jeden katalog lub domenę usługi Azure AD na podłączoną organizację.

    > [!NOTE]
    > Wszyscy użytkownicy z katalogu lub domeny usługi Azure AD będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników w usłudze Azure AD ze wszystkich poddomen skojarzonych z katalogiem, chyba że te domeny są blokowane przez listę dozwolonych i zablokowanych warunków B2B platformy Azure. Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](../b2b/allow-deny-list.md).

1. Po dodaniu katalogu lub domeny usługi Azure AD kliknij przycisk **Wybierz**.

    Organizacja zostanie wyświetlona na liście.

    ![Karta Dodawanie połączonej organizacji — katalogi](./media/entitlement-management-organization/organization-directory-domain.png)

1. Na karcie **sponsorzy** Dodaj opcjonalne sponsorzy dla tej połączonej organizacji.

    Sponsorzy są wewnętrznymi lub zewnętrznymi użytkownikami już w katalogu, które są punktem kontaktu dla relacji z tą połączoną organizacją. Wewnętrzni Sponsorzy są użytkownikami należącymi do katalogu. Zewnętrzne sponsorzy to użytkownicy-Goście z połączonej organizacji, którzy zostali wcześniej zaproszeni i znajdują się już w katalogu. Sponsorzy mogą być używani jako osoby zatwierdzające, gdy użytkownicy w tej połączonej organizacji żądają dostępu do tego pakietu dostępu. Aby uzyskać informacje na temat zapraszania użytkownika-gościa do katalogu, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../b2b/add-users-administrator.md).

    Po kliknięciu przycisku **Dodaj/Usuń**zostanie wyświetlone okienko umożliwiające wybranie wewnętrznych lub zewnętrznych sponsorów. W okienku zostanie wyświetlona niefiltrowana lista użytkowników i grup w Twoim katalogu.

    ![Dostęp do pakietu-zasady-Dodaj podłączoną organizację-sponsorzy](./media/entitlement-management-organization/organization-sponsors.png)

1. Na karcie **Recenzja + tworzenie** Sprawdź ustawienia organizacji, a następnie kliknij przycisk **Utwórz**.

    ![Dostęp do pakietu-zasady-Dodawanie połączonej organizacji — Recenzja + tworzenie](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>Usuwanie połączonej organizacji

Jeśli nie masz już relacji z zewnętrznym katalogiem lub domeną usługi Azure AD, możesz usunąć połączoną organizację.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika lub zapraszający Gości

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **połączone organizacje** , a następnie kliknij, aby otworzyć połączoną organizację.

1. Na stronie Przegląd kliknij przycisk **Usuń** , aby usunąć połączoną organizację.

    Obecnie w przypadku braku połączonych użytkowników można usunąć tylko podłączoną organizację.

    ![Zarządzanie tożsamościami — połączone organizacje — usuwanie połączonej organizacji](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem użytkowników zewnętrznych](entitlement-management-organization.md)
- [Dla użytkowników nieznajdujących się w katalogu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
