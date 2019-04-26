---
title: Dodawanie repozytorium Git do laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać repozytorium GitHub lub repozytorium Git usługi Azure DevOps Services źródła niestandardowe artefakty w usłudze Azure DevTest Labs.
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
ms.openlocfilehash: 5d7665cbfdf855e194f61910f0c8ee2bce5469b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311711"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Dodawanie repozytorium Git do przechowywania niestandardowych artefaktów i szablonów usługi Resource Manager

Możesz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md) dla maszyn wirtualnych w środowisku laboratoryjnym lub [tworzenia środowiska testowego niestandardowych za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Należy dodać prywatnego repozytorium Git dla artefaktów i szablonów usługi Resource Manager, które zespół tworzy. Repozytorium może być hostowana na [GitHub](https://github.com) lub na [usługom DevOps platformy Azure](https://visualstudio.com).

Firma Microsoft oferuje [repozytorium GitHub artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , którą można wdrożyć jako —, lub można je dostosować do laboratorium. Podczas dostosowywania lub tworzenia artefaktu, nie można zapisać artefakt w publicznym repozytorium. Musisz utworzyć własne prywatnym repozytorium dla niestandardowych artefaktów i artefaktów, które tworzysz. 

Podczas tworzenia maszyny Wirtualnej, możesz zapisać szablon usługi Resource Manager, dostosować go, jeśli, a następnie użyć go później do utworzenia więcej maszyn wirtualnych. Należy utworzyć prywatnym repozytorium do przechowywania niestandardowych szablonów usługi Resource Manager.  

* Aby dowiedzieć się, jak utworzyć repozytorium GitHub, zobacz [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Aby dowiedzieć się, jak utworzyć projekt usługom DevOps platformy Azure, która ma repozytorium Git, zobacz [nawiązywanie połączenia z usługi Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Następujący rysunek jest przykładem repozytorium, które ma artefaktów może wyglądać w usłudze GitHub:  

![Przykładowe repozytorium artefaktów w usłudze GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Uzyskaj informacje o repozytorium i poświadczeń
Aby dodać repozytorium do środowiska laboratoryjnego, należy najpierw Pobierz informacje o kluczu ze swojego repozytorium. Poniższe sekcje zawierają opis można pobrać informacji wymaganych w przypadku repozytoriów hostowanych w usłudze GitHub lub usługom DevOps platformy Azure.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania repozytorium GitHub i pat token

1. Przejdź do strony głównej w repozytorium GitHub zawierającego artefaktu lub definicje szablonów usługi Resource Manager.
2. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
3. Aby skopiować adres URL do Schowka, zaznacz **adres url klonowania HTTPS** przycisku. Zapisz adres URL do późniejszego użycia.
4. W prawym górnym rogu usługi GitHub wybierz obraz profilu, a następnie wybierz **ustawienia**.
5. W **ustawienia osobiste** menu po lewej stronie, wybierz opcję **osobiste tokeny dostępu**.
6. Wybierz **Wygeneruj nowy token**.
7. Na **nowe osobisty token dostępu** w obszarze **opis tokenu**, wprowadź opis. Zaakceptuj domyślne elementy w obszarze **wybierz zakresy**, a następnie wybierz pozycję **Generuj Token**.
8. Zapisz wygenerowany token. Przy użyciu tokenu później.
9. Zamknij usługi GitHub.   
10. W dalszym ciągu [laboratorium nawiązać połączenie z repozytorium](#connect-your-lab-to-the-repository) sekcji.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Uzyskaj klonowanie repozytoriów platformy Azure, adres URL i osobistego tokenu dostępu

1. Przejdź do strony głównej zespołu kolekcji (na przykład https://contoso-web-team.visualstudio.com), a następnie wybierz swój projekt.
2. Na stronie głównej projektu wybierz **kodu**.
3. Aby wyświetlić adres URL klonowania w projekcie **kodu** wybierz opcję **klonowania**.
4. Zapisz adres URL. Adres URL można użyć później.
5. Aby utworzyć osobisty token dostępu, w menu rozwijanego konta użytkownika, wybierz **Mój profil**.
6. Na stronie informacje o profilu, wybierz **zabezpieczeń**.
7. Na **zabezpieczeń** zaznacz **Dodaj**.
8. Na **utworzyć osobisty token dostępu** strony:
   1. Wprowadź **opis** dla tokenu.
   2. W **wygasa w** listy wybierz **180 dni**.
   3. W **kont** listy wybierz **wszystkich dostępnych kont**.
   4. Wybierz **wszystkie zakresy** opcji.
   5. Wybierz **Utwórz Token**.
9. Nowy token, który pojawia się w **osobistych tokenów dostępu** listy. Wybierz **kopiowania tokenu**, a następnie zapisz wartość tokenu w celu późniejszego użycia.
10. W dalszym ciągu [laboratorium nawiązać połączenie z repozytorium](#connect-your-lab-to-the-repository) sekcji.

## <a name="connect-your-lab-to-the-repository"></a>Łączenie środowiska laboratoryjnego do repozytorium
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **więcej usług**, a następnie wybierz pozycję **DevTest Labs** z listy usług.
3. Zaznacz na liście laboratoriów środowiska laboratoryjnego. 
4. Wybierz **konfiguracji i zasad** > **repozytoriów** > **+ Dodaj**.

    ![Przycisk Dodaj repozytorium](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. W drugiej **repozytoriów** określ następujące informacje:
   1. **Nazwa**. Wprowadź nazwę repozytorium.
   2. **Adres Url klonowania Git**. Wprowadź adres URL klonowania Git HTTPS, które wcześniej zostały skopiowane z usługi GitHub lub usługom DevOps platformy Azure.
   3. **Gałąź**. Aby uzyskać definicji, wprowadź gałęzi.
   4. **Osobisty Token dostępu**. Wprowadź osobisty token dostępu uzyskany wcześniej w usłudze GitHub lub usługom DevOps platformy Azure.
   5. **Ścieżka folderu**. Wprowadź co najmniej jedną ścieżkę folderu względem adres URL klonowania, który zawiera Twoje artefaktu lub definicje szablonów usługi Resource Manager. Po określeniu podkatalogu, upewnij się, że zawierają ukośnika w ścieżce folderu.

      ![Obszar repozytoriów](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wybierz pozycję **Zapisz**.

### <a name="related-blog-posts"></a>Wpisy w blogu pokrewne
* [Rozwiązywanie problemów w przypadku braku artefaktów w usłudze DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Dołączanie maszyny Wirtualnej do istniejącej domeny usługi Active Directory przy użyciu szablonu usługi Resource Manager w usłudze DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu prywatnym repozytorium Git, można wykonać jedno lub oba z następujących czynności, w zależności od potrzeb:
* Store Twoja [niestandardowych artefaktów](devtest-lab-artifact-author.md). Możesz użyć ich później, można tworzyć nowe maszyny wirtualne.
* [Tworzenie środowisk z wieloma Maszynami wirtualnymi i zasobów PaaS przy użyciu szablonów usługi Resource Manager](devtest-lab-create-environment-from-arm.md). Następnie można przechowywać szablonów w swoim repozytorium prywatnego.

Podczas tworzenia maszyny Wirtualnej można sprawdzić, artefaktów i szablonów są dodawane do repozytorium Git. Są one natychmiast dostępne na liście artefaktów i szablonów. Nazwa repozytorium prywatnego jest wyświetlany w kolumnie, która określa źródło. 
