---
title: Konfigurowanie bezpośredniego Federacja z usługami AD FS dla modelu B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi AD FS jako dostawcy tożsamości dla Federacji bezpośrednie, dzięki czemu goście zalogować się do aplikacji w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544324"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Przykład: Bezpośrednie federacji z usługi Active Directory Federation Services (AD FS) (wersja zapoznawcza)
|     |
| --- |
| Bezpośrednie federacyjnej jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

W tym artykule opisano sposób konfigurowania [bezpośrednie Federacji](direct-federation.md) przy użyciu usługi Active Directory Federation Services (AD FS) jako dostawcy tożsamości SAML 2.0 i WS-Fed. Aby zapewnić obsługę Federacji bezpośrednich, określonych atrybutach i oświadczeniach musi być skonfigurowany w dostawcy tożsamości. Aby zilustrować, jak skonfigurować dostawcę tożsamości dla Federacji bezpośrednie, użyjemy usługi Active Directory Federation Services (AD FS) jako przykład. Poniżej opisano sposób konfigurowania usług AD FS jako dostawcy tożsamości SAML i WS-Fed dostawcy tożsamości.

> [!NOTE]
> W tym artykule opisano sposób konfigurowania usług AD FS dla języka SAML i WS-Fed w celach ilustracyjnych. Integracje bezpośrednie Federacji dostawcy tożsamości w przypadku usług AD FS zaleca się, przy użyciu protokołu WS-Fed. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurowanie usług AD FS dla bezpośredniego federation SAML 2.0
Usługa Azure AD B2B można skonfigurować federowania z dostawcami tożsamości, które używają protokołu SAML przy użyciu określonych wymagań wymienionych poniżej. Aby zilustrować kroki konfiguracji SAML, w tej sekcji przedstawiono sposób konfigurowania usług AD FS 2.0 SAML. 

Aby skonfigurować bezpośrednie Federacji, następujące atrybuty musi otrzymać odpowiedzi SAML 2.0, od dostawcy tożsamości. Te atrybuty można skonfigurować, tworząc usługę tokenu zabezpieczającego online pliku XML lub wprowadzając je ręcznie. W kroku 12 w [Utwórz wystąpienie test usług AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) w tym artykule opisano sposób znajdowania punktów końcowych usługi AD FS lub jak na przykład wygenerować adres URL metadanych `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Atrybut  |Wartość  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Wystawcy identyfikatora URI partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |

Następujące oświadczeń muszą zostać skonfigurowane w tokenie języka SAML 2.0, wystawiony przez dostawcę tożsamości:


|Atrybut  |Wartość  |
|---------|---------|
|Format identyfikatora NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


Następna sekcja przedstawia sposób konfigurowania wymaganych atrybutów i oświadczeń przy użyciu usług AD FS, na przykład dostawcy tożsamości SAML 2.0.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Serwer usług AD FS musi być już ustawiona w pionie i działa, przed rozpoczęciem tej procedury. Aby uzyskać pomoc dotyczącą konfigurowania serwera usług AD FS, zobacz [utworzenia wystąpienia testu usługi AD FS 3.0 na maszynie wirtualnej platformy Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Dodaj opis oświadczeń

1. Na serwerze usług AD FS wybierz **narzędzia** > **zarządzania usług AD FS**.
2. W okienku nawigacji wybierz **usługi** > **opisy oświadczeń**.
3. W obszarze **akcje**, wybierz opcję **dodać opis oświadczenia**.
4. W **dodać opis oświadczeń** okna, podaj następujące wartości:

   - **Nazwa wyświetlana**: Identyfikator trwały
   - **Identyfikator oświadczenia**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Zaznacz pole wyboru dla **opublikować ten opis oświadczenia w metadanych Federacji jako typ oświadczenia, który może akceptować tej usługi federacyjnej**.
   - Zaznacz pole wyboru dla **opublikować ten opis oświadczenia w metadanych Federacji jako typ oświadczenia, który można wysłać tej usługi federacyjnej**.

5. Kliknij przycisk **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Dodawanie zaufania jednostki uzależnionej i reguł oświadczeń

1. Na serwerze usług AD FS, przejdź do **narzędzia** > **zarządzania usług AD FS**.
2. W okienku nawigacji wybierz **relacje zaufania** > **zaufania jednostek uzależnionych**.
3. W obszarze **akcje**, wybierz opcję **Dodaj zaufanie jednostki uzależnionej**. 
4. Dodaj jednostki uzależnionej strony zaufania kreatora dla **wybierz źródło danych**, użyj opcji **importowanie danych dotyczących jednostki uzależnionej opublikowane online lub w sieci lokalnej**. Określ te metadane Federacji adres URL - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml. Pozostaw inne wartości domyślne. Wybierz polecenie **Zamknij**.
5. **Edycja reguł oświadczeń** zostanie otwarty Kreator.
6. W **Edycja reguł oświadczeń** kreatora wybierz **Dodaj regułę**. W **wybierz typ reguły**, wybierz opcję **wysyłać atrybuty LDAP jako oświadczenia**. Wybierz opcję **Dalej**.
7. W **konfigurowanie reguły oświadczeń**, określ następujące wartości: 

   - **Nazwa reguły oświadczeń**: Regułę oświadczenia poczty e-mail 
   - **Magazyn atrybutów**: Usługa Active Directory 
   - **Atrybut LDAP**: E-Mail-Addresses 
   - **Typ oświadczenia wychodzącego**: Adres e-Mail

8. Wybierz pozycję **Finish** (Zakończ).
9. **Edycja reguł oświadczeń** okno zostanie wyświetlone nowe reguły. Kliknij przycisk **Zastosuj**. 
10. Kliknij przycisk **OK**.  

### <a name="create-an-email-transform-rule"></a>Utwórz regułę przekształcenia wiadomości e-mail
1. Przejdź do **Edycja reguł oświadczeń** i kliknij przycisk **Dodaj regułę**. W **wybierz typ reguły**, wybierz opcję **przekształcania oświadczeń przychodzących** i kliknij przycisk **dalej**. 
2. W **konfigurowanie reguły oświadczeń**, określ następujące wartości: 

   - **Nazwa reguły oświadczeń**: Reguły przekształcania poczty e-mail 
   - **Typ oświadczenia przychodzącego**: Adres e-mail 
   - **Typ oświadczenia wychodzącego**: Identyfikator nazwy 
   - **Formatu wychodzącego Identyfikatora nazwy**: Identyfikator trwały 
   - Wybierz pozycję **Przekazuj wszystkie wartości oświadczeń**.

3. Kliknij przycisk **Zakończ**. 
4. **Edycja reguł oświadczeń** okno zostanie wyświetlone nowe zasady. Kliknij przycisk **Zastosuj**. 
5. Kliknij przycisk **OK**. Serwer usług AD FS jest skonfigurowany do bezpośredniego Federacji przy użyciu protokołu SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurowanie usług AD FS dla Federacji bezpośrednie WS-Fed 
Usługa Azure AD B2B można skonfigurować federowania z dostawcami tożsamości, korzystające z protokołu WS-Fed przy użyciu określonych wymagań wymienionych poniżej. Obecnie dwóch dostawców usługi WS-Fed zostały przetestowane na zgodność z usługą Azure AD usług AD FS i sieci Shibboleth. W tym miejscu użyjemy usługi Active Directory Federation Services (AD FS) na przykład WS-Fed dostawcy tożsamości. Aby uzyskać więcej informacji na temat ustanawiania zaufania jednostki uzależnionej między usługi WS-Fed dostawcy zgodne z usługą Azure AD Pobierz Docs zgodności dostawcy usługi Azure AD Identity.

Aby skonfigurować bezpośrednie Federacji, następujące atrybuty musi zostać odebrana w wiadomości WS-Fed od dostawcy tożsamości. Te atrybuty można skonfigurować, tworząc usługę tokenu zabezpieczającego online pliku XML lub wprowadzając je ręcznie. W kroku 12 w [Utwórz wystąpienie test usług AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) w tym artykule opisano sposób znajdowania punktów końcowych usługi AD FS lub jak na przykład wygenerować adres URL metadanych `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Atrybut  |Wartość  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Wystawcy identyfikatora URI partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |

Wymagane oświadczenia dla tokenu usługi WS-Fed, wystawiony przez dostawców tożsamości:

|Atrybut  |Wartość  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

Następna sekcja przedstawia sposób konfigurowania wymaganych atrybutów i oświadczenia, za pomocą usług AD FS, na przykład dostawca tożsamości usługi WS-Fed.

### <a name="before-you-begin"></a>Przed rozpoczęciem
Serwer usług AD FS musi być już ustawiona w pionie i działa, przed rozpoczęciem tej procedury. Aby uzyskać pomoc dotyczącą konfigurowania serwera usług AD FS, zobacz [utworzenia wystąpienia testu usługi AD FS 3.0 na maszynie wirtualnej platformy Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Dodawanie zaufania jednostki uzależnionej i reguł oświadczeń 
1. Na serwerze usług AD FS, przejdź do **narzędzia** > **zarządzania usług AD FS**. 
1. W okienku nawigacji wybierz **relacje zaufania** > **zaufania jednostek uzależnionych**. 
1. W obszarze **akcje**, wybierz opcję **Dodaj zaufanie jednostki uzależnionej**.  
1. W oknie Dodaj jednostki uzależnionej strony zaufania kreatora, aby uzyskać **wybierz źródło danych**, użyj opcji **importowanie danych dotyczących jednostki uzależnionej opublikowane online lub w sieci lokalnej**. Określ ten adres URL metadanych Federacji: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`.  Pozostaw inne wartości domyślne. Wybierz polecenie **Zamknij**.
1. **Edycja reguł oświadczeń** zostanie otwarty Kreator. 
1. W **Edycja reguł oświadczeń** kreatora wybierz **Dodaj regułę**. W **wybierz typ reguły**, wybierz opcję **wysyłanie oświadczeń przy użyciu reguły niestandardowej**. Wybierz opcję *Dalej*. 
1. W **konfigurowanie reguły oświadczeń**, określ następujące wartości:

   - **Nazwa reguły oświadczeń**: Niemodyfikowalny identyfikator problemu  
   - **Niestandardowa reguła**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Wybierz pozycję **Finish** (Zakończ). 
1. **Edycja reguł oświadczeń** okno zostanie wyświetlone nowe reguły. Kliknij przycisk **Zastosuj**.  
1. W tym samym **Edycja reguł oświadczeń** kreatora wybierz **Dodaj regułę**. W **typ reguły Cohose**, wybierz opcję **wysyłać atrybuty LDAP jako oświadczenia**. Wybierz opcję **Dalej**.
1. W **konfigurowanie reguły oświadczeń**, określ następujące wartości: 

   - **Nazwa reguły oświadczeń**: Regułę oświadczenia poczty e-mail  
   - **Magazyn atrybutów**: Usługa Active Directory  
   - **Atrybut LDAP**: E-Mail-Addresses  
   - **Typ oświadczenia wychodzącego**: Adres e-Mail 

1.  Wybierz pozycję **Finish** (Zakończ). 
1.  **Edycja reguł oświadczeń** okno zostanie wyświetlone nowe reguły. Kliknij przycisk **Zastosuj**.  
1.  Kliknij przycisk **OK**. Serwer usług AD FS jest skonfigurowany do bezpośredniego Federacji przy użyciu usługi WS-Fed.

## <a name="next-steps"></a>Kolejne kroki
Następnie będzie [konfigurowania Federacji bezpośrednie w usłudze Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) w portalu usługi Azure AD lub przy użyciu programu PowerShell. 
