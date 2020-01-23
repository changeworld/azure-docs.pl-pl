---
title: Dodawanie połączonej organizacji w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
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
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c1b6f5ebffa39d3b735e85df794e37329e3aa2e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548904"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Dodawanie połączonej organizacji w zarządzaniu prawami usługi Azure AD

Zarządzanie prawami w usłudze Azure AD umożliwia współpracę z osobami spoza organizacji. Jeśli często pracujesz z użytkownikami w zewnętrznym katalogu lub domenie usługi Azure AD, możesz je dodać jako podłączoną organizację. W tym artykule opisano sposób dodawania połączonej organizacji w taki sposób, aby umożliwić użytkownikom spoza organizacji żądanie zasobów w katalogu.

## <a name="what-is-a-connected-organization"></a>Co to jest połączona organizacja?

Połączona organizacja to zewnętrzny katalog usługi Azure AD lub domena, z którą istnieje relacja.

Załóżmy na przykład, że Pracujesz w banku Woodgrove i chcesz współpracować z dwiema organizacjami zewnętrznymi. Te dwie organizacje mają różne konfiguracje:

- Graficzny Instytut projektowania używa usługi Azure AD, a ich użytkownicy mają główną nazwę użytkownika kończącą się na `graphicdesigninstitute.com`
- Firma Contoso nie korzysta jeszcze z usługi Azure AD. Użytkownicy firmy Contoso mają główną nazwę użytkownika kończącą się na `contoso.com`.

W takim przypadku można skonfigurować dwie połączone organizacje. Utworzysz jedną podłączoną organizację dla Instytutu projektowania grafiki i jeden dla contoso. Jeśli następnie dodasz te dwie połączone organizacje do zasad, użytkownicy z każdej organizacji z nazwą główną użytkownika zgodną z zasadami mogą żądać pakietów dostępu. Użytkownicy z główną nazwą użytkownika, która ma domenę graphicdesigninstitute.com, byłyby zgodną z podłączoną organizacją Instytutu projektowego i mogą przesyłać żądania, a użytkownicy z główną nazwą użytkownika, która ma domenę contoso.com, byłyby zgodne Organizacja połączona z contoso, która również może żądać pakietów. Ponadto, ponieważ Instytut projektowania grafiki używa usługi Azure AD, wszyscy użytkownicy z nazwą główną zgodną z [zweryfikowaną domeną](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) dodaną do swojej dzierżawy, na przykład graphicdesigninstitute. przykład będzie mógł również żądać pakietów dostępu przy użyciu tych samych zasad.

![Przykład połączonej organizacji](./media/entitlement-management-organization/connected-organization-example.png)

Sposób uwierzytelniania użytkowników z katalogu lub domeny usługi Azure AD zależy od typu uwierzytelniania. Typy uwierzytelniania dla połączonych organizacji są następujące:

- Azure AD
- [Federacja bezpośrednia](../b2b/direct-federation.md)
- [Jednorazowy kod dostępu](../b2b/one-time-passcode.md) (domena)

Aby zapoznać się z pokazem, jak dodać podłączoną organizację, Obejrzyj następujące wideo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

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
    > Wszyscy użytkownicy z katalogu lub domeny usługi Azure AD będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników w usłudze Azure AD ze wszystkich poddomen skojarzonych z katalogiem, chyba że te domeny są blokowane przez listę dozwolonych i zablokowanych warunków B2B platformy Azure. Aby uzyskać więcej informacji, zobacz [dozwolonych lub zablokowanych zaproszeń do użytkowników B2B z określonym organizacjom](../b2b/allow-deny-list.md).

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

- [Zarządzanie dostępem użytkowników zewnętrznych](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Dla użytkowników nieznajdujących się w katalogu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
