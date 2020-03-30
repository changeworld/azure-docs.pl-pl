---
title: Powiadomienia o ochronie tożsamości usługi Azure Active Directory
description: Dowiedz się, jak powiadomienia wspierają Twoje działania dochodzeniowe.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120130"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Powiadomienia o ochronie tożsamości usługi Azure Active Directory

Usługa Azure AD Identity Protection wysyła dwa typy automatycznych powiadomień e-mail, aby ułatwić zarządzanie ryzykiem użytkownika i wykrywaniem ryzyka:

- Użytkownicy narażeni na ryzyko wykrytych wiadomości e-mail
- Cotygodniowa wiadomość e-mail z podsumowaniem

Z tego artykułu dowiesz się, że oba wiadomości e-mail z powiadomieniami.

## <a name="users-at-risk-detected-email"></a>Użytkownicy narażeni na ryzyko wykrytych wiadomości e-mail

W odpowiedzi na wykryte konto zagrożone usługa Azure AD Identity Protection generuje alert e-mail z **użytkownikami zagrożonymi wykrytymi** jako podmiot. Wiadomość e-mail zawiera link do **[użytkowników oflagowanych do](../reports-monitoring/concept-user-at-risk.md)** raportu o ryzyku. Najlepszym rozwiązaniem należy natychmiast zbadać użytkowników zagrożonych.

Konfiguracja tego alertu umożliwia określenie, na jakim poziomie ryzyka użytkownika ma zostać wygenerowany alert. Wiadomość e-mail zostanie wygenerowana, gdy poziom ryzyka użytkownika osiągnie określony poziom; jednak nie otrzymasz nowych użytkowników zagrożonych alertów e-mail dla tego użytkownika po przejściu do tego poziomu ryzyka użytkownika. Na przykład jeśli ustawisz zasady, aby alerty na średnie ryzyko użytkownika i john użytkownika przenosi się do średniego ryzyka, otrzymasz użytkowników na ryzyko wykryte wiadomości e-mail dla Jana. Jednak nie otrzymasz drugiego użytkownika z ryzykiem wykrytego alertu, jeśli John następnie przechodzi do wysokiego ryzyka lub ma dodatkowe wykrywanie ryzyka.

![Użytkownicy narażeni na ryzyko wykrytych wiadomości e-mail](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurowanie użytkowników z wykrytymi alertami

Jako administrator możesz ustawić:

- **Poziom ryzyka użytkownika, który wyzwala generowanie tej wiadomości e-mail** — domyślnie poziom ryzyka jest ustawiony na "Wysokie" ryzyko.
- **Adresaci tej wiadomości e-mail** — domyślnie adresaci obejmują wszystkich administratorów globalnych. Administratorzy globalni mogą również dodawać innych administratorów globalnych, administratorów zabezpieczeń, czytników zabezpieczeń jako adresatów.
   - Opcjonalnie można **dodać dodatkowe wiadomości e-mail do odbierania powiadomień alertów** ta funkcja jest w wersji zapoznawczej i użytkownicy zdefiniowane muszą mieć odpowiednie uprawnienia do wyświetlania połączonych raportów w witrynie Azure portal.

Skonfiguruj zagrożone wiadomości e-mail użytkowników w **witrynie Azure portal** w obszarze Użytkownicy**ochrony** > tożsamości**zabezpieczeń** >  **usługi Azure Active Directory,** > **którzy narażonych na nie wykryli alerty.**

## <a name="weekly-digest-email"></a>Cotygodniowa wiadomość e-mail z podsumowaniem

Cotygodniowy biuletyn pocztowy zawiera podsumowanie nowych wykrywania ryzyka.  
Obejmuje ona:

- Narażeni użytkownicy
- Podejrzane działania
- Wykryte luki w zabezpieczeniach
- Łącza do powiązanych raportów w ucho.

![Cotygodniowa wiadomość e-mail z podsumowaniem](./media/howto-identity-protection-configure-notifications/400.png)

Domyślnie adresatami są wszyscy administratorzy globalni. Administratorzy globalni mogą również dodawać innych administratorów globalnych, administratorów zabezpieczeń, czytników zabezpieczeń jako adresatów.

### <a name="configure-weekly-digest-email"></a>Konfigurowanie cotygodniowej wiadomości e-mail z podsumowaniem

Jako administrator możesz włączyć lub wyłączyć wysyłanie cotygodniowej wiadomości e-mail z podsumowaniem i wybrać użytkowników przypisanych do odbierania wiadomości e-mail.

Skonfiguruj cotygodniową wiadomość e-mail o skrótach w **witrynie Azure portal** w obszarze**Cotygodniowe podsumowanie**ochrony**tożsamości** > **zabezpieczeń** >  **usługi Azure Active Directory** > .

## <a name="see-also"></a>Zobacz też

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
