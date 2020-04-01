---
title: Blokowanie uwierzytelniania starszego — usługa Azure Active Directory
description: Dowiedz się, jak poprawić stan bezpieczeństwa, blokując starsze uwierzytelnianie przy użyciu dostępu warunkowego usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957aa77e18ea8f910f258d1dc59de0d093b0eab6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476659"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Jak: Blokowanie uwierzytelniania starszej wersji usługi Azure AD za pomocą dostępu warunkowego   

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure Active Directory (Azure AD) obsługuje szeroką gamę protokołów uwierzytelniania, w tym uwierzytelnianie starsze. Jednak starsze protokoły nie obsługują uwierzytelniania wieloskładnikowego (MFA). Usługa MFA jest w wielu środowiskach typowym wymogiem dotyczącym kradzieży tożsamości. 

Alex Weinert, dyrektor działu zabezpieczeń tożsamości w firmie Microsoft, w swoim wpisie w blogu z 12 marca 2020 [r. Nowe narzędzia do blokowania uwierzytelniania starszego w organizacji](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) podkreślają, dlaczego organizacje powinny blokować starsze uwierzytelnianie i jakie dodatkowe narzędzia firma Microsoft udostępnia, aby wykonać to zadanie:

> Aby usługa MFA była skuteczna, należy również zablokować uwierzytelnianie starszej wersji. Dzieje się tak, ponieważ starsze protokoły uwierzytelniania, takie jak POP, SMTP, IMAP i MAPI, nie mogą wymuszać usługi MFA, co czyni je preferowanymi punktami wejścia dla przeciwników atakujących twoją organizację...
> 
>... Liczby dotyczące uwierzytelniania starszego z analizy ruchu usługi Azure Active Directory (Azure AD) są ostre:
> 
> - Ponad 99 procent ataków natryskiwanie haseł korzysta ze starszych protokołów uwierzytelniania
> - Ponad 97 procent ataków na faszerowanie poświadczeń korzysta ze starszego uwierzytelniania
> - Konta usługi Azure AD w organizacjach, które wyłączyły uwierzytelnianie w starszej wersji, doświadczają o 67 procent mniej zabezpieczeń niż konta, w których włączone jest uwierzytelnianie starsze
>

Jeśli środowisko jest gotowe do blokowania starszego uwierzytelniania w celu poprawy ochrony dzierżawy, można osiągnąć ten cel za pomocą dostępu warunkowego. W tym artykule wyjaśniono, jak skonfigurować zasady dostępu warunkowego, które blokują starsze uwierzytelnianie dla dzierżawy.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że jesteś zaznajomiony z: 

- [Podstawowe pojęcia](overview.md) dostępu warunkowego usługi Azure AD 
- [Najważniejsze wskazówki dotyczące](best-practices.md) konfigurowania zasad dostępu warunkowego w witrynie Azure portal

## <a name="scenario-description"></a>Opis scenariusza

Usługa Azure AD obsługuje kilka najczęściej używanych protokołów uwierzytelniania i autoryzacji, w tym uwierzytelnianie starsze. Uwierzytelnianie starsze odnosi się do protokołów korzystających z uwierzytelniania podstawowego. Zazwyczaj te protokoły nie można wymusić wszelkiego rodzaju uwierzytelniania drugiego czynnika. Przykłady dla aplikacji opartych na uwierzytelniania starszej są następujące:

- Starsze aplikacje pakietu Microsoft Office
- Aplikacje korzystające z protokołów poczty, takich jak POP, IMAP i SMTP

Uwierzytelnianie jednoskładnikowe (na przykład nazwa użytkownika i hasło) nie wystarcza w dzisiejszych czasach. Hasła są złe, ponieważ są łatwe do odgadnięcia, a my (ludzie) jesteśmy źli w wyborze dobrych haseł. Hasła są również narażone na różne ataki, takie jak phishing i spray haszyszu. Jedną z najprostszych rzeczy, które można zrobić, aby chronić przed zagrożeniami hasłem jest zaimplementowanie usługi MFA. W przypadku usługi MFA, nawet jeśli osoba atakująca będzie posiadała hasło użytkownika, samo hasło nie jest wystarczające do pomyślnego uwierzytelnienia i uzyskania dostępu do danych.

Jak zapobiec dostępowi aplikacji korzystających ze starszego uwierzytelniania do zasobów dzierżawy? Zalecenie jest po prostu zablokować je za pomocą zasad dostępu warunkowego. W razie potrzeby zezwalasz tylko niektórym użytkownikom i określonym lokalizacjom sieciowym na korzystanie z aplikacji opartych na starszym uwierzytelnianiu.

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego czynnika. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwsza linia obrony dla scenariuszy, takich jak ataki typu odmowa usługi (DoS), ale może korzystać z sygnałów z tych zdarzeń (na przykład poziom ryzyka logowania, lokalizacja żądania i tak dalej) w celu określenia dostępu.

## <a name="implementation"></a>Wdrażanie

W tej sekcji wyjaśniono, jak skonfigurować zasady dostępu warunkowego do blokowania uwierzytelniania starszego. 

### <a name="legacy-authentication-protocols"></a>Starsze protokoły uwierzytelniania

Następujące opcje są uważane za starsze protokoły uwierzytelniania

- Uwierzytelniony protokół SMTP — używany przez klientów POP i IMAP do wysyłania wiadomości e-mail.
- Automatyczne wykrywanie — używane przez klientów programu Outlook i EAS do znajdowania skrzynek pocztowych i łączenia się z nią w usłudze Exchange Online.
- Program Exchange Online PowerShell — służy do łączenia się z usługą Exchange Online za pomocą zdalnego programu PowerShell. Jeśli zablokujesz uwierzytelnianie podstawowe dla programu Exchange Online PowerShell, musisz użyć modułu programu Exchange Online PowerShell, aby się połączyć. Aby uzyskać instrukcje, zobacz [Łączenie się z programem Exchange Online PowerShell przy użyciu uwierzytelniania wieloskładnikowego](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) — interfejs programowania używany przez program Outlook, Outlook dla komputerów Mac i aplikacje innych firm.
- IMAP4 — używany przez klientów poczty e-mail IMAP.
- MAPI za pośrednictwem protokołu HTTP (MAPI/HTTP) — używane przez program Outlook 2010 i nowsze.
- Książka adresowa trybu offline (OAB) — kopia kolekcji list adresów, które są pobierane i używane przez program Outlook.
- Outlook Anywhere (RPC over HTTP) — używany przez program Outlook 2016 i starsze.
- Usługa programu Outlook — używana przez aplikację Poczta i Kalendarz dla systemu Windows 10.
- POP3 — używany przez klientów poczty pop.
- Raportowanie usług sieci Web — służy do pobierania danych raportu w usłudze Exchange Online.
- Inni klienci — inne protokoły zidentyfikowane jako wykorzystujące uwierzytelnianie starsze.

Aby uzyskać więcej informacji na temat tych protokołów i usług uwierzytelniania, zobacz [Raporty aktywności logowania w portalu usługi Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identyfikowanie użycia uwierzytelniania starszego

Aby zablokować starsze uwierzytelnianie w katalogu, należy najpierw zrozumieć, czy użytkownicy mają aplikacje korzystające ze starszego uwierzytelniania i jak wpływa na ogólny katalog. Dzienniki logowania usługi Azure AD mogą służyć do zrozumienia, jeśli używasz uwierzytelniania starszego.

1. Przejdź do witryny **Azure portal** > **Azure Active Directory.** > **Sign-ins**
1. Dodaj kolumnę Aplikacja klienta, jeśli nie jest ona wyświetlana, klikając **aplikację kliencką Kolumny** > **Client App**.
1. **Dodaj filtry** > **Aplikacja klienta** > zaznacz wszystkie starsze protokoły uwierzytelniania i kliknij przycisk **Zastosuj**.

Filtrowanie będzie wyświetlać tylko próby logowania, które zostały wykonane przez starsze protokoły uwierzytelniania. Kliknięcie każdej indywidualnej próby logowania spowoduje wyświetlenie dodatkowych informacji. Pole **Aplikacja kliencka** na karcie **Informacje podstawowe** wskazuje, który starszy protokół uwierzytelniania został użyty.

Te dzienniki wskażą, którzy użytkownicy są nadal zależni od starszego uwierzytelniania i które aplikacje używają starszych protokołów do żądania uwierzytelniania. Dla użytkowników, którzy nie są wyświetlane w tych dziennikach i są potwierdzone, że nie używa uwierzytelniania starszego, należy zaimplementować zasady dostępu warunkowego tylko dla tych użytkowników.

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania 

W zasadach dostępu warunkowego można ustawić warunek, który jest powiązany z aplikacjami klienckimi, które są używane do uzyskiwania dostępu do zasobów. Warunek aplikacji klienckich umożliwia zawężenie zakresu do aplikacji korzystających ze starszego uwierzytelniania, wybierając **klientów Programu Exchange ActiveSync** i **innych klientów** w obszarze **Aplikacje mobilne i klienci klasyczni**.

![Inni klienci](./media/block-legacy-authentication/01.png)

Aby zablokować dostęp do tych aplikacji, musisz wybrać **opcję Zablokuj dostęp**.

![Blokowanie dostępu](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Wybieranie użytkowników i aplikacji w chmurze

Jeśli chcesz zablokować starsze uwierzytelnianie w organizacji, prawdopodobnie uważasz, że możesz to osiągnąć, wybierając:

- Wszyscy użytkownicy
- Wszystkie aplikacje w chmurze
- Blokowanie dostępu

![Przypisania](./media/block-legacy-authentication/03.png)

Platforma Azure ma funkcję bezpieczeństwa, która uniemożliwia tworzenie zasad, takich jak ta, ponieważ ta konfiguracja narusza [najlepsze rozwiązania](best-practices.md) dotyczące zasad dostępu warunkowego.
 
![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/04.png)

Funkcja bezpieczeństwa jest niezbędna, ponieważ *blokowanie wszystkich użytkowników i wszystkich aplikacji w chmurze* może zablokować całą organizację przed logowaniem się do dzierżawy. Należy wykluczyć co najmniej jednego użytkownika, aby spełnić minimalne wymagania dotyczące najlepszych praktyk. Można również wykluczyć rolę katalogu.

![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/05.png)

Tę funkcję bezpieczeństwa można spełnić, wykluczając jednego użytkownika z zasad. W idealnym przypadku należy zdefiniować kilka [kont administracyjnych dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md) i wykluczyć je z zasad.

Korzystanie z [trybu tylko](concept-conditional-access-report-only.md) do raportu podczas włączania zasad do blokowania uwierzytelniania starszego zapewnia organizacji możliwość monitorowania wpływu zasad.

## <a name="policy-deployment"></a>Wdrażanie zasad

Zanim wprowadzisz swoje zasady do produkcji, zadbaj o:
 
- **Konta usług** — identyfikowanie kont użytkowników, które są używane jako konta usług lub urządzeń, takich jak telefony z sali konferencyjnej. Upewnij się, że te konta mają silne hasła i dodaj je do wykluczonej grupy.
- **Raporty logowania** — przejrzyj raport logowania i poszukaj innego ruchu **klienta.** Zidentyfikuj najlepsze użycie i sprawdź, dlaczego jest ono używane. Zazwyczaj ruch jest generowany przez starszych klientów pakietu Office, którzy nie używają nowoczesnego uwierzytelniania lub niektórych aplikacji poczty innych firm. Zaplanuj przeniesienie użycia z tych aplikacji lub jeśli wpływ jest niski, powiadom użytkowników, że nie mogą już korzystać z tych aplikacji.
 
Aby uzyskać więcej informacji, zobacz [Jak wdrożyć nową zasadę?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Co należy wiedzieć

Blokowanie dostępu przy użyciu **innych klientów** blokuje również programy Exchange Online PowerShell i Dynamics 365 przy użyciu podstawowego eru.

Konfigurowanie zasad dla **innych klientów** blokuje całą organizację od niektórych klientów, takich jak SPConnect. Ten blok dzieje się, ponieważ starsi klienci uwierzytelniają się w nieoczekiwany sposób. Problem nie dotyczy głównych aplikacji pakietu Office, takich jak starsi klienci pakietu Office.

Wejście w życie polisy może potrwać do 24 godzin.

Można wybrać wszystkie dostępne formanty dotacji dla warunku **Inni klienci;** jednak środowisko użytkownika końcowego jest zawsze takie samo — zablokowany dostęp.

Jeśli zablokujesz starsze uwierzytelnianie przy użyciu warunku **Inne klientów,** możesz również ustawić platformę urządzenia i warunek lokalizacji. Jeśli na przykład chcesz zablokować tylko starsze uwierzytelnianie dla urządzeń przenośnych, ustaw warunek **platform urządzeń,** wybierając:

- Android
- iOS
- Windows Phone

![Konfiguracja zasad nie jest obsługiwana](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Następne kroki

- [Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)
- Jeśli nie jesteś jeszcze zaznajomiony z konfigurowaniem zasad dostępu warunkowego, zobacz [wymagaj usługi MFA dla określonych aplikacji z dostępem warunkowym usługi Azure Active Directory](app-based-mfa.md) na przykład.
- Aby uzyskać więcej informacji na temat nowoczesnej obsługi uwierzytelniania, zobacz [Jak działa nowoczesne uwierzytelnianie dla aplikacji klienckich pakietu Office 2013 i Office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
