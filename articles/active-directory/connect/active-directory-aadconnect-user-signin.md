---
title: 'Usługi Azure AD Connect: Logowanie użytkownika | Dokumentacja firmy Microsoft'
description: Azure AD Connect logowania użytkownika dla ustawień niestandardowych.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 6a6e83ad73f561cd8aa4fc629fb9b48449af6d0a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect użytkownika opcje logowania
Azure Active Directory (Azure AD) Connect umożliwia użytkownikom logowania się do zasobów w chmurze, jak i dla lokalnego przy użyciu takich samych haseł. W tym artykule opisano podstawowe pojęcia dla każdego modelu tożsamości pomaga wybrać tożsamości, która ma być używany dla logowania do usługi Azure AD.

Jeśli znasz już modelu tożsamości usługi Azure AD i chcesz dowiedzieć się więcej na temat określonej metody, zobacz odpowiednie łącze:

* [Synchronizacja skrótów haseł](#password-hash-synchronization) z [bezproblemowe logowanie jednokrotne (SSO)](active-directory-aadconnect-sso.md)
* [Uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md) z [bezproblemowe logowanie jednokrotne (SSO)](active-directory-aadconnect-sso.md)
* [Federacyjną rejestracją Jednokrotną (z usługi Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)

> [!NOTE] 
> Należy pamiętać, że skonfigurowanie federacyjnego dla usługi Azure AD, zostanie nawiązana relacja zaufania między dzierżawy usługi Azure AD i domen federacyjnych. Z tej domeny federacyjnej relacji zaufania, użytkownicy będą mieć dostęp do zasobów w chmurze Azure AD w ramach dzierżawy.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Wybieranie metody logowania użytkownika w Twojej organizacji
W przypadku większości organizacji, które po prostu chcesz włączyć logowanie użytkowników do usługi Office 365, aplikacji SaaS i innych zasobów platformy Azure na podstawie usługi AD zalecamy domyślną opcją synchronizacji skrótu hasła. Jednak niektóre organizacje mają określonej przyczyny nie są możliwe do użycia tej opcji. Wybiera on albo federacyjnych opcji logowania, takie jak usługi AD FS lub uwierzytelnianie przekazywane. Poniższa tabela umożliwia podejmowanie właściwie.

Muszę | PHS, który z logowania jednokrotnego| PTA z logowania jednokrotnego| AD FS |
 --- | --- | --- | --- |
Synchronizuj automatycznie nowe, skontaktuj się z pomocą, kont użytkowników i grup w lokalnej usłudze Active Directory do chmury.|x|x|x|
Skonfiguruj moje dzierżawy w scenariuszach hybrydowych usługi Office 365.|x|x|x|
Włącz użytkowników do logowania się oraz dostęp do usługi w chmurze przy użyciu swoich haseł lokalnych.|x|x|x|
Implementowanie logowania jednokrotnego przy użyciu poświadczeń firmowych.|x|x|x|
Upewnij się, że hasła nie są przechowywane w chmurze.||x*|x|
Włącz lokalnymi rozwiązaniami usługi Multi-Factor authentication.|||x|

* Za pomocą uproszczonego agenta.

### <a name="password-hash-synchronization"></a>Synchronizacja skrótów haseł
Synchronizacja skrótów haseł skrótów haseł użytkowników są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD. Hasła są zmieniane lub zresetować lokalnymi, nowych skrótów haseł są synchronizowane z usługą Azure AD natychmiast, aby użytkownicy zawsze można używać tego samego hasła dla zasobów w chmurze i zasobów lokalnych. Hasła nigdy nie są wysyłane do usługi Azure AD lub przechowywane w usłudze Azure AD w postaci zwykłego tekstu. Aby umożliwić własnym resetowania haseł w usłudze Azure AD służy synchronizacji skrótu hasła oraz zapisywania zwrotnego haseł.

Ponadto można włączyć [bezproblemowe logowanie Jednokrotne](active-directory-aadconnect-sso.md) dla użytkowników na komputerach przyłączonych do domeny, które znajdują się w sieci firmowej. Z logowania jednokrotnego włączonych użytkowników tylko trzeba wprowadzić nazwę użytkownika, aby ułatwić im bezpiecznego dostępu do zasobów w chmurze.

![Synchronizacja skrótów haseł](./media/active-directory-aadconnect-user-signin/passwordhash.png)

Aby uzyskać więcej informacji, zobacz [synchronizacji skrótu hasła](active-directory-aadconnectsync-implement-password-hash-synchronization.md) artykułu.

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane
Przy użyciu przekazywanego uwierzytelniania hasła została sprawdzona na kontrolerze lokalnej usługi Active Directory. Hasło nie musi znajdować się w usłudze Azure AD w jakimkolwiek formularzu. Dzięki temu zasady lokalne, takie jak ograniczeń logowania godzina ma zostać obliczone podczas uwierzytelniania w chmurze usługi.

Przekazywanego uwierzytelniania przy użyciu prostego agenta na komputerze przyłączonym do domeny systemu Windows Server 2012 R2 w środowisku lokalnym. Ten agent nasłuchuje żądań sprawdzania poprawności hasła. Nie wymaga żadnych portów przychodzących do Internetu.

Ponadto można również włączyć logowanie jednokrotne dla użytkowników na komputerach przyłączonych do domeny, które znajdują się w sieci firmowej. Z logowania jednokrotnego włączonych użytkowników tylko trzeba wprowadzić nazwę użytkownika, aby ułatwić im bezpiecznego dostępu do zasobów w chmurze.
![Przekazywanego uwierzytelniania](./media/active-directory-aadconnect-user-signin/pta.png)

Aby uzyskać więcej informacji, zobacz:
- [Przekazywanego uwierzytelniania](active-directory-aadconnect-pass-through-authentication.md)
- [Logowanie jednokrotne](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federacyjnego, który używa nowej lub istniejącej farmy usług AD FS w systemie Windows Server 2012 R2
Z federacyjnego logowania użytkownicy mogą Zaloguj się do usługi Azure AD na podstawie ich hasłami lokalnymi. Gdy są one w sieci firmowej, ich nawet nie muszą wprowadzać haseł. Przy użyciu opcji federacyjnego z usługami AD FS, można wdrożyć nowej lub istniejącej farmy z usługami AD FS w systemie Windows Server 2012 R2. Jeśli chcesz określić istniejącej farmy, Azure AD Connect konfiguruje relacji zaufania między farmie, a usługą Azure AD, dzięki czemu użytkownicy będą mogli logować.

<center>![Federacja z usług AD FS w systemie Windows Server 2012 R2](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Wdrażanie federacyjnych z usługami AD FS w systemie Windows Server 2012 R2

W przypadku instalowania nowej farmy, potrzebne są:

* Serwer Windows Server 2012 R2 dla serwera federacyjnego.
* Serwer Windows Server 2012 R2 dla serwera Proxy aplikacji sieci Web.
* Plik PFX o jeden certyfikat protokołu SSL dla nazwy usługi federacyjnej zamierzone. Na przykład: fs.contoso.com.

Jeśli wdrażasz nową farmę lub przy użyciu istniejącej farmy, należy:

* Poświadczenia administratora lokalnego na serwerach federacyjnych.
* Poświadczenia administratora lokalnego na żadnym serwerze grupy roboczej (nie przyłączonych do domeny) czy zamierzasz wdrożyć rolę serwera Proxy aplikacji sieci Web na.
* Maszynie uruchom kreatora na, aby można było nawiązać innych maszyn, które chcesz zainstalować usługi AD FS lub serwer Proxy aplikacji sieci Web na za pomocą zdalnego zarządzania systemem Windows.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego za pomocą usług AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Zaloguj się przy użyciu wcześniejszej wersji usług AD FS lub rozwiązanie innej firmy
Jeśli logowania w chmurze została już skonfigurowana przy użyciu wcześniejszej wersji usług AD FS (np. usługi AD FS 2.0) lub dostawcy federacyjnego innych firm, można pominąć logowanie Konfiguracja użytkownika za pośrednictwem usługi Azure AD Connect. Umożliwi to uzyskać ostatniej synchronizacji i innych funkcji programu Azure AD Connect podczas nadal przy użyciu istniejącego rozwiązania do logowania.

Aby uzyskać więcej informacji, zobacz [listę zgodności federacyjnych innych firm w usłudze Azure AD](active-directory-aadconnect-federation-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Logowanie użytkownika i głównej nazwy użytkownika
### <a name="understanding-user-principal-name"></a>Opis główna nazwa użytkownika
W usłudze Active Directory domyślnym sufiksem głównej nazwy (UPN) użytkownika jest nazwą DNS domeny, w której utworzono konto użytkownika. W większości przypadków jest to nazwa domeny, który jest zarejestrowany jako domena przedsiębiorstwa w Internecie. Jednak można dodać więcej sufiksów UPN przy użyciu domeny usługi Active Directory i relacje zaufania.

Nazwa UPN użytkownika ma format username@domain. Na przykład dla domeny usługi Active Directory o nazwie "contoso.com" użytkownika o nazwie Jan może mieć nazwy UPN "john@contoso.com". Nazwa UPN użytkownika jest oparta na RFC 822. Mimo że głównej nazwy użytkownika i adres e-mail współużytkować ten sam format, wartość nazwy UPN użytkownika może lub nie może być taki sam jak adres e-mail użytkownika.

### <a name="user-principal-name-in-azure-ad"></a>Główna nazwa użytkownika w usłudze Azure AD
Kreator Azure AD Connect używa atrybutu userPrincipalName lub umożliwia określenie atrybutu (w instalacji niestandardowej) ma być używany z lokalnymi jako główną nazwę użytkownika w usłudze Azure AD. Jest to wartość, która jest używana dla logowania do usługi Azure AD. Jeśli wartość atrybutu userPrincipalName nie odpowiada zweryfikowanej domeny w usłudze Azure AD, następnie usługi Azure AD zastępuje go z wartości domyślnej. wartość onmicrosoft.com.

Każdym katalogu w usłudze Azure Active Directory jest dostarczany z nazwą domeny wbudowanej z contoso.onmicrosoft.com format, umożliwiające możesz rozpocząć korzystanie z platformy Azure lub innych usług firmy Microsoft. Można ulepszyć i uprościć obsługę logowania za pomocą domen niestandardowych. Informacje o niestandardowych nazw domen w usłudze Azure AD oraz sposób weryfikowania domeny, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../add-custom-domain.md#add-the-custom-domain-name-to-your-directory).

## <a name="azure-ad-sign-in-configuration"></a>Konfiguracja logowania się w usłudze Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfiguracji platformy Azure AD logowania z programem Azure AD Connect
Środowiska logowania usługi Azure AD zależy od tego, czy usługi Azure AD można dopasować użytkownika sufiks głównej nazwy użytkownika, który jest synchronizowanego do jednej z domen niestandardowych, które są sprawdzane w katalogu usługi Azure AD. Azure AD Connect zawiera informacje pomocne podczas konfigurowania ustawień usługi Azure AD logowania, dzięki czemu użytkownik logowania w usługach w chmurze jest podobny do środowiska lokalnego.

Azure AD Connect zawiera listę sufiksów głównej nazwy użytkownika, które są zdefiniowane dla domen i próbuje dopasować je z domeny niestandardowej w usłudze Azure AD. Następnie pomaga z odpowiednią akcję, które należy podjąć.
Strona logowania usługi Azure AD listy sufiksów głównej nazwy użytkownika, które są definiowane dla lokalnej usługi Active Directory i wyświetla stan odpowiedniego dla każdego sufiksu. Wartości stanu może być jedną z następujących czynności:

| Stan | Opis | Wymagana akcja |
|:--- |:--- |:--- |
| Zweryfikowano |Azure AD Connect odnaleziono odpowiadającego mu zweryfikowaną domeną w usłudze Azure AD. Wszyscy użytkownicy w tej domenie zalogować się przy użyciu poświadczeń lokalnych. |Nie jest potrzebne nie działanie. |
| Nie zweryfikowano |Azure AD Connect można odnaleźć pasującego domeny niestandardowej w usłudze Azure AD, ale nie jest on weryfikowany. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na domyślne. po synchronizacji, jeśli domena nie jest zweryfikowana zostanie dodany sufiks onmicrosoft.com. | [Sprawdź, czy domeny niestandardowej w usłudze Azure AD.](../add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| Nie dodano |Azure AD Connect nie znaleziono domeny niestandardowej, która odpowiadał sufiks głównej nazwy użytkownika. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na domyślny. Jeśli domena nie jest dodany i zweryfikowane na platformie Azure zostanie dodany sufiks onmicrosoft.com. | [Dodawania i weryfikowania domeny niestandardowej, która odpowiada sufiks głównej nazwy użytkownika.](../add-custom-domain.md) |

Strona logowania usługi Azure AD listy sufiksów głównej nazwy użytkownika, które są definiowane dla lokalnej usługi Active Directory i odpowiednie domeny niestandardowej w usłudze Azure AD z bieżącym stanem weryfikacji. Do niestandardowej instalacji można teraz wybrać atrybut nazwy głównej użytkownika na **logowania w usłudze Azure AD** strony.

![Strona logowania usługi AD platformy Azure](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Można kliknąć przycisk Odśwież, aby ponownie pobrać najnowszy stan niestandardowych domen z usługi Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Wybranie atrybutu nazwy głównej użytkownika w usłudze Azure AD
Atrybut userPrincipalName jest atrybutem, którego użytkownicy po zalogowaniu się do usługi Azure AD i Office 365. Należy sprawdzić domeny (znanej także jako sufiksy nazw UPN), które są używane w usłudze Azure AD przed zsynchronizowaniem użytkowników.

Firma Microsoft zaleca zachowanie domyślnego atrybutu userPrincipalName. Jeśli ten atrybut jest nonroutable i nie można zweryfikować, możliwe jest wybranie innego atrybutu (na przykład e-mail) jako atrybut zawierający identyfikator logowania. Jest to określane jako alternatywny identyfikator. Wartość atrybutu alternatywnego Identyfikatora musi zgodne ze standardem RFC 822. Używając alternatywnego Identyfikatora z federacyjnego logowania jednokrotnego, jako rozwiązanie logowania i hasło logowania jednokrotnego.

> [!NOTE]
> Korzystanie z alternatywnego Identyfikatora nie jest zgodna z wszystkich obciążeń usługi Office 365. Aby uzyskać więcej informacji, zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Stany różnych domen niestandardowych i ich wpływ na Azure środowiska logowania
Jest bardzo ważne zrozumieć relacji między Stanami domeny niestandardowej w katalogu usługi Azure AD i sufiksy nazw UPN, które są zdefiniowane lokalnie. Przejdź przez różne możliwe Azure logowania procesy podczas podczas konfigurowania synchronizacji za pomocą usługi Azure AD Connect.

Poniższe informacje, załóżmy, że firma Microsoft jest związane z contoso.com sufiks głównej nazwy użytkownika, który jest używany w katalogu lokalnego jako część nazwy UPN — na przykład user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Express synchronizacji skrótu ustawienia i hasła
| Stan | Wpływ na środowisko użytkownika Azure logowania |
|:---:|:--- |
| Nie dodano |W takim przypadku nie domeny niestandardowej dla domeny contoso.com został dodany w katalogu usługi Azure AD. Użytkownicy, którzy mają nazwy UPN lokalnymi wraz z sufiksem @contoso.com nie będzie można ich nazwy UPN lokalnymi umożliwia logowanie do platformy Azure. Zamiast tego będzie musiała użycia nowej nazwy UPN, który został dostarczony do ich przez usługę Azure AD przez dodanie sufiksu domyślny katalog usługi Azure AD. Na przykład, jeśli jest synchronizowanie użytkownikom azurecontoso.onmicrosoft.com katalogu usługi Azure AD, a następnie użytkownika lokalnego user@contoso.com podaje nazwę UPN user@azurecontoso.onmicrosoft.com. |
| Nie zweryfikowano |W takim przypadku mamy niestandardowej domeny contoso.com, który został dodany w katalogu usługi Azure AD. Jednak jeszcze nie sprawdza. Jeśli hypervreplicavolumesize z synchronizowanie użytkowników bez weryfikowania domeny, a następnie użytkownicy zostaną przypisane nowe nazwy UPN przez usługę Azure AD, podobnie jak w scenariuszu "Nie dodano". |
| Zweryfikowano |W takim przypadku mamy niestandardowej domeny contoso.com, który został już dodany i zweryfikowane w usłudze Azure AD dla sufiks głównej nazwy użytkownika. Użytkownicy będą mogli używać swoich lokalnych główna nazwa użytkownika, na przykład user@contoso.com, aby logowanie do platformy Azure po są one zsynchronizowane z usługą Azure AD. |

###### <a name="ad-fs-federation"></a>Usługi AD FS federation
Nie można utworzyć Federacji przy użyciu domyślnego. domeny onmicrosoft.com w usłudze Azure AD lub niezweryfikowanej domeny niestandardowej w usłudze Azure AD. Po uruchomieniu kreatora programu Azure AD Connect, jeśli zostanie wybrana niezweryfikowanej domeny do utworzenia federacji z następnie Azure AD Connect monit rekordów niezbędnych do utworzenia, gdzie jest hostowana serwery DNS dla domeny. Aby uzyskać więcej informacji, zobacz [zweryfikować domenę usługi Azure AD wybranej do Federacji](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

W przypadku wybrania opcji logowania użytkownika **Federacja z usługami AD FS**, wówczas musi mieć niestandardową domenę, aby kontynuować tworzenie federacji w usłudze Azure AD. Nasze omówienie oznacza to, firma Microsoft ma niestandardową domenę contoso.com dodane w katalogu usługi Azure AD.

| Stan | Wpływ na użytkowników platformy Azure środowiska logowania |
|:---:|:--- |
| Nie dodano |W takim przypadku Azure AD Connect nie znaleziono pasującego domeny niestandardowej dla domeny contoso.com sufiks nazwy UPN w katalogu usługi Azure AD. Konieczne jest dodanie contoso.com domeny niestandardowej, jeśli potrzebujesz użytkownikom na logowanie za pomocą usług AD FS z lokalnych nazw UPN (jak user@contoso.com). |
| Nie zweryfikowano |W takim przypadku Azure AD Connect monit odpowiednie szczegóły dotyczące sposobu weryfikacji domeny na późniejszym etapie. |
| Zweryfikowano |W takim przypadku można przejść dalej z konfiguracją bez dalszych działań. |

## <a name="changing-the-user-sign-in-method"></a>Zmiana metody logowania użytkownika
Możesz zmienić metody logowania użytkownika w Federacji, synchronizacji skrótu hasła lub uwierzytelnianie przy użyciu zadań, które są dostępne w programie Azure AD Connect po początkowej konfiguracji programu Azure AD Connect przy użyciu kreatora. Uruchom ponownie Kreatora programu Azure AD Connect i zobaczysz listę zadań, które można wykonywać. Wybierz **zmienić logowania użytkownika** z listy zadań.

![Zmień dane logowania użytkownika](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Na następnej stronie jest wyświetlana prośba o podanie poświadczeń dla usługi Azure AD.

![Łączenie z usługą Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Na **logowania użytkownika** wybierz żądany logowania użytkownika.

![Łączenie z usługą Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Jeśli tworzysz tylko tymczasowego przełącznika do synchronizacji skrótu hasła, następnie wybierz **kont użytkowników nie można konwertować** pole wyboru. Nie sprawdza opcję przekonwertuje każdego użytkownika federacyjnego, a może zająć kilka godzin.
>
>

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
- Dowiedz się więcej o [zagadnienia dotyczące projektowania usługi Azure AD Connect](active-directory-aadconnect-design-concepts.md).
