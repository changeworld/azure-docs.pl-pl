---
title: Zasady ochrony aplikacji z dostępem warunkowym — Azure Active Directory
description: Dowiedz się, jak wymagać zasad ochrony aplikacji dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d8cdb32e04f9ba1274291430ac230107f3150c6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298381"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Instrukcje: wymaganie zasad ochrony aplikacji oraz zatwierdzonej aplikacji klienckiej do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego

Osoby regularnie korzystają z urządzeń przenośnych zarówno do zadań osobistych, jak i służbowych. Mimo że pracownicy mogą pracować wydajnie, organizacje chcą również zapobiec utracie danych z potencjalnie niezabezpieczonych aplikacji. W przypadku dostępu warunkowego organizacje mogą ograniczyć dostęp do zatwierdzonych aplikacji klienckich (nowoczesnego uwierzytelniania) przy użyciu zasad ochrony aplikacji usługi Intune.

W tym artykule przedstawiono dwa scenariusze konfigurowania zasad dostępu warunkowego dla zasobów, takich jak Office 365, Exchange Online i SharePoint Online.

- [Scenariusz 1: aplikacje pakietu Office 365 wymagają zatwierdzonych aplikacji przy użyciu zasad ochrony aplikacji](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenariusz 2: usługi Exchange Online i SharePoint Online wymagają zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

W przypadku dostępu warunkowego te aplikacje klienckie są nazywane ochroną za pomocą zasad ochrony aplikacji. Więcej informacji na temat zasad ochrony aplikacji można znaleźć w artykule [Omówienie zasad ochrony aplikacji](/intune/apps/app-protection-policy)

Aby uzyskać listę kwalifikujących się aplikacji klienckich, zobacz [wymagania dotyczące zasad ochrony aplikacji](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenariusz 1: aplikacje pakietu Office 365 wymagają zatwierdzonych aplikacji przy użyciu zasad ochrony aplikacji

W tym scenariuszu firma Contoso zdecydowała się, że wszystkie dostępy mobilne do zasobów pakietu Office 365 muszą korzystać z zatwierdzonych aplikacji klienckich, takich jak Outlook Mobile, OneDrive i Microsoft Teams chronionych przez zasady ochrony aplikacji przed uzyskaniem dostępu. Wszyscy użytkownicy logują się już przy użyciu poświadczeń usługi Azure AD i mają przypisane licencje, które obejmują Azure AD — wersja Premium P1 lub P2 i Microsoft Intune.

Aby wymagać użycia zatwierdzonej aplikacji klienckiej na urządzeniach przenośnych, organizacje muszą wykonać następujące czynności.

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla pakietu Office 365**

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **zabezpieczenia** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie**wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje lub akcje w chmurze** > **Dołącz**wybierz pozycję **Office 365 (wersja zapoznawcza)** .
1. W obszarze **warunki**wybierz pozycję **platformy urządzeń**.
   1. Ustaw **wartość** **tak**.
   1. Uwzględnij **systemy Android** i **iOS**.
1. W obszarze **warunki**wybierz pozycję **aplikacje klienckie (wersja zapoznawcza)** .
   1. Ustaw **wartość** **tak**.
   1. Wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.
1. W obszarze **kontroli dostępu** > **Udziel**wybierz następujące opcje:
   - **Wymagaj zatwierdzonej aplikacji klienckiej**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj wszystkich zaznaczonych kontrolek**
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z programem ActiveSync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

1. Przejdź do **Azure Active Directory** > **zabezpieczenia** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie**wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje lub akcje w chmurze** > **Dołącz**wybierz pozycję **Office 365 Exchange Online**.
1. W **warunkach**:
   1. **Aplikacje klienckie (wersja zapoznawcza)** :
      1. Ustaw **wartość** **tak**.
      1. Wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.
1. W obszarze **kontroli dostępu** > **Udziel**wybierz pozycję **Udziel dostępu**, **Wymagaj zatwierdzonej aplikacji klienckiej**, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

Zapoznaj się z artykułem [jak utworzyć i przypisać zasady ochrony aplikacji](/intune/apps/app-protection-policies), aby zapoznać się z procedurą tworzenia zasad ochrony aplikacji dla systemów Android i iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenariusz 2: usługi Exchange Online i SharePoint Online wymagają zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji

W tym scenariuszu firma Contoso zdecydowała się, że użytkownicy mogą uzyskiwać dostęp tylko do danych poczty e-mail i programu SharePoint na urządzeniach przenośnych, o ile korzystają z zatwierdzonej aplikacji klienckiej, takiej jak Outlook Mobile chroniona przez zasady ochrony aplikacji przed uzyskaniem dostępu. Wszyscy użytkownicy logują się już przy użyciu poświadczeń usługi Azure AD i mają przypisane licencje, które obejmują Azure AD — wersja Premium P1 lub P2 i Microsoft Intune.

Aby wymagać użycia zatwierdzonej aplikacji klienckiej na urządzeniach przenośnych i klientach programu Exchange ActiveSync, organizacje muszą wykonać następujące trzy kroki.

**Krok 1. zasady dla klientów korzystających z nowoczesnego uwierzytelniania systemu Android i iOS wymagające użycia zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji podczas uzyskiwania dostępu do usługi Exchange Online i SharePoint Online.**

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **zabezpieczenia** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie**wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje lub akcje w chmurze** > **Dołącz**wybierz pozycję **Office 365 Exchange Online** i **pakiet Office 365 SharePoint Online**.
1. W obszarze **warunki**wybierz pozycję **platformy urządzeń**.
   1. Ustaw **wartość** **tak**.
   1. Uwzględnij **systemy Android** i **iOS**.
1. W obszarze **warunki**wybierz pozycję **aplikacje klienckie (wersja zapoznawcza)** .
   1. Ustaw **wartość** **tak**.
   1. Wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **nowoczesne komputery klienckie uwierzytelniania**.
1. W obszarze **kontroli dostępu** > **Udziel**wybierz następujące opcje:
   - **Wymagaj zatwierdzonej aplikacji klienckiej**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj wszystkich zaznaczonych kontrolek**
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 2. zasady dla klientów programu Exchange ActiveSync wymagające użycia zatwierdzonej aplikacji klienckiej.**

1. Przejdź do **Azure Active Directory** > **zabezpieczenia** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie**wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje lub akcje w chmurze** > **Dołącz**wybierz pozycję **Office 365 Exchange Online**.
1. W **warunkach**:
   1. **Aplikacje klienckie (wersja zapoznawcza)** :
      1. Ustaw **wartość** **tak**.
      1. Wybierz pozycję **aplikacje mobilne i klienci stacjonarni** oraz **klienci programu Exchange ActiveSync**.
1. W obszarze **kontroli dostępu** > **Udziel**wybierz pozycję **Udziel dostępu**, **Wymagaj zatwierdzonej aplikacji klienckiej**, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android.**

Zapoznaj się z artykułem [jak utworzyć i przypisać zasady ochrony aplikacji](/intune/apps/app-protection-policies), aby zapoznać się z procedurą tworzenia zasad ochrony aplikacji dla systemów Android i iOS. 

## <a name="next-steps"></a>Następne kroki

[Co to jest dostęp warunkowy?](overview.md)

[Składniki dostępu warunkowego](concept-conditional-access-policies.md)

[Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

