---
title: Najważniejsze wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się więcej o rzeczach, które należy wiedzieć i jakich czynności należy unikać podczas konfigurowania zasad dostępu warunkowego.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295357"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Najważniejsze wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory

Dzięki [dostępowi warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)można kontrolować sposób uzyskiwania dostępu autoryzowanych użytkowników do aplikacji w chmurze. Ten artykuł zawiera informacje na temat:

- Rzeczy, które powinieneś wiedzieć 
- Co to jest należy unikać podczas konfigurowania zasad dostępu warunkowego. 

W tym artykule założono, że znasz pojęcia i terminologii opisane w [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Co jest wymagane do działania polisy?

Podczas tworzenia nowych zasad nie wybrano użytkowników, grup, aplikacji ani kontrolek dostępu.

![Aplikacje w chmurze](./media/best-practices/02.png)

Aby zasady działały, należy skonfigurować:

| Elementy           | W jaki sposób?                                  | Dlaczego |
| :--            | :--                                  | :-- |
| **Aplikacje w chmurze** |Wybierz jedną lub więcej aplikacji.  | Celem zasad dostępu warunkowego jest umożliwienie kontrolowania sposobu, w jaki autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze.|
| **Użytkownicy i grupy** | Wybierz co najmniej jednego użytkownika lub grupę, która jest autoryzowana do uzyskiwania dostępu do wybranych aplikacji w chmurze. | Zasady dostępu warunkowego, który nie ma przypisanych użytkowników i grup, nigdy nie jest wyzwalany. |
| **Kontrole dostępu** | Wybierz co najmniej jedną kontrolę dostępu. | Jeśli warunki są spełnione, procesor zasad musi wiedzieć, co zrobić. |

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="how-are-conditional-access-policies-applied"></a>W jaki sposób stosowane są zasady dostępu warunkowego?

Podczas uzyskiwania dostępu do aplikacji w chmurze może obowiązywać więcej niż jedna zasada dostępu warunkowego. W takim przypadku wszystkie zasady, które mają zastosowanie, muszą być spełnione. Na przykład jeśli jedna zasada wymaga uwierzytelniania wieloskładnikowego (MFA), a druga wymaga zgodnego urządzenia, należy wykonać uwierzytelnianie wieloskładnikowe i używać zgodnego urządzenia. 

Wszystkie zasady są wymuszane w dwóch etapach:

- Etap 1: 
   - Zbieranie szczegółów: Zbieraj szczegóły, aby zidentyfikować zasady, które zostały już spełnione.
   - W tej fazie użytkownicy mogą zobaczyć monit o certyfikat, jeśli zgodność urządzenia jest częścią zasad dostępu warunkowego. Ten monit może wystąpić w przypadku aplikacji przeglądarki, gdy system operacyjny urządzenia nie jest windows 10.
   - Faza 1 oceny zasad występuje dla wszystkich włączonych zasad i zasad w [trybie tylko do raportu](concept-conditional-access-report-only.md).
- Etap 2:
   - Egzekwowanie: Biorąc pod uwagę dane zebrane w fazie 1, poproś użytkownika o spełnienie wszelkich dodatkowych wymagań, które nie zostały spełnione.
   - Zastosuj wyniki do sesji. 
   - Faza 2 oceny zasad występuje dla wszystkich włączonych zasad.

### <a name="how-are-assignments-evaluated"></a>Jak oceniane są przydziały?

Wszystkie przypisania są logicznie **ANDed**. Jeśli skonfigurowano więcej niż jedno przypisanie, wszystkie przypisania muszą być spełnione, aby wyzwolić zasadę.  

Jeśli chcesz skonfigurować warunek lokalizacji, który ma zastosowanie do wszystkich połączeń nawiązywać z sieci organizacji:

- Uwzględnij **wszystkie lokalizacje**
- Wyklucz **wszystkie zaufane usługi IP**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co zrobić, jeśli zostaniesz zablokowany w portalu administratora usługi Azure AD?

Jeśli portal usługi Azure AD jest zablokowany z powodu nieprawidłowego ustawienia w zasadach dostępu warunkowego:

- Sprawdź, czy w organizacji są inni administratorzy, którzy nie są jeszcze zablokowani. Administrator z dostępem do witryny Azure portal można wyłączyć zasady, które ma wpływ na logowania. 
- Jeśli żaden z administratorów w organizacji nie może zaktualizować zasad, należy przesłać żądanie pomocy technicznej. Pomoc techniczna firmy Microsoft może przeglądać i aktualizować zasady dostępu warunkowego, które uniemożliwiają dostęp.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co się stanie, jeśli masz skonfigurowane zasady w klasycznej witrynie Azure i witrynie Azure Portal?  

Obie zasady są wymuszane przez usługę Azure Active Directory, a użytkownik uzyskuje dostęp tylko wtedy, gdy spełnione są wszystkie wymagania.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co się stanie, jeśli masz zasady w portalu Usługi Intune Silverlight i witrynie Azure portal?

Obie zasady są wymuszane przez usługę Azure Active Directory, a użytkownik uzyskuje dostęp tylko wtedy, gdy spełnione są wszystkie wymagania.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co się stanie, jeśli skonfigurowano wiele zasad dla tego samego użytkownika?  

Dla każdego logowania usługa Azure Active Directory ocenia wszystkie zasady i zapewnia, że wszystkie wymagania są spełnione przed przyznaniem dostępu do użytkownika. Blok dostępu przebija wszystkie inne ustawienia konfiguracji. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Czy dostęp warunkowy działa z programem Exchange ActiveSync?

Tak, można użyć programu Exchange ActiveSync w zasadach dostępu warunkowego.

Niektóre aplikacje w chmurze, takie jak SharePoint Online i Exchange Online, obsługują również starsze protokoły uwierzytelniania. Gdy aplikacja kliencka może używać starszego protokołu uwierzytelniania w celu uzyskania dostępu do aplikacji w chmurze, usługa Azure AD nie może wymusić zasad dostępu warunkowego w tej próbie dostępu. Aby zapobiec aplikacji klienckiej z pominięciem wymuszania zasad, należy sprawdzić, czy jest możliwe tylko włączyć nowoczesne uwierzytelnianie w aplikacjach w chmurze, których dotyczy problem.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Jak skonfigurować dostęp warunkowy za pomocą aplikacji usługi Office 365?

Ponieważ aplikacje usługi Office 365 są ze sobą połączone, podczas tworzenia zasad zalecamy przypisywanie powszechnie używanych aplikacji.

Typowe połączone aplikacje to Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online i Office 365 Yammer.

Jest ważne dla zasad, które wymagają interakcji użytkownika, takich jak uwierzytelnianie wieloskładnikowe, gdy dostęp jest kontrolowany na początku sesji lub zadania. Jeśli tego nie zrobisz, użytkownicy nie będą mogli wykonywać niektórych zadań w aplikacji. Jeśli na przykład potrzebujesz uwierzytelniania wieloskładnikowego na urządzeniach niezarządzanych, aby uzyskać dostęp do programu SharePoint, ale nie wysyłać wiadomości e-mail, użytkownicy pracujący w wiadomości e-mail nie będą mogli dołączać plików programu SharePoint do wiadomości. Więcej informacji można znaleźć w artykule, [Co to są zależności usług w usłudze Azure Active Directory dostęp warunkowy?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Czego należy unikać

Struktura dostępu warunkowego zapewnia dużą elastyczność konfiguracji. Jednak duża elastyczność oznacza również, że należy dokładnie przejrzeć każdą zasadę konfiguracji przed zwolnieniem go, aby uniknąć niepożądanych wyników. W tym kontekście należy zwrócić szczególną uwagę na przypisania wpływające na kompletne zestawy, takie jak **wszyscy użytkownicy / grupy / aplikacje w chmurze.**

W twoim środowisku należy unikać następujących konfiguracji:

**Dla wszystkich użytkowników wszystkie aplikacje w chmurze:**

- **Blokuj dostęp** — ta konfiguracja blokuje całą organizację, co zdecydowanie nie jest dobrym pomysłem.
- **Wymagaj zgodnego urządzenia** — dla użytkowników, którzy nie zarejestrowali jeszcze swoich urządzeń, ta zasada blokuje cały dostęp, w tym dostęp do portalu usługi Intune. Jeśli jesteś administratorem bez zarejestrowanego urządzenia, ta zasada uniemożliwia powrót do witryny Azure Portal, aby zmienić zasady.
- **Wymagaj dołączania do domeny** — ten dostęp do blokowania zasad może również blokować dostęp dla wszystkich użytkowników w organizacji, jeśli nie masz jeszcze urządzenia przyłączanego do domeny.
- **Wymagaj zasad ochrony aplikacji** — ten dostęp do blokowania zasad może również blokować dostęp dla wszystkich użytkowników w organizacji, jeśli nie masz zasad usługi Intune. Jeśli jesteś administratorem bez aplikacji klienckiej, która ma zasady ochrony aplikacji usługi Intune, ta zasada uniemożliwia powrót do portali, takich jak Intune i Azure.

**Dla wszystkich użytkowników, wszystkie aplikacje w chmurze, wszystkie platformy urządzeń:**

- **Blokuj dostęp** — ta konfiguracja blokuje całą organizację, co zdecydowanie nie jest dobrym pomysłem.

## <a name="how-should-you-deploy-a-new-policy"></a>Jak wdrożyć nowe zasady?

W pierwszej kolejności należy ocenić zasady za pomocą [narzędzia Co jeśli](what-if-tool.md).

Gdy nowe zasady są gotowe dla środowiska, wdrażaj je etapami:

1. Zastosuj zasadę do małego zestawu użytkowników i sprawdź, czy zachowuje się zgodnie z oczekiwaniami. 
1. Po rozwinięciu zasad, aby uwzględnić więcej użytkowników. Kontynuuj wykluczanie wszystkich administratorów z zasad, aby upewnić się, że nadal mają dostęp i mogą aktualizować zasady, jeśli wymagana jest zmiana.
1. Zastosuj zasady do wszystkich użytkowników tylko wtedy, gdy jest to konieczne. 

Najlepszym rozwiązaniem jest utworzenie konta użytkownika, które jest:

- Dedykowane do zarządzania politykami 
- Wykluczone ze wszystkich zasad

## <a name="policy-migration"></a>Migracja polityki

Rozważ migrację zasad, które nie zostały utworzone w witrynie Azure portal, ponieważ:

- Teraz można rozwiązać scenariusze, których wcześniej nie można było obsłużyć.
- Można zmniejszyć liczbę zasad, którymi należy zarządzać, konsolidując je.   
- Wszystkie zasady dostępu warunkowego można zarządzać w jednej centralnej lokalizacji.
- Klasyczny portal platformy Azure został wycofany.   

Aby uzyskać więcej informacji, zobacz artykuł [Migrate classic policies in the Azure portal (Migrowanie zasad klasycznych w witrynie Azure Portal)](policy-migration.md).

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz wiedzieć:

- Jak skonfigurować zasady dostępu warunkowego, zobacz [Wymaganie usługi MFA dla określonych aplikacji z dostępem warunkowym usługi Azure Active Directory](app-based-mfa.md).
- Jak zaplanować zasady dostępu warunkowego, zobacz [Jak zaplanować wdrożenie dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md).
