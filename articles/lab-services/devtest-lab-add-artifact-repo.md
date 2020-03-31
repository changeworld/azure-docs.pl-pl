---
title: Dodawanie repozytorium Git do laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać repozytorium Git Usługi Git usługi Git usługi Git usługi Git platformy Git dla niestandardowego źródła artefaktów w laboratorium devtest platformy Azure.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1e7587c60e180fb35e1a2bed735b053b6b0c388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294607"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Dodawanie repozytorium Git do przechowywania niestandardowych artefaktów i szablonów Menedżera zasobów

Niestandardowe artefakty dla maszyn [wirtualnych](devtest-lab-artifact-author.md) można utworzyć w laboratorium lub [użyć szablonów usługi Azure Resource Manager do utworzenia niestandardowego środowiska testowego.](devtest-lab-create-environment-from-arm.md) Należy dodać prywatne repozytorium Git dla artefaktów lub szablonów Menedżera zasobów, które tworzy twój zespół. Repozytorium może być hostowane w [usłudze GitHub](https://github.com) lub w [usługach Azure DevOps.](https://visualstudio.com)

Oferujemy [repozytorium GitHub artefaktów,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) które można wdrożyć w stanie takim, w jakim jest lub można dostosować je do laboratoriów. Podczas dostosowywania lub tworzenia artefaktu nie można przechowywać artefaktu w publicznym repozytorium. Należy utworzyć własne prywatne repozytorium dla artefaktów niestandardowych i artefaktów, które tworzysz. 

Podczas tworzenia maszyny wirtualnej można zapisać szablon Menedżera zasobów, dostosować go, jeśli chcesz, a następnie użyć go później, aby utworzyć więcej maszyn wirtualnych. Należy utworzyć własne prywatne repozytorium do przechowywania niestandardowych szablonów Menedżera zasobów.  

* Aby dowiedzieć się, jak utworzyć repozytorium GitHub, zobacz [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Aby dowiedzieć się, jak utworzyć projekt usługi Azure DevOps Services, który ma repozytorium Git, zobacz [Łączenie się z usługami Azure DevOps](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Poniższy rysunek jest przykładem tego, jak repozytorium, które ma artefakty może wyglądać w GitHub:  

![Przykładowe repo artefaktów GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Uzyskaj informacje o repozytorium i poświadczenia
Aby dodać repozytorium do laboratorium, najpierw pobierz kluczowe informacje z repozytorium. W poniższych sekcjach opisano sposób uzyskania wymaganych informacji dla repozytoriów, które są hostowane w usłudze GitHub lub usługi Azure DevOps.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania repozytorium GitHub i osobisty token dostępu

1. Przejdź do strony głównej repozytorium Usługi GitHub zawierającej definicje szablonów artefaktu lub Menedżera zasobów.
2. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
3. Aby skopiować adres URL do schowka, wybierz przycisk **adresu URL klonowania HTTPS.** Zapisz adres URL do późniejszego użycia.
4. W prawym górnym rogu githubu wybierz obraz profilu, a następnie wybierz pozycję **Ustawienia**.
5. W menu **Ustawienia osobiste** po lewej stronie wybierz polecenie **Osobiste tokeny dostępu**.
6. Wybierz **pozycję Generuj nowy token**.
7. Na stronie **Nowy token dostępu osobistego** w obszarze Opis **tokenu**wprowadź opis. Zaakceptuj elementy domyślne w obszarze **Wybierz zakresy**, a następnie wybierz pozycję **Generuj token**.
8. Zapisz wygenerowany token. Token można użyć później.
9. Zamknij GitHub.   
10. Przejdź do sekcji [Połącz laboratorium z repozytorium.](#connect-your-lab-to-the-repository)

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania usługi Azure i token dostępu osobistego

1. Przejdź do strony głównej kolekcji zespołu `https://contoso-web-team.visualstudio.com`(na przykład ), a następnie wybierz projekt.
2. Na stronie głównej projektu wybierz pozycję **Kod**.
3. Aby wyświetlić adres URL klonowania, na stronie **Kod** projektu wybierz pozycję **Klonuj**.
4. Zapisz adres URL. Później użyjesz adresu URL.
5. Aby utworzyć token dostępu osobistego, w menu rozwijanym konta użytkownika wybierz pozycję **Mój profil**.
6. Na stronie informacji o profilu wybierz pozycję **Zabezpieczenia**.
7. Na karcie **Zabezpieczenia** wybierz pozycję **Dodaj**.
8. Na stronie **Tworzenie tokenu dostępu osobistego:**
   1. Wprowadź **opis** tokenu.
   2. Na liście **Wygasa w** wybierz **pozycję 180 dni**.
   3. Na liście **Konta** wybierz pozycję **Wszystkie dostępne konta**.
   4. Wybierz opcję **Tylko do odczytu.**
   5. Wybierz **pozycję Utwórz token**.
9. Nowy token pojawia się na liście **tokenów dostępu osobistego.** Wybierz **polecenie Kopiuj token**, a następnie zapisz wartość tokenu do późniejszego użycia.
10. Przejdź do sekcji [Połącz laboratorium z repozytorium.](#connect-your-lab-to-the-repository)

## <a name="connect-your-lab-to-the-repository"></a>Podłączanie laboratorium do repozytorium
1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **pozycję Więcej usług**, a następnie wybierz **devtest labs** z listy usług.
3. Z listy laboratoriów wybierz laboratorium. 
4. Wybierz pozycję**Repozytoria** >  **konfiguracji i zasad** > **+ Dodaj**.

    ![Przycisk Dodaj repozytorium](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na drugiej stronie **repozytoria** podaj następujące informacje:
   1. **Nazwa**. Wprowadź nazwę repozytorium.
   2. **Git Clone Url**. Wprowadź adres URL klonowania protokołu HTTPS git, który został skopiowany wcześniej z usługi GitHub lub Azure DevOps.
   3. **Oddział**. Aby uzyskać definicje, wprowadź gałąź.
   4. **Token dostępu osobistego**. Wprowadź token dostępu osobistego, który został wcześniej odebrany z usługi GitHub lub Azure DevOps.
   5. **Ścieżki folderów**. Wprowadź co najmniej jedną ścieżkę folderu względem adresu URL klonowania zawierającego artefakt lub definicje szablonów Menedżera zasobów. Po określeniu podkatalogu upewnij się, że ukośnik do przodu jest dołączany do ścieżki folderu.

      ![Obszar repozytoriów](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wybierz **pozycję Zapisz**.

### <a name="related-blog-posts"></a>Podobne posty na blogu
* [Rozwiązywanie problemów z artefaktami w laboratorium DevTest](devtest-lab-troubleshoot-artifact-failure.md)
* [Dołączanie maszyny Wirtualnej do istniejącej domeny usługi Active Directory przy użyciu szablonu Menedżera zasobów w laboratoriach DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu prywatnego repozytorium Git możesz wykonać jedną lub obie z następujących czynności, w zależności od potrzeb:
* Przechowuj [niestandardowe artefakty](devtest-lab-artifact-author.md). Można ich użyć później do tworzenia nowych maszyn wirtualnych.
* [Twórz środowiska wielu maszyn wirtualnych i zasoby PaaS przy użyciu szablonów Menedżera zasobów](devtest-lab-create-environment-from-arm.md). Następnie można przechowywać szablony w repozytorium prywatnym.

Podczas tworzenia maszyny Wirtualnej można sprawdzić, czy artefakty lub szablony są dodawane do repozytorium Git. Są one natychmiast dostępne na liście artefaktów lub szablonów. Nazwa prywatnego repozytorium jest wyświetlana w kolumnie określającej źródło. 
