---
title: Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory niższego poziomu urządzeń przyłączonych do | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory przyłączyć urządzenia niskiego poziomu.
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
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3b99a82b59cbba22d30a4325e246c308a2042ad5
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136311"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia niskiego poziomu przyłączone do 

Ten artykuł ma zastosowanie tylko dla następujących urządzeń: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Dla systemu Windows 10 lub Windows Server 2016, zobacz [urządzenia systemu Windows 10 i Windows Server 2016 przyłączone do hybrydowej Rozwiązywanie problemów z usługi Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-current.md).

W tym artykule założono, że masz [urządzenia przyłączone do hybrydowej skonfigurowanego w usłudze Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) na potrzeby następujących scenariuszy:

- Dostęp warunkowy oparty na urządzeniu

- [Mobilny dostęp firmowy do ustawień](active-directory-windows-enterprise-state-roaming-overview.md)

- [Funkcja Windows Hello dla firm](active-directory-azureadjoin-passport-deployment.md) 





Ten artykuł zawiera w rozwiązywaniu problemów wskazówki dotyczące sposobu rozwiązania potencjalnych problemów.  

**Co należy wiedzieć:** 

- Maksymalna liczba urządzeń na użytkownika jest skoncentrowane na urządzeniu. Na przykład jeśli *jdoe* i *jharnett* Zaloguj się na urządzeniu z systemem rejestracji odrębnych (DeviceID) jest tworzony dla każdego z nich w **użytkownika** karta informacje.  

- Wstępnej rejestracji / przyłączenia urządzeń jest skonfigurowana do wykonywania próba logowania lub Blokowanie / odblokowywanie. Może to być opóźnienie 5-minutowych wyzwolone przez zadania harmonogramu zadań. 

- Ponowna instalacja systemu operacyjnego lub ręczna ponowna rejestracja może utworzyć nowej rejestracji w usłudze Azure AD, co skutkuje wiele wpisów na karcie informacje użytkownika w witrynie Azure portal. 

- Upewnij się, że [KB4284842](https://support.microsoft.com/en-us/help/4284842) jest zainstalowana w razie Windows 7 z dodatkiem SP1 lub Windows Server 2008 R2 z dodatkiem SP1. Ta aktualizacja zapobiega niepowodzeń uwierzytelniania przyszłych z powodu utraty dostępu klienta do klucze chronione po zmianie hasła.

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: Pobieranie stanu rejestracji 

**Aby sprawdzić stan rejestracji:**  

1. Zaloguj się przy użyciu konta użytkownika, który wykonał dołączenie do hybrydowej usługi Azure AD.

2. Otwórz wiersz polecenia jako administrator 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

To polecenie wyświetla okno dialogowe, które zapewnia więcej szczegółów na temat stanu sprzężenia.

![Przyłączanie do obszaru roboczego dla Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: Ocena hybrydowego stan dołączania usługi Azure AD 

Jeśli dołączenie do hybrydowej usługi Azure AD nie powiodło się, okno dialogowe zawiera szczegółowe informacje o problemie, który wystąpił.

**Najbardziej typowe problemy to:**

- Nieprawidłowej konfiguracji usług AD FS lub Azure AD

    ![Przyłączanie do obszaru roboczego dla Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Użytkownik nie jest zarejestrowany jako użytkownik domeny

    ![Przyłączanie do obszaru roboczego dla Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Istnieje kilka różnych powodów dlaczego taka sytuacja może wystąpić:
    
    - Zalogowany użytkownik nie jest użytkownikiem domeny (na przykład użytkownik lokalny). Na urządzeniach niskiego poziomu, dołączenie do usługi Azure AD hybrydowej jest obsługiwana tylko dla użytkowników domeny.
    
    - Autoworkplace.exe nie jest w stanie dyskretnie uwierzytelniania za pomocą usługi Azure AD lub AD FS. Może to być spowodowane problemami z łącznością sieciową wyjściowego powiązanej na adresy URL platformy Azure AD. Możliwe również, czy uwierzytelnianie wieloskładnikowe (MFA) jest włączona lub skonfigurowany dla użytkownika, a WIAORMUTLIAUTHN nie jest skonfigurowany na serwerze federacyjnym. Inną możliwością jest tej strony (HRD) odnajdowania obszaru macierzystego oczekuje na interakcję użytkownika, co uniemożliwia **autoworkplace.exe** w trybie dyskretnym uzyskiwanie tokenu.
    
    - Twoja organizacja korzysta z usługi Azure AD bezproblemowego logowania jednokrotnego, `https://autologon.microsoftazuread-sso.com` lub `https://aadg.windows.net.nsatc.net` nie są obecne urządzenia IE ustawień sieci intranet, a **zezwala na aktualizacje na pasku stanu za pomocą skryptu** nie jest włączona dla strefy intranetowej.

- Osiągnięto limit przydziału

    ![Przyłączanie do obszaru roboczego dla Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Usługa nie odpowiada. 

    ![Przyłączanie do obszaru roboczego dla Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Możesz również znaleźć informacje o stanie w dzienniku zdarzeń w obszarze: **aplikacji i usług Log\Microsoft — dołączanie**
  
**Najczęstszymi przyczynami sprzężenia nie powiodło się hybrydowej usługi Azure AD są:** 

- Komputer nie jest podłączony do sieci wewnętrznej organizacji lub sieci VPN z połączeniem z usługą lokalną kontrolerem domeny usługi AD.

- Użytkownik jest zalogowany do komputera przy użyciu konta komputera lokalnego. 

- Problemy z konfiguracją usługi: 

  - Serwer federacyjny został skonfigurowany do obsługi **WIAORMULTIAUTHN**. 

  - Las komputera nie ma żadnych obiektu punktu połączenia usługi, który wskazuje nazwę zweryfikowanej domeny w usłudze Azure AD 

  - Użytkownik osiągnął limit urządzeń. 

## <a name="next-steps"></a>Kolejne kroki

Masz pytania, zobacz [zarządzanie urządzeniami — często zadawane pytania](device-management-faq.md)  
