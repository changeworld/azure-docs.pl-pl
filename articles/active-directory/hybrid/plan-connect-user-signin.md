---
title: 'Program Azure AD Connect: Logowanie użytkownika | Dokumentacja firmy Microsoft'
description: Usługa Azure AD Connect logowania użytkownika dla ustawień niestandardowych.
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
ms.openlocfilehash: cb44c64540cc461bca4e305f7783f7c6b612591b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296457"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Usługa Azure AD Connect użytkownika opcje logowania
Usługa Azure Active Directory (Azure AD) Connect umożliwia użytkownikom logować się do zasobów lokalnych i chmurze przy użyciu tego samego hasła. W tym artykule opisano kluczowe pojęcia związane z każdego modelu tożsamości ułatwić wybór tożsamości, którego chcesz użyć do logowania do usługi Azure AD.

Jeśli już znasz modelu tożsamości usługi Azure AD i chcesz dowiedzieć się więcej na temat określonej metody, zobacz odpowiednie łącze:

* [Synchronizacja skrótów haseł](#password-hash-synchronization) z [bezproblemowego logowania jednokrotnego (SSO)](how-to-connect-sso.md)
* [Uwierzytelnianie przekazywane](how-to-connect-pta.md) z [bezproblemowego logowania jednokrotnego (SSO)](how-to-connect-sso.md)
* [Federacyjne logowanie Jednokrotne (przy użyciu usługi Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Konfigurowanie federacji z serwerem PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Należy pamiętać, że, konfigurowanie Federacji dla usługi Azure AD, ustanowienia relacji zaufania między dzierżawą usługi Azure AD i domen federacyjnych. Z tej domeny federacyjnej relacji zaufania, użytkownicy mają dostęp do zasobów w chmurze usługi Azure AD w ramach dzierżawy.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Wybieranie metody logowania użytkownika w Twojej organizacji
Pierwszy decyzji wdrażania usługi Azure AD Connect jest wybór metody uwierzytelniania użytkowników będą używane do logowania. Należy się upewnić, że wybrano odpowiednie metody, która spełnia wymagania zaawansowane i zabezpieczeń w organizacji. Uwierzytelnianie jest krytyczny, ponieważ zostanie przeprowadzona Weryfikacja tożsamości użytkownika, dostęp do aplikacji i danych w chmurze. Aby wybrać metodę uwierzytelniania w prawo, należy wziąć pod uwagę czas, istniejącej infrastruktury, złożoności i kosztów wykonania wybranych. Czynniki te różnią się dla każdej organizacji i mogą ulec zmianie wraz z upływem czasu.

Usługa Azure AD obsługuje następujące metody uwierzytelniania: 

* **Uwierzytelnianie w chmurze** — w przypadku wybrania tej metody uwierzytelniania usługi Azure AD obsługuje proces uwierzytelniania użytkownika logowania. Za pomocą uwierzytelniania w chmurze można wybrać jeden z dwóch opcji: 
   * **Synchronizacja skrótów haseł (wersji)** — synchronizacja skrótów haseł umożliwia użytkownikom używanie tej samej nazwy użytkownika i hasło, którego używają lokalnie bez konieczności wdrażania jakiejkolwiek dodatkowej infrastruktury, oprócz usługi Azure AD Connect. 
   * **Uwierzytelnianie przekazywane (PTA)** — ta opcja jest podobny do synchronizacji skrótów haseł, ale udostępnia weryfikację prostych haseł, przy użyciu agentów oprogramowania w środowisku lokalnym dla organizacji mających silne zasady zabezpieczeń i zgodności.
* **Uwierzytelnianie federacyjne** — w przypadku wybrania tej metody uwierzytelniania usługi Azure AD będzie przekazują procesu uwierzytelniania, aby oddzielnym zaufanych systemem uwierzytelniania, takich jak usług AD FS lub systemem Federacji innej firmy do weryfikacji użytkownika logowania. 

W przypadku większości organizacji, które chcesz włączyć logowanie użytkownika do usługi Office 365, aplikacji SaaS i innych zasobów platformy Azure na podstawie usługi AD firma Microsoft zaleca domyślnej opcji synchronizacji skrótu hasła.
 
Aby uzyskać szczegółowe informacje na temat wybierania metody uwierzytelniania, zobacz [wybierz metodę uwierzytelniania odpowiednie dla Twojego rozwiązania tożsamości hybrydowej usługi Azure Active Directory](../../security/azure-ad-choose-authn.md)

### <a name="password-hash-synchronization"></a>Synchronizacja skrótów haseł
Z synchronizacji skrótów haseł skróty haseł użytkowników są synchronizowane z usługi Active Directory środowiska lokalnego do usługi Azure AD. Hasła zostaną zmienione lub zresetować w środowisku lokalnym, nowe skróty haseł są synchronizowane z usługą Azure AD natychmiast tak, aby użytkownicy zawsze można użyć tego samego hasła dla zasobów w chmurze i zasobów lokalnych. Hasła nigdy nie są wysyłane do usługi Azure AD lub przechowywane w usłudze Azure AD w postaci zwykłego tekstu. Aby włączyć samoobsługowe resetowanie haseł w usłudze Azure AD, można użyć synchronizacji skrótów haseł oraz zapisywania zwrotnego haseł.

Ponadto, aby umożliwić [bezproblemowe logowanie Jednokrotne](how-to-connect-sso.md) dla użytkowników na komputerach przyłączonych do domeny, znajdujących się w sieci firmowej. Za pomocą logowania jednokrotnego włączonych użytkowników wystarczy wprowadzić nazwę użytkownika, aby pomóc im bezpiecznego dostępu do zasobów w chmurze.

![Synchronizacja skrótów haseł](./media/plan-connect-user-signin/passwordhash.png)

Aby uzyskać więcej informacji, zobacz [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) artykułu.

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane
Przy użyciu przekazywanego uwierzytelniania użytkownika, hasło jest weryfikowane względem lokalnego kontrolera usługi Active Directory. Hasło nie musi znajdować się w usłudze Azure AD w dowolnej formie. Umożliwia to lokalne zasady, takim jak ograniczenia dotyczące godziny logowania, mogło zostać ocenione podczas uwierzytelniania w chmurze usługi.

Uwierzytelnianie przekazywane używa prostego agenta na komputerze przyłączonym do domeny systemu Windows Server 2012 R2 w środowisku lokalnym. Ten agent nasłuchuje żądań sprawdzania poprawności hasła. Nie wymaga żadnych portów przychodzących do Internetu.

Ponadto można również włączyć logowanie jednokrotne dla użytkowników na komputerach przyłączonych do domeny, znajdujących się w sieci firmowej. Za pomocą logowania jednokrotnego włączonych użytkowników wystarczy wprowadzić nazwę użytkownika, aby pomóc im bezpiecznego dostępu do zasobów w chmurze.
![Uwierzytelnianie przekazywane](./media/plan-connect-user-signin/pta.png)

Aby uzyskać więcej informacji, zobacz:
- [Uwierzytelnianie przekazywane](how-to-connect-pta.md)
- [Logowanie jednokrotne](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federacji, który używa nowej lub istniejącej farmy usług AD FS w systemie Windows Server 2012 R2
Za pomocą federacyjnego logowania użytkownicy zalogować się do usług platformy Azure na podstawie usługi AD za pomocą swoich haseł lokalnych. Gdy są one w sieci firmowej nie nawet muszą wprowadzić swoje hasła. Przy użyciu opcji federacji z usługami AD FS, można wdrożyć nowej lub istniejącej farmie za pomocą usług AD FS w systemie Windows Server 2012 R2. Jeśli chcesz określić istniejącą farmę, program Azure AD Connect konfiguruje relacji zaufania między farmy i usługi Azure AD, dzięki czemu można logowania użytkowników.

<center>

![Federacja z usługami AD FS w systemie Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Wdrażanie Federacja z usługami AD FS w systemie Windows Server 2012 R2

Jeśli wdrażasz nową farmę, potrzebne są:

* Serwer Windows Server 2012 R2 dla serwera federacyjnego.
* Serwer Windows Server 2012 R2 dla serwera Proxy aplikacji sieci Web.
* Plik PFX, z jednym certyfikatem SSL dla nazwy usługi federacyjnej zamierzone. Na przykład: fs.contoso.com.

Jeśli wdrażasz nowej farmy lub przy użyciu istniejącej farmy, należy:

* Poświadczenia administratora lokalnego na serwerach federacyjnych.
* Poświadczenia administratora lokalnego na żadnym serwerze grupy roboczej (nie przyłączonych do domeny) zamierzasz wdrożyć rolę serwera Proxy aplikacji sieci Web w systemie.
* Komputer, na którym uruchom kreatora na, aby można było połączyć się z innymi maszynami, chcesz zainstalować usług AD FS lub serwera Proxy aplikacji sieci Web na przy użyciu Windows Remote Management.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego z usługami AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federacja z serwerem PingFederate
Za pomocą federacyjnego logowania użytkownicy zalogować się do usług platformy Azure na podstawie usługi AD za pomocą swoich haseł lokalnych. Gdy są one w sieci firmowej nie nawet muszą wprowadzić swoje hasła.

Aby uzyskać więcej informacji na temat konfigurowania serwera PingFederate do użycia z usługą Azure Active Directory, zobacz [serwera PingFederate integracji z usługą Azure Active Directory i usługi Office 365](https://www.pingidentity.com/AzureADConnect)

Aby uzyskać informacje na temat konfigurowania usługi Azure AD Connect przy użyciu serwera PingFederate, zobacz [instalacji niestandardowej usługi Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Zaloguj się przy użyciu wcześniejszej wersji usług AD FS lub rozwiązanie innej firmy
Jeśli już skonfigurowano logowania w chmurze przy użyciu wcześniejszej wersji programu AD FS (np. usługi AD FS 2.0) lub dostawcy federacyjnego innej firmy, można pominąć logowanie Konfiguracja użytkownika za pomocą usługi Azure AD Connect. Umożliwi to użytkownikowi na uzyskanie ostatniej synchronizacji i innych funkcji programu Azure AD Connect, używając przy tym nadal istniejącego rozwiązania do logowania.

Aby uzyskać więcej informacji, zobacz [listę zgodności federacyjnych innych firm w usłudze Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Logowanie użytkownika i główna nazwa użytkownika
### <a name="understanding-user-principal-name"></a>Główna nazwa użytkownika — omówienie
W usłudze Active Directory domyślny sufiks głównej nazwy (UPN) użytkownika jest nazwą DNS domeny, w której utworzono konto użytkownika. W większości przypadków to nazwy domeny, która jest zarejestrowana jako domena przedsiębiorstwa w Internecie. Można jednak dodać więcej sufiksów nazw głównych, za pomocą domeny usługi Active Directory i zaufania.

Nazwa UPN użytkownika ma następujący format username@domain. Na przykład dla domeny usługi Active Directory o nazwie "contoso.com" użytkownika o nazwie Jan może mieć nazwy UPN "john@contoso.com". Nazwa UPN użytkownika jest oparty na RFC 822. Mimo że głównej nazwy użytkownika i adres e-mail należy udostępnić ten sam format, wartość nazwy UPN użytkownika może lub nie może być taki sam jak adres e-mail użytkownika.

### <a name="user-principal-name-in-azure-ad"></a>Główna nazwa użytkownika w usłudze Azure AD
Kreator Azure AD Connect używa atrybutu userPrincipalName lub umożliwia określenie atrybutu (w instalacji niestandardowej) do użycia ze środowiska lokalnego jako główną nazwę użytkownika w usłudze Azure AD. Jest to wartość, która jest używana do logowania się do usługi Azure AD. Jeśli wartość atrybutu userPrincipalName nie odpowiadają zweryfikowanej domeny w usłudze Azure AD, następnie usługi Azure AD zastępuje go znakiem domyślnego. wartość onmicrosoft.com.

Każdy katalog usługi Azure Active Directory jest dostarczany z nazwą domeny wbudowane, za pomocą contoso.onmicrosoft.com format, umożliwiające rozpoczęcie korzystania z platformy Azure lub innych usług firmy Microsoft. Można zwiększyć i uprościć środowisko logowania za pomocą domen niestandardowych. Aby uzyskać informacji na temat niestandardowych nazw domen w usłudze Azure AD i sposobie weryfikacji domeny, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Konfiguracja logowania się w usłudze Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfiguracja usłudze Azure AD logowania za pomocą usługi Azure AD Connect
Środowisko logowania usługi Azure AD zależy od tego, czy usługa Azure AD może odnosić się do sufiks głównej nazwy użytkownika, użytkownika, który jest synchronizowany z do jednej z domen niestandardowych, które są sprawdzane w katalogu usługi Azure AD. Program Azure AD Connect udostępnia pomoc podczas konfigurowania usługi Azure AD ustawień logowania, tak, aby środowisko logowania użytkowników w chmurze jest podobne do środowiska lokalnego.

Program Azure AD Connect wymieniono sufiksy nazw UPN, które są zdefiniowane dla domen i próbuje dopasować je przy użyciu domeny niestandardowej w usłudze Azure AD. Następnie pomaga przy użyciu odpowiedniej akcji, które należy podjąć.
Strony logowania usługi Azure AD wymieniono sufiksy nazw UPN, które są zdefiniowane dla lokalnej usługi Active Directory, a następnie wyświetli odpowiedniego stanu dla każdego sufiksu. Wartości stanu może być jednym z następujących czynności:

| Stan | Opis | Wymagana akcja |
|:--- |:--- |:--- |
| Zweryfikowano |Program Azure AD Connect znalezione dopasowania zweryfikowane domeny w usłudze Azure AD. Wszyscy użytkownicy w tej domenie może Zaloguj się przy użyciu ich poświadczeń lokalnych. |Żadna akcja nie jest wymagana. |
| Nie zweryfikowano |Program Azure AD Connect można odnaleźć pasującego domeny niestandardowej w usłudze Azure AD, ale nie jest on weryfikowany. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na wartość domyślna. dodany sufiks onmicrosoft.com po synchronizacji, jeśli domena nie jest zweryfikowana. | [Weryfikowanie domeny niestandardowej w usłudze Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nie dodano |Program Azure AD Connect nie znalazł domeny niestandardowej, która odpowiadał sufiks głównej nazwy użytkownika. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na wartość domyślna. Jeśli domena nie jest dodana i zweryfikowane w usłudze Azure dodany sufiks onmicrosoft.com. | [Dodanie i zweryfikowanie domeny niestandardowej, która odpowiada sufiks głównej nazwy użytkownika.](../fundamentals/add-custom-domain.md) |

Strony logowania usługi Azure AD wymieniono sufiksy nazw UPN, które są zdefiniowane dla usługi Active Directory w środowisku lokalnym i odpowiedniej domeny niestandardowej w usłudze Azure AD przy użyciu bieżącego stanu weryfikacji. W instalacji niestandardowej, teraz możesz wybrać atrybut dla nazwy głównej użytkownika na **logowania w usłudze Azure AD** strony.

![Strona logowania usługi AD systemu Azure](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Możesz kliknąć przycisk Odśwież, aby ponownie pobrać najnowszy stan domeny niestandardowe z usługi Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Wybranie atrybutu główną nazwę użytkownika w usłudze Azure AD
Atrybut userPrincipalName jest atrybutu, którego użytkownicy po zalogowaniu się do usługi Azure AD i Office 365. Należy sprawdzić domeny (znany także jako sufiksy nazw UPN), które są używane w usłudze Azure AD przed zsynchronizowaniem użytkowników.

Firma Microsoft zaleca zachowanie domyślnego atrybutu userPrincipalName. Jeśli ten atrybut jest nonroutable i nie można zweryfikować, możliwe jest wybranie innego atrybutu (adres e-mail, na przykład) jako atrybutu, który zawiera identyfikator logowania. Jest to określane jako identyfikatora alternatywnej. Wartość atrybutu alternatywnego Identyfikatora musi zgodne ze standardem RFC 822. Alternatywny identyfikator można użyć zarówno z hasłami logowania jednokrotnego i federacyjnego logowania jednokrotnego, jako rozwiązanie do logowania.

> [!NOTE]
> Korzystanie z alternatywnego Identyfikatora nie jest zgodna z wszystkich obciążeń usługi Office 365. Aby uzyskać więcej informacji, zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Stany inną domenę niestandardową i ich wpływ na środowisko logowania jest platformy Azure
Jest bardzo ważne, aby zrozumieć relację między Stanami domeny niestandardowej w katalogu usługi Azure AD i sufiksy nazw UPN, które są definiowane w środowisku lokalnym. Przejdźmy przez różne możliwości platformy Azure logowania doświadczeń podczas konfigurowania synchronizacji przy użyciu usługi Azure AD Connect.

W następujące informacje, załóżmy, że jesteśmy zainteresowani, z contoso.com sufiks nazwy UPN, która jest używana w katalogu lokalnym jako część nazwy UPN — na przykład user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Synchronizacja skrótów haseł/ustawienia Express

| Stan | Wpływ na wrażenia użytkownika logowania platformy Azure |
|:---:|:--- |
| Nie dodano |W tym przypadku nie domeny niestandardowej dla domeny contoso.com został dodany w katalogu usługi Azure AD. Użytkownicy, którzy mają nazwy UPN w środowisku lokalnym z sufiksem @contoso.com nie będzie można zalogować się do platformy Azure przy użyciu ich nazwy UPN w środowisku lokalnym. Zamiast tego należy ich używać nowej głównej nazwy użytkownika, który został dostarczony do ich przez usługę Azure AD, dodając sufiks domyślny katalog usługi Azure AD. Na przykład, jeśli synchronizowania użytkowników azurecontoso.onmicrosoft.com katalogu usługi Azure AD, a następnie użytkownika lokalnego user@contoso.com otrzyma nazwę UPN user@azurecontoso.onmicrosoft.com. |
| Nie zweryfikowano |W tym przypadku mamy niestandardowej domeny contoso.com, który zostanie dodany do katalogu usługi Azure AD. Jednak go jeszcze nie została zweryfikowana. Jeśli hypervreplicavolumesize synchronizowaniem użytkowników bez weryfikowania domeny, a następnie użytkownicy zostaną przypisane nową nazwę UPN przez usługę Azure AD, podobnie jak w scenariuszu "Nie dodano". |
| Zweryfikowano |W tym przypadku mamy niestandardowej domeny contoso.com, który został już dodany i zweryfikowane w usłudze Azure AD dla sufiks głównej nazwy użytkownika. Użytkownicy będą mogli używać nazwy głównej użytkownika w środowisku lokalnym, na przykład user@contoso.com, aby zalogować się do platformy Azure, po ich są synchronizowane z usługą Azure AD. |

###### <a name="ad-fs-federation"></a>Usługi AD FS Federacji
Nie można utworzyć Federacji przy użyciu domyślnego. domeny onmicrosoft.com w usłudze Azure AD lub niezweryfikowanej domeny niestandardowej w usłudze Azure AD. Po uruchomieniu kreatora Azure AD Connect, jeśli zostanie wybrana niezweryfikowanej domeny, do tworzenia federacji z następnie program Azure AD Connect monit rekordów niezbędnych do utworzenia hostujące usługi DNS dla domeny. Aby uzyskać więcej informacji, zobacz [zweryfikować domenę usługi Azure AD wybranej do Federacji](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

W przypadku wybrania opcji logowania użytkownika **Federacja z usługami AD FS**, a następnie musi mieć domenę niestandardową, aby kontynuować tworzenie federacji w usłudze Azure AD. Do dyskusji oznacza to, należy się contoso.com domeny niestandardowej, dodane w katalogu usługi Azure AD.

| Stan | Wpływ na użytkowników platformy Azure środowisko logowania |
|:---:|:--- |
| Nie dodano |W takim przypadku program Azure AD Connect nie znaleziono pasującego domeny niestandardowej dla domeny contoso.com sufiks nazwy UPN w katalogu usługi Azure AD. Należy dodać contoso.com domeny niestandardowej, jeśli potrzebujesz użytkownikom na logowanie za pomocą usług AD FS z ich lokalnej nazwy UPN (takich jak user@contoso.com). |
| Nie zweryfikowano |W takim przypadku program Azure AD Connect monituje o odpowiednie szczegóły dotyczące sposobu weryfikacji domeny na późniejszym etapie. |
| Zweryfikowano |W takim przypadku można hypervreplicavolumesize konfiguracji bez żadnych dalszych akcji. |

## <a name="changing-the-user-sign-in-method"></a>Zmiana metody logowania użytkownika
Możesz zmienić metodę logowania użytkownika z Federacji, synchronizacji skrótów haseł lub uwierzytelniania przekazywanego, przy użyciu zadań, które są dostępne w programie Azure AD Connect po początkowej konfiguracji programu Azure AD Connect za pomocą kreatora. Uruchom ponownie kreatora Azure AD Connect, a zobaczysz listę zadań, które mogą wykonywać. Wybierz **zmiana użytkownika logowania** z listy zadań.

![Zmień dane logowania użytkownika](./media/plan-connect-user-signin/changeusersignin.png)

Na następnej stronie pojawi się prośba o podanie poświadczeń dla usługi Azure AD.

![Łączenie z usługą Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Na **logowania użytkownika** wybierz logowania żądanego użytkownika.

![Łączenie z usługą Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Jeśli wprowadzasz tylko tymczasowe przełącznika do synchronizacji skrótów haseł, wybierz **nie Konwertuj kont użytkowników** pole wyboru. Nie sprawdza opcję przekonwertuje każdego użytkownika federacyjnego, a może potrwać kilka godzin.
>
>

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
- Dowiedz się więcej o [pojęć dotyczących projektowania usługi Azure AD Connect](plan-connect-design-concepts.md).
