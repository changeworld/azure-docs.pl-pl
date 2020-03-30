---
title: Rozwiązywanie problemów ze starszymi hybrydowymi urządzeniami przyłączanym do usługi Azure Active Directory
description: Rozwiązywanie problemów z hybrydową usługą Azure Active Directory przyłączonych do urządzeń poziomu down.Troubleshooting hybrid Azure Active Directory joined down-level devices.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379120"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączanych do urządzeń poziomu sieciowego 

Ten artykuł dotyczy tylko następujących urządzeń: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

W przypadku systemów Windows 10 lub Windows Server 2016 zobacz [Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączonych do urządzeń z systemem Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

W tym artykule przyjęto założenie, że [skonfigurowano hybrydowe urządzenia przyłączone do usługi Azure Active Directory](hybrid-azuread-join-plan.md) w następujących scenariuszach:

- Dostęp warunkowy oparty na urządzeniach

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, jak rozwiązać potencjalne problemy.  

**Co powinieneś wiedzieć:** 

- Hybrydowe sprzężenie usługi Azure AD dla urządzeń z systemem Windows niższego poziomu działa nieco inaczej niż w systemie Windows 10. Wielu klientów nie zdaje sobie sprawy, że potrzebują usług AD FS (dla domen federacyjnej) lub bezproblemowego logowania jednokrotnego skonfigurowane (dla domen zarządzanych).
- W przypadku klientów z domenami federacyjnym, jeśli punkt połączenia usługi (SCP) został skonfigurowany w taki sposób, że wskazuje nazwę domeny zarządzanej (na przykład contoso.onmicrosoft.com, zamiast contoso.com), wówczas hybrydowe przyłączanie do usługi Azure AD dla urządzeń z systemem Windows niższego poziomu nie działa.
- Maksymalna liczba urządzeń na użytkownika ma obecnie zastosowanie również do urządzeń hybrydowych usługi Azure AD po niższego poziomu. 
- To samo urządzenie fizyczne pojawia się wiele razy w usłudze Azure AD, gdy wielu użytkowników domeny loguje się do hybrydowych urządzeń platformy Azure AD po niższego poziomu.  Na przykład jeśli *jdoe* i *jharnett* logują się do urządzenia, dla każdego z nich na karcie Informacje **o użytkowniku** zostanie utworzona osobna rejestracja (DeviceID). 
- Można również uzyskać wiele wpisów dla urządzenia na karcie informacje o użytkowniku z powodu ponownej instalacji systemu operacyjnego lub ręcznej ponownej rejestracji.
- Początkowa rejestracja / sprzężenie urządzeń jest skonfigurowana do wykonywania próby logowania lub blokady / odblokowania. Może wystąpić 5-minutowe opóźnienie wyzwalane przez zadanie harmonogramu zadań. 
- Upewnij się, że jest zainstalowana [aktualizacja KB4284842](https://support.microsoft.com/help/4284842) w przypadku dodatku SP1 dla systemu Windows 7 lub dodatku SP1 dla systemu Windows Server 2008 R2. Ta aktualizacja zapobiega przyszłym błędom uwierzytelniania spowodowanym utratą dostępu klienta do chronionych kluczy po zmianie hasła.

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: Pobierz stan rejestracji 

**Aby zweryfikować status rejestracji:**  

1. Zaloguj się przy za pomocą konta użytkownika, który wykonał hybrydowe sprzężenie usługi Azure AD.
1. Otwieranie wiersza polecenia 
1. Wpisz polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

To polecenie wyświetla okno dialogowe, w które zawiera szczegółowe informacje o stanie sprzężenia.

![Dołączanie do miejsca pracy dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: Oceń stan hybrydowego sprzężenia usługi Azure AD 

Jeśli urządzenie nie zostało sprzężene z usługą Azure AD hybrydowe, możesz spróbować wykonać hybrydowe sprzężenie usługi Azure AD, klikając przycisk "Dołącz". Jeśli próba wykonania hybrydowego sprzężenia usługi Azure AD zakończy się niepowodzeniem, zostaną wyświetlone szczegóły dotyczące błędu.

**Najczęstsze problemy to:**

- Nieprawidłowo skonfigurowane problemy z usługami AD FS lub usługą Azure AD lub siecią

    ![Dołączanie do miejsca pracy dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Program Autoworkplace.exe nie może dyskretnie uwierzytelnić się za pomocą usługi Azure AD lub ad fs. Może to być spowodowane brakiem lub nieprawidłowo skonfigurowanym usługą AD FS (dla domen federacyjne) lub brakiem lub nieprawidłowo skonfigurowanym logowaniem jednokrotnym usługi Azure AD Seamless (dla domen zarządzanych) lub problemami z siecią. 
   - Może się okazać, że uwierzytelnianie wieloskładnikowe (MFA) jest włączone/skonfigurowane dla użytkownika, a WIAORMULTIAUTHN nie jest skonfigurowany na serwerze usług AD FS. 
   - Inną możliwością jest to, że strona wykrywania obszaru macierzystego (HRD) czeka na interakcję z użytkownikiem, co uniemożliwia **autoworkplace.exe** ciche żądanie tokenu.
   - Może się okazać, że w strefie intranetowej usługi IE na kliencie brakuje adresów URL usług AD FS i azure AD.
   - Problemy z łącznością sieciową mogą uniemożliwiać **autoworkplace.exe** dotarcie do usług AD FS lub adresów URL usługi Azure AD. 
   - **Program Autoworkplace.exe** wymaga, aby klient miał bezpośredni zasięg wzroku od klienta do lokalnego kontrolera domeny usługi AD w organizacji, co oznacza, że hybrydowe sprzężenie usługi Azure AD powiedzie się tylko wtedy, gdy klient jest połączony z intranetem organizacji.
   - Twoja organizacja korzysta z usługi Azure `https://autologon.microsoftazuread-sso.com` AD `https://aadg.windows.net.nsatc.net` Seamless Single Sign-On lub nie są obecne w ustawieniach intranetu IE urządzenia i zezwalaj na **aktualizacje paska stanu za pomocą skryptu** nie jest włączona dla strefy Intranet.
- Nie jesteś zalogowany jako użytkownik domeny

   ![Dołączanie do miejsca pracy dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Istnieje kilka różnych powodów, dla których może to nastąpić:

   - Zalogowany użytkownik nie jest użytkownikiem domeny (na przykład użytkownikiem lokalnym). Hybrydowe sprzężenie usługi Azure AD na urządzeniach poziomu w dół jest obsługiwane tylko dla użytkowników domeny.
   - Klient nie może połączyć się z kontrolerem domeny.    
- Osiągnięto kwotę

    ![Dołączanie do miejsca pracy dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Usługa nie odpowiada 

    ![Dołączanie do miejsca pracy dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Informacje o stanie można również znaleźć w dzienniku zdarzeń w obszarze: **Dziennik aplikacji i usług\Microsoft-Workplace Join**
  
**Najczęstsze przyczyny nieudanego hybrydowego sprzężenia usługi Azure AD to:** 

- Komputer nie jest połączony z siecią wewnętrzną organizacji ani z siecią VPN z połączeniem z lokalnym kontrolerem domeny usługi AD.
- Użytkownik jest zalogowany do komputera przy użyciu konta komputera lokalnego. 
- Problemy z konfiguracją usługi: 
   - Serwer usług AD FS nie został skonfigurowany do obsługi **programu WIAORMULTIAUTHN**. 
   - Las komputera nie ma obiektu Punktu połączenia usługi, który wskazuje zweryfikowaną nazwę domeny w usłudze Azure AD 
   - Lub jeśli domena jest zarządzana, bezproblemowe logowanie jednokrotne nie zostało skonfigurowane lub działa.
   - Użytkownik osiągnął limit urządzeń. 

## <a name="next-steps"></a>Następne kroki

W przypadku pytań zapoznaj się z [często zadawanymi pytaniami dotyczącymi zarządzania urządzeniami](faq.md)  
