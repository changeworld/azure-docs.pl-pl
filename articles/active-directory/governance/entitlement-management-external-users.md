---
title: Zarządzanie dostępem użytkowników zewnętrznych w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Informacje o ustawieniach, które można określić, aby zarządzać dostępem użytkowników zewnętrznych w Azure Active Directory zarządzania prawami.
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 419970985b9531ffab348491730aaf6c00e143b1
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527101"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>Zarządzanie dostępem użytkowników zewnętrznych w usłudze Azure AD uprawniań (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zarządzanie prawami w usłudze Azure AD wykorzystuje [usługę Azure AD Business-to-Business (B2B)](../b2b/what-is-b2b.md) do współpracy z osobami spoza organizacji w innym katalogu. Przy użyciu usługi Azure AD B2B użytkownicy zewnętrzni są uwierzytelniani w katalogu macierzystym, ale mają reprezentację w katalogu. Reprezentacja w katalogu umożliwia użytkownikowi przypisanie dostępu do zasobów.

W tym artykule opisano ustawienia, które można określić, aby zarządzać dostępem użytkowników zewnętrznych.

## <a name="how-entitlement-management-can-help"></a>Jak zarządzanie uprawnieniami może pomóc

W przypadku korzystania z funkcji zapraszania [B2B usługi Azure AD](../b2b/what-is-b2b.md) musisz znać adresy e-mail użytkowników zewnętrznych Gości, którzy chcą umieścić w katalogu zasobów i pracy z programem. Jest to doskonałe rozwiązanie, gdy pracujesz nad mniejszym lub krótkoterminowym projektem i znasz już wszystkich uczestników, ale jest to trudniejsze do zarządzania, jeśli masz wielu użytkowników, z którymi chcesz pracować, lub jeśli uczestnicy zmieniają się z upływem czasu.  Na przykład użytkownik może pracować z inną organizacją i mieć jeden punkt kontaktu z tą organizacją, ale w miarę upływu czasu dodatkowi użytkownicy z tej organizacji będą musieli również uzyskać dostęp.

Za pomocą zarządzania uprawnieniami można zdefiniować zasady, które umożliwią użytkownikom z określonych organizacji określenie, że możliwość samodzielnego żądania pakietu dostępu. Można określić, czy wymagane jest zatwierdzenie oraz datę wygaśnięcia dostępu. Jeśli jest wymagane zatwierdzenie, można także zaprosić jednego lub kilku użytkowników z organizacji zewnętrznej do katalogu i wyznaczyć ich jako osoby zatwierdzające, ponieważ mogą oni wiedzieć, którzy użytkownicy zewnętrzni z organizacji potrzebują dostępu. Po skonfigurowaniu pakietu dostępu można wysłać link do osoby kontaktowej (sponsora) w organizacji zewnętrznej. Ten kontakt może współdzielić z innymi użytkownikami w organizacji zewnętrznej i może użyć tego linku, aby zażądać pakietu dostępu. Użytkownicy z tej organizacji, którzy zostali już zaproszeni do katalogu, mogą również korzystać z tego linku.

Gdy żądanie zostanie zatwierdzone, zarządzanie prawami zapewni użytkownikowi dostęp do niezbędnego dostępu, który może obejmować zaproszenie użytkownika, jeśli nie znajdują się jeszcze w Twoim katalogu. Usługa Azure AD automatycznie utworzy dla nich konto gościa B2B. Należy pamiętać, że administrator może wcześniej ograniczyć, które organizacje są dozwolone do współpracy, ustawiając [listę dozwolonych lub](../b2b/allow-deny-list.md) zablokowanych B2B, aby zezwalać na zaproszenia do innych organizacji lub je blokować.  Jeśli użytkownik nie jest dozwolony przez listę dozwolonych lub zablokowanych, nie będzie zapraszany.

Ponieważ nie chcesz, aby użytkownik zewnętrzny miał dostęp do ostatniej nieograniczonej, należy określić datę wygaśnięcia zasad, na przykład 180 dni. Po 180 dniach, jeśli ich dostęp nie zostanie rozszerzony, zarządzanie uprawnieniami spowoduje usunięcie wszystkich dostępu skojarzonych z tym pakietem dostępu. Domyślnie, jeśli użytkownik, który został zaproszony za pośrednictwem usługi zarządzania prawami, nie ma żadnych innych przypisań pakietów dostępu, wówczas po utracie ostatniego przypisania ich konto gościa nie zostanie zalogowany przez 30 dni, a następnie usunięte. Zapobiega to rozmnożenia niepotrzebnych kont. Jak opisano w poniższych sekcjach, te ustawienia można konfigurować.

## <a name="how-access-works-for-external-users"></a>Jak działa dostęp dla użytkowników zewnętrznych

Poniższy diagram i kroki zawierają omówienie sposobu udzielania dostępu użytkownikom zewnętrznym do pakietu dostępu.

![Diagram przedstawiający cykl życia użytkowników zewnętrznych](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. W katalogu utworzysz pakiet dostępu zawierający zasady dla użytkowników, które nie znajdują [się w katalogu](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Wysyłasz [link do mojego portalu dostępu](entitlement-management-access-package-settings.md) do kontaktu w organizacji zewnętrznej, który mogą oni udostępniać użytkownikom w celu żądania pakietu dostępu.

1. Użytkownik zewnętrzny (**osoba żądająca A** w tym przykładzie) używa linku Portal dostępu w celu [zażądania dostępu](entitlement-management-request-access.md) do pakietu dostępu.

1. Osoba zatwierdzająca [zatwierdza żądanie](entitlement-management-request-approve.md) (lub żądanie jest zatwierdzane z autozatwierdzeniem).

1. Żądanie przechodzi do [stanu dostarczania](entitlement-management-process.md).

1. Przy użyciu procesu zapraszania B2B konto użytkownika-gościa jest tworzone w Twoim katalogu (w tym przykładzie jest to**osoba żądająca a)** . Jeśli zdefiniowano [listę dozwolonych lub Odmów](../b2b/allow-deny-list.md) , zostanie zastosowane ustawienie listy.

1. Użytkownik-Gość ma przypisany dostęp do wszystkich zasobów w pakiecie dostępu. Wprowadzenie zmian w usłudze Azure AD i innych usługach online firmy Microsoft lub połączonych aplikacjach SaaS może zająć trochę czasu. Aby uzyskać więcej informacji, zobacz [kiedy zmiany są stosowane](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Użytkownik zewnętrzny otrzymuje wiadomość e-mail informującą o tym, że dostęp został [dostarczony](entitlement-management-process.md).

1. Aby uzyskać dostęp do zasobów, użytkownik zewnętrzny może kliknąć link w wiadomości e-mail lub próbować uzyskać dostęp do dowolnego zasobu katalogu bezpośrednio w celu ukończenia procesu zaproszenia.

1. W zależności od ustawień zasad w miarę upływu czasu, przypisanie pakietu dostępu dla użytkownika zewnętrznego wygasa i zostaje usunięty dostęp użytkownika zewnętrznego.

1. W zależności od cyklu życia ustawień użytkowników zewnętrznych, gdy użytkownik zewnętrzny nie ma już żadnych przypisań pakietów dostępu, użytkownik zewnętrzny nie może się zalogować, a konto użytkownika-Gości zostanie usunięte z katalogu.

## <a name="manage-the-lifecycle-of-external-users"></a>Zarządzanie cyklem życia użytkowników zewnętrznych

Możesz wybrać, co się stanie, gdy użytkownik zewnętrzny, który został zaproszony do katalogu przy użyciu zatwierdzonego żądania pakietu dostępu, nie ma już żadnych przypisań pakietów dostępu. Taka sytuacja może wystąpić, jeśli użytkownik utraci wszystkie przypisania pakietu dostępu lub ich ostatnie przypisanie pakietu dostępu wygaśnie. Domyślnie, gdy użytkownik zewnętrzny nie ma już żadnych przypisań pakietów dostępu, nie może się zalogować do katalogu. Po 30 dniach konto użytkownika-Gość zostanie usunięte z katalogu.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie prawami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

    ![Ustawienia do zarządzania cyklem życia użytkowników zewnętrznych](./media/entitlement-management-external-users/settings-external-users.png)

1. W sekcji **Zarządzaj cyklem życia użytkowników zewnętrznych** wybierz różne ustawienia dla użytkowników zewnętrznych.

1. Gdy użytkownik zewnętrzny utraci ostatnie przypisanie do dowolnych pakietów dostępu, jeśli chcesz zablokować użytkownikom możliwość logowania się do tego katalogu, ustaw opcję **Zablokuj użytkownikom zewnętrznym na logowanie do tego katalogu** na **wartość tak**.

    > [!NOTE]
    > Jeśli użytkownik nie może się zalogować do tego katalogu, użytkownik nie będzie mógł ponownie zażądać pakietu dostępu lub poprosić o dodatkowy dostęp w tym katalogu. Nie należy konfigurować blokowania logowania, jeśli będą później musieli zażądać dostępu do innych pakietów dostępu.

1. Gdy użytkownik zewnętrzny utraci swoje ostatnie przypisanie do wszystkich pakietów dostępu, jeśli chcesz usunąć swoje konto użytkownika-gościa w tym katalogu, ustaw opcję **Usuń użytkownika zewnętrznego** na **wartość tak**.

    > [!NOTE]
    > Zarządzanie prawami powoduje usunięcie tylko tych kont, które zostały zaproszone przez Zarządzanie uprawnieniami. Należy również pamiętać, że użytkownik zostanie zablokowany przed zalogowaniem się i usunięciem z tego katalogu, nawet jeśli ten użytkownik został dodany do zasobów w tym katalogu, które nie mają dostępu do przypisań pakietów. Jeśli gość znajdował się w tym katalogu przed odebraniem przypisań pakietów dostępu, pozostanie. Jeśli jednak gość został zaproszony przez przypisanie pakietu dostępu, a po jego zaproszeniu również zostanie przypisany do witryny usługi OneDrive dla firm lub SharePoint Online, zostaną one usunięte.

1. Jeśli chcesz usunąć konto użytkownika-gościa w tym katalogu, możesz ustawić liczbę dni, po której ma zostać usunięta. Jeśli chcesz usunąć konto użytkownika-gościa, gdy tylko utracisz swoje ostatnie przypisanie do wszystkich pakietów dostępu, ustaw **liczbę dni przed usunięciem użytkownika zewnętrznego z tego katalogu** do **0**.

1. Kliknij przycisk **Save** (Zapisz).

## <a name="enable-a-catalog-for-external-users"></a>Włączanie wykazu dla użytkowników zewnętrznych

Podczas tworzenia [nowego wykazu](entitlement-management-catalog-create.md)istnieje ustawienie umożliwiające użytkownikom z katalogów zewnętrznych zażądanie pakietów dostępu w wykazie. Jeśli nie chcesz, aby użytkownicy zewnętrzni mieli uprawnienia do żądania dostępu do pakietów w wykazie, ustaw opcję **włączone dla użytkowników zewnętrznych** na wartość **nie**.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika lub właściciel katalogu

![Nowe okienko katalogu](./media/entitlement-management-shared/new-catalog.png)

Możesz również zmienić to ustawienie po utworzeniu katalogu.

![Edytuj ustawienia katalogu](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>Następne kroki

- [Dla użytkowników nieznajdujących się w katalogu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Tworzenie wykazu zasobów i zarządzanie nim](entitlement-management-catalog-create.md)
- [Delegowanie i role](entitlement-management-delegate.md)
