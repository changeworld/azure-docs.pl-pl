---
title: "Włącz program Microsoft Windows Hello dla firm w Twojej organizacji | Dokumentacja firmy Microsoft"
description: "Instrukcje dotyczące wdrażania do włączenia Microsoft Passport w Twojej organizacji."
services: active-directory
documentationcenter: 
keywords: "Skonfiguruj Microsoft Passport, Microsoft Windows Hello dla firm wdrożenia"
author: MarkusVi
manager: mtillman
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 0aa16e3466b36b6d1d83308cf37623aa15d61fcb
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2018
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Włącz program Microsoft Windows Hello dla firm w Twojej organizacji
Po [łączenie urządzeń przyłączonych do domeny systemu Windows 10 w usłudze Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), wykonaj następujące czynności, aby włączyć program Microsoft Windows Hello dla firm w Twojej organizacji:

1. Wdrażanie programu System Center Configuration Manager  
2. Skonfiguruj ustawienia zasad
3. Konfigurowanie profilu certyfikatu  

## <a name="deploy-system-center-configuration-manager"></a>Wdrażanie programu System Center Configuration Manager
Aby wdrożyć certyfikaty użytkowników oparte na Windows Hello dla firm kluczy, potrzebne są następujące elementy:

* **System Center Configuration Manager Current Branch** — należy zainstalować wersję 1606 lub większą. Aby uzyskać więcej informacji, zobacz [dokumentacji programu System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) i [blogu zespołu programu System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Infrastruktury kluczy publicznych (PKI)** — Aby włączyć program Microsoft Windows Hello dla firm przy użyciu certyfikatów użytkowników wymaga infrastruktury kluczy publicznych w miejscu. Jeśli nie istnieje lub nie chcesz jej używać w przypadku certyfikatów użytkownika, można wdrożyć nowego kontrolera domeny systemu Windows Server 2016 kompilacji 10551 (lub lepszy) zainstalowany. Wykonaj kroki, aby [Instalowanie repliki kontrolera domeny w istniejącej domenie](https://technet.microsoft.com/library/jj574134.aspx) lub [zainstalować nowy las usługi Active Directory, tworząc nowe środowisko](https://technet.microsoft.com/library/jj574166). (Obrazów ISO są dostępne do pobrania na [wymiana nośników Signiant](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Skonfiguruj ustawienia zasad
Aby skonfigurować program Microsoft Windows Hello dla firm ustawień zasad, masz dwie opcje:

* Zasady grupy w usłudze Active Directory 
* System Center Configuration Manager 

W oknie Zasady grupy w usłudze Active Directory jest to zalecana metoda skonfigurować program Microsoft Windows Hello dla firm ustawień zasad. 

Za pomocą programu System Center Configuration Manager jest preferowaną metodą, gdy również używać do wdrażania certyfikatów. Ten scenariusz.

* Zapewnia zgodność z nowszej scenariusze wdrażania
* Wymaga po stronie klienta systemu Windows 10 w wersji 1607 lub lepszej.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Konfigurowanie programu Microsoft Windows Hello dla firm za pomocą zasad grupy w usłudze Active Directory
**Kroki**:

1. Otwórz Menedżera serwera, a następnie przejdź do **narzędzia** > **Zarządzanie zasadami grupy**.
2. W przystawce Zarządzanie zasadami grupy przejdź do węzeł domeny, która odnosi się do domeny, w której chcesz włączyć usługi Azure AD Join.
3. Kliknij prawym przyciskiem myszy **obiektów zasad grupy**i wybierz **nowy**. Nadaj nazwę, na przykład włączyć usługi Windows Hello dla firm z obiektu zasad grupy. Kliknij przycisk **OK**.
4. Kliknij prawym przyciskiem myszy nowy obiekt zasad grupy, a następnie wybierz **Edytuj**.
5. Przejdź do **Konfiguracja komputera** > **zasady** > **Szablony administracyjne** > **systemu Windows Składniki** > **Windows Hello dla firm**.
6. Kliknij prawym przyciskiem myszy **włączenia usługi Windows Hello dla firm**, a następnie wybierz **Edytuj**.
7. Wybierz **włączone** przycisk opcji, a następnie kliknij przycisk **Zastosuj**. Kliknij przycisk **OK**.
8. Teraz możesz połączyć obiekt zasad grupy do wybranej lokalizacji. Aby włączyć te zasady dla wszystkich urządzeń z systemem Windows 10 przyłączonych do domeny w swojej organizacji, należy połączyć zasad grupy do domeny. Na przykład:
   * Określonej jednostki organizacyjnej (OU) usługi Active Directory, gdzie będą umieszczane komputery przyłączone do domeny systemu Windows 10
   * Grupy zabezpieczeń, która zawiera przyłączonych do domeny komputerów z systemem Windows 10, które będą automatycznie rejestrowane w usłudze Azure AD

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Konfigurowanie usługi Windows Hello dla firm przy użyciu programu System Center Configuration Manager
**Kroki**:

1. Otwórz **System Center Configuration Manager**, a następnie przejdź do **zasoby i zgodność > Ustawienia zgodności > dostęp do zasobów firmy > Windows Hello dla firm profile**.
   
    ![Konfigurowanie systemu Windows Hello dla firm](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. Na pasku narzędzi u góry kliknij **tworzenia usługi Windows Hello dla firm profilu**.
   
    ![Konfigurowanie systemu Windows Hello dla firm](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. Na **ogólne** okna dialogowego, wykonaj następujące czynności:
   
    ![Konfigurowanie systemu Windows Hello dla firm](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. W **nazwa** tekstowym, wpisz nazwę profilu, na przykład **Mój profil WHfB**.
   
    b. Kliknij przycisk **Dalej**.
4. Na **obsługiwane platformy** okno dialogowe, wybierz platformy, które będą udostępniane z tym usługi Windows Hello dla firm, a następnie kliknij przycisk **dalej**.
   
    ![Konfigurowanie systemu Windows Hello dla firm](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. Na **ustawienia** okna dialogowego, wykonaj następujące czynności:
   
    ![Konfigurowanie systemu Windows Hello dla firm](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Jako **konfigurowania usługi Windows Hello dla firm**, wybierz pozycję **włączone**.
   
    b. Jako **używaj (Trusted Platform Module)**, wybierz pozycję **wymagane**. 
   
    d. Jako **metodę uwierzytelniania**, wybierz pozycję **opartego na certyfikatach**.
   
    d. Kliknij przycisk **Dalej**.
6. Na **Podsumowanie** okna dialogowego, kliknij przycisk **dalej**.
7. Na **zakończenia** okna dialogowego, kliknij przycisk **Zamknij**.
8. Na pasku narzędzi u góry kliknij **Wdróż**.
   
    ![Konfigurowanie systemu Windows Hello dla firm](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Konfigurowanie profilu certyfikatu
Jeśli używasz uwierzytelniania opartego na certyfikatach dla uwierzytelniania lokalnego, należy skonfigurować i wdrożyć profil certyfikatu. To zadanie wymaga skonfigurowania serwera usługi NDES i roli lokacji punktu rejestracji certyfikatu w System Center Configuration Manager. Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące profilów certyfikatów w programie Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Otwórz **System Center Configuration Manager**, a następnie przejdź do **zasoby i zgodność > Ustawienia zgodności > dostęp do zasobów firmy > Profile certyfikatów**.
2. Wybierz szablon, który zawiera karty inteligentnej logowania rozszerzonego użycia klucza (EKU).

Na **rejestracja SCEP** strony profilu certyfikatu, musisz wybrać **Zainstaluj w usłudze Passport for Work w przeciwnym razie Zgłoś błąd** jako **dostawcy magazynu kluczy**.

## <a name="next-steps"></a>Kolejne kroki
* [System Windows 10 dla przedsiębiorstw: sposoby używania urządzenia do pracy](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozszerzanie możliwości chmury dla urządzeń z systemem Windows 10 za pomocą usługi Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Uwierzytelnianie bez hasła](active-directory-azureadjoin-passport.md)
* [Dowiedz się więcej na temat scenariuszy użycia funkcji Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Łączenie urządzeń przyłączonych do domeny z usługą Azure AD w celu korzystania z możliwości systemu Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurowanie funkcji Azure AD Join](active-directory-azureadjoin-setup.md)

