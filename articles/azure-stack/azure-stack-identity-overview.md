---
title: Przegląd tożsamości usługi Azure Stack | Dokumentacja firmy Microsoft
description: Więcej informacji na temat systemów tożsamości, których można użyć z usługą Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: patricka
ms.reviewer: ''
ms.openlocfilehash: 84ab374c95f3e77b2897ff142c3cacef3df87140
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718920"
---
# <a name="overview-of-identity-for-azure-stack"></a>Przegląd tożsamości usługi Azure Stack

Usługa Azure Stack wymaga usługi Azure Active Directory (Azure AD) lub Active Directory Federation Services (AD FS), obsługiwane przez Active Directory jako dostawcy tożsamości. Dostawcy to jednorazowa decyzja, wprowadzone przy pierwszym wdrożeniu usługi Azure Stack. Pojęcia i szczegóły autoryzacji, w tym artykule mogą pomóc wybrać dostawców tożsamości.

Wybór Azure AD lub AD FS może zależeć od trybu, w którym można wdrożyć usługę Azure Stack:

- Podczas wdrażania w trybie połączonym, można użyć albo usługi Azure AD lub AD FS.
- Podczas wdrażania w trybie odłączenia, bez połączenia z Internetem jest obsługiwana tylko usługi AD FS.

Aby uzyskać więcej informacji o opcjach, które zależą od środowiska usługi Azure Stack, zobacz następujące artykuły:

- Zestaw Azure Stack deployment kit: [zagadnienia dotyczące tożsamości](azure-stack-datacenter-integration.md#identity-considerations).
- Zintegrowane systemy usługi Azure Stack: [decyzje dotyczące usługi Azure Stack do planowania wdrożenia zintegrowane systemy](azure-stack-deployment-decisions.md).

## <a name="common-concepts-for-identity"></a>Typowe pojęcia dla tożsamości

W kolejnych sekcjach omówiono typowe pojęcia dotyczące dostawców tożsamości i ich użycia w usłudze Azure Stack.

![Terminologia dotycząca dostawców tożsamości](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Dzierżawy katalogu i organizacji

Katalog jest kontenerem, który przechowuje informacje o *użytkowników*, *aplikacje*, *grup*, i *jednostki usług*.

Dzierżawa katalogu *organizacji*, takich jak Microsoft lub własnej firmy.

- Usługa Azure AD obsługuje wiele dzierżaw i może obsługiwać wiele organizacji, każde we własnej katalogu. Jeśli korzystasz z usługi Azure AD i występuje wielu dzierżawców, można przyznać aplikacji i użytkowników z jednej dzierżawy dostępu do innych dzierżaw w tym samym katalogu.
- Usługi AD FS obsługuje pojedynczej dzierżawy, a więc jednej organizacji.

### <a name="users-and-groups"></a>Użytkownicy i grupy

Konta użytkowników (tożsamości) są standardowe konta, które przeprowadzają uwierzytelnianie użytkowników indywidualnych, przy użyciu Identyfikatora użytkownika i hasła. Grupy mogą obejmować użytkowników lub inne grupy.

Jak utworzyć i zarządzać użytkownikami i grupami, zależy od rozwiązania tożsamości, którego używasz.

W usłudze Azure Stack, kont użytkowników:

- Są tworzone w *username@domain* formatu. Mimo że usług AD FS kont użytkowników jest mapowany do wystąpienia usługi Active Directory, usług AD FS nie obsługuje użycia  *\<domeny >\<alias >* formatu.
- Można skonfigurować do korzystania z uwierzytelniania Multi-Factor Authentication.
- Są ograniczone do katalogu, gdzie są najpierw zarejestrować, jest katalogiem swojej organizacji.
- Mogą zostać zaimportowane z usługi w katalogach lokalnych. Aby uzyskać więcej informacji, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Po zalogowaniu się do portalu dzierżawcy w Twojej organizacji, możesz użyć *https://portal.local.azurestack.external* adresu URL. Podczas logowania się do portalu usługi Azure Stack z domen innych niż ta, używane do rejestrowania usługi Azure Stack, nazwa domeny używane do rejestrowania usługi Azure Stack musi być przypisany do portalu adresu url. Na przykład, jeśli usługi Azure Stack został zarejestrowany za pomocą fabrikam.onmicrosoft.com i konto użytkownika, logowanie jest admin@contoso.com, adres URL na potrzeby zaloguj się do portalu użytkownika: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Użytkownicy-goście

Użytkownicy-goście, są kontami użytkowników z innych dzierżaw katalogu, którym przyznano dostęp do zasobów w katalogu. Aby zapewnić obsługę użytkowników-gości, używać usługi Azure AD i włączyć obsługę wielu dzierżawców. Po włączeniu obsługi można zaprosić użytkowników-gości na dostęp do zasobów w Twojej dzierżawie katalogu, który z kolei umożliwia ich współpracę z zewnątrz organizacji.

Aby zaprosić użytkowników-gości, można użyć operatorów w chmurze i użytkowników [współpracy B2B usługi Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Zaproszeni użytkownicy uzyskają dostęp do dokumentów, zasobów i aplikacji z katalogu, a Ty masz kontrolę nad własnych zasobów i danych. 

Jako użytkownika-gościa należy zalogować się do dzierżawy katalogu innej organizacji. W tym celu należy dołączyć nazwę katalogu w organizacji do portalu adresu URL. Na przykład, jeśli należą do organizacji Contoso i chcesz zarejestrować się w katalogu firmy Fabrikam, możesz użyć https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Aplikacje

Można rejestrować aplikacje usługi Azure AD lub AD FS, a następnie oferty aplikacji dla użytkowników w Twojej organizacji.

Zastosowań:

- **Aplikacja sieci Web**: przykłady witryny Azure portal i usługi Azure Resource Manager. Obsługiwane są też wywołań interfejsu API sieci Web.
- **Natywny klient**: przykłady programu Azure PowerShell, Visual Studio oraz wiersza polecenia platformy Azure.

Aplikacje mogą obsługiwać dwa rodzaje dzierżawy:

- **Pojedynczej dzierżawy**: obsługuje użytkowników i usług, tylko z tym samym katalogu, w którym aplikacja jest zarejestrowany.

  > [!NOTE]
  > Ponieważ usług AD FS obsługuje tylko jeden katalog, aplikacje utworzone w topologii usług AD FS są, zgodnie z projektem, aplikacje z jedną dzierżawą.

- **Wielodostępne**: obsługuje korzystanie przez użytkowników i usług z katalogu, w którym aplikacja jest zarejestrowany i dodatkowe dzierżawy katalogów. Dzięki aplikacjom wielodostępnym użytkowników z innej dzierżawy katalogu (innej dzierżawy usługi Azure AD) mogą Zaloguj się do aplikacji. 

  Aby uzyskać więcej informacji na temat obsługi wielu dzierżawców, zobacz [Włączanie wielodostępu](azure-stack-enable-multitenancy.md).

  Aby uzyskać więcej informacji na temat tworzenia aplikacji z wieloma dzierżawami, zobacz [aplikacje z wieloma dzierżawami](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Podczas rejestrowania aplikacji, należy utworzyć dwa obiekty:

- **Obiekt aplikacji**: reprezentacja globalnej aplikacji we wszystkich dzierżawach. Ta relacja jest jeden do jednego z aplikacją oprogramowania i istnieje tylko w katalogu, w którym aplikacja najpierw jest zarejestrowany.

- **Obiektu jednostki usługi**: poświadczeniami, które jest tworzone dla aplikacji w katalogu, w którym ją najpierw jest zarejestrowany. Nazwy głównej usługi jest tworzona w katalogu każdej dodatkowe dzierżawy, którym aplikacja jest używana. Ta relacja może być jeden do wielu za pomocą aplikacji.

Aby dowiedzieć się więcej na temat aplikacji i obiektów nazw głównych usług, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Jednostki usługi

Jednostka usługi jest zestawem *poświadczenia* dla aplikacji lub usługi, która przyznać dostęp do zasobów w usłudze Azure Stack. Użyj nazwy głównej usługi oddziela uprawnienia dostępu do aplikacji z uprawnieniami użytkownika aplikacji.

Jednostka usługi jest tworzony w każdej dzierżawy, którym aplikacja jest używana. Nazwa główna usługi ustanawia tożsamość logowania i dostęp do zasobów (np. użytkowników), które są zabezpieczone przez tej dzierżawy.

- Aplikacja jednej dzierżawy ma tylko jedną jednostkę usługi, w tym katalogu, gdzie najpierw jest tworzony. Ta jednostka usługi jest tworzona i wyraża zgodę na używane podczas rejestracji aplikacji.
- Aplikacja sieci web z wieloma dzierżawami lub interfejsu API ma nazwę główną usługi, który jest tworzony w każdej dzierżawy, gdy użytkownik z tej dzierżawy wyraża zgodę na korzystanie z aplikacji.

Poświadczenia dla jednostki usługi może być kluczem, który jest generowany przy użyciu witryny Azure portal lub certyfikat. Używanie certyfikatu nadaje się do usługi automation, ponieważ przyjęto, że certyfikaty są bezpieczniejsze niż kluczy. 

> [!NOTE]
> Korzystając z usług AD FS z usługą Azure Stack, tylko administrator może utworzyć jednostki usługi. Za pomocą usług AD FS nazw głównych usług wymagają certyfikatów i są tworzone za pośrednictwem uprzywilejowanych punktu końcowego (program ten). Aby uzyskać więcej informacji, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](azure-stack-create-service-principals.md).

Aby dowiedzieć się więcej na temat nazw głównych usług dla usługi Azure Stack, zobacz [tworzenie jednostek usługi](azure-stack-create-service-principals.md).

### <a name="services"></a>Usługi

Usługi w usłudze Azure Stack, które współdziałają z dostawcy tożsamości są rejestrowane jako aplikacje za pomocą dostawcy tożsamości. Jak aplikacje rejestracja pozwala programowi service do uwierzytelniania za pomocą systemu tożsamości.

Użyj wszystkich usług platformy Azure [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokołów i [tokenów sieci Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) określić swojej tożsamości. Ponieważ usługi Azure AD i AD FS używają protokołów spójne, możesz użyć [usługi Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) do uwierzytelniania w środowisku lokalnym lub na platformie Azure (w przypadku połączonych). Za pomocą biblioteki ADAL umożliwia także narzędzi, takich jak Azure PowerShell i wiersza polecenia platformy Azure do zarządzania zasobami wielu chmur i rozwiązań lokalnych.

### <a name="identities-and-your-identity-system"></a>Tożsamości i systemu tożsamości

Tożsamości w usłudze Azure Stack zawierają konta użytkowników, grupy i jednostki usługi.

Podczas instalowania usługi Azure Stack kilka wbudowanych aplikacji i usług automatycznego rejestrowania za pomocą dostawcy tożsamości w dzierżawie katalogu. Niektóre usługi, które rejestrują służą do administrowania. Inne usługi są dostępne dla użytkowników. Rejestracje domyślne zapewniają podstawowych usług tożsamości, współpracujące ze sobą i przy użyciu tożsamości, które możesz dodać później.

Po skonfigurowaniu usługi Azure AD za pomocą wielu dzierżawców niektóre aplikacje są propagowane do nowych katalogów.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

### <a name="authentication-by-applications-and-users"></a>Uwierzytelnienia za pomocą aplikacji i użytkowników

![Tożsamości między warstwami usługi Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

W przypadku aplikacji i użytkowników architektury usługi Azure Stack jest opisane w czterech warstwach. Interakcje między każdym z tych warstw można użyć różnych typów uwierzytelniania.

|Warstwa    |Uwierzytelnianie między warstwami  |
|---------|---------|
|Narzędzia i klientów, takich jak portal administratora     | Dostępu lub modyfikacji zasobów w usłudze Azure Stack, narzędzi, a klienci używają [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) można umieścić wywołanie do usługi Azure Resource Manager. <br>Usługa Azure Resource Manager sprawdza poprawność tokenu Web JSON i dokonuje wglądu do *oświadczeń* w wystawiony token, aby oszacować poziom uwierzytelniania tego użytkownika lub jednostka usługi ma w usłudze Azure Stack. |
|Usługa Azure Resource Manager i jego podstawowych usług     |Usługa Azure Resource Manager komunikuje się z dostawcami zasobów do przeniesienia komunikacji z użytkownikami. <br> Przesyła użyj *bezpośredniego imperatywne* wywołania lub *deklaratywne* wywołań za pośrednictwem [szablonów usługi Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates).|
|Dostawcy zasobów     |Wywołania, które są przekazywane do dostawcy zasobów są chronione przy użyciu uwierzytelniania opartego na certyfikatach. <br>Usługa Azure Resource Manager i dostawca zasobów następnie Pozostań w wersji komunikację za pośrednictwem interfejsu API. Za każde wywołanie otrzymanego z usługi Azure Resource Manager dostawcy zasobów weryfikuje wywołania z tym certyfikatem.|
|Infrastruktura i logiki biznesowej     |Dostawcy zasobów komunikować się z logiką biznesową i infrastruktury przy użyciu wybranych przez nich trybu uwierzytelniania. Domyślnych dostawców zasobów, które są dostarczane z usługą Azure Stack uwierzytelnianie Windows do zabezpieczenia tej komunikacji.|

![Informacje wymagane do uwierzytelniania](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Uwierzytelnienia do usługi Azure Resource Manager

Uwierzytelnianie za pomocą dostawcy tożsamości i odbierać tokenu sieci Web JSON, musi mieć następujące informacje:

1. **Adres URL dla systemu tożsamości (urzędu)**: adres URL, w którym dostawca tożsamości jest osiągalna. Na przykład *https://login.windows.net*.
2. **Identyfikator URI Identyfikatora aplikacji dla usługi Azure Resource Manager**: Unikatowy identyfikator dla usługi Azure Resource Manager, która jest zarejestrowana u dostawcy tożsamości. Również jest unikatowy dla każdej instalacji usługi Azure Stack.
3. **Poświadczenia**: poświadczenia służy do uwierzytelniania za pomocą dostawcy tożsamości.
4. **Adres URL dla usługi Azure Resource Manager**: adres URL wskazuje na lokalizację usługi Azure Resource Manager. Na przykład *https://management.azure.com* lub *https://management.local.azurestack.external*.

Gdy podmiot zabezpieczeń (klienta, aplikacja lub użytkownik) wykonuje żądanie uwierzytelnienia dostępu do zasobu, żądanie musi zawierać:

- Poświadczenia dla podmiotu zabezpieczeń.
- Identyfikator URI zasobu, który podmiot zabezpieczeń chce uzyskiwać dostęp do aplikacji.

Poświadczenia są weryfikowane przez dostawcę tożsamości. Dostawca tożsamości sprawdza poprawność, czy aplikacja identyfikator URI jest dla zarejestrowanej aplikacji i że podmiotu zabezpieczeń ma poprawne uprawnienia do uzyskania tokenu dla tego zasobu. Jeśli żądanie jest prawidłowe, zostanie ustanowione tokenu sieci Web JSON.

Następnie token należy przekazać w nagłówku żądania do usługi Azure Resource Manager. Usługa Azure Resource Manager wykonuje następujące czynności, w określonej kolejności:

- Sprawdza poprawność *wystawcy* oświadczenia (iss) upewnić się, że token pochodzi z dostawcy tożsamości poprawne.
- Sprawdza poprawność *odbiorców* oświadczenia (aud), aby upewnić się, czy token został wystawiony do usługi Azure Resource Manager.
- Sprawdza, czy JSON Web Token jest podpisany przy użyciu certyfikatu, który jest skonfigurowany za pomocą protokołu OpenID jest znany do usługi Azure Resource Manager.
- Przegląd *wydane w* (iat) i *wygaśnięcia* (exp) oświadczenia upewnić się, że token jest aktywna i mogą być akceptowane.

Po ukończeniu wszystkich operacji sprawdzania poprawności korzysta z usługi Azure Resource Manager *zgłoszą* (oid) i *grup* oświadczeń można utworzyć zasobów, które mogą uzyskiwać dostęp do podmiotu zabezpieczeń.

![Diagram przedstawiający protokół wymiany tokenu](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Po wdrożeniu uprawnienia administratora globalnego usługi Azure Active Directory nie jest wymagana. Jednak niektóre operacje mogą wymagać poświadczenia administratora globalnego. Na przykład skryptu Instalatora dostawcy zasobów lub nową funkcję wymagające uprawnienia przyznawane. Możesz tymczasowo ponownie przywrócić uprawnienia administratora globalnego dla konta lub użyj konta oddzielne administratora globalnego, który jest właścicielem *domyślne subskrypcję dostawcy*.

### <a name="use-role-based-access-control"></a>Korzystanie z kontroli dostępu opartej na rolach

Oparta na rolach kontrola dostępu (RBAC) w usłudze Azure Stack jest zgodna z implementacją w systemie Microsoft Azure. Za zarządzanie dostępem do zasobów przez przypisywanie użytkownikom, grupom i aplikacjom odpowiednich ról RBAC.
Aby uzyskać informacje o sposobie używania funkcją RBAC przy użyciu usługi Azure Stack, zobacz następujące artykuły:

- [Rozpoczynanie pracy z usługą opartej na rolach kontrola dostępu w witrynie Azure portal](/azure/role-based-access-control/overview).
- [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą kontroli dostępu opartej na rolach](/azure/role-based-access-control/role-assignments-portal).
- [Tworzenie ról niestandardowych dla kontroli dostępu](/azure/role-based-access-control/custom-roles).
- [Zarządzanie kontrolą dostępu opartej na rolach](azure-stack-manage-permissions.md) w usłudze Azure Stack.

### <a name="authenticate-with-azure-powershell"></a>Uwierzytelnianie za pomocą programu Azure PowerShell

Szczegółowe informacje o użyciu programu Azure PowerShell do uwierzytelniania za pomocą usługi Azure Stack, można znaleźć w folderze [konfigurowania środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Uwierzytelnianie za pomocą wiersza polecenia platformy Azure

Aby dowiedzieć się, jak za pomocą programu Azure PowerShell, aby uwierzytelniać przy użyciu usługi Azure Stack, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure do użycia z usługą Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli).

## <a name="next-steps"></a>Kolejne kroki

- [Architektura tożsamości](azure-stack-identity-architecture.md)
- [Integracja z centrum danych — tożsamość](azure-stack-integrate-identity.md)
