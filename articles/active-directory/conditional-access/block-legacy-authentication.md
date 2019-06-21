---
title: Jak zablokować starsze uwierzytelnianie w usłudze Azure Active Directory (Azure AD) przy użyciu dostępu warunkowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zwiększyć poziom bezpieczeństwa przez blokowanie starsze uwierzytelnianie przy użyciu dostępu warunkowego usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2120466652db363206ec20c2303ad56670228c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164797"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Instrukcje: Blokuj starsze uwierzytelnianie do usługi Azure AD przy użyciu dostępu warunkowego   

Aby dać użytkownikom łatwy dostęp do aplikacji w chmurze, Azure Active Directory (Azure AD) obsługuje szerokiej gamy protokołów uwierzytelniania, w tym starsze uwierzytelnianie. Uwierzytelnianie wieloskładnikowe (MFA) nie obsługuje jednak starszych protokołów. Uwierzytelnianie wieloskładnikowe w wielu środowiskach jest typowym wymogiem na kradzież tożsamości adresu. 

Czy środowisko jest gotowe do bloku starszych uwierzytelniania w celu zwiększenia ochrony Twojej dzierżawy, można osiągnąć ten cel przy użyciu dostępu warunkowego. W tym artykule opisano sposób konfigurowania zasad dostępu warunkowego, które zablokowanie starszych uwierzytelniania dla dzierżawy.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz: 

- [Podstawowe pojęcia](overview.md) dostępu warunkowego usługi Azure AD 
- [Najlepsze praktyki](best-practices.md) na temat konfigurowania zasad dostępu warunkowego w witrynie Azure portal

## <a name="scenario-description"></a>Opis scenariusza

Usługa Azure AD obsługuje niektóre z najczęściej używanych protokołów uwierzytelniania i autoryzacji, w tym starsze uwierzytelnianie. Starsze uwierzytelnianie odnosi się do protokołów, które korzystają z uwierzytelniania podstawowego. Zazwyczaj te protokoły nie można wymusić dowolnego typu uwierzytelniania dwuskładnikowego. Przykłady aplikacji, które są oparte na starszych uwierzytelniania są:

- Starsze aplikacje pakietu Microsoft Office

- Aplikacje przy użyciu protokołów poczty, takich jak SMTP, POP i IMAP

Uwierzytelnianie pojedynczy czynnik (na przykład nazwę użytkownika i hasło) nie jest wystarczająco dużo, te dni. Hasła są uszkodzone, są one łatwe do odgadnięcia i firma Microsoft (ludzi) będą nieprawidłowe na wybranie dobre hasła. Hasła są również narażone na różnych atakami, takimi jak osłony techniki wyłudzania informacji oraz hasło. Jeden najprostszym rzeczy, które można zrobić, aby chronić przed zagrożeniami hasła jest zaimplementowanie usługi MFA. Za pomocą usługi MFA nawet wtedy, gdy osoba atakująca uzyskuje w posiadaniu hasła użytkownika samego hasła nie jest wystarczające, aby pomyślnie uwierzytelnić i uzyskać dostęp do danych.

Jak można zapobiec aplikacji korzystających z uwierzytelniania starszej wersji, uzyskiwanie dostępu do zasobów dzierżawy Zalecane jest po prostu je zablokować przy użyciu zasad dostępu warunkowego. Jeśli to konieczne, możesz zezwolić tylko niektórych użytkowników i określone lokalizacje sieciowe korzystać z aplikacji, które są oparte na starszych uwierzytelniania.

Po zakończeniu pierwszego czynnika, wymuszane są zasady dostępu warunkowego. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwszy defense wiersza dla scenariuszy, takich jak ataki (DoS) typu "odmowa usługi", ale mogą korzystać z sygnałów z tych zdarzeń (np. poziom ryzyka logowania, lokalizacja żądania, i tak dalej) w celu określenia dostępu do.

## <a name="implementation"></a>Wdrażanie

W tej sekcji opisano sposób konfigurowania zasad dostępu warunkowego w celu uwierzytelniania starszych bloku. 

### <a name="identify-legacy-authentication-use"></a>Identyfikowanie starszych uwierzytelniania użycia

Zanim zablokujesz starsze uwierzytelnianie w katalogu, należy najpierw zrozumieć, jeśli użytkownicy mają aplikacje, które używają starszych uwierzytelniania i jak wpływa na ogólne katalogu. Dzienniki logowania usługi AD platformy Azure może służyć do zrozumienia, jeśli używasz starszej wersji uwierzytelniania.

1. Przejdź do **witryny Azure portal** > **usługi Azure Active Directory** > **logowania**.
1. Dodaj kolumnę aplikacji klienckiej, jeśli nie jest wyświetlany, klikając **kolumn** > **aplikację kliencką**.
1. Filtruj według **aplikację kliencką** > **innych klientów** i kliknij przycisk **Zastosuj**.

Filtrowanie będzie tylko nieudane próby show logowania wprowadzono protokoły uwierzytelniania w starszej wersji. Kliknięcie każdego poszczególnych prób logowania zostanie wyświetlona dodatkowe szczegóły. **Aplikację kliencką** pola pod **podstawowe informacje o** kartę poinformuje, użyty protokół uwierzytelniania starszej wersji.

Te dzienniki będą wskazywać użytkowników, którzy są nadal w zależności od starszego uwierzytelniania i aplikacji, które używają starszych protokołów uwierzytelniania żądań. Dla użytkowników, którzy nie są wyświetlane w tych dziennikach i zostało potwierdzone nie korzystać z uwierzytelniania starszej wersji należy zaimplementować zasady dostępu warunkowego dla tych użytkowników tylko.

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania 

W zasadach dostępu warunkowego można ustawić warunek, który jest powiązany z aplikacji klienckich, które są używane do dostępu do zasobów. Stan aplikacji klienta umożliwia zawężenie zakresu do aplikacji przy użyciu starszej wersji uwierzytelniania, wybierając **inni klienci** dla **aplikacje mobilne i klienci stacjonarni**.

![Inni klienci](./media/block-legacy-authentication/01.png)

Aby zablokować dostęp do tych aplikacji, musisz wybrać **blokowana**.

![Blokuj dostęp](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Wybierz użytkowników i aplikacje w chmurze

Jeśli chcesz zablokować starsze uwierzytelnianie dla Twojej organizacji, prawdopodobnie uważasz, że można to zrobić, wybierając:

- Wszyscy użytkownicy

- Wszystkie aplikacje w chmurze

- Blokuj dostęp
 

![Przypisania](./media/block-legacy-authentication/03.png)



Platforma Azure ma funkcję bezpieczeństwa, która uniemożliwia tworzenie takie zasady, ponieważ narusza tę konfigurację [najlepsze praktyki](best-practices.md) dla zasad dostępu warunkowego.
 
![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/04.png)


Funkcja bezpieczeństwa jest konieczne ponieważ *zablokowanie wszystkich użytkowników i wszystkie aplikacje w chmurze* może potencjalnie zablokować w całej organizacji, zalogowanie się do swojej dzierżawy. Musisz wykluczyć co najmniej jednego użytkownika do zaspokojenia minimalnym wymaganiem najlepszym rozwiązaniem. Można również wykluczyć rolę katalogu.

![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/05.png)


Ta funkcja może spełnić przez wykluczenie jednego użytkownika z zasad. W idealnym przypadku należy zdefiniować kilka [dostępu awaryjnego kont administracyjnych w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md) i ich wykluczenie z zasad.
 

## <a name="policy-deployment"></a>Wdrażanie zasad

Zanim przeniesiesz zasad w środowisku produkcyjnym zajmie się:
 
- **Konta usług** -zidentyfikować konta użytkowników, które są używane jako konta usług lub urządzeń, takich jak telefony pokoju konferencji. Upewnij się, te konta silnych haseł i dodać je do grupy wykluczonych.
 
- **Raportów logowania** — przejrzeć raport, zaloguj się i poszukaj **innych klientów** ruchu. Identyfikowanie najważniejszych użycia i zbadać, dlaczego jest on używany. Zwykle ruch jest generowana przez starszych klientów pakietu Office, które nie korzystają z nowoczesnego uwierzytelniania lub niektóre aplikacje poczty innych firm. Stworzyć plan przenieść użycia od tych aplikacji lub jeśli brakuje wpływ powiadomić użytkowników, że te aplikacje nie mogą używać już.
 
Aby uzyskać więcej informacji, zobacz [jak należy wdrożyć nowe zasady?](best-practices.md#how-should-you-deploy-a-new-policy).



## <a name="what-you-should-know"></a>Co należy wiedzieć

Blokowanie dostępu za pomocą **inni klienci** blokuje również programu Exchange Online PowerShell przy użyciu uwierzytelniania podstawowego

Konfigurowanie zasad dla **inni klienci** blokuje całą organizację, z niektórych klientów, takich jak SPConnect. Ten blok wynika z faktu, starsi klienci uwierzytelnić się w nieoczekiwany sposób. Ten problem nie ma zastosowania do głównej aplikacji pakietu Office, takich jak starsi klienci pakietu Office.

Może upłynąć do 24 godzin, aż zasady zaczną obowiązywać.

Można wybrać wszystkie formanty grant dostępne dla innych klientów warunku; jednak środowisko użytkownika końcowego jest zawsze taki sam - dostęp jest zablokowany.

Jeśli zablokujesz starsze uwierzytelnianie przy użyciu innych warunków klientów, można również ustawić warunek platformy i lokalizacji urządzenia. Jeśli chcesz zablokować starsze uwierzytelnianie dla urządzeń przenośnych, na przykład ustawić **platform urządzeń** warunku, wybierając pozycję:

- Android

- iOS

- Windows Phone

![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie znasz jeszcze konfigurowania zasad dostępu warunkowego, zobacz [wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-based-mfa.md) przykład.

- Aby uzyskać więcej informacji na temat obsługi nowoczesnego uwierzytelniania, zobacz [sposobu działania nowoczesnego uwierzytelniania dla aplikacji klienckich pakietu Office 2013 i Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
