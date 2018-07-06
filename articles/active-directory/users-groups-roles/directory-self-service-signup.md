---
title: Rejestracja samoobsługi lub wersji próbnej usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Użyj Samoobsługowe tworzenie konta w dzierżawie usługi Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 99c5e99fa3bd33ef42e8df6ceba5be4be2cd1249
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872271"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Co to jest Samoobsługowe tworzenie konta usługi Azure Active Directory?
W tym artykule wyjaśniono, Samoobsługowe tworzenie konta i sposobu jego obsługi w usłudze Azure Active Directory (Azure AD). Jeśli chcesz przejąć nazwę domeny z niezarządzanych do usługi Azure AD dzierżawy, zobacz [przejmowanie katalogu niezarządzanego, jako administrator](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Dlaczego warto używać Samoobsługowe tworzenie konta?
* Pobierz klientów do usług, jaki mu pasuje, szybciej
* Utwórz bazujące na poczcie e-mail oferty usługi
* Tworzenie przepływów rejestracji bazujące na poczcie e-mail, które szybko Zezwalaj użytkownikom na tworzenie tożsamości za pomocą ich aliasów e-mail łatwa do zapamiętania pracy
* Niezależne-eksploatacyjnych utworzonego katalogu usługi Azure AD mogą być uwzględniane zarządzany katalog, który może służyć do innych usług

## <a name="terms-and-definitions"></a>Terminy i definicje
* **Samoobsługowa**: jest to metoda, za pomocą którego użytkownik rejestruje się w usłudze w chmurze i tworzone automatycznie dla nich w usłudze Azure AD tożsamości opartego na swojej domeny poczty e-mail.
* **Niezarządzany katalog usługi Azure AD**: jest to katalog, w którym utworzono tej tożsamości. Niezarządzanego katalogu jest katalogiem, który nie ma administratora globalnego.
* **Weryfikowany pocztą e-mail użytkownika**: jest to typ konta użytkownika w usłudze Azure AD. Użytkownik, który ma automatycznie utworzone po zarejestrowaniu się do oferty samoobsługowego tożsamość jest określany jako użytkownik weryfikowany pocztą e-mail. Użytkownik weryfikowany pocztą e-mail jest regularny członek katalogu oznakowane za pomocą creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Jak kontrolować ustawienia samoobsługowego?
Administratorzy mają dwie kontrolki samoobsługi już dziś. Kontrolować, czy:

* Użytkownicy mogą dołączyć do katalogu za pośrednictwem poczty e-mail.
* Użytkownicy mogą sami licencji dla aplikacji i usług.

### <a name="how-can-i-control-these-capabilities"></a>Jak kontrolować, te funkcje?
Administrator może skonfigurować te funkcje, korzystając z następujących parametrów polecenia cmdlet Set-MsolCompanySettings usługi Azure AD:

* **AllowEmailVerifiedUsers** Określa, czy użytkownik może tworzyć, lub Dołącz do niezarządzanego katalogu. Jeśli ustawisz ten parametr $false weryfikowany pocztą e-mail użytkownicy mogą dołączyć do katalogu.
* **AllowAdHocSubscriptions** steruje możliwością użytkownikom wykonywanie samoobsługowej. Jeśli ustawisz ten parametr $false, nie użytkownicy mogą wykonywać samoobsługowej. 
  
  > [!NOTE]
  > Flow i PowerApps rejestracji wersji próbnej nie są kontrolowane przez **AllowAdHocSubscriptions** ustawienie. Aby uzyskać więcej informacji zobacz następujące artykuły:
  > * [Jak uniemożliwić istniejącym użytkownikom z Rozpoczynanie korzystania z usługi Power BI](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Usługa Flow w Twojej organizacji, pytania i odpowiedzi](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak formanty działają razem?
Te dwa parametry może służyć w połączeniu do definiowania bardziej precyzyjną kontrolę nad samoobsługowej. Na przykład następujące polecenie umożliwi użytkownikom wykonywanie Samoobsługowe tworzenie konta, ale tylko wtedy, jeśli ci użytkownicy mają już konta w usłudze Azure AD (innymi słowy, użytkownicy potrzebują konta weryfikowany pocztą e-mail do utworzenia najpierw nie można wykonać samoobsługowej):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
Następujące schemat blokowy opisano różne kombinacje tych parametrów i wynikowe warunki do katalogu i samoobsługowej.

![][1]

Aby uzyskać więcej informacji i przykłady dotyczące używania tych parametrów, zobacz [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/directory-self-service-signup/SelfServiceSignUpControls.png
