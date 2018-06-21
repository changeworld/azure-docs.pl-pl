---
title: Jak zakaz haseł w usłudze Azure AD
description: Zakaz słabe hasła z Twojej envirionment z usługi Azure AD dynamicznie zakazane passwrords
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 0c1517f94d4a6d59077b62614eec8fef665b1529
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296141"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurowanie listy zabronionych hasła niestandardowego

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy zabronionych hasła niestandardowego są funkcje publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

W wielu organizacjach find użytkownikom tworzenie haseł przy użyciu popularnych wyrazów lokalnych, takich jak służbowe, sportowych zespołu lub osoby Słynne, pozostawiając je łatwe do odgadnięcia. Listy zabronionych hasła niestandardowego firmy Microsoft umożliwia organizacjom dodać parametry do oceny i blokowanie, oprócz globalnej liście haseł zakazane po użytkownikom i administratorom próby zmiany lub resetowania hasła.

## <a name="add-to-the-custom-list"></a>Dodaj do listy niestandardowych

Konfigurowanie listy zabronionych hasła niestandardowego wymaga licencji usługi Azure Active Directory Premium P1 lub P2. Aby uzyskać szczegółowe informacje dotyczące licencjonowania usługi Azure Active Directory, zobacz [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory**, **metod uwierzytelniania**, następnie **ochrony hasłem (wersja zapoznawcza)**.
1. Ustaw opcję **Wymuś niestandardowej listy**, do **tak**.
1. Dodawanie ciągów **niestandardowe zakazane liście haseł**, jednego ciągu w jednym wierszu
   * Lista niestandardowych zabronione hasło może zawierać maksymalnie 1000 słów.
   * Listy zabronionych hasła niestandardowego nie uwzględnia wielkości liter.
   * Na liście hasła niestandardowego zabronione uwzględnia wspólnej podstawianie znaków.
      * Przykład: "o" i "0" lub "a" i "@"
   * Minimalna długość ciągu jest cztery znaki, a maksymalna to 16 znaków.
1. Po dodaniu wszystkich ciągów, kliknij przycisk **zapisać**.

> [!NOTE]
> Może upłynąć kilka godzin aktualizacji do listy zabronionych hasła niestandardowego ma zostać zastosowany.

![Zmodyfikuj listę niestandardowych zabronione hasło w obszarze metod uwierzytelniania w portalu Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Jak to działa

Za każdym razem użytkownik lub administrator resetuje lub zmiany hasła usługi Azure AD, przepłynie za pomocą listy zabronionych hasło, aby upewnić się, że nie jest na liście. Ten test jest dołączony do hasła ustawić lub zmienić za pomocą usługi Azure AD.

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Jeśli użytkownik próbuje zresetować hasło do zasobu, który będzie zakazane, zobaczy następujący komunikat o błędzie:

Niestety hasło zawiera słowo, wyrażenie lub wzorca, który umożliwia łatwe do odgadnięcia hasła. Spróbuj ponownie, używając innego hasła.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie pojęć dotyczących listy zabronionych haseł](concept-password-ban-bad.md)

[Omówienie pojęć dotyczących ochrony hasłem usługi Azure AD](concept-password-ban-bad-on-premises.md)

[Włącz integrację lokalnej z listy zabronionych haseł](howto-password-ban-bad-on-premises.md)
