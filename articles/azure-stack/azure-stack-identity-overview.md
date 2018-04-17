---
title: Omówienie tożsamości dla stosu Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat systemów tożsamości, używanych w usłudze Azure stosu.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 607c7938a789b3504a425057645b291bd4c8235b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Omówienie tożsamości Azure stosu

Stos Azure wymaga usługi Azure Active Directory (Azure AD) lub Active Directory Federation Services (AD FS), przechowywana w usłudze Active Directory jako dostawcy tożsamości. Wybór dostawcy jest jednorazowe decyzji podczas najpierw wdrożyć stosu Azure. Koncepcje i szczegóły autoryzacji, w tym artykule może ułatwić wybór między dostawców tożsamości. 

Wybór Azure AD lub AD FS może zależeć od tryb, w którym wdrożeniem Azure stosu: 
- Podczas wdrażania w trybie połączenia, można użyć albo usługi Azure AD lub AD FS. 
- Podczas wdrażania w trybie rozłączonym, bez połączenia z Internetem jest obsługiwana tylko usługi AD FS.

Aby uzyskać więcej informacji o opcjach, które zależą od środowiska Azure stosu, zobacz następujące artykuły:
- Zestaw wdrożenia usługi Azure stosu: [zagadnienia dotyczące tożsamości](azure-stack-datacenter-integration.md#identity-considerations).
- Stos Azure zintegrowanych systemów: [decyzje dotyczące stosu Azure planowania wdrożenia zintegrowane systemy](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Wspólne pojęcia dotyczące tożsamości
Kolejnych sekcjach omówiono w nim wspólne pojęcia dotyczące dostawców tożsamości i ich użycia w stosie Azure.

![Terminologia dostawców tożsamości](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Katalog dzierżawcy i organizacji
Katalog jest kontenerem, który przechowuje informacje o *użytkowników*, *aplikacji*, *grup*, i *usługi podmiotów*.
 
Jest dostępna dzierżawa katalogu *organizacji*, takich jak Microsoft lub firmy użytkownika. 
- Usługi Azure AD obsługuje wielu dzierżawców i może obsługiwać wiele organizacji, każdy w swoim własnym katalogu. Jeśli używasz usługi Azure AD i mieć wielu dzierżawców, można przyznać aplikacji i użytkowników z jednego dzierżawcy dostęp do innych dzierżawców ten sam katalog.
- Usługi AD FS obsługują pojedynczej dzierżawy i w związku z tym jednej organizacji. 

### <a name="users-and-groups"></a>Użytkownicy i grupy
Konta użytkowników (tożsamości) są standardowe konta uwierzytelniających osoby za pomocą Identyfikatora użytkownika i hasła. Grupy mogą zawierać użytkowników lub inne grupy. 

Jak tworzyć i zarządzać użytkownikami i grupami zależy od rozwiązania tożsamości, których używasz. 

W stosie Azure, kont użytkowników: 
- Są tworzone w *username@domain* format. Mimo że usługi AD FS mapuje kont użytkowników z wystąpieniem usługi Active Directory, usługi AD FS nie obsługuje korzystania z  *\<domeny >\<alias >* format. 
- Można skonfigurować do używania uwierzytelniania wieloskładnikowego. 
- Są ograniczone do katalogu, gdzie są najpierw zarejestrować, jest katalogiem używanych w organizacji.
- Mogą zostać zaimportowane z katalogów lokalnych. Aby uzyskać więcej informacji, zobacz [integrację katalogów lokalnych z usługą Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect). 

Po zalogowaniu się do portalu dzierżawcy w organizacji, użyj *https://portal.local.azurestack.external* adresu URL. 

### <a name="guest-users"></a>Użytkownicy-goście
Goście są konta użytkowników od pozostałych dzierżawców katalogu, którym przyznano dostęp do zasobów w katalogu. Aby obsługiwać gości, przy użyciu usługi Azure AD i włączyć obsługę wielu dzierżawców. Po włączeniu obsługi można zaprosić użytkowników gościa dostęp do zasobów w dzierżawie katalogu, który z kolei umożliwia ich współpracy z zewnątrz organizacji. 

Aby zaprosić gości, Operatorzy chmur i użytkownicy mogą użyć [współpracy B2B usługi Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Zaproszeni użytkownicy uzyskują dostęp do dokumentów, zasobów i aplikacji z katalogu i zachować kontrolę nad własnych zasobów i danych. 

Jako użytkownik-Gość można Zaloguj się do innej organizacji katalogu dzierżawcy. Aby to zrobić, można dołączyć nazwę katalogu Twojej organizacji do portalu adresu URL. Na przykład, jeśli należą do tej organizacji Contoso i chcesz logować się do katalogu firmy Fabrikam, możesz użyć https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Aplikacje
Rejestrowanie aplikacji do usługi Azure AD lub AD FS i następnie oferują aplikacji dla użytkowników w organizacji. 

Aplikacje obejmują:
- **Aplikacja sieci Web**: przykładami portalu Azure i usługi Azure Resource Manager. Obsługuje wywołania interfejsu API sieci Web. 
- **Aplikacja Native client**: przykładów programu Azure PowerShell, Visual Studio i wiersza polecenia platformy Azure.

Aplikacje mogą obsługiwać dwa rodzaje dzierżawy: 
- **Pojedynczej dzierżawy**: obsługuje użytkowników i usług, tylko z tym samym katalogu, w którym aplikacja jest zarejestrowany. 

  > [!NOTE]    
  > Ponieważ usługi AD FS obsługuje tylko jeden katalog, aplikacje utworzone w topologii usług AD FS są, zgodnie z projektem aplikacji pojedynczej dzierżawy.

- **Wielodostępne**: obsługuje korzystanie przez użytkowników i usług z katalogu, w którym aplikacja jest zarejestrowany i dodatkowe dzierżawy katalogów. Z aplikacje wielodostępne innej dzierżawy katalogu (innej dzierżawy usługi Azure AD) mogą logowania użytkownika do aplikacji. 

  Aby uzyskać więcej informacji na temat obsługi wielu dzierżawców, zobacz [włączenia obsługi wielu dzierżawców](azure-stack-enable-multitenancy.md). 

  Aby uzyskać więcej informacji o tworzeniu aplikacji wielodostępnych, zobacz [aplikacje wielodostępne](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Podczas rejestrowania aplikacji jest utworzenie dwóch obiektów:

- **Obiekt aplikacji**: globalne reprezentację aplikacji we wszystkich dzierżawców. Ta relacja jest jeden do jednego z aplikacją oprogramowania i tylko w katalogu najpierw rejestracji aplikacji nie istnieje.

- **Obiekt główny usługi**: poświadczeniami, które jest tworzone dla aplikacji w katalogu najpierw rejestracji aplikacji. Nazwy głównej usługi również jest tworzony w katalogu każdej dodatkowe dzierżawy, w którym aplikacja jest używana. Ta relacja może być jeden do wielu z aplikacją oprogramowania. 

Aby dowiedzieć się więcej na temat aplikacji i obiekty główne usługi, zobacz [aplikacji i usług obiektów principal w usłudze Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Nazwy główne usług 
Nazwy głównej usługi jest zestawem *poświadczeń* dla aplikacji lub usługi, która będzie udzielać dostępu do zasobów w stosie Azure. Użyj nazwy głównej usługi oddziela uprawnienia dostępu do aplikacji z uprawnieniami użytkownika aplikacji.

Nazwy głównej usługi jest tworzony w każdej dzierżawy, gdy aplikacja jest używana. Nazwy głównej usługi ustanawia tożsamości dla logowania i dostępu do zasobów (takich jak użytkownicy), które są zabezpieczone przez tę dzierżawę. 

- Stosowanie pojedynczej dzierżawy ma tylko jedną nazwę główną usługi, w katalogu, w którym najpierw został utworzony. Tej nazwy głównej usługi zostanie utworzona i zgadza może być używany podczas rejestracji aplikacji. 
- Aplikacji sieci web z wieloma dzierżawcami lub interfejsu API ma nazwy głównej usługi jest tworzony w każdej dzierżawy, gdy użytkownik z tej dzierżawy wyraża zgodę na korzystanie z aplikacji. 

Poświadczenia dla nazwy główne usług może być kluczem, który jest generowany przy użyciu portalu Azure lub certyfikatu. Użyj certyfikatu jest przeznaczone do automatyzacji, ponieważ przyjęto, że certyfikaty są bezpieczniejsze niż kluczy. 


> [!NOTE]    
> Gdy używasz usług AD FS z stosu Azure tylko administrator może utworzyć jednostki usługi. Z usługami AD FS nazw głównych usług wymagają certyfikatów i są tworzone za pomocą uprzywilejowanych punktu końcowego (program ten). Aby uzyskać więcej informacji, zobacz [dostęp aplikacji do stosu Azure](azure-stack-create-service-principals.md).

Aby poznać nazwy główne usług Azure stosu, zobacz temat [tworzenia nazwy główne usług](azure-stack-create-service-principals.md).


### <a name="services"></a>Usługi
Usługi w stosie Azure współpracujące z dostawcy tożsamości jest zarejestrowana jako aplikacji przy użyciu dostawcy tożsamości. Jak aplikacje rejestracji umożliwia uwierzytelnianie i tożsamość usługi. 

Użyj wszystkich usług Azure [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokołów i [tokenów sieci Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) ustanowienie tożsamości. Ponieważ usługi Azure AD i usług AD FS używają protokołów spójnie, możesz użyć [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) do uwierzytelniania lokalnego lub na platformie Azure (w przypadku połączonych). Przy użyciu biblioteki ADAL umożliwia także narzędzi, takich jak Azure PowerShell i interfejsu wiersza polecenia Azure dla zarządzania zasobami między chmurze i lokalnie. 


### <a name="identities-and-your-identity-system"></a>Tożsamości i systemu tożsamości 
Tożsamości dla stosu Azure zawierają konta użytkowników, grup i nazwy główne usług. 

Po zainstalowaniu stosu Azure kilka wbudowanych aplikacji i usług automatycznego rejestrowania w usłudze dostawcy tożsamości w dzierżawie katalogu. Niektóre usługi, które rejestrują służą do administrowania. Inne usługi są dostępne dla użytkowników. Rejestracje domyślne, nadaj podstawowej tożsamości usługi, które mogą współdziałać zarówno ze sobą oraz z tożsamościami, które później zostaną dodane.

Jeśli konfigurujesz usługę Azure AD z wielu dzierżawców niektóre aplikacje są propagowane do nowych katalogów. 

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
 

### <a name="authentication-by-applications-and-users"></a>Uwierzytelnianie w aplikacji i użytkowników
  
![Tożsamości między warstwami Azure stosu](media/azure-stack-identity-overview/identity-layers.png)

Dla aplikacji i użytkowników architektura stosu Azure opisano cztery warstwy. Interakcje między każdym z tych warstw można użyć różnych typów uwierzytelniania.


|Warstwy    |Uwierzytelnianie między warstwami  |
|---------|---------|
|Narzędzia i klientów, takich jak portal administratora     | Dostępu lub modyfikacji zasobu w stosie Azure, narzędzia i klienci używają [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) Aby połączyć się z Menedżerem zasobów Azure. <br>Usługa Azure Resource Manager sprawdza poprawność tokenu Web JSON i dokonuje wglądu *oświadczeń* w wystawionego tokenu, aby oszacować poziom autoryzacji tego użytkownika lub nazwy głównej usługi ma w stosie Azure. |
|Usługa Azure Resource Manager i jego core services     |Usługa Azure Resource Manager komunikuje się z dostawcami zasobów na przesyłanie komunikacji z użytkownikami. <br> Transfery użyj *bezpośredniego imperatywne* wywołania lub *deklaratywne* wywołuje za pośrednictwem [szablonów usługi Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Dostawcy zasobów     |Wywołania, które są przekazywane do dostawcy zasobów są zabezpieczone przy użyciu uwierzytelniania opartego na certyfikatach. <br>Usługa Azure Resource Manager i dostawca zasobów pozostają w komunikacji przy użyciu interfejsu API. Dla każdego wywołania odebrane z usługi Azure Resource Manager dostawcy zasobów weryfikuje połączenia z tym certyfikatem.|
|Infrastruktura i logiki biznesowej     |Dostawcy zasobów komunikują się z logiki biznesowej i infrastruktury przy użyciu wybranych przez nich trybu uwierzytelniania. Dostarczanych z stosu Azure dostawców zasobów domyślne uwierzytelniania systemu Windows do zabezpieczania komunikacji.|

![Informacje potrzebne do uwierzytelniania](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Uwierzytelniania do usługi Azure Resource Manager
Do uwierzytelniania za pomocą dostawcy tożsamości i odbierać żetonu Web JSON, musi mieć następujące informacje: 
1.  **Adres URL dla systemu tożsamości (urzędu)**: adres URL, w którym można osiągnąć dostawcy tożsamości. Na przykład *https://login.windows.net*. 
2.  **Identyfikator URI aplikacji usługi Azure Resource Manager**: Unikatowy identyfikator dla usługi Azure Resource Manager zarejestrowana u dostawcy tożsamości. Jest również unikatowa dla każdej instalacji stosu Azure.
3.  **Poświadczenia**: poświadczeń używana do uwierzytelniania z dostawcy tożsamości. 
4.  **Adres URL dla usługi Azure Resource Manager**: adres URL jest lokalizacja usługi Azure Resource Manager. Na przykład *https://management.azure.com* lub *https://management.local.azurestack.external*.

Gdy podmiot zabezpieczeń (klienta, aplikacji lub użytkownika) wysyła żądanie uwierzytelnienia do uzyskania dostępu do zasobu, żądanie musi zawierać:
- Poświadczenia podmiotu.
- Identyfikator URI do zasobu, którego podmiot zabezpieczeń próbuje uzyskać dostęp do aplikacji. 

Poświadczenia są weryfikowane przez dostawcę tożsamości. Dostawca tożsamości jest również sprawdzane, czy aplikacja identyfikator URI jest dla zarejestrowanej aplikacji i że podmiot zabezpieczeń ma poprawne uprawnienia do uzyskania tokenu dla tego zasobu. Jeśli żądanie jest prawidłowe, zostanie przyznany żetonu Web JSON. 

Następnie token musi przejść w nagłówku żądania do usługi Azure Resource Manager. Usługa Azure Resource Manager wykonuje następujące czynności, w określonej kolejności:
- Weryfikuje *wystawcy* oświadczeń (iss) potwierdzić, czy token jest przez dostawcę tożsamości poprawne. 
- Weryfikuje *odbiorców* oświadczeń (lub), aby potwierdzić, czy token został wystawiony do usługi Azure Resource Manager. 
- Sprawdza, czy tokenu Web JSON jest podpisany przy użyciu certyfikatu, która jest skonfigurowana za pośrednictwem protokołu OpenID jest znana usłudze Azure Resource Manager. 
- Przegląd *wystawiony w* (iat) i *wygaśnięcia* (exp) oświadczeń potwierdzić, czy token jest aktywny i mogą być akceptowane. 

Po ukończeniu wszystkich operacji sprawdzania poprawności używa usługi Azure Resource Manager *zgłoszą* (oid) i *grup* oświadczeń do listy zasobów, które mogą uzyskiwać dostęp do podmiotu zabezpieczeń. 

![Diagram protokół wymiany tokenu](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Za pomocą kontroli dostępu opartej na rolach  
Oparta na rolach kontroli dostępu (RBAC) w stosie Azure jest zgodna z implementacją w systemie Microsoft Azure. Dostęp do zasobów można zarządzać, przypisując odpowiednie role RBAC do użytkowników, grup i aplikacji. Aby uzyskać informacje o sposobie używania RBAC stosu Azure, zobacz następujące artykuły:
- [Rozpoczynanie pracy z opartej na rolach kontroli dostępu w portalu Azure](/azure/role-based-access-control/overview).
- [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą opartej na rolach kontroli dostępu](/azure/role-based-access-control/role-assignments-portal).
- [Tworzenie niestandardowych ról dla kontroli dostępu](/azure/role-based-access-control/custom-roles).
- [Zarządzanie kontrolą dostępu opartą na rolach](azure-stack-manage-permissions.md) Azure stosu.


### <a name="authenticate-with-azure-powershell"></a>Uwierzytelnianie przy użyciu programu Azure PowerShell  
Informacje dotyczące korzystania z programu Azure PowerShell do uwierzytelniania za pomocą stosu Azure można znaleźć w folderze [konfigurowania środowiska PowerShell użytkownika stosu Azure](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Uwierzytelniania za pomocą interfejsu wiersza polecenia platformy Azure
Aby dowiedzieć się, jak przy użyciu programu Azure PowerShell do uwierzytelniania w usłudze Azure stosu, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure do użycia z programem Azure stosu](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Kolejne kroki
- [Architektura tożsamości](azure-stack-identity-architecture.md)   
- [Integracja Datacenter - tożsamości](azure-stack-integrate-identity.md)




