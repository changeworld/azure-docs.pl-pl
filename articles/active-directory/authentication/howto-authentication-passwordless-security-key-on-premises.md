---
title: Logowanie się bez hasła do zasobów lokalnych (wersja zapoznawcza) — usługa Azure Active Directory
description: Dowiedz się, jak włączyć logowanie się bez użycia hasła do zasobów lokalnych przy użyciu usługi Azure Active Directory (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b29f84931c169ffe1c2c81d5e32201cbc63fc88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942875"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Włączanie logowania bez hasła do zasobów lokalnych za pomocą usługi Azure Active Directory (wersja zapoznawcza)

Ten dokument koncentruje się na włączaniu uwierzytelniania bez hasła do zasobów lokalnych dla środowisk, w które **są przyłączone do usługi Azure AD,** jak i hybrydowe urządzenia z systemem Windows 10 **przyłączone do usługi Azure AD.** Ta funkcja zapewnia bezproblemowe logowanie jednokrotne (Logowanie jednokrotne) do zasobów lokalnych przy użyciu kluczy zabezpieczeń zgodnych z firmą Microsoft.

|     |
| --- |
| Klucze zabezpieczeń FIDO2 są publiczną funkcją podglądu usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Wpisy typu SSO do zasobów lokalnych przy użyciu kluczy FIDO2

Usługa Azure Active Directory (AD) może wystawiać bilety do udzielania biletów kerberos (TGTs) dla co najmniej jednej domeny usługi Active Directory. Ta funkcja umożliwia użytkownikom logowanie się do systemu Windows przy użyciu nowoczesnych poświadczeń, takich jak klucze zabezpieczeń FIDO2, i uzyskiwanie dostępu do tradycyjnych zasobów opartych na usłudze Active Directory. Bilety usługi Kerberos i autoryzacja są nadal kontrolowane przez lokalne kontrolery domeny usługi Active Directory.

Obiekt serwera Kerberos usługi Azure jest tworzony w lokalnej usłudze Active Directory, a następnie bezpiecznie publikowany w usłudze Azure Active Directory. Obiekt nie jest skojarzony z żadnymi serwerami fizycznymi. Jest to po prostu zasób, który może być używany przez usługę Azure Active Directory do generowania TGT Protokołu Kerberos dla domeny usługi Active Directory.

![Uzyskiwanie biletu do przyznawania biletów (TGT) z usług Ad AD i usług AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Użytkownik loguje się do urządzenia z systemem Windows 10 za pomocą klucza zabezpieczeń FIDO2 i uwierzytelnia się w usłudze Azure AD.
1. Usługa Azure AD sprawdza katalog klucza serwera Kerberos pasującego do lokalnej domeny usługi AD użytkownika.
   1. Usługa Azure AD generuje protokół TGT protokołu Kerberos dla lokalnej domeny usługi AD użytkownika. TGT zawiera tylko identyfikator SID użytkownika. W TGT nie są zawarte żadne dane autoryzacji.
1. TGT jest zwracany do klienta wraz z ich azure ad token odświeżania podstawowego (PRT).
1. Komputer kliencki kontaktuje się z lokalnym kontrolerem domeny usługi AD i handluje częściową licencją TGT dla w pełni uformowanego TGT.
1. Komputer kliencki ma teraz PRT usługi Azure AD i pełną licencję TGT usługi Active Directory i może uzyskiwać dostęp zarówno do zasobów w chmurze, jak i lokalnych.

## <a name="requirements"></a>Wymagania

Organizacje muszą wykonać kroki, aby [włączyć znak bez hasła klucz zabezpieczeń do urządzeń z systemem Windows 10 (wersja zapoznawcza)](howto-authentication-passwordless-security-key.md) przed wykonaniem kroków w tym artykule.

Organizacje muszą również spełniać następujące wymagania dotyczące oprogramowania.

- Urządzenia muszą być uruchomione w systemie Windows 10 Insider Build 18945 lub nowszym.
- Musisz mieć wersję 1.4.32.0 lub nowszą [usługi Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Aby uzyskać więcej informacji na temat dostępnych opcji uwierzytelniania hybrydowego usługi Azure AD, zobacz [Wybieranie właściwej metody uwierzytelniania dla rozwiązania do tożsamości hybrydowej usługi Azure Active Directory](../../security/fundamentals/choose-ad-authn.md) i [Wybierz typ instalacji, który ma być używany dla usługi Azure AD Connect.](../hybrid/how-to-connect-install-select-installation.md)
- Kontrolery domeny systemu Windows Server muszą mieć zainstalowane następujące poprawki:
    - Dla systemu Windows Server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Dla systemu Windows Server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Obsługiwane scenariusze

Scenariusz obsługuje logowanie jednokrotne (Logowanie jednokrotne) w obu następujących scenariuszach:

- W przypadku zasobów w chmurze, takich jak Office 365 i inne aplikacje obsługujące saml.
- W przypadku zasobów lokalnych i uwierzytelniania zintegrowanego z systemem Windows w witrynach sieci Web. Zasoby mogą obejmować witryny sieci Web i witryny programu SharePoint, które wymagają uwierzytelniania usług IIS i / lub zasobów, które używają uwierzytelniania NTLM.

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze nie są obsługiwane:

- Przyłączone do domeny Usług domenowych Active Directory (AD DS) systemu Windows Server (tylko urządzenia lokalne).
- Scenariusze RDP, VDI i Citrix przy użyciu klucza zabezpieczeń.
- S/MIME przy użyciu klucza zabezpieczeń.
- "Uruchom jako" przy użyciu klucza zabezpieczeń.
- Zaloguj się do serwera przy użyciu klucza zabezpieczeń.

## <a name="create-kerberos-server-object"></a>Tworzenie obiektu serwera Kerberos

Administratorzy używają narzędzi programu PowerShell z serwera usługi Azure AD Connect do tworzenia obiektu protokołu Kerberos Server usługi Azure w katalogu lokalnym. Uruchom następujące kroki w każdej domenie i lesie w organizacji, które zawierają użytkowników usługi Azure AD:

1. Uaktualnij do najnowszej wersji usługi Azure AD Connect. Instrukcje zakładają, że usługa Azure AD Connect została już skonfigurowana do obsługi środowiska hybrydowego.
1. Na serwerze Azure AD Connect Server otwórz monit programu PowerShell z podwyższonym poziomem uprawnień i przejdź do`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Uruchom następujące polecenia programu PowerShell, aby utworzyć nowy obiekt serwera usługi Azure AD Kerberos zarówno w lokalnej domenie usługi Active Directory, jak i dzierżawie usługi Azure Active Directory.

> [!NOTE]
> W `contoso.corp.com` poniższym przykładzie zamień lokalną nazwę domeny usługi Active Directory.

```powerShell
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

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Wyświetlanie i weryfikowanie serwera protokołu Kerberos usługi Azure AD

Nowo utworzonego serwera Kerberos usługi Azure AD można wyświetlać i weryfikować za pomocą następującego polecenia:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

To polecenie wyprowadza właściwości serwera Kerberos usługi Azure AD. Można przejrzeć właściwości, aby sprawdzić, czy wszystko jest w dobrym porządku.

| Właściwość | Opis |
| --- | --- |
| ID | Unikatowy identyfikator obiektu kontrolera domeny usług AD DS. Ten identyfikator jest czasami określany jako "slot" lub "identyfikator oddziału". |
| DomainDnsName | Nazwa domeny DNS domeny usługi Active Directory. |
| Konto komputera | Obiekt konta komputera obiektu usługi Azure AD Kerberos Server (kontrolera domeny). |
| Konto użytkownika | Obiekt wyłączonego konta użytkownika, który posiada klucz szyfrowania TGT serwera usługi Azure AD Kerberos. Numer DN tego konta jest`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion (Wersja klawiszowa) | Kluczowa wersja klucza szyfrowania TGT serwera usługi Azure AD Kerberos. Wersja jest przypisywana podczas tworzenia klucza. Wersja jest następnie zwiększana za każdym razem, gdy klucz jest obracany. Przyrosty są oparte na meta-danych replikacji i prawdopodobnie większe niż jeden. Na przykład początkowa *KeyVersion* może być *192272*. Przy pierwszym obrócenie klawisza, wersja może przejść do *212621*. Ważną rzeczą do sprawdzenia jest to, że *KeyVersion* dla obiektu lokalnego i *CloudKeyVersion* dla obiektu w chmurze są takie same. |
| KeyUpdatedOn (KeyUpdatedOn) | Data i godzina, że klucz szyfrowania TGT serwera usługi Azure AD Kerberos został zaktualizowany lub utworzony. |
| KeyUpdatedFrom | Kontroler domeny, w którym ostatnio zaktualizowano klucz szyfrowania TGT serwera usługi Azure AD Kerberos. |
| CloudId (ida chmura) | Identyfikator obiektu usługi Azure AD. Musi być zgodny z powyższym identyfikatorem. |
| Nazwa clouddomaindns | *DomainDnsName* z obiektu usługi Azure AD. Musi być zgodny *z DomainDnsName* powyżej. |
| CloudKeyVersion (Wersja główna) | *KeyVersion* z obiektu usługi Azure AD. Musi odpowiadać *KeyVersion* powyżej. |
| Usługa CloudKeyUpdatedOn | *KeyUpdatedOn* z obiektu usługi Azure AD. Musi być zgodny *z KeyUpdatedOn* powyżej. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Obracanie klucza serwera kerberos usługi Azure AD

Klucze szyfrowania usługi Azure AD Kerberos Server powinny być obracane regularnie. Zaleca się, aby postępować zgodnie z tym samym harmonogramem, którego używasz do obracania wszystkich innych kluczy kontrolera domeny usługi Active Directory.

> [!WARNING]
> Istnieją inne narzędzia, które mogą obracać klucze krbtgt, jednak należy użyć narzędzi wymienionych w tym dokumencie, aby obrócić klucze krbtgt serwera Kerberos usługi Azure. Dzięki temu klucze są aktualizowane zarówno w lokalnej usłudze AD, jak i w usłudze Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Usuwanie serwera protokołu Kerberos usługi Azure AD

Jeśli chcesz przywrócić scenariusz i usunąć serwer Kerberos usługi Azure AD zarówno z lokalnej usługi Active Directory, jak i usługi Azure Active Directory, uruchom następujące polecenie:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenariusze wielolesowe i wielodomenowe

Obiekt serwera usługi Azure AD Kerberos jest reprezentowany w usłudze Azure AD jako obiekt *Protokołu KerberosDomain.* Każda lokalna domena usługi Active Directory jest reprezentowana jako pojedynczy obiekt *KerberosDomain* w usłudze Azure AD.

Na przykład organizacja ma las usługi Active Directory `contoso.com` `fabrikam.com`z dwiema domenami i . Jeśli zdecydujesz się zezwolić usługi Azure AD na wystawianie TGT Protokołu Kerberos dla całego lasu, w usłudze Azure AD znajdują się dwa obiekty *KerberosDomain.* Jeden obiekt *KerberosDomain* dla `contoso.com` `fabrikam.com`, i jeden dla . Jeśli masz wiele lasów usługi Active Directory, w każdym lesie jest jeden obiekt *Protokołu KerberosDomain* dla każdej domeny.

Należy wykonać kroki, aby [utworzyć obiekt serwera Kerberos](#create-kerberos-server-object) w każdej domenie i lesie w organizacji, które zawierają użytkowników usługi Azure AD.

## <a name="known-behavior"></a>Znane zachowanie

Logowanie się za pomocą FIDO jest zablokowane, jeśli hasło wygasło. Oczekuje się, że użytkownik zresetuje swoje hasło, zanim będzie mógł zalogować się za pomocą FIDO.

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkasz problemy podczas wyświetlania podglądu tej funkcji, udostępnij za pośrednictwem aplikacji Centrum opinii systemu Windows, wykonując następujące czynności:

1. Uruchom **Centrum opinii** i upewnij się, że się zalogowałeś.
1. Prześlij opinię w następującej kategorii:
   - Kategoria: Bezpieczeństwo i prywatność
   - Podkategoria: FIDO
1. Aby przechwycić dzienniki, użyj **opcji,** aby ponownie utworzyć problem

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-this-work-in-my-on-premises-environment"></a>Czy to działa w moim środowisku lokalnym?

Ta funkcja nie działa w środowisku czysto lokalnych usług domenowych Active Directory (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Moja organizacja wymaga uwierzytelniania dwuskładnikowego, aby uzyskać dostęp do zasobów. Co mogę zrobić, aby wesprzeć ten wymóg?

Klucze bezpieczeństwa są w różnych rozmiarach. Skontaktuj się z interesującym producentem urządzenia, aby omówić, w jaki sposób można włączyć ich urządzenia za pomocą numeru PIN lub biometrycznego jako drugiego czynnika.

### <a name="can-admins-set-up-security-keys"></a>Czy administratorzy mogą skonfigurować klucze zabezpieczeń?

Pracujemy nad tą możliwością dla ogólnej dostępności (GA) tej funkcji.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Gdzie mogę znaleźć zgodne klucze zabezpieczeń?

[Klucze zabezpieczeń FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Co zrobić, jeśli zgubię klucz zabezpieczeń?

Klucze można usunąć z witryny Azure portal, przechodząc do strony **Informacje o zabezpieczeniach** i usuwając klucz zabezpieczeń.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nie mogę używać FIDO natychmiast po utworzeniu hybrydowego komputera przyłączanego do usługi Azure AD

Jeśli czysta instalacja hybrydowego komputera przyłączone do usługi Azure AD, po dołączeniu do domeny i ponownego uruchomienia procesu należy zalogować się przy użyciu hasła i czekać na zasady do synchronizacji przed będzie można użyć FIDO do logowania.

- Sprawdź swój bieżący `dsregcmd /status` stan, wpisując w oknie polecenia i sprawdź, czy zarówno *AzureAdJoined,* jak i *DomainJoined* są wyświetlane *TAK*.
- To opóźnienie jest znanym ograniczeniem dla urządzeń przyłączonych do domeny i nie jest specyficzne dla FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Nie mogę uzyskać logowania jednośmiętnego do zasobu sieciowego NTLM po zalogowaniu się za pomocą FIDO i uzyskać monit o poświadczenia

Upewnij się, że wystarczająco dużo kontrolerów domeny są załatane, aby odpowiedzieć na czas do obsługi żądania zasobów. Aby sprawdzić, czy na kontrolerze domeny z uruchomieniem `nltest /dsgetdc:contoso /keylist /kdc`tej funkcji jest kontroler domeny, przejrzyj dane wyjściowe pliku .

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o bez hasła](concept-authentication-passwordless.md)
