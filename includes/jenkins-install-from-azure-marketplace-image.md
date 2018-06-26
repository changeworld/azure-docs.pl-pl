---
description: Plik dyrektywy include
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: d5a832909f060ad8c8b3f0e7c7ea4504e5e5aadb
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943474"
---
1. W przeglądarce otwórz [portalu Azure Marketplace obrazu Wpięć](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Wybierz **Pobierz teraz**.

    ![Wybierz teraz IT GIT do uruchamiania procesu instalacji obrazu Wpięć Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Po przejrzeniu informacji cenową szczegóły i warunki, zaznacz **Kontynuuj**.

    ![Marketplace Wpięć ceny i warunki informacji o obrazie.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Wybierz **Utwórz** skonfigurować serwer Wpięć w portalu Azure. 

    ![Zainstaluj obraz Wpięć Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. W **podstawy** karcie, określ następujące wartości:

    - **Nazwa** — wprowadź `Jenkins`.
    - **Nazwa użytkownika** — wprowadź nazwę użytkownika używane podczas logowania się na maszynie wirtualnej, na którym działa Wpięć. Nazwa użytkownika musi spełniać [konkretne wymagania](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
    - **Typ uwierzytelniania** — wybierz tę opcję **klucz publiczny SSH**.
    - **Klucz publiczny SSH** -kopiowania i wklejania klucza publicznego RSA w formacie jednowierszowego (począwszy od `ssh-rsa`) lub format PEM wiele wierszy. Możesz wygenerować kluczy SSH za pomocą ssh-keygen w systemach Linux i macOS lub PuTTYGen w systemie Windows. Aby uzyskać więcej informacji na temat kluczy SSH i Azure, zobacz [jak klucze używanie SSH z systemem Windows Azure](/azure/virtual-machines/linux/ssh-from-windows).
    - **Subskrypcja** — Wybierz subskrypcję platformy Azure, w którym chcesz zainstalować Wpięć.
    - **Grupa zasobów** — wybierz tę opcję **Utwórz nowy**, a następnie wprowadź nazwę grupy zasobów, która służy jako kontener logiczny dla kolekcji zasobów, które składają się na instalację Wpięć.
    - **Lokalizacja** — wybierz tę opcję **wschodnie stany USA**.

    ![Wprowadź informacje grupy uwierzytelniania i zasobów dla Wpięć na karcie podstawowego.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Wybierz **OK** do przejścia do **dodatkowe ustawienia** kartę. 

1. W **dodatkowe ustawienia** karcie, określ następujące wartości:

    - **Rozmiar** — wybierz opcję odpowiednią rozmiaru Wpięć maszyny wirtualnej.
    - **Typ dysku maszyny Wirtualnej** — określ albo dysk twardy (dysk twardy) lub dysków SSD (SSD), aby wskazać, który typ dysku magazynu jest dozwolony dla Wpięć maszyny wirtualnej.
    - **Sieć wirtualna** — (opcjonalnie) zaznacz **sieci wirtualnej** można zmodyfikować ustawienia domyślne.
    - **Podsieci** — wybierz tę opcję **podsieci**, sprawdź informacje i wybierz **OK**.
    - **Publiczny adres IP** — nazwa adresu IP domyślnie nazwa Wpięć określone w poprzedniej strony z sufiksem - IP. Można wybrać opcję, aby zmienić tego ustawienie domyślne.
    - **Etykieta nazwy domeny** — Określ wartość dla w pełni kwalifikowany adres URL do maszyny wirtualnej Wpięć.
    - **Typ zlecenia Wpięć** — wybierz typ żądanej wersji z opcji: `LTS`, `Weekly build`, lub `Azure Verified`. `LTS` i `Weekly build` opcje są szczegółowo opisane w artykule [Wpięć LTS wersji wiersza](https://jenkins.io/download/lts/). `Azure Verified` Opcja odnosi się do [LTS Wpięć wersji](https://jenkins.io/download/lts/) zweryfikowaniu uruchamiania na platformie Azure. 

    ![Wprowadź ustawienia maszyny wirtualnej Wpięć na karcie Ustawienia.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Wybierz **OK** do przejścia do **ustawienia integracji** kartę.

1. W **ustawienia integracji** karcie, określ następujące wartości:

    - **Nazwy głównej usługi** -nazwy głównej usługi jest dodawany do Wpięć jako poświadczenia dla uwierzytelniania przy użyciu platformy Azure. `Auto` oznacza, że podmiot zabezpieczeń zostanie utworzona przez MSI (zarządzane tożsamość usługi). `Manual` oznacza, że podmiot zabezpieczeń powinny być tworzone przez użytkownika. 
        - **Identyfikator aplikacji** i **klucz tajny** — w przypadku wybrania `Manual` opcja dla **nazwy głównej usługi** opcji, musisz określić `Application ID` i `Secret` dla użytkownika nazwy głównej usługi. Gdy [Tworzenie nazwy głównej usługi](/cli/azure/create-an-azure-service-principal-azure-cli), należy zauważyć, że domyślna rola **współautora**, który jest wystarczające do pracy z zasobami Azure.
    - **Włącz agentów chmury** — Określ domyślny szablon chmury dla agentów gdzie `ACI` odwołuje się do wystąpień kontenera Azure i `VM` odwołuje się do maszyny wirtualnej. Można również określić `No` Jeśli nie chcesz, aby włączyć agenta chmury.

1. Wybierz **OK** do przejścia do **Podsumowanie** kartę.

1. Gdy **Podsumowanie** Wyświetla kartę, wprowadzonej zostanie zweryfikowany. Przeanalizowanie **weryfikacji przekazany** komunikatu (u góry karty), wybierz **OK**. 

    ![Karta Podsumowanie Wyświetla i sprawdza poprawność wybranych opcji.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Gdy **Utwórz** Wyświetla kartę, zaznacz **Utwórz** można utworzyć maszyny wirtualnej Wpięć. Gdy serwer jest gotowy, powiadomienie będzie wyświetlane w portalu Azure.

    ![Wpięć jest gotowy powiadomień.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)