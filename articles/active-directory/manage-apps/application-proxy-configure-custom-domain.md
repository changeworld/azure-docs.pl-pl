---
title: Domeny niestandardowe w serwerze proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Konfigurowanie domen niestandardowych w serwerze proxy aplikacji usługi Azure AD i zarządzanie nimi.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481385"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Konfigurowanie domen niestandardowych za pomocą serwera proxy aplikacji usługi Azure AD

Podczas publikowania aplikacji za pośrednictwem serwera proxy aplikacji usługi Azure Active Directory należy utworzyć zewnętrzny adres URL dla użytkowników. Ten adres URL pobiera domyślną *domenę yourtenant.msappproxy.net*. Na przykład, jeśli opublikujesz aplikację o nazwie *Wydatki* w dzierżawie o nazwie *Contoso,* zewnętrzny adres URL to *\/https: /expenses-contoso.msappproxy.net*. Jeśli chcesz użyć własnej nazwy domeny zamiast *msappproxy.net,* możesz skonfigurować domenę niestandardową dla aplikacji. 

## <a name="benefits-of-custom-domains"></a>Korzyści z domen niestandardowych

W miarę możliwości warto skonfigurować domeny niestandardowe dla aplikacji. Oto kilka powodów, dla których warto używać domen niestandardowych:

- Łącza między aplikacjami działają nawet poza siecią firmową. Bez domeny niestandardowej, jeśli aplikacja ma zakodowane łącza wewnętrzne do obiektów docelowych poza serwerem proxy aplikacji, a łącza nie są rozwiązywalne zewnętrznie, zostaną przerwane. Gdy wewnętrzne i zewnętrzne adresy URL są takie same, można uniknąć tego problemu. Jeśli nie możesz używać domen niestandardowych, zobacz [Przekierowywanie linków zakodowanych na stałe dla aplikacji opublikowanych za pomocą serwera proxy aplikacji usługi Azure AD,](../application-proxy-link-translation.md) aby uzyskać inne sposoby rozwiązania tego problemu. 
  
- Użytkownicy będą mieli łatwiejsze środowisko, ponieważ mogą uzyskać dostęp do aplikacji z tym samym adresem URL z wewnątrz lub na zewnątrz sieci. Nie muszą uczyć się różnych wewnętrznych i zewnętrznych adresów URL ani śledzić ich bieżącej lokalizacji. 

- Możesz kontrolować swoją markę i tworzyć żądane adresy URL. Domena niestandardowa może pomóc w budowaniu zaufania użytkowników, ponieważ użytkownicy widzą i używają znanej nazwy zamiast *msappproxy.net*.

- Niektóre konfiguracje będą działać tylko z domenami niestandardowymi. Na przykład potrzebne są domeny niestandardowe dla aplikacji korzystających z języka SAML (Security Assertion Markup Language), na przykład podczas korzystania z usług federacyjnych Active Directory (AD FS), ale nie można używać federacji WS. Aby uzyskać więcej informacji, zobacz [Praca z aplikacjami obsługującymi oświadczenia w serwerze proxy aplikacji](application-proxy-configure-for-claims-aware-applications.md). 

Jeśli nie możesz dopasować wewnętrznych i zewnętrznych adresów URL, używanie domen niestandardowych nie jest tak ważne, ale nadal możesz korzystać z innych korzyści. 

## <a name="dns-configuration-options"></a>Opcje konfiguracji DNS

Istnieje kilka opcji konfigurowania konfiguracji systemu DNS, w zależności od wymagań:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Ten sam wewnętrzny i zewnętrzny adres URL, różne zachowanie wewnętrzne i zewnętrzne 

Jeśli nie chcesz, aby użytkownicy wewnętrzni kierowali się za pośrednictwem serwera proxy aplikacji, możesz skonfigurować *system DNS w podzielonym mózgu.* Podzielona infrastruktura DNS kieruje hosty wewnętrzne do wewnętrznego serwera nazw domen, a hosty zewnętrzne do zewnętrznego serwera nazw domeny w celu rozpoznawania nazw. 

![Środowisko DNS z podziałem informacji](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Różne wewnętrzne i zewnętrzne adresy URL 

Jeśli wewnętrzne i zewnętrzne adresy URL są różne, nie trzeba konfigurować zachowania podzielonego mózgu, ponieważ routing użytkownika jest określany przez adres URL. W takim przypadku można zmienić tylko zewnętrzny dns i kierować zewnętrzny adres URL do punktu końcowego serwera proxy aplikacji. 

Po wybraniu domeny niestandardowej dla zewnętrznego adresu URL na pasku informacji zostanie wyświetle się wpis CNAME, który należy dodać do zewnętrznego dostawcy DNS. Zawsze możesz zobaczyć te informacje, przechodząc do strony **serwera proxy aplikacji** aplikacji.

## <a name="set-up-and-use-custom-domains"></a>Konfigurowanie domen niestandardowych i używanie ich

Aby skonfigurować aplikację lokalną do używania domeny niestandardowej, potrzebujesz zweryfikowanej domeny niestandardowej usługi Azure Active Directory, certyfikatu PFX dla domeny niestandardowej i aplikacji lokalnej do skonfigurowania. 

### <a name="create-and-verify-a-custom-domain"></a>Tworzenie i weryfikowanie domeny niestandardowej

Aby utworzyć i zweryfikować domenę niestandardową:

1. W usłudze Azure Active Directory wybierz **pozycję Niestandardowe nazwy domen** w lewej nawigacji, a następnie wybierz pozycję Dodaj **domenę niestandardową**. 
1. Wprowadź niestandardową nazwę domeny i wybierz pozycję **Dodaj domenę**. 
1. Na stronie domeny skopiuj informacje o rekordzie TXT dla swojej domeny. 
1. Przejdź do rejestratora domen i utwórz nowy rekord TXT dla swojej domeny na podstawie skopiowanych informacji DNS.
1. Po zarejestrowaniu domeny na stronie domeny w usłudze Azure Active Directory wybierz pozycję **Weryfikuj**. Gdy stan domeny jest **zweryfikowany,** można użyć domeny we wszystkich konfiguracjach usługi Azure AD, w tym serwera proxy aplikacji. 

Aby uzyskać bardziej szczegółowe instrukcje, zobacz [Dodawanie niestandardowej nazwy domeny przy użyciu portalu usługi Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Konfigurowanie aplikacji do używania domeny niestandardowej

Aby opublikować aplikację za pośrednictwem serwera proxy aplikacji z domeną niestandardową:

1. W przypadku nowej aplikacji w usłudze Azure Active Directory wybierz **aplikacje enterprise** w lewej nawigacji. Wybierz pozycję **Nowa aplikacja**. W sekcji **Aplikacje lokalne** wybierz pozycję **Dodaj aplikację lokalną**. 
   
   W przypadku aplikacji już w **aplikacjach Enterprise**wybierz ją z listy, a następnie wybierz **pozycję Serwer proxy aplikacji** w lewej nawigacji. 

2. Na stronie Ustawienia serwera proxy aplikacji wprowadź **nazwę,** jeśli dodajesz własną aplikację lokalną.

3.  W polu **Wewnętrzny adres URL** wprowadź wewnętrzny adres URL aplikacji.
   
4. W polu **Adres url zewnętrzny** rozwijana lista i wybierz domenę niestandardową, której chcesz użyć.
   
5. Wybierz pozycję **Dodaj**.
   
   ![Wybieranie domeny niestandardowej](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Jeśli domena ma już certyfikat, w polu **Certyfikat** są wyświetlane informacje o certyfikacie. W przeciwnym razie wybierz pole **Certyfikat.** 
   
   ![Kliknij, aby przekazać certyfikat](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na stronie **certyfikatu SSL** przejdź do pliku certyfikatu PFX i wybierz go. Wprowadź hasło certyfikatu i wybierz pozycję **Przekaż certyfikat**. Aby uzyskać więcej informacji o certyfikatach, zobacz sekcję [Certyfikaty dla domen niestandardowych.](#certificates-for-custom-domains)
   
   ![Przekaż certyfikat](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Domena niestandardowa potrzebuje tylko swojego certyfikatu przesłanego tylko raz. Następnie przekazany certyfikat jest stosowany automatycznie podczas korzystania z domeny niestandardowej dla innych aplikacji.
   
8. Jeśli dodano certyfikat, na stronie **Proxy aplikacji** wybierz pozycję **Zapisz**. 
   
9. Na pasku informacji na stronie **Serwer proxy aplikacji** zanotuj wpis CNAME, który należy dodać do strefy DNS. 
   
   ![Dodaj wpis DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Postępuj zgodnie z instrukcjami [w witrynie Zarządzaj rekordami DNS i zestawami rekordów za pomocą witryny Azure Portal,](../../dns/dns-operations-recordsets-portal.md) aby dodać rekord DNS, który przekierowuje nowy zewnętrzny adres URL do *domeny msappproxy.net.*
   
11. Aby sprawdzić, czy rekord DNS jest poprawnie skonfigurowany, użyj polecenia [nslookup,](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) aby potwierdzić, że zewnętrzny adres URL jest osiągalny, a *msapproxy.net* domena jest wyświetlana jako alias.

Aplikacja jest teraz skonfigurowana do używania domeny niestandardowej. Przed przetestowaniem lub zwolnieniem należy przypisać użytkowników do aplikacji. 

Aby zmienić domenę aplikacji, wybierz inną domenę z listy rozwijanej w **zewnętrznym adresie URL** na stronie **serwera proxy aplikacji.** Przekaż certyfikat zaktualizowanej domeny, jeśli to konieczne, i zaktualizuj rekord DNS. Jeśli nie widzisz żądanej domeny niestandardowej na liście rozwijanej w **zewnętrznym adresie URL,** może ona nie zostać zweryfikowana.

Aby uzyskać bardziej szczegółowe instrukcje dotyczące serwera proxy aplikacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certyfikaty dla domen niestandardowych

Certyfikat tworzy bezpieczne połączenie TLS dla domeny niestandardowej. 

### <a name="certificate-formats"></a>Formaty certyfikatów

Należy użyć certyfikatu PFX, aby upewnić się, że wszystkie wymagane certyfikaty pośrednie są uwzględnione. Certyfikat musi zawierać klucz prywatny.

Metody podpisu certyfikatu nie są pewne. Kryptografii krzywej eliptycznej (ECC), nazwa alternatywna podmiotu (SAN) i inne typowe typy certyfikatów są obsługiwane. 

Certyfikaty symboli wieloznacznych można używać tak długo, jak długo symbol wieloznaczny jest zgodny z zewnętrznym adresem URL. W przypadku aplikacji wieloznacznych należy używać certyfikatów [wieloznacznych.](application-proxy-wildcard.md) Jeśli chcesz użyć certyfikatu, aby uzyskać dostęp do poddomen, musisz dodać symbole wieloznaczne poddomeny jako alternatywne nazwy podmiotu w tym samym certyfikacie. Na przykład certyfikat * \** dla adventure-works.com nie będzie działać dla * \*apps.adventure-works.com,* chyba że zostanie dodana * \*.apps.adventure-works.com* jako alternatywna nazwa podmiotu. 

Certyfikaty wystawione przez własną infrastrukturę kluczy publicznych (PKI) można użyć, jeśli łańcuch certyfikatów jest zainstalowany na urządzeniach klienckich. Usługa Intune może wdrażać te certyfikaty na zarządzanych urządzeniach. W przypadku urządzeń niem zarządzanych należy ręcznie zainstalować te certyfikaty. 

Nie zaleca się używania prywatnego głównego urzędu certyfikacji, ponieważ prywatny główny urząd certyfikacji również musiałby zostać przesunięty na komputery klienckie, co może powodować wiele wyzwań.

### <a name="certificate-management"></a>Zarządzanie certyfikatami

Zarządzanie certyfikatami odbywa się za pośrednictwem poszczególnych stron aplikacji. Przejdź do strony **serwera proxy aplikacji,** aby uzyskać dostęp do pola **Certyfikat.**

Tego samego certyfikatu można używać dla wielu aplikacji. Jeśli przekazany certyfikat działa z inną aplikacją, zostanie on automatycznie zastosowany. Nie zostanie wyświetlony monit o ponowne przekazanie aplikacji. 

Po wygaśnięciu certyfikatu pojawia się ostrzeżenie z informacją o przekazaniu innego certyfikatu. Jeśli certyfikat zostanie odwołany, użytkownicy mogą zobaczyć ostrzeżenie o zabezpieczeniach podczas uzyskiwania dostępu do aplikacji. Aby zaktualizować certyfikat dla aplikacji, przejdź do strony **Proxy aplikacji,** wybierz **pozycję Certyfikat**i przekaż nowy certyfikat. Jeśli stary certyfikat nie jest używany przez inne aplikacje, jest usuwany automatycznie. 

## <a name="next-steps"></a>Następne kroki
* [Włącz logowanie jednokrotne w](application-proxy-configure-single-sign-on-with-kcd.md) opublikowanych aplikacjach za pomocą uwierzytelniania usługi Azure AD.
* [Włącz dostęp warunkowy](../conditional-access/overview.md) do opublikowanych aplikacji.

