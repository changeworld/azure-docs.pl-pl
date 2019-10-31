---
title: Klucz zabezpieczeń bezhasłem Zaloguj się do zasobów lokalnych (wersja zapoznawcza) — Azure Active Directory
description: Włącz klucz zabezpieczeń bezz hasłem Zaloguj się do zasobów lokalnych (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164863"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>Włącz klucz zabezpieczeń bezz hasłem Zaloguj się do zasobów lokalnych (wersja zapoznawcza)

Ten dokument koncentruje się na włączaniu uwierzytelniania bez hasła do zasobów lokalnych w środowiskach z **dołączonymi usługami Azure AD** i **hybrydowymi** urządzeniami z systemem Windows 10 przyłączonymi do usługi Azure AD. Ta funkcja zapewnia bezproblemowe logowanie jednokrotne do zasobów lokalnych przy użyciu kluczy zabezpieczeń zgodnych z firmą Microsoft.

|     |
| --- |
| Klucze zabezpieczeń FIDO2 są publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Logowanie jednokrotne do zasobów lokalnych przy użyciu kluczy FIDO2

Azure Active Directory (AD) może wystawić bilety uprawniające do przyznania biletu Kerberos (TGT) dla co najmniej jednej domeny Active Directory. Ta funkcja umożliwia użytkownikom logowanie się do systemu Windows przy użyciu nowoczesnych poświadczeń, takich jak FIDO2 Security Keys i dostęp do tradycyjnych zasobów opartych na Active Directory. Bilety i autoryzacje usług Kerberos będą nadal kontrolowane przez lokalne Active Directory kontrolery domeny.

Obiekt serwera usługi Azure AD Kerberos zostanie utworzony w Active Directory lokalnym, a następnie być bezpiecznie opublikowany w Azure Active Directory. Obiekt nie jest skojarzony z żadnym serwerem fizycznym. Jest to po prostu zasób, którego może używać Azure Active Directory do generowania biletów udzielania biletów protokołu Kerberos (TGT) dla domena usługi Active Directory.

![Uzyskiwanie biletu TGT i PRT z usługi Azure AD i AD DS](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. Użytkownik loguje się na urządzeniu z systemem Windows 10 przy użyciu klucza zabezpieczeń FIDO2 i uwierzytelnia się w usłudze Azure AD.
1. Usługa Azure AD sprawdza katalog pod kątem klucza serwera Kerberos zgodnego z lokalną domeną usługi AD użytkownika.
   1. Usługa Azure AD generuje bilet TGT protokołu Kerberos dla lokalnej domeny usługi AD użytkownika. Bilet TGT zawiera tylko identyfikator SID użytkownika. Bilet TGT nie zawiera żadnych danych autoryzacji.
1. Bilet TGT jest zwracany do klienta wraz z tokenem podstawowego odświeżania usługi Azure AD (PRT).
1. Komputer kliencki kontaktuje się z lokalnym kontrolerem domeny usługi AD i wymienia częściowy bilet TGT dla w pełni uformowanego biletu TGT.
1. Komputer kliencki ma teraz usługę Azure AD PRT i pełną Active Directory bilet TGT i mogą uzyskać dostęp do zasobów w chmurze i lokalnych.

## <a name="requirements"></a>Wymagania

Przed wykonaniem kroków opisanych w tym artykule organizacje muszą wykonać kroki opisane w artykule [Włączanie znaku zabezpieczeń bezhasło na urządzeniach z systemem Windows 10 (wersja zapoznawcza)](howto-authentication-passwordless-security-key.md) .

Organizacje muszą spełniać następujące wymagania dotyczące oprogramowania.

- Na urządzeniach musi być uruchomiona kompilacja systemu Windows 10 w wersji 18945 lub nowszej
- Uaktualnij do najnowszej wersji [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
- W pełni poprawione kontrolery domeny systemu Windows Server 2016/2019 do obsługi obciążenia lokalnego żądania uwierzytelniania.

### <a name="supported-scenarios"></a>Obsługiwane scenariusze

- Scenariusz obsługuje logowanie jednokrotne (SSO) do obu:
   - Zasoby w chmurze, takie jak pakiet Office 365 i inne aplikacje z obsługą protokołu SAML.
   - Zasoby lokalne i zintegrowane z systemem Windows uwierzytelnianie w witrynach sieci Web, w tym witryny sieci Web i witryny programu SharePoint, które wymagają uwierzytelniania usług IIS i/lub zasoby korzystające z uwierzytelniania NTLM.

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze nie są obsługiwane:

- Przyłączone do domeny Active Directory Domain Services systemu Windows Server (AD DS) wdrożenie **nie jest obsługiwane**.
- Scenariusze dotyczące protokołu RDP, infrastruktury VDI i Citrix **nie są obsługiwane** przy użyciu klucza zabezpieczeń.
- Użycie protokołu S/MIME **nie jest obsługiwane** przy użyciu klucza zabezpieczeń.
- Użycie opcji "Uruchom jako" **nie jest obsługiwane** przy użyciu klucza zabezpieczeń.
- Logowanie się do serwera przy użyciu klucza zabezpieczeń **nie jest obsługiwane**.

## <a name="create-kerberos-server-object"></a>Utwórz obiekt serwera Kerberos

Administratorzy będą korzystać z narzędzi programu PowerShell z serwera Azure AD Connect, aby utworzyć obiekt serwera Kerberos w usłudze Azure AD w katalogu lokalnym. Należy wykonać te kroki w każdej domenie i lesie w organizacji zawierającej użytkowników usługi Azure AD.

1. Uaktualnij do najnowszej wersji Azure AD Connect. W instrukcjach przyjęto założenie, że już skonfigurowano Azure AD Connect do obsługi środowiska hybrydowego.
1. Na serwerze Azure AD Connect Otwórz wiersz polecenia programu PowerShell z podwyższonym poziomem uprawnień i przejdź do folderu C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\
1. Uruchom następujące polecenia programu PowerShell, aby utworzyć nowy obiekt serwera usługi Azure AD Kerberos w lokalnej domenie Active Directory i Azure Active Directory dzierżawie.

> [!NOTE]
> W poniższym przykładzie Zastąp ciąg "contoso.corp.com" nazwą domeny lokalnego Active Directory.

```PowerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Przeglądanie i weryfikowanie serwera usługi Azure AD Kerberos

Można wyświetlić i sprawdzić nowo utworzony serwer Kerberos usługi Azure AD za pomocą następującego polecenia. 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

To polecenie spowoduje wyjście z właściwości serwera Kerberos usługi Azure AD. Możesz przejrzeć właściwości, aby upewnić się, że wszystko jest w dobrej kolejności.

| Właściwość | Opis |
| --- | --- |
| ID | Unikatowy identyfikator obiektu kontrolera domeny usługi AD. Ten identyfikator jest czasami określany jako "gniazdo" lub "Identyfikator rozgałęzienia". |
| DomainDnsName | Nazwa domeny DNS domena usługi Active Directory. |
| ComputerAccount | Obiekt konta komputera serwera usługi Azure AD Kerberos (kontroler domeny). |
| UserAccount | Obiekt wyłączonego konta użytkownika, który zawiera klucz szyfrowania TGT serwera Kerberos usługi Azure AD. Nazwa wyróżniająca tego konta będzie: <br> CN = krbtgt_AzureAD, CN = users, < domena-DN > |
| Wersja programu | Wersja klucza klucza szyfrowania biletu usługi Azure AD Kerberos Server. Wersja zostanie przypisana podczas tworzenia klucza. Wersja jest następnie zwiększana za każdym razem, gdy klucz zostanie obrócony. Przyrosty są oparte na metadanych replikacji i prawdopodobnie będą większe niż jeden. Na przykład początkowa wersja może być 192272. Gdy klucz zostanie obrócony po raz pierwszy, wersja może przejść do 212621. Ważne jest, aby sprawdzić, czy wersja usługi dla obiektu lokalnego i CloudKeyVersion dla obiektu w chmurze jest taka sama. |
| KeyUpdatedOn | Data i godzina aktualizacji/utworzenia klucza szyfrowania TGT serwera Kerberos usługi Azure AD. |
| KeyUpdatedFrom | Kontroler domeny, na którym został ostatnio zaktualizowany klucz szyfrowania TGT serwera Kerberos usługi Azure AD. |
| CloudId | Identyfikator z obiektu usługi Azure AD. Musi być zgodna z powyższym IDENTYFIKATORem. |
| CloudDomainDnsName | DomainDnsName z obiektu usługi Azure AD. Musi być zgodna z DomainDnsName powyżej. |
| CloudKeyVersion | Wersja z obiektu usługi Azure AD. Musi być zgodna z powyższą wersją. |
| CloudKeyUpdatedOn | KeyUpdatedOn z obiektu usługi Azure AD. Musi być zgodna z KeyUpdatedOn powyżej. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Obracanie klucza serwera Kerberos usługi Azure AD

Należy regularnie obrócić klucze KRBTGT szyfrowania serwera Kerberos usługi Azure AD. Zalecamy wykonanie tego samego harmonogramu, który służy do rotacji wszystkich innych kluczy KRBTGT kontrolera domena usługi Active Directory.

> [!WARNING]
> Istnieją inne narzędzia, które mogą obrócić klucze KRBTGT, jednak należy użyć narzędzi wymienionych w tym dokumencie, aby obrócić klucze KRBTGT serwera Kerberos usługi Azure AD. Dzięki temu klucze są aktualizowane zarówno w lokalnej usłudze AD, jak i w usłudze Azure AD.

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Usuwanie serwera usługi Azure AD Kerberos

Jeśli chcesz przywrócić scenariusz i usunąć serwer Kerberos usługi Azure AD zarówno z Active Directory lokalnych, jak i Azure Active Directory, uruchom następujące polecenie.  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenariusze obejmujące wiele lasów i wiele domen

Obiekt serwera Kerberos usługi Azure AD jest reprezentowany w usłudze Azure AD jako obiekt KerberosDomain. Każda lokalna domena Active Directory jest reprezentowana jako pojedynczy obiekt KerberosDomain w usłudze Azure AD.

Na przykład organizacja ma Active Directory Las z dwiema domenami, contoso.com i fabrikam.com. Jeśli zdecydujesz się zezwolić usłudze Azure AD na wystawianie biletów uprawniających do przyznania biletu Kerberos dla całego lasu, w usłudze Azure AD będą dostępne dwa obiekty KerberosDomain. Jeden obiekt KerberosDomain dla contoso.com i jeden dla fabrikam.com. Jeśli masz wiele Active Directory lasów, dla każdej domeny w każdym lesie będzie miał jeden obiekt KerberosDomain.

Należy wykonać kroki opisane w temacie [Tworzenie obiektu serwera Kerberos](#create-kerberos-server-object) w każdej domenie i lesie w organizacji zawierającej użytkowników usługi Azure AD.

## <a name="known-behavior"></a>Znane zachowanie

Logowanie za pomocą FIDO zostanie zablokowane, jeśli hasło wygasło. Oczekiwana jest możliwość resetowania hasła przez użytkownika przed zalogowaniem się przy użyciu FIDO.

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkać problemy podczas korzystania z tej funkcji, Udostępnij aplikację centrum opinii o systemie Windows.

1. Uruchom **centrum opinii** i upewnij się, że użytkownik jest zalogowany.
1. Prześlij opinię poniżej następującej kategoryzacji:
   1. Kategoria: zabezpieczenia i prywatność
   1. Podkategoria: FIDO
1. Aby przechwytywać dzienniki, użyj opcji: **Utwórz ponownie mój problem**

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-this-work-in-my-on-premises-environment"></a>Czy ta usługa działa w środowisku lokalnym?

Ta funkcja nie działa w przypadku czystego środowiska lokalnego Active Directory Domain Services (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Moja organizacja wymaga uwierzytelniania dwuskładnikowego, aby uzyskać dostęp do zasobów, co można zrobić, aby obsłużyć to wymaganie?

Klucze zabezpieczeń są dostępne w różnych aspektach. Skontaktuj się z producentem urządzenia, aby omówić, jak ich urządzenia mogą być włączone przy użyciu kodu PIN lub biometrycznego jako drugiego czynnika.

### <a name="can-admins-set-up-security-keys"></a>Czy Administratorzy mogą konfigurować klucze zabezpieczeń?

Pracujemy nad tą funkcją, aby uzyskać ogólną dostępność tej funkcji.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Gdzie mogę znaleźć zgodne klucze zabezpieczeń?

[FIDO2 klucze zabezpieczeń](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Co mam zrobić, Jeśli utracisz mój klucz zabezpieczeń?

Klucze można usunąć z Azure Portal, przechodząc do strony informacje zabezpieczające i usuwając klucz zabezpieczeń.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nie mogę używać FIDO natychmiast po utworzeniu hybrydowej maszyny połączonej z usługą Azure AD

W przypadku czystej instalacji hybrydowej maszyny połączonej z usługą Azure AD, przyłączenia do domeny i ponownego uruchomienia należy zalogować się przy użyciu hasła i poczekać, aż zasady zsynchronizują się, zanim będzie możliwe użycie FIDO do logowania.

- Sprawdź bieżący stan, wpisując `dsregcmd /status` w oknie polecenia i sprawdź, czy `YES``AzureAdJoined` i `DomainJoined`
- To opóźnienie jest znanym ograniczeniem dla urządzeń przyłączonych do domeny, a nie FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Nie mogę pobrać logowania jednokrotnego do zasobu sieciowego NTLM po zalogowaniu się za pomocą programu FIDO i wyświetleniu monitu o poświadczenia

Upewnij się, że jest wystarczająca liczba kontrolerów domeny w celu uzyskania odpowiedzi na czas na obsługę żądania zasobu. Aby sprawdzić, czy widzisz kontroler domeny, na którym działa funkcja, sprawdź dane wyjściowe polecenia Nltest/dsgetdc: contoso/keylist/KDC.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o bezhaseł](concept-authentication-passwordless.md)
