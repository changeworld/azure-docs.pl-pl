---
title: Jak zablokować haseł w usłudze Azure AD
description: Zablokuj słabe hasła z Twojej envirionment za pomocą usługi Azure AD, dynamicznie zakazane passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30627994a8fe8cfe73e7945dadf2153ea60860b8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175136"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurowanie listy niestandardowej zakazanych haseł

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

W wielu organizacjach find użytkownikom tworzenie haseł przy użyciu typowych lokalnych słów, takich jak szkoły, drużynę lub osoby sławę, pozostawiając je łatwe do odgadnięcia. Listy niestandardowe zakazanych haseł przez firmę Microsoft umożliwia organizacjom dodać parametry do oceny i blokowania, oprócz globalnych zakazane lista hasła, gdy użytkownicy i Administratorzy podejmują próby zmiany lub resetowania hasła.

## <a name="add-to-the-custom-list"></a>Dodaj do listy niestandardowej

Konfigurowanie listy niestandardowej zakazanych haseł wymaga licencji usługi Azure Active Directory Premium P1 lub P2. Aby uzyskać szczegółowe informacje dotyczące licencjonowania usługi Azure Active Directory, zobacz [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do **usługi Azure Active Directory**, **metod uwierzytelniania**, następnie **ochrona za pomocą hasła (wersja zapoznawcza)**.
1. Ustaw opcję **wymuszania niestandardowej listy**, **tak**.
1. Dodaj parametry do **niestandardowe zakazane liście haseł**, jednego ciągu w każdym wierszu
   * Lista niestandardowych zakazanych haseł może zawierać maksymalnie 1000 słów.
   * Listy niestandardowe zakazanych haseł jest rozróżniana wielkość liter.
   * Listy niestandardowe zakazanych haseł uwzględnia wspólnej podstawianie znaków.
      * Przykład: "o" i "0" lub "a" i "\@"
   * Minimalna długość ciągu jest czterech znaków, a maksymalna to 16 znaków.
1. Po dodaniu wszystkich ciągów, kliknij przycisk **Zapisz**.

> [!NOTE]
> Może upłynąć kilka godzin aktualizacji do listy niestandardowe zakazanych haseł, które mają być stosowane.

![Modyfikowanie listy niestandardowej zakazanych haseł w ramach metod uwierzytelniania w witrynie Azure portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Jak to działa

Za każdym razem użytkownik lub administrator resetuje lub zmiany haseł usługi Azure AD, przepływa ona za pomocą listy zakazanych haseł, aby upewnić się, że nie jest na liście. Ten test jest uwzględniona w hasła ustawić lub zmienić za pomocą usługi Azure AD.

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Gdy użytkownik próbuje zresetować hasło do zasobu, który będzie zablokowany, zobaczą następujący komunikat o błędzie:

Niestety Twoje hasło zawiera słowo, frazę lub wzorzec, który sprawia, że hasło jest łatwe do odgadnięcia. Spróbuj ponownie, używając innego hasła.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie pojęć dotyczących listy zakazanych haseł](concept-password-ban-bad.md)

[Omówienie pojęć dotyczących ochrona za pomocą hasła usługi Azure AD](concept-password-ban-bad-on-premises.md)

[Włączanie integracji środowiska lokalnego z listy zakazanych haseł](howto-password-ban-bad-on-premises.md)
