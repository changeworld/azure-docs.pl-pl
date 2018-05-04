---
title: Rozwiązywanie problemów z hybrydowe usługi Azure Active Directory niższego poziomu urządzeniach przyłączonych do | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z hybrydowe usługi Azure Active Directory połączone urządzenia niższego poziomu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 43c1907bf3f9bb8eea92dc02889df24a5a0cc9e3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Rozwiązywanie problemów z hybrydowe usługi Azure Active Directory przyłączone do urządzeń niskiego poziomu 

Ten artykuł dotyczy wyłącznie następujące urządzenia: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Dla systemu Windows 10 lub Windows Server 2016, zobacz [hybrydowego Rozwiązywanie problemów z usługą Azure Active Directory przyłączone do urządzeń z systemem Windows 10 i Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md).

W tym artykule przyjęto założenie, że masz [urządzeniach przyłączonych do skonfigurowanego hybrydowe usługi Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) do obsługi następujących scenariuszy:

- Dostępu warunkowego opartego na urządzeniu

- [Enterprise roaming ustawień](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello dla firm](active-directory-azureadjoin-passport-deployment.md) 





Ten artykuł zawiera z rozwiązywaniem problemów wskazówki dotyczące rozwiązywania potencjalnych problemów.  

**Co należy wiedzieć:** 

- Maksymalna liczba urządzeń dla każdego użytkownika jest perspektywy skoncentrowanej na urządzeniach. Na przykład jeśli *jdoe* i *jharnett* logowania na urządzeniu, oddzielne rejestracji (DeviceID) jest tworzony dla każdego z nich w **użytkownika** kartę informacje.  

- Początkowe rejestracyjny / przyłączenia urządzeń jest skonfigurowany do wykonania próba logowania lub blokowanie / odblokowanie. Mogą wystąpić opóźnienia 5-minutowy wyzwalane przez zadania harmonogramu zadań. 

- Ponowna instalacja systemu operacyjnego lub ręcznej rejestracji ponownie utworzyć nowej rejestracji w usłudze Azure AD, co powoduje wiele wpisów w karcie informacje użytkownika w portalu Azure. 

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: Pobierz stan rejestracji 

**Aby sprawdzić stan rejestracji:**  

1. Logowanie się przy użyciu konta użytkownika, który wykonał sprzężenia hybrydowe usługi Azure AD.

2. Otwórz wiersz polecenia jako administrator 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

To polecenie wyświetla okno dialogowe, która udostępnia szczegółowe informacje o stanie sprzężenia.

![Dołącz do miejsca pracy dla systemu Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: Ocena hybrydowe usługi Azure AD join stanu 

Jeśli sprzężenie hybrydowe usługi Azure AD nie powiodło się, okno dialogowe zawiera szczegółowe informacje o problemie, który wystąpił.

**Najbardziej typowe problemy są:**

- Nieprawidłowej konfiguracji usług AD FS lub Azure AD

    ![Dołącz do miejsca pracy dla systemu Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Użytkownik nie jest zarejestrowany jako użytkownik domeny

    ![Dołącz do miejsca pracy dla systemu Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Istnieje kilka przyczyn dlaczego taka sytuacja może wystąpić:
    
    - Zalogowany użytkownik nie jest użytkownikiem domeny (na przykład użytkownik lokalny). Hybrydowe usługi Azure AD join na niższym poziomie urządzeń jest obsługiwane tylko dla użytkowników domeny.
    
    - Autoworkplace.exe nie jest w stanie dyskretnie uwierzytelniania za pomocą usługi Azure AD lub AD FS. Może to być spowodowane wyświetlania problemy z połączeniem sieciowym wyjściowego powiązanej Azure URL AD (Sprawdź wymagania wstępne). Przyczyną mogą być również uwierzytelnianie wieloskładnikowe (MFA) jest włączona/skonfigurowanego dla użytkownika, a WIAORMUTLIAUTHN nie jest skonfigurowany na serwerze federacyjnym (kroki konfiguracji wyboru). Inną możliwością jest stronę odnajdowania (obszaru macierzystego HRD) tego obszaru macierzystego oczekuje do interakcji z użytkownikiem, który zapobiega **autoworkplace.exe** w trybie dyskretnym uzyskiwania tokenu.
    
    - Twoja organizacja korzysta z usługi Azure AD bezproblemowe logowanie jednokrotne, `https://autologon.microsoftazuread-sso.com` nie znajduje się na urządzeniu IE ustawienia sieci intranet, a **Zezwalaj na pasku stanu za pomocą skryptu aktualizacji** nie jest włączona dla strefy Intranet.

- Osiągnięto limit przydziału

    ![Dołącz do miejsca pracy dla systemu Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Usługa nie odpowiada. 

    ![Dołącz do miejsca pracy dla systemu Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Można również znaleźć informacje o stanie w dzienniku zdarzeń: **aplikacji i usług Log\Microsoft-dołączanie do miejsca pracy**
  
**Najczęstszymi przyczynami sprzężenia nie powiodło się hybrydowej usługi Azure AD są:** 

- Komputer nie jest podłączony do sieci wewnętrznej organizacji ani sieci VPN z połączeniem z lokalnymi kontroler domeny usługi AD.

- Użytkownik jest zalogowany na komputerze przy użyciu konta komputera lokalnego. 

- Problemy z konfiguracją usługi: 

  - Serwer federacyjny został skonfigurowany do obsługi **WIAORMULTIAUTHN**. 

  - Las komputera ma ma obiektu punktu połączenia usługi, który wskazuje nazwę domeny zweryfikowane w usłudze Azure AD 

  - Użytkownik osiągnął limit urządzeń. 

## <a name="next-steps"></a>Kolejne kroki

Odpowiedzi na pytania, zobacz [zarządzanie urządzeniami — często zadawane pytania](device-management-faq.md)  
