---
title: Azure Active Directory na podstawie aplikacji dostępu warunkowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działa dostępu warunkowego na podstawie aplikacji usługi Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: ef801b1ae2b1043e44c39f7df0d57fe4dc1aa341
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724065"
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Azure Active Directory na podstawie aplikacji dostępu warunkowego  

Pracownicy używają urządzeń przenośnych służbowych i prywatnych. Upewnić się, że pracownicy mogą być wydajni, chcesz zapobiec utracie danych. Przy użyciu usługi Azure Active Directory (Azure AD) na podstawie aplikacji dostępu warunkowego można ograniczyć dostęp do aplikacji w chmurze do aplikacji klienta, które może chronić dane firmy.  

W tym temacie opisano sposób konfigurowania dostępu warunkowego na podstawie aplikacji usługi Azure AD.

## <a name="overview"></a>Przegląd

Z [dostępu warunkowego dla usługi Azure AD](active-directory-conditional-access-azure-portal.md), można dostosować sposób autoryzowani użytkownicy mają dostęp do zasobów. Na przykład można ograniczyć dostęp do aplikacji w chmurze na zaufanych urządzeniach.

Można użyć [zasad ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) w celu ochrony danych firmy. Zasady ochrony aplikacji usługi Intune nie wymagają rozwiązania zarządzania urządzeniami przenośnymi (MDM), które umożliwia ochronę danych firmy z lub bez rejestrowania urządzeń w rozwiązaniu do zarządzania urządzeniami.

Umożliwia dostępu warunkowego opartego na aplikacji Azure Active Directory ograniczyć dostęp do aplikacji w chmurze do aplikacji klienckich, które obsługują zasad ochrony aplikacji usługi Intune. Na przykład można ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook.

W terminologii warunkowego dostępu do tych aplikacji klienta są określane jako **zatwierdzonych aplikacji klienta**.  


![Dostęp warunkowy](./media/active-directory-conditional-access-mam/05.png)


Aby uzyskać listę zatwierdzonych klienta aplikacji, zobacz [zatwierdzone wymagania aplikacji klienta](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


Można łączyć zasady dostępu warunkowego opartego na aplikacji przy użyciu innych zasad, takich jak [zasady dostępu warunkowego opartego na urządzeniu](active-directory-conditional-access-policy-connected-applications.md) zapewniające elastyczność w sposób ochrony danych dla urządzeń osobistych i firmowych.

 


## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym temacie założono, że czytelnik zna:

- [Zatwierdzone wymagania aplikacji klienta](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) technicznych.


- Podstawowe pojęcia związane z [dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- Jak [Konfigurowanie zasad dostępu warunkowego](active-directory-conditional-access-azure-portal-get-started.md).

- [Migracji zasad dostępu warunkowego](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć zasady dostępu warunkowego opartego na aplikacji, musi mieć pakietu Enterprise Mobility + Security lub subskrypcję usługi Azure Active Directory premium, a użytkownicy muszą mieć licencję EMS lub usługi Azure AD. 


## <a name="exchange-online-policy"></a>Zasady usługi Exchange Online 

W tym scenariuszu składa się z zasad dostępu warunkowego opartego na aplikacji do uzyskiwania dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Scenariusz podręcznikowym

W tym scenariuszu założono, że użytkownik:

- Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android do łączenia z programem Exchange

- Otrzymuje wiadomość e-mail, która wskazuje, czy dostęp jest dostępna tylko przy użyciu aplikacji Outlook

- Pliki do pobrania aplikacji z linkiem

- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD

- Zostanie poproszony o zainstalowanie uwierzytelniania (iOS) lub Portal firmy (Android), aby kontynuować

- Instalowanie aplikacji i może wrócić do aplikacji Outlook, aby kontynuować

- Jest monitowany o zarejestrowanie urządzenia

- Umożliwia dostęp do poczty e-mail

Wszystkie zasady ochrony aplikacji usługi Intune są aktywowane w tym czasie dostępu do danych firmowych i może Monituj użytkownika o ponowne uruchomienie aplikacji, należy użyć dodatkowych itp numeru PIN (jeśli jest skonfigurowane dla aplikacji i platform).

### <a name="configuration"></a>Konfigurowanie 

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/01.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.

3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/07.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacji klienta**:

    a. Jako **platform urządzeń**, wybierz pozycję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **aplikacjach klienckich**, wybierz pozycję **aplikacji mobilnych i aplikacje komputerowe**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/04.png)

5. Jako **dostęp do formantów**, musisz mieć **wymagają klienta zatwierdzonych aplikacji (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/05.png)
 

**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z Active Sync (EAS)**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/06.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.


3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/07.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **aplikacjach klienckich**. 

    a. Jako **aplikacjach klienckich**, wybierz pozycję **programu Exchange Active Sync**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/08.png)

    b. Jako **dostęp do formantów**, musisz mieć **wymagają klienta zatwierdzonych aplikacji (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/05.png)


**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacje klienckie z systemem Android**


![Dostęp warunkowy](./media/active-directory-conditional-access-mam/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Zasady usługi Exchange Online i SharePoint Online

W tym scenariuszu składa się z dostępu warunkowego z zasadami zarządzania aplikacjami mobilnymi do uzyskiwania dostępu do usługi Exchange Online i SharePoint Online z zatwierdzonych aplikacji.

### <a name="scenario-playbook"></a>Scenariusz podręcznikowym

W tym scenariuszu założono, że użytkownik:

- Podejmuje próbę nawiązania połączenia, a także wyświetlanie lokacjach firmy przy użyciu aplikacji SharePoint

- Próba logowania z tymi samymi poświadczeniami jako poświadczeń aplikacji Outlook

- Nie trzeba ponownie zarejestrować i aby uzyskać dostęp do zasobów


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online i SharePoint Online**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/71.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.


3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online** i **Office 365 SharePoint Online**. 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/02.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacji klienta**:

    a. Jako **platform urządzeń**, wybierz pozycję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **aplikacjach klienckich**, wybierz pozycję **aplikacji mobilnych i aplikacje komputerowe**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/04.png)

5. Jako **dostęp do formantów**, musisz mieć **wymagają klienta zatwierdzonych aplikacji (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/05.png)




**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z Active Sync (EAS)**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/06.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.

3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online**. Online 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/07.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **aplikacji klienta**:

    a. Jako **aplikacjach klienckich**, wybierz pozycję **programu Exchange Active Sync**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/08.png)

    b. Jako **dostęp do formantów**, musisz mieć **wymagają klienta zatwierdzonych aplikacji (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/05.png)




**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacje klienckie z systemem Android**


![Dostęp warunkowy](./media/active-directory-conditional-access-mam/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zasady aplikacji lub zgodnych urządzeń dla usługi Exchange Online i SharePoint Online

W tym scenariuszu składa się z aplikacji lub zgodne urządzenie zasady dostępu warunkowego dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Scenariusz podręcznikowym

W tym scenariuszu założono, że:
 
- Niektóre użytkownika są już zarejestrowane (z lub bez firmowych urządzeń)

- Użytkownicy, którzy nie są zarejestrowane i zarejestrowana w usłudze Azure AD za pomocą aplikacji chronione wymagają aplikacji zarejestrować urządzenie na dostęp do zasobów

- Nie trzeba ponownie zarejestrować urządzenie zarejestrowanych użytkowników przy użyciu aplikacji app chronione


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online i SharePoint Online**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/62.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.

3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online** i **Office 365 SharePoint Online**. 

     ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/02.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacjach klienckich**. 
 
    a. Jako **platform urządzeń**, wybierz pozycję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **aplikacjach klienckich**, wybierz pozycję **aplikacji mobilnych i aplikacje komputerowe**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/04.png)

5. Jako **dostęp do formantów**, musisz mieć następujące wybrane:

    - **Wymagaj na urządzeniu może być oznaczony jako zgodne**

    - **Wymaga aplikacji klienta zatwierdzonych (wersja zapoznawcza)**

    - **Wymagaj wybranych formantów**   
 
    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/11.png)



**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z Active Sync (EAS)**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/61.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.

3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online**. 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/07.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **aplikacjach klienckich**. 

    Jako **aplikacjach klienckich*, wybierz pozycję **programu Exchange Active Sync**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/08.png)

5. Jako **dostęp do formantów**, musisz mieć **wymagają klienta zatwierdzonych aplikacji (wersja zapoznawcza)** wybrane.
 
    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/11.png)




**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacje klienckie z systemem Android**


![Dostęp warunkowy](./media/active-directory-conditional-access-mam/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zasady aplikacji i zgodnych urządzeń dla usługi Exchange Online i SharePoint Online

W tym scenariuszu składa się z aplikacji i zgodne urządzenia zasady dostępu warunkowego dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Scenariusz podręcznikowym

W tym scenariuszu założono, że użytkownik:
 
-   Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android do łączenia z programem Exchange
-   Otrzymuje wiadomość e-mail, która wskazuje, że dostęp wymaga, aby urządzenie było zarejestrowane
-   Pobiera portalu firmy i loguje się do portalu firmy
-   Sprawdza pocztę i jest proszony o przy użyciu aplikacji Outlook
-   Pobieranie aplikacji Outlook
-   Otwiera aplikację Outlook i wprowadzenia poświadczeń używanych w rejestracji
-   Użytkownik ma dostęp do poczty e-mail

Wszystkie zasady ochrony aplikacji usługi Intune zostaną aktywowane w tym czasie dostępu do firmowych danych i może Monituj użytkownika o ponowne uruchomienie aplikacji, należy użyć dodatkowych itp. numer PIN (jeśli jest skonfigurowane dla aplikacji i platformy)


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online i SharePoint Online**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/62.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.

3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online** i **Office 365 SharePoint Online**. 

     ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/02.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacjach klienckich**. 
 
    a. Jako **platform urządzeń**, wybierz pozycję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **aplikacjach klienckich**, wybierz pozycję **aplikacji mobilnych i aplikacje komputerowe**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/04.png)

5. Jako **dostęp do formantów**, musisz mieć następujące wybrane:

    - **Wymagaj na urządzeniu może być oznaczony jako zgodne**

    - **Wymaga aplikacji klienta zatwierdzonych (wersja zapoznawcza)**

    - **Wymagaj zaznaczonych formantów**   
 
    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/13.png)



**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z Active Sync (EAS)**

Zasad dostępu warunkowego, w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/active-directory-conditional-access-mam/61.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: wszystkie zasady dostępu warunkowego muszą mieć co najmniej jeden użytkownik lub wybrana grupa.

3. **Aplikacje w chmurze:** jako aplikacje w chmurze, musisz wybrać **Office 365 usługi Exchange Online**. 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/07.png)

4. **Warunki:** jako **warunki**, należy skonfigurować **aplikacjach klienckich**. 

    Jako **aplikacjach klienckich**, wybierz pozycję **programu Exchange Active Sync**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/08.png)

5. Jako **dostęp do formantów**, musisz mieć następujące wybrane:

    - **Wymagaj na urządzeniu może być oznaczony jako zgodne**

    - **Wymaga aplikacji klienta zatwierdzonych (wersja zapoznawcza)**

    - **Wymagaj zaznaczonych formantów**   
 
    ![Dostęp warunkowy](./media/active-directory-conditional-access-mam/64.png)




**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacje klienckie z systemem Android**


![Dostęp warunkowy](./media/active-directory-conditional-access-mam/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.






## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wprowadzenie dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
