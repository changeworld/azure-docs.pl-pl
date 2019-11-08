---
title: Wskazówki dotyczące platformy Azure Marketplace i wydawcy AppSource | Azure
description: Wskazówki dotyczące platformy Azure Marketplace i AppSource dla wydawców aplikacji i usług
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 371f36e13c244439a583cbeb7ff06c6a1283d272
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825932"
---
# <a name="guidelines"></a>Wytyczne  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Wskazówki dotyczące portalu Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Wskazówki dotyczące tworzenia identyfikatora firmy Microsoft w celu zarządzania kontem witryny Marketplace  
Jeśli więcej niż jedna osoba wymaga dostępu do tego samego identyfikatora firmy Microsoft użytego do utworzenia konta w witrynie Marketplace, należy przestrzegać poniższych wytycznych, aby ułatwić tworzenie konta firmowego. 

>[!IMPORTANT]
>Aby autoryzować wielu użytkowników do uzyskiwania dostępu do konta Centrum deweloperów firmy Microsoft (Centrum deweloperów), firma Microsoft zaleca używanie Azure Active Directory (Azure AD) do przypisywania ról do poszczególnych użytkowników. Każdy użytkownik musi uzyskać dostęp do konta, logując się przy użyciu poszczególnych poświadczeń usługi Azure AD. Utwórz identyfikator Microsoft przy użyciu adresu e-mail w domenie zarejestrowanej w firmie Microsoft sugeruje, że wiadomość e-mail nie jest przypisana do osoby. Może to być na przykład `windowsapps@fabrikam.com`.  
>*   Aby uzyskać więcej informacji, zapoznaj się z sekcją [problem: identyfikator Microsoft w domenie federacyjnej usługi Azure AD](#issue-microsoft-id-in-an-azure-ad-federated-domain) .  

*   Ogranicz dostęp do identyfikatora Microsoft do najmniejszej możliwej liczby deweloperów. 
*   Skonfiguruj firmową listę dystrybucyjną poczty e-mail (DL), która obejmuje wszystkie osoby, które muszą mieć dostęp do konta Centrum deweloperów. Dodaj adres e-mail DL do informacji o zabezpieczeniach. DL umożliwia wszystkim pracownikom z listy otrzymywanie kodów zabezpieczeń na żądanie i zarządzanie informacjami o zabezpieczeniach dla identyfikatora firmy Microsoft. Jeśli skonfigurowanie listy dystrybucyjnej nie jest możliwe, właściciel indywidualnego konta e-mail musi być dostępny, aby uzyskać dostęp do kodu zabezpieczeń i udostępnić go po wyświetleniu monitu.  
    *   Na przykład, właściciel jest monitowany po dodaniu nowych informacji o zabezpieczeniach do identyfikatora Microsoft lub po uzyskaniu dostępu do identyfikatora Microsoft z nowego urządzenia.  
*   Dodaj numer telefonu firmy, który nie wymaga rozszerzenia i jest dostępny dla najważniejszych członków zespołu.  
*   Ogólnie rzecz biorąc deweloperzy muszą używać zaufanych urządzeń do logowania się do konta Centrum deweloperów. Wszyscy członkowie zespołu kluczy powinni mieć dostęp do zaufanych urządzeń. Użycie zaufanych urządzeń w celu uzyskania dostępu zmniejsza wymagania dotyczące wysyłania kodów zabezpieczeń, gdy ktoś uzyskuje dostęp do konta Centrum deweloperów.  
*   Jeśli wymagane jest przyznanie dostępu do konta Centrum deweloperów z niezaufanego komputera, należy ograniczyć dostęp do nie więcej niż pięciu deweloperów. Najlepiej, deweloperzy powinni uzyskiwać dostęp do konta z komputerów, które mają tę samą lokalizację geograficzną i sieciową.  
*   Często Przeglądaj i Weryfikuj informacje o zabezpieczeniach.  
    *   Aby wyświetlić informacje o zabezpieczeniach, odwiedź stronę ustawień zabezpieczeń znajdującą się pod adresem [account.Live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Dla konta Centrum deweloperów należy uzyskać dostęp głównie z zaufanych komputerów. Jest to ważne, aby uzyskać dostęp z zaufanych komputerów, ponieważ istnieje limit liczby kodów generowanych na konto Centrum deweloperów na tydzień. Korzystanie z zaufanych komputerów umożliwia również najbardziej bezpieczne i spójne środowisko logowania. 
*   Więcej informacji o dodatkowych wytycznych i zabezpieczeniach Centrum deweloperów można znaleźć na stronie otwieranie konta dewelopera w witrynie [docs.Microsoft.com/Windows/UWP/Publish/Opening-a-Developer-Account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: identyfikator Microsoft w domenie federacyjnej usługi Azure AD  
Konto firmowe może być federacyjne za pomocą Azure Active Directory (Azure AD). Jeśli spróbujesz utworzyć identyfikator Microsoft przy użyciu firmowego adresu e-mail, który jest federacyjny z usługą Azure AD, zostanie wyświetlony komunikat o błędzie. Jeśli wystąpi błąd, należy skontaktować się z zespołem IT, aby potwierdzić, że Twoje konto jest federacyjne za pomocą usługi Azure AD. Federacyjna poczta e-mail usługi Azure AD jest znanym problemem, a firma Microsoft pracuje nad rozwiązaniem IT.  
*   Aby uzyskać więcej informacji na temat usługi Azure AD, odwiedź stronę dokumentacji Azure Active Directory znajdującą się pod adresem [docs.Microsoft.com/Azure/Active-Directory](https://docs.microsoft.com/azure/active-directory).

Firma Microsoft zaleca obejście problemu. Wykonaj następujące kroki, aby utworzyć nowy adres e-mail w domenie `outlook.com` i utworzyć regułę do przesyłania dalej komunikacji.  
1.  Przejdź do strony Tworzenie konta i kliknij link Uzyskaj nowy adres e-mail. 
    *   Aby zarejestrować się w usłudze Microsoft ID, odwiedź stronę Tworzenie konta znajdującą się pod adresem [signup.Live.com/signup](https://signup.live.com/signup).  
2.  Utwórz nowy adres e-mail i wprowadź hasło. Zostanie utworzony nowy identyfikator Microsoft i Skrzynka pocztowa e-mail w domenie `outlook.com`. Kontynuuj proces rejestracji, dopóki konto nie zostanie utworzone.  

    >[!IMPORTANT]
    >Musisz użyć adresu e-mail lub listy dystrybucyjnej, która jest zarejestrowana jako identyfikator Microsoft w celu zarejestrowania w centrum deweloperów. Firma Microsoft zaleca użycie listy dystrybucyjnej w celu usunięcia zależności od osób indywidualnych. Jeśli Twój adres e-mail lub lista dystrybucyjna nie jest zarejestrowana, musisz zarejestrować się teraz.    

    >[!Important]
    >Jeśli dowolny adres e-mail znajduje się w `Microsoft` domenie firmy, nie będzie można go używać do rejestracji w centrum deweloperów.  

3.  Po utworzeniu identyfikatora Microsoft za pomocą adresu e-mail w programie Outlook Zaloguj się do skrzynki pocztowej programu Outlook. Utwórz regułę przekazywania wiadomości e-mail. Reguła przesyłania dalej wiadomości e-mail powinna przenosić wszystkie wiadomości e-mail otrzymane w skrzynce pocztowej programu Outlook na adres e-mail w domenie utworzonej w celu zarządzania kontem witryny Marketplace.  
    *   Aby zalogować się do skrzynki pocztowej programu Outlook, odwiedź stronę programu Outlook znajdującą się pod adresem [Outlook.Live.com/owa](https://outlook.live.com/owa).  
    *   Aby uzyskać więcej informacji na temat reguł przekazywania, odwiedź stronę używanie reguł w aplikacji Outlook Web App, aby automatycznie przekazywać komunikaty do innej strony konta, która znajduje się w witrynie [support.Office.com/article/use-rules-in-Outlook-Web-App-to-automatically-Forward-messages-to-another-Account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Reguła przekazywania wysyła wszystkie wiadomości e-mail i komunikaty odebrane na konto e-mail programu Outlook na adres e-mail w domenie zarejestrowanej w firmie. Adres e-mail `outlook.com` musi być używany do uwierzytelniania w centrum deweloperów i portal Cloud Partner.  

## <a name="next-steps"></a>Następne kroki

*   Odwiedź stronę [przewodnika po wydawcy portalu Azure Marketplace i AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) . 
 
---
