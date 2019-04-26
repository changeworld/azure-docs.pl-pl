---
title: Certyfikat odnowienia dla użytkowników usługi Office 365 i Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule omówiono dla użytkowników usługi Office 365 Rozwiązywanie problemów z wiadomości e-mail, które powiadamiają o odnowienie certyfikatu.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98a1aabef2de505e66b2127226b9e89cd791e20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60244834"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Odnawianie certyfikatów Federacji dla usługi Office 365 i Azure Active Directory
## <a name="overview"></a>Omówienie
Pomyślne federacji między usługi Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS) certyfikaty używane przez usługi AD FS do podpisywania tokenów zabezpieczających do usługi Azure AD powinny być zgodne portami skonfigurowanymi w usłudze Azure AD. Wszelkie niezgodności może prowadzić do zerwanie relacji zaufania. Usługa Azure AD zapewnia, że te informacje są przechowywane w synchronizacji podczas wdrażania usług AD FS i serwera Proxy aplikacji sieci Web (Aby uzyskać dostęp do ekstranetu).

Ten artykuł zawiera dodatkowe informacje, aby zarządzać certyfikaty podpisywania tokenu i zachować synchronizację z usługą Azure AD w następujących przypadkach:

* Nie wymaga wdrażania serwera Proxy aplikacji sieci Web i w związku z tym metadanych federacji nie jest dostępny w sieci ekstranet.
* Nie używasz domyślnej konfiguracji usług AD FS certyfikaty podpisywania tokenu.
* Użytkownik korzysta z dostawcy tożsamości innych firm.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Domyślna konfiguracja usług AD FS dla certyfikatów podpisywania tokenu
Podpisywania tokenu i odszyfrowywania certyfikaty tokenu są zazwyczaj podpisem certyfikaty i są dobre przez jeden rok. Domyślnie usług AD FS obejmuje proces automatycznego odnawiania, o nazwie **AutoCertificateRollover**. Jeśli używasz usług AD FS 2.0 lub nowszej, usługi Office 365 i Azure AD automatycznie aktualizuje certyfikatu przed jego wygaśnięciem.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Odnawianie powiadomienie z Centrum administracyjnego usługi Microsoft 365 lub wiadomości e-mail
> [!NOTE]
> Jeśli otrzymasz wiadomość e-mail lub powiadomienie portalu prośbą odnawianie certyfikatu dla pakietu Office, zobacz [Zarządzanie zmianami w certyfikaty podpisywania tokenu](#managecerts) do sprawdzenia, jeśli musisz podejmować żadnych działań. Firma Microsoft zapoznała się z potencjalny problem, który może prowadzić do powiadomienia dotyczące odnawiania certyfikatów, które są wysyłane, nawet wtedy, gdy jest wymagana żadna akcja.
>
>

Usługa Azure AD próbuje monitorowanie metadanych Federacji i aktualizowanie certyfikatów podpisywania, wskazane przez te metadane tokenu. 30 dni przed wygaśnięciem certyfikaty podpisywania tokenu usługi Azure AD sprawdza, jeśli nowe certyfikaty są dostępne przez sondowanie metadanych federacji.

* Jeśli go pomyślnie wykonać sondowanie metadanych Federacji i pobrać nowe certyfikaty, bez powiadomienia e-mail i ostrzeżenia w Centrum administracyjnym usługi Microsoft 365 wystawionego dla użytkownika.
* Jeśli nie może pobrać nowe certyfikaty podpisywania tokenu, albo ponieważ metadanych federacji nie jest dostępny lub przerzucanie certyfikatów automatyczne nie jest włączone, usługa Azure AD wystawia wiadomość e-mail z powiadomieniem i ostrzeżenia w Centrum administracyjnym usługi Microsoft 365.

![Powiadomienia portalu usługi Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Jeśli używasz usług AD FS w celu zapewnienia ciągłości biznesowej, sprawdź, czy serwery mają następujące aktualizacje, tak aby nie występują błędy uwierzytelniania pod kątem znanych problemów. Zmniejsza to znane problemy serwera proxy usług AD FS dla tego odnowienia i okresy przyszłych odnawiania:
>
> Server 2012 R2 — [systemu Windows Server zbiorczy maj 2014](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 i 2012 — [uwierzytelniania za pośrednictwem serwera proxy nie powiodło się w systemie Windows Server 2012 lub Windows 2008 R2 z dodatkiem SP1](https://support.microsoft.com/kb/3094446)
>
>

## Sprawdź certyfikaty muszą zostać zaktualizowane <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1: Sprawdź stan AutoCertificateRollover
Na serwerze usług AD FS Otwórz program PowerShell. Upewnij się, że wartość AutoCertificateRollover jest ustawiona na wartość True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Jeśli używasz usług AD FS 2.0, należy najpierw uruchomić Microsoft.Adfs.Powershell Dodaj-Pssnapin.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2: Upewnij się, że usług AD FS a usługą Azure AD są zsynchronizowane
Na serwerze usług AD FS Otwórz wiersz polecenia programu MSOnline PowerShell i łączenie z usługą Azure AD.

> [!NOTE]
> Polecenia cmdlet MSOL są częścią tego modułu MSOnline PowerShell.
> Modułu MSOnline PowerShell można pobrać bezpośrednio z galerii programu PowerShell.
> 
>

    Install-Module MSOnline

Łączenie z usługą Azure AD przy użyciu modułu PowerShell MSOnline.

    Import-Module MSOnline
    Connect-MsolService

Sprawdź skonfigurowanych w AD FS i Azure AD właściwości dla określonej domeny zaufania certyfikatów.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Jeśli odcisków palca w obu dane wyjściowe są zgodne, certyfikaty są zsynchronizowane z usługą Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3: Sprawdź, jeśli Twój certyfikat wkrótce wygaśnie
W danych wyjściowych Get MsolFederationProperty lub Get-AdfsCertificate Sprawdź datę w obszarze "Nie po." Jeśli data jest natychmiast mniej niż 30 dni, należy podjąć działania.

| AutoCertificateRollover | Certyfikaty są zsynchronizowane z usługą Azure AD | Metadanych federacji jest ogólnie dostępny | Ważność | Akcja |
|:---:|:---:|:---:|:---:|:---:|
| Yes |Yes |Yes |- |Nie jest wymagane żadne działanie. Zobacz [odnawiania tokenu podpisywania certyfikatów automatycznie](#autorenew). |
| Yes |Nie |- |Mniej niż 15 dni |Odnów natychmiast. Zobacz [podpisywania tokenu Odnów certyfikat ręcznie](#manualrenew). |
| Nie |- |- |Mniej niż 30 dni |Odnów natychmiast. Zobacz [podpisywania tokenu Odnów certyfikat ręcznie](#manualrenew). |

\[-] Nie ma znaczenia.

## Odnów certyfikat podpisywania automatycznie tokenu (zalecane) <a name="autorenew"></a>
Nie trzeba wykonać wszelkie ręcznie wykonane czynności, jeśli są spełnione oba z następujących czynności:

* Możesz wdrożyć serwer Proxy aplikacji sieci Web, które można włączyć dostęp do metadanych federacji z ekstranetu.
* Używasz domyślnej konfiguracji usług AD FS (AutoCertificateRollover jest włączona).

Sprawdź następujące polecenie, aby potwierdzić, że certyfikat zostaną automatycznie zaktualizowane.

**1. Właściwość usług AD FS AutoCertificateRollover należy ustawić na wartość True.** Oznacza to, że usług AD FS automatycznie generuje nowy podpisywania tokenu i odszyfrowywania tokenów certyfikaty, zanim stare te wygaśnie.

**2. Metadanych federacji usług AD FS jest publicznie dostępny.** Sprawdź, czy metadane Federacji dostępny publicznie, przechodząc do następującego adresu URL z komputera w publicznej sieci internet (zniżki w stosunku do sieci firmowej):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

gdzie `(your_FS_name)` jest zastępowany nazwą hosta usługi federacyjnej, organizacja używa przykład fs.contoso.com.  Jeśli jest to możliwe sprawdzić, oba te ustawienia pomyślnie, nie trzeba nic robić.  

Przykład: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Odnów certyfikat podpisywania ręcznie tokenu <a name="manualrenew"></a>
Istnieje możliwość odnowienia tokenu podpisywania certyfikatów ręcznie. Na przykład następujące scenariusze może działać lepiej do ręcznego odnowienia:

* Token podpisywania certyfikatów są nie podpisem własnym. Najczęstszą przyczyną tego jest, że Twoja organizacja zarządza certyfikatów usług AD FS, zarejestrowanych od urzędu certyfikacji w organizacji.
* Zabezpieczenia sieci nie zezwala na publicznie dostępnych metadanych federacji.

W tych scenariuszach za każdym razem, gdy aktualizacja tokenu podpisywania certyfikatów, należy również zaktualizować domenę usługi Office 365 za pomocą polecenia programu PowerShell Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1: Upewnij się, że usług AD FS ma nowe certyfikaty podpisywania tokenu
**Inne niż domyślne konfiguracji**

Jeśli używasz niedomyślna Konfiguracja usług AD FS (gdzie **AutoCertificateRollover** ustawiono **False**), prawdopodobnie używasz niestandardowe certyfikaty (nie z podpisem własnym). Aby uzyskać więcej informacji o sposobie odnawiania certyfikatów podpisywania tokenu usług AD FS, zobacz [wskazówki dla klientów nie korzystających z usług AD FS certyfikaty z podpisem własnym](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Nie jest publicznie dostępnych metadanych Federacji**

Z drugiej strony Jeśli **AutoCertificateRollover** jest ustawiona na **True**, ale nie jest publicznie dostępnych metadanych Federacji, najpierw upewnij się, że nowe certyfikaty podpisywania tokenu zostały wygenerowane przez usługi AD FS. Upewnij się, że masz nowy token podpisywania certyfikatów, wykonując następujące czynności:

1. Sprawdź, czy użytkownik jest zalogowany do podstawowego serwera usług AD FS.
2. Sprawdź bieżące certyfikaty podpisywania w usługach AD FS, otwierając okno poleceń programu PowerShell i uruchom następujące polecenie:

    PS C:\>Get-ADFSCertificate — CertificateType podpisywania tokenu

   > [!NOTE]
   > Jeśli używasz usług AD FS 2.0, należy uruchomić Microsoft.Adfs.Powershell Dodaj-Pssnapin najpierw.
   >
   >
3. Spójrz na dane wyjściowe polecenia na wszystkie certyfikaty na liście. Usług AD FS wygenerowany nowy certyfikat powinien zostać wyświetlony dwóch certyfikatów w danych wyjściowych: jeden dla którego **IsPrimary** wartość jest **True** i **nie później niż** przypada w ciągu 5 dni i na które **IsPrimary** jest **False** i **NotAfter** to około roku w przyszłości.
4. Jeśli zostanie wyświetlony tylko jeden certyfikat i **NotAfter** przypada w ciągu 5 dni, należy wygenerować nowy certyfikat.
5. Aby wygenerować nowy certyfikat, wykonaj następujące polecenie w wierszu polecenia programu PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Sprawdź aktualizację, uruchamiając następujące polecenie: PS C:\>Get-ADFSCertificate — CertificateType podpisywania tokenu

Teraz powinny zostać wyświetlone dwa certyfikaty, z których jedna ma **NotAfter** daty w przybliżeniu jednego roku i dla których **IsPrimary** wartość **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Krok 2: Aktualizowanie nowego tokenu podpisywania certyfikatów dla zaufania usługi Office 365
Zaktualizuj usługi Office 365 przy użyciu nowego tokenu podpisywania certyfikatów do użytku z relacji zaufania w następujący sposób.

1. Otwórz moduł usługi Active Directory platformy Microsoft Azure dla środowiska Windows PowerShell.
2. Run $cred=Get-Credential. Gdy to polecenie cmdlet wyświetli monit o podanie poświadczeń, wpisz poświadczenia konta administratora usługi chmury.
3. Uruchom Connect-MsolService — Credential $cred. To polecenie cmdlet łączy Cię do usługi w chmurze. Tworzenie kontekstu, która łączy się z usługą w chmurze z jest wymagane przed uruchomieniem dowolnych dodatkowych poleceń cmdlet instalowane przez narzędzie.
4. Jeśli te polecenia są uruchomione na komputerze, który nie jest serwerem podstawowym usług AD FS, uruchom zestaw MSOLAdfscontext-komputer &lt;serwera podstawowego usług AD FS&gt;, gdzie &lt;serwera podstawowego usług AD FS&gt; to wewnętrzna nazwa FQDN Nazwa serwera podstawowego usług AD FS. To polecenie cmdlet tworzy kontekst, który łączy użytkownika z usług AD FS.
5. Uruchom Update-MSOLFederatedDomain-DomainName &lt;domeny&gt;. To polecenie cmdlet zaktualizowanie ustawień z usług AD FS w usłudze w chmurze i konfiguruje relację zaufania między nimi.

> [!NOTE]
> Jeśli wymagana jest obsługa wielu domen najwyższego poziomu, np. contoso.com i fabrikam.com, należy użyć **SupportMultipleDomain** przełącznika za pomocą polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [Obsługa wielu domen poziom górnej](how-to-connect-install-multiple-domains.md).
>


## Naprawa relacji zaufania usługi Azure AD za pomocą usługi Azure AD Connect <a name="connectrenew"></a>
Jeśli Twoje farmy usług AD FS i zaufania usługi Azure AD są skonfigurowane przy użyciu usługi Azure AD Connect, można użyć usługi Azure AD Connect do wykrywania, jeśli musisz podejmować żadnych działań dla Twojego tokenu podpisywania certyfikatów. Jeśli musisz odnowić certyfikaty, można użyć usługi Azure AD Connect, aby to zrobić.

Aby uzyskać więcej informacji, zobacz [naprawiania zaufania](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Usługi AD FS a usługą Azure AD certyfikat kroki aktualizacji
Token podpisywania certyfikatów są standardowe X509 certyfikaty, które są używane do podpisywania bezpiecznie serwer federacyjny wystawia tokeny wszystkie. Certyfikaty odszyfrowywania tokenów są standardowe X509 certyfikaty, które są używane do odszyfrowywania wszystkich tokenów przychodzących. 

Domyślnie usług AD FS jest skonfigurowany do generowania podpisywania tokenu i odszyfrowywania tokenów certyfikaty automatycznie, podczas konfiguracji początkowej i podczas certyfikaty są kontaktowania się z datą wygaśnięcia.

Usługa Azure AD próbuje pobrać nowy certyfikat z metadanych usługi federacyjnej 30 dni przed wygaśnięciem bieżącego certyfikatu. W przypadku, gdy nowy certyfikat nie jest dostępna w tym czasie, usługi Azure AD będzie nadal monitorować metadanych w regularnych odstępach czasu dzienny. Jak najszybciej nowego certyfikatu jest dostępna w metadanych, ustawienia Federacji domeny są aktualizowane przy użyciu nowych informacji o certyfikacie. Możesz użyć `Get-MsolDomainFederationSettings` Aby sprawdzić, jeśli zostanie wyświetlony nowy certyfikat w NextSigningCertificate / SigningCertificate.

Aby uzyskać więcej informacji na temat podpisywania tokenu usług certyfikatów w usługach AD FS zobacz [uzyskiwanie i konfigurowanie podpisywania i certyfikaty odszyfrowywania tokenu usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
