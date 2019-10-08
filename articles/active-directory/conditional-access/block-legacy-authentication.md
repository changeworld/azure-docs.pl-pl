---
title: Jak zablokować starsze uwierzytelnianie w usłudze Azure Active Directory (Azure AD) z dostępem warunkowym | Microsoft Docs
description: Dowiedz się, jak ulepszyć stan zabezpieczeń, blokując starsze uwierzytelnianie przy użyciu dostępu warunkowego usługi Azure AD.
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
ms.openlocfilehash: f4e4dc33d670c5f6c5ebefa21ccf1a1ff941e913
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024581"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Instrukcje: blokowanie starszego uwierzytelniania w usłudze Azure AD przy użyciu dostępu warunkowego   

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure Active Directory (Azure AD) obsługuje szeroką gamę protokołów uwierzytelniania, w tym starsze uwierzytelnianie. Jednak starsze protokoły nie obsługują uwierzytelniania wieloskładnikowego (MFA). Uwierzytelnianie wieloskładnikowe jest w wielu środowiskach typowym wymaganiem do kradzieży tożsamości. 

Jeśli środowisko jest gotowe do blokowania starszego uwierzytelniania w celu usprawnienia ochrony dzierżawy, można osiągnąć ten cel za pomocą dostępu warunkowego. W tym artykule wyjaśniono, jak można skonfigurować zasady dostępu warunkowego, które blokują starsze uwierzytelnianie dla dzierżawy.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz: 

- [Podstawowe koncepcje](overview.md) dostępu warunkowego usługi Azure AD 
- [Najlepsze rozwiązania](best-practices.md) dotyczące konfigurowania zasad dostępu warunkowego w Azure Portal

## <a name="scenario-description"></a>Opis scenariusza

Usługa Azure AD obsługuje kilka powszechnie używanych protokołów uwierzytelniania i autoryzacji, w tym starsze uwierzytelnianie. Starsze uwierzytelnianie dotyczy protokołów korzystających z uwierzytelniania podstawowego. Zazwyczaj te protokoły nie mogą wymuszać żadnego typu uwierzytelniania drugiego. Przykłady dla aplikacji opartych na starszej wersji uwierzytelniania są następujące:

- Starsze aplikacje Microsoft Office
- Aplikacje korzystające z protokołów poczty, takich jak POP, IMAP i SMTP

Uwierzytelnianie wieloskładnikowe (na przykład nazwa użytkownika i hasło) nie wystarcza do korzystania z tych dni. Hasła są nieodpowiednie, ponieważ są one łatwe do odgadnięcia, a firma Microsoft nie jest w stanie wybierać prawidłowych haseł. Hasła są również narażone na różne ataki, takie jak phishing i rozpylanie haseł. Jedną z najłatwiejszych możliwości ochrony przed zagrożeniami hasła jest wdrożenie usługi MFA. W przypadku usługi MFA nawet jeśli osoba atakująca uzyska hasło użytkownika, samo hasło nie jest wystarczające do pomyślnego uwierzytelnienia i uzyskiwania dostępu do danych.

Jak można uniemożliwić aplikacjom korzystającym ze starszego uwierzytelniania dostęp do zasobów dzierżawy? Zalecenie polega na prostu zablokować je za pomocą zasad dostępu warunkowego. W razie potrzeby zezwalasz tylko określonym użytkownikom i określonym lokalizacjom sieciowym na używanie aplikacji opartych na starszej wersji uwierzytelniania.

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwszy wiersz obrony przed scenariuszami, takimi jak ataki typu "odmowa usługi" (DoS), ale mogą korzystać z sygnałów z tych zdarzeń (na przykład poziomu ryzyka logowania, lokalizacji żądania itp.) w celu określenia dostępu.

## <a name="implementation"></a>Wdrażanie

W tej sekcji opisano sposób konfigurowania zasad dostępu warunkowego w celu blokowania starszego uwierzytelniania. 

### <a name="identify-legacy-authentication-use"></a>Identyfikuj użycie starszego uwierzytelniania

Przed zablokowaniem starszego uwierzytelniania w katalogu należy najpierw zrozumieć, czy użytkownicy mają aplikacje korzystające ze starszego uwierzytelniania i jak mają wpływ na ogólny katalog. Korzystając z dzienników logowania usługi Azure AD, można zrozumieć, czy używane jest starsze uwierzytelnianie.

1. Przejdź do **Azure Portal** > **Azure Active Directory** > **logowania**.
1. Dodaj kolumnę aplikacji klienckiej, jeśli nie jest ona wyświetlana przez kliknięcie **kolumn** > **App Client**.
1. **Dodaj filtry** > **aplikacji klienckiej** > Wybierz wszystkie opcje dla **innych klientów** i kliknij przycisk **Zastosuj**.

Filtrowanie będzie zawierać tylko próby logowania, które zostały wykonane przez starsze protokoły uwierzytelniania. Kliknięcie każdej próby logowania spowoduje wyświetlenie dodatkowych szczegółów. Pole **aplikacji klienckiej** na karcie **Informacje podstawowe** wskazuje, który z starszych wersji protokołu uwierzytelniania był używany.

Te dzienniki wskazują, którzy użytkownicy nadal są w stanie w zależności od starszego uwierzytelniania i które aplikacje używają starszych protokołów do przesyłania żądań uwierzytelniania. W przypadku użytkowników, którzy nie są wyświetlani w tych dziennikach i potwierdzają, że nie używają starszego uwierzytelniania, należy zaimplementować zasady dostępu warunkowego tylko dla tych użytkowników.

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania 

W zasadach dostępu warunkowego można ustawić warunek powiązany z aplikacjami klienckimi, które są używane do uzyskiwania dostępu do zasobów. Warunek aplikacje klienckie umożliwia zawężenie zakresu do aplikacji korzystających ze starszego uwierzytelniania przez wybranie **innych klientów** dla **aplikacji mobilnych i klientów stacjonarnych**.

![Inni klienci](./media/block-legacy-authentication/01.png)

Aby zablokować dostęp do tych aplikacji, musisz wybrać opcję **Blokuj dostęp**.

![Blokuj dostęp](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Wybieranie użytkowników i aplikacji w chmurze

Jeśli chcesz zablokować starsze uwierzytelnianie dla swojej organizacji, prawdopodobnie sądzisz, że możesz to zrobić, wybierając pozycję:

- Wszyscy użytkownicy
- Wszystkie aplikacje w chmurze
- Blokuj dostęp

![Przypisania](./media/block-legacy-authentication/03.png)

Platforma Azure ma funkcję zabezpieczeń, która uniemożliwia tworzenie zasad, takich jak ta, ponieważ ta konfiguracja narusza [najlepsze rozwiązania](best-practices.md) dotyczące zasad dostępu warunkowego.
 
![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/04.png)

Funkcja zabezpieczeń jest niezbędna, ponieważ *Blokada wszyscy użytkownicy i wszystkie aplikacje w chmurze* ma możliwość blokowania rejestracji w całej organizacji przed zalogowaniem się do dzierżawy. Należy wykluczyć co najmniej jednego użytkownika, aby spełnić wymagania minimalnego najlepszego rozwiązania. Można również wykluczyć rolę katalogu.

![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/05.png)

Tę funkcję zabezpieczeń można spełnić, wykluczając jednego użytkownika z zasad. W idealnym przypadku należy zdefiniować kilka [kont administracyjnych dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md) i wykluczyć je z zasad.

## <a name="policy-deployment"></a>Wdrażanie zasad

Przed wprowadzeniem zasad do środowiska produkcyjnego należy wziąć pod uwagę:
 
- **Konta usług** — Identyfikuj konta użytkowników, które są używane jako konta usług lub przez urządzenia, takie jak telefony z sali konferencyjnej. Upewnij się, że te konta mają silne hasła, i Dodaj je do wykluczonej grupy.
- **Raporty logowania** — zapoznaj się z raportem logowania i Wyszukaj inny ruch **klientów** . Zidentyfikuj pierwsze użycie i sprawdź, dlaczego są używane. Zwykle ruch jest generowany przez starszych klientów pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania lub niektórych aplikacji poczty innej firmy. Utwórz plan, aby przenieść użycie z tych aplikacji, lub jeśli wpływ jest niski, Powiadom użytkowników, że nie mogą już korzystać z tych aplikacji.
 
Aby uzyskać więcej informacji, zobacz [jak należy wdrożyć nowe zasady?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Co należy wiedzieć

Blokowanie dostępu przy użyciu **innych klientów** blokuje również usługi Exchange Online PowerShell i Dynamics 365 przy użyciu uwierzytelniania podstawowego.

Skonfigurowanie zasad dla **innych klientów** blokuje całą organizację od niektórych klientów, takich jak SPConnect. Ten blok jest spowodowany tym, że starsi klienci uwierzytelniają się w nieoczekiwany sposób. Problem nie dotyczy najważniejszych aplikacji pakietu Office, takich jak starsi klienci pakietu Office.

Zastosowanie zasad może potrwać do 24 godzin.

Można wybrać wszystkie dostępne kontrolki Udziel dla **innych klientów** warunek. jednak środowisko użytkownika końcowego zawsze ma ten sam zablokowany dostęp.

W przypadku zablokowania starszego uwierzytelniania przy użyciu warunku **other clients** można również ustawić pozycję platforma urządzenia i warunek lokalizacji. Jeśli na przykład chcesz zablokować starsze uwierzytelnianie dla urządzeń przenośnych, Ustaw warunek **platformy urządzeń** , wybierając pozycję:

- Android
- iOS
- Windows Phone

![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli nie znasz jeszcze konfiguracji zasad dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji z Azure Active Directory dostępem warunkowym](app-based-mfa.md) na przykład.
- Aby uzyskać więcej informacji na temat nowoczesnej obsługi uwierzytelniania, zobacz [jak nowoczesne uwierzytelnianie działa dla pakietu office 2013 i aplikacji klienckich pakietu office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
