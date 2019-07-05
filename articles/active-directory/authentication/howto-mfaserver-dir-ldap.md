---
title: Serwer MFA uwierzytelnianie LDAP i na platformie Azure — usługa Azure Active Directory
description: Wdrażanie serwera Multi-Factor Authentication uwierzytelnianie LDAP i na platformie Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc67fb216451bd19fe8cfb327c101aa989228b01
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536903"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Uwierzytelnianie LDAP i serwer Azure Multi-Factor Authentication

Domyślnie usługa Serwer Azure Multi-Factor Authentication jest skonfigurowana pod kątem importowania lub synchronizowania użytkowników z usługi Active Directory. Można ją jednak skonfigurować pod kątem powiązań z różnymi katalogami LDAP, takimi jak katalog ADAM, lub z określonym kontrolerem domeny usługi Active Directory. Po nawiązaniu połączenia z katalogiem za pośrednictwem protokołu LDAP serwer usługi Azure Multi-Factor Authentication może działać jako serwera proxy LDAP wykorzystywanego do przeprowadzania uwierzytelnień. Umożliwia również korzystanie z powiązania LDAP jako elementu docelowego protokołu RADIUS do wstępnego uwierzytelniania użytkowników przy użyciu uwierzytelniania w usługach IIS lub do uwierzytelniania podstawowego w portalu użytkowników usługi Azure MFA.

Aby używać usługi Azure Multi-Factor Authentication jako serwera proxy LDAP, Wstaw serwer Azure Multi-Factor Authentication pomiędzy klientem LDAP (na przykład urządzenie sieci VPN, aplikacji) i serwerem katalogu LDAP. Serwer usługi Azure Multi-Factor Authentication musi zostać skonfigurowany pod kątem komunikacji zarówno z serwerami klienta, jak i z katalogiem LDAP. W tej konfiguracji serwer usługi Azure Multi-Factor Authentication akceptuje żądania LDAP od serwerów i aplikacji klientów i przekazuje je do docelowego serwera katalogu LDAP w celu sprawdzania poprawności podstawowych poświadczeń. Jeśli katalog LDAP jest weryfikowana podstawowych poświadczeń, usługi Azure Multi-Factor Authentication przeprowadza drugą weryfikację tożsamości i wysyła odpowiedź z powrotem do klienta LDAP. Cały proces uwierzytelniania powiedzie się tylko wtedy, gdy zarówno uwierzytelnianie serwera LDAP, jak i drugi etap weryfikacji przebiegną prawidłowo.

> [!IMPORTANT]
> Począwszy od 1 lipca 2019 firma Microsoft będzie oferować już serwer MFA w przypadku nowych wdrożeń. Nowi klienci, którzy chcesz wymagać uwierzytelniania wieloskładnikowego od użytkowników należy używać oparte na chmurze usługi Azure Multi-Factor Authentication. Istniejący klienci, którzy aktywowali usługę MFA Server przed 1 lipca będzie można pobrać najnowszą wersję, a przyszłe aktualizacje i Generuj poświadczenia aktywacji w zwykły sposób.

## <a name="configure-ldap-authentication"></a>Konfigurowanie uwierzytelniania LDAP

Aby skonfigurować uwierzytelnianie LDAP, zainstaluj usługę Serwer Azure Multi-Factor Authentication na serwerze z systemem Windows. Użyj poniższej procedury:

### <a name="add-an-ldap-client"></a>Dodawanie klienta LDAP

1. Na serwerze Azure Multi-Factor Authentication wybierz ikonę uwierzytelniania LDAP w menu po lewej stronie.
2. Zaznacz pole wyboru **Włącz uwierzytelnianie LDAP**.

   ![Uwierzytelnianie LDAP na serwerze MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Zmień port TCP i port SSL na karcie Klienci, jeśli usługa LDAP Azure Multi-Factor Authentication powinna być powiązana z portami niestandardowymi w celu umożliwienia nasłuchiwania żądań LDAP.
4. Jeśli planujesz korzystanie z protokołu LDAPS z klienta do serwera Azure Multi-Factor Authentication, certyfikat SSL musi zainstalowany na tym samym serwerze co serwer usługi MFA. Kliknij przycisk **Przeglądaj** obok protokołu SSL certyfikatów pole i wybierz certyfikat do użycia na potrzeby bezpiecznego połączenia.
5. Kliknij pozycję **Add** (Dodaj).
6. W oknie dialogowym Dodawanie klienta LDAP wprowadź adres IP serwera, urządzenia lub aplikacji, który uwierzytelnia serwer i nazwę aplikacji (opcjonalnie). Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
7. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać weryfikacji dwuetapowej. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer i/lub wykluczone z weryfikacji dwuetapowej, należy pozostawić pole puste. Zobacz plik, aby uzyskać dodatkowe informacje na temat tej funkcji pomocy serwera MFA.

Powtórz te kroki, aby dodać kolejnych klientów LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurowanie połączenia katalogu LDAP

Gdy usługa Azure Multi-Factor Authentication jest skonfigurowana pod kątem odbierania uwierzytelnień LDAP, musi ona pełnić rolę serwera proxy przesyłającego te uwierzytelnienia do katalogu LDAP. W związku z tym na karcie Cel wyświetlana jest tylko jedna, wyszarzona opcja użycia obiektu docelowego LDAP.

> [!NOTE]
> Integracja katalogu nie musi pracować z katalogami, innego niż Active Directory Domain Services.

1. Aby skonfigurować połączenie z katalogiem LDAP, kliknij ikonę **Integracja katalogu**.
2. Na karcie Ustawienia wybierz przycisk radiowy **Użyj określonej konfiguracji LDAP**.
3. Wybierz pozycję **Edytuj...**
4. W oknie dialogowym Edycja konfiguracji LDAP wypełnij pola informacjami wymaganymi do nawiązania połączenia z katalogiem LDAP. Opisy tych pól znajdują się w pliku pomocy serwera usługi Azure Multi-Factor Authentication.

    ![Konfiguracja LDAP integracji katalogu](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Przetestuj połączenie LDAP, klikając przycisk **Testuj**.
6. Jeśli test połączenia LDAP zakończył się pomyślnie, kliknij przycisk **OK**.
7. Kliknij kartę **Filtry**. Serwer jest wstępnie skonfigurowany na potrzeby ładowania kontenerów, grup zabezpieczeń i użytkowników z katalogu Active Directory. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP prawdopodobnie będzie konieczna edycja wyświetlanych filtrów. Kliknij link **Pomoc**, aby uzyskać więcej informacji o filtrach.
8. Kliknij kartę **Atrybuty**. Serwer jest wstępnie skonfigurowany pod kątem mapowania atrybutów z katalogu Active Directory.
9. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP lub zmiany wstępnie skonfigurowanego mapowania atrybutów kliknij przycisk **Edytuj…**
10. W oknie dialogowym Edycja atrybutów zmodyfikuj mapowanie atrybutu LDAP katalogu. Nazwy atrybutów można wpisać lub wybrać poprzez kliknięcie przycisku **…** znajdującego się obok każdego pola. Kliknij link **Pomoc**, aby uzyskać więcej informacji o atrybutach.
11. Kliknij przycisk **OK**.
12. Kliknij ikonę **Ustawienia firmy**, a następnie wybierz kartę **Rozpoznawanie nazwy użytkownika**.
13. W przypadku nawiązywania połączenia z katalogiem Active Directory z serwera przyłączonego do domeny zostaw zaznaczony przycisk radiowy **Użyj identyfikatorów zabezpieczeń systemu Windows (SID) w celu dopasowania nazw użytkowników**. W przeciwnym razie wybierz przycisk radiowy **Użyj atrybutu unikatowego identyfikatora LDAP w celu dopasowania nazw użytkowników**.

Gdy przycisk radiowy **Użyj atrybutu unikatowego identyfikatora LDAP w celu dopasowania nazw użytkowników** jest zaznaczony, serwer usługi Azure Multi-Factor Authentication próbuje rozpoznać unikatowy identyfikator w katalogu LDAP na podstawie każdej z nazw użytkowników. Wyszukiwanie LDAP zostaje przeprowadzone z uwzględnieniem atrybutów nazwy użytkownika określonych w obszarze Integracja katalogu na karcie Atrybuty. Podczas uwierzytelniania użytkownika jego nazwa użytkownika zostaje rozpoznany Unikatowy identyfikator w katalogu LDAP. Unikatowy identyfikator jest używany do dopasowania użytkownika w pliku danych usługi Azure Multi-Factor Authentication. Dzięki temu porównania bez uwzględniania wielkości liter, a także długich i krótkich nazw użytkowników.

Po wykonaniu tych kroków, serwer MFA nasłuchuje żądań dostępu LDAP ze skonfigurowanych klientów w ramach skonfigurowanych portów i działa jako serwer proxy dla tych żądań z katalogiem LDAP, do uwierzytelniania.

## <a name="configure-ldap-client"></a>Konfigurowanie klienta LDAP

Aby skonfigurować klienta LDAP, skorzystaj z poniższych wytycznych:

* Skonfiguruj urządzenie, serwer lub aplikację pod kątem uwierzytelniania z użyciem protokołu LDAP na serwerze usługi Azure Multi-Factor Authentication tak, jakby był to katalog LDAP. Użyj tych samych ustawień, które zwykle są używane do bezpośredniego łączenia się z katalogiem LDAP, z wyjątkiem nazwy serwera lub adresu IP, które będą odpowiadać danym serwera usługi Azure Multi-Factor Authentication.
* Skonfiguruj limit czasu protokołu LDAP na 30 – 60 sekund, aby był czas na przeprowadzenie walidacji poświadczeń użytkownika w katalogu LDAP, wykonywanie drugiego etapu weryfikacji, odebranie odpowiedzi i odpowiadać na żądania dostępu LDAP.
* W przypadku użycia protokołu LDAPS urządzenie lub serwer, z którego pochodzą zapytania protokołu LDAP, muszą ufać certyfikatowi SSL zainstalowanemu na serwerze usługi Azure Multi-Factor Authentication.
