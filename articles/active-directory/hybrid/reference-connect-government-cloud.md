---
title: 'Usługa Azure AD Connect: zagadnienia dotyczące tożsamości hybrydowej dla platformy Azure dla instytucji rządowych'
description: Specjalne zagadnienia dotyczące wdrażania usługi Azure AD Connect w chmurze rządowej.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378927"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Zagadnienia dotyczące tożsamości hybrydowej dla platformy Azure dla instytucji rządowych 
W poniższym dokumencie opisano zagadnienia dotyczące implementowania środowiska hybrydowego z chmurą azure dla instytucji rządowych.  Te informacje są dostarczane jako punkt odniesienia dla administratorów i architektów, którzy pracują z chmurą azure dla instytucji rządowych.  
> [!NOTE] 
> Aby zintegrować lokalne środowisko usługi AD z chmurą Usługi Azure Governemnt, należy uaktualnić do najnowszej wersji [usługi Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594) 

> [!NOTE] 
> Pełna lista punktów końcowych dod rządu STANÓW Zjednoczonych znajduje się w [dokumentacji](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane 
Następujące informacje są dostarczane do implementacji uwierzytelniania przekazywanego (PTA) i chmury azure dla instytucji rządowych.

### <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL  
Przed wdrożeniem agenta uwierzytelniania przekazywania sprawdź, czy istnieje zapora między serwerami a usługą Azure AD. Jeśli zapora lub serwer proxy zezwala na umieszczanie na białej liście DNS, dodaj następujące połączenia: 
> [!NOTE]
> Poniższe wskazówki dotyczą również instalowania [łącznika serwera proxy aplikacji](https://aka.ms/whyappproxy) dla środowisk platformy Azure dla instytucji rządowych.

|Adres URL |Zastosowanie|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Komunikacja między agentem a usługą Azure AD w chmurze |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Agent używa tych adresów URL do weryfikacji certyfikatów.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br> *.microsoftonline-p.us </br>*msauth.net </br> *.msauthimages.net .msauthimages.net </br>*msecnd.net</br>*.msftauth.net </br>*msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Agent używa tych adresów URL podczas procesu rejestracji.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instalowanie agenta dla chmury azure dla instytucji rządowych 
Aby zainstalować agenta dla chmury azure dla instytucji rządowych, należy wykonać następujące kroki: W terminalu wiersza polecenia przejdź do folderu, w którym znajduje się plik wykonywalny do zainstalowania agenta. Uruchom następujące polecenie, które określa, że instalacja jest dla platformy Azure dla instytucji rządowych. 

W przypadku uwierzytelniania przekazywanego: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Dla serwera proxy aplikacji:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Znak jednokrotny na 
Konfigurowanie serwera usługi Azure AD Connect: jeśli używasz uwierzytelniania przekazywania jako metody logowania, nie jest wymagane żadne dodatkowe sprawdzanie wymagań wstępnych. Jeśli używasz synchronizacji skrótu hasła jako metody logowania i jeśli istnieje zapora między usługą Azure AD Connect i usługą Azure AD, upewnij się, że:
- Używasz wersji 1.1.644.0 lub nowszej usługi Azure AD Connect. 
- Jeśli zapora lub serwer proxy zezwala na umieszczanie na białej liście DNS, dodaj połączenia do *.msapproxy.us adresów URL za pośrednictwem portu 443. Jeśli nie, zezwalaj na dostęp do zakresów adresów IP centrum danych platformy Azure, które są aktualizowane co tydzień. Ten warunek wstępny ma zastosowanie tylko po włączeniu tej funkcji. Nie jest wymagane dla rzeczywistych logowania użytkownika. 

### <a name="rolling-out-seamless-sso"></a>Wdrażanie bezszwowego logowania jednokrotnego 
Możesz stopniowo wdrażać bezproblemowe logowanie jednokrotne dla użytkowników, korzystając z poniższych instrukcji. Możesz zacząć od dodania następującego adresu URL usługi Azure AD do wszystkich lub wybranych ustawień strefy intranetu wybranych użytkowników przy użyciu zasad grupy w usłudze Active Directory:https://autologon.microsoft.us 

Ponadto należy włączyć ustawienie zasad strefy intranetu o nazwie Zezwalaj na aktualizacje paska stanu za pomocą skryptu za pośrednictwem zasad grupy. Zagadnienia przeglądarki Mozilla Firefox (wszystkie platformy) Mozilla Firefox nie używa automatycznie uwierzytelniania Kerberos. Każdy użytkownik musi ręcznie dodać adres URL usługi Azure AD do ustawień Firefoksa, wykonując następujące kroki: 
1. Uruchom Firefoksa i wpisz about:config na pasku adresu. Odrzuć wszystkie wyświetlane powiadomienia. 
2. Wyszukaj preferencje network.negotiate-auth.trusted-uris. Ta preferencja zawiera listę zaufanych witryn Firefoksa do uwierzytelniania Kerberos. 
3. Kliknij prawym przyciskiem myszy i wybierz polecenie Modyfikuj. 
4. Wprowadź https://autologon.microsoft.us w tym polu.
5. Wybierz przycisk OK, a następnie otwórz ponownie przeglądarkę. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge oparty na Chromium (wszystkie platformy) 
Jeśli zostały zastąpione `AuthNegotiateDelegateAllowlist` lub `AuthServerAllowlist` ustawienia zasad w środowisku, upewnij się, żehttps://autologon.microsoft.us) dodasz adres URL usługi Azure AD (do nich również). 

### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy) 
Jeśli zostały zastąpione `AuthNegotiateDelegateWhitelist` lub `AuthServerWhitelist` ustawienia zasad w środowisku, upewnij się, żehttps://autologon.microsoft.us) dodasz adres URL usługi Azure AD (do nich również). 

## <a name="next-steps"></a>Następne kroki
[Uwierzytelnianie przekazywane Logowanie](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[jednokrotne](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
