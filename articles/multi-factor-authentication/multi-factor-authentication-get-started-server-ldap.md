---
title: "Uwierzytelnianie LDAP i serwer usługi Azure MFA | Microsoft Docs"
description: "Ta strona jest poświęcona tematyce uwierzytelniania wieloskładnikowego na platformie Azure i zawiera informacje pomocne podczas wdrażania uwierzytelniania LDAP i korzystania z usługi Serwer Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.openlocfilehash: 37f96afab67680b3140a6f4da6bff0c23a1f328f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Uwierzytelnianie LDAP i serwera usługi Azure Multi-Factor Authentication
Domyślnie usługa Serwer Azure Multi-Factor Authentication jest skonfigurowana pod kątem importowania lub synchronizowania użytkowników z usługi Active Directory. Można ją jednak skonfigurować pod kątem powiązań z różnymi katalogami LDAP, takimi jak katalog ADAM, lub z określonym kontrolerem domeny usługi Active Directory. Podczas połączenia z katalogiem za pośrednictwem protokołu LDAP, serwer usługi Azure Multi-Factor Authentication może działać jako serwer proxy LDAP do wykonania uwierzytelnienia. Umożliwia również korzystanie z powiązania LDAP jako elementu docelowego protokołu RADIUS do wstępnego uwierzytelniania użytkowników przy użyciu uwierzytelniania w usługach IIS lub do uwierzytelniania podstawowego w portalu użytkowników usługi Azure MFA.

Aby użyć usługi Azure Multi-Factor Authentication jako serwer proxy LDAP, Wstaw serwera usługi Azure Multi-Factor Authentication między klientem LDAP (na przykład urządzenia sieci VPN, aplikacji) i serwera katalogu LDAP. Serwer usługi Azure Multi-Factor Authentication musi zostać skonfigurowany pod kątem komunikacji zarówno z serwerami klienta, jak i z katalogiem LDAP. W tej konfiguracji serwer usługi Azure Multi-Factor Authentication akceptuje żądania LDAP od serwerów i aplikacji klientów i przekazuje je do docelowego serwera katalogu LDAP w celu sprawdzania poprawności podstawowych poświadczeń. Jeśli katalog LDAP weryfikuje poświadczenia podstawowego, uwierzytelnianie wieloskładnikowe Azure wykonuje drugi weryfikacji tożsamości i wysyła odpowiedź z powrotem do klienta LDAP. Cały proces uwierzytelniania powiedzie się tylko wtedy, gdy zarówno uwierzytelnianie serwera LDAP, jak i drugi etap weryfikacji przebiegną prawidłowo.

## <a name="configure-ldap-authentication"></a>Skonfigurowanie uwierzytelniania LDAP
Aby skonfigurować uwierzytelnianie LDAP, zainstaluj usługę Serwer Azure Multi-Factor Authentication na serwerze z systemem Windows. Postępuj zgodnie z następującą procedurą:

### <a name="add-an-ldap-client"></a>Dodawanie klienta LDAP

1. Wybierz ikonę uwierzytelniania LDAP w menu po lewej stronie serwera usługi Azure Multi-Factor Authentication.
2. Zaznacz pole wyboru **Włącz uwierzytelnianie LDAP**.

   ![Uwierzytelnianie LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Zmień port TCP i port SSL na karcie Klienci, jeśli usługa LDAP Azure Multi-Factor Authentication powinna być powiązana z portami niestandardowymi w celu umożliwienia nasłuchiwania żądań LDAP.
4. Jeśli planujesz użyć LDAPS od klienta do serwera usługi Azure Multi-Factor Authentication, certyfikat SSL należy zainstalować na tym samym serwerze co serwer usługi MFA. Kliknij przycisk **Przeglądaj** obok SSL certyfikatu pola, a następnie wybierz certyfikat do użycia na potrzeby bezpiecznego połączenia.
5. Kliknij pozycję **Dodaj**.
6. W oknie dialogowym Dodawanie klienta LDAP wprowadź adres IP serwera, urządzenia lub aplikacji, który służy do uwierzytelniania serwera i nazwy aplikacji (opcjonalnie). Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
7. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać weryfikacji dwuetapowej. Jeśli duża liczba użytkowników nie został zaimportowany do serwera i/lub są wykluczone z weryfikacji dwuetapowej, nie zaznaczaj pola wyboru. Zobacz serwer usługi MFA, plik, aby uzyskać dodatkowe informacje na temat tej funkcji pomocy.

Powtórz te kroki, aby dodać kolejnych klientów LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurowanie połączenia katalogu LDAP

Gdy usługa Azure Multi-Factor Authentication jest skonfigurowana pod kątem odbierania uwierzytelnień LDAP, musi ona pełnić rolę serwera proxy przesyłającego te uwierzytelnienia do katalogu LDAP. W związku z tym na karcie Cel wyświetlana jest tylko jedna, wyszarzona opcja użycia obiektu docelowego LDAP.

1. Aby skonfigurować połączenie z katalogiem LDAP, kliknij ikonę **Integracja katalogu**.
2. Na karcie Ustawienia wybierz przycisk radiowy **Użyj określonej konfiguracji LDAP**.
3. Wybierz pozycję **Edytuj...**
4. W oknie dialogowym Edycja konfiguracji LDAP wypełnij pola informacjami wymaganymi do nawiązania połączenia z katalogiem LDAP. Opisy tych pól znajdują się w pliku pomocy serwera usługi Azure Multi-Factor Authentication.

    ![Integracja katalogu](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Przetestuj połączenie LDAP, klikając przycisk **Testuj**.
6. Jeśli test połączenia LDAP zakończył się pomyślnie, kliknij przycisk **OK**.
7. Kliknij kartę **Filtry**. Serwer jest wstępnie skonfigurowany na potrzeby ładowania kontenerów, grup zabezpieczeń i użytkowników z katalogu Active Directory. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP prawdopodobnie będzie konieczna edycja wyświetlanych filtrów. Kliknij link **Pomoc**, aby uzyskać więcej informacji o filtrach.
8. Kliknij kartę **Atrybuty**. Serwer jest wstępnie skonfigurowany pod kątem mapowania atrybutów z katalogu Active Directory.
9. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP lub zmiany wstępnie skonfigurowanego mapowania atrybutów kliknij przycisk **Edytuj…**
10. W oknie dialogowym Edycja atrybutów zmodyfikuj mapowanie atrybutu LDAP katalogu. Nazwy atrybutów można wpisać lub wybrać poprzez kliknięcie przycisku **…** znajdującego się obok każdego pola. Kliknij link **Pomoc**, aby uzyskać więcej informacji o atrybutach.
11. Kliknij przycisk **OK**.
12. Kliknij ikonę **Ustawienia firmy**, a następnie wybierz kartę **Rozpoznawanie nazwy użytkownika**.
13. W przypadku nawiązywania połączenia z katalogiem Active Directory z serwera przyłączonego do domeny zostaw zaznaczony przycisk radiowy **Użyj identyfikatorów zabezpieczeń systemu Windows (SID) w celu dopasowania nazw użytkowników**. W przeciwnym razie wybierz przycisk radiowy **Użyj atrybutu unikatowego identyfikatora LDAP w celu dopasowania nazw użytkowników**. 

Gdy przycisk radiowy **Użyj atrybutu unikatowego identyfikatora LDAP w celu dopasowania nazw użytkowników** jest zaznaczony, serwer usługi Azure Multi-Factor Authentication próbuje rozpoznać unikatowy identyfikator w katalogu LDAP na podstawie każdej z nazw użytkowników. Wyszukiwanie LDAP zostaje przeprowadzone z uwzględnieniem atrybutów nazwy użytkownika określonych w obszarze Integracja katalogu na karcie Atrybuty. Podczas uwierzytelniania użytkownika, jego nazwa jest rozpoznana z unikatowym identyfikatorem w katalogu LDAP. Unikatowy identyfikator jest używany do dopasowywania użytkownika w pliku danych usługi Azure Multi-Factor Authentication. Dzięki temu porównania bez uwzględniania wielkości liter, a nazwa użytkownika długich i krótkich formatów.

Po wykonaniu tych kroków serwera usługi MFA nasłuchuje skonfigurowanych portów dla żądań dostępu LDAP z klientów skonfigurowanych i działa jako serwer proxy dla tych żądań w celu uwierzytelniania katalogu LDAP.

## <a name="configure-ldap-client"></a>Konfigurowanie klienta LDAP
Aby skonfigurować klienta LDAP, skorzystaj z poniższych wytycznych:

* Skonfiguruj urządzenie, serwer lub aplikację pod kątem uwierzytelniania z użyciem protokołu LDAP na serwerze usługi Azure Multi-Factor Authentication tak, jakby był to katalog LDAP. Użyj tych samych ustawień, które zwykle są używane do bezpośredniego łączenia się z katalogiem LDAP, z wyjątkiem nazwy serwera lub adresu IP, które będą odpowiadać danym serwera usługi Azure Multi-Factor Authentication.
* Limit czasu LDAP na 30 – 60 sekund należy skonfigurować, tak aby jest czasu na sprawdzanie poprawności poświadczeń użytkownika z katalogiem LDAP, przeprowadzenia weryfikacji krok drugi otrzymał ich odpowiedzi i odpowiadać na żądania dostępu do katalogu LDAP.
* W przypadku użycia protokołu LDAPS urządzenie lub serwer, z którego pochodzą zapytania protokołu LDAP, muszą ufać certyfikatowi SSL zainstalowanemu na serwerze usługi Azure Multi-Factor Authentication.

