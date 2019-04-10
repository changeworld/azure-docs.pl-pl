---
title: Sposób wymagać zasad ochrony aplikacji w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wymagać zasad ochrony aplikacji w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w usłudze Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, zabezpieczenia dostępu do zasobów firmy, zasady dostępu warunkowego
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
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288504"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Instrukcje: Wymagaj zasad ochrony aplikacji w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego (wersja zapoznawcza)

Pracownicy używają urządzeń przenośnych zarówno osobistych i służbowych. Upewnij się, że Twoi pracownicy mogą być produktywność, ma zapobiegać utracie danych. Przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD) można chronić dane firmy przez ograniczenie dostępu do aplikacji w chmurze do aplikacji klienckich, które najpierw mieć zasady ochrony aplikacji.

W tym temacie opisano sposób konfigurowania zasad dostępu warunkowego, które mogą wymagać zasad ochrony aplikacji przed dostępem do danych.

## <a name="overview"></a>Przegląd

Za pomocą [dostępu warunkowego usługi Azure AD](overview.md), można dostosować sposób autoryzowani użytkownicy mogą dostęp do zasobów. Na przykład można ograniczyć dostęp do aplikacji w chmurze do zaufanych urządzeń.

Możesz użyć [zasady ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) w celu ochrony danych firmy. Zasady ochrony aplikacji usługi Intune nie wymagają rozwiązania do zarządzania urządzeniami przenośnymi (MDM), które umożliwia ochronę danych firmy bez rejestrowania urządzeń w rozwiązaniu do zarządzania urządzeniami.

Dostęp warunkowy usługi Azure Active Directory ogranicza dostęp do aplikacji w chmurze dla aplikacji klienckich, które usługa Intune zgłosił do usługi Azure AD jako otrzymywanie zasad ochrony aplikacji. Na przykład można ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook, która zawiera zasady ochrony aplikacji usługi Intune.

W terminologii dostępu warunkowego, te aplikacje klienckie są znane jako chroniony za pomocą zasad **zasad ochrony aplikacji**.  

![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)

Lista zasad chronionego klienta, zobacz [wymaganie dotyczące zasad ochrony aplikacji](technical-reference.md#approved-client-app-requirement).

Można połączyć zasad dostępu warunkowego na podstawie ochrony aplikacji przy użyciu innych zasad, takich jak [zasady dostępu warunkowego opartego na urządzeniach](require-managed-devices.md) aby zapewnić większą elastyczność w sposobie ochrony danych dla urządzeń osobistych i firmowych.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Zalety wymagania dostępu warunkowego na podstawie ochrony aplikacji

Podobnie jak zgodności zgłaszane przez usługę Intune dla systemów iOS i Android for zarządzane urządzenia, usługa Intune teraz raporty do usługi Azure AD Jeśli zastosowano zasady ochrony aplikacji, tak, aby dostęp warunkowy go użyć jako kontrolę dostępu. Nowe zasady dostępu warunkowego **zasad ochrony aplikacji** zwiększa bezpieczeństwo i ochrona przed administratora błędów, takich jak:

- Użytkownicy, którzy nie mają licencji usługi Intune
- Użytkownicy, którzy nie może odbierać zasady ochrony aplikacji usługi Intune
- Usługa Intune aplikacji zasad ochrony aplikacji, które nie zostały skonfigurowane do otrzymywania zasad


## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym temacie założono, że znasz:

- [Wymaganie dotyczące zasad ochrony aplikacji](technical-reference.md#app-protection-policy-requirement) techniczne.

- [Zatwierdzone klienta aplikacji wymaganie](technical-reference.md#approved-client-app-requirement) techniczne.

- Podstawowe pojęcia związane z [dostępu warunkowego w usłudze Azure Active Directory](overview.md).

- Jak [konfigurowania zasad dostępu warunkowego](app-based-mfa.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć zasady dostępu warunkowego opartego na ochronę aplikacji, musisz mieć
- Masz pakietu Enterprise Mobility + Security lub subskrypcję usługi Azure Active Directory premium + usługi Intune
- Upewnij się, użytkownicy są licencjonowane na potrzeby usługi EMS lub Azure AD i Intune
- Upewnij się, że aplikacja klienta została skonfigurowana w usłudze Intune, aby otrzymać zasady ochrony aplikacji
- Upewnij się, że użytkownicy są skonfigurowani w usłudze Intune, aby otrzymać zasady ochrony aplikacji usługi Intune

## <a name="app-protection-based-policy-for-exchange-online"></a>Zasady ochrony na podstawie aplikacji dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego opartego na ochronę aplikacji dla dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:

- Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android nawiązać połączenia z programem Exchange

- Otrzymuje wiadomość e-mail, która wskazuje, że dostęp jest możliwy wyłącznie przy użyciu aplikacji Outlook

- Pliki do pobrania aplikacji z linkiem

- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD

- Jest monit o zainstalowanie aplikacji Authenticator (iOS) lub aplikacji Portal firmy (Android), aby kontynuować

- Instalacje aplikacji i możesz powrócić do aplikacji Outlook, aby kontynuować

- Jest monitowany o zarejestrowanie urządzenia

- Może odbierać zasady ochrony aplikacji usługi Intune

- Umożliwia dostęp do poczty e-mail

Żadne zasady ochrony aplikacji usługi Intune musi znajdować się w aplikacji na dostęp do firmowych danych i może monitować użytkownika o ponowne uruchomienie aplikacji, należy użyć dodatkowych itp numeru PIN (jeśli jest skonfigurowane dla aplikacji i platformy).

### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/01.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**:

    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)**, wybierz opcję **aplikacje mobilne i aplikacje komputerowe** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć **wymagają zasad ochrony aplikacji (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)
 

**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą Active Sync (EAS)**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.


3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **aplikacje klienckie (wersja zapoznawcza)**. 

    a. Jako **aplikacje klienckie (wersja zapoznawcza)**, wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

    b. Jako **kontrole dostępu**, musisz mieć **wymagają zasad ochrony aplikacji (wersja zapoznawcza)** wybrane.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)


**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Zasady ochrony lub być zgodne urządzenie aplikacji dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego aplikacji ochrony lub być zgodne urządzenie do uzyskiwania dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że:
 
- Niektóre użytkownik jest już zarejestrowany (z lub bez urządzenia firmowe)

- Użytkownicy, którzy nie są zarejestrowane i zarejestrowane w usłudze Azure AD przy użyciu aplikacji chronionych wymagania aplikacji, aby zarejestrować urządzenia uzyskują dostęp do zasobów

- Zarejestrowani użytkownicy za pomocą aplikacji chronionej aplikacji, nie trzeba ponownie zarejestrować urządzenie

- Użytkownik może odbierać zasady ochrony aplikacji usługi Intune w przeciwnym razie zarejestrowane

- Użytkownika mogą uzyskiwać dostęp do poczty e-mail przy użyciu programu Outlook i zasady ochrony aplikacji usługi Intune w przeciwnym razie zarejestrowane

- Użytkownik ma dostęp do poczty e-mail przy użyciu programu Outlook, jeśli urządzenie jest zarejestrowane


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/62.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. 

     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**. 
 
    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)**, wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj jednej z wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/11.png)



**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą Active Sync (EAS)**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. 

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **aplikacje klienckie**. 

    Jako **aplikacje klienckie (wersja zapoznawcza)**, wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj jednej z wybranych kontrolek**   
    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/11.png)



**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Zasady ochrony i zgodnego urządzenia aplikacji dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego opartego na ochronę aplikacji i zgodnego urządzenia do uzyskiwania dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:
 
-   Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android nawiązać połączenia z programem Exchange
-   Otrzymuje wiadomość e-mail, który oznacza, że dostępu wymaga urządzenia do zarejestrowania
-   Pliki do pobrania aplikacji portal firmy oraz loguje się do portalu firmy
-   Sprawdza, czy wiadomości e-mail i zostanie poproszony o korzystali z aplikacji Outlook
-   Pobieranie aplikacji Outlook
-   Zostanie otwarty w aplikacji Outlook i wprowadza poświadczenia używane w rejestracji
-   Może odbierać otrzymywać zasady ochrony aplikacji usługi Intune
-   Umożliwia dostęp do poczty e-mail za pomocą programu Outlook i zasady ochrony aplikacji usługi Intune

Żadne zasady ochrony aplikacji usługi Intune zostaną aktywowane przed dostęp do danych firmowych i może monitować użytkownika o ponowne uruchomienie aplikacji, należy użyć dodatkowych itp numeru PIN (jeśli jest skonfigurowane dla aplikacji i platformy)


### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/01.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. 

     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**. 
 
    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)**, wybierz opcję **aplikacje mobilne i aplikacje komputerowe** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj wszystkich wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/13.png)



**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą Active Sync (EAS)**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. 

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **aplikacje klienckie (wersja zapoznawcza)**. 

    Jako **aplikacje klienckie (wersja zapoznawcza)**, wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)**

   - **Wymagaj wszystkich wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/13.png)




**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Zasady ochrony lub aplikacji aplikacji dla usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z zasad ochrony lub zatwierdzonych aplikacji aplikacji do uzyskiwania dostępu do usługi Exchange Online i SharePoint Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:

- Konfiguruje albo aplikacje klienckie, które są na liście aplikacji, które obsługują wymagania zasad ochrony aplikacji lub wymagania zatwierdzonych aplikacji.  
- Użytkownik używa kliencką spełniających wymagania zasad ochrony aplikacji może odbierać zasady ochrony aplikacji usługi Intune
- Użytkownik korzysta z aplikacji klienckich, które spełnia wymagania zasad zatwierdzonych aplikacji, który obsługuje zasad ochrony aplikacji usługi Intune
- Otwiera aplikację dostępu do poczty e-mail lub dokumentów
- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD
- Jest monit o zainstalowanie aplikacji Authenticator (iOS) lub aplikacji Portal firmy (Android), aby kontynuować (Jeśli nie zainstalowano)
- Instalacje aplikacji i możesz powrócić do aplikacji Outlook, aby kontynuować
- Jest monitowany o zarejestrowanie urządzenia
- Może odbierać zasady ochrony aplikacji usługi Intune
- Umożliwia dostęp do poczty e-mail za pomocą programu Outlook i zasady ochrony aplikacji usługi Intune
- Jest możliwość dostępu do witryn/dokumentów z aplikacją w taki sposób, nie na wymaganie zasad ochrony aplikacji, ale wymienione wymaganie zatwierdzonej aplikacji.

Żadne zasady ochrony aplikacji usługi Intune są wymagane do przeprowadzenia dostęp do danych firmowych i może monitować użytkownika o ponowne uruchomienie aplikacji, należy użyć dodatkowych itp numeru PIN (jeśli jest skonfigurowane dla aplikacji i platformy)

**Uwagi**

- W tym scenariuszu można użyć, jeśli chcesz obsługiwać obie zasady ochrony, jak i oparty na aplikacji dostępu warunkowego aplikacji.

- W tym *lub* zasad, aplikacji za pomocą **zasad ochrony aplikacji** wymagania są oceniane dostępu przed **zatwierdzonych aplikacji klienckich** wymagań.

### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Zasady dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/62.png)

1. **Nazwa** zasad dostępu warunkowego.

2. **Użytkownicy i grupy**: Wszystkie zasady dostępu warunkowego musi mieć co najmniej jednego użytkownika lub wybranej grupy.

3. **Aplikacje w chmurze:** Jak aplikacje w chmurze, musisz wybrać **Office 365 Exchange Online**. 

     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/02.png)

4. **Warunki:** Jako **warunki**, należy skonfigurować **platform urządzeń** i **aplikacje klienckie**. 
 
    a. Jako **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. Jako **aplikacje klienckie (wersja zapoznawcza)**, wybierz opcję **aplikacje mobilne i aplikacje komputerowe** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. Jako **kontrole dostępu**, musisz mieć następujące wybrane:

   - **Wymagaj zatwierdzonej aplikacji klienckiej**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj jednej z wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/12.png)


**Krok 2 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Aby uzyskać więcej informacji.




## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md).

Jeśli chcesz skonfigurować zasady dostępu warunkowego w swoim środowisku, zobacz [Best practices for conditional access in Azure Active Directory (Najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory)](best-practices.md). 