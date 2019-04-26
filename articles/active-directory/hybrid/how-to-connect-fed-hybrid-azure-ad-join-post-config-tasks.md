---
title: 'Program Azure AD Connect: Zadania konfiguracji po przyłączanie do hybrydowej usługi Azure AD | Dokumentacja firmy Microsoft'
description: Szczegóły tego dokumentu, wpis zadania konfiguracji potrzebne do ukończenia dołączenie do hybrydowej usługi Azure AD
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9af969700f4f2dfbedc4833badd7e7349696302
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60244593"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Zadania pokonfiguracyjne dotyczące dołączania do hybrydowej usługi Azure AD

Po uruchomieniu usługi Azure AD Connect, aby skonfigurować organizacji do hybrydowej usługi Azure AD join, istnieje kilka dodatkowych kroków, które należy wykonać w celu zakończenia tej instalacji.  Wykonaj kroki, które są stosowane do urządzeń.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurowanie kontrolowane wprowadzanie (opcjonalnie)
Zarejestruj wszystkie przyłączone do domeny urządzenia z systemem Windows 10 i Windows Server 2016 automatycznie z usługą Azure AD po zakończeniu wszystkich kroków konfiguracji. Jeśli wolisz kontrolowane wprowadzanie zamiast rejestracji automatycznej można użyć zasad grupy można selektywnie włączać lub wyłączać automatycznego wdrożenia.  Te zasady grupy należy ustawić przed uruchomieniem innych konfiguracji kroków: Azure AD
* Utwórz obiekt zasad grupy w usłudze Active Directory.
* Nazwij ją (dołączenie do ex hybrydowej usługi Azure AD).
* Edytuj & Przejdź do:  Konfiguracja komputera > zasady > Szablony administracyjne > składniki Windows > Rejestracja urządzeń w usłudze.

>[!NOTE]
>Dla 2012R2 ustawienia zasad są w **Konfiguracja komputera > zasady > Szablony administracyjne > składniki Windows > Dołączanie > automatycznie sprzężenia klienta komputerów w miejscu pracy**

* To ustawienie:  Komputery przyłączone do domeny należy zarejestrować jako urządzenia.
* Zastosowanie, a następnie kliknij przycisk OK.
* Połącz obiekt zasad grupy do lokalizacji wybranej (jednostki organizacyjnej, zabezpieczeń grupy, lub do domeny na wszystkich urządzeniach).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Skonfiguruj sieć z punktami końcowymi rejestracji urządzenia
Upewnij się, że następujące adresy URL są dostępne z komputerów w sieci organizacji w celu rejestracji do usługi Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementowanie urządzeń WPAD dla systemu Windows 10
Jeśli Twoja organizacja ma dostęp do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, należy zaimplementować autowykrywania serwera Proxy sieci Web (WPAD) aby umożliwić komputerom systemu Windows 10 zarejestrować się do usługi Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Skonfiguruj punkt połączenia usługi w dowolnym lasów, które nie zostały skonfigurowane przy użyciu usługi Azure AD Connect 

Punkt połączenia usługi (SCP) zawiera dane dzierżawy usługi Azure AD, który będzie używany przez urządzenia do rejestracji automatycznej.  Uruchom skrypt programu PowerShell, a plik ConfigureSCP.ps1, który został pobrany z usługi Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Skonfiguruj wszystkie usługi federacyjnej, która nie została skonfigurowana przy użyciu usługi Azure AD Connect

Jeśli Twoja organizacja używa usługi federacyjnej, aby zarejestrować się w usłudze Azure AD, reguły oświadczeń w usługi Azure AD zaufania jednostki uzależnionej musi zezwalać na uwierzytelnianie urządzenia. Jeśli używasz federacji z usługami AD FS, przejdź do strony [pomocy usługi AD FS](https://aka.ms/aadrptclaimrules) do generowania reguł oświadczeń. Jeśli używasz rozwiązania Federacji firmy innej niż Microsoft, skontaktuj się z tego dostawcy, aby uzyskać wskazówki.  

>[!NOTE]
>W przypadku urządzeń z Windows niższego poziomu, usługa musi obsługiwać wystawiania oświadczeń authenticationmethod i wiaormultiauthn podczas odbierania żądań zaufania usługi Azure AD. W usługach AD FS, należy dodać reguły przekształcania wystawiania, który przechodzi przez metodę uwierzytelniania.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Włączanie usługi Azure AD bezproblemowego logowania jednokrotnego dla Windows niższego poziomu urządzeń

Jeśli Twoja organizacja korzysta z synchronizacji skrótów haseł lub uwierzytelniania przekazywanego, aby zarejestrować się w usłudze Azure AD, należy włączyć Azure bezproblemowe logowanie Jednokrotne usługi AD za pomocą tej metody logowania do uwierzytelniania systemu Windows niższego poziomu urządzeń: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Ustaw zasady usługi Azure AD dla Windows niższego poziomu urządzeń

Aby zarejestrować urządzenia niskiego poziomu Windows, należy się upewnić, że zasady usługi Azure AD umożliwia użytkownikom rejestrowanie urządzeń. 

* Zaloguj się do swojego konta w witrynie Azure portal.
* Przejdź do strony:  Usługa Azure Active Directory > urządzeń > Ustawienia urządzenia
* Ustaw "Użytkownicy mogą rejestrować swoje urządzenia z usługą Azure AD" do wszystkich.
* Klikanie pozycji Zapisz.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Dodawanie punktu końcowego usługi Azure AD do Windows niższego poziomu urządzeń

Dodaj punkt końcowy uwierzytelniania na urządzeniu usługi Azure AD do lokalnej strefy Intranet na urządzeniach Windows niższego poziomu w celu uniknięcia monity certyfikatu podczas uwierzytelniania urządzenia: https://device.login.microsoftonline.com 

Jeśli używasz [bezproblemowe logowanie Jednokrotne](how-to-connect-sso.md)również włączyć "Zezwalaj na aktualizacje paska stanu za pomocą skryptu" w tej strefie i Dodaj następujący punkt końcowy: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Zainstaluj Microsoft dołączanie do miejsca pracy na urządzeniach z Windows niższego poziomu

Ten Instalator jest utworzenie zaplanowanego zadania w systemie urządzenia, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika, po uwierzytelnieniu przy użyciu zintegrowanego uwierzytelniania Windows. Centrum pobierania wynosi https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurowanie zasad grupy, aby zezwolić na rejestrację urządzeń

* Utwórz obiekt zasad grupy w usłudze Active Directory — Jeśli nie został jeszcze utworzony.
* Nazwij ją (dołączenie do ex hybrydowej usługi Azure AD).
* Edytuj & Przejdź do:  Konfiguracja komputera > zasady > Szablony administracyjne > składniki Windows > Rejestracja urządzeń
* Włącz:  Rejestrowanie komputerów przyłączonych do domeny jako urządzenia
* Zastosowanie, a następnie kliknij przycisk OK.
* Połącz obiekt zasad grupy do lokalizacji wybranej (jednostki organizacyjnej, zabezpieczeń grupy, lub do domeny na wszystkich urządzeniach).

>[!NOTE]
>Dla 2012R2 ustawienia zasad są w **Konfiguracja komputera > zasady > Szablony administracyjne > składniki Windows > Dołączanie > automatycznie sprzężenia klienta komputerów w miejscu pracy**

## <a name="next-steps"></a>Kolejne kroki
[Konfiguruj zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md)
