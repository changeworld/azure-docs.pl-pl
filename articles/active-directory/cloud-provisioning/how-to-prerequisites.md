---
title: Wymagania wstępne dotyczące inicjowania obsługi administracyjnej chmury usługi Azure AD Connect w usłudze Azure AD
description: W tym artykule opisano wymagania wstępne i wymagania sprzętowe potrzebne do inicjowania obsługi administracyjnej w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332083"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Wymagania wstępne dotyczące inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect
Ten artykuł zawiera wskazówki dotyczące wybierania i używania usługi Azure Active Directory (Azure AD) Connect cloud provisioning jako rozwiązania tożsamości.



## <a name="cloud-provisioning-agent-requirements"></a>Wymagania dotyczące agenta inicjowania obsługi administracyjnej w chmurze
Aby korzystać z obsługi administracyjnej w chmurze usługi Azure AD Connect, potrzebne są następujące elementy:
    
- Konto administratora globalnego dla dzierżawy usługi Azure AD, która nie jest użytkownikiem-gościem.
- Serwer lokalny dla agenta inicjującego inicjowanie obsługi administracyjnej z systemem Windows 2012 R2 lub nowszym.
- Konfiguracje zapory lokalnej.

>[!NOTE]
>Agent inicjowania obsługi administracyjnej można obecnie zainstalować tylko na serwerach w języku angielskim. Zainstalowanie pakietu języka angielskiego na serwerze w języku nieanglojęzycznym nie jest prawidłowym rozwiązaniem i spowoduje, że agent nie zostanie zainstalowany. 

Pozostała część dokumentu zawiera instrukcje krok po kroku dla tych wymagań wstępnych.

### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym usługi Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób można zarządzać konfiguracją dzierżawy, jeśli usługi lokalne nie powiodą się lub staną się niedostępne. Dowiedz się, jak [dodać konto administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Zakończenie tego kroku ma kluczowe znaczenie dla zapewnienia, że nie zostaniesz zablokowany z dzierżawy.
1. Dodaj co najmniej jedną [niestandardową nazwę domeny](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy jednej z tych nazw domen.

### <a name="in-your-directory-in-active-directory"></a>W katalogu w usłudze Active Directory

Uruchom [narzędzie IdFix,](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) aby przygotować atrybuty katalogu do synchronizacji.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Zidentyfikuj serwer hosta przyłączony do domeny z systemem Windows Server 2012 R2 lub nowszym z co najmniej 4 GB pamięci RAM i .NET 4.7.1+ runtime.

1. Jeśli między serwerami a usługą Azure AD znajduje się zapora, skonfiguruj następujące elementy:
   - Upewnij się, że agenci mogą składać żądania *wychodzące* do usługi Azure AD za pomocą następujących portów:

        | Numer portu | Zastosowanie |
        | --- | --- |
        | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas sprawdzania poprawności certyfikatu TLS/SSL.  |
        | **443** | Obsługuje całą komunikację wychodzącą z usługą. |
        | **8080** (opcjonalnie) | Agenci zgłaszają swój stan co 10 minut za portem 8080, jeśli port 443 jest niedostępny. Ten stan jest wyświetlany w portalu usługi Azure AD. |
     
   - Jeśli zapora wymusza reguły zgodnie z użytkownikami źródłowymi, otwórz te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy umożliwia określenie bezpiecznych sufiksów, dodaj połączenia do \*msappproxy.net i \*.servicebus.windows.net. Jeśli nie, zezwalaj na dostęp do [zakresów adresów IP centrum danych platformy Azure,](https://www.microsoft.com/download/details.aspx?id=41653)które są aktualizowane co tydzień.
   - Twoi agenci potrzebują dostępu do login.windows.net i login.microsoftonline.com do wstępnej rejestracji. Otwórz również zaporę dla tych adresów URL.
   - Aby uzyskać weryfikację certyfikatu, odblokuj następujące adresy URL: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 i www\.microsoft.com:80. Te adresy URL są używane do sprawdzania poprawności certyfikatów z innymi produktami firmy Microsoft, więc te adresy URL mogą być już odblokowane.

### <a name="verify-the-port"></a>Sprawdź port
Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy agent może się z nią komunikować, użyj następującego adresu URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure za pomocą portu 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym jest zainstalowany agent.

![Weryfikacja osiągów portu](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Dodatkowe wymagania
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Wymagania dotyczące protokołu TLS

>[!NOTE]
>Transport Layer Security (TLS) to protokół zapewniający bezpieczną komunikację. Zmiana ustawień TLS wpływa na cały las. Aby uzyskać więcej informacji, zobacz [Aktualizacja, aby włączyć protokoły TLS 1.1 i TLS 1.2 jako domyślne bezpieczne protokoły w systemie WinHTTP w systemie Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Serwer systemu Windows obsługujący agent aprowizujący w chmurze usługi Azure AD Connect musi mieć włączoną funkcję TLS 1.2 przed jego zainstalowaniem.

Aby włączyć TLS 1.2, wykonaj następujące kroki.

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)

