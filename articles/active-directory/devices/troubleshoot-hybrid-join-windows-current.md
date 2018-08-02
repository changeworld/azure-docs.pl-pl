---
title: Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia z systemem Windows 10 i Windows Server 2016 przyłączone do | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory przyłączone do urządzeń z systemem Windows 10 i Windows Server 2016.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 584a36e766e9e054af2ad1ce4f2105108fe80525
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414910"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia z systemem Windows 10 i Windows Server 2016 przyłączone do 

Ten artykuł ma zastosowanie do następujących klientów:

-   Windows 10
-   Windows Server 2016

Dla innych klientów Windows zobacz [niższego poziomu urządzenia przyłączone do hybrydowej Rozwiązywanie problemów z usługi Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md).

W tym artykule założono, że masz [urządzenia przyłączone do hybrydowej skonfigurowanego w usłudze Azure Active Directory](hybrid-azuread-join-plan.md) na potrzeby następujących scenariuszy:

- Dostęp warunkowy oparty na urządzeniu

- [Mobilny dostęp firmowy do ustawień](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Funkcja Windows Hello dla firm](../active-directory-azureadjoin-passport-deployment.md)


Ten dokument zawiera wskazówki dotyczące rozwiązywania problemów dotyczące rozwiązywania potencjalnych problemów. 


Dla systemu Windows 10 i Windows Server 2016, hybrydowe przyłączanie do usługi Azure Active Directory obsługuje systemu Windows 10 listopada 2015 r. Zaktualizuj i nowsze wersje. Zalecamy używanie Rocznicowej aktualizacji.

## <a name="step-1-retrieve-the-join-status"></a>Krok 1: Pobieranie stan dołączania 

**Aby pobrać stan dołączania:**

1. Otwórz wiersz polecenia jako administrator

2. Typ **dsregcmd parametru/status**



    +----------------------------------------------------------------------+
    | Stan urządzenia |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Nie DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 odcisk palca: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: TpmProtected dostawca usług kryptograficznych platformy firmy Microsoft: tak KeySignTest:: należy uruchomić z podwyższonym poziomem uprawnień do testowania.
                  Dostawcy tożsamości: login.windows.net identyfikator dzierżawy: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ == JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: KeySrvUrl 1.0: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs:enterpriseregistration.windows.net DomainJoined: tak nazwa_domeny: CONTOSO
    
    +----------------------------------------------------------------------+
    | Stan użytkownika |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizacje WamDefaultId: https://login.microsoft.com WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} AzureAdPrt (AzureAd): tak



## <a name="step-2-evaluate-the-join-status"></a>Krok 2. Ocena stan dołączania 

Przejrzyj poniższe pola i upewnij się, że mają one oczekiwane wartości:

### <a name="azureadjoined--yes"></a>AzureAdJoined: tak  

To pole wskazuje, czy urządzenie jest połączone z usługą Azure AD. Jeśli wartość jest **nie**, dołączania do usługi Azure AD nie została jeszcze ukończona. 

**Możliwe przyczyny:**

- Nie można uwierzytelnić komputera w celu utworzenia sprzężenia.

- W organizacji, która nie może być odnajdywane przez komputer znajduje się serwer proxy HTTP

- Komputer nie może nawiązać połączenia usługi Azure AD do uwierzytelniania lub Azure usługi rejestracji urządzeń do rejestracji

- Komputer może nie być w sieci wewnętrznej organizacji lub sieci VPN, za pomocą bezpośredniego linii wzroku do lokalnego kontrolera domeny usługi AD.

- Jeśli komputer ma modułu TPM, może być w nieprawidłowym stanie.

- Może być błędnej konfiguracji w usługach zanotowanej w dokumencie wcześniej, trzeba będzie ponownie zweryfikować prawo. Typowe przykłady to:

    - Serwerze federacyjnym nie ma włączonych punktów końcowych usługi WS-Trust

    - Serwerze federacyjnym nie zezwala na przychodzący uwierzytelnienia z komputerów w sieci przy użyciu zintegrowanego uwierzytelniania Windows.

    - Nie ma żadnego obiektu punktu połączenia usługi, który wskazuje nazwę zweryfikowanej domeny w usłudze Azure AD w lesie usługi AD, w którym komputer należy do

---

### <a name="domainjoined--yes"></a>DomainJoined: tak  

To pole wskazuje, czy urządzenie jest przyłączone do usługi Active Directory w środowisku lokalnym, czy nie. Jeśli wartość jest **nie**, urządzenie nie może wykonać dołączenie do hybrydowej usługi Azure AD.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: Brak  

To pole wskazuje, czy urządzenie jest zarejestrowane w usłudze Azure AD jako urządzenie osobiste (oznaczone jako *dołączone do miejsca pracy*). Ta wartość powinna być **nie** na komputerze przyłączonym do domeny, który jest przyłączone do hybrydowej usługi Azure AD. Jeśli wartość jest **tak**, dodano konto służbowe lub szkolne przed ukończeniem dołączenie do hybrydowej usługi Azure AD. W tym przypadku to konto jest ignorowana, gdy używana wersja Rocznicowej aktualizacji systemu Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Tak i AzureADPrt: tak
  
Te pola wskazuje, czy użytkownik został pomyślnie uwierzytelniony do usługi Azure AD podczas logowania się do urządzenia. Jeśli wartości są **nie**, może to być termin:

- Klucz magazynu zły (STK) w module TPM skojarzony z urządzeniem przy rejestracji (sprawdzanie KeySignTest uruchomionej z podwyższonym poziomem uprawnień).

- Identyfikatora logowania alternatywnej

- Nie można odnaleźć serwera HTTP Proxy

## <a name="next-steps"></a>Kolejne kroki

Masz pytania, zobacz [zarządzanie urządzeniami — często zadawane pytania](faq.md) 