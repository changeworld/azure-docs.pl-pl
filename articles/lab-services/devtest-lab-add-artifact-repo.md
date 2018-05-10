---
title: Dodaj repozytorium Git do laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać źródła niestandardowe artefakty repozytorium GitHub lub Visual Studio Team Services Git w usłudze Azure DevTest Labs.
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
ms.openlocfilehash: 80724a7d8d2b5cec19bdbce27cdafd4a9c09eb47
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Dodaj repozytorium Git do przechowywania niestandardowych artefaktów i szablony usługi Resource Manager

Możesz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md) dla maszyn wirtualnych w laboratorium, lub [umożliwiają utworzenie środowiska testowego niestandardowych szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Należy dodać prywatne repozytorium Git dla szablonów usługi Resource Manager, które tworzy zespołu lub artefakty. Repozytorium może być hostowana na [GitHub](https://github.com) lub na [Visual Studio Team Services](https://visualstudio.com).

Oferujemy [repozytorium GitHub artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) wdrażanego jako-, lub dostosować je w laboratoriach użytkownika. Podczas dostosowywania lub tworzenia artefaktu, nie można zapisać artefaktu publicznego repozytorium. Musisz utworzyć własne prywatne repozytorium artefaktów niestandardowych i artefaktów, które można utworzyć. 

Podczas tworzenia maszyny Wirtualnej, można zapisać szablon usługi Resource Manager, dostosować go, jeśli, a następnie użyć go później można utworzyć więcej maszyn wirtualnych. Należy utworzyć prywatne repozytorium do przechowywania szablonów niestandardowych usługi Resource Manager.  

* Aby dowiedzieć się, jak utworzyć repozytorium GitHub, zobacz [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Aby dowiedzieć się, jak utworzyć projekt usługi Team Services, który ma repozytorium Git, zobacz [Połącz z Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Poniższa ilustracja jest przykładem repozytorium, który ma artefaktów może wyglądać w witrynie GitHub:  

![Przykładowe repozytorium artefaktów GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Pobierz informacje o repozytorium i poświadczenia
Aby dodać repozytorium do laboratorium, najpierw Pobierz informacje o kluczu z repozytorium. W poniższych sekcjach opisano, jak można uzyskać informacji wymaganych dla repozytoriów, które są hostowane w serwisie GitHub lub Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Uzyskaj token dostępu osobistych i adres URL klonowania repozytorium GitHub

1. Przejdź do strony głównej repozytorium GitHub, zawierający artefaktu lub definicji szablonu usługi Resource Manager.
2. Wybierz **klonowania lub pobierania**.
3. Aby skopiować adres URL do Schowka, zaznacz **HTTPS url klonowania** przycisku. Zapisz adres URL do późniejszego użycia.
4. W prawym górnym rogu GitHub, wybierz obraz profilu, a następnie wybierz **ustawienia**.
5. W **ustawienia osobiste** menu po lewej stronie, wybierz opcję **osobiste tokeny dostępu**.
6. Wybierz **Wygeneruj nowy token**.
7. Na **nowy osobisty token dostępu** w obszarze **Token opis**, wprowadź opis. Zaakceptuj domyślne ustawienia w obszarze **wybierz zakres**, a następnie wybierz **Generuj Token**.
8. Zapisz wygenerowany token. Użyj tokenu na później.
9. Zamknij GitHub.   
10. Nadal [laboratorium nawiązać połączenia z repozytorium](#connect-your-lab-to-the-repository) sekcji.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Uzyskaj token dostępu osobistych i adres URL klonowania repozytorium programu Visual Studio Team Services

1. Przejdź do strony głównej zespołu kolekcji (na przykład https://contoso-web-team.visualstudio.com), a następnie wybierz projekt.
2. Na stronie głównej projektu wybierz **kod**.
3. Aby wyświetlić adres URL klonowania, w projekcie **kod** wybierz pozycję **klonowania**.
4. Zapisz adres URL. Później możesz użyć adresu URL.
5. Aby utworzyć osobisty token dostępu, w menu rozwijanym konta użytkownika, wybierz **Mój profil**.
6. Na stronie informacje o profilu wybierz **zabezpieczeń**.
7. Na **zabezpieczeń** wybierz opcję **Dodaj**.
8. Na **utworzyć osobisty token dostępu** strony:
   1. Wprowadź **opis** dla tokenu.
   2. W **wygasa w** listy, wybierz **180 dni**.
   3. W **kont** listy, wybierz **wszystkich dostępnych kont**.
   4. Wybierz **wszystkie zakresy** opcji.
   5. Wybierz **Utwórz Token**.
9. Nowy token, który jest wyświetlany w **osobiste tokeny dostępu** listy. Wybierz **kopiowania tokenu**, a następnie zapisz wartość tokenu do późniejszego użycia.
10. Nadal [laboratorium nawiązać połączenia z repozytorium](#connect-your-lab-to-the-repository) sekcji.

## <a name="connect-your-lab-to-the-repository"></a>Laboratorium nawiązać połączenia z repozytorium
1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **więcej usług**, a następnie wybierz **DevTest Labs** na liście usług.
3. Z listy labs wybierz laboratorium. 
4. Wybierz **konfiguracji i zasadach** > **repozytoria** > **+ Dodaj**.

    ![Przycisk Dodaj repozytorium](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na drugiej stronie **repozytoria** Podaj następujące informacje:
  1. **Nazwa**. Wprowadź nazwę dla repozytorium.
  2. **Adres Url klonowania Git**. Wprowadź adres URL klonowania Git HTTPS, które wcześniej zostały skopiowane z usługi GitHub lub Visual Studio Team Services.
  3. **Gałąź**. Aby uzyskać definicji, wprowadź gałęzi.
  4. **Osobisty Token dostępu**. Wprowadź osobisty token dostępu wcześniej pochodzący z usługi GitHub lub Visual Studio Team Services.
  5. **Ścieżka folderu**. Wprowadź co najmniej jedną ścieżkę folderu względem adresu URL w klonowania, zawierający Twoje artefaktu lub definicji szablonu usługi Resource Manager. Po określeniu podkatalogu, upewnij się, że zawierają ukośnik w ścieżce folderu.

     ![Repozytoria obszaru](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wybierz pozycję **Zapisz**.

### <a name="related-blog-posts"></a>Wpisy na blogu pokrewne
* [Rozwiązywanie problemów w przypadku braku artefaktów w usłudze DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Dołącz Maszynę wirtualną do istniejącej domeny usługi Active Directory przy użyciu szablonu usługi Resource Manager w usłudze DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu swoje prywatne repozytorium Git, można wykonać jedną lub obie z nich, w zależności od potrzeb:
* Magazyn sieci [niestandardowe artefakty](devtest-lab-artifact-author.md). Możesz użyć je później, do tworzenia nowych maszyn wirtualnych.
* [Tworzenie środowisk wielu maszyn wirtualnych i PaaS zasobów przy użyciu szablonów usługi Resource Manager](devtest-lab-create-environment-from-arm.md). Następnie można przechowywać szablonów w sieci prywatnej repozytorium.

Podczas tworzenia maszyny Wirtualnej, można sprawdzić, czy szablony lub artefakty są dodawane do repozytorium Git. Są one dostępne natychmiast na liście artefaktów lub szablonów. Nazwa sieci prywatne repozytorium jest wyświetlany w kolumnie, która określa źródło. 
