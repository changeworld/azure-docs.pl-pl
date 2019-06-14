---
title: Wymagaj zasad ochrony aplikacji w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60356075"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Wymagaj zasad ochrony aplikacji w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego (wersja zapoznawcza)

Pracownicy używają urządzeń przenośnych zarówno osobistych i służbowych. Upewnij się, że Twoi pracownicy mogą być produktywność, ma zapobiegać utracie danych. Przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD) można chronić dane firmy przez ograniczenie dostępu do aplikacji w chmurze. Najpierw użyj aplikacji klienckich za pomocą zasad ochrony aplikacji.

W tym artykule opisano sposób konfigurowania zasad dostępu warunkowego, które mogą wymagać zasad ochrony aplikacji przed uzyskaniem dostępu do danych.

## <a name="overview"></a>Omówienie

Za pomocą [dostępu warunkowego usługi Azure AD](overview.md), można dostosować sposób autoryzowani użytkownicy mogą dostęp do zasobów. Na przykład można ograniczyć dostęp do aplikacji w chmurze do zaufanych urządzeń.

Możesz użyć [zasady ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) w celu ochrony danych firmy. Zasady ochrony aplikacji usługi Intune nie wymagają rozwiązania do zarządzania (urządzeniami przenośnymi MDM) urządzenia przenośnego. Możesz chronić swoje dane firmowe z lub bez rejestracji urządzeń w rozwiązaniu do zarządzania urządzeniami.

Dostęp warunkowy usługi Azure Active Directory ogranicza dostęp do aplikacji w chmurze dla aplikacji klienckich, które usługa Intune zgłosił do usługi Azure AD jako otrzymywanie zasad ochrony aplikacji. Na przykład można ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook, która zawiera zasady ochrony aplikacji usługi Intune.

W terminologii dostępu warunkowego, te aplikacje klienckie są znane jako chroniony za pomocą zasad *zasad ochrony aplikacji*.  

![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)

Aby uzyskać listę aplikacji chronionej przez zasady klienta, zobacz [wymaganie dotyczące zasad ochrony aplikacji](technical-reference.md#approved-client-app-requirement).

Można połączyć zasad dostępu warunkowego na podstawie ochrony aplikacji przy użyciu innych zasad, takich jak [zasady dostępu warunkowego opartego na urządzeniach](require-managed-devices.md). W ten sposób można zapewnić większą elastyczność w sposobie ochrony danych dla urządzeń osobistych i firmowych.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Zalety wymagania dostępu warunkowego na podstawie ochrony aplikacji

Podobnie jak zgodności, który jest zgłaszany przez usługę Intune dla systemów iOS i Android zarządzanego urządzenia usługi Intune, po zastosowaniu raporty do usługi Azure AD, jeśli zasady ochrony aplikacji. Dostęp warunkowy służy tych zasad kontroli dostępu. Te nowe zasady dostępu warunkowego, zasady ochrony aplikacji, zwiększa bezpieczeństwo. Chroni przed błędy administratora, takich jak:

- Użytkownicy, którzy nie mają licencji usługi Intune.
- Użytkownicy, którzy nie może odbierać zasady ochrony aplikacji usługi Intune.
- Usługa Intune aplikacji zasad ochrony aplikacji, które nie są skonfigurowane do otrzymywania zasad.


## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że znasz:

- [Wymaganie dotyczące zasad ochrony aplikacji](technical-reference.md#app-protection-policy-requirement) techniczne.
- [Zatwierdzone klienta aplikacji wymaganie](technical-reference.md#approved-client-app-requirement) techniczne.
- Podstawowe pojęcia związane z [dostępu warunkowego w usłudze Azure Active Directory](overview.md).
- Jak [konfigurowania zasad dostępu warunkowego](app-based-mfa.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć zasady dostępu warunkowego opartego na ochronę aplikacji, musisz mieć:

- Mieć pakietu Enterprise Mobility + Security lub subskrypcję usługi Azure Active Directory premium + usługi Intune.
- Upewnij się, że użytkownicy są licencjonowane na potrzeby rozwiązania Enterprise Mobility + Security lub usługi Azure AD i Intune.
- Upewnij się, że aplikacja kliencka jest skonfigurowany w usłudze Intune, aby otrzymać zasady ochrony aplikacji.
- Upewnij się, że użytkownicy są skonfigurowani w usłudze Intune, aby otrzymać zasady ochrony aplikacji usługi Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Zasady ochrony na podstawie aplikacji dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego opartego na ochronę aplikacji dla dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:

- Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android, aby nawiązać połączenie z programem Exchange.
- Otrzymuje wiadomość e-mail, która wskazuje, że dostęp jest dostępne tylko przy użyciu aplikacji Outlook.
- Pobiera aplikację za pomocą linku.
- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD.
- Jest monit o zainstalowanie Microsoft Authenticator do użytku z systemem iOS albo Portal firmy usługi Intune do użycia dla systemu Android kontynuować.
- Instaluje aplikację i zwraca do aplikacji Outlook, aby kontynuować.
- Jest monitowany o zarejestrowanie urządzenia.
- Może odbierać zasady ochrony aplikacji usługi Intune.
- Dostęp do poczty e-mail.

Żadne zasady ochrony aplikacji usługi Intune musi być w aplikacji dostęp do danych firmowych. Zasady może monitować użytkownika o ponowne uruchomienie aplikacji lub użyj dodatkowego kodu PIN. Jest to tak, jeśli zasady są skonfigurowane dla aplikacji i platformy.

### <a name="configuration"></a>Konfigurowanie

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Dla zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/01.png)

1. Wprowadź nazwę swoich zasad dostępu warunkowego.

2. W obszarze **przypisania**w **użytkowników i grup**, wybierz co najmniej jednego użytkownika lub grupy dla każdej zasady dostępu warunkowego.

3. W **aplikacje w chmurze**, wybierz opcję **Office 365 Exchange Online**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. W **warunki**, skonfiguruj **platform urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** :

    a. W **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. W **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. W obszarze **kontrole dostępu**, wybierz opcję **wymagają zasad ochrony aplikacji (wersja zapoznawcza)** .

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)
 

**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą programu ActiveSync (EAS)**

Dla zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. Wprowadź nazwę swoich zasad dostępu warunkowego.

2. W obszarze **przypisania**w **użytkowników i grup**, wybierz co najmniej jednego użytkownika lub grupy dla każdej zasady dostępu warunkowego.


3. W **aplikacje w chmurze**, wybierz opcję **Office 365 Exchange Online**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. W **warunki**, skonfiguruj **aplikacje klienckie (wersja zapoznawcza)** . 

    a. W **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

    b. W obszarze **kontrole dostępu**, wybierz opcję **wymagają zasad ochrony aplikacji (wersja zapoznawcza)** .

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)


**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Zasady ochrony lub być zgodne urządzenie aplikacji dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego aplikacji ochrony lub być zgodne urządzenie do uzyskiwania dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że:
 
- Użytkownik jest już zarejestrowany, z lub bez nich urządzeń firmowych.
- Użytkownicy, którzy nie są zarejestrowane i zarejestrowane w usłudze Azure AD za pomocą aplikacji pakietu chronione aplikacji trzeba zarejestrować urządzenie, aby uzyskać dostęp do zasobów.
- Zarejestrowani użytkownicy, którzy korzystają z chronionych aplikacji nie trzeba ponownie zarejestrować urządzenie.
- Użytkownik może odbierać zasady ochrony aplikacji usługi Intune w przeciwnym razie zarejestrowane.
- Użytkownik mogą uzyskiwać dostęp do poczty e-mail przy użyciu programu Outlook i zasady ochrony aplikacji usługi Intune w przeciwnym razie zarejestrowane.
- Użytkownik ma dostęp poczty e-mail przy użyciu programu Outlook, jeśli urządzenie jest zarejestrowane.


### <a name="configuration"></a>Konfigurowanie

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Dla zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/62.png)

1. Wprowadź nazwę swoich zasad dostępu warunkowego.

2. W obszarze **przypisania**w **użytkowników i grup**, wybierz co najmniej jednego użytkownika lub grupy dla każdej zasady dostępu warunkowego.

3. W **aplikacje w chmurze**, wybierz opcję **Office 365 Exchange Online**. 

     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. W **warunki**, skonfiguruj **platform urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** . 
 
    a. W **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. W **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. W obszarze **kontrole dostępu**, wybierz następujące opcje:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj jednej z wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/11.png)



**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online przy użyciu programu ActiveSync**

Dla zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. Wprowadź nazwę swoich zasad dostępu warunkowego.

2. W obszarze **przypisania**w **użytkowników i grup**, wybierz co najmniej jednego użytkownika lub grupy dla każdej zasady dostępu warunkowego.

3. W **aplikacje w chmurze**, wybierz opcję **Office 365 Exchange Online**. 

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. W **warunki**, skonfiguruj **aplikacje klienckie (wersja zapoznawcza)** . 

    W **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

5. W obszarze **kontrole dostępu**, wybierz następujące opcje:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj jednej z wybranych kontrolek**

     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/11.png)



**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Zasady ochrony i zgodnego urządzenia aplikacji dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego opartego na ochronę aplikacji i zgodnego urządzenia do uzyskiwania dostępu do usługi Exchange Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:
 
-   Konfiguruje poczty e-mail przy użyciu aplikacji natywnego programu pocztowego w systemie iOS lub Android, aby nawiązać połączenie z programem Exchange.
-   Otrzymuje wiadomość e-mail, który oznacza, że dostępu wymaga zarejestrowania swojego urządzenia.
-   Pliki do pobrania aplikacji Portal firmy usługi Intune oraz loguje się do portalu.
-   Sprawdza, czy wiadomości e-mail i zostanie poproszony o korzystali z aplikacji Outlook.
-   Pobieranie aplikacji Outlook.
-   Zostanie otwarty w aplikacji Outlook i wprowadza poświadczenia używane w rejestracji.
-   Może odbierać zasady ochrony aplikacji usługi Intune.
-   Dostęp do poczty e-mail przy użyciu programu Outlook i zasady ochrony aplikacji usługi Intune.

Żadne zasady ochrony aplikacji usługi Intune zostaną aktywowane przed uzyskaniem dostępu do danych firmowych. Zasady może monitować użytkownika o ponowne uruchomienie aplikacji lub użyj dodatkowego kodu PIN. Jest to tak, jeśli zasady są skonfigurowane dla aplikacji i platformy.


### <a name="configuration"></a>Konfigurowanie

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Dla zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/01.png)

1. Wprowadź nazwę swoich zasad dostępu warunkowego.

2. W obszarze **przypisania**w **użytkowników i grup**, wybierz co najmniej jednego użytkownika lub grupy dla każdej zasady dostępu warunkowego.

3. W **aplikacje w chmurze**, wybierz opcję **Office 365 Exchange Online**. 

     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. W **warunki**, skonfiguruj **platform urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** . 
 
    a. W **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. W **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. W obszarze **kontrole dostępu**, wybierz następujące opcje:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj wszystkich wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/13.png)



**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online przy użyciu programu ActiveSync**

Dla zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. Wprowadź nazwę swoich zasad dostępu warunkowego.

2. W obszarze **przypisania**w **użytkowników i grup**, wybierz co najmniej jednego użytkownika lub grupy dla każdej zasady dostępu warunkowego.

3. W **aplikacje w chmurze**, wybierz opcję **Office 365 Exchange Online**. 

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

4. W **warunki**, skonfiguruj **aplikacje klienckie (wersja zapoznawcza)** . 

    W **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **klienci programu Exchange ActiveSync**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

5. W obszarze **kontrole dostępu**, wybierz następujące opcje:

   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj wszystkich wybranych kontrolek**   
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/13.png)




**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Zasady ochrony lub aplikacji aplikacji dla usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z zasad ochrony lub zatwierdzonych aplikacji aplikacji do uzyskiwania dostępu do usługi Exchange Online i SharePoint Online.


### <a name="scenario-playbook"></a>Podręcznik dotyczący scenariusza

W tym scenariuszu założono, że użytkownik:

- Konfiguruje aplikacje klienckie, które są na liście aplikacji, które obsługują wymagania zasad ochrony aplikacji lub wymagania zatwierdzonych aplikacji.  
- Korzysta z aplikacji klienckich, które spełnia wymagania zasad ochrony aplikacji i może odbierać zasady ochrony aplikacji usługi Intune.
- Korzysta z aplikacji klienckich, które spełnia wymagania zasad zatwierdzonych aplikacji, który obsługuje zasad ochrony aplikacji usługi Intune.
- Otwiera aplikację dostępu do poczty e-mail lub dokumenty.
- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD.
- Jest monit o zainstalowanie Microsoft Authenticator do użytku z systemem iOS albo Portal firmy usługi Intune do użycia dla systemu Android, jeśli są one jeszcze zainstalowane.
- Instalacje aplikacji i może zwrócić się do aplikacji Outlook, aby kontynuować.
- Jest monitowany o zarejestrowanie urządzenia.
- Może odbierać zasady ochrony aplikacji usługi Intune.
- Dostęp do poczty e-mail przy użyciu programu Outlook i zasady ochrony aplikacji usługi Intune.
- Dostęp do witryny i dokumenty z aplikacją w taki sposób, nie na wymaganie zasad ochrony aplikacji, ale na liście wymagań zatwierdzonej aplikacji.

Przed uzyskaniem dostępu do danych firmowych, żadne zasady ochrony aplikacji usługi Intune są wymagane. Zasady może monitować użytkownika o ponowne uruchomienie aplikacji lub użyj dodatkowego kodu PIN. Jest to tak, jeśli zasady są skonfigurowane dla aplikacji i platformy.

**Uwagi**

- W tym scenariuszu można użyć, jeśli chcesz obsługiwać obie zasady ochrony, jak i oparty na aplikacji dostępu warunkowego aplikacji.
- W tym *lub* zasad i aplikacji z wymaganiami zasady ochrony aplikacji są obliczane dla dostępu przed wymagane aplikacje zatwierdzone klienta.

### <a name="configuration"></a>Konfigurowanie

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

Dla zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/62.png)

1. Wprowadź nazwę swoich zasad dostępu warunkowego.

2. W obszarze **przypisania**w **użytkowników i grup**, wybierz co najmniej jednego użytkownika lub grupy dla każdej zasady dostępu warunkowego.

3. W **aplikacje w chmurze**, wybierz opcję **Office 365 Exchange Online**. 

     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/02.png)

4. W **warunki**, skonfiguruj **platform urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** . 
 
    a. W **platform urządzeń**, wybierz opcję **Android** i **iOS**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

    b. W **aplikacje klienckie (wersja zapoznawcza)** , wybierz opcję **aplikacje mobilne i klienci stacjonarni** i **Nowocześni Klienci uwierzytelniania**.

    ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. W obszarze **kontrole dostępu**, wybierz następujące opcje:

   - **Wymagaj zatwierdzonej aplikacji klienckiej**

   - **Wymagane zasady ochrony aplikacji (wersja zapoznawcza)**

   - **Wymagaj jednej z wybranych kontrolek**
 
     ![Dostęp warunkowy](./media/app-protection-based-conditional-access/12.png)


**Krok 2. Konfigurowanie zasad ochrony aplikacji usługi Intune dla systemów iOS i aplikacji klienckich dla systemu Android**


![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [ochrona aplikacji i danych w usłudze Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md).
- Jeśli wszystko będzie gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 