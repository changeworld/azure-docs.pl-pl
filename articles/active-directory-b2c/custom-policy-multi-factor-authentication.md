---
title: Uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Jak włączyć uwierzytelnianie wieloskładnikowe w aplikacjach konsumenckich zabezpieczonych przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25724ba82e57c5e3800fa1a989dd4f504df1c163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189280"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory B2C

Usługa Azure Active Directory B2C (Azure AD B2C) integruje się bezpośrednio z [uwierzytelnianiem wieloskładnikowym platformy Azure,](../active-directory/authentication/multi-factor-authentication.md) dzięki czemu można dodać drugą warstwę zabezpieczeń, aby zarejestrować się i zarejestrować w aplikacjach. Uwierzytelniania wieloskładnikowego można włączyć bez pisania pojedynczego wiersza kodu. Jeśli utworzono już przepływy użytkowników rejestracji i logowania, nadal można włączyć uwierzytelnianie wieloskładnikowe.

Ta funkcja pomaga aplikacjom obsługiwać scenariusze, takie jak:

- Nie potrzebujesz uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do jednej aplikacji, ale wymagasz jej, aby uzyskać dostęp do innej. Na przykład klient może zalogować się do aplikacji ubezpieczenia automatycznego za pomocą konta społecznościowego lub lokalnego, ale musi zweryfikować numer telefonu przed dostępem do aplikacji ubezpieczenia domu zarejestrowanej w tym samym katalogu.
- Nie wymaga uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do aplikacji w ogóle, ale wymaga go, aby uzyskać dostęp do poufnych części w nim. Na przykład klient może zalogować się do aplikacji bankowej za pomocą konta społecznościowego lub lokalnego i sprawdzić saldo konta, ale musi zweryfikować numer telefonu przed próbą przelewu bankowego.

## <a name="set-multi-factor-authentication"></a>Ustawianie uwierzytelniania wieloskładnikowego

Podczas tworzenia przepływu użytkownika, masz możliwość włączenia uwierzytelniania wieloskładnikowego.

![Ustawianie uwierzytelniania wieloskładnikowego](./media/custom-policy-multi-factor-authentication/add-policy.png)

Ustaw **uwierzytelnianie wieloskładnikowe** na **Włączone**.

Można użyć **Uruchom przepływ użytkownika,** aby zweryfikować środowisko. Potwierdź następujący scenariusz:

Konto klienta jest tworzone w dzierżawie przed wystąpieniem kroku uwierzytelniania wieloskładnikowego. Podczas kroku klient jest proszony o podanie numeru telefonu i zweryfikowanie go. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu zostanie dołączony do konta do późniejszego użycia. Nawet jeśli klient anuluje lub wypada, klient może zostać poproszony o ponowne zweryfikowanie numeru telefonu podczas następnego logowania z włączonym uwierzytelnianiem wieloskładnikowym.

## <a name="add-multi-factor-authentication"></a>Dodawanie uwierzytelniania wieloskładnikowego

Możliwe jest włączenie uwierzytelniania wieloskładnikowego w przepływie użytkownika, który został wcześniej utworzony.

Aby włączyć uwierzytelnianie wieloskładnikowe:

1. Otwórz przepływ użytkownika, a następnie wybierz polecenie **Właściwości**.
2. Obok pozycji **Uwierzytelnianie wieloskładnikowe**wybierz pozycję **Włączone**.
3. Kliknij przycisk **Zapisz** w górnej części strony.


