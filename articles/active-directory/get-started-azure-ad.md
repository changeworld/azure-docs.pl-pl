---
title: Rozpoczynanie pracy z usługą Azure Active Directory | Dokumentacja firmy Microsoft
description: Pobierz licencji, Dodaj nazwę domeny, tworzyć niestandardowe strony logowania i Dodaj Samoobsługowe Resetowanie w katalogów Active Azure haseł
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: fc6cc88cabab1bc56b0a04ce8778823647c5b2bf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-azure-ad"></a>Rozpoczynanie pracy z usługą Azure AD
Zarządzanie tożsamościami nowoczesnych wymaga skalowalnych, spójne reliablity w celu zapewnienia dostępności aplikacji i usług tylko do uwierzytelnionych użytkowników. Do obsługi potrzeb zarządzania tożsamościami użytkowników, dział IT musi odpowiednio, sposób, aby zapewnić dostęp do oprogramowania zatwierdzone, public jako aplikacje usługi (SaaS), sposób hosta wewnętrzne aplikacje biznesowe, a nawet sposobów zwiększenia lokalnych tworzenia aplikacji i użycia. Wszystkie te wymagania dotyczące punktu na potrzeby rozwiązania do zarządzania tożsamościami w chmurze.      

Azure Active Directory (Azure AD) jest wielodostępne firmy Microsoft, chmurze Usługa zarządzania katalogami i tożsamościami. Usługi Azure AD łączy podstawowych usług katalogowych, Zarządzanie zaawansowane tożsamości i zarządzania dostępem do aplikacji. Podczas projektowania wielodostępne, rozproszona geograficznie o wysokiej dostępności usługi Azure AD oznacza, że mogą polegać na nim do najważniejszych potrzeb biznesowych.

Usługi Azure AD zawiera całą gamę możliwości zarządzania tożsamościami, w tym możliwość synchronizowania lokalnych informacji o zasobach, można dostosować logo firmy, zarządzanie licencjami proste i zarządzanie hasłami nawet samoobsługi. Te możliwości łatwa do wdrożenia może pomóc rozpocząć używanie programu Azure AD do zabezpieczenia aplikacji opartych na chmurze, usprawnić procesów IT, zmniejszyć koszty i ułatwić, upewnij się, że są spełnione cele zgodność z zasadami firmowymi.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

Dalszej części tego artykułu opisano, jak rozpocząć pracę z usługą Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Rejestrowanie w usłudze Azure Active Directory — wersja Premium
Aby [wprowadzenie do usługi Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md), można zakupić licencje i skojarzyć je z subskrypcją platformy Azure. Jeśli tworzysz nową subskrypcję platformy Azure, należy również aktywować plan licencjonowania, a dostęp do usługi Azure AD. 

W usłudze Active Directory — wersja Premium można zarejestrować się na kilka sposobów: 

- Użyj swojej subskrypcji platformy Azure lub usługi Office 365.
- Użyj planu licencjonowania pakietu Enterprise Mobility + Security.
- Użyj planu licencjonowania zbiorowego firmy Microsoft.

### <a name="verification-step"></a>Krok weryfikacji
Po aktywowaniu subskrypcji, upewnij się, że możesz zalogować się do usługi.

## <a name="add-a-custom-domain-name"></a>Dodawanie niestandardowej nazwy domeny
Każdy katalog usługi Azure AD jest dostarczany z początkową nazwę domeny w postaci *domainname*. onmicrosoft.com. Nie można zmienić ani usunąć z początkowej nazwy domeny, ale można też [Dodawanie nazwy domeny firmy do usługi Azure AD](add-custom-domain.md). Twoja organizacja ma prawdopodobnie innych nazw domen używane biznesowe i użytkowników, którzy Zaloguj się przy użyciu nazwy domeny firmowej. Dodawanie niestandardowych nazw domen do usługi Azure AD umożliwia przypisywanie nazw użytkowników w katalogu, które są znajome dla użytkowników, takich jak "alice@contoso.com." zamiast "alice@.onmicrosoft.com". Proces jest prosty:

1. Dodawanie niestandardowej nazwy domeny do katalogu
2. Dodawanie wpisu DNS dla nazwy domeny w rejestratorze nazw domen
3. Weryfikowanie niestandardowej nazwy domeny w usłudze Azure AD

### <a name="verification-step"></a>Krok weryfikacji
Po dodaniu niestandardową domenę, upewnij się, że ma **zweryfikowano** stanie wyświetlony na **niestandardowych nazw domen** bloku portalu usługi Azure AD.

## <a name="add-company-branding-to-your-sign-in-page"></a>Dodawanie znakowania firmowego do strony logowania 
Aby uniknąć nieporozumień, wiele firm chce zastosować spójny wygląd i zachowanie we wszystkich witrynach sieci Web i usługach, którymi zarządzają. Azure Active Directory (Azure AD) zapewnia tę funkcję, umożliwiając [dostosowanie wyglądu strony logowania z logo firmy i niestandardowych schematów kolorów](customize-branding.md). Strona logowania jest to strona wyświetlana podczas logowania się do usługi Office 365 lub innych aplikacji opartych na sieci web, które używają usługi Azure AD jako dostawcy tożsamości. Użytkownik interakcji z tej strony, aby wprowadzić swoje poświadczenia.

### <a name="verification-step"></a>Krok weryfikacji
Zaloguj się do portalu Azure i upewnij się, że wszelkie dostosowania dodatkowych języków i dostosowanej strony logowania zostały skonfigurowane prawidłowo. 

## <a name="add-new-users"></a>Dodaj nowych użytkowników
Możesz [Dodawanie nowych użytkowników do organizacji usługi Azure AD](add-users-azure-active-directory.md) jednego za pomocą portalu Azure dzięki synchronizacji danych zasobów lokalnych usług AD systemu Windows Server. Można dodawać użytkowników w chmurze bezpośrednio z portalu usługi Azure AD i synchronizowania informacji użytkownika lokalnego.

Aby włączyć synchronizację lokalnych tożsamości z usługą Azure AD, musisz zainstalować i skonfigurować aplikację [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) na serwerze w swojej organizacji. Ta aplikacja zajmuje się synchronizowaniem użytkowników oraz grup w istniejącym źródle tożsamości do dzierżawy usługi Azure AD.

### <a name="verification-step"></a>Krok weryfikacji
Po utworzeniu lub synchronizowanie nowych użytkowników, upewnij się, że są one widoczne w usłudze Azure AD.

## <a name="assign-licenses"></a>Przypisywanie licencji
Uzyskiwanie subskrypcji ma wszystkie informacje wymagane do konfigurowania płatnej możliwości, ale należy nadal [przypisywać licencje użytkowników](license-users-groups.md) płatnej funkcji Azure AD — wersja Premium. Każdy użytkownik, którzy powinni mieć dostęp do lub który odbywa się za pośrednictwem usługi Azure AD płatnej funkcji należy przypisać im licencję. Przypisanie licencji jest mapowanie między użytkownikiem i zakupionych usług, takich jak Azure AD Premium, Basic lub pakietu Enterprise Mobility + Security.

Przypisanie oparte na grupach licencji umożliwia konfigurowanie reguł, w poniższych przykładach:

- Wszyscy użytkownicy w katalogu automatycznie otrzymuje licencję
- Każdy z tytułem odpowiednie zadanie pobiera licencji
- Decyzja o innych menedżerów w organizacji można delegować (przy użyciu grup samoobsługi)

### <a name="verification-step"></a>Krok weryfikacji
Przegląd przypisane i dostępnych licencji w obszarze **usługi Azure Active Directory** > **licencji** > **wszystkie produkty**.

## <a name="configure-self-service-password-reset"></a>Konfiguruj samoobsługowe resetowanie haseł
[Samoobsługowego resetowania hasła (SSPR)](authentication/quickstart-sspr.md) oferuje prosty oznacza dla administratorów IT umożliwić użytkownikom Resetowanie lub odblokować ich hasła lub konta. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym wykorzystaniu.

### <a name="verification-step"></a>Krok weryfikacji
Przejrzyj włączone właściwości SSPR pod **usługi Azure Active Directory** > **resetowania hasła** zapewnienie wprowadzono prawidłowego użytkownika i przypisania grupy. 


## <a name="next-steps"></a>Kolejne kroki
[Strony usługi Active Directory systemu Azure](https://azure.microsoft.com/services/active-directory/)

[Azure Active Directory cenową strona informacji o](https://azure.microsoft.com/pricing/details/active-directory/)