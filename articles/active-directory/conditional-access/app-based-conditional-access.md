---
title: Jak wymagać zatwierdzonych aplikacji klienckich w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wymagać zatwierdzonych aplikacji klienckich w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w usłudze Azure Active Directory.
services: active-directory
keywords: Dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd3b49dfbba29da084aec777ce022d003a8de86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112725"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Instrukcje: Wymagaj zatwierdzonych aplikacji klienckich w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego 

Pracownicy używają urządzeń przenośnych zarówno osobistych i służbowych. Upewnij się, że Twoi pracownicy mogą być produktywność, ma zapobiegać utracie danych. Przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD) można ograniczyć dostęp do aplikacji w chmurze do zatwierdzonych aplikacji klienckich, które może chronić dane firmowe.  

W tym temacie opisano sposób konfigurowania zasad dostępu warunek wymagające zatwierdzonych aplikacji klienckich.

## <a name="overview"></a>Omówienie

Za pomocą [dostępu warunkowego usługi Azure AD](overview.md), można dostosować sposób autoryzowani użytkownicy mogą dostęp do zasobów. Na przykład można ograniczyć dostęp do aplikacji w chmurze do zaufanych urządzeń.

Możesz użyć [zasady ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) w celu ochrony danych firmy. Zasady ochrony aplikacji usługi Intune nie wymagają rozwiązania do zarządzania urządzeniami przenośnymi (MDM), które umożliwia ochronę danych firmy bez rejestrowania urządzeń w rozwiązaniu do zarządzania urządzeniami.

Usługa Azure Active Directory dostępu warunkowego umożliwia ograniczanie dostępu do aplikacji w chmurze do aplikacji klienckich, które obsługują zasady ochrony aplikacji usługi Intune. Na przykład można ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook.

W terminologii dostępu warunkowego, te aplikacje klienckie są znane jako **zatwierdzonych aplikacji klienckich**.  


![Dostęp warunkowy](./media/app-based-conditional-access/05.png)


Aby uzyskać listę zatwierdzonych aplikacji klienckich, zobacz [zatwierdzone klienta aplikacji wymaganie](technical-reference.md#approved-client-app-requirement).


Można połączyć opartego na aplikacji zasad dostępu warunkowego przy użyciu zasad usługi, takie jak [zasady dostępu warunkowego opartego na urządzeniach](require-managed-devices.md) aby zapewnić większą elastyczność w sposobie ochrony danych dla urządzeń osobistych i firmowych.

 


## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym temacie założono, że znasz:

- [Zatwierdzone klienta aplikacji wymaganie](technical-reference.md#approved-client-app-requirement) techniczne.


- Podstawowe pojęcia związane z [dostępu warunkowego w usłudze Azure Active Directory](overview.md).

- Jak [konfigurowania zasad dostępu warunkowego](app-based-mfa.md).

- [Migrację zasad dostępu warunkowego](best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć zasady dostępu warunkowego opartego na aplikacji, konieczne jest posiadanie pakietu Enterprise Mobility + Security lub subskrypcję usługi Azure Active Directory premium, a użytkownicy muszą mieć licencję usług EMS lub Azure AD. 


## <a name="exchange-online-policy"></a>Zasady usługi Exchange Online 

Ten scenariusz składa się z zasad dostępu warunkowego opartego na aplikacji do uzyskiwania dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:

- Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android nawiązać połączenia z programem Exchange

- Otrzymuje wiadomość e-mail, która wskazuje, że dostęp jest możliwy wyłącznie przy użyciu aplikacji Outlook

- Pliki do pobrania aplikacji z linkiem

- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD

- Jest monit o zainstalowanie aplikacji Authenticator (iOS) lub aplikacji Portal firmy (Android), aby kontynuować

- Instalacje aplikacji i możesz powrócić do aplikacji Outlook, aby kontynuować

- Jest monitowany o zarejestrowanie urządzenia

- Umożliwia dostęp do poczty e-mail

Wszystkie zasady ochrony aplikacji usługi Intune są aktywowane w momencie dostęp do danych firmowych i może monitować użytkownika o ponowne uruchomienie aplikacji, należy użyć dodatkowych itp numeru PIN (jeśli jest skonfigurowane dla aplikacji i platformy).

### <a name="configuration"></a>Konfigurowanie 

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/01.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**:

    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i aplikacje komputerowe** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)
 

**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą Active Sync (EAS)**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/06.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.


3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **aplikacje klienckie (wersja zapoznawcza)** . 

    a. Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/92.png)

    b. Jako **kontrole dostępu**, musisz mieć **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)


**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Zasady usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z dostępu warunkowego za pomocą zasad zarządzania aplikacjami mobilnymi do uzyskiwania dostępu do usługi Exchange Online i SharePoint Online przy użyciu zatwierdzonych aplikacji.

### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:

- Próbuje użyć aplikacji programu SharePoint, łączenie i wyświetlanie ich firmowych witryn

- Próby logowania przy użyciu tych samych poświadczeń, poświadczenia aplikacji Outlook

- Nie trzeba ponownie zarejestrować i uzyskać dostęp do zasobów


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online i SharePoint Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/71.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.


3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online** i **Office 365 SharePoint Online**. 

    ![Dostęp warunkowy](./media/app-based-conditional-access/02.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**:

    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)




**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą Active Sync (EAS)**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/06.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. Online 

    ![Dostęp warunkowy](./media/app-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **aplikacje klienckie**:

    a. Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/92.png)

    b. Jako **kontrole dostępu**, musisz mieć **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)




**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zasady aplikacji lub być zgodne urządzenia dla usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z aplikacji lub być zgodne urządzenie z systemem zasad dostępu warunkowego dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że:
 
- Niektóre użytkownika są już zarejestrowane (z lub bez urządzenia firmowe)

- Użytkownicy, którzy nie są zarejestrowane i zarejestrowane w usłudze Azure AD przy użyciu aplikacji chronionych wymagania aplikacji, aby zarejestrować urządzenia uzyskują dostęp do zasobów

- Zarejestrowani użytkownicy za pomocą aplikacji chronionej aplikacji, nie trzeba ponownie zarejestrować urządzenie


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online i SharePoint Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/62.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online** i **Office 365 SharePoint Online**. 

     ![Dostęp warunkowy](./media/app-based-conditional-access/02.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**. 
 
    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)**

   - **Wymagaj jednej z wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-based-conditional-access/11.png)



**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą Active Sync (EAS)**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/61.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. 

    ![Dostęp warunkowy](./media/app-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **aplikacje klienckie**. 

    Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** wybrane.
 
    ![Dostęp warunkowy](./media/app-based-conditional-access/11.png)




**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zasady aplikacji i zgodnego urządzenia dla usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z aplikacji i zgodne urządzenia z systemem zasad dostępu warunkowego dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:
 
-   Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android nawiązać połączenia z programem Exchange
-   Otrzymuje wiadomość e-mail, który oznacza, że dostępu wymaga urządzenia do zarejestrowania
-   Pliki do pobrania aplikacji portal firmy oraz loguje się do portalu firmy
-   Sprawdza, czy wiadomości e-mail i zostanie poproszony o korzystali z aplikacji Outlook
-   Pobieranie aplikacji Outlook
-   Zostanie otwarty w aplikacji Outlook i wprowadza poświadczenia używane w rejestracji
-   Użytkownik ma dostęp do poczty e-mail

Żadne zasady ochrony aplikacji usługi Intune są aktywowane w momencie dostęp do danych firmowych i może monitować użytkownika o ponowne uruchomienie aplikacji, należy użyć dodatkowych itp numeru PIN (jeśli jest skonfigurowane dla aplikacji i platformy)


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online i SharePoint Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/62.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online** i **Office 365 SharePoint Online**. 

     ![Dostęp warunkowy](./media/app-based-conditional-access/02.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**. 
 
    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i aplikacje komputerowe** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)**

   - **Wymagaj wszystkich wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-based-conditional-access/13.png)



**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą Active Sync (EAS)**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-based-conditional-access/61.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. 

    ![Dostęp warunkowy](./media/app-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **aplikacje klienckie (wersja zapoznawcza)** . 

    Jako **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-based-conditional-access/92.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)**

   - **Wymagaj wszystkich wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-based-conditional-access/64.png)




**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.






## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-based-mfa.md).

Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 
