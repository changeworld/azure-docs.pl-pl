---
title: Dodawanie połączonej organizacji w zarządzaniu uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak zezwolić osobom spoza organizacji na żądanie pakietów dostępu, aby można było współpracować nad projektami.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128608"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Dodawanie połączonej organizacji w zarządzaniu uprawnieniami usługi Azure AD

Dzięki zarządzaniu uprawnieniami usługi Azure Active Directory (Azure AD) możesz współpracować z osobami spoza organizacji. Jeśli często współpracujesz z użytkownikami w zewnętrznym katalogu lub domenie usługi Azure AD, możesz dodać ich jako połączoną organizację. W tym artykule opisano sposób dodawania połączonej organizacji, aby umożliwić użytkownikom spoza organizacji żądanie zasobów w katalogu.

## <a name="what-is-a-connected-organization"></a>Co to jest połączona organizacja?

Połączona organizacja to zewnętrzny katalog lub domena usługi Azure AD, z którą masz relację.

Załóżmy na przykład, że pracujesz w Woodgrove Bank i chcesz współpracować z dwiema organizacjami zewnętrznymi. Te dwie organizacje mają różne konfiguracje:

- Instytut Projektowania Graficznego używa usługi Azure AD, a ich użytkownicy mają główną nazwę użytkownika, która kończy się *graphicdesigninstitute.com*.
- Contoso nie używa jeszcze usługi Azure AD. Użytkownicy contoso mają główną nazwę użytkownika, która kończy się *contoso.com*.

W takim przypadku można skonfigurować dwie połączone organizacje. Tworzysz jedną połączoną organizację dla Instytutu Projektowania Graficznego i jedną dla firmy Contoso. Jeśli następnie dodasz dwie połączone organizacje do zasad, użytkownicy z każdej organizacji o głównej nazwie użytkownika, która pasuje do zasad, mogą żądać pakietów dostępu. Użytkownicy o głównej nazwie użytkownika, która ma domenę *graphicdesigninstitute.com,* będą pasować do organizacji połączonej z Instytutem Projektowania Graficznego i będą mogli przesyłać żądania. Użytkownicy o głównej nazwie użytkownika, która ma domenę *contoso.com,* będą pasować do organizacji połączonej z usługą Contoso i będą mogli również żądać pakietów. A ponieważ Instytut Projektowania Graficznego używa usługi Azure AD, wszyscy użytkownicy o głównej nazwie, która pasuje do [zweryfikowanej domeny,](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) która jest dodana do ich dzierżawy, takich jak *graphicdesigninstitute.example,* również będą mogli żądać pakietów dostępu przy użyciu tych samych zasad.

![Przykład połączonej organizacji](./media/entitlement-management-organization/connected-organization-example.png)

Sposób uwierzytelniania użytkowników z katalogu usługi Azure AD lub uwierzytelniania domeny zależy od typu uwierzytelniania. Typy uwierzytelniania dla połączonych organizacji to:

- Azure AD
- [Federacja bezpośrednia](../b2b/direct-federation.md)
- [Jednorazowy kod dostępu](../b2b/one-time-passcode.md) (domena)

Aby zademonstrować sposób dodawania połączonej organizacji, obejrzyj następujący film:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Dodawanie połączonej organizacji

Aby dodać zewnętrzny katalog lub domenę usługi Azure AD jako połączoną organizację, postępuj zgodnie z instrukcjami zawartymi w tej sekcji.

**Rola wstępna**: *Administrator globalny,* *Administrator użytkownika*lub *Zaproszony gość*

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Zarządzanie tożsamościami**.

1. W lewym okienku wybierz pozycję **Połączone organizacje**, a następnie wybierz pozycję **Dodaj połączoną organizację**.

    ![Przycisk "Dodaj połączoną organizację"](./media/entitlement-management-organization/connected-organization.png)

1. Wybierz kartę **Podstawy,** a następnie wprowadź nazwę wyświetlaną i opis organizacji.

    ![Okienko Podstawowe informacje "Dodaj połączoną organizację"](./media/entitlement-management-organization/organization-basics.png)

1. Wybierz kartę **Katalog + domena,** a następnie wybierz pozycję **Dodaj katalog + domenę**.

    Zostanie otwarte okienko **Wybierz katalogi + domeny.**

1. W polu wyszukiwania wprowadź nazwę domeny, aby wyszukać katalog lub domenę usługi Azure AD. Pamiętaj, aby wprowadzić całą nazwę domeny.

1. Sprawdź, czy nazwa organizacji i typ uwierzytelniania są poprawne. Sposób logowania użytkowników zależy od typu uwierzytelniania.

    ![Okienko "Wybierz katalogi + domeny"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Wybierz **pozycję Dodaj,** aby dodać katalog lub domenę usługi Azure AD. Obecnie można dodać tylko jeden katalog usługi Azure AD lub domenę na połączoną organizację.

    > [!NOTE]
    > Wszyscy użytkownicy z katalogu lub domeny usługi Azure AD będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników w usłudze Azure AD ze wszystkich poddomen skojarzonych z katalogiem, chyba że te domeny są blokowane przez firmę Usługi Azure AD do firmy (B2B) zezwalają lub odmawiają listy. Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń do użytkowników B2B z określonych organizacji](../b2b/allow-deny-list.md).

1. Po dodaniu katalogu lub domeny usługi Azure AD wybierz pozycję **Wybierz**.

    Organizacja pojawi się na liście.

    ![Okienko "Katalog + domena"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Wybierz kartę **Sponsorzy,** a następnie dodaj opcjonalnych sponsorów dla tej połączonej organizacji.

    Sponsorzy są użytkownikami wewnętrznymi lub zewnętrznymi już w katalogu, którzy są punktem kontaktowym dla relacji z tą połączoną organizacją. Sponsorzy wewnętrzni są użytkownikami członkowskimi w katalogu. Sponsorzy zewnętrzni to użytkownicy-goście z połączonej organizacji, którzy zostali wcześniej zaproszeni i są już w katalogu. Sponsorzy mogą być wykorzystywani jako osoby zatwierdzające, gdy użytkownicy w tej połączonej organizacji żądają dostępu do tego pakietu dostępu. Aby uzyskać informacje dotyczące zapraszania użytkownika-gościa do katalogu, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w portalu Azure.](../b2b/add-users-administrator.md)

    Po wybraniu opcji **Dodaj/Usuń**zostanie otwarte okienko, w którym można wybrać sponsorów wewnętrznych lub zewnętrznych. W okienku zostanie wyświetlona niefiltrowana lista użytkowników i grup w katalogu.

    ![Okienko Sponsorzy](./media/entitlement-management-organization/organization-sponsors.png)

1. Wybierz kartę **Recenzja + utwórz,** przejrzyj ustawienia organizacji, a następnie wybierz pozycję **Utwórz**.

    ![Okienko "Recenzja + utwórz"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Aktualizowanie połączonej organizacji 

Jeśli połączona organizacja zmieni się w inną domenę, zmieni nazwę organizacji lub chcesz zmienić sponsorów, możesz zaktualizować połączoną organizację, postępując zgodnie z instrukcjami w tej sekcji.

**Rola wstępna**: *Administrator globalny,* *Administrator użytkownika*lub *Zaproszony gość*

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Zarządzanie tożsamościami**.

1. W lewym okienku wybierz pozycję **Połączone organizacje**, a następnie wybierz połączoną organizację, aby ją otworzyć.

1. W okienku przeglądu połączonej organizacji wybierz pozycję **Edytuj,** aby zmienić nazwę lub opis organizacji.  

1. W okienku **Katalog + domena** wybierz pozycję **Aktualizuj katalog + domenę,** aby zmienić go na inny katalog lub domenę.

1. W okienku **Sponsorzy** wybierz pozycję **Dodaj sponsorów wewnętrznych** lub **Dodaj sponsorów zewnętrznych,** aby dodać użytkownika jako sponsora. Aby usunąć sponsora, wybierz sponsora, a w prawym okienku wybierz pozycję **Usuń**.


## <a name="delete-a-connected-organization"></a>Usuwanie połączonej organizacji

Jeśli nie masz już relacji z zewnętrznym katalogiem lub domeną usługi Azure AD, możesz usunąć połączoną organizację.

**Rola wstępna**: *Administrator globalny,* *Administrator użytkownika*lub *Zaproszony gość*

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Zarządzanie tożsamościami**.

1. W lewym okienku wybierz pozycję **Połączone organizacje**, a następnie wybierz połączoną organizację, aby ją otworzyć.

1. W okienku przeglądu połączonej organizacji wybierz pozycję **Usuń,** aby go usunąć.

    Obecnie można usunąć połączoną organizację tylko wtedy, gdy nie ma połączonych użytkowników.

    ![Przycisk Usuń połączoną organizację](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem dla użytkowników zewnętrznych](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Zarządzanie dostępem dla użytkowników, którzy nie mają w katalogu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
