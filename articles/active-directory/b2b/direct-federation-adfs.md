---
title: Konfigurowanie bezpośredniej federacji przy usługach AD FS dla B2B — usługa Azure AD
description: Dowiedz się, jak skonfigurować usługi AD FS jako dostawcę tożsamości dla bezpośredniej federacji, aby goście mogli logować się do aplikacji usługi Azure AD
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
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272825"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Przykład: Bezpośrednia federacja z usługami federacyjnymi Active Directory (AD FS) (wersja zapoznawcza)
|     |
| --- |
| Bezpośrednia federacja jest publiczną funkcją podglądu usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

W tym artykule opisano sposób konfigurowania [federacji bezpośredniej](direct-federation.md) przy użyciu usług federacyjnych Active Directory (AD FS) jako dostawcy tożsamości SAML 2.0 lub WS-Fed. Aby obsługiwać bezpośrednią federację, niektóre atrybuty i oświadczenia muszą być skonfigurowane u dostawcy tożsamości. Aby zilustrować sposób konfigurowania dostawcy tożsamości dla federacji bezpośredniej, jako przykład użyjemy usług federacyjnych Active Directory (AD FS). Pokażemy, jak skonfigurować usługi AD FS zarówno jako dostawca tożsamości SAML, jak i jako dostawca tożsamości WS-Fed.

> [!NOTE]
> W tym artykule opisano sposób konfigurowania usług AD FS dla celów ilustracyjnych zarówno dla saml, jak i WS-Fed. W przypadku bezpośrednich integracji federacyjnych, w których dostawcą tożsamości jest usługa AD FS, zalecamy użycie protokołu WS-Fed. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurowanie usług AD FS dla bezpośredniej federacji SAML 2.0
Usługi Azure AD B2B można skonfigurować do ujednolici z dostawcami tożsamości, którzy używają protokołu SAML z określonymi wymaganiami wymienionymi poniżej. Aby zilustrować kroki konfiguracji SAML, w tej sekcji pokazano, jak skonfigurować usługi AD FS dla SAML 2.0. 

Aby skonfigurować federację bezpośrednią, należy otrzymać następujące atrybuty w odpowiedzi SAML 2.0 od dostawcy tożsamości. Atrybuty te można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego online lub wprowadzając je ręcznie. Krok 12 w [artykule Tworzenie testowego wystąpienia usług AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) opisuje, jak znaleźć punkty `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`końcowe usług AD FS lub jak wygenerować adres URL metadanych, na przykład . 

|Atrybut  |Wartość  |
|---------|---------|
|TwierdzenieConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator wystawcy identyfikatora partnera, na przykład`http://www.example.com/exk10l6w90DHM0yi...`         |

Następujące oświadczenia muszą być skonfigurowane w tokenie SAML 2.0 wystawionym przez dostawcę tożsamości:


|Atrybut  |Wartość  |
|---------|---------|
|NameID Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


W następnej sekcji pokazano, jak skonfigurować wymagane atrybuty i oświadczenia przy użyciu usług AD FS jako przykład dostawcy tożsamości SAML 2.0.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem tej procedury należy już skonfigurować i działać serwer usług AD FS. Aby uzyskać pomoc dotyczącą konfigurowania serwera usług AD FS, zobacz [Tworzenie testowego wystąpienia usług AD FS 3.0 na maszynie wirtualnej platformy Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Dodawanie opisu oświadczenia

1. Na serwerze usług AD FS wybierz pozycję **Zarządzanie usługami** > **AD FS**.
2. W okienku nawigacji wybierz pozycję **Opisy** > **oświadczeń serwisowych**.
3. W obszarze **Akcje**wybierz pozycję **Dodaj opis oświadczenia**.
4. W oknie **Dodaj opis oświadczenia** określ następujące wartości:

   - **Nazwa wyświetlana**: Identyfikator trwały
   - **Identyfikator oświadczenia**:`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Zaznacz pole wyboru **Opublikuj ten opis oświadczenia w metadanych federacji jako typ oświadczenia, który może zaakceptować ta usługa federacji.**
   - Zaznacz pole wyboru **Opublikuj ten opis oświadczenia w metadanych federacji jako typ oświadczenia, który może wysłać ta usługa federacji.**

5. Kliknij przycisk **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Dodawanie reguł zaufania i oświadczeń jednostki uzależniającej

1. Na serwerze usług AD FS przejdź do **pozycji Zarządzanie usługami** > **AD FS**.
2. W okienku nawigacji wybierz pozycję **Relacje** > zaufania**relacji zależnych zaufania jednostki zaufania**.
3. W obszarze **Akcje**wybierz pozycję **Dodaj zaufanie jednostki uzależniającej**. 
4. W kreatorze dodawania zaufania jednostki uzależniającej dla **wybierz źródło danych**użyj opcji **Importowanie danych o stronie uzależniającej opublikowanej w trybie online lub w sieci lokalnej**. Określ ten adres https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlURL metadanych federacji- . Pozostaw inne domyślne zaznaczenia. Wybierz polecenie **Zamknij**.
5. Zostanie otwarty kreator **Edytowanie reguł oświadczeń.**
6. W kreatorze **Edytowanie reguł oświadczeń** wybierz pozycję **Dodaj regułę**. W **obszarze Wybierz typ reguły**wybierz pozycję Wyślij **atrybuty LDAP jako oświadczenia**. Wybierz **pozycję Dalej**.
7. W **obszarze Konfiguruj regułę oświadczeń**określ następujące wartości: 

   - **Nazwa reguły oświadczeń**: Reguła oświadczenia e-mail 
   - **Magazyn atrybutów**: Usługa Active Directory 
   - **Atrybut LDAP**: Adresy e-mail 
   - **Typ oświadczenia wychodzącego:** Adres e-mail

8. Wybierz **pozycję Zakończ**.
9. W oknie **Edytuj reguły oświadczeń** zostanie wyświetlenia nowej reguły. Kliknij przycisk **Zastosuj**. 
10. Kliknij przycisk **OK**.  

### <a name="create-an-email-transform-rule"></a>Tworzenie reguły przekształcania wiadomości e-mail
1. Przejdź do **pozycję Edytuj reguły oświadczeń** i kliknij pozycję Dodaj **regułę**. W **obszarze Wybierz typ reguły**wybierz pozycję **Przekształć oświadczenie przychodzące** i kliknij przycisk **Dalej**. 
2. W **obszarze Konfiguruj regułę oświadczeń**określ następujące wartości: 

   - **Nazwa reguły oświadczeń**: Reguła przekształcania wiadomości e-mail 
   - **Przychodzące typy oświadczeń**: Adres e-mail 
   - **Typ oświadczenia wychodzącego**: Identyfikator nazwy 
   - **Format identyfikatora nazwy wychodzącej**: Identyfikator trwały 
   - Wybierz pozycję **Przekazuj wszystkie wartości oświadczeń**.

3. Kliknij przycisk **Zakończ**. 
4. W oknie **Edytuj reguły oświadczeń** zostaną wyświetlene nowe reguły. Kliknij przycisk **Zastosuj**. 
5. Kliknij przycisk **OK**. Serwer usług AD FS jest teraz skonfigurowany do bezpośredniej federacji przy użyciu protokołu SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurowanie usług AD FS dla bezpośredniej federacji WS-Fed 
Usługi Azure AD B2B można skonfigurować do ujednolici z dostawcami tożsamości, którzy używają protokołu WS-Fed z określonymi wymaganiami wymienionymi poniżej. Obecnie dwóch dostawców WS-Fed zostały przetestowane pod kątem zgodności z usługą Azure AD obejmują usługi AD FS i Shibboleth. W tym miejscu użyjemy usług fedasacyjnych Active Directory (AD FS) jako przykładu dostawcy tożsamości WS-Fed. Aby uzyskać więcej informacji na temat ustanawiania zaufania jednostki uzależnionej między dostawcą zgodnym z usługą Azure AD, pobierz dokumenty zgodności dostawcy tożsamości usługi Azure AD.

Aby skonfigurować federację bezpośrednią, w wiadomości WS-Fed od dostawcy tożsamości należy odbierać następujące atrybuty. Atrybuty te można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego online lub wprowadzając je ręcznie. Krok 12 w [artykule Tworzenie testowego wystąpienia usług AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) opisuje, jak znaleźć punkty `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`końcowe usług AD FS lub jak wygenerować adres URL metadanych, na przykład .
 
|Atrybut  |Wartość  |
|---------|---------|
|Pasywny PunktEndor     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator wystawcy identyfikatora partnera, na przykład`http://www.example.com/exk10l6w90DHM0yi...`         |

Wymagane roszczenia dotyczące tokenu WS-Fed wystawionego przez IdP:

|Atrybut  |Wartość  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

W następnej sekcji pokazano, jak skonfigurować wymagane atrybuty i oświadczenia przy użyciu usług AD FS jako przykład dostawcy tożsamości WS-Fed.

### <a name="before-you-begin"></a>Przed rozpoczęciem
Przed rozpoczęciem tej procedury należy już skonfigurować i działać serwer usług AD FS. Aby uzyskać pomoc dotyczącą konfigurowania serwera usług AD FS, zobacz [Tworzenie testowego wystąpienia usług AD FS 3.0 na maszynie wirtualnej platformy Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Dodawanie reguł zaufania i oświadczeń jednostki uzależniającej 
1. Na serwerze usług AD FS przejdź do **pozycji Zarządzanie usługami** > **AD FS**. 
1. W okienku nawigacji wybierz pozycję **Relacje** > zaufania**relacji zależnych zaufania jednostki zaufania**. 
1. W obszarze **Akcje**wybierz pozycję **Dodaj zaufanie jednostki uzależniającej**.  
1. W kreatorze dodawania zaufania jednostki uzależniającej w obszarze **Wybierz źródło danych**użyj opcji **Importowanie danych o stronie uzależniającej opublikowanej w trybie online lub w sieci lokalnej**. Określ ten adres `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`URL metadanych federacji: .  Pozostaw inne domyślne zaznaczenia. Wybierz polecenie **Zamknij**.
1. Zostanie otwarty kreator **Edytowanie reguł oświadczeń.** 
1. W kreatorze **Edytowanie reguł oświadczeń** wybierz pozycję **Dodaj regułę**. W **obszarze Wybierz typ reguły**wybierz pozycję Wyślij oświadczenia przy użyciu **reguły niestandardowej**. Wybierz *pozycję Dalej*. 
1. W **obszarze Konfiguruj regułę oświadczeń**określ następujące wartości:

   - **Nazwa reguły oświadczeń**: Identyfikator nieodmówiony do wydania  
   - **Reguła niestandardowa**:`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Wybierz **pozycję Zakończ**. 
1. W oknie **Edytuj reguły oświadczeń** zostanie wyświetlenia nowej reguły. Kliknij przycisk **Zastosuj**.  
1. W tym samym kreatorze **Edytowania reguł oświadczeń** wybierz pozycję **Dodaj regułę**. W **obszarze Typ reguły cohose**wybierz pozycję **Wyślij atrybuty LDAP jako oświadczenia**. Wybierz **pozycję Dalej**.
1. W **obszarze Konfiguruj regułę oświadczeń**określ następujące wartości: 

   - **Nazwa reguły oświadczeń**: Reguła oświadczenia e-mail  
   - **Magazyn atrybutów**: Usługa Active Directory  
   - **Atrybut LDAP**: Adresy e-mail  
   - **Typ oświadczenia wychodzącego:** Adres e-mail 

1.  Wybierz **pozycję Zakończ**. 
1.  W oknie **Edytuj reguły oświadczeń** zostanie wyświetlenia nowej reguły. Kliknij przycisk **Zastosuj**.  
1.  Kliknij przycisk **OK**. Serwer usług AD FS jest teraz skonfigurowany do bezpośredniej federacji przy użyciu programu WS-Fed.

## <a name="next-steps"></a>Następne kroki
Następnie [skonfigurujesz bezpośrednią federację w usłudze Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) w portalu usługi Azure AD lub przy użyciu programu PowerShell. 
