---
title: Powiadomienia Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się, jak powiadomienia obsługują Twoje działania dochodzeniowe.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9da0552e3ccc707c6b2f228b402f4e9db7dafee3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125701"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Powiadomienia Azure Active Directory Identity Protection

Azure AD Identity Protection wysyła dwa typy zautomatyzowanych wiadomości e-mail z powiadomieniami, które ułatwiają zarządzanie ryzykiem użytkownika i wykrywaniem ryzyka:

- Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail
- Tygodniowy adres e-mail w postaci skróconej

Ten artykuł zawiera omówienie obu wiadomości e-mail z powiadomieniami.

## <a name="users-at-risk-detected-email"></a>Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail

W odpowiedzi na wykryte konto zagrożone Azure AD Identity Protection generuje alert e-mail z **użytkownikami narażonymi na ryzyko wykryte** jako temat. Wiadomość e-mail zawiera link do raportu **[Użytkownicy oflagowani w związku z ryzykiem](../reports-monitoring/concept-user-at-risk.md)** . Najlepszym rozwiązaniem jest natychmiastowe badanie narażonych użytkowników.

Konfiguracja tego alertu umożliwia określenie poziomu ryzyka użytkownika, który ma zostać wygenerowany przez alert. Wiadomość e-mail zostanie wygenerowana, gdy poziom ryzyka użytkownika osiągnie określone elementy. jednak nie otrzymasz nowych użytkowników na ryzyko wykrycia alertów e-mail dla tego użytkownika po przejściu na ten poziom ryzyka użytkownika. Jeśli na przykład ustawisz zasady, aby otrzymywać alerty na średnim ryzyku użytkownika, a użytkownik Jan przejdzie do średniego ryzyka, otrzymasz wiadomość e-mail z informacją o ryzyku wykrytym przez Jan. Jednak nie otrzymasz alertu drugiego użytkownika w przypadku wykrycia ryzyka, jeśli Jan następnie przejdzie do wysokiego ryzyka lub ma dodatkowe wykrycie ryzyka.

![Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail](./media/notifications/01.png)

### <a name="configuration"></a>Konfigurowanie

Jako administrator możesz ustawić:

- **Poziom ryzyka użytkownika wyzwalający generowanie tej wiadomości e-mail** — domyślnie poziom ryzyka jest ustawiany na "wysoki".
- Adresaci **tej wiadomości e-mail** — domyślnie odbiorcy obejmują wszystkich administratorów globalnych. Administratorzy globalni mogą również dodawać innych administratorów globalnych, administratorów zabezpieczeń, czytelników zabezpieczeń jako odbiorców.  

Aby otworzyć okno dialogowe pokrewne, kliknij pozycję **alerty** w sekcji **Ustawienia** na stronie **Ochrona tożsamości** .

![Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>Tygodniowy adres e-mail w postaci skróconej

Cotygodniowa wiadomość e-mail z podsumowaniem zawiera streszczenie nowych wykrytych zagrożeń.  
Obejmuje:

- Narażeni użytkownicy
- Podejrzane działania
- Wykryte luki w zabezpieczeniach
- Linki do pokrewnych raportów w usłudze Identity Protection

    ![Korygowanie](./media/notifications/400.png "Korygowanie")

### <a name="configuration"></a>Konfigurowanie

Jako administrator możesz wyłączyć wysyłanie Cotygodniowych wiadomości e-mail z podsumowaniem.

![Czynniki ryzyka użytkownika](./media/notifications/62.png "Czynniki ryzyka użytkownika")

Aby otworzyć okno dialogowe pokrewne, kliknij pozycję **cotygodniowe podsumowanie** w sekcji **Ustawienia** na stronie **Ochrona tożsamości** .

![Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail](./media/notifications/04.png)

## <a name="see-also"></a>Zobacz także

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
