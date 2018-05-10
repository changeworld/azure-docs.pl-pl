---
title: Dowiedz się więcej o weryfikacji dwuetapowej w usługi Azure MFA | Dokumentacja firmy Microsoft
description: Co to jest usługa Azure Multi-Factor Authentication, dlaczego warto używać usługi MFA i różnych metod i wersje dostępne.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 2bf2cbefb2b60ad5278bdee7bd97aff1ed6212a4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="what-is-azure-multi-factor-authentication"></a>Co to jest usługa Multi-Factor Authentication platformy Azure?

Weryfikacja dwuetapowa to metoda uwierzytelniania, która wymaga więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. Działania, gdyż dowolne dwa lub więcej z następujących metod weryfikacji:

* Coś znasz (zwykle hasła)
* Coś (zaufanych urządzeń, który nie jest łatwo zduplikowany, takich jak telefon)
* Coś jest (biometria)

<center>![Nazwa użytkownika i hasło](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![certyfikaty](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Inteligentne Phone](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![kart inteligentnych](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Wirtualnej karty inteligentnej](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![nazwy użytkownika i hasła](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft służące do przeprowadzania weryfikacji dwuetapowej. Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Oferuje ona silne uwierzytelnianie za pośrednictwem różnych metod weryfikacji, w tym weryfikacji w trakcie rozmowy telefonicznej albo przy użyciu wiadomości SMS lub aplikacji mobilnej.

## <a name="why-use-azure-multi-factor-authentication"></a>Dlaczego warto używać usługi Azure Multi-Factor Authentication?
Już dziś, więcej niż kiedykolwiek osoby coraz są połączone. W przypadku inteligentne telefony, tablety, komputery przenośne i komputery osoby mają wiele opcji, dostęp do swoich kont i aplikacji z dowolnego miejsca i łączność w dowolnym momencie.

Uwierzytelnianie wieloskładnikowe platformy Azure jest łatwy w użyciu, skalowalne i niezawodne rozwiązanie, które oferuje drugi metoda uwierzytelniania, aby chronić użytkowników.

| ![Łatwość użycia](./media/multi-factor-authentication/simple.png) | ![Skalowalność](./media/multi-factor-authentication/scalable.png) | ![Chronione przez cały czas](./media/multi-factor-authentication/protected.png) | ![Niezawodność](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Łatwy w użyciu** |**Skalowalne** |**Chronione przez cały czas** |**Niezawodne** |

* **Łatwy w użyciu** -Azure Multi-Factor Authentication jest prosta do konfigurowania i używania. Dodatkowa ochrona, dołączanego do usługi Azure Multi-Factor Authentication umożliwia użytkownikom zarządzanie swoimi własnymi urządzeniami. Najlepsze, w wielu przypadkach go można skonfigurować kilka prostych kliknięć.
* **Skalowalne** — uwierzytelnianie wieloskładnikowe Azure korzysta z możliwości chmury i integruje się z lokalnymi AD i aplikacji niestandardowych. Ta ochrona jest nawet rozszerzony do dużego, kluczowych scenariuszy.
* **Chronione przez cały czas** -Azure Multi-Factor Authentication udostępnia silnego uwierzytelniania za pomocą najwyższymi standardami branżowymi.
* **Niezawodne** -Microsoft gwarantuje dostępność usługi Azure Multi-Factor Authentication 99,9%. Usługa jest uznawana za niedostępną, gdy nie może otrzymywać lub przetwarzania żądania weryfikacji na potrzeby weryfikacji dwuetapowej.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [jak działa usługa Azure Multi-Factor Authentication](concept-mfa-howitworks.md)

- Przeczytaj informacje o różnych [wersji i metody zużycia uwierzytelnianie wieloskładnikowe Azure](concept-mfa-licensing.md)
