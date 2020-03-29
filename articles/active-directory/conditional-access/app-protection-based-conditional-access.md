---
title: Zasady ochrony aplikacji z dostępem warunkowym — usługa Azure Active Directory
description: Dowiedz się, jak wymagać zasad ochrony aplikacji dostępu do aplikacji w chmurze za pomocą dostępu warunkowego w usłudze Azure Active Directory.
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
ms.openlocfilehash: 9859c884f6a1e22a1ac2bd21106ef51ead23fa41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080077"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Jak: Wymagaj zasad ochrony aplikacji i zatwierdzonej aplikacji klienckiej do dostępu do aplikacji w chmurze z dostępem warunkowym

Użytkownicy regularnie korzystają ze swoich urządzeń mobilnych zarówno do zadań osobistych, jak i służbowych. Upewniając się, że pracownicy mogą być produktywni, organizacje chcą również zapobiegać utracie danych z potencjalnie niezabezpieczonych aplikacji. Dzięki dostępowi warunkowemu organizacje mogą ograniczyć dostęp do zatwierdzonych (nowoczesnych aplikacji obsługujących uwierzytelnianie) z zastosowanymi do nich zasadami ochrony aplikacji usługi Intune.

W tym artykule przedstawiono dwa scenariusze konfigurowania zasad dostępu warunkowego dla zasobów, takich jak Office 365, Exchange Online i SharePoint Online.

- [Scenariusz 1: aplikacje usługi Office 365 wymagają zatwierdzonych aplikacji z zasadami ochrony aplikacji](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenariusz 2: Usługi Exchange Online i SharePoint Online wymagają zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

W dostępie warunkowym te aplikacje klienckie są znane jako chronione za pomocą zasad ochrony aplikacji. Więcej informacji na temat zasad ochrony aplikacji można znaleźć w artykule, [Omówienie zasad ochrony aplikacji](/intune/apps/app-protection-policy)

Aby uzyskać listę kwalifikujących się aplikacji klienckich, zobacz [Wymagania dotyczące zasad ochrony aplikacji](concept-conditional-access-grant.md).

> [!NOTE]
>    Klauzula lub jest używana w ramach zasad, aby umożliwić użytkownikom korzystanie z aplikacji, które obsługują **zasady ochrony aplikacji Wymagaj** lub wymagają zatwierdzonych formantów udzielania dotacji **aplikacji klienckich.** Aby uzyskać więcej informacji na temat aplikacji, które obsługują kontrolę zasad **ochrony aplikacji,** zobacz [Wymagania dotyczące zasad ochrony aplikacji](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenariusz 1: aplikacje usługi Office 365 wymagają zatwierdzonych aplikacji z zasadami ochrony aplikacji

W tym scenariuszu firma Contoso zdecydowała, że wszystkie zasoby mobilnego dostępu do zasobów usługi Office 365 muszą używać zatwierdzonych aplikacji klienckich, takich jak Outlook mobile, OneDrive i Microsoft Teams chronionych przez zasady ochrony aplikacji przed otrzymaniem dostępu. Wszyscy ich użytkownicy już logują się przy użyciu poświadczeń usługi Azure AD i mają przypisane do nich licencje, które obejmują usługi Azure AD Premium P1 lub P2 i Microsoft Intune.

Organizacje muszą wykonać następujące kroki, aby wymagać użycia zatwierdzonej aplikacji klienckiej na urządzeniach przenośnych.

**Krok 1: Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Office 365**

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy** lub **konkretna użytkownicy i grupy,** do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w** > chmurze**Uwzględnij**wybierz pozycję **Office 365 (wersja zapoznawcza)**.
1. W **obszarze Warunki**wybierz platformy **urządzeń**.
   1. Ustaw **pozycję Konfiguruj** na **Tak**.
   1. Uwzględnij **Android** i **iOS**.
1. W **obszarze Warunki**wybierz pozycję **Aplikacje klienckie (wersja zapoznawcza)**.
   1. Ustaw **pozycję Konfiguruj** na **Tak**.
   1. Wybierz pozycje **Aplikacje mobilne i klienci stacjonarni** oraz **Nowocześni klienci uwierzytelniania**.
1. W obszarze **Udziel kontrolki** > **Grant**dostępu wybierz następujące opcje:
   - **Wymagaj zatwierdzonej aplikacji klienckiej**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj jednego z wybranych formantów**
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć i włączyć zasady.

**Krok 2: Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online za pomocą usługi ActiveSync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy** lub **konkretna użytkownicy i grupy,** do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w** > chmurze**Uwzględnij**pozycję **Office 365 Exchange Online**.
1. W **warunkach:**
   1. **Aplikacje klienckie (wersja zapoznawcza)**:
      1. Ustaw **pozycję Konfiguruj** na **Tak**.
      1. Wybierz **aplikacje mobilne i klientów klasycznych** i **klientów programu Exchange ActiveSync**.
1. W obszarze **Formanty** > dostępu**Przyznanie**wybierz pozycję **Udzielić dostępu**, **Wymagaj zasad ochrony aplikacji**i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć i włączyć zasady.

**Krok 3: Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

Zapoznaj się z artykułem [Jak tworzyć i przypisywać zasady ochrony aplikacji](/intune/apps/app-protection-policies), aby wykonać kroki tworzenia zasad ochrony aplikacji dla systemów Android i iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenariusz 2: Usługi Exchange Online i SharePoint Online wymagają zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji

W tym scenariuszu firma Contoso zdecydowała, że użytkownicy mogą uzyskiwać dostęp tylko do danych poczty e-mail i programu SharePoint na urządzeniach przenośnych, o ile korzystają z zatwierdzonej aplikacji klienckiej, takiej jak Outlook mobile, chronionej przez zasady ochrony aplikacji przed otrzymaniem dostępu. Wszyscy ich użytkownicy już logują się przy użyciu poświadczeń usługi Azure AD i mają przypisane do nich licencje, które obejmują usługi Azure AD Premium P1 lub P2 i Microsoft Intune.

Organizacje muszą wykonać następujące trzy kroki, aby wymagać użycia zatwierdzonej aplikacji klienckiej na urządzeniach przenośnych i klientach programu Exchange ActiveSync.

**Krok 1: Zasady dla nowoczesnych klientów uwierzytelniania opartych na systemie Android i iOS, które wymagają użycia zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji podczas uzyskiwania dostępu do usługi Exchange Online i usługi SharePoint Online.**

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy** lub **konkretna użytkownicy i grupy,** do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w** > chmurze**Uwzględnij**wybierz pozycję **Office 365 Exchange Online** i Office **365 SharePoint Online**.
1. W **obszarze Warunki**wybierz platformy **urządzeń**.
   1. Ustaw **pozycję Konfiguruj** na **Tak**.
   1. Uwzględnij **Android** i **iOS**.
1. W **obszarze Warunki**wybierz pozycję **Aplikacje klienckie (wersja zapoznawcza)**.
   1. Ustaw **pozycję Konfiguruj** na **Tak**.
   1. Wybierz pozycje **Aplikacje mobilne i klienci stacjonarni** oraz **Nowocześni klienci uwierzytelniania**.
1. W obszarze **Udziel kontrolki** > **Grant**dostępu wybierz następujące opcje:
   - **Wymagaj zatwierdzonej aplikacji klienckiej**
   - **Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)**
   - **Wymagaj jednego z wybranych formantów**
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć i włączyć zasady.

**Krok 2: Zasady dla klientów Programu Exchange ActiveSync wymagających użycia zatwierdzonej aplikacji klienckiej.**

1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy** lub **konkretna użytkownicy i grupy,** do których chcesz zastosować te zasady. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w** > chmurze**Uwzględnij**pozycję **Office 365 Exchange Online**.
1. W **warunkach:**
   1. **Aplikacje klienckie (wersja zapoznawcza)**:
      1. Ustaw **pozycję Konfiguruj** na **Tak**.
      1. Wybierz **aplikacje mobilne i klientów klasycznych** i **klientów programu Exchange ActiveSync**.
1. W obszarze **Formanty** > dostępu**Przyznanie**wybierz pozycję **Udzielić dostępu**, **Wymagaj zasad ochrony aplikacji**i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć i włączyć zasady.

**Krok 3: Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android.**

Zapoznaj się z artykułem [Jak tworzyć i przypisywać zasady ochrony aplikacji](/intune/apps/app-protection-policies), aby wykonać kroki tworzenia zasad ochrony aplikacji dla systemów Android i iOS. 

## <a name="next-steps"></a>Następne kroki

[Co to jest dostęp warunkowy?](overview.md)

[Składniki dostępu warunkowego](concept-conditional-access-policies.md)

[Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

