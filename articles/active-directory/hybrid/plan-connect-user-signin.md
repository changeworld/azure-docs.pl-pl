---
title: 'Usługa Azure AD Connect: logowanie użytkownika | Dokumenty firmy Microsoft'
description: Użytkownik usługi Azure AD Connect loguje się w celu uzyskania ustawień niestandardowych.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a08120b98c7a08bca50453df59df313b1645c5c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331270"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Opcje logowania użytkownika usługi Azure AD Connect
Usługa Azure Active Directory (Azure AD) Connect umożliwia użytkownikom logowanie się do zasobów w chmurze i lokalnie przy użyciu tych samych haseł. W tym artykule opisano pojęcia kluczowe dla każdego modelu tożsamości, aby ułatwić wybór tożsamości, która ma być używana do logowania się do usługi Azure AD.

Jeśli znasz już model tożsamości usługi Azure AD i chcesz dowiedzieć się więcej o określonej metodzie, zobacz odpowiednie łącze:

* [Synchronizacja skrótów haseł](#password-hash-synchronization) z [bezproblemowym logowaniem jednokrotnym](how-to-connect-sso.md)
* [Uwierzytelnianie przekazywane za](how-to-connect-pta.md) pomocą [bezproblemowego logowania jednokrotnego](how-to-connect-sso.md)
* [Sfederowane przysłówek SYD (z usługami federacyjnymi Active Directory (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federacja z serwerem PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Należy pamiętać, że konfigurując federację dla usługi Azure AD, można ustanowić zaufanie między dzierżawy usługi Azure AD i domen federacyjne. Dzięki temu zaufaniu użytkownicy domeny federacyjnej będą mieli dostęp do zasobów chmury usługi Azure AD w obrębie dzierżawy.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Wybieranie metody logowania użytkownika w organizacji
Pierwszą decyzją o zaimplementowaniu usługi Azure AD Connect jest wybranie metody uwierzytelniania używanej przez użytkowników do logowania. Ważne jest, aby upewnić się, że wybierzesz odpowiednią metodę, która spełnia zabezpieczenia organizacji i zaawansowane wymagania. Uwierzytelnianie ma kluczowe znaczenie, ponieważ będzie sprawdzać poprawność tożsamości użytkowników w celu uzyskania dostępu do aplikacji i danych w chmurze. Aby wybrać odpowiednią metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt wdrożenia wyboru. Te czynniki są różne dla każdej organizacji i mogą się zmieniać w czasie.

Usługa Azure AD obsługuje następujące metody uwierzytelniania: 

* **Uwierzytelnianie w chmurze** — po wybraniu tej metody uwierzytelniania usługa Azure AD obsługuje proces uwierzytelniania logowania użytkownika. Za pomocą uwierzytelniania w chmurze możesz wybrać jedną z dwóch opcji: 
   * **Synchronizacja skrótów haseł (PHS)** — synchronizacja skrótów haseł umożliwia użytkownikom używanie tej samej nazwy użytkownika i hasła, których używają lokalnie bez konieczności wdrażania dodatkowej infrastruktury poza usługą Azure AD Connect. 
   * **Uwierzytelnianie przekazywane (PTA)** — ta opcja jest podobna do synchronizacji skrótów haseł, ale zapewnia proste sprawdzanie poprawności hasła przy użyciu lokalnych agentów oprogramowania dla organizacji z silnymi zasadami zabezpieczeń i zgodności.
* **Uwierzytelnianie federacyjne** — po wybraniu tej metody uwierzytelniania usługa Azure AD przekaże proces uwierzytelniania oddzielnemu zaufanemu systemowi uwierzytelniania, takiemu jak AD FS lub system federacyjne innej firmy, aby sprawdzić poprawność logowania użytkownika. 

W przypadku większości organizacji, które chcą po prostu włączyć logowanie użytkowników do usługi Office 365, aplikacji SaaS i innych zasobów opartych na usłudze Azure AD, zaleca się domyślną opcję synchronizacji skrótów haseł.
 
Aby uzyskać szczegółowe informacje na temat wybierania metody uwierzytelniania, zobacz [Wybieranie właściwej metody uwierzytelniania dla rozwiązania tożsamości hybrydowej usługi Azure Active Directory](../../security/fundamentals/choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Synchronizacja skrótów haseł
Z synchronizacją skrótów haseł skróty haseł użytkowników są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD. Gdy hasła są zmieniane lub resetowane lokalnie, nowe skróty haseł są natychmiast synchronizowane z usługą Azure AD, dzięki czemu użytkownicy mogą zawsze używać tego samego hasła dla zasobów w chmurze i zasobów lokalnych. Hasła nigdy nie są wysyłane do usługi Azure AD lub przechowywane w usłudze Azure AD w postaci zwykłego tekstu. Synchronizacja skrótu hasła wraz z odpisem hasła umożliwia samoobsługowe resetowanie hasła w usłudze Azure AD.

Ponadto można włączyć [bezproblemowe logowanie jednokrotne](how-to-connect-sso.md) dla użytkowników na komputerach przyłączonych do domeny, które znajdują się w sieci firmowej. W przypadku logowania jednokrotnego włączeni użytkownicy muszą tylko wprowadzić nazwę użytkownika, aby ułatwić im bezpieczny dostęp do zasobów w chmurze.

![Synchronizacja skrótów haseł](./media/plan-connect-user-signin/passwordhash.png)

Aby uzyskać więcej informacji, zobacz artykuł [synchronizacji skrótów haseł.](how-to-connect-password-hash-synchronization.md)

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane
W ramach uwierzytelniania przekazywanego hasło użytkownika jest sprawdzane na lokalnym kontrolerze usługi Active Directory. Hasło nie musi być obecny w usłudze Azure AD w dowolnej formie. Dzięki temu zasady lokalne, takie jak ograniczenia godzin logowania, mają być oceniane podczas uwierzytelniania w usługach w chmurze.

Uwierzytelnianie przekazywane używa prostego agenta na komputerze przyłączanym do domeny systemu Windows Server 2012 R2 w środowisku lokalnym. Ten agent nasłuchuje żądań sprawdzania poprawności hasła. Nie wymaga żadnych portów przychodzących, aby być otwarte dla Internetu.

Ponadto można również włączyć logowanie jednokrotne dla użytkowników na komputerach przyłączonych do domeny, które znajdują się w sieci firmowej. W przypadku logowania jednokrotnego włączeni użytkownicy muszą tylko wprowadzić nazwę użytkownika, aby ułatwić im bezpieczny dostęp do zasobów w chmurze.
![Uwierzytelnianie przekazywane](./media/plan-connect-user-signin/pta.png)

Aby uzyskać więcej informacji, zobacz:
- [Uwierzytelnianie przekazywane](how-to-connect-pta.md)
- [Rejestracja jednokrotna](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federacja korzystająca z nowej lub istniejącej farmy z usługą AD FS w systemie Windows Server 2012 R2
Dzięki federacyjnemu logowi użytkownicy mogą logować się do usług opartych na usłudze Azure AD przy ich hasłach lokalnych. Podczas gdy są w sieci firmowej, nie muszą nawet wprowadzać swoich haseł. Korzystając z opcji federacyjnych z usługą AD FS, można wdrożyć nową lub istniejącą farmę z usługami AD FS w systemie Windows Server 2012 R2. Jeśli zdecydujesz się określić istniejącą farmę, usługa Azure AD Connect konfiguruje zaufanie między farmą a usługą Azure AD, aby użytkownicy mogli się logować.

<center>

![Federacja z usługami AD FS w systemie Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Wdrażanie federacji z usługami AD FS w systemie Windows Server 2012 R2

Jeśli wdrażasz nową farmę, potrzebujesz:

* Serwer systemu Windows Server 2012 R2 dla serwera federacyjnego.
* Serwer systemu Windows Server 2012 R2 dla serwera proxy aplikacji sieci Web.
* Plik .pfx z jednym certyfikatem TLS/SSL dla zamierzonej nazwy usługi federacyjnej. Na przykład: fs.contoso.com.

Jeśli wdrażasz nową farmę lub używasz istniejącej farmy, musisz:

* Poświadczenia administratora lokalnego na serwerach federacyjnych.
* Poświadczenia administratora lokalnego na wszystkich serwerach grupy roboczej (nie przyłączonych do domeny), na których zamierzasz wdrożyć rolę serwera proxy aplikacji sieci Web.
* Komputer, na którego uruchomisz kreatora, aby móc połączyć się z innymi komputerami, na których chcesz zainstalować usługi AD FS lub serwer proxy aplikacji sieci Web przy użyciu zdalnego zarządzania systemem Windows.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania sytego przy systemach AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federacja z serwerem PingFederate
Dzięki federacyjnemu logowi użytkownicy mogą logować się do usług opartych na usłudze Azure AD przy ich hasłach lokalnych. Podczas gdy są w sieci firmowej, nie muszą nawet wprowadzać swoich haseł.

Aby uzyskać więcej informacji na temat konfigurowania funkcji PingFederate do użytku z usługą Azure Active Directory, zobacz [PingFederate Integration with Azure Active Directory and Office 365](https://www.pingidentity.com/AzureADConnect)

Aby uzyskać informacje dotyczące konfigurowania usługi Azure AD Connect przy użyciu usługi PingFederate, zobacz [Instalacja niestandardowa usługi Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Logowanie się przy użyciu wcześniejszej wersji usług AD FS lub rozwiązania innej firmy
Jeśli masz już skonfigurowane logowanie do chmury przy użyciu wcześniejszej wersji usług AD FS (takiej jak AD FS 2.0) lub zewnętrznego dostawcy federacyjnego, możesz pominąć konfigurację logowania użytkownika za pośrednictwem usługi Azure AD Connect. Umożliwi to uzyskanie najnowszej synchronizacji i innych funkcji usługi Azure AD Connect przy jednoczesnym użyciu istniejącego rozwiązania do logowania.

Aby uzyskać więcej informacji, zobacz [listę zgodności federacji innej firmy usługi Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Logowanie użytkownika i nazwa główna użytkownika
### <a name="understanding-user-principal-name"></a>Opis nazwy głównej użytkownika
W usłudze Active Directory domyślny sufiks głównej nazwy użytkownika (UPN) to nazwa DNS domeny, w której utworzono konto użytkownika. W większości przypadków jest to nazwa domeny zarejestrowana jako domena przedsiębiorstwa w Internecie. Można jednak dodać więcej sufiksów nazwy UPN przy użyciu domen i relacji zaufania usługi Active Directory.

Nazwa UPN użytkownika ma username@domainformat . Na przykład dla domeny usługi Active Directory o nazwie "contoso.com", użytkownik ojohn@contoso.comimieniu John może mieć nazwę UPN " ". Nazwa UPN użytkownika jest oparta na RFC 822. Mimo że nazwy UPN i poczta e-mail mają ten sam format, wartość nazwy UPN dla użytkownika może być taka sama jak adres e-mail użytkownika.

### <a name="user-principal-name-in-azure-ad"></a>Główna nazwa użytkownika w usłudze Azure AD
Kreator usługi Azure AD Connect używa atrybutu userPrincipalName lub umożliwia określenie atrybutu (w instalacji niestandardowej), który ma być używany z lokalnego jako główna nazwa użytkownika w usłudze Azure AD. Jest to wartość, która jest używana do logowania się do usługi Azure AD. Jeśli wartość atrybutu userPrincipalName nie odpowiada zweryfikowanej domenie w usłudze Azure AD, usługa Azure AD zastępuje ją domyślną wartością onmicrosoft.com.

Każdy katalog w usłudze Azure Active Directory jest wyposażony we wbudowaną nazwę domeny o formacie contoso.onmicrosoft.com, który umożliwia rozpoczęcie korzystania z platformy Azure lub innych usług firmy Microsoft. Można poprawić i uprościć środowisko logowania przy użyciu domen niestandardowych. Aby uzyskać informacje na temat niestandardowych nazw domen w usłudze Azure AD i sposobu weryfikacji domeny, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Konfiguracja logowania się w usłudze Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfiguracja logowania usługi Azure AD za pomocą usługi Azure AD Connect
Środowisko logowania usługi Azure AD zależy od tego, czy usługa Azure AD może być zgodna z sufiksem głównej nazwy użytkownika użytkownika, który jest synchronizowany z jedną z domen niestandardowych zweryfikowanych w katalogu usługi Azure AD. Usługa Azure AD Connect zapewnia pomoc podczas konfigurowania ustawień logowania usługi Azure AD, dzięki czemu środowisko logowania użytkownika w chmurze jest podobne do środowiska lokalnego.

Usługa Azure AD Connect wyświetla listę sufiksów nazwy UPN zdefiniowanych dla domen i próbuje dopasować je do domeny niestandardowej w usłudze Azure AD. Następnie pomaga w odpowiednich działaniach, które należy podjąć.
Strona logowania usługi Azure AD zawiera listę sufiksów nazwy UPN zdefiniowanych dla lokalnej usługi Active Directory i wyświetla odpowiedni stan dla każdego sufiksu. Wartości stanu mogą być jedną z następujących wartości:

| Stan | Opis | Potrzebne działania |
|:--- |:--- |:--- |
| Sprawdzonych |Usługa Azure AD Connect znalazła pasującą zweryfikowaną domenę w usłudze Azure AD. Wszyscy użytkownicy tej domeny mogą logować się przy użyciu swoich poświadczeń lokalnych. |Nie są potrzebne żadne działania. |
| Nie zweryfikowane |Usługa Azure AD Connect znalazła pasującą domenę niestandardową w usłudze Azure AD, ale nie została zweryfikowana. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na domyślny sufiks onmicrosoft.com po synchronizacji, jeśli domena nie zostanie zweryfikowana. | [Sprawdź domenę niestandardową w usłudze Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nie dodano |Usługa Azure AD Connect nie znalazła domeny niestandardowej odpowiadającej sufiksowi nazwy UPN. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na domyślny sufiks onmicrosoft.com ., jeśli domena nie zostanie dodana i zweryfikowana na platformie Azure. | [Dodaj i sprawdź domenę niestandardową odpowiadającą sufiksowi nazwy UPN.](../fundamentals/add-custom-domain.md) |

Strona logowania usługi Azure AD zawiera listę sufiksów nazwy UPN zdefiniowanych dla lokalnej usługi Active Directory i odpowiedniej domeny niestandardowej w usłudze Azure AD z bieżącym stanem weryfikacji. W instalacji niestandardowej można teraz wybrać atrybut dla głównej nazwy użytkownika na stronie **logowania usługi Azure AD.**

![Strona logowania usługi Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Możesz kliknąć przycisk odświeżania, aby ponownie pobrać najnowszy stan domen niestandardowych z usługi Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Wybieranie atrybutu głównej nazwy użytkownika w usłudze Azure AD
Atrybut userPrincipalName jest atrybutem używanym przez użytkowników podczas logowania się do usługi Azure AD i usługi Office 365. Należy sprawdzić domeny (znane również jako sufiksy nazwy UPN), które są używane w usłudze Azure AD przed synchronizacją użytkowników.

Zdecydowanie zaleca się zachowanie domyślnego atrybutu userPrincipalName. Jeśli ten atrybut jest niezdatny do użytku i nie można go zweryfikować, można wybrać inny atrybut (na przykład e-mail) jako atrybut zawierający identyfikator logowania. Jest to nazywane identyfikatorem alternatywnym. Wartość atrybutu identyfikatora alternatywnego musi być zgodna ze standardem RFC 822. Jako rozwiązaniu logowania można użyć alternatywnego identyfikatora z hasłem logowania jednokrotnego i logowania jednokrotnego federacji.

> [!NOTE]
> Używanie alternatywnego identyfikatora nie jest zgodne ze wszystkimi obciążeniami usługi Office 365. Aby uzyskać więcej informacji, zobacz [artykuł opisujący konfigurowanie alternatywnego identyfikatora logowania](https://technet.microsoft.com/library/dn659436.aspx) (strona może zostać wyświetlona w języku angielskim).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Różne stany domeny niestandardowej i ich wpływ na środowisko logowania platformy Azure
Bardzo ważne jest zrozumienie relacji między stanami domeny niestandardowej w katalogu usługi Azure AD a sufiksami nazwy UPN zdefiniowanymi lokalnie. Przejdźmy przez różne możliwe środowiska logowania platformy Azure podczas konfigurowania synchronizacji przy użyciu usługi Azure AD Connect.

Aby uzyskać następujące informacje, załóżmy, że zajmujemy się przyrostkiem upn contoso.com, który jest używany w katalogu lokalnym jako część user@contoso.comupn - na przykład .

###### <a name="express-settingspassword-hash-synchronization"></a>Ekspresowe ustawienia/synchronizacja skrótu hasła

| Stan | Wpływ na środowisko logowania platformy Azure użytkownika |
|:---:|:--- |
| Nie dodano |W takim przypadku nie domena niestandardowa dla contoso.com została dodana w katalogu usługi Azure AD. Użytkownicy, którzy mają lokalną firmę UPN z sufiksem, @contoso.com nie będą mogli używać lokalnej sieci UPN do logowania się na platformie Azure. Zamiast tego będą musieli użyć nowej łącze UPN, która jest im dostarczana przez usługę Azure AD, dodając sufiks dla domyślnego katalogu usługi Azure AD. Na przykład jeśli synchronizujesz użytkowników z katalogiem usługi Azure AD azurecontoso.onmicrosoft.com, użytkownik user@contoso.com lokalny otrzyma pozycję UPN . user@azurecontoso.onmicrosoft.com |
| Nie zweryfikowane |W takim przypadku mamy domenę niestandardową contoso.com dodawany w katalogu usługi Azure AD. Jednak nie jest jeszcze zweryfikowana. Jeśli przejdziesz dalej z synchronizacją użytkowników bez weryfikacji domeny, użytkownicy zostaną przydzieleni nową nazwę UPN przez usługę Azure AD, podobnie jak w scenariuszu "Nie dodane". |
| Sprawdzonych |W takim przypadku mamy domenę niestandardową contoso.com, która została już dodana i zweryfikowana w usłudze Azure AD dla sufiksu nazwy UPN. Użytkownicy będą mogli używać lokalnej nazwy głównej użytkownika, user@contoso.comna przykład , aby zalogować się na platformie Azure po ich synchronizacji z usługą Azure AD. |

###### <a name="ad-fs-federation"></a>Federacja usług AD FS
Nie można utworzyć federacji z domyślną domeną onmicrosoft.com w usłudze Azure AD ani niezweryfikowaną domeną niestandardową w usłudze Azure AD. Po uruchomieniu kreatora usługi Azure AD Connect, jeśli wybierzesz niezweryfikowaną domenę do utworzenia federacji, usługa Azure AD Connect wyświetli monit o utworzenie niezbędnych rekordów, które mają zostać utworzone w miejscu, w którym usługa DNS jest hostowana dla domeny. Aby uzyskać więcej informacji, zobacz [Sprawdzanie domeny usługi Azure AD wybranej do federacji](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Jeśli wybrano opcję logowania użytkownika **Federacja z usługami AD FS,** musisz mieć domenę niestandardową, aby kontynuować tworzenie federacji w usłudze Azure AD. W naszej dyskusji oznacza to, że powinniśmy mieć domenę niestandardową contoso.com dodaną w katalogu usługi Azure AD.

| Stan | Wpływ na środowisko logowania użytkownika platformy Azure |
|:---:|:--- |
| Nie dodano |W takim przypadku usługa Azure AD Connect nie znalazła pasującej domeny niestandardowej dla sufiksu nazwy UPN contoso.com w katalogu usługi Azure AD. Musisz dodać domenę niestandardową contoso.com, jeśli potrzebujesz, aby użytkownicy logowali się przy użyciu usług user@contoso.comAD FS przy użyciu lokalnej nazwy UPN (np. |
| Nie zweryfikowane |W takim przypadku usługa Azure AD Connect monituje o odpowiednie szczegóły dotyczące sposobu weryfikacji domeny na późniejszym etapie. |
| Sprawdzonych |W takim przypadku można przejść do konfiguracji bez żadnych dalszych działań. |

## <a name="changing-the-user-sign-in-method"></a>Zmiana metody logowania użytkownika
Metodę logowania użytkownika można zmienić z federacji, synchronizacji skrótów haseł lub uwierzytelniania przekazywanego przy użyciu zadań dostępnych w usłudze Azure AD Connect po wstępnej konfiguracji usługi Azure AD Connect za pomocą kreatora. Uruchom ponownie kreatora usługi Azure AD Connect, a zobaczysz listę zadań, które można wykonać. Z listy zadań wybierz **pozycję Zmień logowanie użytkownika.**

![Zmienianie logowania użytkownika](./media/plan-connect-user-signin/changeusersignin.png)

Na następnej stronie zostanie wyświetlona prośba o podanie poświadczeń usługi Azure AD.

![Łączenie z usługą Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Na stronie **Logowania użytkownika** wybierz żądane logowanie użytkownika.

![Łączenie z usługą Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Jeśli dokonujesz tylko tymczasowego przełączania do synchronizacji skrótów haseł, zaznacz pole wyboru **Nie konwertuj kont użytkowników.** Nieewidencjonowanie opcji spowoduje konwersję każdego użytkownika na federacyjne i może potrwać kilka godzin.
>
>

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [integrując tożsamości lokalne z usługą Azure Active Directory.](whatis-hybrid-identity.md)
- Dowiedz się więcej o [pojęciach dotyczących projektowania usługi Azure AD Connect](plan-connect-design-concepts.md).
