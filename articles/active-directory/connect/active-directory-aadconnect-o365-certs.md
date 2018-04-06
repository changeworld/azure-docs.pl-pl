---
title: Certyfikat odnowienia dla użytkowników usługi Office 365 i Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano użytkowników usługi Office 365, jak rozwiązać problemy z wiadomości e-mail, które powiadamiają o odnawiania certyfikatu.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: billmath
ms.openlocfilehash: f0435f1c5aae9381c76441b1233a47799af94768
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Odnawianie certyfikatów Federacji dla usługi Office 365 i Azure Active Directory
## <a name="overview"></a>Przegląd
Do pomyślnego federacji między Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS) certyfikaty używane przez usługi AD FS do podpisywania tokenów zabezpieczających do usługi Azure AD powinna odpowiadać co to jest skonfigurowany w usłudze Azure AD. Wszelkie niezgodność może prowadzić do zerwaniem relacji zaufania. Usługi Azure AD zapewnia, że te informacje są utrzymywane w synchronizacji podczas wdrażania usług AD FS i serwera Proxy aplikacji sieci Web (Aby uzyskać dostęp przez ekstranet).

Ten artykuł zawiera dodatkowe informacje na temat zarządzania token certyfikaty podpisywania i zachowania ich synchronizacji z usługą Azure AD w następujących przypadkach:

* Nie wymaga wdrażania serwera Proxy aplikacji sieci Web, a w związku z tym metadanych federacji nie jest dostępna w ekstranetu.
* Nie używasz domyślnej konfiguracji usług AD FS certyfikaty podpisywania tokenu.
* Czy przy użyciu dostawcy tożsamości innych firm.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Domyślna konfiguracja usług AD FS dla certyfikatów podpisywania tokenu
Podpisywania tokenu i odszyfrowywania certyfikaty tokenu są zwykle samodzielnie podpisanych certyfikatów i są dobrym przez jeden rok. Domyślnie usługi AD FS obejmują proces automatycznego odnawiania o nazwie **AutoCertificateRollover**. Jeśli korzystasz z usług AD FS 2.0 lub nowszej, usługi Office 365 i Azure AD automatycznie aktualizuje certyfikatu przed jego wygaśnięciem.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Powiadomienie odnawiania z portalu usługi Office 365 lub wiadomości e-mail
> [!NOTE]
> W przypadku otrzymania wiadomości e-mail lub portalu powiadomienie monitem o odnawianie certyfikatu dla pakietu Office, zobacz [Zarządzanie zmianami w certyfikaty podpisywania tokenu](#managecerts) do sprawdzenia, czy trzeba wykonywać żadnych czynności. Firma Microsoft dokłada świadomość możliwy problem, który może prowadzić do powiadomienia dotyczące odnawiania certyfikatów, które są wysyłane, nawet wtedy, gdy nie jest wymagana żadna akcja.
>
>

Usługi Azure AD próbuje metadanych Federacji monitorowanie i aktualizowanie certyfikatów podpisywania wskazywany przez te metadane tokenu. 30 dni przed wygaśnięciem tokenu podpisywania certyfikatów, usługi Azure AD sprawdza, czy nowe certyfikaty są dostępne przez sondowanie metadanych federacji.

* Czy można pomyślnie sondowania metadanych Federacji i pobrać nowe certyfikaty, bez powiadomienia e-mail i ostrzeżenia w portalu usługi Office 365 jest wystawiony dla użytkownika.
* Jeśli nie może pobrać nowych certyfikatów podpisywania tokenu, albo ponieważ metadanych federacji nie jest dostępny lub nie włączono automatycznego przerzucania, usługa Azure AD wystawia wiadomość e-mail z powiadomieniem i ostrzeżenie w portalu usługi Office 365.

![Powiadomieniu portalu usługi Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Jeśli używasz usług AD FS, aby zapewnić ciągłość, sprawdź, czy serwery powinni mieć następujące aktualizacje, dzięki czemu nie występują błędy uwierzytelniania pod kątem znanych problemów. Zmniejsza to zagrożenie wynikające znanych problemów dotyczących serwera proxy usług AD FS dla tego odnawiania i okresy przyszłych odnawiania:
>
> Server 2012 R2 — [systemu Windows Server zbiorczy maja 2014](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 i 2012 — [niepowodzenia uwierzytelniania za pośrednictwem serwera proxy w systemie Windows Server 2012 lub Windows 2008 R2 z dodatkiem SP1](http://support.microsoft.com/kb/3094446)
>
>

## Sprawdź, czy certyfikaty muszą zostać zaktualizowane <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1: Sprawdź stan AutoCertificateRollover
Na serwerze usług AD FS Otwórz program PowerShell. Sprawdź, czy wartość AutoCertificateRollover jest równa True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Jeśli korzystasz z usług AD FS 2.0, najpierw uruchom Microsoft.Adfs.Powershell Dodaj-Pssnapin.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2: Sprawdź, czy usługi AD FS a usługą Azure AD są zsynchronizowane
Na serwerze usług AD FS Otwórz wiersz programu Azure AD PowerShell i łączenie z usługą Azure AD.

> [!NOTE]
> Możesz pobrać program Azure AD PowerShell [tutaj](https://technet.microsoft.com/library/jj151815.aspx).
>
>

    Connect-MsolService

Sprawdź certyfikaty, skonfigurowane w usłudze AD FS a usługą Azure AD zaufania właściwości dla określonej domeny.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Jeśli odcisków palca w obu dane wyjściowe są zgodne, certyfikaty są zsynchronizowane z usługą Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3: Sprawdź, czy certyfikat wygaśnie
W danych wyjściowych Get MsolFederationProperty lub Get-AdfsCertificate Sprawdź datę w obszarze "Nie po." Jeśli data jest mniej niż 30 dni nieobecności, należy podjąć działania.

| AutoCertificateRollover | Certyfikaty w synchronizacji z usługą Azure AD | Jest dostępny publicznie element metadanych Federacji | Ważność | Akcja |
|:---:|:---:|:---:|:---:|:---:|
| Yes |Yes |Yes |- |Nie jest wymagana żadna akcja. Zobacz [podpisywania tokenu odnawiania certyfikatów automatycznie](#autorenew). |
| Yes |Nie |- |Mniej niż 15 dni |Odnów natychmiast. Zobacz [podpisywania tokenu odnawiania certyfikatu ręcznie](#manualrenew). |
| Nie |- |- |Mniej niż 30 dni |Odnów natychmiast. Zobacz [podpisywania tokenu odnawiania certyfikatu ręcznie](#manualrenew). |

\[-] Nie ma znaczenia.

## Odnów certyfikat podpisywania automatycznie tokenu (zalecane) <a name="autorenew"></a>
Nie trzeba wykonywać żadnych czynności ręcznej, jeśli są spełnione oba poniższe:

* Należy wdrożyć serwer Proxy aplikacji sieci Web, które można włączyć dostęp do metadanych federacji z ekstranetu.
* Używania domyślnej konfiguracji usług AD FS (AutoCertificateRollover jest włączona).

Sprawdź następujące polecenie, aby potwierdzić, że certyfikat mogą być automatycznie aktualizowane.

**1. Właściwość usług AD FS AutoCertificateRollover musi mieć ustawioną wartość True.** To wskazuje, że usługi AD FS automatycznie generuje nowy token podpisywania i odszyfrowywania tokenów certyfikatów przed stary tych wygaśnie.

**2. Metadane federacji usług AD FS jest dostępny publicznie.** Sprawdź, czy z metadanych federacji jest dostępny publicznie, przechodząc pod następujący adres URL z komputera w publicznej sieci internet (się z sieci firmowej):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

gdzie `(your_FS_name) `jest zastępowany nazwę hosta usługi federacyjnej, organizacja używa, takich jak fs.contoso.com.  Jeśli możesz zweryfikować oba te ustawienia pomyślnie, nie trzeba nic robić.  

Przykład: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Odnów certyfikat podpisywania ręcznie tokenu <a name="manualrenew"></a>
Można odnowić certyfikatów podpisywania ręcznie tokenu. Na przykład poniższe scenariusze mogą działać lepiej odnowienia ręczne:

* Certyfikaty podpisywania tokenu to certyfikaty nie podpisem. Najczęstszą przyczyną tego jest, że Twoja organizacja zarządza certyfikatów usługi AD FS zarejestrowane od urzędu certyfikacji w organizacji.
* Zabezpieczenia sieci nie zezwalają na metadanych Federacji, które mają być publicznie dostępne.

W tych scenariuszach za każdym razem, gdy certyfikaty podpisywania tokenu należy również zaktualizować domeną usługi Office 365 za pomocą polecenia programu PowerShell, MsolFederatedDomain aktualizacji.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1: Upewnij się, że usługi AD FS ma nowych certyfikatów podpisywania tokenu
**Inne niż domyślne konfiguracji**

Jeśli używasz niedomyślna Konfiguracja usług AD FS (gdzie **AutoCertificateRollover** ustawiono **False**), są prawdopodobnie używa niestandardowych certyfikatów (nie z podpisem własnym). Aby uzyskać więcej informacji o sposobie odnawiania certyfikatów podpisywania tokenu usług AD FS, zobacz [wskazówki dotyczące klientów nie korzystających z usług AD FS certyfikaty z podpisem własnym](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadane federacji nie jest publicznie dostępna**

Z drugiej strony Jeśli **AutoCertificateRollover** ma ustawioną wartość **True**, ale z metadanych federacji nie jest dostępny publicznie, najpierw upewnij się, że nowe certyfikaty podpisywania tokenu zostały wygenerowane przez usługi AD FS. Upewnij się, że masz nowy token certyfikaty podpisywania, wykonując następujące czynności:

1. Sprawdź, czy użytkownik jest zalogowany do podstawowego serwera usług AD FS.
2. Sprawdź bieżący podpisywania certyfikatów w usługach AD FS, otwierając okno poleceń programu PowerShell i uruchom następujące polecenie:

    PS C:\>podpisywania tokenu Get-ADFSCertificate — CertificateType

   > [!NOTE]
   > Jeśli korzystasz z usług AD FS 2.0, należy najpierw uruchomić Microsoft.Adfs.Powershell Dodaj-Pssnapin.
   >
   >
3. Sprawdź dane wyjściowe polecenia na wszystkie certyfikaty na liście. Jeśli usługi AD FS wygenerował nowy certyfikat, powinny pojawić się dwa certyfikaty w danych wyjściowych: jeden do którego **IsPrimary** wartość jest **True** i **nie później niż** przypada w ciągu 5 dni i na które **IsPrimary** jest **False** i **nie później niż** dotyczy roku w przyszłości.
4. Jeśli zostanie wyświetlony tylko jeden certyfikat i **nie później niż** przypada w ciągu 5 dni, należy wygenerować nowy certyfikat.
5. Aby wygenerować nowy certyfikat, uruchom następujące polecenie w wierszu polecenia programu PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Sprawdź aktualizację, uruchamiając następujące polecenie: PS C:\>podpisywania tokenu Get-ADFSCertificate — CertificateType

Powinien być teraz wyświetlany dwa certyfikaty, z których jedna ma **nie później niż** datę w przyszłości około jednego roku i dla których **IsPrimary** wartość jest **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Krok 2: Zaktualizuj nowy token podpisywania certyfikatów dla zaufania usługi Office 365
Aktualizacja usługi Office 365 przy użyciu nowego tokenu podpisywania certyfikatów do zastosowania w przypadku zaufania, w następujący sposób.

1. Otwórz moduł usługi Active Directory platformy Microsoft Azure dla środowiska Windows PowerShell.
2. Run $cred=Get-Credential. Gdy to polecenie cmdlet wyświetla monit o podanie poświadczeń, wpisz poświadczenia konta administratora usługi chmury.
3. Uruchom Connect MsolService — $cred poświadczeń. To polecenie cmdlet łączy do usługi w chmurze. Tworzenie kontekstu, który łączy do usługi w chmurze jest wymagana przed uruchomieniem dowolnych poleceniach cmdlet dodatkowe zainstalowane przez narzędzie.
4. Jeśli używasz tych poleceń na komputerze, który nie jest serwerem podstawowym usług AD FS, uruchom zestaw MSOLAdfscontext-komputer &lt;podstawowego serwera usług AD FS&gt;, gdzie &lt;podstawowego serwera usług AD FS&gt; jest wewnętrzna nazwa FQDN Nazwa podstawowego serwera usług AD FS. To polecenie cmdlet tworzy kontekstu, który łączy użytkownika z usług AD FS.
5. Uruchom aktualizacji MSOLFederatedDomain — DomainName &lt;domeny&gt;. To polecenie cmdlet zaktualizowanie ustawień z usług AD FS do usługi w chmurze i konfiguruje relację zaufania między nimi.

> [!NOTE]
> Jeśli zachodzi konieczność obsługi wielu domen najwyższego poziomu, takich jak contoso.com i fabrikam.com, należy użyć **SupportMultipleDomain** przełącznik z dowolnego polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [Obsługa wielu domen poziom górnej](active-directory-aadconnect-multiple-domains.md).
>


## Napraw zaufania usługi Azure AD za pomocą usługi Azure AD Connect <a name="connectrenew"></a>
Jeśli farma usług AD FS, a relacja zaufania usługi Azure AD są skonfigurowane za pomocą usługi Azure AD Connect, można użyć usługi Azure AD Connect do wykrywania, jeśli musisz podejmować żadnych działań dla Twojego certyfikaty podpisywania tokenu. Jeśli chcesz odnowić certyfikaty, można użyć Azure AD Connect, aby to zrobić.

Aby uzyskać więcej informacji, zobacz [naprawiania zaufania](active-directory-aadconnect-federation-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Usługi AD FS a usługą Azure AD certyfikatu kroki aktualizacji
Certyfikaty podpisywania tokenu są standardowe X509 certyfikaty, które są używane do bezpiecznego podpisywania wszystkich tokenów, które serwer federacyjny. Certyfikaty odszyfrowywania tokenów są standardowe X509 certyfikaty, które są używane do odszyfrowywania wszystkich tokenów przychodzących. 

Domyślnie usługi AD FS jest skonfigurowany do generowania podpisywania tokenu i odszyfrowywania tokenów certyfikatów automatycznie, podczas konfiguracji początkowej oraz gdy zbliża się ich daty wygaśnięcia certyfikatów.

Usługi Azure AD próbuje pobrać nowy certyfikat z metadanych usługi federacyjnej 30 dni przed wygaśnięciem bieżącego certyfikatu. W przypadku, gdy nowy certyfikat nie jest dostępny w tym czasie, usługi Azure AD będzie monitorować metadanych w regularnych odstępach czasu codziennie. Jak nowy certyfikat jest dostępny w metadanych, ustawienia Federacji dla domeny są aktualizowane przy użyciu nowych informacji certyfikatu. Można użyć `Get-MsolDomainFederationSettings` można zweryfikować, jeśli zostanie wyświetlony nowy certyfikat w NextSigningCertificate / SigningCertificate.

Aby uzyskać więcej informacji na temat podpisywania tokenu Zobacz certyfikaty w usługach AD FS [Uzyskaj i skonfigurować podpisywania tokenu i tokenu odszyfrowywania certyfikaty dla usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
