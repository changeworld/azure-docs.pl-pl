---
title: Przenieś aplikacje z usług AD FS do usługi Azure AD. | Microsoft Docs
description: Ten artykuł ma ułatwić organizacjom zrozumienie sposobu przenoszenia aplikacji do usługi Azure AD, ze szczególnym uwzględnieniem federacyjnych aplikacji SaaS.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec825a562b57f081305af20ee6a6ce078d5c0505
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159016"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>Przenieś aplikacje z usług AD FS do usługi Azure AD 

Ten artykuł pomoże Ci zrozumieć, jak przenieść aplikacje z usług AD FS do usługi Azure Active Directory (Azure AD). W szczególności koncentruje się na federacyjnych aplikacjach SaaS.

Ten artykuł nie zawiera wskazówek krok po kroku. Podano w nim ogólne wskazówki, które pomogą pomyślnie przeprowadzić migrację dzięki zrozumieniu sposobu przekładania konfiguracji lokalnych na usługę Azure AD. Obejmuje on również typowe scenariusze.

## <a name="introduction"></a>Wprowadzenie

Jeśli masz katalog lokalny, który zawiera konta użytkownika, prawdopodobnie masz również co najmniej jedną lub dwie aplikacje. A aplikacje te zostały skonfigurowane dla użytkowników tak, aby umożliwić im dostęp przez zalogowanie przy użyciu tych tożsamości.

I jeśli Twoja firma działa tak jak większość organizacji, prawdopodobnie jesteś właśnie w trakcie implementowania tożsamości i aplikacji w chmurze. Być może pracujesz z usługą Office 365 i programem Azure AD Connect. Możliwe, że dla niektórych kluczowych obciążeń skonfigurowano oparte na chmurze aplikacje SaaS, ale nie dla wszystkich.  

Wiele organizacji często ma — oprócz aplikacji opartych na usługach Office 365 i Azure AD — aplikacje SaaS lub niestandardowe aplikacje biznesowe sfederowane bezpośrednio z lokalną usługą logowania, taką jak Active Directory Federation Service (AD FS). Ten przewodnik opisuje dlaczego i jak przenieść swoje aplikacje do usługi Azure AD.

> [!NOTE]
> Ten przewodnik zawiera szczegółowe informacje dotyczące konfiguracji i migracji aplikacji SaaS oraz ogólne informacje o niestandardowych aplikacjach biznesowych. W przyszłości planujemy opublikowanie bardziej szczegółowych wskazówek dotyczących niestandardowych aplikacji biznesowych.

![Aplikacje połączone bezpośrednio lokalnie](media/migrate-adfs-apps-to-azure/migrate1.png)

![Aplikacje federacyjne za pomocą usługi Azure AD](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>Przyczyny przenoszenia aplikacji do usługi Azure AD

Dla organizacji, która już korzysta z usług AD FS, Ping lub innego dostawcy uwierzytelniania lokalnego przenoszenie aplikacji do usługi Azure AD zapewnia następujące korzyści:

- **Bezpieczniejszy dostęp**

  - Konfigurowanie szczegółowych kontroli dostępu dla poszczególnych aplikacji, w tym usługi Azure Multi-Factor Authentication, przy użyciu [dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md). Zasady można stosować do aplikacji SaaS i niestandardowych w taki sam sposób, w jaki mogą być one stosowane obecnie w usłudze Office 365.
  - Aby wykrywać zagrożenia i pomóc chronić logowanie w oparciu o uczenie maszynowe i algorytmy heurystyczne identyfikujące ryzykowny ruch, skorzystaj z usługi [Azure AD Identity Protection](../active-directory-identityprotection.md).

- **Współpraca B2B w usłudze Azure AD**

  Gdy logowanie do aplikacji SaaS opiera się na usłudze Azure AD, można przyznać partnerom prawa dostępu do zasobów w chmurze dzięki funkcji [współpracy B2B w usłudze Azure AD](../b2b/what-is-b2b.md).

- **Prostsze środowisko pracy administratora i dodatkowe możliwości usługi Azure AD**
  
  Usługa Azure AD jako dostawca tożsamości dla aplikacji SaaS obsługuje dodatkowe możliwości, takie jak:
  - Certyfikaty podpisywania tokenu dla poszczególnych aplikacji.
  - [Konfigurowalne daty wygaśnięcia certyfikatów](manage-certificates-for-federated-single-sign-on.md).
  - [Automatyczna aprowizacja](../app-provisioning/user-provisioning.md) kont użytkowników (w kluczowych aplikacjach z witryny Azure Marketplace) w oparciu o tożsamości usługi Azure AD.

- **Zachowanie korzyści oferowanych przez lokalnego dostawcę tożsamości**
  
  Zyskujesz korzyści ze stosowania usługi Azure AD, a jednocześnie możesz nadal używać lokalnego rozwiązania do uwierzytelniania. Dzięki temu korzyści takie jak lokalne rozwiązania do uwierzytelniania wieloskładnikowego, rejestrowanie i inspekcja są nadal dostępne.

- **Pomoc przy wycofywaniu lokalnego dostawcy tożsamości**
  
  W przypadku organizacji, które chcą wycofać lokalnie instalowanym produktem uwierzytelniania przenoszenie aplikacji do usługi Azure AD umożliwia łatwiejsze przejście dzięki eliminowaniu niektórych prac do końca.

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mapowanie typów aplikacji w środowisku lokalnym na typy aplikacji w usłudze Azure AD

Większość aplikacji można dopasować do jednej z kilku kategorii na podstawie używanego typu logowania. Te kategorie określają, jak aplikacja jest reprezentowana w usłudze Azure AD.

Krótko mówiąc, aplikacje SAML 2.0 można zintegrować z usługą Azure AD za pomocą galerii aplikacji usługi Azure AD w witrynie Marketplace lub jako aplikacje spoza witryny Marketplace. Aplikacje używające uwierzytelniania OAuth 2.0 lub OpenID Connect można podobnie zintegrować z usługą Azure AD jako *rejestracje aplikacji*. Dalsze informacje znajdziesz poniżej.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Federacyjne aplikacje SaaS a niestandardowe aplikacje biznesowe

Aplikacje federacyjne to aplikacje, które należą do następujących kategorii:

- Aplikacje SaaS 
    - Jeśli użytkownicy logują się do aplikacji SaaS, takich jak Salesforce, ServiceNow lub Workday, i przeprowadzana jest integracja z lokalnym dostawcą tożsamości, takim jak usługi AD FS lub Ping, używasz logowania federacyjnego dla aplikacji SaaS.
    - Aplikacje zazwyczaj używają protokołu SAML 2.0 na potrzeby logowania federacyjnego.
    - Aplikacje, które należą do tej kategorii, można zintegrować z usługą Azure AD jako aplikacje przedsiębiorstwa, pochodzące z witryny Marketplace lub spoza niej.
- Niestandardowe aplikacje biznesowe
    - Ta nazwa odnosi się do aplikacji innych niż SaaS, które opracowano wewnętrznie w organizacji lub które są dostępne jako standardowy produkt w pakiecie zainstalowanym w centrum danych. Obejmuje to aplikacje programu SharePoint i aplikacje skompilowane w rozwiązaniu Windows Identity Foundation.
    - Aplikacje mogą używać uwierzytelniania SAML 2.0, usług federacyjnych w Internecie, OAuth lub OpenID Connect na potrzeby logowania federacyjnego.
    - Aplikacje niestandardowe używające uwierzytelniania OAuth 2.0, OpenID Connect lub usług federacyjnych w Internecie można zintegrować z usługą Azure AD jako rejestracje aplikacji. Aplikacje niestandardowe, które używają uwierzytelniania SAML 2.0 lub usług federacyjnych w Internecie, można zintegrować jako aplikacje spoza witryny Marketplace w aplikacjach dla przedsiębiorstw.

### <a name="non-federated-apps"></a>Aplikacje inne niż federacyjne

Aplikacje inne niż federacyjne można zintegrować z usługą Azure AD przy użyciu serwera proxy aplikacji usługi Azure AD i powiązanych możliwości. Aplikacje inne niż federacyjne obejmują:

- Aplikacje korzystające ze zintegrowanego uwierzytelniania systemu Windows bezpośrednio z usługą Active Directory. Te aplikacje można zintegrować z usługą Azure AD za pomocą [serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md).
- Aplikacje integrowane z dostawcą logowania jednokrotnego za pośrednictwem agenta i używające nagłówków na potrzeby autoryzacji. Aplikacje lokalne, które używają zainstalowanego agenta do logowania oraz autoryzacji opartej na nagłówkach, można skonfigurować na potrzeby logowania opartego na usłudze Azure AD za pośrednictwem serwera proxy aplikacji usługi Azure AD i funkcji [dostępu usługi Ping dla usługi Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Tłumaczenie lokalnych aplikacji federacyjnych na usługę Azure AD

Usługi AD FS i Azure AD działają podobnie, dlatego pojęcia dotyczące konfigurowania relacji zaufania, adresów URL związanych z logowaniem i wylogowywaniem oraz identyfikatorów można stosować w obydwu przypadkach. Istnieją jednak niewielkie różnice, które należy znać podczas dokonywania przejścia.

Następujące tabele mapują kluczowe koncepcje współdzielone przez usługi AD FS i Azure AD oraz aplikacje SaaS, co ułatwia ich przekład.

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Reprezentowanie aplikacji w usługach Azure AD lub AD FS

Migracja rozpoczyna się od oceny konfiguracji aplikacji w środowisku lokalnym i sposobu mapowania tej konfiguracji do usługi Azure AD. Poniższa tabela przedstawia mapowanie elementów konfiguracji jednostek uzależnionych usług AD FS na odpowiednie elementy w usłudze Azure AD.

- Termin dotyczący usług AD FS: jednostka uzależniona lub zaufanie jednostki uzależnionej.
- Termin dotyczący usługi Azure AD: aplikacja dla przedsiębiorstw lub rejestracja aplikacji (w zależności od typu aplikacji).

|Element konfiguracji aplikacji|Opis|Lokalizacja w konfiguracji usług AD FS|Odpowiednia lokalizacja w konfiguracji usługi Azure AD|Element tokenu języka SAML|
|-----|-----|-----|-----|-----|
|Adres URL logowania do aplikacji|Adres URL strony logowania do danej aplikacji. Jest to miejsce, do którego użytkownik przechodzi w celu zalogowania się do aplikacji w ramach przepływu SAML zainicjowanego przez dostawcę usług.|Nie dotyczy|W usłudze Azure AD adres URL logowania jest konfigurowany w witrynie Azure Portal w obszarze właściwości **Logowanie jednokrotne** aplikacji jako adres URL logowania.</br></br>(W celu wyświetlenia adresu URL logowania może być konieczne kliknięcie pozycji **Pokaż zaawansowane ustawienia adresu URL**).|Nie dotyczy|
|Adres URL odpowiedzi aplikacji|Adres URL aplikacji z perspektywy dostawcy tożsamości. Jest to miejsce, do którego wysyła się użytkownika i token, gdy użytkownik zaloguje się do dostawcy tożsamości.</br></br> Jest ono również czasem nazywane „punktem końcowym odbiorcy potwierdzeń SAML”.|Znajduje się w relacji zaufania jednostki uzależnionej usług AD FS dla aplikacji. Kliknij prawym przyciskiem myszy jednostkę uzależnioną, wybierz pozycję **Właściwości**, a następnie wybierz kartę **Punkty końcowe**.|W usłudze Azure AD adres URL odpowiedzi jest konfigurowany w witrynie Azure Portal w obszarze właściwości **Logowanie jednokrotne** aplikacji jako adres URL odpowiedzi.</br></br>(W celu wyświetlenia adresu URL odpowiedzi może być konieczne kliknięcie pozycji **Pokaż zaawansowane ustawienia adresu URL**).|Jest on mapowany na element **Destination** w tokenie języka SAML.</br></br> Przykładowa wartość: `https://contoso.my.salesforce.com`|
|Adres URL wylogowania aplikacji|Adres URL, do którego są wysyłane żądania „czyszczenia wylogowania”, gdy użytkownik wylogowuje się z aplikacji — ma to na celu wylogowanie ze wszystkich innych aplikacji, do których dostawca tożsamości zalogował użytkownika.|Znajduje się w funkcji zarządzania usługami AD FS w obszarze **Relacje zaufania jednostek uzależnionych**. Kliknij prawym przyciskiem myszy jednostkę uzależnioną, wybierz pozycję **Właściwości**, a następnie wybierz kartę **Punkty końcowe**.|Nie dotyczy. Usługa Azure AD nie obsługuje „wylogowania jednokrotnego”, czyli wylogowania ze wszystkich aplikacji. Wylogowuje ona po prostu użytkownika z samej usługi Azure AD.|Nie dotyczy|
|Identyfikator aplikacji|Identyfikator aplikacji z perspektywy dostawcy tożsamości. Wartość adresu URL logowania jest często używana jako identyfikator (ale nie zawsze).</br></br> Czasami w aplikacji ta wartość jest nazywana „identyfikatorem jednostki”.|W usługach AD FS jest to identyfikator jednostki uzależnionej. Kliknij prawym przyciskiem myszy relację zaufania jednostki uzależnionej, wybierz polecenie **Właściwości**, a następnie wybierz kartę **Identyfikatory**.|W usłudze Azure AD identyfikator jest konfigurowany we właściwościach **Logowanie jednokrotne** aplikacji w witrynie Azure Portal jako identyfikator w obszarze **Domena i adresy URL**. (Może być konieczne zaznaczenie pola wyboru **Pokaż zaawansowane ustawienia adresu URL**).|Odpowiada elementowi **Audience** w tokenie języka SAML.|
|Metadane federacji aplikacji|Lokalizacja metadanych federacji aplikacji. Używana przez dostawcę tożsamości do automatycznego aktualizowania określonych ustawień konfiguracji, takich jak punkty końcowe lub certyfikaty szyfrowania.|Adres URL metadanych federacji aplikacji znajduje się w relacji zaufania jednostki uzależnionej usług AD FS dla aplikacji. Kliknij prawym przyciskiem myszy relację zaufania, a następnie wybierz polecenie **Właściwości** i kliknij kartę **Monitorowanie**.|Nie dotyczy. Usługa Azure AD nie obsługuje bezpośredniego korzystania z metadanych federacji aplikacji.|Nie dotyczy|
|Identyfikator użytkownika/element **NameID**|Atrybut używany do jednoznacznego wskazywania tożsamości użytkownika z usług Azure AD lub AD FS do aplikacji.</br></br> Ten atrybut to przeważnie główna nazwa lub adres e-mail użytkownika.|W usługach AD FS jest to reguła oświadczenia powiązana z jednostką uzależnioną. W większości przypadków reguła oświadczenia wystawia oświadczenie z typem kończącym się ciągiem „nameidentifier”.|W usłudze Azure AD identyfikator użytkownika można znaleźć we właściwościach **Logowanie jednokrotne** aplikacji w witrynie Azure Portal w obszarze **Atrybuty użytkownika**.</br></br>Domyślnie jest używana główna nazwa użytkownika.|Wartość przekazywana przez dostawcę tożsamości do aplikacji jako element **NameID** w tokenie języka SAML.|
|Inne oświadczenia do wysłania do aplikacji|Oprócz identyfikatora użytkownika/elementu **NameID** dostawca tożsamości przeważnie wysyła do aplikacji inne informacje o oświadczeniu. Na przykład imię, nazwisko, adres e-mail i grupy, do których należy użytkownik.|W usługach AD FS są to inne reguły oświadczenia powiązane z jednostką uzależnioną.|W usłudze Azure AD można je znaleźć we właściwościach **Logowanie jednokrotne** aplikacji w witrynie Azure Portal w obszarze **Atrybuty użytkownika**. Wybierz pozycję **Wyświetl** i edytuj wszystkie inne atrybuty użytkownika.|Nie dotyczy|

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Reprezentowanie usługi Azure AD jako dostawcy tożsamości w aplikacji SaaS
W ramach migracji aplikację należy skonfigurować tak, aby wskazywała usługę Azure AD (w odróżnieniu od lokalnego dostawcy tożsamości). W tej sekcji skupiono się na aplikacjach SaaS, które używają protokołu SAML, a nie na niestandardowych aplikacjach biznesowych. Pojęcia będą jednak dotyczyć również niestandardowych aplikacji biznesowych.

Na poziomie ogólnym istnieje kilka kluczowych elementów wskazujących usługę Azure AD aplikacji SaaS:

- Wystawca dostawcy tożsamości: https&#58;//sts.windows.net/{identyfikator-dzierżawy}/
- Adres URL logowania dostawcy tożsamości: https&#58;//login.microsoftonline.com/{identyfikator-dzierżawy}/saml2
- Adres URL wylogowywania dostawcy tożsamości: https&#58;//login.microsoftonline.com/{identyfikator-dzierżawy}/saml2 
- Lokalizacja metadanych federacji: https&#58;//login.windows.net/{identyfikator-dzierżawy}/federationmetadata/2007-06/federationmetadata.xml?appid={identyfikator-aplikacji} 

Zastąp zmienną {identyfikator-dzierżawy} identyfikatorem dzierżawy znajdującym się w witrynie Azure Portal w obszarze **Azure Active Directory** > **Właściwości** w polu **Identyfikator katalogu**. Zastąp zmienną {identyfikator-aplikacji} identyfikatorem aplikacji znajdującym się we właściwościach aplikacji w polu **Identyfikator aplikacji**.

Poniższa tabela opisuje kluczowe elementy konfiguracji dostawcy tożsamości służące do konfigurowania ustawień logowania jednokrotnego w aplikacji oraz ich wartości lub lokalizacje w usługach AD FS i Azure AD. Układem odniesienia tabeli jest aplikacja SaaS, która musi wiedzieć, dokąd wysyłać żądania uwierzytelniania i jak weryfikować odebrane tokeny.

|Element konfiguracji|Opis|AD FS|Azure AD|
|---|---|---|---|
|Dostawca tożsamości </br>Logowanie </br>Adres URL|Adres URL logowania dostawcy tożsamości z perspektywy aplikacji (do którego użytkownik jest przekierowywany na potrzeby logowania).|Adres URL logowania usług AD FS to nazwa usługi federacyjnej AD FS, po której następuje ciąg „/adfs/ls/”. Na przykład: https&#58;//fs.contoso.com/adfs/ls/|Odpowiadająca wartość w usłudze Azure AD jest zgodna z wzorcem, w którym zmienna {identyfikator-dzierżawy} jest zastępowana identyfikatorem dzierżawy. Znaleźć go można w witrynie Azure Portal w obszarze **Azure Active Directory** > **Właściwości** w polu **Identyfikator katalogu**.</br></br>W przypadku aplikacji korzystających z protokołu SAML-P: https&#58;//login.microsoftonline.com/{identyfikator-dzierżawy}/saml2 </br></br>W przypadku aplikacji korzystających z protokołu usług federacyjnych w Internecie: https&#58;//login.microsoftonline.com/{identyfikator-dzierżawy}/wsfed|
|Dostawca tożsamości </br>wylogowanie </br>Adres URL|Adres URL wylogowania dostawcy tożsamości z perspektywy aplikacji (do którego użytkownik jest przekierowywany w celu wylogowania z aplikacji).|W usługach AD FS adres URL wylogowania jest taki sam jak adres URL logowania lub jest to ten sam adres URL z dołączonym ciągiem „wa=wsignout1.0”. Na przykład: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|Odpowiadająca wartość w usłudze Azure AD zależy od tego, czy aplikacja obsługuje wylogowywanie w protokole SAML 2.0.</br></br>Jeśli aplikacja obsługuje wylogowywanie w protokole SAML, wartość jest zgodna z wzorcem, w którym wartość {identyfikator-dzierżawy} jest zastępowana identyfikatorem dzierżawy. Znaleźć go można w witrynie Azure Portal w obszarze **Azure Active Directory** > **Właściwości** w polu **Identyfikator katalogu**: https&#58;//login.microsoftonline.com/{identyfikator-dzierżawy}/saml2</br></br>Jeśli aplikacja nie obsługuje wylogowywania w protokole SAML: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Token </br>podpisywanie </br>certyfikat|Certyfikat, którego klucz prywatny jest używany przez dostawcę tożsamości do podpisywania wystawionych tokenów. Weryfikuje, że token pochodzi od tego samego dostawcy tożsamości, z którym aplikacja ma skonfigurowaną relację zaufania.|Certyfikat podpisywania tokenu usług AD FS znajduje się w funkcji zarządzania usługami AD FS w obszarze **Certyfikaty**.|W usłudze Azure AD certyfikat podpisywania tokenu można znaleźć we właściwościach **Logowanie jednokrotne** aplikacji w witrynie Azure Portal w obszarze **Certyfikat podpisywania SAML**. W tym miejscu możesz pobrać certyfikat, aby następnie przekazać go do aplikacji.</br></br> Jeśli aplikacja ma więcej niż jeden certyfikat, wszystkie certyfikaty można znaleźć w pliku XML metadanych federacji.|
|Identyfikator/</br>„wystawca”|Identyfikator dostawcy tożsamości z perspektywy aplikacji (czasami nazywany „identyfikatorem wystawcy”).</br></br>W tokenie języka SAML wartość jest wyświetlana jako element **Issuer**.|Identyfikator w usługach AD FS to zazwyczaj identyfikator usługi federacyjnej w funkcji zarządzania usługami AD FS w obszarze **Usługa** > **Edytuj właściwości usługi federacyjnej**. Na przykład: http&#58;//fs.contoso.com/adfs/services/trust|Odpowiadająca wartość w usłudze Azure AD jest zgodna z wzorcem, w którym wartość {identyfikator-dzierżawy} jest zastępowana identyfikatorem dzierżawy. Znaleźć go można w witrynie Azure Portal w obszarze **Azure Active Directory** > **Właściwości** w polu **Identyfikator katalogu**: https&#58;//sts.windows.net/{identyfikator-dzierżawy}/|
|Dostawca tożsamości </br>federacja </br>metadane|Lokalizacja publicznie dostępnych metadanych federacji dostawcy tożsamości. (Niektóre aplikacje używają metadanych federacji jako alternatywy sytuacji, w której administrator indywidualnie konfiguruje adresy URL, identyfikator i certyfikat podpisywania tokenu).|Adres URL metadanych federacji usług AD FS można znaleźć w funkcji zarządzania usługami AD FS w obszarze **Usługa** > **Punkty końcowe** > **Metadane** > **Typ: Metadane federacji**. Na przykład: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|Odpowiadająca wartość w usłudze Azure AD jest zgodna z wzorcem https&#58;//login.microsoftonline.com/{NazwaDomenyDzierżawy}/FederationMetadata/2007-06/FederationMetadata.xml. Wartość {NazwaDomenyDzierżawy} jest zastępowana nazwą dzierżawy w formacie „contoso.onmicrosoft.com”. </br></br>Aby uzyskać więcej informacji, zobacz [Metadane federacji](../azuread-dev/azure-ad-federation-metadata.md).

## <a name="moving-saas-apps"></a>Przenoszenie aplikacji SaaS

Przenoszenie aplikacji SaaS z usług AD FS lub innego dostawcy tożsamości do usługi Azure AD jest obecnie procesem wykonywanym ręcznie. Aby uzyskać wskazówki specyficzne dla aplikacji, zobacz [listę samouczków dotyczących integrowania aplikacji SaaS znajdujących się w witrynie Marketplace](../saas-apps/tutorial-list.md).

W samouczkach integracji założono, że integracja jest przeprowadzana w trybie Green Field. Jeśli planujesz, oceniasz i konfigurujesz aplikacje lub przeprowadzasz ich migrację jednorazową, należy poznać kilka kluczowych pojęć specyficznych dla migracji:  

- Migracja niektórych aplikacji jest prosta. Aplikacje o bardziej złożonych wymaganiach, takich jak oświadczenia niestandardowe, mogą wymagać dodatkowej konfiguracji w usłudze Azure AD i/lub programie Azure AD Connect.
- W najbardziej typowych scenariuszach aplikacja wymaga tylko oświadczenia **NameID** i innych typowych oświadczeń identyfikatora użytkownika. Aby określić, czy są wymagane jakieś dodatkowe oświadczenia, sprawdź, jakie oświadczenia są wystawiane z usług AD FS lub dostawcy tożsamości innej firmy.
- Po ustaleniu, że dodatkowe oświadczenia są wymagane, upewnij się, że są one dostępne w usłudze Azure AD. Sprawdź konfigurację synchronizacji programu Azure AD Connect, aby upewnić się, że wymagany atrybut, na przykład **samAccountName**, jest synchronizowany z usługą Azure AD.
- Gdy atrybuty są dostępne w usłudze Azure AD, możesz dodać reguły wystawiania oświadczeń w usłudze Azure AD, aby uwzględnić te atrybuty jako oświadczenia w wystawionych tokenach. Te reguły należy dodać we właściwościach **Logowanie jednokrotne** aplikacji w usłudze Azure AD.

### <a name="assess-what-can-be-moved"></a>Ocenianie, co można przenieść

Aplikacje SAML 2.0 można zintegrować z usługą Azure AD za pomocą galerii aplikacji usługi Azure AD w witrynie Marketplace lub jako aplikacje spoza witryny Marketplace.  

Pewne konfiguracje wymagają wykonania dodatkowych czynności w celu skonfigurowania w usłudze Azure AD, a niektóre konfiguracje nie są obecnie obsługiwane. Aby ustalić, co można przenieść, zapoznaj się z bieżącą konfiguracją każdej aplikacji. W szczególności szukaj następujących informacji:

- Skonfigurowane reguły oświadczeń (reguły przekształcania wystawiania).
- Atrybut i format identyfikatora SAML **NameID**.
- Wystawione wersje tokenu języka SAML.
- Inne konfiguracje, takie jak reguły autoryzacji wystawiania lub zasady kontroli dostępu i reguły uwierzytelniania wieloskładnikowego (uwierzytelniania dodatkowego).

#### <a name="what-can-be-moved-today"></a>Co można przenieść już dziś

Aplikacje, które można obecnie łatwo przenieść to aplikacje SAML 2.0 używające standardowego zestawu elementów konfiguracji i oświadczeń. Te aplikacje mogą się składać z następujących elementów:

- Nazwa główna użytkownika.
- Adres e-mail.
- Imię.
- Nazwisko.
- Atrybut alternatywny, taki jak **NameID** w języku SAML, w tym atrybut poczty usługi Azure AD, prefiks poczty, identyfikator pracownika, atrybuty rozszerzenia 1–15 lub lokalny atrybut **SamAccountName**. Aby uzyskać więcej informacji, zobacz [Edytowanie oświadczenia NameIdentifier](../develop/active-directory-saml-claims-customization.md).
- Oświadczenia niestandardowe. Aby uzyskać informacje dotyczące obsługiwanych mapowań oświadczeń, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory](../develop/active-directory-claims-mapping.md) i [Dostosowywanie oświadczeń wystawionych w tokenie języka SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Oprócz oświadczeń niestandardowych i elementów **NameID**, konfiguracje, które wymagają wykonania dodatkowych kroków konfiguracji w usłudze Azure AD w ramach migracji, to:

- Niestandardowe reguły autoryzacji lub uwierzytelniania wieloskładnikowego w usługach AD FS. Można je skonfigurować za pomocą funkcji [dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md) .
- Aplikacje z wieloma punktami końcowymi języka SAML. Można je skonfigurować w usłudze Azure AD za pomocą programu PowerShell. (Ta możliwość nie jest dostępna w portalu).
- Aplikacje usług federacyjnych w Internecie, takie jak aplikacje programu SharePoint, które wymagają tokenów w wersji 1.1 protokołu SAML. Należy je skonfigurować ręcznie przy użyciu programu PowerShell.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikacje i konfiguracje, które nie są obecnie obsługiwane w usłudze Azure AD

Obecnie nie można migrować aplikacji, które wymagają wymienionych poniżej możliwości. Jeśli masz aplikacje, które wymagają tych funkcji, wprowadź swoją opinię w sekcji komentarzy.

- Możliwości protokołu:
    - Obsługa wylogowania jednokrotnego protokołu SAML ze wszystkich aplikacji, do których się zalogowano.
    - Obsługa wzorca WS-Trust ActAs.
    - Rozpoznawanie artefaktów języka SAML.
    - Weryfikacja podpisu podpisanych żądań języka SAML. Pamiętaj, że podpisane żądania są akceptowane, ale podpis nie jest weryfikowany.
- Możliwości tokenów:
    - Szyfrowanie tokenu języka SAML.
    - Tokeny języka SAML w wersji 1.1 w odpowiedziach protokołu SAML.
- Oświadczenia w możliwościach tokenów:
    - Wystawianie nazw grup lokalnych jako oświadczeń.
    - Oświadczenia z magazynów innych niż usługa Azure AD.
    - Złożone reguły przekształcania wystawiania oświadczeń. Aby uzyskać informacje dotyczące obsługiwanych mapowań oświadczeń, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory](../develop/active-directory-claims-mapping.md) i [Dostosowywanie oświadczeń wystawionych w tokenie języka SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](../develop/active-directory-saml-claims-customization.md).
    - Wystawianie rozszerzeń katalogów jako oświadczeń.
    - Niestandardowa specyfikacja formatu **NameID**.
    - Wystawianie atrybutów o wielu wartościach.

> [!NOTE]
> Usługa Azure AD jest stale rozwijana i dodawane są możliwości w tym obszarze. Aktualizujemy ten artykuł na bieżąco.

### <a name="configure-azure-ad"></a>Konfigurowanie usługi Azure AD

#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Konfigurowanie ustawień logowania jednokrotnego dla aplikacji SaaS

W usłudze Azure AD konfigurowanie logowania języka SAML (zgodnie z wymaganiami aplikacji) odbywa się we właściwościach **Logowanie jednokrotne** aplikacji w obszarze **Atrybuty użytkownika**.

![Właściwości logowania jednokrotnego z sekcją „Atrybuty użytkownika”](media/migrate-adfs-apps-to-azure/migrate3.png)

Wybierz pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika**, aby zobaczyć atrybuty do wysłania jako oświadczenia w tokenie zabezpieczającym.

![Pokazuje listę atrybutów, które można wysyłać jako oświadczenia](media/migrate-adfs-apps-to-azure/migrate4.png)

Wybierz wiersz konkretnego atrybutu, aby go edytować, lub wybierz pozycję **Dodaj atrybut**, aby dodać nowy atrybut.

![Pokazuje okienko "Edytuj atrybut"](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Przypisywanie użytkowników do aplikacji

Aby użytkownicy w usłudze Azure AD mogli logować się do aplikacji SaaS, musisz udzielić im dostępu.

Aby przypisać użytkowników w portalu usługi Azure AD, przejdź do strony aplikacji SaaS, a następnie wybierz pozycję **Użytkownicy i grupy** na pasku bocznym. Aby dodać użytkownika lub grupę, wybierz pozycję **Dodaj użytkownika** w górnej części okienka.

![Przycisk „Dodaj użytkownika” w okienku „Użytkownicy i grupy”](media/migrate-adfs-apps-to-azure/migrate6.png)

![Pokazuje okienko "Dodawanie przypisania"](media/migrate-adfs-apps-to-azure/migrate7.png)

Aby zweryfikować dostęp: aplikacja SaaS powinna być widoczna w [panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md) użytkownika po zalogowaniu. Panel dostępu można znaleźć pod adresem https://myapps.microsoft.com. W tym przykładzie użytkownikowi pomyślnie przypisano prawa dostępu do aplikacji Salesforce i ServiceNow.

![Przykład panelu dostępu z aplikacjami Salesforce i ServiceNow](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Konfigurowanie aplikacji SaaS

Proces migracji jednorazowej z federacji lokalnej do usługi Azure AD zależy od tego, czy aplikacja SaaS, z którą pracujesz, obsługuje wielu dostawców tożsamości. Poniżej przedstawiono niektóre często zadawane pytania dotyczące obsługi wielu dostawców tożsamości:

   **Pyt.: Co to znaczy, że aplikacja obsługuje wielu dostawców tożsamości?**

   Odp.: Aplikacje SaaS, które obsługują wielu dostawców tożsamości, umożliwiają wprowadzanie wszystkich informacji o nowym dostawcy tożsamości (w naszym przypadku o usłudze Azure AD) przed zatwierdzeniem zmian środowiska logowania. Po zakończeniu konfigurowania można przełączyć konfigurację uwierzytelniania aplikacji tak, aby wskazywała usługę Azure AD.

   **Pyt.: Dlaczego ważne jest, czy aplikacja obsługuje wielu dostawców tożsamości?**

   Odp.: Jeśli opcja wielu dostawców tożsamości nie jest obsługiwana, administrator musi zarezerwować krótki przedział czasu jako okres niedostępności usługi lub niedostępności z powodu konserwacji. W tym okresie usługa Azure AD zostanie skonfigurowana jako nowy dostawca tożsamości aplikacji. Należy powiadomić użytkowników, że podczas tej niedostępności nie będą mogli logować się do swoich kont.

   Jeśli aplikacja obsługuje wielu dostawców tożsamości, konfigurację dodatkowego dostawcy tożsamości można przeprowadzić z wyprzedzeniem. Administrator może wtedy zmienić dostawcę tożsamości podczas jednorazowej migracji platformy Azure.

   Jeśli aplikacja obsługuje wielu dostawców tożsamości i wybierzesz wielu dostawców tożsamości do jednoczesnej obsługi uwierzytelniania podczas logowania, użytkownik może wskazać dostawcę tożsamości na potrzeby uwierzytelnienia na swojej stronie logowania.

#### <a name="example-support-for-multiple-identity-providers"></a>Przykład: obsługa wielu dostawców tożsamości

Na przykład w aplikacji Salesforce konfigurację dostawcy tożsamości można znaleźć w obszarze **Settings (Ustawienia)**  > **Company Settings (Ustawienia firmowe)**  > **My Domain (Moja domena)**  > **Authentication Configuration (Konfiguracja uwierzytelniania)** .

![Sekcja „Authentication Configuration” (Konfiguracja uwierzytelniania) w aplikacji Salesforce](media/migrate-adfs-apps-to-azure/migrate9.png)

Ze względu na konfigurację utworzoną wcześniej w obszarze **Identity (Tożsamość)**  > **Single sign-on settings (Ustawienia logowania jednokrotnego)** powinna być możliwa zmiana dostawcy tożsamości dla konfiguracji uwierzytelniania. Na przykład można go zmienić z usług AD FS na usługę Azure AD.

![Wybieranie usługi Azure AD jako usługi uwierzytelniania](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Opcjonalnie: konfigurowanie aprowizowania użytkowników w usłudze Azure AD

Jeśli chcesz, aby usługa Azure AD bezpośrednio obsługiwała aprowizowanie użytkowników dla danej aplikacji SaaS, zobacz [Automatyzowanie aprowizacji i anulowania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory](what-is-application-management.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
- [Program Azure AD Connect a federacja](../hybrid/how-to-connect-fed-whatis.md)
