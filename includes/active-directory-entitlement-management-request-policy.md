---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 11/11/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: c73a62b2d5feeae42a5ea35c88073dd5fcc0d78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77193026"
---
## <a name="for-users-in-your-directory"></a>Dla użytkowników w katalogu

Wykonaj następujące kroki, jeśli chcesz zezwolić użytkownikom w katalogu, aby mogli zażądać tego pakietu dostępu. Podczas definiowania zasad żądania można określić poszczególnych użytkowników lub częściej grupy użytkowników. Na przykład organizacja może mieć już grupę, taką jak **Wszyscy pracownicy.**  Jeśli ta grupa zostanie dodana do zasad dla użytkowników, którzy mogą żądać dostępu, każdy członek tej grupy może zażądać dostępu.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu,** kliknij pozycję **Dla użytkowników w katalogu**.

    Po wybraniu tej opcji pojawią się nowe opcje, aby dodatkowo uściślić, kto w katalogu może zażądać tego pakietu dostępu.

    ![Pakiet dostępu — żądania — dla użytkowników w katalogu](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Konkretni użytkownicy i grupy** | Wybierz tę opcję, jeśli chcesz, aby tylko użytkownicy i grupy w katalogu, które określisz, mogły żądać tego pakietu dostępu. |
    | **Wszyscy członkowie (z wyłączeniem gości)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy elementów członkowskich w katalogu mogli zażądać tego pakietu dostępu. Ta opcja nie obejmuje żadnych użytkowników-gości, których zaproszono do katalogu. |
    | **Wszyscy użytkownicy (w tym goście)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy członkowskie i użytkownicy-goście w katalogu mogli zażądać tego pakietu dostępu. |

    Użytkownicy-goście odnoszą się do użytkowników zewnętrznych, którzy zostali zaproszeni do katalogu za pomocą [usługi Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Aby uzyskać więcej informacji na temat różnic między użytkownikami członkowskimi a użytkownikami-gośćmi, zobacz [Jakie są domyślne uprawnienia użytkowników w usłudze Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Jeśli wybrano **określonych użytkowników i grupy,** kliknij pozycję **Dodaj użytkowników i grupy**.

1. W okienku Wybierz użytkowników i grupy wybierz użytkowników i grupy, które chcesz dodać.

    ![Pakiet dostępu — żądania — wybieranie użytkowników i grup](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Kliknij **przycisk Wybierz,** aby dodać użytkowników i grupy.

1. Przejdź w dół do sekcji [Zatwierdzanie.](#approval)

## <a name="for-users-not-in-your-directory"></a>Dla użytkowników, którzy nie mają w katalogu

 **Użytkownicy niew katalogu** odnosi się do użytkowników, którzy znajdują się w innym katalogu usługi Azure AD lub domeny. Ci użytkownicy mogą nie zostać jeszcze zaproszeni do katalogu. Katalogi usługi Azure AD muszą być skonfigurowane tak, aby zezwalały na zaproszenia w **ograniczeniach współpracy.** Aby uzyskać więcej informacji, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać gości.](../articles/active-directory/b2b/delegate-invitations.md)

> [!NOTE]
> Konto użytkownika-gościa zostanie utworzone dla użytkownika, który nie znajduje się jeszcze w katalogu, którego żądanie jest zatwierdzone lub automatycznie zatwierdzone. Gość zostanie zaproszony, ale nie otrzyma e-maila z zaproszeniem. Zamiast tego otrzymają wiadomość e-mail po dostarczeniu przypisania pakietu dostępu. Domyślnie później, gdy ten użytkownik-gość nie ma już żadnych przypisań pakietów dostępu, ponieważ ich ostatnie przypisanie wygasło lub zostało anulowane, to konto użytkownika gościa zostanie zablokowane przed logowaniem, a następnie usunięte. Jeśli chcesz, aby użytkownicy-goście pozostali w katalogu przez czas nieokreślony, nawet jeśli nie mają przypisania pakietów dostępu, możesz zmienić ustawienia konfiguracji zarządzania uprawnieniami. Aby uzyskać więcej informacji na temat obiektu użytkownika-gościa, zobacz [Właściwości użytkownika współpracy usługi Azure Active Directory B2B](../articles/active-directory/b2b/user-properties.md).

Wykonaj następujące kroki, aby zezwolić użytkownikom, którzy nie mają w katalogu, na żądanie tego pakietu dostępu:

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu,** kliknij pozycję **Dla użytkowników, którzy nie mają w katalogu**.

    Po wybraniu tej opcji pojawią się nowe opcje.

    ![Pakiet dostępu — żądania — dla użytkowników niew katalogu](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Konkretne połączone organizacje** | Wybierz tę opcję, jeśli chcesz wybrać z listy organizacji, które administrator wcześniej dodał. Wszyscy użytkownicy z wybranych organizacji mogą zażądać tego pakietu dostępu. |
    | **Wszystkie połączone organizacje** | Wybierz tę opcję, jeśli wszyscy użytkownicy ze wszystkich połączonych organizacji mogą zażądać tego pakietu dostępu. |
    | **Wszyscy użytkownicy (wszystkie połączone organizacje + nowi użytkownicy zewnętrzni)** | Wybierz tę opcję, jeśli wszyscy użytkownicy ze wszystkich połączonych organizacji mogą zażądać tego pakietu dostępu i że ustawienia listy zezwalają lub odrzucaj b2B powinny mieć pierwszeństwo dla każdego nowego użytkownika zewnętrznego. |

    Połączona organizacja to zewnętrzny katalog lub domena usługi Azure AD, z którą masz relację.

1. Jeśli wybrano **opcję Określone połączone organizacje,** kliknij pozycję **Dodaj katalogi,** aby wybrać z listy połączonych organizacji, które administrator wcześniej dodał.

1. Wpisz nazwę lub nazwę domeny, aby wyszukać wcześniej połączoną organizację.

    ![Pakiet dostępu — żądania — wybieranie katalogów](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Jeśli organizacji, z którą chcesz współpracować, nie ma na liście, możesz poprosić administratora o dodanie jej jako połączonej organizacji. Aby uzyskać więcej informacji, zobacz [Dodawanie połączonej organizacji](../articles/active-directory/governance/entitlement-management-organization.md).

1. Po wybraniu wszystkich połączonych organizacji kliknij pozycję **Wybierz**.

    > [!NOTE]
    > Wszyscy użytkownicy z wybranych połączonych organizacji będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników w usłudze Azure AD ze wszystkich poddomen skojarzonych z organizacją, chyba że te domeny są blokowane przez listę zezwalania lub odrzucania platformy Azure B2B. Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń do użytkowników B2B z określonych organizacji](../articles/active-directory/b2b/allow-deny-list.md).

1. Przejdź w dół do sekcji [Zatwierdzanie.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>Brak (tylko przypisania bezpośrednie administratora)

Wykonaj następujące kroki, jeśli chcesz pominąć żądania dostępu i zezwolić administratorom na bezpośrednie przypisywanie określonych użytkowników do tego pakietu dostępu. Użytkownicy nie będą musieli żądać pakietu dostępu. Nadal można ustawić ustawienia cyklu życia, ale nie ma żadnych ustawień żądania.

1. W sekcji **Użytkownicy, którzy mogą żądać dostępu,** kliknij pozycję **Brak (tylko przypisania bezpośrednie administratora**.

    ![Pakiet dostępu — żądania — brak bezpośrednich przypisań administratora](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Po utworzeniu pakietu dostępu można bezpośrednio przypisać określonych użytkowników wewnętrznych i zewnętrznych do pakietu dostępu. Jeśli określisz użytkownika zewnętrznego, w katalogu zostanie utworzone konto użytkownika-gościa. Aby uzyskać informacje dotyczące bezpośredniego przypisywania użytkownika, zobacz [Wyświetlanie, dodawanie i usuwanie przypisań dla pakietu dostępu](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Przejdź w dół do sekcji [Włącz żądania.](#enable-requests)

## <a name="approval"></a>Zatwierdzenie

W sekcji Zatwierdzanie można określić, czy zatwierdzenie jest wymagane, gdy użytkownicy żądają tego pakietu dostępu. Ustawienia zatwierdzenia działają w następujący sposób:

- Tylko jeden z wybranych osób zatwierdzających lub rezerwowych osób zatwierdzających musi zatwierdzić wniosek o zatwierdzenie jednoetapowe. 
- Tylko jeden z wybranych osób zatwierdzających z każdego etapu musi zatwierdzić wniosek o zatwierdzenie dwuetapowe.
- Osoba zatwierdzająca może być menedżerem, sponsorem wewnętrznym lub zewnętrznym w zależności od tego, kto zarządza dostępem.
- Zatwierdzenie przez każdą wybraną osobę zatwierdzającej nie jest wymagane do zatwierdzania jedno- lub dwuetapowego.
- Decyzja zatwierdzająca opiera się na tym, który zatwierdzający najpierw rozpatrzy wniosek.

Aby zademonstrować sposób dodawania osób zatwierdzających do zasad żądania, obejrzyj następujący film:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Aby zademonstrować sposób dodawania wieloetapowej zgody na zatwierdzanie do zasad żądania, obejrzyj następujący film:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Wykonaj następujące kroki, aby określić ustawienia zatwierdzania żądań dla pakietu dostępu:

1. Aby wymagać zatwierdzenia żądań od wybranych użytkowników, należy ustawić przełącznik **Wymagaj zatwierdzenia** na **Tak**. Aby żądania były automatycznie zatwierdzane, ustaw przełącznik na **Nie**.

1. Aby wymagać od użytkowników podania uzasadnienia żądania pakietu dostępu, należy ustawić przełącznik **Wymagaj żądania lub uzasadnienia** na **Tak**.
    
1. Teraz określ, czy żądania będą wymagały jedno- czy dwuetapowego zatwierdzenia. Ustaw przełącznik **Ile etapów** na **1** dla zatwierdzania jednostopniowego lub ustaw przełącznik na **2** dla zatwierdzenia dwuetapowego.

    ![Pakiet dostępu — żądania — ustawienia zatwierdzania](./media/active-directory-entitlement-management-request-policy/approval.png)

Aby dodać osoby zatwierdzające, należy wykonać następujące czynności po wybraniu liczby wymaganych etapów: 

### <a name="single-stage-approval"></a>Zatwierdzenie jednoetapowe

1. Dodaj **pierwszą osobę zatwierdzającej:**
    
    Jeśli zasada jest ustawiona tak, aby regulować dostęp dla użytkowników w katalogu, można wybrać **Menedżera jako osobę zatwierdzaącą**. Możesz też dodać określonego użytkownika, klikając pozycję **Dodaj osoby zatwierdzające** po wybraniu z menu rozwijanego Wybierz określone osoby zatwierdzające.
    
    ![Pakiet dostępu — żądania — dla użytkowników w katalogu — pierwsza osoba zatwierdzająca](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Jeśli ta zasada jest ustawiona tak, aby regulować dostęp dla użytkowników, którzy nie mają w katalogu, można wybrać **sponsora zewnętrznego** lub **sponsora wewnętrznego**. Możesz też dodać określonego użytkownika, klikając pozycję **Dodaj osoby zatwierdzające** lub grupy w obszarze Wybierz określone osoby zatwierdzające.
    
    ![Pakiet dostępu — żądania — dla użytkowników z katalogu — pierwsza osoba zatwierdzająca](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Jeśli jako pierwszą osobę zatwierdzającej wybrano **Menedżera,** kliknij przycisk **Dodaj rezerwowe,** aby wybrać jednego lub więcej użytkowników lub grup w katalogu jako osobę zatwierdzaącą rezerwową. Osoby zatwierdzające rezerwowe otrzymują żądanie, jeśli zarządzanie uprawnieniami nie może znaleźć menedżera dla użytkownika żądającego dostępu.

    Menedżer jest odnaleziony przez zarządzanie uprawnieniami przy użyciu atrybutu **Manager.** Atrybut znajduje się w profilu użytkownika w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika przy użyciu usługi Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Jeśli wybrano **opcję Wybierz określone osoby zatwierdzające,** kliknij pozycję Dodaj osoby **zatwierdzające,** aby wybrać jednego lub więcej użytkowników lub grup w katalogu, które mają być osobami zatwierdzających.

1. W polu w obszarze **Decyzja musi być podjęta w ilu dniach?**, określić liczbę dni, przez które osoba zatwierdzająca musi przejrzeć wniosek o ten pakiet dostępu.

    Jeśli żądanie nie zostanie zatwierdzone w tym okresie, zostanie automatycznie odrzucone. Użytkownik będzie musiał przesłać kolejne żądanie pakietu dostępu.

1. Aby wymagać od osób zatwierdzających przedstawienia uzasadnienia swojej decyzji, ustaw polecenie Wymagaj uzasadnienia osoby zatwierdzającej na **Tak**.

    Uzasadnienie jest widoczne dla innych osób zatwierdzających i osoby wnioskującej.

### <a name="2-stage-approval-preview"></a>Homologacja dwustopniowa (wersja zapoznawcza)

Jeśli wybrano zatwierdzenie dwuetapowe, musisz dodać drugą osobę zatwierdzającej.

1. Dodaj **drugą osobę zatwierdzającej:** 
    
    Jeśli użytkownicy znajdują się w katalogu, dodaj określonego użytkownika jako drugiego osobę zatwierdzającą, klikając **pozycję Dodaj osoby zatwierdzające** w obszarze Wybierz konkretne osoby zatwierdzające.

    ![Pakiet dostępu — żądania — dla użytkowników w katalogu — druga osoba zatwierdzająca](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Jeśli użytkownicy nie są w katalogu, wybierz **sponsora wewnętrznego** lub **sponsora zewnętrznego** jako drugą osobę zatwierdzającej. Po wybraniu osoby zatwierdzającej dodaj osoby zatwierdzające rezerwowe.

    ![Pakiet dostępu — żądania — dla użytkowników z katalogu — druga osoba zatwierdzająca](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Określ liczbę dni, przez które druga osoba zatwierdzająca musi zatwierdzić żądanie w polu w obszarze **Decyzja musi zostać podjęta w ciągu kilku dni?**. 

1. Ustaw opcję Wymagaj, aby uzasadnienie osoby zatwierdzającej było ustawione na **Tak** lub **Nie**.

### <a name="alternate-approvers"></a>Osoby zatwierdzające alternatywne

Można określić alternatywne osoby zatwierdzające, podobnie jak określanie pierwszej i drugiej osoby zatwierdzającej, które mogą zatwierdzać żądania. Posiadanie alternatywnych osób zatwierdzających pomoże zapewnić, że żądania są zatwierdzane lub odrzucane przed ich wygaśnięciem (limit czasu). Do zatwierdzenia dwuetapowego można wyświetlić listę osób zatwierdzających na liście osób zatwierdzających, które mają być wystawiane jako osoby zatwierdzające. 

Określając alternatywne osoby zatwierdzające, w przypadku, gdy pierwsza lub druga osoba zatwierdzająca nie może zatwierdzić lub odrzucić żądania, oczekujące żądanie zostanie przekazane do alternatywnych osób zatwierdzających zgodnie z harmonogramem przekazywania określonym podczas konfigurowania zasad. Otrzymują wiadomość e-mail, aby zatwierdzić lub odrzucić oczekujące żądanie.

Po przesuniętym żądania do innych osób zatwierdzających pierwsza lub druga osoba zatwierdzająca może nadal zatwierdzać lub odrzucać żądanie. Osoby zatwierdzające alternatywne używają tej samej witryny Mój dostęp do zatwierdzania lub odrzucania oczekującego żądania.

Możemy wymienić osoby lub grupy osób, które mają być osobami zatwierdzające i alternatywnymi osobami zatwierdzających. Upewnij się, że lista różnych zestawów osób, które mają być pierwszym, drugim i alternatywnym osobami zatwierdzających.
Jeśli na przykład alicja i Robert są wymieniane jako osoby zatwierdzające jako osoby zatwierdzające, jako osoby zatwierdzające jako osoby zatwierdzające jako osoby zatwierdzające. Aby dodać alternatywne osoby zatwierdzające do pakietu dostępu, należy wykonać następujące czynności:

1. W obszarze Pierwsza osoba zatwierdzająca, druga osoba zatwierdzająca lub obie te opcje kliknij pozycję **Pokaż ustawienia żądań zaawansowanych**.

    ![Pakiet dostępu — zasady — wyświetlanie zaawansowanych ustawień żądań](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Ustaw, **jeśli nie podjęto żadnych działań, prześlij do alternatywnych osób zatwierdzających?** **Yes**

1. Kliknij **pozycję Dodaj alternatywne osoby zatwierdzające** i wybierz z listy alternatywną osobę zatwierdzających.

    ![Pakiet dostępu — zasady — dodawanie alternatywnych osób zatwierdzających](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. W polu **Przekaż do alternatywnych osób zatwierdzających po liczbie dni** umieść w polu liczba dni, przez które osoby zatwierdzające muszą zatwierdzić lub odrzucić żądanie. Jeśli żadna osoba zatwierdzająca nie zatwierdziła lub nie odrzuciła żądania przed czasem trwania żądania, żądanie wygasa (limit czasu), a użytkownik będzie musiał przesłać inne żądanie dla pakietu dostępu. 

    Żądania mogą być przekazywane tylko do alternatywnych osób zatwierdzających dzień po upływie okresu półtrwania żądania. W tym przykładzie czas trwania żądania wynosi 14 dni. Tak więc czas trwania żądania osiąga okres półtrwania w dniu 7. Dlatego nie można przesłać dalej żądania wcześniej niż dzień 8. Ponadto żądania nie mogą być przekazywane w ostatnim dniu trwania żądania. Tak więc w przykładzie najnowsze żądanie może być przekazane jest dzień 13.

## <a name="enable-requests"></a>Włączanie żądań

1. Jeśli chcesz, aby pakiet dostępu był natychmiast udostępniany użytkownikom w zasadach żądań, przesuń przełącznik Włącz na **Tak**.

    Zawsze można włączyć go w przyszłości po zakończeniu tworzenia pakietu dostępu.

    Jeśli wybrano **opcję Brak (tylko przypisania bezpośrednie administratora)** i ustawiono opcję **Nie,** administratorzy nie będą mogli bezpośrednio przypisać tego pakietu dostępu.

    ![Pakiet dostępu — zasady — włączanie ustawień zasad](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Kliknij przycisk **alej**.
