---
title: Środowisko użytkownika logowania sms dla numeru telefonu (wersja zapoznawcza) — Azure AD
description: Dowiedz się więcej o interfejsie użytkownika logowania sms dla nowych lub istniejących numerów telefonów
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378836"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Używanie numeru telefonu jako nazwy użytkownika (wersja zapoznawcza)

Rejestracja urządzenia daje telefonowi dostęp do usług organizacji i nie zezwala organizacji na dostęp do telefonu. Jeśli jesteś administratorem, możesz znaleźć więcej informacji w [Konfigurować i włączać użytkowników do uwierzytelniania opartego na programie SMS](../authentication/howto-authentication-sms-signin.md).

Jeśli twoja organizacja nie udostępniła logowania SMS, nie zobaczysz opcji, aby była dostępna podczas rejestracji telefonu na swoim koncie.  

## <a name="when-you-have-a-new-phone-number"></a>Gdy masz nowy numer telefonu

Jeśli otrzymasz nowy telefon lub nowy numer i zarejestrujesz go w organizacji, dla której jest dostępne logowanie SMS, wystąpi normalny proces rejestracji telefonu:

1. Wybierz **pozycję Dodaj metodę**.
1. Wybierz **pozycję Telefon**.
1. Wprowadź numer telefonu i wybierz **opcję Wyślij mi kod**.
1. Po wprowadzeniu kodu wybierz przycisk **Dalej**.
1. Pojawi się monit z napisem "SMS zweryfikowane. Twój telefon został pomyślnie zarejestrowany."

> [!Important]
> Ze względu na znany problem w wersji zapoznawczej, przez krótki czas dodanie numeru telefonu nie zarejestruje numeru logowania SMS. Musisz zalogować się przy pomocą dodanego numeru, a następnie postępować zgodnie z instrukcjami, aby zarejestrować numer logowania SMS.

### <a name="when-the-phone-number-is-in-use"></a>Gdy numer telefonu jest używany

Jeśli spróbujesz użyć numeru telefonu używanego przez inną osobę w organizacji, zostanie wyświetlony następujący komunikat:

![Komunikat o błędzie, gdy numer telefonu jest już używany](media/sms-sign-in-explainer/sms-sign-in-error.png)

Skontaktuj się z administratorem, aby naprawić problem.

## <a name="when-you-have-an-existing-number"></a>Gdy masz istniejący numer

Jeśli korzystasz już z numeru telefonu w organizacji i przy użyciu numeru telefonu jako nazwy użytkownika staje się dostępna, poniższe kroki mogą pomóc w zalogowaniu się.

1. Gdy logowanie sms jest dostępne, zostanie wyświetlony baner z pytaniem, czy chcesz włączyć numer telefonu do logowania się za pomocą wiadomości SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Ponadto po wybraniu cieście na kafelka metody telefonu pojawi się przycisk **Włącz:**

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Aby włączyć metodę, **wybierz**włącz . Zostanie wyświetlony monit o potwierdzenie akcji:

    ![Okno dialogowe potwierdzenia umożliwiające logowanie się sms-a dla numeru telefonu](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Wybierz pozycję **Włącz**.

## <a name="when-you-remove-your-phone-number"></a>Po usunięciu numeru telefonu

1. Aby usunąć numer telefonu, wybierz przycisk usuń na kafelku metody logowania SMS.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Po wyświetleniu monitu o potwierdzenie akcji wybierz przycisk **OK**.

Nie można usunąć numeru telefonu, który jest używany jako domyślna metoda logowania. Aby usunąć numer, należy zmienić domyślną metodę logowania, a następnie ponownie usunąć numer telefonu.
