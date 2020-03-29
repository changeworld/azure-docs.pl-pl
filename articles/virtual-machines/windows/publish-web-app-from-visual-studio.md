---
title: Publikowanie aplikacji sieci Web na maszynie Wirtualnej platformy Azure z programu Visual Studio
description: Publikowanie ASP.NET aplikacji sieci Web na maszynie wirtualnej platformy Azure z programu Visual Studio
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71970878"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikowanie ASP.NET aplikacji sieci Web na maszynie Wirtualnej platformy Azure z programu Visual Studio

W tym dokumencie opisano sposób publikowania ASP.NET aplikacji sieci web na maszynie wirtualnej platformy Azure przy użyciu funkcji publikowania **maszyn wirtualnych platformy Microsoft Azure** w programie Visual Studio 2019.  

## <a name="prerequisites"></a>Wymagania wstępne
Aby program Visual Studio można było opublikować projekt ASP.NET na maszynie Wirtualnej platformy Azure, maszyna wirtualna musi być poprawnie skonfigurowana.

- Komputer musi być skonfigurowany do uruchamiania aplikacji sieci web ASP.NET i mieć zainstalowany system WebDeploy.

- Maszyna wirtualna musi mieć skonfigurowaną nazwę DNS. Aby uzyskać więcej informacji, zobacz [Tworzenie w pełni kwalifikowanej nazwy domeny w portalu Azure dla maszyny Wirtualnej systemu Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikowanie ASP.NET aplikacji sieci Web na maszynie Wirtualnej platformy Azure przy użyciu programu Visual Studio
W poniższej sekcji opisano sposób publikowania istniejącej aplikacji sieci web ASP.NET na maszynie wirtualnej platformy Azure.

1. Otwórz rozwiązanie aplikacji sieci web w programie Visual Studio 2019.
2. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie **Publikuj...**
3. Użyj strzałki po prawej stronie strony, aby przewijać opcje publikowania, aż znajdziesz **maszyny wirtualne platformy Microsoft Azure**.  

   ![Strona publikowania — strzałka w prawo]

4. Wybierz ikonę **Maszyny wirtualne platformy Microsoft Azure** i wybierz pozycję **Publikuj**.

   ![Strona publikowania — ikona maszyny wirtualnej platformy Microsoft Azure]

5. Wybierz odpowiednie konto (z subskrypcją platformy Azure połączoną z maszyną wirtualną).  
   - Jeśli zalogujesz się do programu Visual Studio, lista kont zostanie wypełniona wszystkimi uwierzytelnionymi kontami.  
   - Jeśli nie jesteś zalogowany lub potrzebne konto nie znajduje się na liście, wybierz opcję "Dodaj konto..." i postępuj zgodnie z instrukcjami, aby się zalogować.  
   ![Selektor kont platformy Azure]  

6. Wybierz odpowiednią maszynę wirtualną z listy istniejących maszyn wirtualnych.

   > [!Note]
   > Zapełnianie tej listy może zająć trochę czasu.

   ![Selektor maszyn wirtualnych platformy Azure]

7. Kliknij przycisk OK, aby rozpocząć publikowanie.

8. Po wyświetleniu monitu o poświadczenia podaj nazwę użytkownika i hasło konta użytkownika na docelowej maszynie Wirtualnej skonfigurowanej z prawami do publikowania. Te poświadczenia są zazwyczaj nazwa użytkownika administratora i hasło używane podczas tworzenia maszyny Wirtualnej.  

   ![Logowanie do webdeploy]

9. Zaakceptuj certyfikat zabezpieczeń.

   ![Błąd certyfikatu]

10. Obejrzyj okno Dane wyjściowe, aby sprawdzić postęp operacji publikowania.

    ![Okno wyniku]

11. Jeśli publikowanie zakończy się pomyślnie, przeglądarka uruchamia się, aby otworzyć adres URL nowo opublikowanej witryny.

**Sukces!**

Aplikacja sieci Web została pomyślnie opublikowana na maszynie wirtualnej platformy Azure.

## <a name="publish-page-options"></a>Opcje publikowania strony

Po zakończeniu pracy kreatora publikowania strona Publikowania jest dobrze otwierana w dokumencie z wybranym nowym profilem publikowania.

### <a name="re-publish"></a>Ponowne opublikowanie

Aby opublikować aktualizacje aplikacji sieci Web, wybierz przycisk **Publikuj** na stronie Publikowania.  
- Jeśli zostanie wyświetlony monit, wprowadź nazwę użytkownika i hasło.  
- Publikowanie rozpoczyna się natychmiast.

![Przycisk Publikuj stronę — przycisk Publikuj]

### <a name="modify-publish-profile-settings"></a>Modyfikowanie ustawień profilu publikowania

Aby wyświetlić i zmodyfikować ustawienia profilu publikowania, wybierz **pozycję Ustawienia...**.  

![Przycisk Publikuj stronę — ustawienia]

Ustawienia powinny wyglądać mniej więcej tak:  

![Ustawienia publikowania — strona Połączenia]

#### <a name="save-user-name-and-password"></a>Zapisz nazwę użytkownika i hasło
- Unikaj podawania informacji uwierzytelniających za każdym razem, gdy publikujesz. Aby to zrobić, wypełnij pola **Nazwa użytkownika** i **Hasło** i wybierz pole **Zapisz hasło.**
- Użyj przycisku **Sprawdź poprawność połączenia,** aby potwierdzić, że wprowadzono odpowiednie informacje.

#### <a name="deploy-to-clean-web-server"></a>Wdrażanie na czystym serwerze sieci Web

- Aby upewnić się, że serwer sieci web ma czystą kopię aplikacji sieci web po każdym przekazaniu i że żadne inne pliki nie zostały z poprzedniego wdrożenia, możesz zaznaczyć pole wyboru **Usuń dodatkowe pliki w miejscu docelowym na** karcie **Ustawienia.**

- Ostrzeżenie: Publikowanie za pomocą tego ustawienia powoduje usunięcie wszystkich plików, które istnieją na serwerze sieci web (katalog wwwroot). Upewnij się, że znasz stan urządzenia przed opublikowaniem z włączoną tą opcją. 

![Ustawienia publikowania — strona Ustawienia]

## <a name="next-steps"></a>Następne kroki

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Konfigurowanie ciągłej integracji/ciągłego wdrażania do automatycznego wdrażania na maszynie Wirtualnej platformy Azure

Aby skonfigurować potok ciągłego dostarczania za pomocą usługi Azure Pipelines, zobacz [Wdrażanie na maszynie wirtualnej systemu Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Strona publikowania — strzałka w prawo]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Strona publikowania — ikona maszyny wirtualnej platformy Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selektor kont platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Selektor maszyn wirtualnych platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Logowanie do webdeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Błąd certyfikatu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Okno wyjściowe]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Przycisk Publikuj stronę — przycisk Publikuj]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Przycisk Publikuj stronę — ustawienia]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Ustawienia publikowania — strona Połączenia]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Ustawienia publikowania — strona Ustawienia]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
