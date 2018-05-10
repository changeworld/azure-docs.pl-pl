---
title: Samoobsługi lub próbnej usługi rejestracji w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Użyj subskrypcji samoobsługowej w dzierżawie usługi Azure Active Directory (Azure AD)
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
ms.openlocfilehash: 67fd5ba9be2de1f79511c806ffaa0ae3001bfdcf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Co to jest subskrypcji samoobsługowej usługi Azure Active Directory?
W tym artykule opisano subskrypcji samoobsługowej oraz do jego obsługi w usłudze Azure Active Directory (Azure AD). Jeśli chcesz przejąć nazwy domeny z usługi Azure AD niezarządzanej dzierżawy, zobacz [przejąć niezarządzane katalogu jako administrator](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Dlaczego warto używać subskrypcji samoobsługowej?
* Uzyskanie dostępu do usług, które mają szybszy klientów
* Tworzenie opartych na poczcie e-mail oferty usługi
* Tworzenie przepływów signup opartych na poczcie e-mail, umożliwiające szybkie tworzenie tożsamości za pomocą ich aliasów e-mail łatwych do zapamiętania pracy
* Niezależne-eksploatacyjnych utworzonych katalog usługi Azure AD mogą być uwzględniane w zarządzanych katalogu, który może służyć do innych usług

## <a name="terms-and-definitions"></a>— Warunki
* **Subskrypcji samoobsługowej**: jest to metoda, za pomocą którego użytkownik tworzy konto w usłudze w chmurze i ma tożsamość automatycznie utworzone dla nich w usłudze Azure AD na podstawie ich domenę poczty e-mail.
* **Katalog usługi Azure AD niezarządzanych**: jest to katalog, w której jest tworzony tej tożsamości. Niezarządzane katalog jest katalogiem, który nie ma administratora globalnego.
* **Zweryfikować adres e-mail użytkownika**: jest to typ konta użytkownika w usłudze Azure AD. Użytkownik, który ma tożsamość automatycznie utworzone po zarejestrowaniu się do oferty samoobsługi nazywa się zweryfikować adres e-mail użytkownika. Zweryfikować adres e-mail użytkownika jest członkiem regularne katalogu z creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Jak kontrolować ustawienia samoobsługowego?
Administratorzy mają dwa formanty samoobsługi dzisiaj. Kontrolować, czy:

* Użytkownicy mogą dołączać katalogu za pośrednictwem poczty e-mail.
* Użytkownicy mogą się licencji dla aplikacji i usług.

### <a name="how-can-i-control-these-capabilities"></a>Metody kontrolowania tych funkcji
Administrator można skonfigurować te funkcje przy użyciu następujących parametrów polecenia cmdlet Set-MsolCompanySettings usługi Azure AD:

* **AllowEmailVerifiedUsers** Określa, czy użytkownik może utworzyć czy Dołącz katalog niezarządzane. Jeśli ten parametr jest ustawiony na $false, użytkownicy nie zweryfikować poczty e-mail może dołączać katalogu.
* **AllowAdHocSubscriptions** kontroluje zdolność użytkownikom wykonywanie subskrypcji samoobsługowej. Jeśli ten parametr jest ustawiony na $false, użytkownicy nie mogą wykonywać subskrypcji samoobsługowej. 
  
  > [!NOTE]
  > Utworzenie przepływu i rozwiązanie PowerApps konta wersji próbnej nie są kontrolowane przez **AllowAdHocSubscriptions** ustawienie. Aby uzyskać więcej informacji zobacz następujące artykuły:
  > * [Jak zapobiec Mój istniejący użytkownicy z zacznie korzystać z usługi Power BI](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Przepływ w organizacji pytań i odpowiedzi](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak formanty działają razem?
Tych parametrów można w połączeniu do definiowania dokładniejszej kontroli nad subskrypcji samoobsługowej. Na przykład następujące polecenie umożliwi użytkownikom wykonywanie subskrypcji samoobsługowej, ale tylko wtedy, jeśli ci użytkownicy mają już konto w usłudze Azure AD (innymi słowy, użytkownicy potrzebują zweryfikować adres e-mail konta należy utworzyć najpierw nie można wykonać subskrypcji samoobsługowej):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
Poniższy schemat wyjaśniono różnych kombinacji tych parametrów i wynikowy warunki katalogu i subskrypcji samoobsługowej.

![][1]

Aby uzyskać dodatkowe informacje i przykłady dotyczące używania tych parametrów, zobacz [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](add-custom-domain.md)
* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
