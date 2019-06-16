---
title: Tworzenie. Plik PFX przy użyciu bezpiecznego certyfikatu LDAP (LDAPS) dla domeny usług domenowych Azure AD
description: Utwórz certyfikat secure LDAP dla usługi Azure AD Domain Services Zarządzaj domeną
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: b79418c6e64727e4fdf13b3f4f267d84fcf5da85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235023"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Tworzenie. Plik PFX przy użyciu bezpiecznego certyfikatu LDAP (LDAPS) dla domeny zarządzanej

## <a name="before-you-begin"></a>Przed rozpoczęciem

Pełne [zadanie 1: uzyskiwanie certyfikatu dla protokołu secure LDAP](configure-ldaps.md).

## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>Zadanie 2. Certyfikat secure LDAP do eksportowania. Plik PFX

Przed rozpoczęciem tego zadania należy uzyskać certyfikat secure LDAP z publicznego urzędu certyfikacji, lub Utwórz certyfikat z podpisem własnym.

Aby wyeksportować certyfikat protokołu LDAPS. Plik PFX:

1. Naciśnij klawisz **Start** przycisk i typ **R**. W **Uruchom** okno dialogowe, typ **mmc** i kliknij przycisk **OK**.

    ![Uruchom konsolę MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Na **Kontrola konta użytkownika** monit, kliknij przycisk **tak** na uruchamianie konsoli MMC (Microsoft Management Console) jako administrator.
3. Z **pliku** menu, kliknij przycisk **Dodaj/Usuń przystawkę...** .

    ![Dodawanie przystawki do konsoli MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. W **Dodawanie lub usuwanie przystawek** okno dialogowe, wybierz opcję **certyfikaty** przystawki, a następnie kliknij przycisk **Dodaj >** przycisku.

    ![Dodawanie przystawki Certyfikaty do konsoli MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. W **certyfikatów w przystawce** kreatora wybierz **konto komputera** i kliknij przycisk **dalej**.

    ![Dodaj przystawkę Certyfikaty dla konta komputera](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Na **Wybieranie komputera** wybierz **komputer lokalny: (komputer jest na uruchomiona ta konsola)** i kliknij przycisk **Zakończ**.

    ![Dodaj przystawkę Certyfikaty — wybierz komputer](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. W **Dodawanie lub usuwanie przystawek** okno dialogowe, kliknij przycisk **OK** do dodawania przystawki Certyfikaty w programie MMC.

    ![Dodaj przystawkę Certyfikaty do konsoli MMC — gotowe](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. W oknie konsoli MMC kliknij, aby rozwinąć **katalog główny konsoli**. Powinieneś zobaczyć przystawki Certyfikaty załadowane. Kliknij przycisk **certyfikaty (komputer lokalny)** rozszerzenia. Kliknij, aby rozwinąć **osobistych** węzła, a następnie **certyfikaty** węzła.

    ![Otwórz osobistym magazynie certyfikatów](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Powinien zostać wyświetlony utworzony certyfikat z podpisem własnym. Można zbadać właściwości certyfikatu, aby sprawdzić, czy odcisk palca jest zgodny z typem zgłoszone w systemie windows PowerShell, podczas tworzenia certyfikatu.
10. Wybierz certyfikat z podpisem własnym i **kliknij prawym przyciskiem myszy**. W menu kliknij prawym przyciskiem myszy, wybierz opcję **wszystkie zadania** i wybierz **eksportu...** .

    ![Eksportowanie certyfikatu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. W **Kreatora eksportu certyfikatów**, kliknij przycisk **dalej**.

    ![Certyfikat Kreator eksportu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Na **eksportowanie klucza prywatnego** wybierz opcję **tak, Eksportuj klucz prywatny**i kliknij przycisk **dalej**.

    ![Eksportuj klucz prywatny certyfikatu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > NALEŻY wyeksportować klucz prywatny, wraz z certyfikatu. Jeśli podasz PFX, który nie zawiera klucza prywatnego dla certyfikatu, włączanie protokołu secure LDAP dla domeny zarządzanej nie powiedzie się.
    >
    >

13. Na **Format pliku eksportu** wybierz opcję **wymiana informacji osobistych — PKCS #12 (. Plik PFX)** jako format pliku dla wyeksportowanego certyfikatu.

    ![Certyfikat format pliku eksportu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Tylko. Format pliku PFX jest obsługiwany. Nie Eksportuj certyfikat. Format pliku CER.
    >
    >

14. Na **zabezpieczeń** wybierz opcję **hasło** opcji i wpisz hasło, aby chronić. Plik PFX. Zapamiętaj to hasło, ponieważ będą potrzebne w ramach następnego zadania. Kliknij przycisk **Dalej**.

    ![Hasło do eksportowania certyfikatów](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Zanotuj hasło. Będą potrzebne podczas włączania protokołu secure LDAP dla tej domeny zarządzanej w [zadanie 3 — Włączanie protokołu secure LDAP dla domeny zarządzanej](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >

15. Na **Eksport pliku** Określ nazwę pliku i lokalizację, w którym chcesz wyeksportować certyfikat.

    ![Ścieżka do eksportowania certyfikatów](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Na następnej stronie kliknij pozycję **Zakończ** Aby wyeksportować certyfikat do pliku PFX. Okno dialogowe potwierdzenia powinien zostać wyświetlony, gdy został wyeksportowany certyfikat.

    ![Wyeksportuj certyfikat gotowe](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)

## <a name="next-step"></a>Następny krok

[Zadanie 3: Włączanie protokołu secure LDAP dla domeny zarządzanej](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
