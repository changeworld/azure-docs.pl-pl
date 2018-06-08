---
title: Publikowanie aplikacji sieci Web do maszyny Wirtualnej platformy Azure w programie Visual Studio
description: Publikowanie aplikacji sieci Web ASP.NET do maszyny wirtualnej platformy Azure w programie Visual Studio
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: aspnet
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d9e2c1a941c25aedd9048f2784350106b8739dba
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "31797198"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikowanie aplikacji sieci Web platformy ASP.NET dla maszyny Wirtualnej platformy Azure w programie Visual Studio

W tym dokumencie opisano sposób publikowania aplikacji sieci web ASP.NET przy użyciu maszyny wirtualnej platformy Azure (VM) **maszyny wirtualne Microsoft Azure** funkcja publikowania w programie Visual Studio 2017 r.  

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było używać programu Visual Studio do publikowania projektu programu ASP.NET w maszynie Wirtualnej platformy Azure, maszyny Wirtualnej musi być prawidłowo ustawiony.

- Komputer musi być skonfigurowany do uruchamiania aplikacji sieci web ASP.NET i ma zainstalowany pakiet WebDeploy.

- Maszyna wirtualna musi mieć skonfigurowaną nazwę DNS. Aby uzyskać więcej informacji, zobacz [utworzenia w pełni kwalifikowaną nazwą domeny w portalu Azure dla maszyny Wirtualnej systemu Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikowanie aplikacji sieci web ASP.NET do maszyny Wirtualnej platformy Azure przy użyciu programu Visual Studio
W poniższej sekcji opisano sposób publikowania istniejącą aplikację sieci web platformy ASP.NET do maszyny wirtualnej platformy Azure.

1. Otwórz rozwiązanie aplikacji sieci web w programie Visual Studio 2017 r.
2. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie **publikowania...**
3. Użyj strzałki po prawej stronie, aby przewijać opcje publikowania do momentu znalezienia **maszyny wirtualne Microsoft Azure**.  

   ![Publikowanie strony - strzałki w prawo]

4. Wybierz **maszyny wirtualne Microsoft Azure** ikony, jak i wybierz **publikowania**.

   ![Publikowanie strony — ikona maszyny wirtualnej platformy Microsoft Azure]

5. Wybierz odpowiednie konto (z subskrypcją platformy Azure, połączony z maszyną wirtualną).  
   - Jeśli użytkownik jest zarejestrowany programie Visual Studio, z listy kont jest wypełniana uwierzytelnionego konta.  
   - Jeśli nie jest zalogowany lub nie ma konta należy, wybierz polecenie "Dodaj konto..." i postępuj zgodnie z monitami, aby się zalogować.  
   ![Selektor konto platformy Azure]  

6. Wybierz odpowiednią maszynę Wirtualną z listy z istniejących maszyn wirtualnych.

   > [!Note]
   > Wypełnianie tej listy może zająć trochę czasu.

   ![Selektor maszyna wirtualna platformy Azure]

7. Kliknij przycisk OK, aby rozpocząć publikowanie.

8. Po wyświetleniu monitu o poświadczenia, podaj nazwę użytkownika i hasło konta użytkownika na docelowej maszynie Wirtualnej, która jest skonfigurowana z publikowaniem praw (zazwyczaj nazwa użytkownika i hasło używane podczas tworzenia maszyny Wirtualnej).  

   ![Pakiet WebDeploy logowania]

9. Zaakceptować certyfikat zabezpieczeń.

   ![Błąd certyfikatu]

10. Obejrzyj w oknie danych wyjściowych, aby sprawdzić postęp operacji publikowania.

    ![Okno danych wyjściowych]

11. Jeśli publikowanie zakończy się pomyślnie, przeglądarce uruchamia otworzyć adres URL witryny nowo opublikowana.

**Powodzenie!**

Teraz został pomyślnie opublikowany aplikacji sieci web do maszyny wirtualnej platformy Azure.

## <a name="publish-page-options"></a>Strona opcji publikowania

Po zakończeniu pracy Kreatora publikowania, strony publikowania jest otwarty w dokumencie z wybrany nowy profil publikowania.

### <a name="re-publish"></a>Ponownie opublikować

Aby opublikować aplikację sieci web aktualizacji, zaznacz **publikowania** przycisk na stronie publikowania.  
- Jeśli zostanie wyświetlony monit, wprowadź nazwę użytkownika i hasło.  
- Publikowanie rozpocznie się natychmiast.

![Strona publikowania — przycisk Publikuj]

### <a name="modify-publish-profile-settings"></a>Zmodyfikuj ustawienia profilu publikowania

Wyświetlanie i modyfikowanie ustawień profilu publikowania, wybierz **ustawień...** .  

![Strona — przycisk Ustawienia publikowania]

Twoje ustawienia powinny wyglądać mniej więcej tak:  

![Publikowanie ustawienia — strona połączenia]

#### <a name="save-user-name-and-password"></a>Zapisz nazwę użytkownika i hasło
- Aby uniknąć, podając informacje o uwierzytelnianiu za każdym razem, gdy opublikujesz, można go wypełnić **nazwy użytkownika** i **hasło** pola i wybierz pozycję **Zapisz hasło** pole.
- Użyj **sprawdzania poprawności połączenia** przycisk, aby potwierdzić, że wprowadzono odpowiednie informacje.

#### <a name="deploy-to-clean-web-server"></a>Wdróż do usunięcia serwera sieci web

- Jeśli chcesz upewnij się, że serwer sieci web ma poprawną kopię aplikacji sieci web po każdej przekazywania (i pozostanie żadne inne pliki wiszące z poprzedniego wdrożenia), można sprawdzić **Usuń dodatkowe pliki w miejscu docelowym** pole wyboru w **ustawienia** kartę.

- Ostrzeżenie: Publikowanie za pomocą tego ustawienia usuwa wszystkie pliki, które istnieją na serwerze sieci web (katalogu wwwroot). Upewnij się, że znasz stan maszyny przed opublikowaniem włączeniu tej opcji. 

![Ustawienia — strona Ustawienia publikowania]

## <a name="next-steps"></a>Kolejne kroki

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Ustawienia elementu konfiguracji/CD dla automatycznego wdrażania do maszyny Wirtualnej Azure

Aby skonfigurować potoku ciągłego dostarczania z programu Visual Studio Team Service, zobacz [Wdróż maszynę wirtualną systemu Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publikowanie strony - strzałki w prawo]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publikowanie strony — ikona maszyny wirtualnej platformy Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selektor konto platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Selektor maszyna wirtualna platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Pakiet WebDeploy logowania]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Błąd certyfikatu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Okno danych wyjściowych]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Strona publikowania — przycisk Publikuj]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Strona — przycisk Ustawienia publikowania]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publikowanie ustawienia — strona połączenia]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Ustawienia — strona Ustawienia publikowania]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
