---
title: Zabezpieczanie zasobów w chmurze za pomocą usługi Azure MFA i usług AD FS | Microsoft Docs
description: Ta strona dotyczy usługi Azure Multi-Factor Authentication i zawiera informacje umożliwiające rozpoczęcie korzystania z usługi Azure MFA i usług AD FS w chmurze.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 868af1903623f612a7a987c1247bd138bcf26002
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430538"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Zabezpieczanie zasobów w chmurze przy użyciu usługi Azure Multi-Factor Authentication i usług AD FS

Jeśli Twoja organizacja jest sfederowana z użyciem usługi Azure Active Directory, możesz użyć usługi Azure Multi-Factor Authentication lub usług Active Directory Federation Services (AD FS) do zabezpieczenia zasobów używanych przez usługę Azure AD. Aby zabezpieczyć zasoby usługi Azure Active Directory za pomocą usługi Azure Multi-Factor Authentication lub usług Active Directory Federation Services, postępuj zgodnie z poniższymi procedurami.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Zabezpieczanie zasobów usługi Azure AD za pomocą usług AD FS

Aby zabezpieczyć zasób w chmurze, skonfiguruj regułę oświadczeń, tak aby usługi Active Directory Federation Services emitowały oświadczenie multipleauthn, gdy użytkownik pomyślnie przeprowadzi weryfikację dwuetapową. To oświadczenie jest przekazywane do usługi Azure AD. Wykonaj tę procedurę w celu przejścia przez poszczególne kroki:

1. Otwórz przystawkę zarządzania usługami AD FS.
2. Po lewej stronie wybierz pozycję **Relacje zaufania jednostek zależnych**.
3. Kliknij prawym przyciskiem myszy pozycję **Platforma tożsamości usługi Microsoft Office 365** i wybierz pozycję **Edytuj reguły oświadczeń**.

   ![Chmura](./media/howto-mfa-adfs/trustedip1.png)

4. Na karcie Reguły przekształcania wystawiania kliknij pozycję **Dodaj regułę**.

   ![Chmura](./media/howto-mfa-adfs/trustedip2.png)

5. W Kreatorze dodawania reguły przekształcania oświadczeń wybierz z listy rozwijanej pozycję **Przekazywanie lub filtrowanie oświadczenia przychodzącego**, a następnie kliknij przycisk **Dalej**.

   ![Chmura](./media/howto-mfa-adfs/trustedip3.png)

6. Nadaj regule nazwę. 
7. Wybierz wartość **Odwołania metod uwierzytelniania** jako typ oświadczenia przychodzącego.
8. Wybierz pozycję **Przekazuj wszystkie wartości oświadczeń**.
    ![Kreator dodawania reguły przekształcania dotyczącej oświadczeń](./media/howto-mfa-adfs/configurewizard.png)
9. Kliknij przycisk **Zakończ**. Zamknij konsolę zarządzania usługami AD FS.

## <a name="trusted-ips-for-federated-users"></a>Zaufane adresy IP dla użytkowników federacyjnych

Zaufane adresy IP umożliwiają administratorom pomijanie weryfikacji dwuetapowej w przypadku określonych adresów IP lub użytkowników federacyjnych, którzy wysyłają żądania z firmowej sieci intranet. Poniższe sekcje zawierają instrukcje dotyczące konfigurowania zaufanych adresów IP usługi Azure Multi-Factor Authentication dla użytkowników federacyjnych i pomijania weryfikacji dwuetapowej w przypadku żądań pochodzących od użytkowników federacyjnych z sieci intranet. Osiąga się to przez skonfigurowanie usług AD FS pod kątem używania szablonu przekazywania lub szablonu filtrowania oświadczeń przychodzących za pomocą typu oświadczeń wewnętrznej sieci firmowej.

W tym przykładzie użyto usługi Office 365 w celu pokazania obsługi relacji zaufania jednostek zależnych.

### <a name="configure-the-ad-fs-claims-rules"></a>Konfigurowanie reguł oświadczeń usług AD FS

W pierwszej kolejności należy skonfigurować oświadczenia usług AD FS. Utwórz dwie reguły oświadczeń — jedną dla typu oświadczenia wewnętrznej sieci firmowej, a drugą na potrzeby umożliwienia stałego zalogowania użytkowników.

1. Otwórz przystawkę zarządzania usługami AD FS.
2. Po lewej stronie wybierz pozycję **Relacje zaufania jednostek zależnych**.
3. Kliknij prawym przyciskiem myszy pozycję **Platforma tożsamości usługi Microsoft Office 365** i wybierz pozycję **Edytuj reguły oświadczeń...**
   ![Chmura](./media/howto-mfa-adfs/trustedip1.png)
4. Na karcie Reguły przekształcania wystawiania kliknij pozycję **Dodaj regułę.**
   ![Chmura](./media/howto-mfa-adfs/trustedip2.png)
5. W Kreatorze dodawania reguły przekształcania oświadczeń wybierz z listy rozwijanej pozycję **Przekazywanie lub filtrowanie oświadczenia przychodzącego**, a następnie kliknij przycisk **Dalej**.
   ![Chmura](./media/howto-mfa-adfs/trustedip3.png)
6. W polu Nazwa reguły oświadczenia wpisz nazwę reguły, Na przykład: InsideCorpNet.
7. Dla pola Typ oświadczenia przychodzącego wybierz z listy rozwijanej pozycję **Wewnątrz sieci firmowej**.
   ![Chmura](./media/howto-mfa-adfs/trustedip4.png)
8. Kliknij przycisk **Zakończ**.
9. Na karcie Reguły przekształcania wystawiania kliknij pozycję **Dodaj regułę**.
10. W Kreatorze dodawania reguły przekształcania oświadczeń wybierz z listy rozwijanej pozycję **Wysyłanie oświadczeń przy użyciu reguły niestandardowej**, a następnie kliknij przycisk **Dalej**.
11. W polu Nazwa reguły oświadczenia wprowadź tekst *Nie wylogowuj użytkowników*.
12. W polu Reguła niestandardowa wprowadź kod:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Chmura](./media/howto-mfa-adfs/trustedip5.png)
13. Kliknij przycisk **Zakończ**.
14. Kliknij przycisk **Zastosuj**.
15. Kliknij przycisk **OK**.
16. Zamknij przystawkę zarządzania usługami AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Konfigurowanie zaufanych adresów IP usługi Azure Multi-Factor Authentication dla użytkowników federacyjnych

Po skonfigurowaniu oświadczeń można przystąpić do konfigurowania zaufanych adresów IP.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Azure Active Directory** > **Dostęp warunkowy** > **Lokalizacje z nazwą**.
3. W bloku **Dostęp warunkowy — lokalizacje z nazwą** wybierz opcję **Konfiguruj zaufane adresy IP usługi MFA**

   ![Dostęp warunkowy — lokalizacje z nazwą usługi Azure AD: opcja Konfiguruj zaufane adresy IP usługi MFA](./media/howto-mfa-adfs/trustedip6.png)

4. Na stronie Ustawienia usługi w obszarze **zaufanych adresów IP** wybierz pozycję **Pomiń uwierzytelnianie wieloskładnikowe w przypadku żądań od użytkowników federacyjnych w moim intranecie**.  
5. Kliknij pozycję **Zapisz**.

Gotowe. Od tej pory federacyjni użytkownicy usługi Office 365 muszą używać usługi MFA, tylko jeśli ich oświadczenia pochodzą spoza firmowego intranetu.
