---
title: Wizualne Tworzenie usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać wizualnego tworzenia w usłudze Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: shlo
ms.openlocfilehash: 8132f89423883422d70981edd3ddaf86147830e2
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394430"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Wizualne Tworzenie usługi Azure Data Factory
Usługi Azure Data Factory użytkownika interfejsu środowiska pozwala wizualnie tworzyć i wdrażać zasoby fabryki danych bez konieczności pisania kodu. Można przeciągnąć działania na kanwę potoku, wykonywać przebiegi testowe, interakcyjnie debugować i wdrożyć i monitorować uruchomienia potoków. Dostępne są dwie opcje używania środowiska użytkownika do wykonania wizualnego tworzenia:

- Autor bezpośrednio z usługi Data Factory.
- Tworzenie za pomocą integracji Azure repozytoriów Git do współpracy, kontroli źródła i przechowywanie wersji.

## <a name="author-directly-with-the-data-factory-service"></a>Tworzenie bezpośrednio za pomocą usługi Data Factory
Tworzeniu wizualizacji przy użyciu usługi Data Factory różni się od tworzeniu wizualizacji przy użyciu integrację z usługą Git na dwa sposoby:

- Usługa Data Factory nie obejmuje repozytorium do przechowywania obiektów JSON dla Twoich zmian.
- Usługa Data Factory nie jest zoptymalizowana pod kątem współpracy lub kontroli wersji.

![Konfigurowanie usługi Data Factory ](media/author-visually/configure-data-factory.png)

Jeśli używasz środowiska użytkownika **Kanwa tworzenia** można tworzyć bezpośrednio w usłudze Data Factory, tylko **Opublikuj wszystkie** tryb jest dostępny. Wszelkie zmiany, które są publikowane bezpośrednio w usłudze Data Factory.

![Tryb publikowania](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Tworzenie przy użyciu integrację z usługą Azure repozytoriów Git
Tworzeniu wizualizacji przy użyciu integrację z usługą Azure repozytoriów Git obsługuje kontroli źródła i współpracy for work na potoków usługi data factory. Fabryki danych można skojarzyć z repozytorium organizacji Azure repozytoriów Git do kontroli źródła, współpracy, przechowywanie wersji i tak dalej. Jednej organizacji Git repozytoriów platformy Azure może mieć wiele repozytoriów, ale repozytorium Git repozytoriów platformy Azure może być skojarzony z tylko jedną fabryki danych. Jeśli nie masz repozytoriów platformy Azure organizacji lub repozytorium, postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) do tworzenia zasobów.

> [!NOTE]
> Skrypt i pliki danych można przechowywać w repozytorium Git repozytoriów platformy Azure. Jednak trzeba ręcznie przekazać pliki do usługi Azure Storage. Potok usługi Data Factory nie automatycznie Przekaż skrypt lub pliki danych przechowywanych w repozytorium Git repozytoriów platformy Azure do usługi Azure Storage.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurowanie repozytorium Git repozytoriów platformy Azure przy użyciu usługi Azure Data Factory
Repozytorium Git repozytoriów platformy Azure można skonfigurować za pomocą usługi data factory, za pomocą dwóch metod.

#### <a name="method1"></a> Metoda konfiguracji 1 (repozytorium Git repozytoriów platformy Azure): strona zaczynajmy

W usłudze Azure Data Factory, przejdź do **zaczynajmy** strony. Wybierz **Konfigurowanie repozytorium kodu**:

![Konfigurowanie repozytorium kodu repozytoriów platformy Azure](media/author-visually/configure-repo.png)

**Ustawienia repozytorium** zostanie wyświetlone okienko konfiguracji:

![Konfigurowanie ustawień repozytorium kodu](media/author-visually/repo-settings.png)

W okienku wyświetlana następujący kod repozytoriów Azure ustawień repozytorium:

| Ustawienie | Opis | Wartość |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu repozytoriów platformy Azure.<br/>**Uwaga**: GitHub nie jest obecnie obsługiwane. | Azure repozytoriów Git |
| **Azure Active Directory** | Nazwa dzierżawy usługi Azure AD. | <your tenant name> |
| **Organizacja repozytoriów platformy Azure** | Nazwa organizacji repozytoriów platformy Azure. Możesz znaleźć, jeśli nazwa organizacji repozytoriów platformy Azure w `https://{organization name}.visualstudio.com`. Możesz [Zaloguj się do Twojej organizacji repozytoriów Azure](https://www.visualstudio.com/team-services/git/) dostęp do Twojego profilu programu Visual Studio i zobacz projektów i repozytoriów. | <your organization name> |
| **ProjectName** | Nazwa projektu repozytoriów platformy Azure. Możesz znaleźć, jeśli nazwa projektu repozytoriów platformy Azure w `https://{organization name}.visualstudio.com/{project name}`. | <your Azure Repos project name> |
| **RepositoryName** | Nazwę repozytorium kodu repozytoriów platformy Azure. Projekty repozytoriów platformy Azure zawierają repozytoriów Git do zarządzania kodem źródłowym, wraz ze wzrostem natężenia projektu. Możesz utworzyć nowe repozytorium, lub użyć istniejącego repozytorium, który jest już w projekcie. | <your Azure Repos code repository name> |
| **Gałąź współpracy** | Gałęzi współpracy repozytoriów platformy Azure, które jest używane do publikowania. Domyślnie jest `master`. To ustawienie można zmienić w przypadku, gdy chcesz opublikować zasobów z innej gałęzi. | <your collaboration branch name> |
| **Folder główny** | Folderem w gałęzi współpracy repozytoriów platformy Azure. | <your root folder name> |
| **Importuj istniejące zasoby fabryki danych do repozytorium** | Określa, czy Importuj istniejące zasoby fabryki danych ze środowiska użytkownika **Kanwa tworzenia** do repozytorium Git repozytoriów platformy Azure. Zaznacz pole, aby zaimportować swoje zasoby fabryki danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja Eksportuje każdy zasób osobno (czyli usługi połączone i zestawy danych są eksportowane do oddzielnych JSON Smb1sessionsetup). Gdy to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Wybrane (ustawienie domyślne) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Metoda konfiguracji 2 (repozytorium Git repozytoriów platformy Azure): UX Kanwa tworzenia
W Interfejsie usługi Azure Data Factory **Kanwa tworzenia**, zlokalizuj fabryką danych. Wybierz **usługi Data Factory** menu rozwijanego, a następnie wybierz **Konfiguruj repozytorium kodu**.

Zostanie wyświetlone okienko konfiguracji. Aby uzyskać szczegółowe informacje o ustawieniach konfiguracji, zobacz opisy w <a href="#method1">metody konfiguracji 1</a>.

![Konfigurowanie ustawień repozytorium kodu na potrzeby tworzenia interfejsu użytkownika](media/author-visually/configure-repo-2.png)

## <a name="use-a-different-azure-active-directory-tenant"></a>Użyj innej dzierżawy usługi Azure Active Directory

Repozytorium Git repozytoriów platformy Azure można utworzyć w innej dzierżawie usługi Azure Active Directory. Aby określić inną dzierżawą usługi Azure AD, musisz mieć uprawnienia administratora dla subskrypcji platformy Azure, której używasz.

## <a name="switch-to-a-different-git-repo"></a>Przełącz się do innego repozytorium Git

Aby przełączyć się do innego repozytorium Git, Znajdź ikonę w prawym górnym rogu strony Przegląd usługi Data Factory, jak pokazano na poniższym zrzucie ekranu. Jeśli nie widzisz ikonę wyczyścić pamięci podręcznej lokalnej przeglądarki. Wybierz ikonę, aby usunąć skojarzenie z bieżącego repozytorium.

Po usunięciu skojarzenia z bieżącego repozytorium, można skonfigurować ustawienia Git, aby użyć innego repozytorium. Następnie można zaimportować istniejące zasoby fabryki danych do nowego repozytorium.

![Usuń skojarzenie z bieżącego repozytorium Git](media/author-visually/remove-repo.png)

## <a name="use-version-control"></a>Korzystanie z kontroli wersji
Systemy kontroli wersji (znany także jako _kontroli źródła_) umożliwiają deweloperom współpracować nad kodu i śledzenie zmian wprowadzonych do kodu podstawowego. Kontrola źródła jest niezbędnego narzędzia dla deweloperów wielu projektów.

Każde repozytorium Git repozytoriów platformy Azure, która jest skojarzona z fabryką danych ma gałąź pracy zespołowej. (`master` to gałąź domyślna współpracy). Użytkownicy mogą również tworzyć gałęzie funkcji, klikając **+ nowa gałąź** i Programowanie w gałęzie funkcji.

![Zmień kod, synchronizowanie i publikowania](media/author-visually/sync-publish.png)

Gdy jesteś gotowy z programowaniem funkcji w gałęzi funkcji, możesz kliknąć **Utwórz żądanie ściągnięcia**. Tej akcji wykonywanych Git repozytoriów platformy Azure, w którym można podnieść, żądaniach ściągnięcia, przeglądy kodu i scalania zmian w gałęzi współpracy. (`master` jest ustawieniem domyślnym). Tylko możesz opublikować w usłudze Data Factory ze swojej gałęzi współpracy. 

![Utwórz nowe żądanie ściągnięcia](media/author-visually/create-pull-request.png)

## <a name="configure-publishing-settings"></a>Konfigurowanie ustawień publikowania

Aby skonfigurować gałąź publikowania — oznacza to, gałęzi, w którym są zapisywane szablonów usługi Resource Manager — Dodaj `publish_config.json` plik do folderu głównego w gałęzi współpracy. Data Factory odczytuje tego pliku, szuka pole `publishBranch`i tworzy nową gałąź (jeśli jeszcze nie istnieje) z podanej wartości. Następnie zapisuje wszystkie szablony usługi Resource Manager w określonej lokalizacji. Na przykład:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Podczas publikowania w trybie Git, można sprawdzić, Data Factory używa gałęzi publikowania, która spodziewasz się, jak pokazano na poniższym zrzucie ekranu:

![Upewnij się, poprawny publikowania gałęzi](media/author-visually/configure-publish-branch.png)

Podczas określania nowej gałęzi publikowania usługi Data Factory nie powoduje usunięcia poprzedniej gałęzi publikowania. Chcąc zdalnego poprzedniego gałęzi publikowania, usuń go ręcznie.

Data Factory odczytuje tylko `publish_config.json` plików podczas ładowania fabryki. Jeśli masz już fabryki załadowane w portalu, Odśwież przeglądarkę, aby wprowadzić zmiany zaczęły obowiązywać.

## <a name="publish-code-changes"></a>Publikowanie zmian w kodzie
Po zostały scalone zmiany w gałęzi współpracy (`master` jest ustawieniem domyślnym), wybierz opcję **Publikuj** ręcznie opublikować zmiany kodu w gałęzi głównej w usłudze Data Factory.

![Publikowanie zmian w usłudze Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Gałąź główna nie jest językiem co to jest wdrożony w usłudze Data Factory. Gałąź główna *musi* można ręcznie opublikować w usłudze Data Factory.

## <a name="author-with-github-integration"></a>Tworzenie za pomocą integracji usługi GitHub

Tworzeniu wizualizacji przy użyciu Integracja z usługą GitHub obsługuje kontroli źródła i współpracy for work na potoków usługi data factory. Fabryki danych można skojarzyć z repozytorium GitHub konta do kontroli źródła, współpracy, obsługi wersji. Na jednym koncie usługi GitHub może mieć wiele repozytoriów, ale repozytorium GitHub mogą być skojarzone z fabryką danych tylko jeden. Jeśli nie masz konta usługi GitHub lub repozytorium, postępuj zgodnie z [w instrukcjach](https://github.com/join) do tworzenia zasobów. Integracja z usługą GitHub przy użyciu usługi Data Factory obsługuje zarówno publiczne usługi GitHub oraz jak GitHub Enterprise.

Aby skonfigurować repozytorium GitHub, musisz mieć uprawnienia administratora dla subskrypcji platformy Azure, którego używasz.

Wprowadzenie dziewięć minutę i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Ograniczenia

- Skrypt i pliki danych można przechowywać w repozytorium GitHub. Jednak trzeba ręcznie przekazać pliki do usługi Azure Storage. Potok usługi Data Factory nie automatycznie Przekaż skrypt lub pliki danych przechowywanych w repozytorium GitHub do usługi Azure Storage.

- GitHub Enterprise w wersji starszej niż 2.14.0 nie działa w przeglądarce Microsoft Edge.

- Integracja z usługą GitHub przy użyciu współczynnika danych wizualnych narzędzi do tworzenia działa tylko w ogólnie dostępnej wersji usługi fabryka danych.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Konfigurowanie publicznego repozytorium GitHub przy użyciu usługi Azure Data Factory

Repozytorium GitHub można skonfigurować za pomocą usługi data factory, za pomocą dwóch metod.

**Metoda konfiguracji 1 (publiczne repozytorium): strona zaczynajmy**

W usłudze Azure Data Factory, przejdź do **zaczynajmy** strony. Wybierz **Konfigurowanie repozytorium kodu**:

![Strona wprowadzenie fabryki danych](media/author-visually/github-integration-image1.png)

**Ustawienia repozytorium** zostanie wyświetlone okienko konfiguracji:

![Ustawienia repozytorium GitHub](media/author-visually/github-integration-image2.png)

W okienku wyświetlana następujący kod repozytoriów Azure ustawień repozytorium:

| **Ustawienie**                                              | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Wartość**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Typ repozytorium**                                      | Typ repozytorium kodu repozytoriów platformy Azure.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Konto usługi GitHub**                                       | Nazwa konta usługi GitHub. Ta nazwa można znaleźć https://github.com/{account Nazwa} / {Nazwa repozytorium}. Przejdź do tej strony wyświetli monit o wprowadzenie poświadczeń OAuth usługi GitHub do konta usługi GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Nazwa repozytorium usługi GitHub kodu. Konta usługi GitHub zawierają repozytoriów Git do zarządzania kodem źródłowym. Możesz utworzyć nowe repozytorium, lub użyć istniejącego repozytorium, który jest już na Twoim koncie.                                                                                                                                                                                                                              |                    |
| **Gałąź współpracy**                                 | Gałęzi współpracy usługi GitHub, które jest używane do publikowania. Domyślnie jest głównym. To ustawienie można zmienić w przypadku, gdy chcesz opublikować zasobów z innej gałęzi.                                                                                                                                                                                                                                                               |                    |
| **Folder główny**                                          | Folderem w gałęzi współpracy usługi GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importuj istniejące zasoby fabryki danych do repozytorium** | Określa, czy Importuj istniejące zasoby fabryki danych ze środowiska użytkownika **Kanwa tworzenia** do repozytorium GitHub. Zaznacz pole, aby zaimportować swoje zasoby fabryki danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja Eksportuje każdy zasób osobno (czyli usługi połączone i zestawy danych są eksportowane do oddzielnych JSON Smb1sessionsetup). Gdy to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Wybrane (ustawienie domyślne) |
| **Gałąź do importowania zasobów do**                       | Określa, które gałęzią zasoby fabryki danych (potoki, zestawy danych, połączonych usług itp.) są importowane. Zasoby można importować do jednej z następujących gałęziach:. B współpracy. Utwórz nowy. Użyj istniejącego                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Metoda konfiguracji 2 (publiczne repozytorium): UX Kanwa tworzenia

W Interfejsie usługi Azure Data Factory **Kanwa tworzenia**, zlokalizuj fabryką danych. Wybierz **usługi Data Factory** menu rozwijanego, a następnie wybierz **Konfiguruj repozytorium kodu**.

Zostanie wyświetlone okienko konfiguracji. Aby uzyskać szczegółowe informacje o ustawieniach konfiguracji, zobacz opisy w *metody konfiguracji 1* powyżej.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Konfigurowanie repozytorium GitHub Enterprise za pomocą usługi Azure Data Factory

Repozytorium GitHub Enterprise można skonfigurować za pomocą usługi data factory, za pomocą dwóch metod.

 #### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Metoda konfiguracji 1 (Enterprise repozytorium): strona zaczynajmy

W usłudze Azure Data Factory, przejdź do **zaczynajmy** strony. Wybierz **Konfigurowanie repozytorium kodu**:

![Strona wprowadzenie fabryki danych](media/author-visually/github-integration-image1.png)

**Ustawienia repozytorium** zostanie wyświetlone okienko konfiguracji:

![Ustawienia repozytorium GitHub](media/author-visually/github-integration-image3.png)

W okienku wyświetlana następujący kod repozytoriów Azure ustawień repozytorium:

| **Ustawienie**                                              | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Wartość**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Typ repozytorium**                                      | Typ repozytorium kodu repozytoriów platformy Azure.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Użyj GitHub Enterprise**                                | Pole wyboru, aby GitHub Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **Adres URL GitHub Enterprise**                                | GitHub Enterprise głównego adresu URL. Na przykład: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **Konto usługi GitHub**                                       | Nazwa konta usługi GitHub. Ta nazwa można znaleźć https://github.com/{account Nazwa} / {Nazwa repozytorium}. Przejdź do tej strony wyświetli monit o wprowadzenie poświadczeń OAuth usługi GitHub do konta usługi GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Nazwa repozytorium usługi GitHub kodu. Konta usługi GitHub zawierają repozytoriów Git do zarządzania kodem źródłowym. Możesz utworzyć nowe repozytorium, lub użyć istniejącego repozytorium, który jest już na Twoim koncie.                                                                                                                                                                                                                              |                    |
| **Gałąź współpracy**                                 | Gałęzi współpracy usługi GitHub, które jest używane do publikowania. Domyślnie jest głównym. To ustawienie można zmienić w przypadku, gdy chcesz opublikować zasobów z innej gałęzi.                                                                                                                                                                                                                                                               |                    |
| **Folder główny**                                          | Folderem w gałęzi współpracy usługi GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importuj istniejące zasoby fabryki danych do repozytorium** | Określa, czy Importuj istniejące zasoby fabryki danych ze środowiska użytkownika **Kanwa tworzenia** do repozytorium GitHub. Zaznacz pole, aby zaimportować swoje zasoby fabryki danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja Eksportuje każdy zasób osobno (czyli usługi połączone i zestawy danych są eksportowane do oddzielnych JSON Smb1sessionsetup). Gdy to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Wybrane (ustawienie domyślne) |
| **Gałąź do importowania zasobów do**                       | Określa, które gałęzią zasoby fabryki danych (potoki, zestawy danych, połączonych usług itp.) są importowane. Zasoby można importować do jednej z następujących gałęziach:. B współpracy. Utwórz nowy. Użyj istniejącego                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Metoda konfiguracji 2 (Enterprise repozytorium): UX Kanwa tworzenia

W Interfejsie usługi Azure Data Factory **Kanwa tworzenia**, zlokalizuj fabryką danych. Wybierz **usługi Data Factory** menu rozwijanego, a następnie wybierz **Konfiguruj repozytorium kodu**.

Zostanie wyświetlone okienko konfiguracji. Aby uzyskać szczegółowe informacje o ustawieniach konfiguracji, zobacz opisy w *metody konfiguracji 1* powyżej.

## <a name="use-the-expression-language"></a>Użyj języka wyrażeń
Wyrażenia wartości właściwości można określić przy użyciu języka wyrażeń, który jest obsługiwany przez usługę Azure Data Factory.

Określ wyrażeń dla wartości właściwości, wybierając **Dodawanie zawartości dynamicznej**:

![Użyj języka wyrażeń](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Użyj funkcji i parametrów

Możesz użyć funkcji lub określ parametry w celu potoków i zestawów danych w usłudze Data Factory **Konstruktor wyrażeń**:

Aby uzyskać informacji na temat obsługiwanych wyrażeń, zobacz [wyrażeń i funkcji w usłudze Azure Data Factory](control-flow-expression-language-functions.md).

![Dodaj zawartość dynamiczną](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Przekazywanie opinii
Wybierz **opinii** komentarz na temat funkcji lub powiadomić firmę Microsoft o problemach, za pomocą narzędzia:

![Opinia](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat monitorowania i zarządzania potoków, zobacz [monitorowanie potoków i zarządzanie nimi programistycznie](monitor-programmatically.md).
