---
title: Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory niższego poziomu urządzeń przyłączonych do | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory przyłączyć urządzenia niskiego poziomu.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76177972cbd002793f5d9fc4ab8bbe6ef2121e91
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106695"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia niskiego poziomu przyłączone do 

Ten artykuł ma zastosowanie tylko dla następujących urządzeń: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Dla systemu Windows 10 lub Windows Server 2016, zobacz [urządzenia systemu Windows 10 i Windows Server 2016 przyłączone do hybrydowej Rozwiązywanie problemów z usługi Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

W tym artykule założono, że masz [urządzenia przyłączone do hybrydowej skonfigurowanego w usłudze Azure Active Directory](hybrid-azuread-join-plan.md) na potrzeby następujących scenariuszy:

- Dostęp warunkowy oparty na urządzeniach


Ten artykuł zawiera w rozwiązywaniu problemów wskazówki dotyczące sposobu rozwiązania potencjalnych problemów.  

**Co należy wiedzieć:** 

- Jest dołączenie do hybrydowej usługi Azure AD dla niskiego poziomu Windows urządzeń działa nieco inaczej niż w systemie Windows 10. Wielu klientów nie należy pamiętać, że potrzebują usług AD FS (w przypadku domen federacyjnych) lub bezproblemowe logowanie Jednokrotne skonfigurowane (w przypadku domen zarządzanych).

- W przypadku klientów z domeny federacyjne Jeśli punkt połączenia usługi (SCP) została skonfigurowana w taki sposób, że wskazuje nazwę domeny zarządzanej (na przykład contoso.onmicrosoft.com, zamiast contoso.com), następnie hybrydowych usługi Azure AD Join dla urządzeń Windows niższego poziomu będzie nie działają.

- Maksymalną liczbę urządzeń na użytkownika aktualnie dotyczą również urządzeń przyłączonych do usługi Azure AD hybrydowego niskiego poziomu. 

- Tym samym urządzeniu fizycznym pojawia się wiele razy w usłudze Azure AD, gdy wielu użytkowników domeny logowania urządzeń przyłączonych do usługi Azure AD hybrydowego niskiego poziomu.  Na przykład jeśli *jdoe* i *jharnett* Zaloguj się na urządzeniu z systemem rejestracji odrębnych (DeviceID) jest tworzony dla każdego z nich w **użytkownika** karta informacje. 

- Możesz także uzyskać wiele wpisów dla urządzenia, na karcie informacje użytkownika, z powodu ponownej instalacji systemu operacyjnego lub ręcznej ponownej rejestracji.

- Wstępnej rejestracji / przyłączenia urządzeń jest skonfigurowana do wykonywania próba logowania i blokowanie / odblokowywanie. Może to być opóźnienie 5-minutowych wyzwolone przez zadania harmonogramu zadań. 

- Upewnij się, że [KB4284842](https://support.microsoft.com/help/4284842) jest zainstalowana w razie Windows 7 z dodatkiem SP1 lub Windows Server 2008 R2 z dodatkiem SP1. Ta aktualizacja zapobiega niepowodzeń uwierzytelniania przyszłych z powodu utraty dostępu klienta do klucze chronione po zmianie hasła.

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: Pobieranie stanu rejestracji 

**Aby sprawdzić stan rejestracji:**  

1. Zaloguj się przy użyciu konta użytkownika, który wykonał dołączenie do hybrydowej usługi Azure AD.

2. Otwórz okno wiersza polecenia 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

To polecenie wyświetla okno dialogowe, które zawiera szczegóły dotyczące stanu sprzężenia.

![Przyłączanie do obszaru roboczego dla Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: Oceń hybrydowego stan dołączania usługi Azure AD 

Jeśli urządzenie nie było hybrydowe przyłączone do usługi Azure AD, można spróbować wykonać dołączenie do hybrydowej usługi Azure AD, klikając przycisk "Dołącz". Jeśli próba czy dołączenie do hybrydowej usługi Azure AD nie powiedzie się, będą wyświetlane szczegóły dotyczące błędu.


**Najbardziej typowe problemy to:**

- A źle skonfigurowany usług AD FS lub Azure AD lub problemy z sieci

    ![Przyłączanie do obszaru roboczego dla Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
  - Autoworkplace.exe nie jest w stanie dyskretnie uwierzytelniania za pomocą usługi Azure AD lub AD FS. To może być spowodowane brakiem lub nieprawidłowo skonfigurowany usług AD FS (w przypadku domen federacyjnych) lub brakuje lub zostały nieprawidłowo skonfigurowane usługi Azure AD bezproblemowego logowania jednokrotnego (w przypadku domen zarządzanych) lub problemy z siecią. 
    
    - Może to być czy uwierzytelnianie wieloskładnikowe (MFA) jest włączona lub skonfigurowany dla użytkownika, a WIAORMUTLIAUTHN nie jest skonfigurowany na serwerze usług AD FS. 
     
    - Inną możliwością jest tej strony (HRD) odnajdowania obszaru macierzystego oczekuje na interakcję użytkownika, co uniemożliwia **autoworkplace.exe** w trybie dyskretnym żądania tokenu.
     
    - Możliwe, że usług AD FS i adresów URL usługi Azure AD są nieobecne w strefie intranetu programu Internet Explorer na komputerze klienckim.
     
    - Problemy z łącznością sieciową uniemożliwiają **autoworkplace.exe** dotrze usług AD FS lub adresy URL platformy Azure AD. 
     
    - **Autoworkplace.exe** wymaga od klienta bezpośredniego linii wzroku od klienta do organizacji lokalnego kontrolera domeny usługi AD, co oznacza, że dołączenie do hybrydowej usługi Azure AD powiedzie się tylko wtedy, gdy klient jest podłączony do sieci intranet w organizacji.
     
    - Twoja organizacja korzysta z usługi Azure AD bezproblemowego logowania jednokrotnego, `https://autologon.microsoftazuread-sso.com` lub `https://aadg.windows.net.nsatc.net` nie są obecne urządzenia IE ustawień sieci intranet, a **zezwala na aktualizacje na pasku stanu za pomocą skryptu** nie jest włączona dla strefy intranetowej.

- Użytkownik nie jest zarejestrowany jako użytkownik domeny

    ![Przyłączanie do obszaru roboczego dla Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Istnieje kilka różnych powodów dlaczego taka sytuacja może wystąpić:
    
    - Zalogowany użytkownik nie jest użytkownikiem domeny (na przykład użytkownik lokalny). Na urządzeniach niskiego poziomu, dołączenie do usługi Azure AD hybrydowej jest obsługiwana tylko dla użytkowników domeny.
    
    - Klient nie jest w stanie połączyć się z kontrolerem domeny.    

- Osiągnięto limit przydziału

    ![Przyłączanie do obszaru roboczego dla Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Usługa nie odpowiada. 

    ![Przyłączanie do obszaru roboczego dla Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Można również znaleźć informacje o stanie w dzienniku zdarzeń: **Aplikacje i usługi Log\Microsoft — dołączanie**
  
**Najczęstszymi przyczynami sprzężenia nie powiodło się hybrydowej usługi Azure AD są:** 

- Komputer nie jest podłączony do sieci wewnętrznej organizacji lub sieci VPN z połączeniem z usługą lokalną kontrolerem domeny usługi AD.

- Użytkownik jest zalogowany do komputera przy użyciu konta komputera lokalnego. 

- Problemy z konfiguracją usługi: 

  - Serwer usług AD FS nie został skonfigurowany do obsługi **WIAORMULTIAUTHN**. 

  - Las komputera nie ma żadnych obiektu punktu połączenia usługi, który wskazuje nazwę zweryfikowanej domeny w usłudze Azure AD 
  
  - Lub jeśli odbywa się domenę, a następnie bezproblemowe logowanie Jednokrotne nie zostało skonfigurowane lub nie działa.

  - Użytkownik osiągnął limit urządzeń. 

## <a name="next-steps"></a>Kolejne kroki

Masz pytania, zobacz [zarządzanie urządzeniami — często zadawane pytania](faq.md)  
