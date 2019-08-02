---
title: Jak wymagać zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak wymagać zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45eeef7d96f194e224e5b44421e73eb5ee5d9c0d
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515147"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Instrukcje: Wymagaj zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego 

Pracownicy używają urządzeń przenośnych zarówno do zadań osobistych, jak i służbowych. Zapewniając, że pracownicy mogą pracować wydajnie, możesz również zapobiec utracie danych. Za pomocą dostępu warunkowego usługi Azure Active Directory (Azure AD) można ograniczyć dostęp do aplikacji w chmurze do zatwierdzonych aplikacji klienckich, które mogą chronić dane firmowe.  

W tym temacie opisano sposób konfigurowania zasad dostępu warunku, które wymagają zatwierdzonych aplikacji klienckich.

## <a name="overview"></a>Przegląd

Za pomocą [dostępu warunkowego usługi Azure AD](overview.md)możesz dostosować sposób, w jaki autoryzowani użytkownicy mogą uzyskiwać dostęp do zasobów. Można na przykład ograniczyć dostęp do aplikacji w chmurze do zaufanych urządzeń.

[Zasady ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) mogą pomóc w ochronie danych firmy. Zasady ochrony aplikacji usługi Intune nie wymagają rozwiązania do zarządzania urządzeniami przenośnymi (MDM), które umożliwia ochronę danych firmy za pomocą lub bez rejestracji urządzeń w rozwiązaniu do zarządzania urządzeniami.

Azure Active Directory dostęp warunkowy umożliwia ograniczenie dostępu do aplikacji w chmurze do aplikacji klienckich, które obsługują zasady ochrony aplikacji usługi Intune. Na przykład możesz ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook.

W terminologii dostępu warunkowego te aplikacje klienckie są nazywane zatwierdzonymi **aplikacjami klienckimi**.  

![Dostęp warunkowy](./media/app-based-conditional-access/05.png)

Aby uzyskać listę zatwierdzonych aplikacji klienckich, zobacz [wymagania dotyczące zatwierdzonej aplikacji klienckiej](technical-reference.md#approved-client-app-requirement).

Zasady dostępu warunkowego opartego na aplikacji można łączyć z innymi zasadami, takimi jak [zasady dostępu warunkowego oparte na urządzeniach](require-managed-devices.md) , aby zapewnić elastyczność w zakresie ochrony danych zarówno na urządzeniach osobistych, jak i firmowych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym temacie założono, że znasz:

- Informacje techniczne [dotyczące zatwierdzonej aplikacji klienckiej](technical-reference.md#approved-client-app-requirement) .
- Podstawowe pojęcia związane z [dostępem warunkowym w Azure Active Directory](overview.md).
- Jak [skonfigurować zasady dostępu warunkowego](app-based-mfa.md).
- [Migracja zasad dostępu warunkowego](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć zasady dostępu warunkowego opartego na aplikacji, musisz mieć Enterprise Mobility + Security Azure Active Directory lub subskrypcję Premium, a użytkownicy muszą mieć licencję na usługi EMS lub Azure AD. 

## <a name="exchange-online-policy"></a>Zasady usługi Exchange Online 

Ten scenariusz składa się z zasad dostępu warunkowego opartego na aplikacji dla dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że użytkownik:

- Konfiguruje pocztę e-mail przy użyciu natywnej aplikacji poczty w systemach iOS i Android w celu nawiązania połączenia z programem Exchange
- Odbiera wiadomość e-mail wskazującą, że dostęp jest dostępny tylko przy użyciu aplikacji Outlook
- Pobiera aplikację za pomocą linku
- Otwiera aplikację Outlook i loguje się przy użyciu poświadczeń usługi Azure AD
- Zostanie wyświetlony monit o zainstalowanie uwierzytelniania (iOS) lub Portal firmy (Android) w celu kontynuowania
- Instaluje aplikację i może powrócić do aplikacji Outlook, aby kontynuować
- Jest monitowany o zarejestrowanie urządzenia
- Jest w stanie uzyskać dostęp do poczty e-mail

Wszystkie zasady ochrony aplikacji usługi Intune są aktywowane w momencie uzyskiwania dostępu do danych firmowych i mogą monitować użytkownika o ponowne uruchomienie aplikacji, użycie dodatkowego numeru PIN itp. (jeśli jest skonfigurowany dla aplikacji i platformy).

### <a name="configuration"></a>Konfigurowanie 

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet Office 365 Exchange Online**.
1. **Przyczyn** W przypadku **warunków**należy skonfigurować **platformy urządzeń** i **aplikacje klienckie**:
   1. Jako **platformy urządzeń**wybierz pozycję **Android** i **iOS**.
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i aplikacje klasyczne** oraz **nowoczesne komputery klienckie uwierzytelniania**.
1. Jako **kontroli dostępu**musisz mieć wybraną pozycję **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** .

   ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)

**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z funkcją Active Sync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet Office 365 Exchange Online**.
1. **Przyczyn** Zgodnie z **warunkami**należy skonfigurować **aplikacje klienckie (wersja zapoznawcza)** . 
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.
   1. Jako **kontroli dostępu**musisz mieć wybraną pozycję **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** .

      ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)

**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

Aby uzyskać więcej informacji [, zobacz Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="exchange-online-and-sharepoint-online-policy"></a>Zasady usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z dostępu warunkowego za pomocą zasad zarządzania aplikacjami mobilnymi w celu uzyskiwania dostępu do usługi Exchange Online i SharePoint Online z zatwierdzonymi aplikacjami.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że użytkownik:

- Próbuje nawiązać połączenie za pomocą aplikacji SharePoint, a także wyświetlić ich Lokacje firmowe
- Próbuje zalogować się przy użyciu tych samych poświadczeń, które są poświadczeniami aplikacji Outlook
- Nie musi ponownie rejestrować i uzyskać dostępu do zasobów

### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usług Exchange Online i SharePoint Online**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet office 365 Exchange Online** i **pakiet Office 365 SharePoint Online**. 
1. **Przyczyn** W przypadku **warunków**należy skonfigurować **platformy urządzeń** i **aplikacje klienckie**:
   1. Jako **platformy urządzeń**wybierz pozycję **Android** i **iOS**.
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.
1. Jako **kontroli dostępu**musisz mieć wybraną pozycję **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** .

   ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)

**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z funkcją Active Sync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet Office 365 Exchange Online**. Online 
1. **Przyczyn** W przypadku **warunków**należy skonfigurować **aplikacje klienckie**:
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.
   1. Jako **kontroli dostępu**musisz mieć wybraną pozycję **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** .

      ![Dostęp warunkowy](./media/app-based-conditional-access/05.png)

**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

![Dostęp warunkowy](./media/app-based-conditional-access/09.png)

Aby uzyskać więcej informacji [, zobacz Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zasady zgodne z aplikacjami lub urządzeniami dla usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z zasad dostępu warunkowego na podstawie aplikacji lub zgodnego urządzenia na potrzeby dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że:
 
- Niektórzy użytkownicy są już zarejestrowani (z urządzeniami firmowymi lub bez nich)
- Użytkownicy, którzy nie są zarejestrowani i zarejestrowani w usłudze Azure AD przy użyciu aplikacji chronionej przez aplikację, muszą zarejestrować urządzenie w celu uzyskania dostępu do zasobów
- Zarejestrowani użytkownicy korzystający z aplikacji chronionej przez aplikację nie muszą ponownie rejestrować urządzenia

### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usług Exchange Online i SharePoint Online**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet office 365 Exchange Online** i **pakiet Office 365 SharePoint Online**. 
1. **Przyczyn** W przypadku **warunków**należy skonfigurować **platformy urządzeń** i **aplikacje klienckie**. 
   1. Jako **platformy urządzeń**wybierz pozycję **Android** i **iOS**.
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.
1. Jako **kontroli dostępu**należy wybrać następujące elementy:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
   - **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)**
   - **Wymagaj jednej z wybranych kontrolek**   
 
      ![Dostęp warunkowy](./media/app-based-conditional-access/11.png)

**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z funkcją Active Sync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet Office 365 Exchange Online**. 
1. **Przyczyn** Zgodnie z **warunkami**należy skonfigurować **aplikacje klienckie**. 
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.
1. Jako **kontroli dostępu**musisz mieć wybraną pozycję **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)** .
 
   ![Dostęp warunkowy](./media/app-based-conditional-access/11.png)

**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

![Dostęp warunkowy](./media/app-based-conditional-access/09.png)

Aby uzyskać więcej informacji [, zobacz Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zasady aplikacji i zgodne urządzenia dla usługi Exchange Online i SharePoint Online

Ten scenariusz składa się z zasad dostępu warunkowego na podstawie aplikacji i zgodnych urządzeń w celu uzyskania dostępu do usługi Exchange Online.

### <a name="scenario-playbook"></a>Element PlayBook scenariusza

W tym scenariuszu przyjęto założenie, że użytkownik:
 
- Konfiguruje pocztę e-mail przy użyciu natywnej aplikacji poczty w systemach iOS i Android w celu nawiązania połączenia z programem Exchange
- Odbiera wiadomość e-mail z informacją, że dostęp wymaga zarejestrowania urządzenia
- Pobiera Portal firmy i loguje się do portalu firmy
- Sprawdza pocztę i prosi o korzystanie z aplikacji Outlook
- Pobiera aplikację Outlook
- Otwiera aplikację Outlook i wprowadza poświadczenia używane podczas rejestracji
- Użytkownik może uzyskać dostęp do poczty e-mail

Wszystkie zasady ochrony aplikacji usługi Intune są aktywowane w momencie uzyskiwania dostępu do danych firmowych i mogą monitować użytkownika o ponowne uruchomienie aplikacji, użycie dodatkowego numeru PIN itp. (jeśli jest skonfigurowany dla aplikacji i platformy)

### <a name="configuration"></a>Konfigurowanie

**Krok 1 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usług Exchange Online i SharePoint Online**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet office 365 Exchange Online** i **pakiet Office 365 SharePoint Online**. 
1. **Przyczyn** W przypadku **warunków**należy skonfigurować **platformy urządzeń** i **aplikacje klienckie**. 
   1. Jako **platformy urządzeń**wybierz pozycję **Android** i **iOS**.
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i aplikacje klasyczne** oraz **nowoczesne komputery klienckie uwierzytelniania**.
1. Jako **kontroli dostępu**należy wybrać następujące elementy:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
   - **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)**
   - **Wymagaj wszystkich zaznaczonych kontrolek**   
 
      ![Dostęp warunkowy](./media/app-based-conditional-access/13.png)

**Krok 2 — Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z funkcją Active Sync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku należy skonfigurować następujące składniki:

1. **Nazwa** zasad dostępu warunkowego.
1. **Użytkownicy i grupy**: Dla każdej zasady dostępu warunkowego należy wybrać co najmniej jednego użytkownika lub grupę.
1. **Aplikacje w chmurze:** Jako aplikacje w chmurze musisz wybrać **pakiet Office 365 Exchange Online**. 
1. **Przyczyn** Zgodnie z **warunkami**należy skonfigurować **aplikacje klienckie (wersja zapoznawcza)** . 
   1. Jako **aplikacje klienckie (wersja zapoznawcza)** wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.

   ![Dostęp warunkowy](./media/app-based-conditional-access/92.png)

1. Jako **kontroli dostępu**należy wybrać następujące elementy:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
   - **Wymagaj zatwierdzonej aplikacji klienckiej (wersja zapoznawcza)**
   - **Wymagaj wszystkich zaznaczonych kontrolek**   

**Krok 3 — Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

![Dostęp warunkowy](./media/app-based-conditional-access/09.png)

Aby uzyskać więcej informacji [, zobacz Ochrona aplikacji i danych za pomocą Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).

Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md). 
