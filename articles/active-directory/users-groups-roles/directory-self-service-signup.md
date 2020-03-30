---
title: Samoobsługowa rejestracja dla użytkowników zweryfikowanych pocztą e-mail — Azure AD | Dokumenty firmy Microsoft
description: Korzystanie z samoobsługowej rejestracji w dzierżawie usługi Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027631"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Co to jest samoobsługowe rejestrowanie się w usłudze Azure Active Directory?

W tym artykule wyjaśniono, jak używać samoobsługowego rejestrowania w celu wypełniania organizacji w usłudze Azure Active Directory (Azure AD). Jeśli chcesz przejąć nazwę domeny od niezarządzanej organizacji usługi Azure AD, zobacz [Przejęcie niezarządzanego katalogu jako administrator](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Dlaczego warto skorzystać z samoobsługi?
* Szybsze uzyskiwanie klientów do usług, których chcą
* Tworzenie ofert opartych na posie: e-mail dla usługi
* Tworzenie przepływów rejestracji opartych na wiadomościach e-mail, które szybko umożliwiają użytkownikom tworzenie tożsamości przy użyciu łatwych do zapamiętania służbowych aliasów poczty e-mail
* Samoobsługowy katalog usługi Azure AD można przekształcić w katalog zarządzany, który może być używany w innych usługach

## <a name="terms-and-definitions"></a>Warunki i definicje
* **Samoobsługowa rejestracja:** Jest to metoda, za pomocą której użytkownik rejestruje się w usłudze w chmurze i ma tożsamość automatycznie utworzoną dla nich w usłudze Azure AD na podstawie ich domeny poczty e-mail.
* **Niezarządzany katalog usługi Azure AD:** Jest to katalog, w którym tworzona jest ta tożsamość. Katalog niezarządzany to katalog, który nie ma administratora globalnego.
* **Użytkownik zweryfikowany pocztą e-mail:** Jest to typ konta użytkownika w usłudze Azure AD. Użytkownik, który ma tożsamość utworzoną automatycznie po zarejestrowaniu się w celu uzyskania oferty samoobsługowej, jest znany jako użytkownik zweryfikowany pocztą e-mail. Użytkownik zweryfikowany pocztą e-mail jest zwykłym członkiem katalogu oznaczonego jako creationmethod=Email Verifiified.

## <a name="how-do-i-control-self-service-settings"></a>Jak sterować ustawieniami samoobsługi?
Administratorzy mają dziś dwie samoobsługowe mechanizmy kontroli. Mogą kontrolować, czy:

* Użytkownicy mogą dołączyć do katalogu za pośrednictwem poczty e-mail
* Użytkownicy mogą udzielać licencji na aplikacje i usługi

### <a name="how-can-i-control-these-capabilities"></a>Jak mogę kontrolować te możliwości?
Administrator może skonfigurować te funkcje przy użyciu następujących parametrów zestawu zabezpieczeń cmdlet usługi Azure AD Set-MsolCompanySettings:

* **AllowEmailVerifiedUsers** określa, czy użytkownik może utworzyć lub dołączyć do katalogu. Jeśli ustawisz ten parametr na $false, żaden użytkownik zweryfikowany pocztą e-mail nie może dołączyć do katalogu.
* **AllowAdHocSubscriptions** kontroluje możliwość wykonywania przez użytkowników samoobsługowej rejestracji. Jeśli ustawisz ten parametr na $false, żaden użytkownik nie może wykonać samoobsługowej rejestracji.
  
AllowEmailVerifiedUsers i AllowAdHocSubscriptions są ustawieniami dla całego katalogu, które można zastosować do katalogu zarządzanego lub niezarządzanego. Oto przykład, w którym:

* Administrujesz katalogiem ze zweryfikowaną domeną, taką jak contoso.com
* Współpraca B2B z innego katalogu służy do zapraszania użytkownika, który jeszcze nie istnieje (userdoesnotexist@contoso.com) w katalogu macierzystym contoso.com
* Katalog domowy ma włączony

Jeśli powyższe warunki są spełnione, użytkownik członkowski jest tworzony w katalogu macierzystym, a użytkownik-gość B2B jest tworzony w katalogu zapraszania.

Rejestracje w wersji próbnej Flow i PowerApps nie są kontrolowane przez ustawienie **AllowAdHocSubscriptions.** Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Jak uniemożliwić istniejącym użytkownikom rozpoczynanie korzystania z usługi Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Usługa Flow w organizacji — pytania i odpowiedzi](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak działają kontrole?
Te dwa parametry mogą być używane w połączeniu do definiowania bardziej precyzyjnej kontroli nad rejestracją samoobsługową. Na przykład następujące polecenie umożliwi użytkownikom samodzielne rejestrowanie, ale tylko wtedy, gdy ci użytkownicy mają już konto w usłudze Azure AD (innymi słowy, użytkownicy, którzy potrzebują konta zweryfikowanego pocztą e-mail, który ma zostać utworzony jako pierwszy, nie mogą samodzielnie zarejestrować się):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Poniższy schemat blokowy wyjaśnia różne kombinacje dla tych parametrów i wynikające z tego warunki dla katalogu i rejestracji samoobsługowej.

![schemat blokowy samoobsługowych kontroli rejestracji](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Aby uzyskać więcej informacji i przykładów używania tych parametrów, zobacz [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Następne kroki

* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Zamykanie konta służbowego w katalogu niezarządzanym](users-close-account.md)
