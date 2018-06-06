---
title: Rozwiązywanie problemów z hybrydowe usługi Azure Active Directory systemu Windows 10 i Windows Server 2016 urządzeniach przyłączonych do | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z hybrydowe usługi Azure Active Directory przyłączone do urządzeń z systemem Windows 10 i Windows Server 2016.
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
ms.openlocfilehash: 40303d72f4facc6cd8c762f2542c48df3e370c0a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713732"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Rozwiązywanie problemów z hybrydowe usługi Azure Active Directory przyłączone do urządzeń z systemem Windows 10 i Windows Server 2016 

Ten artykuł ma zastosowanie do następujących klientów:

-   Windows 10
-   Windows Server 2016

Dla innych klientów z systemem Windows, temacie [niższego poziomu urządzeniach przyłączonych do rozwiązywania problemów hybrydowe usługi Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-legacy.md).

W tym artykule przyjęto założenie, że masz [urządzeniach przyłączonych do skonfigurowanego hybrydowe usługi Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) do obsługi następujących scenariuszy:

- Dostępu warunkowego opartego na urządzeniu

- [Enterprise roaming ustawień](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello dla firm](active-directory-azureadjoin-passport-deployment.md)


Ten dokument zawiera wskazówki dotyczące rozwiązywania problemów na jak rozwiązać potencjalne problemy. 


Dla systemu Windows 10 i Windows Server 2016, hybrydowe usługi Azure Active Directory join obsługuje systemu Windows 10 listopada 2015 aktualizacji i powyżej. Firma Microsoft zaleca używanie aktualizacji rocznicy.

## <a name="step-1-retrieve-the-join-status"></a>Krok 1: Pobranie stanu sprzężenia 

**Aby pobrać stan sprzężenia:**

1. Otwórz wiersz polecenia jako administrator

2. Typ **dsregcmd/status**



    +----------------------------------------------------------------------+
    | Stan urządzenia |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Nie DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 odcisk palca: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: TpmProtected dostawca usług kryptograficznych platformy firmy Microsoft: tak KeySignTest:: należy uruchomić z podwyższonym poziomem uprawnień do testowania.
                  IDP: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ == JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs:enterpriseregistration.windows.net DomainJoined: nazwadomeny tak: CONTOSO
    
    +----------------------------------------------------------------------+
    | Stan użytkownika |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizacje WamDefaultId: https://login.microsoft.com WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} AzureAdPrt (AzureAd): tak



## <a name="step-2-evaluate-the-join-status"></a>Krok 2: Ocena stanu sprzężenia 

Przejrzyj następujące pola i upewnij się, że mają one oczekiwanych wartości:

### <a name="azureadjoined--yes"></a>AzureAdJoined: tak  

To pole wskazuje, czy urządzenie jest połączone z usługą Azure AD. Jeśli wartość jest **nr**, sprzężenia do usługi Azure AD nie została jeszcze ukończona. 

**Możliwe przyczyny:**

- Nie można przeprowadzić uwierzytelniania komputera w celu utworzenia sprzężenia.

- W organizacji, które nie mogą zostać odnalezione przez komputer znajduje się serwer proxy HTTP

- Komputer można nawiązać połączenia z usługą Azure AD do uwierzytelniania lub Azure usługi rejestracji urządzeń do rejestracji

- Komputer może nie być w wewnętrznej sieci firmy lub w sieci VPN, z bezpośredniej procesów z wiersza do lokalnego kontrolera domeny usługi AD.

- Jeśli komputer ma modułu TPM, może być nieprawidłowy stan.

- Może być błąd konfiguracji w usługach zauważyć w dokumencie wcześniej należy zweryfikować ponownie. Typowe przykłady są:

    - Serwer federacyjny nie ma punktów końcowych protokołu WS-Trust włączone

    - Serwerze federacyjnym nie zezwala na uwierzytelnianie ruchu przychodzącego z komputerów w sieci przy użyciu zintegrowanego uwierzytelniania systemu Windows.

    - Nie ma żadnego obiektu punktu połączenia usługi, który wskazuje nazwę domeny zweryfikowane w usłudze Azure AD w lesie usługi AD, w którym komputer należy do

---

### <a name="domainjoined--yes"></a>DomainJoined: tak  

To pole wskazuje, czy urządzenie jest dołączane do lokalnej usługi Active Directory lub nie. Jeśli wartość jest **nr**, urządzenie nie może wykonać sprzężenia hybrydowej usługi Azure AD.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: nie  

To pole wskazuje, czy urządzenie jest zarejestrowane w usłudze Azure AD jako urządzenia osobiste (oznaczony jako *dołączonych do miejsca pracy*). Ta wartość powinna być **nr** na komputerze przyłączonym do domeny, który jest przyłączony do hybrydowej usługi Azure AD. Jeśli wartość jest **tak**, dodano konto służbowe przed ukończeniem sprzężenia hybrydowej usługi Azure AD. W takim przypadku konto jest ignorowane w przypadku przy użyciu rocznicy zaktualizowanej wersji systemu Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Tak i AzureADPrt: tak
  
Te pola wskazują, czy użytkownik został pomyślnie uwierzytelniony za pomocą usługi Azure AD podczas rejestrowania urządzenia. Jeśli wartości są **nr**, może to być z powodu:

- Klucz magazynu zły (STK) w moduł TPM skojarzoną z tym urządzeniem rejestracji (wyboru KeySignTest uruchomionej z podwyższonym poziomem uprawnień).

- Alternatywnego Identyfikatora logowania

- Nie można odnaleźć serwera HTTP Proxy

## <a name="next-steps"></a>Kolejne kroki

Odpowiedzi na pytania, zobacz [zarządzanie urządzeniami — często zadawane pytania](device-management-faq.md) 