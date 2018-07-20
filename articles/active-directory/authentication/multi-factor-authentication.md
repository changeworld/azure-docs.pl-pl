---
title: Dowiedz się więcej o weryfikacji dwuetapowej w usłudze Azure MFA | Dokumentacja firmy Microsoft
description: Co to jest usługa Azure Multi-Factor Authentication, dlaczego warto używać usługi MFA i różnych metod i wersje dostępne.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d58d81d85dac7e5cd520b8d8a3fb5d91650e0cbe
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161609"
---
# <a name="what-is-azure-multi-factor-authentication"></a>Co to jest usługa Multi-Factor Authentication platformy Azure?

Weryfikacja dwuetapowa jest metodą uwierzytelniania, która wymaga więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. To działa, wymagając dowolne dwa lub więcej z poniższych metod weryfikacji:

* Coś, co wiesz (zwykle hasła)
* Coś, co masz (zaufane urządzenie, którego nie można łatwo zduplikować, takie jak telefon)
* Coś, co Ciebie cechuje (biometria)

<center>![Nazwa użytkownika i hasło](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![certyfikaty](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Inteligentne Phone](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![karty inteligentnej](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Wirtualnej karty inteligentnej](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![nazwy użytkownika i hasła](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft służące do przeprowadzania weryfikacji dwuetapowej. Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Oferuje ona silne uwierzytelnianie za pośrednictwem różnych metod weryfikacji, w tym weryfikacji w trakcie rozmowy telefonicznej albo przy użyciu wiadomości SMS lub aplikacji mobilnej.

## <a name="why-use-azure-multi-factor-authentication"></a>Dlaczego warto używać usługi Azure Multi-Factor Authentication?
Już dziś, więcej niż kiedykolwiek wcześniej osób coraz bardziej są połączone. Za pomocą smartfonach, tabletach, komputery przenośne i komputery osoby mają wiele opcji dostępu do swoich kont i aplikacji z dowolnego miejsca i pozostawanie w dowolnym momencie.

Uwierzytelnianie wieloskładnikowe systemu Azure to łatwy w użyciu, skalowalne i niezawodne rozwiązanie, które oferuje drugiej metody uwierzytelniania, aby chronić użytkowników.

| ![Łatwość użycia](./media/multi-factor-authentication/simple.png) | ![Skalowalność](./media/multi-factor-authentication/scalable.png) | ![Zawsze chronione](./media/multi-factor-authentication/protected.png) | ![Niezawodność](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Łatwe w użyciu** |**Skalowalna** |**Zawsze chronione** |**Niezawodne** |

* **Łatwe w użyciu** — uwierzytelnianie wieloskładnikowe systemu Azure jest prosta do konfigurowania i używania. Dodatkową ochronę, który jest dostarczany z usługą Azure Multi-Factor Authentication umożliwia użytkownikom zarządzanie swoimi własnymi urządzeniami. Najlepsze, w wielu przypadkach go można skonfigurować pod kątem przy użyciu jedynie kilku kliknięć.
* **Skalowalne** — usługi Azure Multi-Factor Authentication wykorzystuje możliwości chmury i integruje się z lokalnej usługi AD i aplikacje niestandardowe. Ta ochrona nawet jest rozszerzona na potrzeby scenariuszy obejmujących mocno obciążające o kluczowym znaczeniu.
* **Zawsze chronione** — uwierzytelnianie wieloskładnikowe systemu Azure zapewnia silne uwierzytelnianie za pomocą najwyższych standardów branżowych.
* **Niezawodne** — firma Microsoft gwarantuje więc 99,9% dostępności usługi Azure Multi-Factor Authentication. Usługa jest uznawana za niedostępną, jeśli nie może odbierać lub przetwarzać żądania weryfikacji na potrzeby weryfikacji dwuetapowej.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [działania usługi Azure Multi-Factor Authentication](concept-mfa-howitworks.md)

- Przeczytaj o różnych [wersji i metody użycia usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md)
