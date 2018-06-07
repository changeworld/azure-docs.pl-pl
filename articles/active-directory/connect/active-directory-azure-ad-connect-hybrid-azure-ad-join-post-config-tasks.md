---
title: 'Azure AD Connect: Zadania konfiguracji post hybrydowej usługi Azure AD join | Dokumentacja firmy Microsoft'
description: Szczegóły tego dokumentu post zadania konfiguracji niezbędne do ukończenia hybrydowej usługi Azure AD join
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: samueld
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.component: hybrid
ms.author: anandyadavmsft
ms.openlocfilehash: b337594f53c03ca1ddbc230473fe191bc968c92b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593303"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Zadania po konfiguracji w celu utworzenia sprzężenia hybrydowej usługi Azure AD

Po uruchomieniu Azure AD Connect, aby skonfigurować organizacji w celu utworzenia sprzężenia hybrydowej usługi Azure AD, istnieje kilka dodatkowych kroków, które należy wykonać, aby zakończyć konfigurowanie.  Wykonaj kroki, które są stosowane do urządzenia.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurowanie wdrożenia kontrolowane (opcjonalnie)
Wszystkich urządzeniach przyłączonych do domeny z systemem Windows 10 i Windows Server 2016 automatycznie zarejestrować w usłudze Azure AD po zakończeniu wszystkich kroków konfiguracji. Jeśli wolisz kontrolowany przez wdrożenie, a nie rejestracji automatycznej można użyć zasad grupy selektywnie włączyć lub wyłączyć automatyczne wdrażanie.  Zasady grupy należy ustawić przed rozpoczęciem innych konfiguracji kroki: Azure AD
* Utwórz obiekt zasad grupy w usłudze Active Directory.
* Nazwę (ex hybrydowej usługi Azure AD join).
* Edytuj i przejdź do: Konfiguracja komputera > zasady > Szablony administracyjne > składniki systemu Windows > rejestracji urządzeń.

>[!NOTE]
>2012R2 ustawienia zasad są w **Konfiguracja komputera > zasady > Szablony administracyjne > składniki systemu Windows > Dołączanie > automatycznie sprzężenia klienta komputerów w miejscu pracy**

* To ustawienie: rejestrowanie komputerów przyłączonych do domeny jako urządzenia.
* Zastosuj, a następnie kliknij przycisk OK.
* Połącz obiekt zasad grupy do lokalizacji (jednostki organizacyjnej, zabezpieczeń grupy, lub do domeny dla wszystkich urządzeń).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Skonfiguruj sieć z punktami końcowymi rejestracji urządzenia
Upewnij się, że następujące adresy URL są dostępne z komputerów w sieci organizacji rejestracji do usługi Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementowanie urządzeń WPAD dla systemu Windows 10
Jeśli Twoja organizacja ma dostęp do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, należy zaimplementować autowykrywania serwera Proxy sieci Web (WPAD) umożliwia komputerom z systemem Windows 10 można zarejestrować się w usłudze Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Skonfiguruj punkt połączenia usługi do żadnych lasów, które nie zostały skonfigurowane przez program Azure AD Connect 

Punkt połączenia usługi (SCP) zawiera dane dzierżawy usługi Azure AD, który będzie używany przez urządzenia do rejestracji automatycznej.  Uruchom skrypt programu PowerShell, ConfigureSCP.ps1, który został pobrany z usługi Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Skonfiguruj wszystkie usługi federacyjnej, która nie została skonfigurowana przy użyciu usługi Azure AD Connect

Jeśli Twoja organizacja korzysta z usługi federacyjnej, zaloguj się do usługi Azure AD, reguł oświadczeń w Twojej usługi Azure AD zaufania jednostki uzależnionej muszą zezwalać na uwierzytelnianie urządzenia. Jeśli korzystasz z federacyjnego z usługami AD FS, przejdź do [AD FS — Pomoc](https://aka.ms/aadrptclaimrules) do generowania reguł oświadczeń. Jeśli używasz rozwiązania federacyjnej innych niż Microsoft, skontaktuj się z tego dostawcy wskazówki.  

>[!NOTE]
>Jeśli masz urządzenia z systemem Windows niższego poziomu usługi musi obsługiwać wystawiania oświadczeń authenticationmethod i wiaormultiauthn podczas odbierania żądań do zaufania usługi Azure AD. W usługach AD FS należy dodać reguły przekształcania wystawiania, który przechodzi przez metodę uwierzytelniania.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Włączanie usługi Azure AD bezproblemowe logowania jednokrotnego dla niskiego poziomu urządzenia z systemem Windows

Jeśli organizacja używa synchronizacji skrótu hasła lub uwierzytelniania przekazywanego do logowania do usługi Azure AD, Azure AD SSO bezproblemowe włączyć za pomocą tej metody logowania do uwierzytelniania urządzeń z systemem Windows niższego poziomu: https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Ustaw zasady usługi Azure AD dla urządzeń z systemem Windows niższego poziomu

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, należy się upewnić, że zasady usługi Azure AD umożliwia użytkownikom rejestrowanie urządzeń. 

* Zaloguj się do konta w portalu Azure.
* Przejdź do: Azure Active Directory > urządzenia > Ustawienia urządzenia
* Ustaw "Użytkownicy mogą zarejestrować swoje urządzenia z usługą Azure AD" do wszystkich.
* Klikanie pozycji Zapisz.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Dodawanie punktu końcowego usługi Azure AD do urządzeń z systemem Windows niższego poziomu

Dodawanie punktu końcowego uwierzytelniania urządzenia usługi Azure AD do lokalnej strefy Intranet na urządzeniach z systemem Windows niższego poziomu Aby monity certyfikatu podczas uwierzytelniania urządzenia: https://device.login.microsoftonline.com 

Jeśli używasz [bezproblemowe logowanie Jednokrotne](https://aka.ms/hybrid/sso), również włączyć "Zezwalaj na aktualizacje paska stanu za pomocą skryptów" w tej strefie i Dodaj następujący punkt końcowy: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Zainstaluj Microsoft dołączania urządzenia z systemem Windows niższego poziomu

Ten Instalator tworzy zaplanowane zadanie w systemie urządzenia, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie nie przyłączy urządzenia z usługą Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnieniu przy użyciu zintegrowanego uwierzytelniania systemu Windows. Centrum pobierania wynosi https://www.microsoft.com/en-us/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurowanie zasad grupy, aby umożliwić rejestrację urządzenia

* Utwórz obiekt zasad grupy w usłudze Active Directory — Jeśli nie został jeszcze utworzony.
* Nazwę (ex hybrydowej usługi Azure AD join).
* Edytuj i przejdź do: Konfiguracja komputera > zasady > Szablony administracyjne > składniki systemu Windows > rejestracji urządzeń
* Włącz: Rejestrowanie komputerów przyłączonych do domeny jako urządzenia
* Zastosuj, a następnie kliknij przycisk OK.
* Połącz obiekt zasad grupy do lokalizacji (jednostki organizacyjnej, zabezpieczeń grupy, lub do domeny dla wszystkich urządzeń).

>[!NOTE]
>2012R2 ustawienia zasad są w **Konfiguracja komputera > zasady > Szablony administracyjne > składniki systemu Windows > Dołączanie > automatycznie sprzężenia klienta komputerów w miejscu pracy**

## <a name="next-steps"></a>Kolejne kroki
[Skonfigurować zapisu zwrotnego urządzeń](./active-directory-aadconnect-feature-device-writeback.md)
