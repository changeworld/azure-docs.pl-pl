---
title: Uwierzytelnianie LDAP i serwer usługi Azure MFA — usługa Azure Active Directory
description: Wdrażanie uwierzytelniania LDAP i serwera uwierzytelniania wieloskładnikowego platformy Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7a5400fce2fce1b75cbd579830f929fcc0d0dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454469"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Uwierzytelnianie LDAP i serwer uwierzytelniania wieloskładnikowego platformy Azure

Domyślnie usługa Serwer Azure Multi-Factor Authentication jest skonfigurowana pod kątem importowania lub synchronizowania użytkowników z usługi Active Directory. Można ją jednak skonfigurować pod kątem powiązań z różnymi katalogami LDAP, takimi jak katalog ADAM, lub z określonym kontrolerem domeny usługi Active Directory. Po połączeniu z katalogiem za pośrednictwem protokołu LDAP serwer uwierzytelniania wieloskładnikowego platformy Azure może działać jako serwer proxy LDAP w celu wykonywania uwierzytelniania. Umożliwia również korzystanie z powiązania LDAP jako elementu docelowego protokołu RADIUS do wstępnego uwierzytelniania użytkowników przy użyciu uwierzytelniania w usługach IIS lub do uwierzytelniania podstawowego w portalu użytkowników usługi Azure MFA.

Aby użyć uwierzytelniania wieloskładnikowego platformy Azure jako serwera proxy LDAP, wstaw serwer uwierzytelniania wieloskładnikowego platformy Azure między klientem LDAP (na przykład urządzeniem sieci VPN, aplikacją) a serwerem katalogowym LDAP. Serwer usługi Azure Multi-Factor Authentication musi zostać skonfigurowany pod kątem komunikacji zarówno z serwerami klienta, jak i z katalogiem LDAP. W tej konfiguracji serwer usługi Azure Multi-Factor Authentication akceptuje żądania LDAP od serwerów i aplikacji klientów i przekazuje je do docelowego serwera katalogu LDAP w celu sprawdzania poprawności podstawowych poświadczeń. Jeśli katalog LDAP sprawdza poprawność poświadczeń podstawowych, uwierzytelnianie wieloskładnikowe platformy Azure przeprowadza weryfikację drugiej tożsamości i wysyła odpowiedź z powrotem do klienta LDAP. Cały proces uwierzytelniania powiedzie się tylko wtedy, gdy zarówno uwierzytelnianie serwera LDAP, jak i drugi etap weryfikacji przebiegną prawidłowo.

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="configure-ldap-authentication"></a>Konfigurowanie uwierzytelniania LDAP

Aby skonfigurować uwierzytelnianie LDAP, zainstaluj usługę Serwer Azure Multi-Factor Authentication na serwerze z systemem Windows. Postępuj zgodnie z następującą procedurą:

### <a name="add-an-ldap-client"></a>Dodawanie klienta LDAP

1. Na serwerze uwierzytelniania wieloskładnikowego platformy Azure wybierz ikonę uwierzytelniania LDAP w menu po lewej stronie.
2. Zaznacz pole wyboru **Włącz uwierzytelnianie LDAP.**

   ![Uwierzytelnianie LDAP na serwerze usługi MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Na karcie Klienci zmień port TCP i port SSL (TLS), jeśli usługa LDAP uwierzytelniania wieloskładnikowego azure powinna być powiązana z niestandardowymi portami w celu nasłuchiwania żądań LDAP.
4. Jeśli planujesz używać protokołu LDAPS od klienta do serwera uwierzytelniania wieloskładnikowego platformy Azure, certyfikat TLS/SSL musi być zainstalowany na tym samym serwerze co serwer usługi MFA. Kliknij **pozycję Przeglądaj** obok pola certyfikatu SSL (TLS) i wybierz certyfikat do użycia dla bezpiecznego połączenia.
5. Kliknij przycisk **Dodaj**.
6. W oknie dialogowym Dodawanie klienta LDAP wprowadź adres IP urządzenia, serwera lub aplikacji uwierzytelniającego się na serwerze oraz nazwę aplikacji (opcjonalnie). Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
7. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać weryfikacji dwuetapowej. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer i/lub jest zwolniona z weryfikacji dwuetapowej, pozostaw to pole niezaznaczone. Dodatkowe informacje na temat tej funkcji można znaleźć w pliku pomocy serwera usługi MFA.

Powtórz te kroki, aby dodać kolejnych klientów LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurowanie połączenia katalogu LDAP

Gdy usługa Azure Multi-Factor Authentication jest skonfigurowana pod kątem odbierania uwierzytelnień LDAP, musi ona pełnić rolę serwera proxy przesyłającego te uwierzytelnienia do katalogu LDAP. W związku z tym na karcie Cel wyświetlana jest tylko jedna, wyszarzona opcja użycia obiektu docelowego LDAP.

> [!NOTE]
> Integracja katalogów nie jest gwarantowana do pracy z katalogami innymi niż Usługi domenowe Active Directory.

1. Aby skonfigurować połączenie katalogu LDAP, kliknij ikonę **Integracja katalogów.**
2. Na karcie Ustawienia wybierz przycisk opcji **Użyj określonego ustawienia konfiguracji LDAP.**
3. Wybierz **pozycję Edytuj...**
4. W oknie dialogowym Edycja konfiguracji LDAP wypełnij pola informacjami wymaganymi do nawiązania połączenia z katalogiem LDAP. Opisy tych pól znajdują się w pliku pomocy serwera usługi Azure Multi-Factor Authentication.

    ![Konfigura integracji katalogów LDAP](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Przetestuj połączenie LDAP, klikając przycisk **Testuj.**
6. Jeśli test połączenia LDAP zakończył się pomyślnie, kliknij przycisk **OK.**
7. Kliknij kartę **Filtry.** Serwer jest wstępnie skonfigurowany do ładowania kontenerów, grup zabezpieczeń i użytkowników z usługi Active Directory. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP prawdopodobnie będzie konieczna edycja wyświetlanych filtrów. Kliknij łącze **Pomoc,** aby uzyskać więcej informacji na temat filtrów.
8. Kliknij kartę **Atrybuty.** Serwer jest wstępnie skonfigurowany do mapowania atrybutów z usługi Active Directory.
9. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP lub zmiany wstępnie skonfigurowanego mapowania atrybutów kliknij przycisk **Edytuj…**
10. W oknie dialogowym Edycja atrybutów zmodyfikuj mapowanie atrybutu LDAP katalogu. Nazwy atrybutów można wpisywać lub wybierać, klikając **przycisk ...** znajdującego się obok każdego pola. Kliknij łącze **Pomoc,** aby uzyskać więcej informacji na temat atrybutów.
11. Kliknij przycisk **OK.**
12. Kliknij ikonę **Ustawienia firmy** i wybierz kartę **Rozpoznawanie nazw użytkowników.**
13. W przypadku nawiązywania połączenia z katalogiem Active Directory z serwera przyłączonego do domeny zostaw zaznaczony przycisk radiowy **Użyj identyfikatorów zabezpieczeń systemu Windows (SID) w celu dopasowania nazw użytkowników**. W przeciwnym razie wybierz **atrybut Użyj unikatowego identyfikatora LDAP dla pasującego** przycisku opcji nazwy użytkowników.

Gdy przycisk radiowy **Użyj atrybutu unikatowego identyfikatora LDAP w celu dopasowania nazw użytkowników** jest zaznaczony, serwer usługi Azure Multi-Factor Authentication próbuje rozpoznać unikatowy identyfikator w katalogu LDAP na podstawie każdej z nazw użytkowników. Wyszukiwanie LDAP jest wykonywane na atrybutach nazwa użytkownika zdefiniowanych na karcie Atrybuty integracji > katalogu. Gdy użytkownik uwierzytelnia się, nazwa użytkownika jest rozpoznawany do unikatowego identyfikatora w katalogu LDAP. Unikatowy identyfikator jest używany do dopasowywania użytkownika w pliku danych uwierzytelniania wieloskładnikowego platformy Azure. Pozwala to na porównania bez uwzględniania wielkości liter oraz długie i krótkie formaty nazwy użytkownika.

Po wykonaniu tych kroków serwer usługi MFA nasłuchuje skonfigurowanych portów żądań dostępu LDAP od skonfigurowanych klientów i działa jako serwer proxy dla tych żądań do katalogu LDAP do uwierzytelniania.

## <a name="configure-ldap-client"></a>Konfigurowanie klienta LDAP

Aby skonfigurować klienta LDAP, skorzystaj z poniższych wytycznych:

* Skonfiguruj urządzenie, serwer lub aplikację pod kątem uwierzytelniania z użyciem protokołu LDAP na serwerze usługi Azure Multi-Factor Authentication tak, jakby był to katalog LDAP. Użyj tych samych ustawień, które zwykle są używane do bezpośredniego łączenia się z katalogiem LDAP, z wyjątkiem nazwy serwera lub adresu IP, które będą odpowiadać danym serwera usługi Azure Multi-Factor Authentication.
* Skonfiguruj limit czasu protokołu LDAP do 30-60 sekund, aby sprawdzić poprawność poświadczeń użytkownika za pomocą katalogu LDAP, przeprowadzić weryfikację drugiego kroku, otrzymać odpowiedź i odpowiedzieć na żądanie dostępu LDAP.
* W przypadku korzystania z protokołu LDAPS urządzenie lub serwer dokonujący kwerend LDAP musi ufać certyfikatowi TLS/SSL zainstalowanemu na serwerze uwierzytelniania wieloskładnikowego platformy Azure.
