---
title: Jak zablokować słabe hasła w usłudze Azure AD — Azure Active Directory
description: Zablokuj słabe hasła z envirionment za pomocą usługi Azure AD dynamicznie przekazana passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb47b9df51803c76662b5fb4ca1fe23740e7af9a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155064"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurowanie listy niestandardowo zakazanych haseł

W wielu organizacjach użytkownicy mogą tworzyć hasła przy użyciu wspólnych słów lokalnych, takich jak szkoła, zespół sportowy lub osoba sławę, pozostawiając im łatwy do odgadnięcia. Niestandardowa lista zablokowanych haseł firmy Microsoft umożliwia organizacjom Dodawanie ciągów do szacowania i blokowania, poza globalną listą haseł zabronionych, gdy użytkownicy i Administratorzy próbują zmienić lub zresetować hasło.

## <a name="add-to-the-custom-list"></a>Dodaj do listy niestandardowej

Skonfigurowanie niestandardowej listy haseł zabronionych wymaga licencji na Azure Active Directory — wersja Premium P1 lub P2. Aby uzyskać szczegółowe informacje na temat licencjonowania Azure Active Directory, zobacz [stronę cennika Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do **Azure Active Directory** > **zabezpieczenia** > **metody uwierzytelniania** > **ochronę hasłem**.
1. Ustaw opcję **Wymuszaj listę niestandardową**na **wartość tak**.
1. Dodawanie ciągów do **listy niestandardowych zakazanych haseł**, jeden ciąg na wiersz
   * Niestandardowa lista wykluczonych haseł może zawierać do 1000 warunków.
   * Niestandardowa lista wykluczonych haseł nie uwzględnia wielkości liter.
   * Niestandardowa lista wykluczonych haseł uwzględnia wspólne podstawienie znaków.
      * Przykład: "o" i "0", "a" i "\@"
   * Minimalna długość ciągu to cztery znaki, a maksymalna to 16 znaków.
1. Po dodaniu wszystkich ciągów kliknij przycisk **Zapisz**.

> [!NOTE]
> Aktualizacja listy niestandardowo zakazanych haseł może potrwać kilka godzin.

> [!NOTE]
> Niestandardowa lista wykluczonych haseł jest ograniczona do maksymalnie 1000 warunków. Nie jest ona przeznaczona do blokowania bardzo dużych list haseł. Aby w pełni wykorzystać zalety niestandardowej listy wykluczonych haseł, firma Microsoft zaleca, aby najpierw przejrzeć i zrozumieć zamierzone projektowanie i użycie niestandardowo wykluczonej listy haseł (zobacz [Niestandardowa lista wykluczonych haseł](concept-password-ban-bad.md#custom-banned-password-list)), a także algorytm oceny hasła (zobacz [jak są oceniane hasła](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Zmodyfikuj niestandardową listę wykluczonych haseł w obszarze metody uwierzytelniania w Azure Portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Zasady działania

Za każdym razem, gdy użytkownik lub administrator resetuje lub zmienia hasło usługi Azure AD, przechodzi przez listę wykluczonych haseł, aby potwierdzić, że nie znajduje się na liście. Ta kontrola jest uwzględniana w dowolnych hasłach ustawionych lub zmienianych przy użyciu usługi Azure AD.

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Gdy użytkownik próbuje zresetować hasło do elementu, który mógłby zostać zabroniony, zobaczy jeden z następujących komunikatów o błędzie:

* Niestety, hasło zawiera słowo, frazę lub wzorzec, które ułatwiają odgadnięcie hasła. Spróbuj ponownie, używając innego hasła.
* Niestety, nie można użyć tego hasła, ponieważ zawiera słowa lub znaki, które zostały zablokowane przez administratora. Spróbuj ponownie, używając innego hasła.

## <a name="next-steps"></a>Następne kroki

[Omówienie pojęć dotyczących list zakazanych haseł](concept-password-ban-bad.md)

[Omówienie pojęć związanych z ochroną hasłem w usłudze Azure AD](concept-password-ban-bad-on-premises.md)

[Włącz integrację lokalną z listami zabronionych haseł](howto-password-ban-bad-on-premises.md)
