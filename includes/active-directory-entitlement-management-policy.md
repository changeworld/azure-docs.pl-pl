---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: ef8155cda6c31a63204af80ca091a13bb16a687d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866435"
---
### <a name="policy-for-users-in-your-directory"></a>Zasady: Dla użytkowników w katalogu

Wykonaj następujące kroki, jeśli chcesz, aby Twoje zasady dla użytkowników i grup w katalogu, jakiej może żądać tego pakietu dostępu.

1. W **użytkowników, którzy mogą zażądać dostępu** zaznacz **dla użytkowników w katalogu**.

1. W **Wybieranie użytkowników i grup** kliknij **dodanie użytkowników i grup**.

1. W okienku grupy i wybierz użytkowników wybierz użytkowników i grup, które chcesz dodać.

    ![Pakiet dostępu — zasad — wybierz użytkowników i grup](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Kliknij przycisk **wybierz** do dodawania użytkowników i grup.

1. Przejdź w dół do [zasad: Żądanie](#policy-request) sekcji.

### <a name="policy-for-users-not-in-your-directory"></a>Zasady: Dla użytkowników spoza katalogu

Jeśli chcesz, aby zasady dla użytkowników spoza katalogu, jakiej może żądać tego pakietu dostęp, wykonaj następujące kroki. Katalogi musi być skonfigurowany tak, aby dozwolone było w **ograniczenia współpracy relacje w organizacji** ustawienia.

1. W **użytkowników, którzy mogą zażądać dostępu** zaznacz **dla użytkowników spoza katalogu**.

1. W **wybierz zewnętrznych usługi Azure AD directory** kliknij **dodać katalogi**.

1. Wprowadź nazwę domeny i wyszukaj zewnętrznego katalogu usługi Azure AD.

1. Sprawdź, czy jest to prawidłowy katalog według nazwy podany katalog i domeny początkowej.

    > [!NOTE]
    > Wszyscy użytkownicy z katalogu będzie można zażądać tego dostępu do pakietu. Obejmuje to użytkowników z wszystkimi domenami podrzędnymi skojarzone z katalogu, nie tylko domeny, które są używane w wyszukiwaniu.

    ![Pakiet dostępu — zasad — Wybierz katalogi](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Kliknij przycisk **Dodaj** można dodać katalogu.

1. Powtórz ten krok można dodać żadnych katalogów.

1. Po dodaniu wszystkich katalogów chcesz uwzględnić w zasadach, kliknij przycisk **wybierz**.

1. Przejdź w dół do [zasad: Żądanie](#policy-request) sekcji.

### <a name="policy-none-administrator-direct-assignments-only"></a>Zasady: Brak (administrator przypisań bezpośrednich tylko)

Jeśli chcesz, aby zasady, aby ominąć żądania dostępu i umożliwiają administratorom bezpośrednio przypisać określonych użytkowników do pakietów programu access, wykonaj następujące kroki. Użytkownicy nie będą musieli żądania dostępu do pakietu. Można skonfigurować ustawienia wygaśnięcia, ale nie ma żadnych ustawień żądania.

1. W **użytkowników, którzy mogą zażądać dostępu** zaznacz **None (administrator tylko przypisań bezpośrednich**.

    Po utworzeniu pakietu dostęp do określonych użytkowników wewnętrznych i zewnętrznych można przypisać bezpośrednio do dostępu do pakietu. Jeśli określisz użytkownika zewnętrznego, konto użytkownika gościa zostanie utworzony w katalogu.

1. Przejdź w dół do [zasad: Wygaśnięcie](#policy-expiration) sekcji.

### <a name="policy-request"></a>Zasady: Żądanie

W sekcji żądania należy określić ustawienia zatwierdzania po użytkownik zażąda dostępu do pakietu.

1. Aby wymagać zatwierdzenia dla żądań z wybranych użytkowników, należy ustawić **wymagają zatwierdzenia** Przełącz, aby **tak**. Aby automatycznie zatwierdzone żądania, ustaw przełącznik w pozycji **nie**.

1. Jeśli potrzebujesz zatwierdzenia w **wybierz osoby zatwierdzające** kliknij **Dodaj zatwierdzających**.

1. W okienku wybierz osoby zatwierdzające wybierz jeden lub więcej użytkowników i/lub grupy jako osób zatwierdzających.

    Tylko jeden z wybranych osób zatwierdzających musi zatwierdzić żądanie. Zatwierdzenie wszystkich osób zatwierdzających nie jest wymagana. Decyzji o zatwierdzeniu opiera się na niezależnie od osoba zatwierdzająca przegląda najpierw.

    ![Pakiet dostępu — zasad — wybierz osoby zatwierdzające](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Kliknij przycisk **wybierz** dodać osób zatwierdzających.

1. Kliknij przycisk **Pokaż zaawansowane ustawienia żądania** wyświetlanie dodatkowych ustawień.

    ![Pakiet dostępu — zasad — Wybierz katalogi](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Aby użytkownicy musieli podać uzasadnienie, aby zażądać dostępu do pakietu, należy ustawić **wymaga uzasadnienia** do **tak**.

1. Aby wymagać od osoby zatwierdzającej podać uzasadnienie, aby zatwierdzić żądanie dostępu do pakietu, należy ustawić **wymaga uzasadnienia osoba zatwierdzająca** do **tak**.

1. W **limit czasu żądania zatwierdzenia (w dniach)** Określ ilość czasu osób zatwierdzających musi przejrzeć żądania. Jeśli żadne osoby zatwierdzające je przejrzeć w następującej liczbie dni, żądanie wygaśnie, a użytkownik będzie musiał przesłać kolejne żądanie dostępu do pakietu.

### <a name="policy-expiration"></a>Zasady: Wygaśnięcie

W sekcji wygaśnięcia należy określić, kiedy wygasa przypisanie użytkownika do dostępu do pakietu.

1. W **wygaśnięcia** sekcji, ustaw **pakiet dostępu wygasa** do **w dniu**, **liczbę dni**, lub **nigdy**.

    Aby uzyskać **w dniu**, wybierz przyszłą datę wygaśnięcia.

    Aby uzyskać **liczbę dni**, określ liczbę z zakresu od 0 do 3660 dni.

    Na podstawie dokonanego wyboru, przypisanie użytkownika do pakietu dostępu wygasa po określonej dacie określonej liczbie dni, po ich zatwierdzeniu lub nigdy.

1. Kliknij przycisk **Pokaż zaawansowane ustawienia wygaśnięcia** wyświetlanie dodatkowych ustawień.

1. Aby zezwolić użytkownikowi rozszerzyć swoje przypisania, ustaw **Zezwalaj użytkownikom na rozszerzanie dostępu** do **tak**.

    Jeśli rozszerzenia są dozwolone w zasadach, użytkownik otrzyma wiadomość e-mail 14 dni, a także 1 dzień przed ich przypisanie pakietu dostępu ma ustawioną datę ważności monitowania rozszerzenie przypisania.

    ![Pakiet dostępu — ustawienia wygaśnięcia zasad](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Zasady: Włącz zasady

1. Pakiet dostępu do udostępnienia bezpośrednio do użytkowników w zasadach, kliknij przycisk **tak** Aby włączyć zasady.

    Zawsze można włączyć go w przyszłości po zakończeniu tworzenia pakietu dostępu.

    ![Pakiet dostępu — ustawienie zasad obsługi zasad](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Kliknij przycisk **dalej** lub **tworzenie**.
