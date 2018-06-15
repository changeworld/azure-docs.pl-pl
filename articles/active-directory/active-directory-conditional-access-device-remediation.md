---
title: Nie można dostać się tam z tego miejsca w witrynie Azure Portal na urządzeniu z systemem Windows | Microsoft Docs
description: Dowiedz się, dlaczego występuje sytuacja, w której nie można dostać się do danej lokalizacji z tego miejsca, i co można sprawdzić, aby to okno dialogowe nie było wyświetlane.
services: active-directory
keywords: dostęp warunkowy oparty na urządzeniach, rejestracja urządzenia, włączanie rejestracji urządzenia, rejestracja urządzenia i MDM
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 664909fa78f2034c0750492e529b21cebc087e5c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723800"
---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Nie można dostać się tam z tego miejsca na urządzeniu z systemem Windows

Na przykład podczas próby uzyskania dostępu do intranetu usługi SharePoint Online w organizacji możesz zostać wyświetlona strona z informacją o tym, że *nie można dostać się tam z tego miejsca*. Ta strona jest wyświetlana, ponieważ administrator skonfigurował zasady dostępu warunkowego, które blokują dostęp do zasobów organizacji w pewnych warunkach. W celu rozwiązania tego problemu może być konieczne skontaktowanie się z pomocą techniczną lub administratorem, ale najpierw możesz spróbować samodzielnie wykonać pewne czynności.

Jeśli używasz urządzenia z systemem **Windows**, sprawdź następujące elementy:

- Czy używasz obsługiwanej przeglądarki?

- Czy używasz obsługiwanej wersji systemu Windows na urządzeniu?

- Czy urządzenie jest zgodne?






## <a name="supported-browser"></a>Obsługiwana przeglądarka

Jeśli administrator skonfigurował zasady dostępu warunkowego, dostęp do zasobów organizacji można uzyskiwać tylko za pomocą obsługiwanej przeglądarki. Na urządzeniu z systemem Windows są obsługiwane tylko przeglądarki **Internet Explorer** i **Edge**.

Możesz łatwo sprawdzić, czy dostęp do zasobu jest niemożliwy z powodu nieobsługiwanej przeglądarki, w sekcji szczegółów na stronie błędu:

![Komunikat „Nie można dostać się tam z tego miejsca” dotyczący nieobsługiwanych przeglądarek](./media/active-directory-conditional-access-device-remediation/02.png "Scenariusz")

Jedynym wyjściem jest korzystanie z przeglądarki obsługiwanej przez aplikację na danej platformie sprzętowej. Pełna lista obsługiwanych przeglądarek jest dostępna na stronie [obsługiwanych przeglądarek](active-directory-conditional-access-supported-apps.md).  


## <a name="supported-versions-of-windows"></a>Obsługiwane wersje systemu Windows

Poniżej przedstawiono wymagania, jakie musi spełniać system operacyjny Windows na urządzeniu: 

- Jeśli na urządzeniu korzystasz z komputerowego systemu operacyjnego Windows, musi to być system Windows 7 lub nowszy.
- Jeśli na urządzeniu korzystasz z serwerowego systemu operacyjnego Windows, musi to być system Windows Server 2008 R2 lub nowszy. 


## <a name="compliant-device"></a>Zgodne urządzenie

Administrator mógł skonfigurować zasady dostępu warunkowego, które zezwalają na dostęp do zasobów organizacji tylko ze zgodnych urządzeń. Aby zapewnić zgodność, urządzenie musi być przyłączone do lokalnej usługi Active Directory lub do usługi Azure Active Directory.

Możesz łatwo sprawdzić, czy dostęp do zasobu jest niemożliwy z powodu niezgodnego urządzenia, zaglądając do sekcji szczegółów na stronie błędu:
 
![Komunikaty „Nie można dostać się tam z tego miejsca” dotyczące niezarejestrowanych urządzeń](./media/active-directory-conditional-access-device-remediation/01.png "Scenariusz")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>Urządzenie jest przyłączone do lokalnej usługi Active Directory?

**Jeśli urządzenie jest przyłączone do lokalnej usługi Active Directory w Twojej organizacji:**

1. Upewnij się, że logujesz się do systemu Windows przy użyciu Twojego konta służbowego (Twojego konta usługi Active Directory).
2. Połącz się z siecią firmową za pośrednictwem wirtualnej sieci prywatnej (VPN) lub funkcji DirectAccess.
3. Po nawiązaniu połączenia naciśnij klawisze logo Windows + L, aby zablokować sesję systemu Windows.
4. Odblokuj sesję systemu Windows, wprowadzając poświadczenia konta służbowego.
5. Zaczekaj chwilę, a następnie spróbuj ponownie, aby uzyskać dostęp do aplikacji lub usługi.
6. Jeśli zostanie wyświetlona ta sama strona, kliknij link **Więcej szczegółów**, a następnie skontaktuj się z administratorem i podaj mu szczegóły.


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>Urządzenie nie jest przyłączone do lokalnej usługi Active Directory?

Jeśli urządzenie nie jest przyłączone do lokalnej usługi Active Directory i korzysta z systemu Windows 10, są dostępne dwa rozwiązania:

* Uruchom funkcję Azure AD Join
* Dodaj swoje konto służbowe lub szkolne do systemu Windows

Aby uzyskać informacje o różnicach między tymi dwoma opcjami, zobacz [Korzystanie z urządzeń z systemem Windows 10 w miejscu pracy](active-directory-azureadjoin-windows10-devices.md).  
Możliwe są następujące sytuacje:

- Jeśli urządzenie należy do organizacji, uruchom funkcję Azure AD Join.
- Jeśli korzystasz z urządzenia osobistego lub telefonu z systemem Windows Phone, dodaj swoje konto służbowe do systemu Windows 



#### <a name="azure-ad-join-on-windows-10"></a>Funkcja Azure AD Join w systemie Windows 10

Procedura przyłączania urządzenia do usługi Azure AD zależy od wersji systemu Windows 10 używanej na tym urządzeniu. Aby określić wersję systemu operacyjnego Windows 10, uruchom polecenie **winver**: 

![Wersja systemu Windows](./media/active-directory-conditional-access-device-remediation/03.png )


**Rocznicowa aktualizacja systemu Windows 10 (wersja 1607):**

1. Otwórz aplikację **Ustawienia**.
2. Kliknij pozycję **Konta** > **Dostęp w pracy lub szkole**.
3. Kliknij przycisk **Połącz**.
4. Kliknij pozycję **Dołącz to urządzenie do usługi Azure AD**.
5. Uwierzytelnij się w organizacji, jeśli zostanie wyświetlony monit, podaj dane uwierzytelniania wieloskładnikowego, a następnie wykonaj kolejne wyświetlane kroki.
6. Wyloguj się, a następnie zaloguj się przy użyciu swojego konta służbowego.
7. Spróbuj ponownie uzyskać dostęp do aplikacji.

**Windows 10 — aktualizacja z listopada 2015 (wersja 1511):**

1. Otwórz aplikację **Ustawienia**.
2. Kliknij pozycję **System** > **Informacje**.
3. Kliknij pozycję **Przyłącz do usługi Azure AD**.
4. Uwierzytelnij się w organizacji, jeśli zostanie wyświetlony monit, podaj dane uwierzytelniania wieloskładnikowego, a następnie wykonaj kolejne wyświetlane kroki.
5. Wyloguj się, a następnie zaloguj się przy użyciu swojego konta służbowego (Twojego konta usługi Azure AD).
6. Spróbuj ponownie uzyskać dostęp do aplikacji.


#### <a name="workplace-join-on-windows-81"></a>Przyłączanie w miejscu pracy w systemie Windows 8.1

Jeśli urządzenie nie jest przyłączone do domeny i jest na nim uruchomiony system Windows 8.1, aby dołączyć to urządzenie do miejsca pracy i zarejestrować je w usłudze Microsoft Intune, wykonaj następujące czynności:

1. Otwórz okno **Ustawienia komputera**.
2. Kliknij pozycję **Sieć** > **Miejsce pracy**.
3. Kliknij pozycję **Dołącz**.
4. Uwierzytelnij się w organizacji, jeśli zostanie wyświetlony monit, podaj dane uwierzytelniania wieloskładnikowego, a następnie wykonaj kolejne wyświetlane kroki.
5. Kliknij pozycję **Włącz**.
6. Spróbuj ponownie uzyskać dostęp do aplikacji.



#### <a name="add-your-work-or-school-account-to-windows"></a>Dodaj swoje konto służbowe lub szkolne do systemu Windows 


**Rocznicowa aktualizacja systemu Windows 10 (wersja 1607):**

1. Otwórz aplikację **Ustawienia**.
2. Kliknij pozycję **Konta** > **Dostęp w pracy lub szkole**.
3. Kliknij przycisk **Połącz**.
4. Uwierzytelnij się w organizacji, jeśli zostanie wyświetlony monit, podaj dane uwierzytelniania wieloskładnikowego, a następnie wykonaj kolejne wyświetlane kroki.
5. Spróbuj ponownie uzyskać dostęp do aplikacji.


**Windows 10 — aktualizacja z listopada 2015 (wersja 1511):**

1. Otwórz aplikację **Ustawienia**.
2. Kliknij pozycję **Konta** > **Twoje konta**.
3. Kliknij pozycję **Dodaj konto służbowe**.
4. Uwierzytelnij się w organizacji, jeśli zostanie wyświetlony monit, podaj dane uwierzytelniania wieloskładnikowego, a następnie wykonaj kolejne wyświetlane kroki.
5. Spróbuj ponownie uzyskać dostęp do aplikacji.





## <a name="next-steps"></a>Następne kroki
[Dostęp warunkowy do usługi Azure Active Directory](active-directory-conditional-access-azure-portal.md)

