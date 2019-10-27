---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 78a0dafeedc9aac4db69903b9f1193574cbd39c7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934746"
---
## <a name="for-users-in-your-directory"></a>Dla użytkowników w katalogu

Wykonaj te kroki, jeśli chcesz zezwolić użytkownikom w katalogu na żądanie tego pakietu dostępu. Podczas definiowania zasad żądań można określić poszczególnych użytkowników lub bardziej często grup użytkowników. Na przykład organizacja może mieć już grupę, taką jak **Wszyscy pracownicy**.  Jeśli ta grupa zostanie dodana w zasadach dla użytkowników, którzy mogą żądać dostępu, wówczas każdy członek tej grupy może następnie zażądać dostępu.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **dla użytkowników w katalogu**.

    Po wybraniu tej opcji pojawią się nowe opcje, które umożliwią dokładniejsze określenie, kto w katalogu może zażądać tego pakietu dostępu.

    ![Dostęp do pakietów — dla użytkowników w katalogu](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Określeni użytkownicy i grupy** | Wybierz tę opcję, jeśli chcesz, aby tylko użytkownicy i grupy w Twoim katalogu mogli żądać tego pakietu dostępu. |
    | **Wszyscy członkowie (z wyłączeniem Gości)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy należący do katalogu mogli żądać tego pakietu dostępu. Ta opcja nie obejmuje żadnych użytkowników-Gości, którzy zostali zaproszeni do katalogu. |
    | **Wszyscy użytkownicy (w tym Goście)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy i Goście w katalogu mogli żądać tego pakietu dostępu. |

    Użytkownicy-Goście odwołują się do użytkowników zewnętrznych, którzy zostali zaproszeni do katalogu za pomocą [usługi Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Aby uzyskać informacje o różnicach między użytkownikami należącymi do członków i użytkownikami-Gośćmi, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. W przypadku wybrania **określonych użytkowników i grup**kliknij pozycję **Dodaj użytkowników i grupy**.

1. W okienku wybierz użytkowników i grupy wybierz użytkowników i grupy, które chcesz dodać.

    ![Dostęp do pakietu-żądania — Wybieranie użytkowników i grup](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Kliknij pozycję **Wybierz** , aby dodać użytkowników i grupy.

1. Przejdź do sekcji [zatwierdzenie](#approval) .

## <a name="for-users-not-in-your-directory"></a>Dla użytkowników nieznajdujących się w katalogu

Wykonaj te kroki, jeśli chcesz zezwolić użytkownikom znajdującym się poza katalogiem na żądanie tego pakietu dostępu. Użytkownicy znajdujący się **poza katalogiem nie** odwołują użytkowników, którzy znajdują się w innym katalogu lub domenie usługi Azure AD, ale mogą nie zostać zaproszeni do katalogu. Katalogi usługi Azure AD muszą być skonfigurowane tak, aby zezwalać na zaproszenia w **ograniczeniach współpracy**. Aby uzyskać więcej informacji, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać Gości](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Konto użytkownika-gościa zostanie utworzone dla użytkownika, który jeszcze nie znajduje się w katalogu, którego żądanie jest zatwierdzone lub zaakceptowane. Gość zostanie zaproszony, ale nie otrzyma wiadomości e-mail z zaproszeniem. Zamiast tego otrzymają wiadomość e-mail, gdy zostanie dostarczone przypisanie pakietu dostępu. Domyślnie, gdy użytkownik tego gościa nie ma już żadnych przypisań pakietów dostępu, ponieważ ostatnie przypisanie wygasło lub zostało anulowane, konto użytkownika Gość zostanie zablokowane przed zalogowaniem się i usunięciem. Jeśli chcesz, aby użytkownicy-Goście pozostali w Twoim katalogu przez czas nieokreślony, nawet jeśli nie mają przypisanych pakietów dostępu, możesz zmienić ustawienia konfiguracji zarządzania uprawnieniami. Aby uzyskać więcej informacji na temat obiektu użytkownika-gościa, zobacz [właściwości Azure Active Directory użytkownika współpracy B2B](../articles/active-directory/b2b/user-properties.md).

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **dla użytkowników, którzy nie są w Twoim katalogu**.

    Po wybraniu tej opcji pojawiają się nowe opcje.

    ![Dostęp do pakietów — dla użytkowników spoza katalogu](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Określone organizacje połączone** | Wybierz tę opcję, jeśli chcesz wybrać z listy organizacji, które wcześniej dodaliśmy do administratora. Wszyscy użytkownicy z wybranych organizacji będą mogli zażądać tego pakietu dostępu. |
    | **Wszystkie połączone organizacje** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy ze wszystkich połączonych organizacji mogli żądać tego pakietu dostępu. |

    Połączona organizacja to zewnętrzny katalog usługi Azure AD lub domena, z którą istnieje relacja.

1. W przypadku wybrania opcji **określone połączone organizacje**kliknij pozycję **Dodaj katalogi** , aby wybrać z listy połączonych organizacji, które zostały wcześniej dodane przez administratora.

1. Wpisz nazwę lub nazwę domeny, aby wyszukać połączoną wcześniej organizację.

    ![Dostęp do pakietu-żądania — wybierz katalogi](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Jeśli organizacja, z którą chcesz współpracować, nie znajduje się na liście, możesz poprosił administratora, aby dodał ją jako podłączoną organizację. Aby uzyskać więcej informacji, zobacz [Dodawanie połączonej organizacji](../articles/active-directory/governance/entitlement-management-organization.md).

1. Po wybraniu wszystkich połączonych organizacji kliknij pozycję **Wybierz**.

    > [!NOTE]
    > Wszyscy użytkownicy z wybranych połączonych organizacji będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników w usłudze Azure AD ze wszystkich poddomen skojarzonych z organizacją, chyba że te domeny są blokowane przez listę dozwolonych lub zablokowanych warunków B2B platformy Azure. Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](../articles/active-directory/b2b/allow-deny-list.md).

1. Przejdź do sekcji [zatwierdzenie](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Brak (tylko przypisania bezpośrednie do administratorów)

Wykonaj następujące kroki, aby obejść żądania dostępu i umożliwić administratorom bezpośrednie przypisanie określonych użytkowników do tego pakietu dostępu. Użytkownicy nie będą musieli żądać pakietu dostępu. Nadal można ustawiać ustawienia cyklu życia, ale nie ma żadnych ustawień żądania.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **Brak (tylko przypisania bezpośrednie administratora**).

    ![Dostęp do pakietów — brak przypisań tylko administrator](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Po utworzeniu pakietu dostępu można bezpośrednio przypisać określonych użytkowników wewnętrznych i zewnętrznych do pakietu dostępu. W przypadku określenia użytkownika zewnętrznego w katalogu zostanie utworzone konto użytkownika-gościa. Aby uzyskać informacje na temat bezpośredniego przypisywania użytkownika, zobacz [Wyświetlanie, Dodawanie i usuwanie przypisań dla pakietu dostępu](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Przejdź do sekcji [Włączanie żądań](#enable-requests) .

## <a name="approval"></a>Zatwierdzenie

W sekcji zatwierdzenie należy określić, czy zatwierdzenie ma być wymagane, jeśli użytkownicy zażądają tego pakietu dostępu. Ustawienia zatwierdzania działają w następujący sposób:

- Tylko jeden z wybranych osób zatwierdzających lub osoby zatwierdzające rezerw muszą zatwierdzić żądanie. Zatwierdzenie ze wszystkich osób zatwierdzających nie jest wymagane.
- Decyzja o zatwierdzeniu zależy od osoby zatwierdzającej, która najpierw przegląda żądanie.

Wykonaj następujące kroki, aby określić ustawienia zatwierdzania dla wcześniej wybranych użytkowników.

1. Aby wymagać zatwierdzenia dla żądań od wybranych użytkowników, ustaw przełącznik **Wymagaj zatwierdzenia** na **wartość tak**. Aby żądania były automatycznie zatwierdzane, ustaw przełącznik na wartość **nie**.

    ![Dostęp do pakietów — ustawienia zatwierdzenia](./media/active-directory-entitlement-management-request-policy/approval.png)

1. Aby wymagać od użytkowników podania uzasadnienia żądania pakietu dostępu, ustaw przełącznik Żądaj **uzasadnienia żądającego** na **wartość tak**.

1. Ustal, czy żądanie będzie wymagało zatwierdzenia pojedynczego lub wieloetapowego. Ustaw liczbę przełączeń między **etapami** na **1** dla jednego etapu.

1. W obszarze osoby zatwierdzające wybierz pozycję **Menedżer jako osoba zatwierdzająca** lub **Wybierz określone osoby zatwierdzające**.

    Menedżer jest określany przez atrybut **Manager** w profilu użytkownika usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika przy użyciu Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

    ![Profil użytkownika Azure Active Directory — atrybut Manager](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. W przypadku wybrania opcji Menedżer jako osoby zatwierdzającej kliknij pozycję **Dodaj rezerwę** , aby wybrać co najmniej jednego użytkownika lub grupy w Twoim katalogu jako osobę zatwierdzającą rezerwowe w przypadku, gdy zarządzanie uprawnieniami nie może znaleźć Menedżera.

1. W przypadku wybrania opcji wybierz określone osoby zatwierdzające kliknij przycisk **Dodaj osoby zatwierdzające** , aby wybrać co najmniej jednego użytkownika lub grupę w katalogu, który ma zostać zatwierdzony.

1. W ramach **decyzji musi zostać podjęta w ciągu kilku dni?** Określ liczbę dni, przez które osoba zatwierdzająca musi przejrzeć żądanie tego pakietu dostępu.

    Jeśli żądanie nie zostanie zatwierdzone w tym okresie, zostanie automatycznie odrzucone. Użytkownik będzie musiał przesłać kolejne żądanie dotyczące pakietu dostępu.

1. Aby wymagać od użytkowników podania uzasadnienia żądania pakietu dostępu, ustaw opcję **Wymagaj uzasadnienia** na **wartość tak**.

    Uzasadnienie jest widoczne dla innych osób zatwierdzających i osoby zgłaszającej żądanie.

## <a name="enable-requests"></a>Włącz żądania

1. Jeśli chcesz, aby pakiet dostępu został natychmiast udostępniony dla użytkowników w zasadach żądania, kliknij przycisk **tak** , aby włączyć.

    Zawsze możesz włączyć ją w przyszłości po zakończeniu tworzenia pakietu dostępu.

    W przypadku wybrania opcji **Brak (tylko przypisania bezpośrednie do administratorów)** i skonfigurowania opcji Włącz na **nie**Administratorzy nie będą mogli bezpośrednio przypisywać tego pakietu dostępu.

    ![Dostęp do pakietu — ustawienie zasad włączania zasad](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Kliknij przycisk **Dalej**.
