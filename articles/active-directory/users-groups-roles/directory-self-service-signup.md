---
title: Samoobsługowe tworzenie konta dla kont użytkowników weryfikowany pocztą e-mail — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Użyj Samoobsługowe tworzenie konta w dzierżawie usługi Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7aee10780512e284faccadface0dc928ef8270e
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501896"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Co to jest Samoobsługowe tworzenie konta usługi Azure Active Directory?

W tym artykule wyjaśniono, jak wypełnić organizacji w usłudze Azure Active Directory (Azure AD) za pomocą samoobsługowej. Jeśli chcesz przejąć nazwę domeny z niezarządzanego platformy Azure AD w organizacji, zobacz [przejmowanie katalogu niezarządzanego, jako administrator](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Dlaczego warto używać Samoobsługowe tworzenie konta?
* Pobierz klientów do usług, jaki mu pasuje, szybciej
* Utwórz bazujące na poczcie e-mail oferty usługi
* Tworzenie przepływów rejestracji bazujące na poczcie e-mail, które szybko Zezwalaj użytkownikom na tworzenie tożsamości za pomocą ich aliasów e-mail łatwa do zapamiętania pracy
* Niezależne-eksploatacyjnych utworzonego katalogu usługi Azure AD mogą być uwzględniane zarządzany katalog, który może służyć do innych usług

## <a name="terms-and-definitions"></a>Terminy i definicje
* **Samoobsługowa**: Jest to metoda, za pomocą którego użytkownik rejestruje się w usłudze w chmurze i tworzone automatycznie dla nich w usłudze Azure AD tożsamości opartego na swojej domeny poczty e-mail.
* **Niezarządzany katalog usługi Azure AD**: Jest to katalog, w którym zostanie utworzona tej tożsamości. Niezarządzanego katalogu jest katalogiem, który nie ma administratora globalnego.
* **Weryfikowany pocztą e-mail użytkownika**: Jest to typ konta użytkownika w usłudze Azure AD. Użytkownik, który ma automatycznie utworzone po zarejestrowaniu się do oferty samoobsługowego tożsamość jest określany jako użytkownik weryfikowany pocztą e-mail. Użytkownik weryfikowany pocztą e-mail jest regularny członek katalogu oznakowane za pomocą creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Jak kontrolować ustawienia samoobsługowego?
Administratorzy mają dwie kontrolki samoobsługi już dziś. Kontrolować, czy:

* Użytkownicy mogą dołączyć do katalogu za pośrednictwem poczty e-mail
* Użytkownicy mogą sami licencji dla aplikacji i usług

### <a name="how-can-i-control-these-capabilities"></a>Jak kontrolować, te funkcje?
Administrator może skonfigurować te funkcje, korzystając z następujących parametrów polecenia cmdlet Set-MsolCompanySettings usługi Azure AD:

* **AllowEmailVerifiedUsers** Określa, czy użytkownik może utworzyć, lub Dołącz do katalogu. Jeśli ustawisz ten parametr $false, użytkownik nie weryfikowany pocztą e-mail można dołączyć do katalogu.
* **AllowAdHocSubscriptions** steruje możliwością użytkownikom wykonywanie samoobsługowej. Jeśli ustawisz ten parametr $false, żaden użytkownik nie można wykonać samoobsługowej.
  
AllowEmailVerifiedUsers i AllowAdHocSubscriptions są ustawienia obejmujące cały katalog, które mogą być stosowane do zarządzanego lub niezarządzanego katalogu. Oto przykład, gdzie:

* Administrowanie katalogiem, za pomocą zweryfikowanej domeny, np. contoso.com
* Użyj współpracy B2B z innego katalogu można zaprosić użytkownika, który już nie istnieje (userdoesnotexist@contoso.com) w katalogu głównym domeny contoso.com
* Katalog macierzysty ma AllowEmailVerifiedUsers włączona

Jeśli powyższe warunki są spełnione, następnie użytkownika elementu członkowskiego jest tworzony w katalogu macierzystego, a użytkownik-Gość B2B jest tworzony w katalogu zapraszania.

Flow i PowerApps rejestracji wersji próbnej nie są kontrolowane przez **AllowAdHocSubscriptions** ustawienie. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Jak uniemożliwić istniejącym użytkownikom z Rozpoczynanie korzystania z usługi Power BI](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Usługa Flow w Twojej organizacji, pytania i odpowiedzi](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak formanty działają razem?
Te dwa parametry może służyć w połączeniu do definiowania bardziej precyzyjną kontrolę nad samoobsługowej. Na przykład następujące polecenie umożliwi użytkownikom wykonywanie Samoobsługowe tworzenie konta, ale tylko wtedy, jeśli ci użytkownicy mają już konta w usłudze Azure AD (innymi słowy, użytkownicy potrzebują konta weryfikowany pocztą e-mail do utworzenia najpierw nie można wykonać samoobsługowej):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Następujące schemat blokowy opisano różne kombinacje tych parametrów i wynikowe warunki do katalogu i samoobsługowej.

![Schemat blokowy samoobsługowego tworzenia konta formantów](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Aby uzyskać więcej informacji i przykłady dotyczące używania tych parametrów, zobacz [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Kolejne kroki

* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
