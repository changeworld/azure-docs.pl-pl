---
title: Publikowanie aplikacji sieci Web na maszynie Wirtualnej platformy Azure z programu Visual Studio
description: Publikowanie aplikacji sieci Web platformy ASP.NET na maszynie wirtualnej platformy Azure z programu Visual Studio
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 4b8e3ddf1cf5d61f730ce01a35ee0813b47ad2d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66305936"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikowanie aplikacji sieci Web platformy ASP.NET na maszynie Wirtualnej platformy Azure z programu Visual Studio

W tym dokumencie opisano sposób publikowania aplikacji sieci web ASP.NET przy użyciu maszyn wirtualnych (VM) **Microsoft Azure Virtual Machines** funkcja publikowania w programie Visual Studio 2019 r.  

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było używać programu Visual Studio do publikowania projektu programu ASP.NET na Maszynie wirtualnej platformy Azure, maszyna wirtualna musi być skonfigurowany prawidłowo.

- Maszyna musi być skonfigurowany w celu uruchomienia aplikacji sieci web ASP.NET i WebDeploy zainstalowane.

- Maszyna wirtualna musi mieć nazwę DNS skonfigurowane. Aby uzyskać więcej informacji, zobacz [tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure portal dla maszyny Wirtualnej z systemem Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikowanie aplikacji sieci web platformy ASP.NET do maszyny Wirtualnej platformy Azure przy użyciu programu Visual Studio
W poniższej sekcji opisano sposób publikowania istniejącą aplikację sieci web platformy ASP.NET na maszynie wirtualnej platformy Azure.

1. Otwórz swoje rozwiązanie aplikacji sieci web w programie Visual Studio 2019 r.
2. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie **publikowania...**
3. Użyj strzałki w prawym rogu strony, aby przewijać opcje publikowania, dopóki nie znajdziesz **Microsoft Azure Virtual Machines**.  

   ![Publikowanie strony - strzałkę w prawo]

4. Wybierz **Microsoft Azure Virtual Machines** ikonę i wybierz pozycję **Publikuj**.

   ![Publikowanie strony — ikona maszyny wirtualnej Microsoft Azure]

5. Wybierz odpowiednie konto (w ramach subskrypcji platformy Azure, połączone z maszyną wirtualną).  
   - Jeśli zalogowano się do programu Visual Studio, z listy kont zostanie wypełniona uwierzytelnionego konta.  
   - Jeśli użytkownik nie zalogowany lub konto, należy nie ma na liście, wybierz pozycję "Dodaj konto..." i postępuj zgodnie z monitami, aby zalogować się.  
   ![Selektor konta platformy Azure]  

6. Wybierz odpowiednią maszynę Wirtualną z listy z istniejących maszyn wirtualnych.

   > [!Note]
   > Wypełnianie tej listy może zająć trochę czasu.

   ![Selektor maszyny Wirtualnej platformy Azure]

7. Kliknij przycisk OK, aby rozpocząć publikowanie.

8. Po wyświetleniu monitu o poświadczenia, należy podać nazwę użytkownika i hasło konta użytkownika na docelowej maszynie Wirtualnej, który jest skonfigurowany z prawa do publikacji. Te poświadczenia są zazwyczaj nazwa użytkownika administratora i hasło użyte podczas tworzenia maszyny Wirtualnej.  

   ![WebDeploy logowania]

9. Zaakceptuj ten certyfikat zabezpieczeń.

   ![Błąd certyfikatu]

10. Obejrzyj w oknie danych wyjściowych, aby sprawdzić postęp operacji publikowania.

    ![Okno wyniku]

11. Jeśli publikowanie zakończy się pomyślnie, otworzyć adres URL witryny nowo opublikowana spowoduje uruchomienie przeglądarki.

**SUKCES!**

Twoja aplikacja sieci web teraz został pomyślnie opublikowany na maszynie wirtualnej platformy Azure.

## <a name="publish-page-options"></a>Strona Opcje publikowania

Po zakończeniu pracy Kreatora publikowania strony publikowania zostanie otwarty w dokumencie dobrze z wybrany nowy profil publikowania.

### <a name="re-publish"></a>Ponowne publikowanie

Aby publikować aktualizacje do aplikacji sieci web, wybierz **Publikuj** przycisk na stronie publikowania.  
- Jeśli zostanie wyświetlony monit, wprowadź nazwę użytkownika i hasło.  
- Publikowanie rozpocznie się natychmiast.

![Strona publikowania — przycisk Opublikuj]

### <a name="modify-publish-profile-settings"></a>Modyfikowanie ustawień profilu publikowania

Aby wyświetlić i zmodyfikować ustawienia profilu publikowania, wybierz pozycję **ustawień...** .  

![Strona — przycisk Ustawienia publikowania]

Twoje ustawienia powinny wyglądać następująco:  

![Ustawienia — strona połączenia publikowania]

#### <a name="save-user-name-and-password"></a>Zapisz nazwę użytkownika i hasło
- Należy unikać, podając informacje o uwierzytelnianiu za każdym razem, gdy publikujesz. Aby to zrobić, należy wypełnić **nazwa_użytkownika** i **hasło** pola i wybierz pozycję **Zapisz hasło** pole.
- Użyj **Waliduj połączenie** przycisk, aby upewnić się, że wprowadzono odpowiednie informacje.

#### <a name="deploy-to-clean-web-server"></a>Wdrażanie do czyszczenia serwera sieci web

- Jeśli chcesz upewnić się, czy serwer sieci web jest czysta kopia aplikacji sieci web po każdym przekazywania oraz że żadne inne pliki nie są pozostawiane z poprzedniego wdrożenia można sprawdzić **Usuń dodatkowe pliki w lokalizacji docelowej** pola wyboru w  **Ustawienia** kartę.

- Ostrzeżenie: Publikowanie za pomocą tego ustawienia spowoduje usunięcie wszystkich plików, które istnieją na serwerze sieci web (katalogu wwwroot). Upewnij się, że znasz stan maszyny przed opublikowaniem po włączeniu tej opcji. 

![Ustawienia — strona Ustawienia publikowania]

## <a name="next-steps"></a>Kolejne kroki

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Konfigurowanie ciągłej integracji/ciągłego Dostarczania dla automatycznego wdrażania na maszynie Wirtualnej platformy Azure

Aby skonfigurować potok ciągłego dostarczania z potokiem, Azure, zobacz [Wdróż maszynę wirtualną Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publikowanie strony - strzałkę w prawo]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publikowanie strony — ikona maszyny wirtualnej Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selektor konta platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Selektor maszyny Wirtualnej platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy logowania]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Błąd certyfikatu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Okno Dane wyjściowe]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Strona publikowania — przycisk Opublikuj]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Strona — przycisk Ustawienia publikowania]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Ustawienia — strona połączenia publikowania]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Ustawienia — strona Ustawienia publikowania]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
