---
title: Odnawianie certyfikatów dla użytkowników usługi Office 365 i usługi Azure AD | Dokumenty firmy Microsoft
description: Z tego artykułu dowiesz się, jak rozwiązać problemy z wiadomościami e-mail, które informują ich o odnawianiu certyfikatu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244834"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Odnawianie certyfikatów federacyjnych dla usługi Office 365 i usługi Azure Active Directory
## <a name="overview"></a>Omówienie
W przypadku pomyślnej federacji między usługą Azure Active Directory (Azure AD) a usługami federacyjnymi Active Directory (AD FS) certyfikaty używane przez usługi AD FS do podpisywania tokenów zabezpieczających w usłudze Azure AD powinny być zgodne z certyfikatami skonfigurowanym w usłudze Azure AD. Wszelkie niezgodności może prowadzić do złamania zaufania. Usługa Azure AD zapewnia, że te informacje są synchronizowane podczas wdrażania usług AD FS i serwera proxy aplikacji sieci Web (w celu uzyskania dostępu do ekstranetu).

Ten artykuł zawiera dodatkowe informacje do zarządzania certyfikatami podpisywania tokenu i zachować je w synchronizacji z usługą Azure AD, w następujących przypadkach:

* Nie wdrażasz serwera proxy aplikacji sieci Web i dlatego metadane federacji nie są dostępne w ekstranecie.
* Nie używasz domyślnej konfiguracji usług AD FS dla certyfikatów podpisywania tokenów.
* Używasz zewnętrznego dostawcy tożsamości.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Domyślna konfiguracja usług AD FS dla certyfikatów podpisywania tokenów
Certyfikaty podpisywania tokenów i odszyfrowywania tokenów są zazwyczaj certyfikatami z podpisem własnym i są dobre przez rok. Domyślnie usługi AD FS obejmują proces automatycznego odnawiania o nazwie **AutoCertificateRollover**. Jeśli używasz usług AD FS 2.0 lub nowszych, usługa Office 365 i usługa Azure AD automatycznie aktualizują certyfikat przed jego wygaśnięciem.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Powiadomienie o odnawianiu z centrum administracyjnego usługi Microsoft 365 lub wiadomość e-mail
> [!NOTE]
> Jeśli otrzymasz wiadomość e-mail lub powiadomienie z portalu z prośbą o odnowienie certyfikatu dla pakietu Office, zobacz [Zarządzanie zmianami w certyfikatach podpisywania tokenów,](#managecerts) aby sprawdzić, czy trzeba podjąć jakiekolwiek działania. Firma Microsoft jest świadoma możliwego problemu, który może prowadzić do wysyłania powiadomień o odnowieniu certyfikatu, nawet jeśli nie jest wymagana żadna akcja.
>
>

Usługa Azure AD próbuje monitorować metadane federacji i aktualizować certyfikaty podpisywania tokenów zgodnie z tym metadanymi. 30 dni przed wygaśnięciem certyfikatów podpisywania tokenów usługa Azure AD sprawdza, czy nowe certyfikaty są dostępne przez sondowanie metadanych federacji.

* Jeśli może pomyślnie sondować metadane federacji i pobrać nowe certyfikaty, nie jest wydawane użytkownikowi żadne powiadomienie e-mail lub ostrzeżenie w centrum administracyjnym usługi Microsoft 365.
* Jeśli nie można pobrać nowych certyfikatów podpisywania tokenu, ponieważ metadane federacji nie są osiągalne lub automatyczne przerzucanie certyfikatu nie jest włączone, usługa Azure AD wystawia powiadomienie e-mail i ostrzeżenie w centrum administracyjnym usługi Microsoft 365.

![Powiadomienie o portalu usługi Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Jeśli używasz usług AD FS, aby zapewnić ciągłość działania, sprawdź, czy na serwerach są zainstalowane następujące aktualizacje, aby nie występowały błędy uwierzytelniania znanych problemów. Eliminuje to znane problemy z serwerem proxy usług AD FS dla tego odnawiania i przyszłych okresów odnawiania:
>
> Server 2012 R2 — [zestawienie zbiorcze systemu Windows Server z maja 2014 r.](https://support.microsoft.com/kb/2955164)
>
> Serwer 2008 R2 i 2012 — [uwierzytelnianie za pomocą serwera proxy kończy się niepowodzeniem w systemie Windows Server 2012 lub Windows 2008 R2 z dodatku SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Sprawdź, czy certyfikaty wymagają aktualizacji<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1: Sprawdź stan AutoCertificateRollover
Na serwerze usług AD FS otwórz program PowerShell. Sprawdź, czy wartość AutoCertificateRollover jest ustawiona na Wartość True.

    Get-Adfsproperties

![AutocertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Jeśli używasz usług AD FS 2.0, najpierw uruchom program Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2: Upewnij się, że usługi AD FS i usługi Azure AD są zsynchronizowane
Na serwerze usług AD FS otwórz monit programu MSOnline PowerShell i połącz się z usługą Azure AD.

> [!NOTE]
> Polecenia CMDLET MSOL są częścią modułu MSOnline PowerShell.
> Moduł programu MSOnline PowerShell można pobrać bezpośrednio z galerii programu PowerShell.
> 
>

    Install-Module MSOnline

Połącz się z usługą Azure AD przy użyciu modułu programu MSOnline PowerShell.

    Import-Module MSOnline
    Connect-MsolService

Sprawdź certyfikaty skonfigurowane we właściwościach zaufania usług AD FS i Azure AD dla określonej domeny.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Jeśli odciski palców w obu wyjściach są zgodne, certyfikaty są zsynchronizowane z usługą Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3: Sprawdź, czy twój certyfikat wkrótce wygaśnie
W danych wyjściowych właściwości Get-MsolFederationProperty lub Get-AdfsCertificate sprawdź datę w obszarze "Nie po". Jeśli data jest mniej niż 30 dni, należy podjąć działania.

| AutocertificateRollover | Certyfikaty zsynchronizowane z usługą Azure AD | Metadane federacji są publicznie dostępne | Ważności | Akcja |
|:---:|:---:|:---:|:---:|:---:|
| Tak |Tak |Tak |- |Nie jest wymagane żadne działanie. Zobacz [Automatyczne odnawianie certyfikatu podpisywania tokenu](#autorenew). |
| Tak |Nie |- |Mniej niż 15 dni |Natychmiast odnów. Zobacz [ręczne odnawianie certyfikatu podpisywania tokenu](#manualrenew). |
| Nie |- |- |Mniej niż 30 dni |Natychmiast odnów. Zobacz [ręczne odnawianie certyfikatu podpisywania tokenu](#manualrenew). |

\[-] Nie ma znaczenia

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Automatyczne odnawianie certyfikatu podpisywania tokenu (zalecane)<a name="autorenew"></a>
Nie trzeba wykonywać żadnych ręcznych kroków, jeśli oba te elementy są spełnione:

* Wdrożono serwer proxy aplikacji sieci Web, który umożliwia dostęp do metadanych federacji z ekstranetu.
* Używasz domyślnej konfiguracji usług AD FS (autocertificaterollover jest włączona).

Sprawdź następujące kwestie, aby potwierdzić, że certyfikat może być automatycznie aktualizowany.

**1. Właściwość usługi AD FS AutoCertificateRollover musi być ustawiona na True.** Oznacza to, że usługi AD FS będą automatycznie generować nowe certyfikaty podpisywania tokenów i odszyfrowywania tokenów, zanim stare wygasną.

**2. Metadane federacji usług AD FS są publicznie dostępne.** Sprawdź, czy metadane federacji są publicznie dostępne, przechodząc do następującego adresu URL z komputera w publicznym Internecie (poza siecią firmową):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

gdzie `(your_FS_name)` jest zastępowany nazwą hosta usługi federacyjnej używaną przez organizację, taką jak fs.contoso.com.  Jeśli możesz pomyślnie zweryfikować oba te ustawienia, nie musisz nic robić.  

Przykład: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## <a name="renew-the-token-signing-certificate-manually"></a>Ręczne odnawianie certyfikatu podpisywania tokenu<a name="manualrenew"></a>
Certyfikaty podpisywania tokenu można odnowić ręcznie. Na przykład następujące scenariusze mogą działać lepiej w przypadku ręcznego odnawiania:

* Certyfikaty podpisywania tokenów nie są certyfikatami z podpisem własnym. Najczęstszą przyczyną jest to, że organizacja zarządza certyfikatami usług AD FS zarejestrowanymi z organizacyjnego urzędu certyfikacji.
* Zabezpieczenia sieci nie zezwalają na publiczne udostępnienie metadanych federacji.

W tych scenariuszach za każdym razem, gdy aktualizujesz certyfikaty podpisywania tokenu, należy również zaktualizować domenę usługi Office 365 przy użyciu polecenia Programu PowerShell, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1: Upewnij się, że usługi AD FS mają nowe certyfikaty podpisywania tokenów
**Konfiguracja nienawisowa**

Jeśli używasz domyślnej konfiguracji usług AD FS (gdzie **AutoCertificateRollover** jest ustawiona na **False),** prawdopodobnie używasz certyfikatów niestandardowych (nie podpisanych samodzielnie). Aby uzyskać więcej informacji na temat odnawiania certyfikatów podpisywania tokenów usług AD FS, zobacz [Wskazówki dla klientów nieużywanych certyfikatów z podpisem własnym usług AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadane federacji nie są publicznie dostępne**

Z drugiej strony, jeśli **AutoCertificateRollover** jest **ustawiona**na True , ale metadane federacji nie są publicznie dostępne, najpierw upewnij się, że nowe certyfikaty podpisywania tokenów zostały wygenerowane przez usługi AD FS. Upewnij się, że masz nowe certyfikaty podpisywania tokenów, wykonując następujące kroki:

1. Sprawdź, czy użytkownik jest zalogowany na podstawowym serwerze usług AD FS.
2. Sprawdź bieżące certyfikaty podpisywania w u źródła usług AD FS, otwierając okno polecenia programu PowerShell i uruchamiając następujące polecenie:

    PS C:\>Get-ADFSCertificate — podpisywanie tokenów typu certyfikatu

   > [!NOTE]
   > Jeśli używasz usług AD FS 2.0, należy najpierw uruchomić add-pssnapin Microsoft.Adfs.Powershell.
   >
   >
3. Spójrz na dane wyjściowe polecenia na wszystkich wymienionych certyfikatach. Jeśli usługi AD FS wygenerował nowy certyfikat, powinien zostać wyświetlony dwa certyfikaty w danych wyjściowych: jeden dla których **IsPrimary** wartość **true** i **NotAfter** data jest w ciągu 5 dni, a jeden dla których **IsPrimary** jest **False** i **NotAfter** jest o rok w przyszłości.
4. Jeśli widzisz tylko jeden certyfikat, a data **NotAfter** jest w ciągu 5 dni, musisz wygenerować nowy certyfikat.
5. Aby wygenerować nowy certyfikat, wykonaj następujące polecenie `PS C:\>Update-ADFSCertificate –CertificateType token-signing`w wierszu polecenia programu PowerShell: .
6. Sprawdź aktualizację, ponownie uruchamiając następujące\>polecenie: PS C: Get-ADFSCertificate –CertificateType token-signing

Dwa certyfikaty powinny być wymienione teraz, z których jeden ma **NotAfter** daty około jednego roku w przyszłości i dla których **IsPrimary** wartość jest **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Krok 2: Aktualizowanie nowych certyfikatów podpisywania tokenów dla zaufania usługi Office 365
Zaktualizuj usługę Office 365 o nowe certyfikaty podpisywania tokenów, które mają być używane dla zaufania, w następujący sposób.

1. Otwórz moduł usługi Microsoft Azure Active Directory dla programu Windows PowerShell.
2. Uruchom $cred=Poświadczenie. Gdy to polecenie cmdlet wyświetli monit o podanie poświadczeń, wpisz poświadczenia konta administratora usługi w chmurze.
3. Uruchom Connect-MsolService —Credential $cred. To polecenie cmdlet łączy cię z usługą w chmurze. Tworzenie kontekstu, który łączy się z usługą w chmurze jest wymagane przed uruchomieniem dowolnego z dodatkowych poleceń cmdlet zainstalowanych przez narzędzie.
4. Jeśli te polecenia są uruchamiane na komputerze, który nie jest podstawowym serwerem federacyjnym usług &lt;AD FS, &lt;uruchom serwer podstawowy&gt; Set-MSOLAdfscontext -Computer AD FS&gt;, gdzie serwer podstawowy usług AD FS jest wewnętrzną nazwą nazwy FQDN podstawowego serwera usług AD FS. To polecenie cmdlet tworzy kontekst, który łączy użytkownika z usługami AD FS.
5. Uruchom update-MSOLFederatedDomain &lt;–Domena&gt;DomainName . To polecenie cmdlet aktualizuje ustawienia z usług AD FS do usługi w chmurze i konfiguruje relację zaufania między nimi.

> [!NOTE]
> Jeśli potrzebujesz do obsługi wielu domen najwyższego poziomu, takich jak contoso.com i fabrikam.com, należy użyć **Przełącznik SupportMultipleDomain** z dowolnymi poleceniami cmdlet. Aby uzyskać więcej informacji, zobacz [Obsługa wielu domen najwyższego poziomu](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Naprawianie zaufania usługi Azure AD przy użyciu usługi Azure AD Connect<a name="connectrenew"></a>
Jeśli skonfigurowano farmę usług AD FS i zaufanie usługi Azure AD przy użyciu usługi Azure AD Connect, można użyć usługi Azure AD Connect do wykrywania, czy trzeba podjąć jakiekolwiek działania dla certyfikatów podpisywania tokenu. Jeśli chcesz odnowić certyfikaty, możesz użyć usługi Azure AD Connect, aby to zrobić.

Aby uzyskać więcej informacji, zobacz [Naprawianie zaufania](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Kroki aktualizacji certyfikatów usług AD FS i usługi Azure AD
Certyfikaty podpisywania tokenów są standardowymi certyfikatami X509, które są używane do bezpiecznego podpisywania wszystkich tokenów wystawianych przez serwer federacyjny. Certyfikaty odszyfrowywania tokenów są standardowymi certyfikatami X509, które są używane do odszyfrowywania wszelkich tokenów przychodzących. 

Domyślnie usługi AD FS są skonfigurowane do automatycznego generowania certyfikatów podpisywania tokenów i odszyfrowywania tokenów, zarówno w początkowej konfiguracji, jak i w momencie zbliżania się do daty wygaśnięcia certyfikatów.

Usługa Azure AD próbuje pobrać nowy certyfikat z metadanych usługi federacyjnej 30 dni przed wygaśnięciem bieżącego certyfikatu. W przypadku, gdy nowy certyfikat nie jest dostępny w tym czasie, usługa Azure AD będzie nadal monitorować metadane w regularnych odstępach czasu. Gdy tylko nowy certyfikat będzie dostępny w metadanych, ustawienia federacji domeny są aktualizowane o nowe informacje o certyfikacie. Można użyć, `Get-MsolDomainFederationSettings` aby sprawdzić, czy widzisz nowy certyfikat w NextSigningCertificate / SigningCertificate.

Aby uzyskać więcej informacji na temat certyfikatów podpisywania tokenów w systemach AD FS, zobacz [Uzyskiwanie i konfigurowanie certyfikatów podpisywania tokenów i odszyfrowywania tokenów dla usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
