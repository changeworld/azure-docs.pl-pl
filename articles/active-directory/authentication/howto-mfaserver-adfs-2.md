---
title: Korzystanie z serwera usługi Azure MFA server z usługą AD FS 2.0 — usługa Azure Active Directory
description: Ta strona dotyczy usługi Azure Multi-Factor Authentication i zawiera informacje umożliwiające rozpoczęcie korzystania z usługi Azure MFA i usług AD FS 2.0.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c79a42bbd60d7a1857649cffc97ed7f0103fa16
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653518"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Konfigurowanie serwera usługi Azure Multi-Factor Authentication do współdziałania z usługami AD FS 2.0

Ten artykuł jest przeznaczony dla organizacji, które są sfederowane z usługą Azure Active Directory i chcą zabezpieczyć zasoby przechowywane lokalnie i w chmurze. Zasoby można chronić, używając serwera usługi Azure Multi-Factor Authentication i konfigurując go do pracy z usługami AD FS w taki sposób, aby weryfikacja dwuetapowa była wymagana dla ważnych punktów końcowych.

Ta dokumentacja dotyczy korzystania z serwera usługi Azure Multi-Factor Authentication z usługami AD FS 2.0. Aby dowiedzieć się więcej na temat usług AD FS, zobacz [Zabezpieczanie zasobów w chmurze i lokalnych przy użyciu serwera usługi Azure Multi-Factor Authentication i usług AD FS systemu Windows Server 2012 R2](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Zabezpieczanie usługi AD FS 2.0 przy użyciu serwera proxy

Aby zabezpieczyć usługi AD FS 2.0 za pomocą serwera proxy, zainstaluj serwer usługi Azure Multi-Factor Authentication na serwerze proxy usług AD FS.

### <a name="configure-iis-authentication"></a>Konfigurowanie uwierzytelniania usług IIS

1. Na serwerze usługi Azure Multi-Factor Authentication kliknij ikonę **Uwierzytelnianie usług IIS** w menu po lewej stronie.
2. Kliknij kartę **Oparte na formularzach.**
3. Kliknij przycisk **Dodaj**.

   ![Okno Uwierzytelnianie usług IIS serwera usługi MFA](./media/howto-mfaserver-adfs-2/setup1.png)

4. Aby automatycznie wykryć zmienne nazwy użytkownika, hasła i `https://sso.contoso.com/adfs/ls`domeny, wprowadź adres URL logowania (np. ) w oknie dialogowym Automatyczna konfigurowanie witryny sieci Web opartej na formularzach i kliknij przycisk **OK**.
5. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać weryfikacji dwuetapowej. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer lub będzie wykluczona z weryfikacji dwuetapowej, należy pozostawić to pole puste.
6. Jeśli nie można automatycznie wykryć zmiennych strony, kliknij przycisk **Określ ręcznie...** w oknie dialogowym Automatyczna konfiguracja witryny sieci Web opartej na formularzach.
7. W oknie dialogowym Dodawanie witryny sieci Web opartej na formularzach wprowadź adres `https://sso.contoso.com/adfs/ls`URL strony logowania usług AD FS w polu Prześlij adres URL (np. Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
8. Ustaw format żądania na wartość **POST lub GET**.
9. Wprowadź wartość zmiennej nazwy użytkownika (ctl00$ContentPlaceHolder1$UsernameTextBox) i wartość zmiennej hasła (ctl00$ContentPlaceHolder1$PasswordTextBox). Jeśli na stronie logowania opartej na formularzach jest wyświetlane pole tekstowe domeny, wprowadź również wartość zmiennej domeny. W celu znalezienia nazw pól wejściowych na stronie logowania przejdź do tej strony w przeglądarce sieci Web, kliknij prawym przyciskiem myszy i wybierz polecenie **Wyświetl źródło**.
10. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać weryfikacji dwuetapowej. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer lub będzie wykluczona z weryfikacji dwuetapowej, należy pozostawić to pole puste.

    ![Dodawanie witryny sieci Web opartej na formularzach do serwera usługi MFA](./media/howto-mfaserver-adfs-2/manual.png)

11. Kliknij pozycję **Zaawansowane**, aby przejrzeć ustawienia zaawansowane. Ustawienia, które można skonfigurować, to:

    - Wybór pliku niestandardowej strony odmowy
    - Buforowanie pomyślnych uwierzytelnień do witryny sieci Web przy użyciu plików cookie
    - Wybór sposobu uwierzytelniania poświadczeń podstawowych

12. Ponieważ serwer proxy usług AD FS przypuszczalnie nie zostanie przyłączony do domeny, do nawiązywania połączenia z kontrolerem domeny w celu importowania i wstępnego uwierzytelniania użytkowników możesz użyć protokołu LDAP. W oknie dialogowym Zaawansowana witryna sieci Web oparta na formularzach kliknij kartę **Uwierzytelnianie podstawowe** i wybierz opcję **Powiązanie z protokołem LDAP** jako typ uwierzytelniania wstępnego.
13. Po wykonaniu tej czynności kliknij przycisk **OK**, aby wrócić do okna dialogowego Dodawanie witryny sieci Web opartej na formularzach.
14. Kliknij przycisk **OK**, aby zamknąć okno dialogowe.
15. Po wykryciu lub wprowadzeniu adresu URL i zmiennych na stronie dane witryny sieci Web wyświetlane są w panelu Oparte na formularzach.
16. Kliknij kartę **Moduł macierzysty** i wybierz serwer, witrynę sieci Web, w obszarze w niej uruchomiony serwer proxy usług AD FS (na przykład "Domyślna witryna sieci Web") lub aplikację serwera proxy usług AD FS (na przykład "ls" w obszarze "adfs"), aby włączyć wtyczkę usług IIS na żądanym poziomie.
17. Kliknij pole **Włącz uwierzytelnianie usług IIS** w górnej części ekranu.

Uwierzytelnianie usług IIS zostało włączone.

### <a name="configure-directory-integration"></a>Konfigurowanie integracji katalogu

Uwierzytelnianie usług IIS zostało włączone, ale aby przeprowadzić uwierzytelnianie wstępne w usłudze Active Directory (AD) za pośrednictwem protokołu LDAP, musisz skonfigurować połączenie protokołu LDAP z kontrolerem domeny.

1. Kliknij ikonę **Integracja katalogu**.
2. Na karcie Ustawienia wybierz przycisk opcji **Użyj określonego ustawienia konfiguracji LDAP.**

   ![Konfigurowanie ustawień protokołu LDAP dla określonych ustawień protokołu LDAP](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Kliknij pozycję **Edytuj**.
4. W oknie dialogowym Edycja konfiguracji LDAP wypełnij pola informacjami wymaganymi do nawiązania połączenia z kontrolerem domeny usługi AD. Opisy tych pól znajdują się w pliku pomocy serwera usługi Azure Multi-Factor Authentication.
5. Przetestuj połączenie LDAP, klikając przycisk **Testuj.**

   ![Testowanie konfiguracji LDAP na serwerze usługi MFA](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Jeśli test połączenia LDAP zakończył się pomyślnie, kliknij przycisk **OK**.

### <a name="configure-company-settings"></a>Konfigurowanie ustawień firmy

1. Następnie kliknij ikonę **Ustawienia firmy** i wybierz kartę **Rozpoznawanie nazw użytkowników.**
2. Wybierz **atrybut Użyj unikatowego identyfikatora LDAP dla pasującego** przycisku opcji nazwy użytkowników.
3. Jeśli użytkownicy wejdą swoją nazwę użytkownika w formacie "domena\nazwa użytkownika", serwer musi mieć możliwość odsuń domenę od nazwy użytkownika podczas tworzenia kwerendy LDAP. Można to skonfigurować za pomocą ustawienia rejestru.
4. W przypadku serwera 64-bitowego otwórz Edytor rejestru i przejdź do klucza HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor. Jeśli na serwerze 32-bitowym, weź "Wow6432Node" ze ścieżki. Utwórz klucz rejestru DWORD o nazwie "UsernameCxz_stripPrefixDomain" i ustaw wartość 1. Po wykonaniu tych czynności serwer proxy usług AD FS jest chroniony przez usługę Azure Multi-Factor Authentication.

Upewnij się, że zaimportowano użytkowników z usługi Active Directory na serwer. Zobacz [sekcję Zaufane adresy IP,](#trusted-ips) jeśli chcesz zezwolić na wewnętrzne adresy IP, aby weryfikacja dwuetapowa nie była wymagana podczas logowania się do witryny sieci Web z tych lokalizacji.

![Edytor rejestru do konfigurowania ustawień firmy](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>Bezpośrednie używanie usług AD FS 2.0 bez serwera proxy

Jeśli serwer proxy usług AD FS nie jest używany, można zabezpieczyć usługi AD FS. Zainstaluj serwer usługi Azure Multi-Factor Authentication na serwerze usług AD FS i skonfiguruj go, wykonując następujące kroki:

1. W ramach serwera uwierzytelniania wieloskładnikowego platformy Azure kliknij ikonę **uwierzytelniania usług IIS** w menu po lewej stronie.
2. Kliknij kartę **HTTP.**
3. Kliknij przycisk **Dodaj**.
4. W oknie dialogowym Dodawanie podstawowego adresu URL wprowadź adres URL witryny `https://sso.domain.com/adfs/ls/auth/integrated`sieci Web usług AD FS, w której wykonywane jest uwierzytelnianie HTTP (np. ) w polu Podstawowy adres URL. Następnie wprowadź nazwę aplikacji (opcjonalnie). Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
5. W razie potrzeby dostosuj wartości w polach Limit czasu bezczynności i Maksymalna długość sesji.
6. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać weryfikacji dwuetapowej. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer lub będzie wykluczona z weryfikacji dwuetapowej, należy pozostawić to pole puste.
7. W razie potrzeby zaznacz pole użycia plików cookie do buforowania.

   ![Bezpośrednie używanie usług AD FS 2.0 bez serwera proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Kliknij przycisk **OK**.
9. Kliknij kartę **Moduł macierzysty** i wybierz serwer, witrynę sieci Web (na przykład "Domyślna witryna sieci Web") lub aplikację AD FS (na przykład "ls" w obszarze "adfs"), aby włączyć wtyczkę usług IIS na żądanym poziomie.
10. Kliknij pole **Włącz uwierzytelnianie usług IIS** w górnej części ekranu.

Po wykonaniu tych czynności usługi AD FS są chronione przez usługę Azure Multi-Factor Authentication.

Upewnij się, że zaimportowano użytkowników z usługi Active Directory na serwer. Zobacz sekcję Zaufane adresy IP, jeśli chcesz zezwolić na wewnętrzne adresy IP, aby weryfikacja dwuetapowa nie była wymagana podczas logowania się do witryny sieci Web z tych lokalizacji.

## <a name="trusted-ips"></a>Zaufane adresy IP

Zaufane adresy IP pozwalają użytkownikom pominąć uwierzytelnianie za pomocą usługi Azure Multi-Factor Authentication w przypadku żądań witryn sieci Web pochodzących z określonych adresów IP lub podsieci. Można na przykład chcieć zwolnić użytkowników z konieczności przechodzenia weryfikacji dwuetapowej, jeśli logują się z biura. W tym celu należy określić podsieć biura jako wpis z zaufanymi adresami IP.

### <a name="to-configure-trusted-ips"></a>Aby skonfigurować zaufane adresy IP

1. W sekcji Uwierzytelnianie usług IIS kliknij kartę **Zaufane pliki IP.**
2. Kliknij przycisk **Dodaj...** Edytuj...
3. Gdy pojawi się okno dialogowe Dodawanie zaufanych adresów IP, wybierz jeden z przycisków radiowych: **Pojedynczy adres IP**, **Zakres adresów IP** albo **Podsieć**.
4. Wprowadź adres IP, zakres adresów IP lub podsieci, które powinny być dozwolone. Jeśli wejdź do podsieci, wybierz odpowiednią maskę sieci i kliknij przycisk **OK.**

![Konfigurowanie zaufanych adresów IP na serwerze usługi MFA](./media/howto-mfaserver-adfs-2/trusted.png)
