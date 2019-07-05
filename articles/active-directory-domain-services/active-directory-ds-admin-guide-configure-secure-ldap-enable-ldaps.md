---
title: Włączanie protokołu Secure LDAP (LDAPS) w usługach domenowych Azure AD | Dokumentacja firmy Microsoft
description: Włączanie protokołu Secure LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: iainfou
ms.openlocfilehash: 631689d28399c07330c7f57b08952961973d1a92
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474324"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Włączanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD

## <a name="before-you-begin"></a>Przed rozpoczęciem
Pełne [zadanie 2 — certyfikat secure LDAP do eksportowania. Plik PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Zadanie 3. Włączanie protokołu secure LDAP dla domeny zarządzanej przy użyciu witryny Azure portal
Aby włączyć protokół secure LDAP, wykonaj następujące kroki konfiguracji:

1. Przejdź do  **[witryny Azure portal](https://portal.azure.com)** .

2. Wyszukaj termin "domain services" w **Wyszukaj zasoby** pola wyszukiwania. Wybierz **usług domenowych Azure AD** z wyników wyszukiwania. **Usług domenowych Azure AD** strona zawiera listę Twojej domeny zarządzanej.

    ![Znajdź trwa aprowizowanie domeny zarządzanej](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Kliknij nazwę domeny zarządzanej (na przykład "contoso100.com"), aby zobaczyć więcej szczegółów dotyczących domeny.

    ![Usługi domenowe — stan aprowizacji](./media/getting-started/domain-services-provisioning-state.png)

3. Kliknij przycisk **Secure LDAP** w okienku nawigacji.

    ![Usługi domenowe — strona protokołu Secure LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Domyślnie dostęp do Twojej domeny zarządzanej secure LDAP jest wyłączona. Przełącz **bezpieczny protokół LDAP** do **Włącz**.

    ![Włączanie protokołu secure LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Domyślnie dostęp do Twojej domeny zarządzanej za pośrednictwem Internetu secure LDAP jest wyłączona. Przełącz **Zezwalaj na dostęp protokołu secure LDAP przez internet** do **Włącz**, jeśli potrzebujesz.

    > [!WARNING]
    > Jeśli włączysz dostęp protokołu secure LDAP przez internet, Twoja domena to podatny na ataki siłowe hasła za pośrednictwem Internetu. Dlatego zaleca się skonfigurowanie sieciowej grupy zabezpieczeń do blokowania dostępu do zakresów adresów IP wymagana źródła. Zapoznaj się z instrukcjami, aby [blokowanie dostępu protokołu LDAPS do domeny zarządzanej za pośrednictwem Internetu](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Kliknij ikonę następujący folder **. Plik PFX z certyfikatem secure LDAP**. Określ ścieżkę do pliku PFX z certyfikatem, aby uzyskać dostęp do domeny zarządzanej secure LDAP.

7. Określ **hasła do odszyfrowywania. Plik PFX**. Podaj hasło użyte podczas eksportowania certyfikatu do pliku PFX.

8. Gdy wszystko będzie gotowe, kliknij przycisk **Zapisz** przycisku.

9. Zobaczysz, że trwa Konfiguracja powiadomienie, które informuje, że secure LDAP dla domeny zarządzanej. Do czasu ukończenia tej operacji nie można modyfikować inne ustawienia dla domeny.

    ![Konfigurowanie protokołu secure LDAP dla domeny zarządzanej](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Włączanie protokołu secure LDAP dla domeny zarządzanej trwa około 10 – 15 minut. Podany certyfikat secure LDAP niezgodna wymagane kryteria nie włączono protokół secure LDAP dla katalogu i zostanie wyświetlony błąd. Na przykład nazwa domeny jest nieprawidłowa, certyfikat już wygasł lub wkrótce wygaśnie. W takim przypadku spróbuj ponownie z prawidłowym certyfikatem.
>
>

## <a name="next-step"></a>Następny krok
[Task 4: configure DNS to access the managed domain from the internet (Zadanie 4. Konfigurowanie systemu DNS do uzyskiwania dostępu do domeny zarządzanej z Internetu)](active-directory-ds-ldaps-configure-dns.md)
