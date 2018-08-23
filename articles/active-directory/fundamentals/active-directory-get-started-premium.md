---
title: Tworzenie konta w usłudze Azure AD w wersji Premium | Microsoft Docs
description: Opisuje sposób rejestrowania w usłudze Azure Active Directory — wersja Premium.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2017
ms.author: lizross
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: a5ede142b7f3dd314d1a6b3eb8f100ec55a4ea4e
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42024007"
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>Szybki start: Rejestrowanie w usłudze Azure Active Directory — wersja Premium
Aby rozpocząć pracę z usługą Azure Active Directory (Azure AD) — wersja Premium, możesz zakupić licencje i skojarzyć je ze swoją subskrypcją platformy Azure. Jeśli tworzysz nową subskrypcję platformy Azure, musisz również aktywować plan licencjonowania i dostęp do usługi Azure AD zgodnie z opisem w poniższych sekcjach. 

## <a name="sign-up-for-active-directory-premium"></a>Rejestrowanie w usłudze Active Directory — wersja Premium
W usłudze Active Directory — wersja Premium można zarejestrować się na kilka sposobów: 
* Użyj swojej subskrypcji platformy Azure lub usługi Office 365.
* Użyj planu licencjonowania pakietu Enterprise Mobility + Security.
* Użyj planu licencjonowania zbiorowego firmy Microsoft.

### <a name="azure-or-office-365"></a>Platforma Azure lub usługa Office 365 
Jako subskrybent platformy Azure lub usługi Office 365 możesz kupić usługę Azure Active Directory — wersja Premium online. 

Aby uzyskać szczegółowy opis kroków, zobacz [How to Purchase Azure Active Directory Premium - Existing Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) (Jak kupić usługę Azure Active Directory — wersja Premium: istniejący klienci) lub [How to Purchase Azure Active Directory Premium - New Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers) (Jak kupić usługę Azure Active Directory — wersja Premium: nowi klienci).  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Usługa Enterprise Mobility + Security (EMS) to ekonomiczne rozwiązanie dla organizacji korzystających z następujących usług w ramach jednego planu licencjonowania: Azure Active Directory — wersja Premium, Azure Information Protection i Microsoft Intune. Więcej o usłudze EMS dowiesz się w [witrynie internetowej usługi Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security), a o typach licencji usługi EMS, które można kupić, na stronie [Enterprise Mobility + Security Pricing Options (Opcje cenowe dla usługi Enterprise Mobility + Security)](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing).  

Możesz rozpocząć pracę z usługą Azure AD za pomocą licencji usługi EMS przy użyciu jednej z poniższych opcji licencjonowania:

- Wypróbuj usługę EMS w ramach bezpłatnej [subskrypcji wersji próbnej usługi Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)
- Kup [licencje usługi Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- Kup [licencje usługi Enterprise Mobility + Security E3](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Licencjonowanie zbiorowe firmy Microsoft
Usługa Azure Active Directory — wersja Premium jest dostępna na podstawie [umowy Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 lub więcej licencji) lub za pośrednictwem programu [licencji zbiorowej Open](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5-250 licencji).

Dowiedz się więcej o opcjach zakupu licencjonowania zbiorowego na stronie [Jak kupować w ramach licencjonowania zbiorowego](https://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).

> [!NOTE]
> Klienci w Chinach mogą używać wersji Premium i Podstawowa usługi Azure Active Directory za pomocą wystąpienia usługi Azure Active Directory dostępnego na całym świecie. Wersje Premium i Podstawowa usługi Azure Active Directory nie są obecnie obsługiwane w usłudze Microsoft Azure świadczonej przez 21Vianet w Chinach. Aby uzyskać więcej informacji, skontaktuj się z nami na [forum usługi Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Jeśli wcześniej zostały zakupione i aktywowane licencje usługi Azure AD dla tej samej subskrypcji platformy Azure, która była używana w poprzednich krokach, licencje zostaną automatycznie aktywowane w tym samym katalogu. W przeciwnym razie wykonaj czynności opisane w dalszej części tego artykułu.

## <a name="activate-your-license-plan"></a>Aktywowanie planu licencjonowania
Czy to Twój pierwszy plan licencjonowania usługi Azure AD kupiony od firmy Microsoft? Jeśli tak, zostanie wygenerowana wiadomość e-mail z potwierdzeniem, która zostanie wysłana do Ciebie po zakończeniu zakupu. Ta wiadomość e-mail będzie potrzebna do aktywowania Twojego pierwszego planu licencjonowania.

**Aby aktywować plan licencjonowania, wykonaj jedną z następujących czynności:**

1. Aby rozpocząć aktywację, kliknij pozycję **Zaloguj** lub **Utwórz konto**.
   
    ![Logowanie](media/active-directory-get-started-premium/MOLSEmail.png)

    - Jeśli masz istniejącą dzierżawę, kliknij pozycję **Zaloguj**, aby zalogować się przy użyciu istniejącego konta administratora. Zaloguj się przy użyciu poświadczeń administratora globalnego dla dzierżawy, w której muszą zostać aktywowane licencje.

    - Jeśli chcesz utworzyć nową dzierżawę usługi Azure AD do użycia w ramach planu licencjonowania, kliknij pozycję **Utwórz konto**, aby otworzyć okno dialogowe **Tworzenie profilu konta**.

        ![Tworzenie profilu konta](media/active-directory-get-started-premium/MOLSAccountProfile.png)

Gdy wszystko będzie gotowe, zostanie wyświetlone następujące okno dialogowe jako potwierdzenie aktywowania planu licencjonowania dla dzierżawy:

![Potwierdzenie](media/active-directory-get-started-premium/MOLSThankYou.png)

## <a name="activate-your-azure-active-directory-access"></a>Aktywowanie dostępu do usługi Azure Active Directory
Jeśli dodajesz nowe licencje usługi Azure AD — wersja Premium do istniejącej subskrypcji, Twój dostęp do usługi Azure AD powinien już być aktywowany. W przeciwnym razie musisz aktywować dostęp do usługi Azure AD po otrzymaniu **powitalnej wiadomości e-mail**.  

Gdy kupione licencje zostaną aprowizowane w Twoim katalogu, zostanie do Ciebie wysłana **powitalna wiadomość e-mail**. Wiadomość e-mail stanowi potwierdzenie, że możesz rozpocząć zarządzanie licencjami i funkcjami usługi Azure Active Directory — wersja Premium lub pakietu Enterprise Mobility Suite + Security. 

> [!TIP]
> Nie masz dostępu do usługi Azure AD dla nowej dzierżawy do momentu aktywowania dostępu do katalogu usługi Azure AD przy użyciu powitalnej wiadomości e-mail wysyłanej automatycznie po ukończeniu procesu aprowizacji licencji. 

**Aby uaktywnić Twój dostęp do usługi Azure AD, wykonaj następujące kroki:**

1. W Twojej **powitalnej wiadomości e-mail** kliknij pozycję **Zaloguj**. 
   
    ![Powitalna wiadomość e-mail](media/active-directory-get-started-premium/AADEmail.png)
2. Po pomyślnym zalogowaniu się musisz również przejść drugi etap uwierzytelniania przy użyciu urządzenia przenośnego:
   
    ![Weryfikacja mobilna](media/active-directory-get-started-premium/SignUppage.png)

Aktywacja powinna trwać tylko kilka minut, a następnie będziesz mieć dostęp do zarządzania usługą Azure AD. 

## <a name="next-steps"></a>Następne kroki
Po zapoznaniu się z tym przewodnikiem szybkiego startu wiesz już, jak zarejestrować się w usłudze Azure AD — wersja Premium i jak aktywować dostęp do usługi Azure Active Directory. 

Jeśli masz już subskrypcję platformy Azure, możesz użyć następującego linku, aby rozpocząć okres próbny lub kupić licencje usługi Azure AD — wersja Premium w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Aktywuj licencje usługi Azure AD — wersja Premium](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade)
