---
title: Wymagania wstępne dotyczące Azure AD Connect aprowizacji w chmurze w usłudze Azure AD
description: Ten temat zawiera opis wymagań wstępnych i wymagania sprzętowe dotyczące aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155edf72a60e079a609853e953e3cf66024cc83c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794252"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Wymagania wstępne dotyczące Azure AD Connect aprowizacji w chmurze
Ten temat zawiera wskazówki dotyczące wybierania i używania Azure AD Connect aprowizacji w chmurze jako rozwiązania do obsługi tożsamości.



## <a name="cloud-provisioning-agent-requirements"></a>Wymagania dotyczące agenta aprowizacji w chmurze
Aby korzystać z usługi Cloud Provisioning Azure AD Connect, potrzebne są następujące elementy:
    
- konto administratora globalnego dla dzierżawy usługi Azure AD
- serwer lokalny dla agenta aprowizacji z systemem Windows 2012 R2 lub nowszym
- konfiguracje zapory lokalnej

Pozostała część dokumentu zawiera instrukcje krok po kroku dotyczące tych wymagań wstępnych.

### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób możesz zarządzać konfiguracją dzierżawy, jeśli usługi lokalne zakończą się niepowodzeniem lub staną się niedostępne. Dowiedz się więcej [na temat dodawania konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku jest niezwykle ważne, aby upewnić się, że dzierżawa nie została zablokowana.
2. Dodaj co najmniej jedną [niestandardową nazwę domeny](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy użyciu jednej z tych nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Tożsamość serwera hosta przyłączonego do domeny z systemem Windows Server 2012 R2 lub nowszym z co najmniej 4 GB pamięci RAM i .NET 4.7.1 + środowisko uruchomieniowe 

2. Jeśli między serwerami i usługą Azure AD istnieje Zapora, skonfiguruj następujące elementy:
   - Upewnij się, że agenci mogą wykonywać żądania *wychodzące* do usługi Azure AD za pośrednictwem następujących portów:

     | Numer portu | Zastosowanie |
     | --- | --- |
     | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas weryfikacji certyfikatu SSL |
     | **443** | Obsługuje całą komunikację wychodzącą z usługą |
     | **8080** (opcjonalnie) | Agenci raportują swój stan co dziesięć minut w porcie 8080, jeśli port 443 jest niedostępny. Ten stan jest wyświetlany w portalu usługi Azure AD. Port 8080 _nie_ jest używany podczas logowania użytkownika. |
     
     Jeśli Zapora wymusza reguły zależne od użytkowników inicjujących, należy otworzyć te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy umożliwia określenie bezpiecznych sufiksów, należy dodać połączenia do **\*. msappproxy.NET** i **\*. ServiceBus.Windows.NET**. W przeciwnym razie Zezwól na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień.
   - Twoje Agenci muszą mieć dostęp do **login.Windows.NET** i **login.microsoftonline.com** na potrzeby rejestracji wstępnej. Należy również otworzyć Zaporę dla tych adresów URL.
   - Aby sprawdzić poprawność certyfikatu, Odblokuj następujące adresy URL: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**i **www\.Microsoft.com:80**. Ponieważ te adresy URL są używane do sprawdzania poprawności certyfikatu z innymi produktami firmy Microsoft, te adresy URL mogą już być odblokowane.

### <a name="verify-the-port"></a>Weryfikowanie portu
Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy Agent może się z nim komunikować, możesz użyć następujących czynności:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdzi, czy agenci mogą komunikować się z platformą Azure przez port 443.  Otwórz przeglądarkę i przejdź do powyższego adresu URL z serwera, na którym Agent jest zainstalowany.
![Usługi](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Wymagania dodatkowe
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Wymagania protokołu TLS

>[!NOTE]
>Transport Layer Security (TLS) to protokół, który zapewnia bezpieczną komunikację.  Zmiana ustawień protokołu TLS ma wpływ na cały las.  Aby uzyskać więcej informacji [, zobacz Aktualizacja umożliwiająca włączenie protokołu tls 1,1 i tls 1,2 jako domyślnych protokołów Secure w usłudze WinHTTP w systemie Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

W systemie Windows Server, który będzie hostem agenta aprowizacji Azure AD Connect w chmurze, należy włączyć protokół TLS 1,2 przed jego zainstalowaniem.

Aby włączyć protokół TLS 1.2:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)

