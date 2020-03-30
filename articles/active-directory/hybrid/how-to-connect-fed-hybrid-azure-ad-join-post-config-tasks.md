---
title: 'Usługa Azure AD Connect: zadania konfiguracji dołączania do usługi Azure AD | Dokumenty firmy Microsoft'
description: Ten dokument zawiera szczegółowe informacje o zadaniach konfiguracyjnych postu potrzebnych do ukończenia hybrydowego sprzężenia usługi Azure AD
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
ms.openlocfilehash: ffb8243041bb93ba8be6a65bb83df6f84affaee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049663"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Zadania pokonfiguracyjne dotyczące dołączania do hybrydowej usługi Azure AD

Po uruchomieniu usługi Azure AD Connect w celu skonfigurowania organizacji dla hybrydowego sprzężenia usługi Azure AD, istnieje kilka dodatkowych kroków, które należy wykonać, aby zakończyć tę konfigurację.  Należy wykonać tylko te czynności, które mają zastosowanie do twoich urządzeń.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurowanie kontrolowanego wdrażania (opcjonalnie)
Wszystkie urządzenia przyłączone do domeny z systemami Windows 10 i Windows Server 2016 automatycznie rejestrują się w usłudze Azure AD po zakończeniu wszystkich kroków konfiguracji. Jeśli wolisz kontrolowane wdrażanie, a nie tę automatyczną rejestrację, możesz użyć zasad grupy, aby selektywnie włączyć lub wyłączyć automatyczne wdrażanie.  Ta zasada grupy powinna zostać ustawiona przed rozpoczęciem innych kroków konfiguracji:
* Tworzenie obiektu zasad grupy w usłudze Active Directory.
* Nazwij go (ex- hybrydowe sprzężenie usługi AZURE AD).
* Edytuj i przejdź do: Zasady konfiguracji komputera > > szablony administracyjne > składniki systemu Windows > rejestracji urządzeń.

>[!NOTE]
>W przypadku 2012R2 ustawienia zasad znajdują się w obszarze **Konfiguracja komputera > Zasady > szablony administracyjne > składniki systemu Windows > dołączanie do miejsca pracy > Automatyczne dołączanie do miejsc pracy komputerów klienckich**

* Włącz to ustawienie: rejestrowanie komputerów przyłączonych do domeny jako urządzeń.
* Zastosuj i kliknij przycisk OK.
* Połącz obiekt zasad grupy z wybraną lokalizacją (jednostką organizacyjną, grupą zabezpieczeń lub domeną dla wszystkich urządzeń).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurowanie sieci z punktami końcowymi rejestracji urządzeń
Upewnij się, że następujące adresy URL są dostępne z komputerów w sieci organizacyjnej do rejestracji w usłudze Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementowanie WPAD dla urządzeń z systemem Windows 10
Jeśli twoja organizacja uzyskuje dostęp do Internetu za pośrednictwem wychodzącego serwera proxy, zaimplementuj automatyczne odnajdywanie serwera proxy sieci Web (WPAD), aby umożliwić komputerom z systemem Windows 10 rejestrowanie się w usłudze Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurowanie punktu połączenia SCP w lasach, które nie zostały skonfigurowane przez usługę Azure AD Connect 

Punkt połączenia usługi (SCP) zawiera informacje dzierżawy usługi Azure AD, które będą używane przez urządzenia do automatycznej rejestracji.  Uruchom skrypt programu PowerShell ConfigureSCP.ps1 pobrany z usługi Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurowanie dowolnej usługi federacyjnej, która nie została skonfigurowana przez usługę Azure AD Connect

Jeśli twoja organizacja używa usługi federacyjnej do logowania się do usługi Azure AD, reguły oświadczeń w zaufaniu jednostki uzależniającej usługi Azure AD muszą zezwalać na uwierzytelnianie urządzeń. Jeśli używasz federacji z usługą AD FS, przejdź do [pomocy usług AD FS,](https://aka.ms/aadrptclaimrules) aby wygenerować reguły oświadczeń. Jeśli używasz rozwiązania federacyjnego firmy innych niż Microsoft, skontaktuj się z tym dostawcą, aby uzyskać wskazówki.  

>[!NOTE]
>Jeśli masz urządzenia w dół systemu Windows, usługa musi obsługiwać wystawianie oświadczeń authenticationmethod i wiaormultiauthn podczas odbierania żądań do zaufania usługi Azure AD. W ujrzewach ad fs należy dodać regułę przekształcania wystawy, która przechodzi przez metodę uwierzytelniania.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Włącz bezproblemowe logowanie jednokrotne usługi Azure AD dla urządzeń z systemem Windows w dół

Jeśli twoja organizacja używa synchronizacji skrótów haseł lub uwierzytelniania przekazywania do logowania się do usługi Azure AD, włącz bezproblemowe https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ssologowanie usługi Azure AD bez logowania za pomocą tej metody logowania w celu uwierzytelnienia urządzeń w dół systemu Windows: . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Ustawianie zasad usługi Azure AD dla urządzeń z systemem Windows w dół

Aby zarejestrować urządzenia z systemem Windows w dół, należy upewnić się, że zasady usługi Azure AD umożliwiają użytkownikom rejestrowanie urządzeń. 

* Zaloguj się do swojego konta w witrynie Azure portal.
* Przejdź do: Ustawienia > urządzeń usługi Azure Active Directory > urządzenia
* Ustaw "Użytkownicy mogą rejestrować swoje urządzenia za pomocą usługi Azure AD" na ALL.
* Klikanie pozycji Zapisz.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Dodawanie punktu końcowego usługi Azure AD do urządzeń z systemem Windows w dół

Dodaj punkt końcowy uwierzytelniania urządzeń usługi Azure AD do lokalnych stref intranetowych na urządzeniach z systemem Windows w dół, aby uniknąć monitów o certyfikat podczas uwierzytelniania urządzeń:`https://device.login.microsoftonline.com` 

Jeśli używasz [bezproblemowego logowania jednokrotnego,](how-to-connect-sso.md)włącz również "Zezwalaj na aktualizacje paska stanu za pomocą skryptu" w tej strefie i dodaj następujący punkt końcowy:`https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instalowanie programu Microsoft Workplace Join na urządzeniach z systemem Windows w dół

Ten instalator tworzy zaplanowane zadanie w systemie urządzeń, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD z poświadczeniami użytkownika po uwierzytelnieniu przy użyciu zintegrowanego uwierzytelniania systemu Windows. Centrum pobierania znajduje https://www.microsoft.com/download/details.aspx?id=53554się pod adresem . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurowanie zasad grupy w celu umożliwienia rejestracji urządzenia

* Utwórz obiekt zasad grupy w usłudze Active Directory — jeśli nie został jeszcze utworzony.
* Nazwij go (ex- hybrydowe sprzężenie usługi AZURE AD).
* Edytuj & przejdź do: Zasady > konfiguracji komputera > szablony administracyjne > > rejestracji urządzeń składników systemu Windows
* Włącz: rejestrowanie komputerów przyłączonych do domeny jako urządzeń
* Zastosuj i kliknij przycisk OK.
* Połącz obiekt zasad grupy z wybraną lokalizacją (jednostką organizacyjną, grupą zabezpieczeń lub domeną dla wszystkich urządzeń).

>[!NOTE]
>W przypadku 2012R2 ustawienia zasad znajdują się w obszarze **Konfiguracja komputera > Zasady > szablony administracyjne > składniki systemu Windows > dołączanie do miejsca pracy > Automatyczne dołączanie do miejsc pracy komputerów klienckich**

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie stornia zwrotnego urządzenia](how-to-connect-device-writeback.md)
