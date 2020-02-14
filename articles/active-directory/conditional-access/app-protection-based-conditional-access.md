---
title: Zasady ochrony aplikacji z dostępem warunkowym — Azure Active Directory
description: Dowiedz się, jak wymagać zasad ochrony aplikacji dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c853ef3a5a40381aba4e1c13eaf9ad7d8653170
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186634"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Wymagaj zasad ochrony aplikacji dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego (wersja zapoznawcza)

Pracownicy używają urządzeń przenośnych zarówno do celów służbowych, jak i prywatnych. Zapewniając, że pracownicy mogą pracować wydajnie, możesz również zapobiec utracie danych. Za pomocą dostępu warunkowego usługi Azure Active Directory (Azure AD) możesz chronić dane firmowe, ograniczając dostęp do aplikacji w chmurze. Najpierw użyj aplikacji klienckich z zasadami ochrony aplikacji.

W tym artykule wyjaśniono, jak skonfigurować zasady dostępu warunkowego, które mogą wymagać zasad ochrony aplikacji przed udzieleniem dostępu do danych.

## <a name="overview"></a>Omówienie

Za pomocą [dostępu warunkowego usługi Azure AD](overview.md)możesz dostosować sposób, w jaki autoryzowani użytkownicy mogą uzyskiwać dostęp do zasobów. Można na przykład ograniczyć dostęp do aplikacji w chmurze do zaufanych urządzeń.

[Zasady ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) mogą pomóc w ochronie danych firmy. Zasady ochrony aplikacji usługi Intune nie wymagają rozwiązania do zarządzania urządzeniami przenośnymi (MDM). Dane firmy można chronić za pomocą lub bez rejestracji urządzeń w rozwiązaniu do zarządzania urządzeniami.

Azure Active Directory dostęp warunkowy ogranicza dostęp do aplikacji w chmurze do aplikacji klienckich, które usługa Intune zgłosiła w usłudze Azure AD jako otrzymywanie zasad ochrony aplikacji. Można na przykład ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook, która ma zasady ochrony aplikacji w usłudze Intune.

W terminologii dostępu warunkowego te aplikacje klienckie są znane jako zasady chronione przy użyciu *zasad ochrony aplikacji*.  

![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)

Aby uzyskać listę aplikacji klienckich chronionych przez zasady, zobacz [wymagania dotyczące zasad ochrony aplikacji](concept-conditional-access-grant.md).

Zasady dostępu warunkowego opartego na ochronie aplikacji można łączyć z innymi zasadami, takimi jak [zasady dostępu warunkowego oparte na urządzeniach](require-managed-devices.md). W ten sposób można zapewnić elastyczność w zakresie ochrony danych zarówno na urządzeniach osobistych, jak i firmowych.

> [!NOTE]
> Zasady ochrony aplikacji dostępu warunkowego nie mogą być stosowane do użytkowników B2B, ponieważ zapraszana organizacja nie ma wglądu w organizację domową użytkownika B2B.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Zalety wymagania dostępu warunkowego opartego na ochronie aplikacji

Podobnie jak w przypadku zgodności raportowanej przez usługę Intune dla systemów iOS i Android w przypadku urządzenia zarządzanego, usługa Intune teraz raportuje usługę Azure AD w przypadku zastosowania zasad ochrony aplikacji. Dostęp warunkowy może używać tych zasad jako kontroli dostępu. Te nowe zasady dostępu warunkowego, zasady ochrony aplikacji, zwiększają bezpieczeństwo. Chroni przed błędami administratora, takimi jak:

- Użytkownicy, którzy nie mają licencji usługi Intune.
- Użytkownicy, którzy nie mogą otrzymywać zasad ochrony aplikacji usługi Intune.
- Aplikacje zasad ochrony aplikacji usługi Intune, które nie są skonfigurowane do odbierania zasad.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że znasz:

- [Wymaganie zasad ochrony aplikacji](concept-conditional-access-grant.md).
- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](concept-conditional-access-grant.md).
- Podstawowe pojęcia związane z [dostępem warunkowym w Azure Active Directory](overview.md).
- Jak [skonfigurować zasady dostępu warunkowego](app-based-mfa.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć zasady dostępu warunkowego opartego na ochronie aplikacji, należy:

- Posiadanie Enterprise Mobility + Security lub subskrypcji Azure Active Directory Premium i usługi Intune.
- Upewnij się, że użytkownicy mają licencję na Enterprise Mobility + Security lub usługę Azure AD + Intune.
- Upewnij się, że aplikacja kliencka jest skonfigurowana w usłudze Intune w celu odbierania zasad ochrony aplikacji.
- Upewnij się, że użytkownicy są skonfigurowani w usłudze Intune w celu otrzymywania zasad ochrony aplikacji usługi Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Zasady oparte na ochronie aplikacji dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego opartego na ochronie aplikacji dla dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że użytkownik:

- Konfiguruje pocztą e-mail za pomocą natywnej aplikacji poczty w systemach iOS i Android w celu nawiązania połączenia z programem Exchange.
- Odbiera wiadomość e-mail wskazującą, że dostęp jest dostępny tylko przy użyciu aplikacji Outlook.
- Pobiera aplikację za pomocą linku.
- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD.
- Zostanie wyświetlony monit o zainstalowanie **aplikacji Microsoft Authenticator** lub **Intune — portal firmy** w celu kontynuowania.
- Instaluje aplikację i zwraca do aplikacji Outlook, aby kontynuować.
- Zostanie wyświetlony monit o zarejestrowanie urządzenia.
- Może otrzymać zasady ochrony aplikacji usługi Intune.
- Może uzyskać dostęp do poczty e-mail.

Aby uzyskać dostęp do danych firmowych, wszystkie zasady ochrony aplikacji usługi Intune muszą znajdować się w aplikacji. Zasady mogą monitować użytkownika o ponowne uruchomienie aplikacji lub użycie dodatkowego numeru PIN. Dotyczy to sytuacji, gdy zasady są skonfigurowane dla aplikacji i platformy.

### <a name="configuration"></a>Konfiguracja

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/01.png)

1. Wprowadź nazwę zasad dostępu warunkowego.
1. W obszarze **przypisania**, w obszarze **Użytkownicy i grupy**wybierz co najmniej jednego użytkownika lub grupę dla każdej zasady dostępu warunkowego.
1. W obszarze **aplikacje w chmurze**wybierz pozycję **Office 365 Exchange Online**.

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

1. W obszarze **warunki**Skonfiguruj **platformy urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** :
   1. W obszarze **platformy urządzeń**wybierz pozycję **Android** i **iOS**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

   1. W obszarze **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

1. W obszarze **kontroli dostępu**wybierz pozycję **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)** .

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)

**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z programem ActiveSync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. Wprowadź nazwę zasad dostępu warunkowego.
1. W obszarze **przypisania**, w obszarze **Użytkownicy i grupy**wybierz co najmniej jednego użytkownika lub grupę dla każdej zasady dostępu warunkowego.
1. W obszarze **aplikacje w chmurze**wybierz pozycję **Office 365 Exchange Online**.

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

1. W obszarze **warunki**Skonfiguruj **aplikacje klienckie (wersja zapoznawcza)** . 

   1. W obszarze **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

   1. W obszarze **kontroli dostępu**wybierz pozycję **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)** .

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/05.png)

**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Zasady dotyczące ochrony aplikacji lub zgodnego urządzenia dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego opartego na ochronie aplikacji lub zgodności urządzeń na potrzeby dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że:
 
- Użytkownik jest już zarejestrowany, z lub bez urządzeń firmowych.
- Użytkownicy, którzy nie są zarejestrowani i zarejestrowani w usłudze Azure AD przy użyciu aplikacji chronionej przez aplikację, muszą zarejestrować urządzenie w celu uzyskania dostępu do zasobów.
- Zarejestrowani użytkownicy korzystający z aplikacji chronionej przez aplikację nie muszą ponownie rejestrować urządzenia.
- Użytkownik może otrzymać zasady ochrony aplikacji usługi Intune, jeśli nie zostały zarejestrowane.
- Użytkownik może uzyskać dostęp do poczty e-mail przy użyciu programu Outlook i zasad ochrony aplikacji usługi Intune, jeśli nie zostały zarejestrowane.
- Użytkownik może uzyskać dostęp do poczty e-mail w programie Outlook, jeśli urządzenie jest zarejestrowane.

### <a name="configuration"></a>Konfiguracja

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/62.png)

1. Wprowadź nazwę zasad dostępu warunkowego.
1. W obszarze **przypisania**, w obszarze **Użytkownicy i grupy**wybierz co najmniej jednego użytkownika lub grupę dla każdej zasady dostępu warunkowego.
1. W obszarze **aplikacje w chmurze**wybierz pozycję **Office 365 Exchange Online**. 

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

1. W obszarze **warunki**Skonfiguruj **platformy urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** . 
 
   1. W obszarze **platformy urządzeń**wybierz pozycję **Android** i **iOS**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

   1. W obszarze **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

5. W obszarze **kontroli dostępu**wybierz następujące opcje:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj jednej z wybranych kontrolek**   
 
      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/11.png)

**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online przy użyciu programu ActiveSync**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. Wprowadź nazwę zasad dostępu warunkowego.
1. W obszarze **przypisania**, w obszarze **Użytkownicy i grupy**wybierz co najmniej jednego użytkownika lub grupę dla każdej zasady dostępu warunkowego.
1. W obszarze **aplikacje w chmurze**wybierz pozycję **Office 365 Exchange Online**. 

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

1. W obszarze **warunki**Skonfiguruj **aplikacje klienckie (wersja zapoznawcza)** . 

   W obszarze **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

1. W obszarze **kontroli dostępu**wybierz następujące opcje:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj jednej z wybranych kontrolek**

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/11.png)

**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Zasady dotyczące ochrony aplikacji i zgodnego urządzenia dla usługi Exchange Online

Ten scenariusz składa się z zasad dostępu warunkowego opartego na aplikacji i zgodności urządzeń w celu uzyskania dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że użytkownik:
 
- Konfiguruje pocztą e-mail za pomocą natywnej aplikacji poczty w systemach iOS i Android w celu nawiązania połączenia z programem Exchange.
- Odbiera wiadomość e-mail z informacją, że dostęp wymaga zarejestrowania urządzenia.
- Pobiera Intune — Portal firmy i loguje się do portalu.
- Sprawdza pocztę i prosi o korzystanie z aplikacji Outlook.
- Pobiera aplikację Outlook.
- Otwiera aplikację Outlook i wprowadza poświadczenia używane podczas rejestracji.
- Może otrzymać zasady ochrony aplikacji usługi Intune.
- Może uzyskać dostęp do poczty e-mail przy użyciu programu Outlook i zasad ochrony aplikacji usługi Intune.

Wszystkie zasady ochrony aplikacji usługi Intune są aktywowane przed udzieleniem dostępu do danych firmowych. Zasady mogą monitować użytkownika o ponowne uruchomienie aplikacji lub użycie dodatkowego numeru PIN. Dotyczy to sytuacji, gdy zasady są skonfigurowane dla aplikacji i platformy.

### <a name="configuration"></a>Konfiguracja

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/01.png)

1. Wprowadź nazwę zasad dostępu warunkowego.
1. W obszarze **przypisania**, w obszarze **Użytkownicy i grupy**wybierz co najmniej jednego użytkownika lub grupę dla każdej zasady dostępu warunkowego.
1. W obszarze **aplikacje w chmurze**wybierz pozycję **Office 365 Exchange Online**. 

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

1. W obszarze **warunki**Skonfiguruj **platformy urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** . 
   1. W obszarze **platformy urządzeń**wybierz pozycję **Android** i **iOS**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

   1. W obszarze **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

1. W obszarze **kontroli dostępu**wybierz następujące opcje:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj wszystkich zaznaczonych kontrolek**   
 
      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/13.png)

**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online przy użyciu programu ActiveSync**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/06.png)

1. Wprowadź nazwę zasad dostępu warunkowego.
1. W obszarze **przypisania**, w obszarze **Użytkownicy i grupy**wybierz co najmniej jednego użytkownika lub grupę dla każdej zasady dostępu warunkowego.
1. W obszarze **aplikacje w chmurze**wybierz pozycję **Office 365 Exchange Online**. 

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/07.png)

1. W obszarze **warunki**Skonfiguruj **aplikacje klienckie (wersja zapoznawcza)** . 

   W obszarze **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/92.png)

1. W obszarze **kontroli dostępu**wybierz następujące opcje:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj wszystkich zaznaczonych kontrolek**   
 
      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/13.png)

**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Zasady ochrony aplikacji lub oparte na aplikacji dla usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z zasad dotyczących ochrony aplikacji lub aplikacji zatwierdzonych w celu uzyskania dostępu do usługi Exchange Online i SharePoint Online.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że użytkownik:

- Konfiguruje aplikacje klienckie, które znajdują się na liście aplikacji, które obsługują wymagania zasad ochrony aplikacji lub wymagania dotyczące zatwierdzonych aplikacji.  
- Program korzysta z aplikacji klienckich, które spełniają wymagania zasad ochrony aplikacji i mogą otrzymywać zasady ochrony aplikacji usługi Intune.
- Program korzysta z aplikacji klienckich, które spełniają wymagania zasad zatwierdzonych aplikacji, które obsługują zasady ochrony aplikacji usługi Intune.
- Otwiera aplikację w celu uzyskania dostępu do poczty e-mail lub dokumentów.
- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD.
- Zostanie wyświetlony monit o zainstalowanie Microsoft Authenticator dla systemu iOS lub Intune — Portal firmy do użycia w systemie Android, jeśli nie są one jeszcze zainstalowane.
- Instaluje aplikację i może powrócić do aplikacji Outlook, aby kontynuować.
- Zostanie wyświetlony monit o zarejestrowanie urządzenia.
- Może otrzymać zasady ochrony aplikacji usługi Intune.
- Może uzyskać dostęp do poczty e-mail przy użyciu programu Outlook i zasad ochrony aplikacji usługi Intune.
- Może uzyskiwać dostęp do witryn i dokumentów z aplikacją, która nie znajduje się w wymaganiu zasad ochrony aplikacji, ale wymienionej w wymaganej aplikacji.

Przed uzyskaniem dostępu do danych firmowych wymagane są wszystkie zasady ochrony aplikacji usługi Intune. Zasady mogą monitować użytkownika o ponowne uruchomienie aplikacji lub użycie dodatkowego numeru PIN. Dotyczy to sytuacji, gdy zasady są skonfigurowane dla aplikacji i platformy.

**Uwagi**

- Tego scenariusza można użyć, jeśli chcesz obsługiwać zasady dostępu warunkowego opartego na ochronie aplikacji i aplikacji.
- W ramach tego *lub* zasad aplikacje z wymaganiem zasad ochrony aplikacji są oceniane pod kątem dostępu najpierw przed wymaganiem zatwierdzonych aplikacji klienckich.

### <a name="configuration"></a>Konfiguracja

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usług Exchange Online i SharePoint Online**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

![Dostęp warunkowy](./media/app-protection-based-conditional-access/62.png)

1. Wprowadź nazwę zasad dostępu warunkowego.
1. W obszarze **przypisania**, w obszarze **Użytkownicy i grupy**wybierz co najmniej jednego użytkownika lub grupę dla każdej zasady dostępu warunkowego.
1. W obszarze **aplikacje w chmurze**wybierz pozycję **Office 365 Exchange Online** i **pakiet Office 365 SharePoint Online**. 

   ![Dostęp warunkowy](./media/app-protection-based-conditional-access/02.png)

1. W obszarze **warunki**Skonfiguruj **platformy urządzeń** i **aplikacje klienckie (wersja zapoznawcza)** . 
   1. W obszarze **platformy urządzeń**wybierz pozycję **Android** i **iOS**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/03.png)

   1. W obszarze **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.

      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/91.png)

1. W obszarze **kontroli dostępu**wybierz następujące opcje:
   - **Wymagaj zatwierdzonej aplikacji klienckiej**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj jednej z wybranych kontrolek**
 
      ![Dostęp warunkowy](./media/app-protection-based-conditional-access/12.png)

**Krok 2. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

![Dostęp warunkowy](./media/app-protection-based-conditional-access/09.png)

Aby uzyskać więcej informacji, zobacz [Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md).
