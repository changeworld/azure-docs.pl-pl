---
title: "Uwierzytelnianie tożsamości bez hasła przy użyciu usługi Windows Hello dla firm i Azure AD | Dokumentacja firmy Microsoft"
description: "Zawiera omówienie usługi Windows Hello dla firm i dodatkowe informacje na temat wdrażania usługi Windows Hello dla firm."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 22534cc5f7f2de235bc1f1212c63ea227083c5a4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Uwierzytelnianie tożsamości bez hasła przy użyciu usługi Windows Hello dla firm
Bieżącej metody uwierzytelniania przy użyciu haseł wyłącznie nie są wystarczające, aby zapewnić bezpieczeństwo użytkowników. Użytkownicy ponownego wykorzystania i zapomnisz hasła. Hasła są breachable, phishable, podatne na rys i odgadnięcia. One również uzyskać trudne do zapamiętania i podatność na ataki, takie jak "[przekazać skrót](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-windows-hello-for-business"></a>Temat Windows Hello dla firm
Usługi Windows Hello dla firm jest klucza publicznego i prywatnego lub metody uwierzytelniania opartego na certyfikatach dla organizacji i konsumentów wykracza poza hasła. Ta forma uwierzytelniania opiera się na poświadczenia parę kluczy, można zastąpić hasła, które są odporne na naruszenia, thefts i wyłudzaniem informacji.

 Usługi Windows Hello dla firm pozwala uwierzytelniać się na konto Microsoft, konto usługi Active Directory systemu Windows Server, konto Microsoft Azure Active Directory (Azure AD) lub usługa firmy Microsoft, która obsługuje uwierzytelnianie Fast tożsamości Online (FIDO). Po weryfikacji dwuetapowej początkowej podczas Windows Hello dla firm rejestracji usługi Windows Hello dla firm jest skonfigurowany na urządzeniu użytkownika, i gestów, który może być Windows Hello lub numeru PIN ustawiony przez użytkownika. Użytkownik udostępnia gestu weryfikacji tożsamości. Windows następnie używa usługi Windows Hello dla firm do uwierzytelnienia użytkownika i pomóc im dostęp do chronionych zasobów i usług.

Klucz prywatny jest udostępniane wyłącznie za pomocą "gestu użytkownika" jak numer PIN, biometrycznych lub urządzenie zdalne, takie jak karty inteligentnej, których użytkownik używa do logowania się na urządzeniu. Te informacje jest połączony z certyfikatu lub parę kluczy asymetrycznych. Klucz prywatny jest sprzętu zaświadczenia, jeśli urządzenie ma układ modułu Trusted Platform Module (TPM). Klucz prywatny nigdy nie opuszcza urządzenia.

Klucz publiczny jest zarejestrowany w usłudze Azure Active Directory i usługi Active Directory systemu Windows Server (w przypadku lokalnego). Dostawcy tożsamości (IDPs) sprawdza, czy użytkownik przez mapowanie klucz publiczny użytkownika do klucza prywatnego i podaj informacje logowania za pośrednictwem jednego czasu hasła (OTP), PhoneFactor lub mechanizm powiadamiania inną.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>Dlaczego przedsiębiorstwa powinna przyjąć Windows Hello dla firm
Przez włączenie usługi Windows Hello dla firm, przedsiębiorstw ułatwia ich zasobów jeszcze bardziej bezpieczne przez:

* Konfigurowanie usługi Windows Hello dla firm za pomocą opcji preferowane sprzętu. Oznacza to, że klucze będą generowane w moduł TPM 1.2 lub 2.0 modułu TPM, jeśli jest dostępna. W przypadku niedostępności modułu TPM, oprogramowania wygeneruje klucz.
* Definiowanie złożoność i długości numeru PIN, i czy użycia Hello jest włączone w Twojej organizacji.
* Konfigurowanie usługi Windows Hello dla firm do obsługi scenariuszy, jak karty inteligentnej za pomocą opartego na certyfikatach zaufania.

## <a name="how-windows-hello-for-business-works"></a>Jak Windows Hello dla firm działa
1. Klucze są generowane na sprzęcie przez moduł TPM i oprogramowania. Wiele urządzeń ma wbudowany moduł TPM, która zabezpiecza sprzętu przez integrację klucze szyfrowania z urządzeń. Moduł TPM 1.2 lub 2.0 generuje klucze i certyfikaty, które są tworzone na podstawie wygenerowane klucze.
2. Moduł TPM poświadcza te klucze związanych ze sprzętem.
3. Gest unlock pojedynczego odblokowuje urządzenie. Ten gest zezwala na dostęp do wielu zasobów, jeśli urządzenie jest przyłączony do domeny lub Azure przyłączonych do usługi AD.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Jak Windows Hello dla firm cyklu życia działa
![Usługi Windows Hello dla firm cykl życia](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Poprzedni diagram przedstawia pary klucza publicznego i prywatnego i weryfikacja przez dostawcę tożsamości. Każda z tych czynności jest szczegółowo opisany w tym miejscu:

1. Użytkownik potwierdzające jego tożsamość przez wiele wbudowanych metod sprawdzania pisowni (gesty, fizycznymi kartami inteligentnymi, usługa Multi-Factor authentication) i wysyła te informacje do tożsamości dostawcy (IDP) jak usługa Azure Active Directory lub lokalnej usługi Active Directory.
2. Urządzenie tworzy klucz, poświadcza klucz, przyjmuje publicznej części klucza, dołącza go z instrukcji stacji, loguje i wysyła go do IDP do klucza rejestru.
3. Jak najszybciej po rozszerzeniu IDP rejestruje publicznej części klucza, IDP żąda urządzenie, aby podpisać część klucza prywatnego.
4. Następnie IDP weryfikuje i wystawia token uwierzytelniania, umożliwiający dostęp do urządzeń i użytkownika chronionych zasobów. IDPs można zapisać wieloplatformowych aplikacji lub obsługi w przeglądarce (za pośrednictwem interfejsów API języka JavaScript/Webcrypto) umożliwia tworzenie i używanie usługi Windows Hello dla firm poświadczenia dla użytkowników.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Wymagania dotyczące wdrażania dla usługi Windows Hello dla firm
### <a name="at-the-enterprise-level"></a>Na poziomie przedsiębiorstwa
* Przedsiębiorstwo ma subskrypcji platformy Azure.

### <a name="at-the-user-level"></a>Na poziomie użytkownika
* Na komputerze użytkownika działa system Windows 10 Professional lub Enterprise.

Aby uzyskać szczegółowe instrukcje, zobacz [włączenia usługi Windows Hello dla firm w organizacji](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Dodatkowe informacje
* [System Windows 10 dla przedsiębiorstw: sposoby używania urządzenia do pracy](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozszerzanie możliwości chmury dla urządzeń z systemem Windows 10 za pomocą usługi Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Dowiedz się więcej na temat scenariuszy użycia funkcji Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Łączenie urządzeń przyłączonych do domeny z usługą Azure AD w celu korzystania z możliwości systemu Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurowanie funkcji Azure AD Join](active-directory-azureadjoin-setup.md)

