---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 07/31/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: db16a2f122da1bf6c767e0a47c93c22f1882c406
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817237"
---
### <a name="policy-for-users-in-your-directory"></a>Zasady: Dla użytkowników w katalogu

Wykonaj te kroki, jeśli chcesz, aby zasady były przeznaczone dla użytkowników w katalogu, którzy mogą zażądać tego pakietu dostępu.  **Użytkownicy w katalogu** odwołują się zarówno do użytkowników wewnętrznych, jak i użytkowników zewnętrznych, którzy zostali wcześniej zaproszeni do katalogu, przez ich żądanie zarządzania uprawnieniami z innym pakietem dostępu lub zapraszanie go za pomocą B2B usługi Azure AD. Podczas definiowania zasad można określić poszczególnych użytkowników lub kilka często grup użytkowników. Na przykład organizacja może mieć już grupę, taką jak **Wszyscy pracownicy**.  Jeśli ta grupa zostanie dodana w zasadach dla użytkowników, którzy mogą żądać dostępu, wówczas każdy członek tej grupy może następnie zażądać dostępu.

1. W sekcji **Użytkownicy, którzy mogą żądać dostępu** , wybierz pozycję **dla użytkowników w katalogu**.

    Należy pamiętać, że ustawienie **dla użytkowników w katalogu** obejmuje zarówno użytkowników składowych, jak i użytkowników-Gości, którzy zostali dodani do katalogu. Jeśli chcesz uwzględnić tylko użytkowników należących do członków, a nie użytkowników-Gości, wybierz pozycję **dla użytkowników w katalogu** , a następnie wybierz grupę użytkowników należących do członków. W razie potrzeby można utworzyć grupę dynamiczną użytkowników należących do członków (User. UserType-EQ "member"). Aby uzyskać więcej informacji, zobacz [dynamiczne reguły członkostwa dla grup w Azure Active Directory](../articles/active-directory/users-groups-roles/groups-dynamic-membership.md).

1. W sekcji **Wybieranie użytkowników i grup** kliknij pozycję **Dodaj użytkowników i grupy**.

1. W okienku wybierz użytkowników i grupy wybierz użytkowników i grupy, które chcesz dodać.

    ![Dostęp do pakietu-zasady — Wybieranie użytkowników i grup](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Kliknij pozycję **Wybierz** , aby dodać użytkowników i grupy.

1. Przejdź do [zasad: Sekcja](#policy-request) żądania.

### <a name="policy-for-users-not-in-your-directory"></a>Zasady: Dla użytkowników nieznajdujących się w katalogu

Wykonaj te kroki, jeśli chcesz, aby zasady były przeznaczone dla użytkowników, którzy nie należą do katalogu, którzy mogą zażądać tego pakietu dostępu. Użytkownicy znajdujący się **w katalogu nie** są użytkownikami, którzy znajdują się w innym katalogu usługi Azure AD i mogą nie zostać zaproszeni do katalogu.  Katalogi muszą być skonfigurowane tak, aby były dozwolone w ustawieniach **ograniczeń organizacyjnych relacje współpracy** .

> [!NOTE]
> Zostanie utworzone konto użytkownika zewnętrznego gościa dla użytkownika, którego jeszcze nie ma w Twoim katalogu, którego żądanie zostało zatwierdzone lub którego zatwierdzeni zostanie zaakceptowany. Gość zostanie zaproszony, ale nie otrzyma wiadomości e-mail z zaproszeniem. Zamiast tego otrzymają wiadomość e-mail, gdy zostanie dostarczone przypisanie pakietu dostępu. Domyślnie, gdy użytkownik tego gościa nie ma już żadnych przypisań pakietów dostępu, ponieważ ostatnie przypisanie wygasło lub zostało anulowane, konto użytkownika Gość zostanie zablokowane przed zalogowaniem się i usunięciem. Jeśli chcesz, aby użytkownicy-Goście pozostali w Twoim katalogu przez czas nieokreślony, nawet jeśli nie mają przypisanych pakietów dostępu, możesz zmienić ustawienia konfiguracji zarządzania uprawnieniami.

1. W sekcji **Użytkownicy, którzy mogą żądać dostępu** , wybierz opcję **dla użytkowników**nienależących do katalogu.

1. W sekcji **wybierz zewnętrzny katalog usługi Azure AD** kliknij pozycję **Dodaj katalogi**.

1. Wprowadź nazwę domeny i Wyszukaj katalog usługi Azure AD przy użyciu tej nazwy domeny.

1. Sprawdź, czy jest to poprawny katalog według podanej nazwy katalogu i domeny początkowej.

    > [!NOTE]
    > Wszyscy użytkownicy z katalogu będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników ze wszystkich poddomen skojarzonych z katalogiem, a nie tylko do domeny użytej w wyszukiwaniu.

    ![Dostęp do pakietu-zasady-wybieranie katalogów](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Kliknij przycisk **Dodaj** , aby dodać katalog.

1. Powtórz ten krok, aby dodać więcej katalogów.

1. Po dodaniu wszystkich katalogów, które chcesz dołączyć do zasad, kliknij przycisk **Wybierz**.

1. Przejdź do [zasad: Sekcja](#policy-request) żądania.

### <a name="policy-none-administrator-direct-assignments-only"></a>Zasady: Brak (tylko przypisania bezpośrednie do administratorów)

Wykonaj te kroki, jeśli chcesz, aby zasady pomijają żądania dostępu, a administratorzy mogą bezpośrednio przypisywać określonych użytkowników do pakietu dostępu. Użytkownicy nie będą musieli żądać pakietu dostępu. Nadal można ustawić ustawienia wygasania, ale nie ma żadnych ustawień żądania.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , wybierz opcję **Brak (tylko przypisania bezpośrednie administratora**.

    Po utworzeniu pakietu dostępu można bezpośrednio przypisać określonych użytkowników wewnętrznych i zewnętrznych do pakietu dostępu. W przypadku określenia użytkownika zewnętrznego w katalogu zostanie utworzone konto użytkownika-gościa.

1. Przejdź do [zasad: Sekcja](#policy-expiration) wygasania.

### <a name="policy-request"></a>Zasady: Żądanie

W sekcji żądanie Określ ustawienia zatwierdzania, gdy użytkownicy zażądają pakietu dostępu.

1. Aby wymagać zatwierdzenia dla żądań od wybranych użytkowników, ustaw przełącznik **Wymagaj zatwierdzenia** na **wartość tak**. Aby żądania były automatycznie zatwierdzane, ustaw przełącznik na wartość **nie**.

1. Jeśli wymagane jest zatwierdzenie, w sekcji **Wybierz osoby zatwierdzające** kliknij przycisk **Dodaj osoby zatwierdzające**.

1. W okienku wybierz osoby zatwierdzające wybierz co najmniej jednego użytkownika i/lub grupy, które mają być zatwierdzane.

    Tylko jeden z wybranych osób zatwierdzających musi zatwierdzić żądanie. Zatwierdzenie ze wszystkich osób zatwierdzających nie jest wymagane. Decyzja o zatwierdzeniu zależy od osoby zatwierdzającej, która najpierw przegląda żądanie.

    ![Dostęp do pakietu-zasady-wybierz osoby zatwierdzające](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Kliknij pozycję **Wybierz** , aby dodać osoby zatwierdzające.

1. Kliknij pozycję **Pokaż zaawansowane ustawienia żądania** , aby wyświetlić dodatkowe ustawienia.

    ![Dostęp do pakietu-zasady-wybieranie katalogów](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Aby wymagać od użytkowników podania uzasadnienia żądania pakietu dostępu, ustaw opcję **Wymagaj uzasadnienia** na **wartość tak**.

1. Aby wymagać od osoby zatwierdzającej uzasadnienia zatwierdzenia żądania dla pakietu dostępu, ustaw opcję **Wymagaj zatwierdzenia osoby zatwierdzającej** na **wartość tak**.

1. W polu **limit czasu żądania zatwierdzenia (w dniach)** określ ilość czasu, przez który osoby zatwierdzające muszą przejrzeć żądanie. Jeśli żadne osoby zatwierdzające nie przeglądają go w ciągu następującej liczby dni, żądanie wygasa i użytkownik będzie musiał przesłać kolejne żądanie dotyczące pakietu dostępu.

### <a name="policy-expiration"></a>Zasady: Wygaśnięcie

W sekcji wygaśnięcie Określ, kiedy wygasa przypisanie użytkownika do pakietu dostępu.

1. W sekcji **wygaśnięcie** ustaw pozycję **pakiet dostępu** na wartość **Data**, **Liczba dni**lub **nigdy**.

    Na **dzień**wybierz datę wygaśnięcia w przyszłości.

    Dla **liczby dni**Określ liczbę z zakresu od 0 do 3660 dni.

    W zależności od dokonanego wyboru przypisanie użytkownika do pakietu dostępu wygasa w określonym dniu, przez określoną liczbę dni po zatwierdzeniu lub nigdy.

1. Kliknij pozycję **Pokaż zaawansowane ustawienia wygasania** , aby wyświetlić dodatkowe ustawienia.

1. Aby zezwolić użytkownikowi na rozszeranie swoich przypisań, ustaw opcję **zezwól użytkownikom na rozszerzone dostęp** na **wartość tak**.

    Jeśli w zasadach są dozwolone rozszerzenia, użytkownik otrzyma wiadomość e-mail 14 dni, a także 1 dzień przed przypisaniem pakietu dostępu zostanie ustawiony na wygaśnięcie monitu o rozszerzenie przypisania.

    ![Dostęp do pakietu-zasady-ustawienia wygasania](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Zasady: Włączanie zasad

1. Jeśli chcesz, aby pakiet dostępu został natychmiast udostępniony użytkownikom w ramach zasad, kliknij przycisk **tak** , aby włączyć zasady.

    Zawsze możesz włączyć ją w przyszłości po zakończeniu tworzenia pakietu dostępu.

    ![Dostęp do pakietu — ustawienie zasad włączania zasad](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Kliknij przycisk **dalej** lub **Utwórz**.
