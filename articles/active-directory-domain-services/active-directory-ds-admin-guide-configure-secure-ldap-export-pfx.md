---
title: Skonfigurować bezpiecznego protokołu LDAP (LDAPS) w usługach domenowych Azure AD | Dokumentacja firmy Microsoft
description: Skonfiguruj zabezpieczenia protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: d2c7bd8b335ce49bed8e39812cccbe7ab474bf8f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211530"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD

## <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, przeprowadzisz [zadanie 1 — Uzyskaj certyfikat dla bezpiecznego protokołu LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Zadanie 2 — wyeksportowany certyfikat bezpiecznego LDAP. Plik PFX
Przed rozpoczęciem tego zadania upewnij się, czy bezpiecznego certyfikat LDAP został uzyskany z publicznego urzędu certyfikacji lub utworzono certyfikatu z podpisem własnym.

Wykonaj poniższe kroki, aby wyeksportować certyfikat LDAPS do. Plik PFX.

1. Naciśnij klawisz **Start** przycisk i typ **R**. W **Uruchom** okno dialogowe, typ **mmc** i kliknij przycisk **OK**.

    ![Uruchamianie konsoli MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Na **Kontrola konta użytkownika** monit, kliknij przycisk **tak** na uruchamianie konsoli MMC (Microsoft Management Console) jako administrator.
3. Z **pliku** menu, kliknij przycisk **Dodaj/Usuń przystawkę...** .

    ![Dodawanie przystawki do konsoli MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. W **Dodawanie lub usuwanie przystawek** okno dialogowe, wybierz opcję **certyfikaty** przystawki, a następnie kliknij przycisk **Dodaj >** przycisku.

    ![Dodawanie przystawki Certyfikaty do konsoli MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. W **certyfikatów przystawki** kreatora wybierz **konto komputera** i kliknij przycisk **dalej**.

    ![Dodaj przystawkę Certyfikaty dla konta komputera](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Na **Wybieranie komputera** wybierz **komputer lokalny: (komputer ten jest uruchomiona konsola)** i kliknij przycisk **Zakończ**.

    ![Dodaj przystawkę Certyfikaty — komputer select](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. W **Dodawanie lub usuwanie przystawek** okna dialogowego, kliknij przycisk **OK** Aby dodać przystawkę Certyfikaty do programu MMC.

    ![Dodawanie przystawki Certyfikaty do programu MMC — Zakończono](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. W oknie konsoli MMC kliknij, aby rozwinąć **katalog główny konsoli**. Powinna zostać wyświetlona przystawki Certyfikaty załadowane. Kliknij przycisk **certyfikaty (komputer lokalny)** aby rozwinąć. Kliknij, aby rozwinąć **osobistych** węzeł, a następnie **certyfikaty** węzła.

    ![Otwórz osobistym magazynie certyfikatów](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Certyfikat z podpisem własnym utworzonego powinna zostać wyświetlona. Można zbadać właściwości certyfikatu, aby upewnić się, że odcisk palca jest zgodna z wersją zgłoszone w systemie windows PowerShell, podczas tworzenia certyfikatu.
10. Wybierz certyfikat z podpisem własnym i **kliknij prawym przyciskiem myszy**. Wybierz z menu kliknij prawym przyciskiem myszy **wszystkie zadania** i wybierz **Eksportuj...** .

    ![Eksportowanie certyfikatu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. W **Kreatora eksportu certyfikatów**, kliknij przycisk **dalej**.

    ![Kreator eksportu certyfikatu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Na **eksportowanie klucza prywatnego** wybierz pozycję **tak, Eksportuj klucz prywatny**i kliknij przycisk **dalej**.

    ![Eksportowanie klucza prywatnego certyfikatu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Należy wyeksportować klucz prywatny wraz z certyfikatu. Jeśli podasz PFX, który nie zawiera klucza prywatnego dla certyfikatu, włączanie bezpiecznego protokołu LDAP do domeny zarządzanej nie powiedzie się.
    >
    >
13. Na **Format pliku eksportu** wybierz pozycję **wymiana informacji osobistych — PKCS #12 (. PFX)** jako format pliku dla wyeksportowanego certyfikatu.

    ![Format pliku eksportu certyfikatu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Tylko. Format pliku PFX jest obsługiwany. Nie Eksportuj certyfikat. Format pliku CER.
    >
    >
14. Na **zabezpieczeń** wybierz pozycję **hasło** opcję i wpisz hasło, aby chronić. Plik PFX. Zapamiętaj to hasło, ponieważ jest ono potrzebne w następnego zadania. Kliknij przycisk **dalej** aby kontynuować.

    ![Hasło do eksportowania certyfikatów ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Zanotuj to hasło. Należy podczas włączania bezpiecznego protokołu LDAP dla tej domeny zarządzanej w [zadanie 3 — Włączanie bezpiecznego protokołu LDAP do domeny zarządzanej](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >
15. Na **Eksport pliku** Określ nazwę pliku i lokalizację, w której chcesz wyeksportować certyfikat.

    ![Ścieżka do eksportowania certyfikatów](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Na następnej stronie kliknij **Zakończ** można wyeksportować certyfikat do pliku PFX. Jeśli certyfikat został wyeksportowany powinno zostać wyświetlone okno dialogowe potwierdzenia.

    ![Wyeksportuj certyfikat gotowe](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Następny krok
[Zadanie 3 — Włączanie bezpiecznego protokołu LDAP do domeny zarządzanej](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
